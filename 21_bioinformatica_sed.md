## 1. Introducción a sed en bioinformática

**Autor: MD. Christian Robles**

**Fecha: 23/01/2026**

En bioinformática, además de analizar datos, necesitamos **editar y transformar archivos de texto masivos**: FASTA, GFF, BED, CSV/TSV.  
`sed` (stream editor) es una herramienta Unix diseñada para aplicar **transformaciones en flujo**: sustituciones, eliminaciones, inserciones y reordenamientos, todo sin abrir un editor gráfico.

---

### 1.1 ¿Por qué usar sed en bioinformática?
- **Edición masiva**: cambiar miles de encabezados FASTA en segundos.  
- **Transformación rápida**: sustituir caracteres ambiguos (`N`) por guiones o espacios.  
- **Normalización**: convertir delimitadores, estandarizar nombres de genes.  
- **Automatización**: integrar en pipelines junto con `awk`, `grep`, `cut`.  

---

### 1.2 Sintaxis básica
La forma más común de `sed` es:

```bash
sed 's/patrón/reemplazo/' archivo
```

- `s` → indica sustitución.  
- `patrón` → expresión regular a buscar.  
- `reemplazo` → texto que reemplaza al patrón.  
- `archivo` → archivo de entrada.  

---

### 1.3 Ejemplo básico en FASTA: quitar el símbolo `>`
Archivo `secuencias.fasta`:
```
>seq1
ATGCGTACGTAGCTAGCTAG
>seq2
ATCGATCGATCG
```

Código:
```bash
sed 's/^>//' \
     secuencias.fasta
```

Salida:
```
seq1
ATGCGTACGTAGCTAGCTAG
seq2
ATCGATCGATCG
```

**Explicación**:  
- `^>` → busca el símbolo `>` al inicio de la línea.  
- `s/^>//` → lo sustituye por nada (lo elimina).  
- Resultado: encabezados sin el símbolo `>`.

---

### 1.4 Ejemplo: renombrar encabezados añadiendo prefijo
```bash
sed 's/^>/>sample_/' \
     secuencias.fasta
```

Salida:
```
>sample_seq1
ATGCGTACGTAGCTAGCTAG
>sample_seq2
ATCGATCGATCG
```

**Explicación**:  
- `^>` → detecta el inicio de cada encabezado.  
- `>sample_` → añade el prefijo `sample_` después del símbolo `>`.  
- Útil para normalizar IDs en proyectos con múltiples muestras.

---

### 1.5 Ejemplo: reemplazar nucleótidos ambiguos
```bash
sed 's/N/-/g' \
     secuencias.fasta
```

Salida:
```
>seq1
ATGC--ACGTAGCTAGCTAG
>seq2
ATCGATCGATCG
```

**Explicación**:  
- `s/N/-/g` → sustituye todas las apariciones de `N` por `-`.  
- La opción `g` asegura que se reemplacen todas las ocurrencias en la línea.  
- Esto limpia secuencias con nucleótidos ambiguos.

---

### Nota de usuario avanzado
- `sed` es más directo que `awk` para **sustituciones y ediciones simples**.  
- Su fuerza está en las **expresiones regulares** y la capacidad de aplicar cambios en flujo.  
- Filosofía Unix: piensa en `sed` como un editor automático que transforma texto sin intervención manual.

---

## 2. Fundamentos aplicados de sed

Antes de entrar en ejemplos bioinformáticos más complejos, conviene dominar las **opciones básicas de `sed`**. Estas permiten controlar cómo se muestran los resultados, si se aplican cambios directamente en los archivos y cómo combinar múltiples instrucciones.

---

### 2.1 Opción `-n` y el comando `p`
Por defecto, `sed` imprime todas las líneas del archivo. Con `-n` podemos suprimir la salida y mostrar solo lo que nos interesa usando `p`.

Ejemplo: imprimir solo encabezados FASTA.
```bash
sed -n '/^>/p' \
     secuencias.fasta
```

Salida:
```
>seq1
>seq2
```

**Explicación**:  
- `-n` → suprime la salida automática.  
- `/^>/p` → imprime solo las líneas que comienzan con `>`.

---

### 2.2 Opción `-i` (edición en el archivo)
Permite aplicar cambios directamente en el archivo, sin necesidad de redirigir la salida.

Ejemplo: eliminar el símbolo `>` de los encabezados en el archivo original.
```bash
sed -i 's/^>//' \
     secuencias.fasta
```

**Explicación**:  
- `-i` → modifica el archivo en el lugar.  
- `s/^>//` → elimina el símbolo `>` al inicio de cada encabezado.  
- Útil para limpiar archivos sin generar copias intermedias (aunque conviene hacer respaldo).

---

### 2.3 Opción `-e` (múltiples instrucciones)
Permite ejecutar varias transformaciones en una sola llamada.

Ejemplo: añadir prefijo y reemplazar `N` por `-` en un FASTA.
```bash
sed -e 's/^>/>sample_/' \
    -e 's/N/-/g' \
    secuencias.fasta
```

**Explicación**:  
- Primera instrucción: añade `sample_` a los encabezados.  
- Segunda instrucción: reemplaza todas las `N` por `-`.  
- `-e` permite encadenar varias reglas en un solo comando.

---

### 2.4 Uso de rangos de líneas
Podemos aplicar transformaciones solo a un rango de líneas.

Ejemplo: convertir a minúsculas las primeras 5 secuencias.
```bash
sed '2,6 y/ATGC/atgc/' \
     secuencias.fasta
```

**Explicación**:  
- `2,6` → aplica la transformación de la línea 2 a la 6.  
- `y/ATGC/atgc/` → convierte las letras A, T, G, C en minúsculas.  
- Útil para pruebas en un subconjunto de datos.

---

### 2.5 Expresiones regulares
La verdadera potencia de `sed` está en las expresiones regulares.

Ejemplo: marcar secuencias que contienen el motivo `ATG`.
```bash
sed 's/ATG/[ATG]/g' \
     secuencias.fasta
```

Salida:
```
>seq1
[ATG]CGTACGTAGCTAGCTAG
>seq2
[ATG]CGATCGATCG
```

**Explicación**:  
- `s/ATG/[ATG]/g` → sustituye cada aparición de `ATG` por `[ATG]`.  
- Esto permite resaltar motivos dentro de las secuencias.

---

### Nota de usuario avanzado
- `-n` y `p` son esenciales para filtrar.  
- `-i` es poderoso pero peligroso: siempre respalda tus datos.  
- `-e` permite modularidad en un solo comando.  
- Las expresiones regulares convierten a `sed` en un editor masivo y flexible.  

---

## 3. Manipulación de archivos FASTA con sed

Los archivos FASTA son uno de los formatos más comunes en bioinformática. Con `sed` podemos realizar transformaciones rápidas sobre encabezados y secuencias, sin necesidad de programas más pesados.

---

### 3.1 Eliminar encabezados
Ejemplo: obtener solo las secuencias, sin los encabezados.

```bash
sed '/^>/d' \
     secuencias.fasta
```

**Explicación**:  
- `/^>/d` → elimina todas las líneas que comienzan con `>`.  
- Resultado: archivo con solo las secuencias.

---

### 3.2 Extraer solo los encabezados
Ejemplo: obtener únicamente los IDs de las secuencias.

```bash
sed -n '/^>/p' \
     secuencias.fasta
```

**Explicación**:  
- `-n` → suprime la salida automática.  
- `/^>/p` → imprime solo las líneas que comienzan con `>`.  
- Resultado: lista de encabezados.

---

### 3.3 Renombrar encabezados con prefijo
Ejemplo: añadir `sample_` a todos los IDs.

```bash
sed 's/^>/>sample_/' \
     secuencias.fasta
```

**Explicación**:  
- `s/^>/>sample_/` → sustituye el símbolo `>` por `>sample_`.  
- Resultado: encabezados normalizados con prefijo.

---

### 3.4 Reemplazar nucleótidos ambiguos
Ejemplo: sustituir todas las `N` por guiones.

```bash
sed 's/N/-/g' \
     secuencias.fasta
```

**Explicación**:  
- `s/N/-/g` → reemplaza todas las apariciones de `N` por `-`.  
- Útil para limpiar secuencias con bases ambiguas.

---

### 3.5 Convertir secuencias a minúsculas
Ejemplo: transformar todas las bases a minúsculas.

```bash
sed 'y/ATGC/atgc/' \
     secuencias.fasta
```

**Explicación**:  
- `y/ATGC/atgc/` → convierte cada carácter A, T, G, C en su versión minúscula.  
- Resultado: secuencias en minúsculas, útiles para ciertos programas que requieren este formato.

---

### 3.6 Eliminar líneas vacías
Ejemplo: limpiar un FASTA con saltos de línea extra.

```bash
sed '/^$/d' \
     secuencias.fasta
```

**Explicación**:  
- `/^$/d` → elimina las líneas vacías.  
- Resultado: archivo más limpio y uniforme.

---

### Nota de usuario avanzado
- `sed` es ideal para **limpieza rápida de FASTA**: quitar encabezados, normalizar IDs, transformar secuencias.  
- Su ventaja es la **simplicidad y velocidad** en archivos grandes.  
- Filosofía Unix: usa `sed` como filtro para preparar secuencias antes de análisis más complejos.

---

## 4. Procesamiento de archivos CSV/TSV con sed

En bioinformática, los archivos tabulares (CSV/TSV) son comunes para almacenar datos de expresión génica, anotaciones y resultados experimentales. Con `sed` podemos realizar transformaciones rápidas y limpias sobre estos archivos.

---

### 4.1 Cambiar delimitadores
Ejemplo: convertir un archivo CSV (delimitado por comas) en TSV (delimitado por tabulaciones).

```bash
sed 's/,/\t/g' \
     expresion.csv
```

**Explicación**:  
- `s/,/\t/g` → sustituye todas las comas por tabulaciones.  
- Útil para adaptar archivos a programas que requieren formato TSV.

---

### 4.2 Normalizar nombres de genes
Ejemplo: convertir todos los nombres de genes a mayúsculas.

```bash
sed 's/\([A-Za-z0-9]*\),/\U\1,/' \
     expresion.csv
```

**Explicación**:  
- `\([A-Za-z0-9]*\),` → captura el nombre del gen antes de la coma.  
- `\U\1` → convierte la captura a mayúsculas.  
- Resultado: nombres de genes estandarizados.

---

### 4.3 Reemplazar estados de expresión
Ejemplo: cambiar “up” por “sobreexpresado” y “down” por “subexpresado”.

```bash
sed -e 's/up/sobreexpresado/g' \
    -e 's/down/subexpresado/g' \
    expresion.csv
```

**Explicación**:  
- Primera regla: sustituye “up” por “sobreexpresado”.  
- Segunda regla: sustituye “down” por “subexpresado”.  
- Resultado: estados más descriptivos en español.

---

### 4.4 Eliminar columnas innecesarias
Ejemplo: eliminar la primera columna (valores numéricos).

```bash
sed 's/^[^,]*,//' \
     expresion.csv
```

**Explicación**:  
- `^[^,]*,` → busca todo lo que está antes de la primera coma y lo elimina.  
- Resultado: archivo sin la primera columna.

---

### 4.5 Reordenar columnas
Ejemplo: imprimir primero el estado y luego el gen.

```bash
sed 's/^\([^,]*\),\([^,]*\),\([^,]*\)$/\3,\2/' \
     expresion.csv
```

**Explicación**:  
- `\([^,]*\)` → captura cada columna separada por comas.  
- `\3,\2` → imprime la tercera columna (estado) seguida de la segunda (gen).  
- Resultado: archivo con columnas reordenadas.

---

### Nota de usuario avanzado
- `sed` es muy útil para **transformaciones rápidas en CSV/TSV**: cambiar delimitadores, normalizar nombres, traducir estados.  
- Aunque `awk` es más potente para cálculos, `sed` brilla en **sustituciones y reordenamientos simples**.  
- Filosofía Unix: usa `sed` como filtro ligero para preparar datos tabulares antes de análisis más complejos.

---

## 5. Análisis de secuencias con sed y expresiones regulares

Una de las fortalezas de `sed` es el uso de **expresiones regulares** para buscar y transformar patrones dentro de secuencias. Esto lo convierte en una herramienta útil para detectar motivos genómicos, limpiar secuencias y resaltar regiones de interés.

---

### 5.1 Buscar motivos específicos en secuencias
Ejemplo: marcar todas las apariciones del motivo `ATG`.

```bash
sed 's/ATG/[ATG]/g' \
     secuencias.fasta
```

**Explicación**:  
- `s/ATG/[ATG]/g` → sustituye cada aparición de `ATG` por `[ATG]`.  
- El modificador `g` asegura que se reemplacen todas las ocurrencias en cada línea.  
- Resultado: las secuencias muestran el motivo resaltado.

---

### 5.2 Sustituir motivos por etiquetas
Ejemplo: reemplazar el motivo `TATA` por la etiqueta `[PROMOTOR]`.

```bash
sed 's/TATA/[PROMOTOR]/g' \
     secuencias.fasta
```

**Explicación**:  
- `s/TATA/[PROMOTOR]/g` → sustituye todas las apariciones de `TATA` por `[PROMOTOR]`.  
- Útil para marcar regiones promotoras en secuencias genómicas.

---

### 5.3 Buscar motivos con expresiones regulares
Ejemplo: detectar un motivo de inicio de codón seguido de tres bases cualquiera y un codón de parada `TAA`.

```bash
sed -n '/ATG[ATGC]\{3\}TAA/p' \
     secuencias.fasta
```

**Explicación**:  
- `ATG[ATGC]\{3\}TAA` → patrón que busca `ATG` seguido de 3 bases y luego `TAA`.  
- `-n` y `p` → imprimen solo las líneas que contienen el motivo.  
- Resultado: se muestran las secuencias que contienen ese patrón.

---

### 5.4 Sustituir caracteres ambiguos con expresiones regulares
Ejemplo: reemplazar cualquier carácter que no sea A, T, G o C por `-`.

```bash
sed 's/[^ATGC]/-/g' \
     secuencias.fasta
```

**Explicación**:  
- `[^ATGC]` → expresión regular que selecciona cualquier carácter distinto de A, T, G o C.  
- `s/[^ATGC]/-/g` → sustituye esos caracteres por guiones.  
- Resultado: secuencias limpias con solo bases válidas.

---

### 5.5 Resaltar secuencias con alto contenido GC
Ejemplo: marcar líneas que contienen al menos 5 apariciones consecutivas de G o C.

```bash
sed 's/[GC]\{5,\}/[GC-rich]/g' \
     secuencias.fasta
```

**Explicación**:  
- `[GC]\{5,\}` → busca secuencias con 5 o más G o C consecutivas.  
- `s/.../[GC-rich]/g` → sustituye esas regiones por la etiqueta `[GC-rich]`.  
- Útil para identificar regiones con alto contenido GC.

---

### Nota de usuario avanzado
- `sed` permite aplicar **expresiones regulares complejas** para detectar y transformar motivos genómicos.  
- Es ideal para tareas de **preprocesamiento rápido**: marcar, sustituir o limpiar secuencias antes de análisis más profundos.  
- Filosofía Unix: usa `sed` como filtro para resaltar patrones clave en grandes volúmenes de datos.

---

## 6. Casos prácticos integradores con sed

En este capítulo aplicamos todo lo aprendido en **FASTA y CSV/TSV** para construir pipelines bioinformáticos con `sed`. La idea es mostrar cómo usarlo en flujos completos de limpieza y transformación de datos.

---

### 6.1 Limpieza de un archivo FASTA
Ejemplo: eliminar encabezados, convertir secuencias a minúsculas y reemplazar `N` por `-`.

```bash
sed -e '/^>/d' \
    -e 'y/ATGC/atgc/' \
    -e 's/N/-/g' \
    secuencias.fasta
```

**Explicación**:  
- `/^>/d` → elimina encabezados.  
- `y/ATGC/atgc/` → convierte bases a minúsculas.  
- `s/N/-/g` → reemplaza nucleótidos ambiguos por guiones.  
- Resultado: archivo limpio con solo secuencias normalizadas.

---

### 6.2 Normalización de un CSV de expresión génica
Ejemplo: convertir delimitador coma → tabulación y traducir estados.

```bash
sed -e 's/,/\t/g' \
    -e 's/up/sobreexpresado/g' \
    -e 's/down/subexpresado/g' \
    expresion.csv
```

**Explicación**:  
- `s/,/\t/g` → cambia comas por tabulaciones.  
- `s/up/sobreexpresado/g` → traduce “up”.  
- `s/down/subexpresado/g` → traduce “down”.  
- Resultado: archivo TSV más descriptivo y legible.

---

### 6.3 Pipeline FASTA + CSV: IDs consistentes
Supongamos que los encabezados FASTA tienen IDs como `seq1`, `seq2`, y el CSV usa nombres de genes. Queremos añadir un prefijo común.

```bash
sed 's/^>/>gene_/' \
     secuencias.fasta > fasta_norm.fasta

sed 's/^\([0-9]*,\)\([^,]*\)/\1gene_\2/' \
     expresion.csv > csv_norm.csv
```

**Explicación**:  
- Primer `sed`: añade `gene_` a los encabezados FASTA.  
- Segundo `sed`: añade `gene_` al nombre de cada gen en el CSV.  
- Resultado: IDs consistentes entre FASTA y CSV.

---

### 6.4 Reporte rápido de motivos genómicos
Ejemplo: marcar secuencias que contienen el motivo `ATG...TAA`.

```bash
sed -n '/ATG[ATGC]\{3\}TAA/p' \
     secuencias.fasta > motivos.txt
```

**Explicación**:  
- `ATG[ATGC]\{3\}TAA` → busca el patrón de inicio `ATG`, seguido de 3 bases, y `TAA`.  
- `-n` y `p` → imprimen solo las secuencias que cumplen el patrón.  
- Resultado: archivo con secuencias que contienen el motivo.

---

### 6.5 Integración con otras herramientas
Ejemplo: obtener genes únicos en estado “up” y normalizar nombres.

```bash
sed -n '/up/p' \
     expresion.csv | \
sed 's/,/\t/g' | \
cut -f2 | sort | uniq
```

**Explicación**:  
- Primer `sed`: selecciona solo las filas con “up”.  
- Segundo `sed`: convierte comas en tabulaciones.  
- `cut -f2` → extrae la columna de genes.  
- `sort | uniq` → ordena y elimina duplicados.  
- Resultado: lista de genes únicos en estado “up”.

---

### Nota de usuario avanzado
- `sed` brilla en **pipelines integradores**: limpieza de FASTA, normalización de CSV y detección de motivos.  
- Su fuerza está en las **expresiones regulares** y la capacidad de aplicar múltiples transformaciones en flujo.  
- Filosofía Unix: combina `sed` con otras utilidades para construir flujos claros y reproducibles.

---

## 7. Buenas prácticas en bioinformática con sed

Hasta ahora hemos visto cómo `sed` puede limpiar y transformar archivos FASTA y CSV/TSV, además de detectar motivos genómicos. Para que estos flujos sean realmente útiles en bioinformática, es importante aplicar **buenas prácticas** que aseguren claridad, reproducibilidad y mantenimiento.

---

### 7.1 Documentar comandos y scripts
Cuando un comando es largo, conviene guardarlo en un archivo `.sed` y añadir comentarios:

Archivo `limpieza_fasta.sed`:
```sed
# Script para limpiar un archivo FASTA
# - Elimina encabezados
# - Convierte bases a minúsculas
# - Sustituye N por guiones

/^>/d
y/ATGC/atgc/
s/N/-/g
```

Uso:
```bash
sed -f limpieza_fasta.sed secuencias.fasta
```

**Buenas prácticas**:  
- Usa comentarios (`#`) para explicar cada regla.  
- Incluye instrucciones de uso al inicio.  
- Esto facilita que otros investigadores entiendan el script.

---

### 7.2 Modularizar tareas
En lugar de un solo comando enorme, divide el flujo en pasos:

```bash
# Paso 1: eliminar encabezados
sed '/^>/d' secuencias.fasta > sin_encabezados.fasta

# Paso 2: convertir a minúsculas
sed 'y/ATGC/atgc/' sin_encabezados.fasta > minusculas.fasta

# Paso 3: reemplazar N por guiones
sed 's/N/-/g' minusculas.fasta > limpio.fasta
```

**Buenas prácticas**:  
- Divide el análisis en archivos intermedios.  
- Permite revisar cada paso y detectar errores fácilmente.  
- El resultado final es más confiable.

---

### 7.3 Reproducibilidad
- **Guarda los comandos en scripts** (`.sh` o `.sed`) en lugar de ejecutarlos solo en la terminal.  
- **Usa nombres claros** para archivos intermedios y resultados.  
- **Incluye metadatos**: fecha, parámetros usados, versión de los datos.  
- Esto asegura que otros puedan repetir tu análisis exactamente.

---

### 7.4 Integración con otras herramientas
`sed` es más poderoso cuando se combina con utilidades clásicas de Unix:

```bash
sed 's/,/\t/g' expresion.csv | cut -f2 | sort | uniq
```

- `sed` → convierte delimitadores.  
- `cut` → extrae la columna de genes.  
- `sort | uniq` → ordena y elimina duplicados.  

**Buenas prácticas**:  
- Usa pipelines para tareas rápidas.  
- Documenta cada paso para que el flujo sea entendible.  

---

### 7.5 Estilo y legibilidad
- **Divide comandos largos con `\`** para que no se desborden en PDF ni en la terminal.  
- **Indentación**: alinea bloques para que sean fáciles de leer.  
- **Comentarios**: explica cada paso, incluso si parece obvio.  

---

### Nota de usuario avanzado
- La claridad y reproducibilidad son tan importantes como el resultado.  
- `sed` puede ser un bisturí muy preciso, pero solo si los scripts están bien documentados.  
- Filosofía Unix: piensa en tus comandos como piezas de un puzzle que otros puedan reutilizar.

---

## 8. Ejemplos avanzados de sed en bioinformática

En este capítulo exploramos usos más complejos de `sed`, aplicados a formatos de anotación genómica como **GFF** y **BED**, y aprovechando **grupos de captura** y referencias para transformaciones precisas.

---

### 8.1 Filtrar genes en un archivo GFF
Archivo `anotacion.gff` (simplificado):
```
chr1 . gene 1000 5000 . + . ID=gene1;Name=TP53
chr1 . exon 1000 1200 . + . ID=exon1;Parent=gene1
chr2 . gene 2000 6000 . - . ID=gene2;Name=EGFR
```

Ejemplo: extraer solo las líneas de genes.
```bash
sed -n '/\sgene\s/p' \
     anotacion.gff
```

**Explicación**:  
- `\sgene\s` → busca la palabra “gene” rodeada de espacios.  
- `-n` y `p` → imprimen solo esas líneas.  
- Resultado: lista de genes con sus posiciones.

---

### 8.2 Calcular longitudes de regiones en BED
Archivo `regiones.bed`:
```
chr1 1000 1500 region1
chr1 2000 2100 region2
chr2 3000 4000 region3
```

Ejemplo: añadir la longitud como nueva columna.
```bash
sed 's/\([0-9]*\)\s\([0-9]*\)\s\([^\s]*\)/\1 \2 \3 len:\ \2-\1/' \
     regiones.bed
```

**Explicación**:  
- `\([0-9]*\)` → captura números (inicio y fin).  
- `\([^\s]*\)` → captura el nombre de la región.  
- `\2-\1` → imprime la diferencia (fin - inicio).  
- Resultado: cada línea incluye la longitud calculada.

---

### 8.3 Extraer nombres de genes del GFF
Ejemplo: obtener solo el valor de `Name=` en los atributos.

```bash
sed -n 's/.*Name=\([^;]*\).*/\1/p' \
     anotacion.gff
```

Salida:
```
TP53
EGFR
```

**Explicación**:  
- `Name=\([^;]*\)` → captura el texto después de `Name=` hasta el siguiente `;`.  
- `\1` → imprime la captura.  
- Resultado: lista de nombres de genes.

---

### 8.4 Reemplazar IDs de exones con su gen padre
Ejemplo: transformar atributos para mostrar `Parent` en lugar de `ID`.

```bash
sed -n 's/.*Parent=\([^;]*\).*/Parent:\1/p' \
     anotacion.gff
```

Salida:
```
Parent:gene1
Parent:gene2
```

**Explicación**:  
- `Parent=\([^;]*\)` → captura el valor del atributo `Parent`.  
- `Parent:\1` → imprime el valor con prefijo.  
- Resultado: IDs de genes padres de cada exón.

---

### 8.5 Uso de grupos de captura múltiples
Ejemplo: reorganizar columnas en BED para mostrar nombre primero.

```bash
sed 's/^\([^ ]*\)\s\([^ ]*\)\s\([^ ]*\)\s\([^ ]*\)$/\4 \1 \2 \3/' \
     regiones.bed
```

Salida:
```
region1 chr1 1000 1500
region2 chr1 2000 2100
region3 chr2 3000 4000
```

**Explicación**:  
- Captura cromosoma, inicio, fin y nombre.  
- Reordena para imprimir primero el nombre y luego las coordenadas.  

---

### Nota de usuario avanzado
- Con **grupos de captura** (`\(...\)`) y referencias (`\1`, `\2`), `sed` puede reordenar y transformar datos complejos.  
- Es ideal para **extraer atributos** de GFF y reorganizar columnas en BED.  
- Filosofía Unix: usa `sed` como filtro preciso para preparar anotaciones antes de análisis más pesados.

---

## 9. Conclusiones y visión práctica de sed en bioinformática

Hemos recorrido desde los fundamentos hasta ejemplos avanzados de `sed` aplicados directamente a bioinformática. Este último capítulo resume lo aprendido y plantea cómo aprovechar la guía en tu trabajo futuro.

---

### 9.1 Lo que hemos aprendido
- **FASTA**: eliminar encabezados, renombrar IDs, limpiar secuencias, transformar bases.  
- **CSV/TSV**: cambiar delimitadores, normalizar nombres de genes, traducir estados de expresión, reordenar columnas.  
- **Expresiones regulares**: detectar motivos genómicos, sustituir patrones complejos, limpiar caracteres ambiguos.  
- **GFF/BED**: extraer atributos, reorganizar columnas, filtrar genes y exones.  
- **Pipelines integradores**: combinar FASTA y CSV para generar reportes consistentes y normalizados.  

---

### 9.2 Filosofía Unix aplicada
- `sed` es un **editor de flujo**: transforma texto mientras se lee, sin necesidad de abrirlo en un editor.  
- Su poder está en la **simplicidad modular**: reglas cortas que, combinadas, resuelven problemas complejos.  
- Combinado con otras utilidades (`awk`, `grep`, `cut`, `sort`), se convierte en un motor de edición masiva y precisa.

---

### 9.3 Buenas prácticas
- **Documentar**: guarda tus reglas en archivos `.sed` con comentarios claros.  
- **Modularizar**: divide flujos en pasos intermedios para mayor claridad y depuración.  
- **Reproducibilidad**: conserva scripts y resultados con nombres descriptivos y metadatos.  
- **Legibilidad**: divide comandos largos con `\` y usa indentación para que sean fáciles de leer en PDF y terminal.  

---

### 9.4 Visión práctica
- En bioinformática, `sed` es ideal para **preprocesamiento de datos**: limpiar, normalizar y transformar archivos antes de análisis más pesados.  
- Su uso te permite ahorrar tiempo y mantener flujos reproducibles.  
- Pensar en `sed` como un editor automático de texto te dará ventaja en análisis genómicos y clínicos.  

---

### 9.5 Próximos pasos
- Consolidar esta guía en un documento completo (Markdown → PDF con pandoc/xelatex).  
- Crear un **repositorio de scripts `.sed`** para bioinformática, organizados por tareas (FASTA, CSV, GFF, pipelines).  
- Integrar `sed` junto con `awk` en tus flujos clínicos y de investigación, como parte de tu preparación para bioinformática avanzada.  

