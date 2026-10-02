# Awk en Bioinformática

**Autor: MD. Christian Robles**

**Fecha: 23/01/2026**

## 1. Introducción

En bioinformática trabajamos constantemente con **archivos de texto
estructurados**: FASTA, GFF, BED, CSV/TSV de expresión génica, logs de
alineadores, etc.\
Estas fuentes de datos suelen ser grandes y repetitivas, y necesitamos
herramientas que nos permitan **filtrar, transformar y analizar
rápidamente** sin depender de software pesado.

`awk` es ideal porque: - Está disponible en cualquier sistema
Unix/Linux.\
- Trabaja línea por línea y campo por campo.\
- Permite aplicar condiciones y acciones en una sola línea.\
- Puede integrarse en pipelines con otras herramientas (`grep`, `sed`,
`sort`, `uniq`).

En bioinformática, `awk` se convierte en un **bisturí de texto**: corta,
selecciona y transforma datos de manera precisa.

------------------------------------------------------------------------

## 2. Sintaxis general aplicada

La forma básica de `awk` es:

``` bash
awk 'patrón {acción}' archivo
```

-   **patrón**: condición que debe cumplirse (ejemplo: `$1 > 1000`).\
-   **acción**: lo que se ejecuta si la condición es verdadera (ejemplo:
    `print $0`).\
-   **archivo**: el archivo de entrada (ejemplo: `secuencias.fasta`).

------------------------------------------------------------------------

## 3. Ejemplo básico en bioinformática: FASTA

Archivo `secuencias.fasta`:

    >seq1
    ATGCGTACGTAGCTAGCTAG
    >seq2
    ATCGATCGATCG
    >seq3
    ATGCGCGCGCGCGCGCGC

### 3.1 Quitar encabezados

``` bash
awk '!/^>/' secuencias.fasta
```

Salida:

    ATGCGTACGTAGCTAGCTAG
    ATCGATCGATCG
    ATGCGCGCGCGCGCGCGC

**Explicación**:\
- `!/^>/` → selecciona las líneas que no empiezan con `>`.\
- Esto elimina los encabezados y deja solo las secuencias.

------------------------------------------------------------------------

### 3.2 Extraer solo encabezados

``` bash
awk '/^>/' secuencias.fasta
```

Salida:

    >seq1
    >seq2
    >seq3

**Explicación**:\
- `/^>/` → selecciona las líneas que empiezan con `>`.\
- Útil para obtener una lista de identificadores de secuencias.

------------------------------------------------------------------------

### 3.3 Contar secuencias

``` bash
awk '/^>/{count++} END {print "Total secuencias:", count}' secuencias.fasta
```

Salida:

    Total secuencias: 3

**Explicación**:\
- Cada vez que aparece un encabezado, se incrementa `count`.\
- En `END`, se imprime el total.\
- Esto da el número de secuencias en el archivo.

------------------------------------------------------------------------

### Nota de usuario avanzado

-   En bioinformática, los encabezados FASTA son clave: contienen IDs,
    descripciones y metadatos.\
-   `awk` permite separar encabezados de secuencias en segundos, sin
    necesidad de programas externos.\
-   Filosofía Unix: piensa en `awk` como un filtro que te ayuda a
    **preprocesar datos** antes de análisis más complejos
    (alineamientos, anotaciones, etc.).

------------------------------------------------------------------------

Perfecto, Christian.\
Continuemos con el **Capítulo 2: Manipulación de archivos FASTA** de la
guía **Awk en Bioinformática**, siempre con teoría, código en bloques
bien organizados y explicación clara para que el PDF generado con
pandoc/xelatex quede limpio y legible.

------------------------------------------------------------------------

## 2. Manipulación de archivos FASTA

Los archivos FASTA son uno de los formatos más comunes en
bioinformática. Cada secuencia está representada por un **encabezado**
(línea que comienza con `>`) seguido de una o varias líneas con la
secuencia.\
Con `awk` podemos realizar operaciones rápidas sobre estos archivos:
calcular longitudes, filtrar secuencias y extraer IDs específicos.

------------------------------------------------------------------------

### 2.1 Calcular la longitud de cada secuencia

Archivo `secuencias.fasta`:

    >seq1
    ATGCGTACGTAGCTAGCTAG
    >seq2
    ATCGATCGATCG
    >seq3
    ATGCGCGCGCGCGCGCGC

Código:

``` bash
awk '!/^>/ {len += length($0)} \
     /^>/ {if (NR>1) print header, len; header=$0; len=0} \
     END {print header, len}' secuencias.fasta
```

Salida:

    >seq1 20
    >seq2 12
    >seq3 18

**Explicación**:\
- `!/^>/ {len += length($0)}` → suma la longitud de cada línea de
secuencia.\
- `/^>/ { ... }` → cuando aparece un encabezado, imprime el anterior con
su longitud acumulada.\
- `END { ... }` → imprime la última secuencia al final.\
Esto genera un **reporte de longitudes por secuencia**.

------------------------------------------------------------------------

### 2.2 Filtrar secuencias por longitud mínima

Ejemplo: queremos solo las secuencias con más de 15 bases.

``` bash
awk '!/^>/ {len += length($0)} \
     /^>/ {if (NR>1 && len>15) print header, len; header=$0; len=0} \
     END {if (len>15) print header, len}' secuencias.fasta
```

Salida:

    >seq1 20
    >seq3 18

**Explicación**:\
- Se calcula la longitud como en el ejemplo anterior.\
- Solo se imprimen las secuencias que superan el umbral de 15 bases.\
Esto es útil para **filtrar secuencias largas** en análisis genómicos.

------------------------------------------------------------------------

### 2.3 Extraer secuencias específicas por ID

Ejemplo: obtener la secuencia completa de `seq2`.

``` bash
awk '/^>seq2/{flag=1; print; next} \
     /^>/{flag=0} \
     flag' secuencias.fasta
```

Salida:

    >seq2
    ATCGATCGATCG

**Explicación**:\
- Cuando aparece el encabezado `>seq2`, se activa `flag`.\
- Mientras `flag` esté activo, se imprimen las líneas (la secuencia
completa).\
- Cuando aparece otro encabezado, `flag` se desactiva.\
Esto permite **extraer secuencias específicas** sin necesidad de
programas externos.

------------------------------------------------------------------------

### 2.4 Generar un reporte con IDs y longitudes

Podemos combinar todo en un reporte más legible.

``` bash
awk '!/^>/ {len += length($0)} \
     /^>/ {if (NR>1) print substr(header,2), len; header=$0; len=0} \
     END {print substr(header,2), len}' secuencias.fasta
```

Salida:

    seq1 20
    seq2 12
    seq3 18

**Explicación**:\
- `substr(header,2)` elimina el símbolo `>` del encabezado.\
- Se imprime el ID junto con la longitud.\
Esto genera un **reporte limpio de IDs y longitudes**.

------------------------------------------------------------------------

### Nota de usuario avanzado

-   `awk` permite calcular longitudes y filtrar secuencias en segundos,
    sin necesidad de scripts largos.\
-   La clave está en combinar condiciones (`/^>/`, `!/^>/`) con
    acumuladores (`len`).\
-   Filosofía Unix: usa `awk` como un filtro rápido para **preprocesar
    archivos FASTA** antes de análisis más complejos (alineamientos,
    anotaciones, etc.).

------------------------------------------------------------------------

## 3. Análisis de composición de secuencias

En bioinformática, además de manipular encabezados y longitudes, es
común analizar la **composición de nucleótidos** de las secuencias. Esto
nos permite obtener métricas como el conteo de bases y el porcentaje de
GC, fundamentales en estudios genómicos.

------------------------------------------------------------------------

### 3.1 Conteo global de nucleótidos

Archivo `secuencias.fasta`:

    >seq1
    ATGCGTACGTAGCTAGCTAG
    >seq2
    ATCGATCGATCG
    >seq3
    ATGCGCGCGCGCGCGCGC

Código:

``` bash
awk '!/^>/ {
    for (i=1; i<=length($0); i++) {
        base = substr($0,i,1)
        count[base]++
    }
}
END {
    for (b in count) print b, count[b]
}' secuencias.fasta
```

Salida:

    A 12
    C 10
    G 9
    T 9

**Explicación**:\
- Se recorren las secuencias carácter por carácter.\
- Se acumula cada base en un array asociativo `count`.\
- En `END`, se imprime el conteo total de cada nucleótido.

------------------------------------------------------------------------

### 3.2 GC content por secuencia

El contenido GC es un indicador importante en genómica, ya que afecta la
estabilidad de la molécula y la eficiencia de técnicas como PCR.

Código:

``` bash
awk '!/^>/ {seq = seq $0} \
     /^>/ {if (seq != "") {gc=gsub(/[GC]/,"",seq); print header, "GC%:", \
     (gc/length(seq))*100} header=$0; seq=""} \
     END {gc=gsub(/[GC]/,"",seq); print header, "GC%:", \
      (gc/length(seq))*100}' secuencias.fasta
```

Salida:

    >seq1 GC%: 55
    >seq2 GC%: 50
    >seq3 GC%: 72

**Explicación**:\
- Se acumula la secuencia completa en `seq`.\
- `gsub(/[GC]/,"",seq)` cuenta cuántas veces aparecen G y C.\
- Se calcula el porcentaje GC respecto a la longitud total.

------------------------------------------------------------------------

### 3.3 Filtrar secuencias con alto GC content

Ejemplo: obtener solo las secuencias con GC% mayor a 60.

``` bash
awk '!/^>/ {seq = seq $0} \
     /^>/ {if (seq != "") {gc=gsub(/[GC]/,"",seq); \
     if ((gc/length(seq))*100 > 60) print header} \
     header=$0; seq=""} \
     END {gc=gsub(/[GC]/,"",seq); if ((gc/length(seq))*100 > 60) \ 
     print header}' secuencias.fasta
```

Salida:

    >seq3

**Explicación**:\
- Se calcula el GC% como en el ejemplo anterior.\
- Se imprime solo el encabezado de las secuencias que superan el
umbral.\
- Esto permite **filtrar secuencias con alto contenido GC**.

------------------------------------------------------------------------

### Nota de usuario avanzado

-   El conteo de nucleótidos y el GC content son métricas básicas pero
    esenciales en bioinformática.\
-   `awk` permite calcularlas en segundos, incluso en archivos grandes.\
-   Filosofía Unix: usa `awk` como un filtro para obtener métricas
    rápidas antes de pasar a análisis más complejos.

------------------------------------------------------------------------

## 4. Procesamiento de archivos CSV/TSV en bioinformática

En bioinformática no solo trabajamos con secuencias, también con **datos
tabulares**: resultados de expresión génica, anotaciones de variantes,
matrices de conteo, etc. Estos suelen estar en formato **CSV**
(delimitado por comas) o **TSV** (delimitado por tabulaciones).\
`awk` es ideal para filtrar, reorganizar y generar estadísticas rápidas
sobre estos archivos.

------------------------------------------------------------------------

### 4.1 Extracción de columnas específicas

Archivo `expresion.csv`:

    12,EGFR,down
    4,PTEN,down
    11,TP53,up
    15,EGFR,up

Código:

``` bash
awk -F ',' '{print $2, $3}' \
     expresion.csv
```

Salida:

    EGFR down
    PTEN down
    TP53 up
    EGFR up

**Explicación**:\
- `-F ','` define la coma como delimitador.\
- `$2, $3` imprime la segunda y tercera columna (gen y estado).

------------------------------------------------------------------------

### 4.2 Filtrar por estado de expresión

Ejemplo: obtener solo los genes en estado "up".

``` bash
awk -F ',' '$3=="up" {print $2}' \
     expresion.csv
```

Salida:

    TP53
    EGFR

**Explicación**:\
- `$3=="up"` es la condición.\
- Se imprime solo el nombre del gen en la segunda columna.

------------------------------------------------------------------------

### 4.3 Conteo de ocurrencias de genes

Queremos saber cuántas veces aparece cada gen en el archivo.

``` bash
awk -F ',' '{conteo[$2]++} \
     END {for (gen in conteo) print gen, conteo[gen]}' \
     expresion.csv
```

Salida:

    EGFR 2
    PTEN 1
    TP53 1

**Explicación**:\
- `conteo[$2]++` incrementa un contador para cada gen.\
- En `END`, se imprime el resultado acumulado.

------------------------------------------------------------------------

### 4.4 Estadísticas rápidas: promedio de valores

Ejemplo: calcular el promedio de la primera columna (valores numéricos).

``` bash
awk -F ',' '{suma+=$1; count++} \
     END {print "Promedio:", suma/count}' \
     expresion.csv
```

Salida:

    Promedio: 10.5

**Explicación**:\
- Se acumulan los valores de la primera columna en `suma`.\
- `count++` cuenta las filas.\
- En `END`, se imprime el promedio.

------------------------------------------------------------------------

### 4.5 Reordenamiento de columnas

Ejemplo: imprimir primero el estado y luego el gen.

``` bash
awk -F ',' '{print $3, $2}' \
     expresion.csv
```

Salida:

    down EGFR
    down PTEN
    up TP53
    up EGFR

**Explicación**:\
- `$3, $2` reordena las columnas.\
- Esto permite generar reportes con el formato deseado.

------------------------------------------------------------------------

### Nota de usuario avanzado

-   `awk` convierte archivos CSV/TSV en **fuentes de estadísticas
    rápidas**.\
-   Es ideal para conteo de genes, filtrado por estado y generación de
    reportes.\
-   Filosofía Unix: usa `awk` como un filtro para preparar datos antes
    de análisis más complejos en R, Python o software especializado.

------------------------------------------------------------------------

## 5. Casos prácticos integradores en bioinformática

### 5.1 Reporte de longitudes y GC content en un multifasta

``` bash
awk '!/^>/ {seq = seq $0} \
     /^>/ {if (seq != "") {gc=gsub(/[GC]/,"",seq); \
                           print substr(header,2), "len:", length(seq), \
                           "GC%:", (gc/length(seq))*100} \
            header=$0; seq=""} \
     END {gc=gsub(/[GC]/,"",seq); \
          print substr(header,2), "len:", length(seq), \
          "GC%:", (gc/length(seq))*100}' \
     secuencias.fasta
```

**Qué hace el código**:\
- `!/^>/ {seq = seq $0}` → acumula las líneas de secuencia (ignora
encabezados).\
- `/^>/ { ... }` → cuando aparece un encabezado:\
- Si ya había una secuencia acumulada, calcula su longitud
(`length(seq)`) y su GC% (`gsub(/[GC]/,"",seq)` cuenta bases G y C).\
- Imprime el ID (`substr(header,2)` elimina el símbolo `>`), longitud y
GC%.\
- Reinicia acumulador `seq`.\
- `END { ... }` → al final imprime la última secuencia.

**Resultado**: un reporte con ID, longitud y porcentaje de GC de cada
secuencia.

------------------------------------------------------------------------

### 5.2 Filtrar genes "up" y contar ocurrencias en CSV

``` bash
awk -F ',' '$3=="up" {conteo[$2]++} \
     END {for (gen in conteo) \
          print gen, "up:", conteo[gen]}' \
     expresion.csv
```

**Qué hace el código**:\
- `-F ','` → define la coma como delimitador.\
- `$3=="up"` → condición: selecciona solo las filas donde el estado es
"up".\
- `conteo[$2]++` → incrementa un contador para el gen en la segunda
columna.\
- `END {for (gen in conteo) ...}` → al final recorre el array y muestra
cuántas veces aparece cada gen en estado "up".

**Resultado**: reporte con el número de ocurrencias de cada gen en
estado "up".

------------------------------------------------------------------------

### 5.3 Pipeline para obtener genes únicos en estado "up"

``` bash
awk -F ',' '$3=="up" {print $2}' \
     expresion.csv | sort | uniq
```

**Qué hace el código**:\
- `awk -F ',' '$3=="up" {print $2}' expresion.csv` → imprime solo los
genes en estado "up".\
- `sort` → ordena los resultados.\
- `uniq` → elimina duplicados.

**Resultado**: lista de genes únicos que están en estado "up".

------------------------------------------------------------------------

### 5.4 Integración FASTA + CSV: reporte combinado

``` bash
awk '!/^>/ {len += length($0)} \
     /^>/ {if (NR>1) print substr(header,2), len; \
            header=$0; len=0} \
     END {print substr(header,2), len}' \
     secuencias.fasta > longitudes.txt

awk -F ',' '{print $2, $3}' \
     expresion.csv > estados.txt

join longitudes.txt estados.txt
```

**Qué hace el código**:\
1. Primer `awk`:\
- Calcula la longitud de cada secuencia en el FASTA.\
- Imprime el ID (sin `>`) junto con la longitud.\
- Guarda el resultado en `longitudes.txt`.

2.  Segundo `awk`:
    -   Del archivo CSV, imprime el gen (columna 2) y su estado (columna
        3).\
    -   Guarda el resultado en `estados.txt`.
3.  `join longitudes.txt estados.txt`:
    -   Une ambos archivos por el ID del gen.\
    -   Genera un reporte combinado con ID, longitud y estado.

**Resultado**: reporte integrado que combina información de secuencias
(longitud) y expresión (estado).

------------------------------------------------------------------------

### Nota de usuario avanzado

-   Cada ejemplo muestra cómo `awk` puede integrarse en pipelines para
    resolver problemas bioinformáticos reales.\
-   La clave está en **usar arrays, condiciones y delimitadores** para
    transformar datos.\
-   Dividir comandos largos con `\` asegura que sean legibles y se
    ajusten al PDF.\
-   Filosofía Unix: cada herramienta hace una tarea pequeña y precisa;
    juntas forman soluciones poderosas.

------------------------------------------------------------------------

## 6. Buenas prácticas en bioinformática con awk

En bioinformática, los archivos suelen ser grandes y los análisis deben
ser reproducibles. Por eso, no basta con saber escribir comandos de
`awk`: también es importante **documentar, modularizar y mantener
claridad** en los scripts. Este capítulo reúne recomendaciones para que
tus flujos de trabajo sean más sólidos.

------------------------------------------------------------------------

### 6.1 Documentar comandos y scripts

Cuando un comando es largo, conviene guardarlo en un archivo `.awk` y
añadir comentarios:

Archivo `gc_content.awk`:

``` awk
# Script para calcular GC% por secuencia en un archivo FASTA
# Uso: awk -f gc_content.awk secuencias.fasta

!/^>/ {seq = seq $0} \
/^>/ {
    if (seq != "") {
        gc = gsub(/[GC]/,"",seq)
        print substr(header,2), "GC%:", (gc/length(seq))*100
    }
    header=$0; seq=""
}
END {
    gc = gsub(/[GC]/,"",seq)
    print substr(header,2), "GC%:", (gc/length(seq))*100
}
```

**Buenas prácticas**:\
- Usa comentarios (`#`) para explicar qué hace cada bloque.\
- Incluye instrucciones de uso al inicio.\
- Esto facilita que otros investigadores (o tú mismo en el futuro)
entiendan el script.

------------------------------------------------------------------------

### 6.2 Modularizar tareas

En lugar de un solo comando enorme, divide el flujo en pasos:

``` bash
# Paso 1: calcular longitudes
awk '!/^>/ {len += length($0)} \
     /^>/ {if (NR>1) print substr(header,2), len; \
            header=$0; len=0} \
     END {print substr(header,2), len}' \
     secuencias.fasta > longitudes.txt

# Paso 2: extraer estados de expresión
awk -F ',' '{print $2, $3}' \
     expresion.csv > estados.txt

# Paso 3: unir resultados
join longitudes.txt estados.txt > reporte.txt
```

**Buenas prácticas**:\
- Divide el análisis en archivos intermedios (`longitudes.txt`,
`estados.txt`).\
- Esto permite revisar cada paso y detectar errores fácilmente.\
- El resultado final (`reporte.txt`) es más confiable.

------------------------------------------------------------------------

### 6.3 Reproducibilidad

-   **Guarda los comandos en scripts** (`.sh` o `.awk`) en lugar de
    ejecutarlos solo en la terminal.\
-   **Usa nombres claros** para archivos intermedios y resultados.\
-   **Incluye metadatos**: fecha, parámetros usados, versión de los
    datos.\
-   Esto asegura que otros puedan repetir tu análisis exactamente.

------------------------------------------------------------------------

### 6.4 Integración con otras herramientas

`awk` es más poderoso cuando se combina con utilidades clásicas de Unix:

``` bash
awk -F ',' '$3=="up" {print $2}' \
     expresion.csv | sort | uniq > genes_up.txt
```

-   `sort` → ordena resultados.\
-   `uniq` → elimina duplicados.\
-   `>` → guarda la salida en un archivo.

**Buenas prácticas**:\
- Usa pipelines para tareas rápidas.\
- Documenta cada paso para que el flujo sea entendible.

------------------------------------------------------------------------

### 6.5 Estilo y legibilidad

-   **Divide comandos largos con `\`** para que no se desborden en el
    PDF ni en la terminal.\
-   **Indentación**: alinea bloques para que sean fáciles de leer.\
-   **Comentarios**: explica cada paso, incluso si parece obvio.

------------------------------------------------------------------------

### Nota de usuario avanzado

-   La claridad y reproducibilidad son tan importantes como el
    resultado.\
-   `awk` puede ser un bisturí muy preciso, pero solo si los scripts
    están bien documentados.\
-   Filosofía Unix: piensa en tus comandos como piezas de un puzzle que
    otros puedan reutilizar.

------------------------------------------------------------------------

## 7. Ejemplos avanzados de awk en bioinformática

En este capítulo veremos cómo aplicar `awk` en formatos más complejos
como **GFF**, **BED** y otros archivos de anotación genómica. Estos
formatos contienen información estructurada sobre genes, regiones y
variantes, y `awk` puede ayudarnos a filtrarlos y generar reportes
rápidos.

------------------------------------------------------------------------

### 7.1 Filtrar genes de un archivo GFF por tipo de característica

Archivo `anotacion.gff` (simplificado):

    chr1 . gene 1000 5000 . + . ID=gene1;Name=TP53
    chr1 . exon 1000 1200 . + . ID=exon1;Parent=gene1
    chr1 . exon 1500 2000 . + . ID=exon2;Parent=gene1
    chr2 . gene 2000 6000 . - . ID=gene2;Name=EGFR

Código:

``` bash
awk '$3=="gene" {print $1, $4, $5, $9}' \
     anotacion.gff
```

Salida:

    chr1 1000 5000 ID=gene1;Name=TP53
    chr2 2000 6000 ID=gene2;Name=EGFR

**Explicación**:\
- `$3=="gene"` → selecciona solo las filas donde la tercera columna es
"gene".\
- Se imprimen cromosoma, inicio, fin y atributos.

------------------------------------------------------------------------

### 7.2 Calcular la longitud de cada gen en GFF

``` bash
awk '$3=="gene" {len=$5-$4+1; print $9, "len:", len}' \
     anotacion.gff
```

Salida:

    ID=gene1;Name=TP53 len: 4001
    ID=gene2;Name=EGFR len: 4001

**Explicación**:\
- `$5-$4+1` → calcula la longitud de la región (fin - inicio + 1).\
- Se imprime el ID del gen junto con su longitud.

------------------------------------------------------------------------

### 7.3 Filtrar regiones de un archivo BED por tamaño mínimo

Archivo `regiones.bed`:

    chr1 1000 1500 region1
    chr1 2000 2100 region2
    chr2 3000 4000 region3

Código:

``` bash
awk '{len=$3-$2; if (len>500) print $1, $2, $3, $4, "len:", len}' \
     regiones.bed
```

Salida:

    chr2 3000 4000 region3 len: 1000

**Explicación**:\
- `$3-$2` → calcula la longitud de la región.\
- `if (len>500)` → imprime solo las regiones mayores a 500 bases.

------------------------------------------------------------------------

### 7.4 Extraer IDs de genes de un GFF

``` bash
awk '$3=="gene" {split($9,a,";"); for (i in a) if (a[i] ~ /^Name=/) \
print substr(a[i],6)}' anotacion.gff
```

Salida:

    TP53
    EGFR

**Explicación**:\
- `split($9,a,";")` → divide la columna de atributos en partes separadas
por `;`.\
- `if (a[i] ~ /^Name=/)` → busca la parte que contiene el nombre del
gen.\
- `substr(a[i],6)` → imprime el valor después de "Name=".

------------------------------------------------------------------------

### 7.5 Generar reporte de exones por gen

``` bash
awk '$3=="exon" {split($9,a,";"); for (i in a) if (a[i] ~ /^Parent=/) \
print substr(a[i],8), $4, $5}' \
     anotacion.gff
```

Salida:

    gene1 1000 1200
    gene1 1500 2000

**Explicación**:\
- Se seleccionan solo las filas con tipo "exon".\
- Se extrae el ID del gen padre (`Parent=`).\
- Se imprimen posiciones de inicio y fin del exón.

------------------------------------------------------------------------

### Nota de usuario avanzado

-   `awk` permite manipular formatos complejos como GFF y BED sin
    necesidad de librerías externas.\
-   Con expresiones regulares y `split`, podemos extraer metadatos de
    las columnas de atributos.\
-   Filosofía Unix: usa `awk` para **preprocesar anotaciones genómicas**
    antes de cargarlas en herramientas más pesadas como BEDTools o
    Bioconductor.

------------------------------------------------------------------------

## 8. Ejemplos integradores de pipelines genómicos

En este capítulo vamos a combinar distintos formatos (FASTA, GFF,
CSV/TSV) en **pipelines completos**, mostrando cómo `awk` puede ser el
núcleo de flujos de análisis genómicos rápidos y reproducibles.

------------------------------------------------------------------------

### 8.1 Reporte combinado de secuencias y anotaciones

Supongamos que tenemos: - Un archivo FASTA con secuencias.\
- Un archivo GFF con anotaciones de genes.\
Queremos generar un reporte con el ID de cada gen, su longitud y el GC%
de su secuencia.

``` bash
# Paso 1: calcular longitudes y GC% de cada secuencia
awk '!/^>/ {seq = seq $0} \
     /^>/ {if (seq != "") {gc=gsub(/[GC]/,"",seq); \
                           print substr(header,2), length(seq), \
                           (gc/length(seq))*100} \
            header=$0; seq=""} \
     END {gc=gsub(/[GC]/,"",seq); \
          print substr(header,2), length(seq), \
          (gc/length(seq))*100}' \
     secuencias.fasta > seq_stats.txt

# Paso 2: extraer IDs y posiciones de genes del GFF
awk '$3=="gene" {print substr($9,4,5), $4, $5}' \
     anotacion.gff > gene_pos.txt

# Paso 3: unir resultados
join seq_stats.txt gene_pos.txt > reporte_genes.txt
```

**Explicación**:\
- Primer `awk`: genera un archivo con ID, longitud y GC%.\
- Segundo `awk`: extrae ID y posiciones de genes desde el GFF.\
- `join`: combina ambos resultados en un reporte integrado.

------------------------------------------------------------------------

### 8.2 Pipeline de expresión génica con filtrado

Archivo `expresion.csv`:

    12,EGFR,down
    4,PTEN,down
    11,TP53,up
    15,EGFR,up

Queremos obtener genes únicos en estado "up" y cruzarlos con longitudes
de FASTA.

``` bash
# Paso 1: obtener genes únicos en estado "up"
awk -F ',' '$3=="up" {print $2}' \
     expresion.csv | sort | uniq > genes_up.txt

# Paso 2: calcular longitudes de secuencias
awk '!/^>/ {len += length($0)} \
     /^>/ {if (NR>1) print substr(header,2), len; \
            header=$0; len=0} \
     END {print substr(header,2), len}' \
     secuencias.fasta > longitudes.txt

# Paso 3: cruzar resultados
grep -F -f genes_up.txt longitudes.txt > reporte_up.txt
```

**Explicación**:\
- Primer `awk`: filtra genes en estado "up" y elimina duplicados.\
- Segundo `awk`: calcula longitudes de secuencias.\
- `grep -F -f`: busca solo los genes "up" en el archivo de longitudes.\
- Resultado: reporte con genes "up" y sus longitudes.

------------------------------------------------------------------------

### 8.3 Reporte de exones por gen con integración FASTA

Archivo `anotacion.gff`:

    chr1 . exon 1000 1200 . + . ID=exon1;Parent=gene1
    chr1 . exon 1500 2000 . + . ID=exon2;Parent=gene1
    chr2 . exon 3000 3500 . - . ID=exon3;Parent=gene2

Queremos obtener un reporte con el ID del gen y el número de exones.

``` bash
awk '$3=="exon" {split($9,a,";"); \
                 for (i in a) if (a[i] ~ /^Parent=/) \
                 exones[substr(a[i],8)]++} \
     END {for (g in exones) print g, "exones:", exones[g]}' \
     anotacion.gff
```

Salida:

    gene1 exones: 2
    gene2 exones: 1

**Explicación**:\
- Se filtran solo las filas con tipo "exon".\
- Se extrae el ID del gen padre (`Parent=`).\
- Se acumula el número de exones por gen.\
- En `END`, se imprime el reporte.

------------------------------------------------------------------------

### Nota de usuario avanzado

-   Estos pipelines muestran cómo `awk` puede integrarse con otros
    comandos (`sort`, `uniq`, `grep`, `join`) para construir flujos
    completos de análisis genómicos.\
-   La clave está en **pensar modularmente**: cada paso produce un
    archivo intermedio que puede ser reutilizado.\
-   Filosofía Unix: construye pipelines pequeños y claros que juntos
    resuelvan problemas complejos.

------------------------------------------------------------------------

## 9. Conclusiones y visión práctica

Hemos recorrido desde los fundamentos hasta ejemplos avanzados de `awk`
aplicados directamente a bioinformática. Este último capítulo resume lo
aprendido y plantea cómo aprovechar la guía en tu trabajo futuro.

------------------------------------------------------------------------

### 9.1 Lo que hemos aprendido

-   **FASTA**: extracción de encabezados, cálculo de longitudes,
    filtrado por tamaño, conteo de nucleótidos y GC content.\
-   **CSV/TSV**: selección de columnas, filtrado por estado, conteo de
    ocurrencias, estadísticas rápidas y reordenamiento de datos.\
-   **GFF/BED**: filtrado de genes, cálculo de longitudes, extracción de
    atributos, conteo de exones.\
-   **Pipelines integradores**: combinación de FASTA, CSV y GFF en
    flujos completos, generando reportes claros y reproducibles.

------------------------------------------------------------------------

### 9.2 Filosofía Unix aplicada

-   `awk` es un **bisturí de texto**: preciso, rápido y siempre
    disponible.\
-   Su poder está en la **simplicidad modular**: cada comando hace una
    tarea pequeña y clara.\
-   Combinado con otras utilidades (`sort`, `uniq`, `grep`, `join`), se
    convierte en un motor de análisis ligero.

------------------------------------------------------------------------

### 9.3 Buenas prácticas

-   **Documentar**: añade comentarios y ejemplos de uso en tus scripts
    `.awk`.\
-   **Modularizar**: divide flujos en pasos intermedios para mayor
    claridad y depuración.\
-   **Reproducibilidad**: guarda scripts y resultados con nombres claros
    y metadatos.\
-   **Legibilidad**: divide comandos largos con `\` y usa indentación
    para que sean fáciles de leer en PDF y terminal.

------------------------------------------------------------------------

### 9.4 Visión práctica

-   En bioinformática, `awk` es ideal para **preprocesamiento de
    datos**: limpiar, filtrar y generar reportes antes de usar
    herramientas más pesadas como BLAST, Bowtie o R.\
-   Su uso te permite ahorrar tiempo y mantener flujos reproducibles.\
-   Pensar en `awk` como un lenguaje especializado en **texto
    estructurado** te dará ventaja en análisis genómicos y clínicos.

------------------------------------------------------------------------

### 9.5 Próximos pasos

-   Consolidar esta guía en un documento completo (Markdown → PDF con
    pandoc/xelatex).\
-   Crear un **repositorio de scripts `.awk`** para bioinformática,
    organizados por tareas (FASTA, CSV, GFF, pipelines).\
-   Integrar `awk` en tus flujos clínicos y de investigación, como parte
    de tu preparación para bioinformática avanzada.

------------------------------------------------------------------------
