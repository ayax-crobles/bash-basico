## 1. Fundamentos de grep y regex en bioinformática

**Autor: MD. Christian Robles**

**Fecha: 23/01/2026**

`grep` es una de las herramientas más usadas en Unix/Linux para **buscar patrones de texto** en archivos. Su potencia se multiplica cuando se combina con **expresiones regulares (regex)**, lo que lo convierte en un recurso clave para bioinformática, donde los datos suelen estar en formatos de texto masivo (FASTA, CSV, GFF, BED).

---

### 1.1 ¿Qué es grep?
- `grep` significa **Global Regular Expression Print**.  
- Su función principal es **buscar coincidencias** de patrones en archivos o flujos de texto.  
- Se integra fácilmente en pipelines con otras herramientas (`awk`, `sed`, `cut`, `sort`).  

Ejemplo básico:
```bash
grep "ATG" secuencias.fasta
```
Busca todas las líneas que contienen el motivo `ATG`.

---

### 1.2 Opciones comunes
- `-E` → activa expresiones regulares extendidas (más potentes).  
- `-i` → búsqueda insensible a mayúsculas/minúsculas.  
- `-n` → muestra el número de línea junto con la coincidencia.  
- `-A NUM` → muestra NUM líneas después de la coincidencia.  
- `-B NUM` → muestra NUM líneas antes de la coincidencia.  
- `-C NUM` → muestra NUM líneas de contexto alrededor de la coincidencia.  

Ejemplo:
```bash
grep -n -A2 "Error" log.txt
```
Muestra el número de línea y dos líneas después de cada coincidencia de “Error”.

---

### 1.3 Diferencia con awk y sed
- **`grep`** → especializado en **buscar y filtrar** texto.  
- **`sed`** → orientado a **editar y transformar** texto en flujo.  
- **`awk`** → diseñado para **procesar y analizar** texto estructurado en columnas.  

En bioinformática, `grep` es ideal para **detección rápida de motivos y patrones**, mientras que `awk` y `sed` se usan para cálculos y transformaciones.

---

### 1.4 Ejemplo aplicado a FASTA
Archivo `secuencias.fasta`:
```
>seq1
ATGCGTACGTAGCTAGCTAG
>seq2
ATCGATCGATCG
```

Buscar secuencias que contienen el motivo `ATG`:
```bash
grep "ATG" secuencias.fasta
```

Salida:
```
ATGCGTACGTAGCTAGCTAG
ATCGATCGATCG
```

**Explicación**:  
- `grep "ATG"` → imprime todas las líneas que contienen el motivo `ATG`.  
- Útil para detectar rápidamente secuencias con codones de inicio.

---

### 1.5 Ejemplo aplicado a CSV
Archivo `expresion.csv`:
```
12,EGFR,down
4,PTEN,down
11,TP53,up
15,EGFR,up
```

Buscar genes en estado “up”:
```bash
grep "up" expresion.csv
```

Salida:
```
11,TP53,up
15,EGFR,up
```

**Explicación**:  
- `grep "up"` → imprime solo las filas que contienen “up”.  
- Útil para filtrar rápidamente genes sobreexpresados.

---

### Nota de usuario avanzado
- `grep` es el **primer filtro** en muchos pipelines bioinformáticos: rápido, ligero y preciso.  
- Su verdadero poder está en las **regex**, que permiten definir patrones complejos.  
- Filosofía Unix: usa `grep` para detectar, y luego pasa los resultados a `awk` o `sed` para análisis o transformación.

---

## 2. Coincidencias básicas y anclas en grep

En este capítulo veremos cómo usar **coincidencias simples** y **anclas de línea** para controlar con precisión dónde aparece un patrón dentro de un archivo. Estos conceptos son fundamentales para aplicar `grep` en bioinformática.

---

### 2.1 El comodín `.` (cualquier carácter)
Ejemplo: buscar cualquier carácter seguido de `txt`.

```bash
grep ".txt" archivo.txt
```

**Explicación**:  
- `.` → representa cualquier carácter.  
- Coincide con `atxt`, `btxt`, `1txt`, etc.  
- Útil para detectar variaciones en nombres de archivos o secuencias.

---

### 2.2 El punto literal `\.`
Ejemplo: buscar exactamente `.txt`.

```bash
grep "\.txt" archivo.txt
```

**Explicación**:  
- `\.` → el `\` escapa el punto para que se interprete como carácter literal.  
- Coincide solo con `.txt`.  
- Útil para buscar extensiones de archivo o secuencias con puntos específicos.

---

### 2.3 Ancla de inicio de línea `^`
Ejemplo: buscar encabezados FASTA (que siempre empiezan con `>`).

```bash
grep "^>" secuencias.fasta
```

Salida:
```
>seq1
>seq2
```

**Explicación**:  
- `^` → marca el inicio de la línea.  
- Coincide solo si el patrón aparece al comienzo.  
- Útil para extraer encabezados en archivos FASTA.

---

### 2.4 Ancla de fin de línea `$`
Ejemplo: buscar secuencias que terminan en `TAA` (codón de parada).

```bash
grep "TAA$" secuencias.fasta
```

Salida:
```
ATGCGTACGTAGCTAGCTAA
```

**Explicación**:  
- `$` → marca el final de la línea.  
- Coincide solo si el patrón aparece al final.  
- Útil para detectar codones de parada en secuencias.

---

### 2.5 Combinación de anclas
Ejemplo: buscar líneas que comiencen con `ATG` y terminen en `TAA`.

```bash
grep "^ATG.*TAA$" secuencias.fasta
```

**Explicación**:  
- `^ATG` → inicio con `ATG`.  
- `.*` → cualquier número de caracteres intermedios.  
- `TAA$` → termina con `TAA`.  
- Útil para detectar secuencias completas de genes con inicio y parada.

---

### Nota de usuario avanzado
- `^` y `$` son esenciales para controlar la **posición exacta** de los patrones.  
- En bioinformática, esto permite distinguir entre motivos que aparecen en cualquier parte de la secuencia y aquellos que definen **inicio o fin** de regiones.  
- Filosofía Unix: piensa en `grep` como un microscopio que te permite enfocar exactamente dónde aparece un patrón.

---

## 3. Conjuntos, rangos y cuantificadores en grep

Ahora vamos a profundizar en cómo `grep` utiliza **conjuntos de caracteres** y **cuantificadores** para construir búsquedas más potentes. Esto es clave en bioinformática, donde necesitamos detectar patrones específicos en secuencias o datos tabulares.

---

### 3.1 Conjuntos de caracteres `[ ]`
- **Cualquier dígito**  
  ```bash
  grep "[0-9]" datos.txt
  ```
  Coincide con cualquier número del 0 al 9.

- **Solo vocales**  
  ```bash
  grep "[aeiou]" texto.txt
  ```
  Encuentra líneas con al menos una vocal.

- **Bases de ADN**  
  ```bash
  grep "[ATGC]" secuencias.fasta
  ```
  Coincide con cualquier línea que contenga al menos una base válida.

---

### 3.2 Rangos
- **Mayúsculas**  
  ```bash
  grep "^[A-Z]" texto.txt
  ```
  Encuentra líneas que empiezan con una letra mayúscula.

- **Números en encabezados FASTA**  
  ```bash
  grep "^>seq[0-9]" secuencias.fasta
  ```
  Coincide con encabezados que contienen números (ej. `>seq1`, `>seq2`).

---

### 3.3 Cuantificadores
- **Cero o más (`*`)**  
  ```bash
  grep "A*T" secuencias.fasta
  ```
  Coincide con `T`, `AT`, `AAT`, `AAAT`, etc.

- **Uno o más (`+`, requiere `-E`)**  
  ```bash
  grep -E "A+T" secuencias.fasta
  ```
  Coincide con `AT`, `AAT`, `AAAT`, pero no con `T` solo.

- **Opcional (`?`, requiere `-E`)**  
  ```bash
  grep -E "colou?r" texto.txt
  ```
  Coincide con `color` y `colour`.

---

### 3.4 Ejemplo aplicado a bioinformática
Archivo `secuencias.fasta`:
```
>seq1
ATGCGTACGTAGCTAGCTAG
>seq2
ATCGNNNNATCG
>seq3
GGGGGGGGGG
```

- Buscar secuencias con al menos 4 `N` consecutivos:
  ```bash
  grep "N\{4,\}" secuencias.fasta
  ```
  Salida:
  ```
  ATCGNNNNATCG
  ```

- Buscar secuencias con más de 5 `G` consecutivos:
  ```bash
  grep "G\{6,\}" secuencias.fasta
  ```
  Salida:
  ```
  GGGGGGGGGG
  ```

**Explicación**:  
- `\{4,\}` → al menos 4 repeticiones.  
- `\{6,\}` → al menos 6 repeticiones.  
- Útil para detectar regiones repetitivas o GC-rich.

---

### Nota de usuario avanzado
- Los **conjuntos** permiten definir qué caracteres son válidos.  
- Los **rangos** simplifican búsquedas amplias (ej. `[A-Z]`).  
- Los **cuantificadores** son esenciales para detectar repeticiones, muy comunes en secuencias genómicas.  
- Filosofía Unix: usa `grep` como detector de patrones repetitivos y específicos en grandes volúmenes de datos.

---

## 4. Alternativas y agrupaciones en grep

En bioinformática, muchas veces necesitamos buscar **varios motivos o variantes de un patrón** en un solo paso. Aquí es donde entran en juego las **alternativas (`|`)** y las **agrupaciones (`()`)**, que amplían la potencia de `grep`.

---

### 4.1 Alternativas con `|`
Ejemplo: buscar secuencias que contengan `ATG` o `TATA`.

```bash
grep -E "ATG|TATA" secuencias.fasta
```

**Explicación**:  
- `|` → significa “o”.  
- `ATG|TATA` → coincide con cualquiera de los dos motivos.  
- Útil para detectar múltiples señales genómicas en un solo comando.

---

### 4.2 Agrupaciones con `()`
Ejemplo: buscar variantes de un motivo promotor `TATA` seguido de `A` o `G`.

```bash
grep -E "TATA(A|G)" secuencias.fasta
```

**Explicación**:  
- `(A|G)` → agrupa alternativas dentro de paréntesis.  
- Coincide con `TATAA` o `TATAG`.  
- Útil para detectar variantes de un mismo motivo.

---

### 4.3 Combinación de agrupaciones y cuantificadores
Ejemplo: buscar secuencias que comiencen con `ATG` y terminen en cualquiera de los codones de parada (`TAA`, `TAG`, `TGA`).

```bash
grep -E "^ATG.*(TAA|TAG|TGA)$" secuencias.fasta
```

**Explicación**:  
- `^ATG` → inicio con `ATG`.  
- `.*` → cualquier número de caracteres intermedios.  
- `(TAA|TAG|TGA)$` → termina con uno de los tres codones de parada.  
- Útil para identificar secuencias completas de genes.

---

### 4.4 Ejemplo aplicado a CSV
Archivo `expresion.csv`:
```
12,EGFR,down
4,PTEN,down
11,TP53,up
15,EGFR,up
```

Buscar genes en estado “up” o “down”:
```bash
grep -E "up|down" expresion.csv
```

Salida:
```
12,EGFR,down
4,PTEN,down
11,TP53,up
15,EGFR,up
```

**Explicación**:  
- `up|down` → detecta ambas condiciones.  
- Útil para filtrar rápidamente todas las filas con estados de expresión definidos.

---

### Nota de usuario avanzado
- Las **alternativas** permiten buscar múltiples motivos en paralelo.  
- Las **agrupaciones** son esenciales para construir patrones complejos y variantes.  
- En bioinformática, esto se traduce en detectar **familias de motivos genómicos** o **variantes de codones** en un solo paso.  
- Filosofía Unix: usa `grep` como detector flexible que puede adaptarse a múltiples hipótesis de búsqueda.

---

## 5. Opciones avanzadas de grep

Además de las coincidencias básicas y las regex, `grep` ofrece **opciones avanzadas** que permiten controlar mejor la salida y adaptar las búsquedas a distintos contextos. Estas opciones son muy útiles en bioinformática para filtrar secuencias, contar coincidencias o mostrar contexto alrededor de un motivo.

---

### 5.1 Buscar palabras completas (`-w`)
Ejemplo: detectar solo la palabra `cat` en un archivo de texto.

```bash
grep -w "cat" animales.txt
```

**Explicación**:  
- `-w` → asegura que el patrón coincida solo como palabra completa.  
- Evita coincidencias parciales como `concatenate`.  
- Útil en bioinformática para buscar genes exactos sin confundirlos con nombres más largos.

---

### 5.2 Invertir coincidencias (`-v`)
Ejemplo: mostrar todas las líneas que **no** contienen `Error`.

```bash
grep -v "Error" log.txt
```

**Explicación**:  
- `-v` → invierte la búsqueda.  
- Útil para excluir secuencias con motivos indeseados o filtrar genes que no cumplen una condición.

---

### 5.3 Contar coincidencias (`-c`)
Ejemplo: contar cuántas líneas contienen `Error`.

```bash
grep -c "Error" log.txt
```

**Explicación**:  
- `-c` → devuelve el número de líneas que coinciden.  
- Útil para cuantificar cuántas secuencias contienen un motivo específico.

---

### 5.4 Mostrar número de línea (`-n`)
Ejemplo: mostrar en qué línea aparece `ATG`.

```bash
grep -n "ATG" secuencias.fasta
```

Salida:
```
2:ATGCGTACGTAGCTAGCTAG
```

**Explicación**:  
- `-n` → añade el número de línea antes de la coincidencia.  
- Útil para localizar rápidamente dónde aparece un motivo en un archivo grande.

---

### 5.5 Mostrar contexto (`-A`, `-B`, `-C`)
Ejemplo: mostrar coincidencias de `Error` con 2 líneas de contexto antes y después.

```bash
grep -C2 "Error" log.txt
```

**Explicación**:  
- `-A NUM` → muestra NUM líneas después.  
- `-B NUM` → muestra NUM líneas antes.  
- `-C NUM` → muestra NUM líneas antes y después.  
- Útil en bioinformática para ver el motivo en contexto dentro de una secuencia o anotación.

---

### 5.6 Ejemplo aplicado a bioinformática
Archivo `secuencias.fasta`:
```
>seq1
ATGCGTACGTAGCTAGCTAG
>seq2
ATCGNNNNATCG
>seq3
GGGGGGGGGG
```

- Contar cuántas secuencias contienen `ATG`:
  ```bash
  grep -c "ATG" secuencias.fasta
  ```
  Salida:
  ```
  2
  ```

- Mostrar encabezados que **no** contienen números:
  ```bash
  grep -v "[0-9]" secuencias.fasta
  ```

- Mostrar coincidencias de `NNNN` con una línea de contexto:
  ```bash
  grep -C1 "NNNN" secuencias.fasta
  ```

---

### Nota de usuario avanzado
- Estas opciones convierten a `grep` en una herramienta **más flexible y analítica**.  
- En bioinformática, permiten no solo detectar motivos, sino también **contarlos, excluirlos y verlos en contexto**.  
- Filosofía Unix: usa `grep` como un filtro inteligente que te da control sobre la salida.

---

## 6. Aplicaciones de grep en bioinformática

Ahora que dominamos las bases y opciones avanzadas, vamos a ver cómo `grep` se aplica directamente en **FASTA, CSV y GFF**, resolviendo problemas típicos de bioinformática.

---

### 6.1 Buscar motivos en secuencias FASTA
Archivo `secuencias.fasta`:
```
>seq1
ATGCGTACGTAGCTAGCTAG
>seq2
ATCGNNNNATCG
>seq3
GGGGGGGGGG
```

- **Detectar codones de inicio (`ATG`)**:
  ```bash
  grep "ATG" secuencias.fasta
  ```
  Resultado: imprime las secuencias que contienen `ATG`.

- **Detectar codones de parada (`TAA`, `TAG`, `TGA`)**:
  ```bash
  grep -E "TAA|TAG|TGA" secuencias.fasta
  ```
  Resultado: imprime las secuencias que contienen cualquiera de los tres codones.

---

### 6.2 Filtrar genes en CSV de expresión
Archivo `expresion.csv`:
```
12,EGFR,down
4,PTEN,down
11,TP53,up
15,EGFR,up
```

- **Genes sobreexpresados (`up`)**:
  ```bash
  grep "up" expresion.csv
  ```
  Resultado:
  ```
  11,TP53,up
  15,EGFR,up
  ```

- **Genes subexpresados (`down`)**:
  ```bash
  grep "down" expresion.csv
  ```

---

### 6.3 Extraer IDs de encabezados FASTA
```bash
grep "^>" secuencias.fasta
```

Resultado:
```
>seq1
>seq2
>seq3
```

**Explicación**:  
- `^>` → busca encabezados que comienzan con `>`.  
- Útil para obtener rápidamente la lista de IDs.

---

### 6.4 Detectar regiones GC-rich
Ejemplo: buscar secuencias con al menos 6 `G` consecutivos.

```bash
grep "G\{6,\}" secuencias.fasta
```

Resultado:
```
GGGGGGGGGG
```

**Explicación**:  
- `G\{6,\}` → busca 6 o más repeticiones de `G`.  
- Útil para identificar regiones con alto contenido GC.

---

### 6.5 Filtrar anotaciones en GFF
Archivo `anotacion.gff`:
```
chr1 . gene 1000 5000 . + . ID=gene1;Name=TP53
chr1 . exon 1000 1200 . + . ID=exon1;Parent=gene1
chr2 . gene 2000 6000 . - . ID=gene2;Name=EGFR
```

- **Extraer solo genes**:
  ```bash
  grep "gene" anotacion.gff
  ```

- **Extraer solo exones**:
  ```bash
  grep "exon" anotacion.gff
  ```

---

### Nota de usuario avanzado
- `grep` es excelente para **detección rápida**: motivos en FASTA, estados en CSV, atributos en GFF.  
- Su ventaja es la **velocidad y simplicidad** en archivos grandes.  
- Filosofía Unix: usa `grep` como primer filtro para localizar patrones antes de análisis más complejos con `awk` o `sed`.

---

## 7. Casos prácticos integradores con grep

Aquí reunimos todo lo aprendido en **FASTA, CSV y GFF**, aplicando `grep` en pipelines bioinformáticos que combinan varios formatos. La idea es mostrar cómo usarlo en flujos completos de búsqueda y filtrado.

---

### 7.1 Pipeline FASTA + CSV: genes sobreexpresados presentes en secuencias
Supongamos que tenemos un archivo `expresion.csv` con genes en estado “up” y un archivo `secuencias.fasta` con secuencias genómicas.

- **Extraer genes sobreexpresados**:
  ```bash
  grep "up" expresion.csv | cut -d',' -f2 > genes_up.txt
  ```

- **Buscar esos genes en FASTA**:
  ```bash
  grep -f genes_up.txt secuencias.fasta
  ```

**Explicación**:  
- Primer comando → filtra genes “up” y guarda sus nombres.  
- Segundo comando → busca esos nombres en el FASTA.  
- Resultado: lista de secuencias correspondientes a genes sobreexpresados.

---

### 7.2 Pipeline FASTA: detectar motivos y mostrar contexto
Ejemplo: buscar secuencias con el motivo `ATG` y mostrar la línea anterior (encabezado).

```bash
grep -B1 "ATG" secuencias.fasta
```

**Explicación**:  
- `-B1` → muestra una línea antes de cada coincidencia.  
- Útil para ver el encabezado junto con la secuencia que contiene el motivo.

---

### 7.3 Pipeline GFF: extraer genes con nombre específico
Archivo `anotacion.gff`:
```
chr1 . gene 1000 5000 . + . ID=gene1;Name=TP53
chr1 . exon 1000 1200 . + . ID=exon1;Parent=gene1
chr2 . gene 2000 6000 . - . ID=gene2;Name=EGFR
```

Ejemplo: buscar genes TP53 y EGFR.
```bash
grep -E "Name=(TP53|EGFR)" anotacion.gff
```

**Explicación**:  
- `Name=(TP53|EGFR)` → detecta genes con esos nombres.  
- Útil para filtrar anotaciones de interés.

---

### 7.4 Pipeline BED: filtrar regiones por rango
Archivo `regiones.bed`:
```
chr1 1000 1500 region1
chr1 2000 2100 region2
chr2 3000 4000 region3
```

Ejemplo: buscar regiones en `chr1`.
```bash
grep "^chr1" regiones.bed
```

**Explicación**:  
- `^chr1` → busca líneas que comienzan con `chr1`.  
- Útil para filtrar regiones de un cromosoma específico.

---

### 7.5 Pipeline combinado: genes “up” y anotaciones GFF
Ejemplo: filtrar anotaciones de genes sobreexpresados.

```bash
grep "up" expresion.csv | cut -d',' -f2 > genes_up.txt
grep -f genes_up.txt anotacion.gff
```

**Explicación**:  
- Primer comando → obtiene lista de genes “up”.  
- Segundo comando → busca esos genes en el archivo GFF.  
- Resultado: anotaciones de genes sobreexpresados.

---

### Nota de usuario avanzado
- `grep` se integra fácilmente en pipelines con `cut`, `sort`, `uniq`, y otros comandos.  
- En bioinformática, esto permite construir flujos rápidos para **filtrar secuencias, genes y anotaciones**.  
- Filosofía Unix: piensa en `grep` como el detector inicial que alimenta el resto del análisis.

---

## 8. Buenas prácticas con grep en bioinformática

Para que `grep` sea realmente útil en proyectos bioinformáticos, no basta con saber los comandos: es fundamental aplicar **buenas prácticas** que aseguren claridad, reproducibilidad y eficiencia en el trabajo con datos.

---

### 8.1 Documentar comandos y scripts
Cuando un comando es largo o se usa repetidamente, conviene guardarlo en un archivo `.sh` o `.grep` con comentarios.

Ejemplo: `buscar_motivos.sh`
```bash
#!/bin/bash
# Script para buscar motivos genómicos en FASTA
# - Detecta codones de inicio ATG
# - Detecta codones de parada TAA, TAG, TGA

grep "ATG" secuencias.fasta
grep -E "TAA|TAG|TGA" secuencias.fasta
```

**Buenas prácticas**:  
- Usa comentarios (`#`) para explicar cada paso.  
- Incluye instrucciones de uso al inicio.  
- Facilita que otros investigadores comprendan y reutilicen el script.

---

### 8.2 Modularizar tareas
En lugar de un solo comando enorme, divide el flujo en pasos claros:

```bash
# Paso 1: extraer encabezados
grep "^>" secuencias.fasta > encabezados.txt

# Paso 2: buscar codones de inicio
grep "ATG" secuencias.fasta > con_inicio.txt

# Paso 3: buscar codones de parada
grep -E "TAA|TAG|TGA" secuencias.fasta > con_parada.txt
```

**Ventaja**: cada archivo intermedio puede revisarse y validarse antes de continuar.

---

### 8.3 Reproducibilidad
- **Guarda los comandos en scripts** en lugar de ejecutarlos solo en la terminal.  
- **Usa nombres descriptivos** para archivos intermedios y resultados.  
- **Incluye metadatos**: fecha, parámetros usados, versión de los datos.  
- Esto asegura que otros puedan repetir tu análisis exactamente.

---

### 8.4 Integración con otras herramientas
`grep` es más poderoso cuando se combina con utilidades clásicas de Unix:

```bash
grep "up" expresion.csv | cut -d',' -f2 | sort | uniq
```

- `grep` → filtra genes en estado “up”.  
- `cut` → extrae la columna de genes.  
- `sort | uniq` → ordena y elimina duplicados.  

**Buenas prácticas**:  
- Usa pipelines para tareas rápidas.  
- Documenta cada paso para que el flujo sea entendible.

---

### 8.5 Estilo y legibilidad
- **Divide comandos largos con `\`** para que no se desborden en PDF ni en la terminal.  
- **Indentación**: alinea bloques para que sean fáciles de leer.  
- **Comentarios**: explica cada paso, incluso si parece obvio.  

---

### Nota de usuario avanzado
- La claridad y reproducibilidad son tan importantes como el resultado.  
- `grep` puede ser un detector muy preciso, pero solo si los scripts están bien documentados.  
- Filosofía Unix: piensa en tus comandos como piezas de un puzzle que otros puedan reutilizar.

---

## 9. Ejemplos avanzados de grep en bioinformática

En este capítulo veremos usos más sofisticados de `grep`, aprovechando regex extendidas, integración con otras utilidades y búsquedas masivas en directorios. Estos ejemplos muestran cómo llevar `grep` al siguiente nivel en análisis bioinformáticos.

---

### 9.1 Uso de `grep -P` (regex Perl)
`grep -P` permite usar expresiones regulares más potentes (PCRE).

Ejemplo: detectar secuencias con un motivo repetido de tres bases (`ATG`) seguido de un codón de parada.
```bash
grep -P "^ATG(?:[ATGC]{3})+(TAA|TAG|TGA)$" secuencias.fasta
```

**Explicación**:  
- `(?:[ATGC]{3})+` → grupo no capturante de tripletes repetidos.  
- `(TAA|TAG|TGA)$` → termina en un codón de parada.  
- Útil para identificar secuencias completas de genes con regex más expresivas.

---

### 9.2 Integración con `find` para búsquedas masivas
Ejemplo: buscar motivos `ATG` en todos los archivos FASTA de un directorio.

```bash
find ./datos -name "*.fasta" -exec grep -H "ATG" {} \;
```

**Explicación**:  
- `find` → localiza todos los archivos `.fasta`.  
- `-exec grep -H` → ejecuta `grep` en cada archivo, mostrando nombre del archivo.  
- Útil para explorar colecciones grandes de secuencias.

---

### 9.3 Combinación con `xargs`
Ejemplo: buscar genes “up” en múltiples archivos CSV.

```bash
find ./expresion -name "*.csv" | xargs grep "up"
```

**Explicación**:  
- `find` → lista todos los CSV.  
- `xargs grep "up"` → aplica `grep` a cada archivo listado.  
- Útil para filtrar datos de expresión en lotes.

---

### 9.4 Búsquedas complejas en GFF
Ejemplo: extraer genes en cromosoma 1 con nombre TP53 o EGFR.

```bash
grep -P "^chr1.*Name=(TP53|EGFR)" anotacion.gff
```

**Explicación**:  
- `^chr1` → inicio de línea con cromosoma 1.  
- `.*Name=(TP53|EGFR)` → busca genes con esos nombres.  
- Útil para filtrar anotaciones específicas en un cromosoma.

---

### 9.5 Exploración masiva de directorios de secuencias
Ejemplo: detectar secuencias con repeticiones de `N` en todos los FASTA.

```bash
grep -R "N\{5,\}" ./genomas/
```

**Explicación**:  
- `-R` → búsqueda recursiva en subdirectorios.  
- `N\{5,\}` → busca secuencias con 5 o más `N` consecutivos.  
- Útil para detectar regiones ambiguas en colecciones genómicas.

---

### Nota de usuario avanzado
- `grep -P` abre la puerta a regex más complejas y expresivas.  
- Combinado con `find` y `xargs`, permite búsquedas masivas en directorios.  
- En bioinformática, esto significa poder explorar **colecciones completas de genomas y anotaciones** con un solo comando.  
- Filosofía Unix: piensa en `grep` como un radar que puede escanear miles de archivos en segundos.

---

## 9. Ejemplos avanzados de grep en bioinformática

En este capítulo veremos usos más sofisticados de `grep`, aprovechando regex extendidas, integración con otras utilidades y búsquedas masivas en directorios. Estos ejemplos muestran cómo llevar `grep` al siguiente nivel en análisis bioinformáticos.

---

### 9.1 Uso de `grep -P` (regex Perl)
`grep -P` permite usar expresiones regulares más potentes (PCRE).

Ejemplo: detectar secuencias con un motivo repetido de tres bases (`ATG`) seguido de un codón de parada.
```bash
grep -P "^ATG(?:[ATGC]{3})+(TAA|TAG|TGA)$" secuencias.fasta
```

**Explicación**:  
- `(?:[ATGC]{3})+` → grupo no capturante de tripletes repetidos.  
- `(TAA|TAG|TGA)$` → termina en un codón de parada.  
- Útil para identificar secuencias completas de genes con regex más expresivas.

---

### 9.2 Integración con `find` para búsquedas masivas
Ejemplo: buscar motivos `ATG` en todos los archivos FASTA de un directorio.

```bash
find ./datos -name "*.fasta" -exec grep -H "ATG" {} \;
```

**Explicación**:  
- `find` → localiza todos los archivos `.fasta`.  
- `-exec grep -H` → ejecuta `grep` en cada archivo, mostrando nombre del archivo.  
- Útil para explorar colecciones grandes de secuencias.

---

### 9.3 Combinación con `xargs`
Ejemplo: buscar genes “up” en múltiples archivos CSV.

```bash
find ./expresion -name "*.csv" | xargs grep "up"
```

**Explicación**:  
- `find` → lista todos los CSV.  
- `xargs grep "up"` → aplica `grep` a cada archivo listado.  
- Útil para filtrar datos de expresión en lotes.

---

### 9.4 Búsquedas complejas en GFF
Ejemplo: extraer genes en cromosoma 1 con nombre TP53 o EGFR.

```bash
grep -P "^chr1.*Name=(TP53|EGFR)" anotacion.gff
```

**Explicación**:  
- `^chr1` → inicio de línea con cromosoma 1.  
- `.*Name=(TP53|EGFR)` → busca genes con esos nombres.  
- Útil para filtrar anotaciones específicas en un cromosoma.

---

### 9.5 Exploración masiva de directorios de secuencias
Ejemplo: detectar secuencias con repeticiones de `N` en todos los FASTA.

```bash
grep -R "N\{5,\}" ./genomas/
```

**Explicación**:  
- `-R` → búsqueda recursiva en subdirectorios.  
- `N\{5,\}` → busca secuencias con 5 o más `N` consecutivos.  
- Útil para detectar regiones ambiguas en colecciones genómicas.

---

### Nota de usuario avanzado
- `grep -P` abre la puerta a regex más complejas y expresivas.  
- Combinado con `find` y `xargs`, permite búsquedas masivas en directorios.  
- En bioinformática, esto significa poder explorar **colecciones completas de genomas y anotaciones** con un solo comando.  
- Filosofía Unix: piensa en `grep` como un radar que puede escanear miles de archivos en segundos.

---
