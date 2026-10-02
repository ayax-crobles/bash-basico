# Procesamiento de texto  

**Christian Robles**

## Parte 1 — Visualización de contenido

### Introducción

Antes de transformar o analizar texto, es necesario **visualizarlo**.
Unix/Linux ofrece comandos simples pero poderosos para mostrar archivos en
pantalla. Estos comandos permiten leer archivos completos, inspeccionar solo
una parte o navegar de manera interactiva.  

---

### 1. `cat` — Concatenate and display

**Teoría:**

- Muestra el contenido completo de uno o varios archivos en la salida estándar.  
- También puede concatenar varios archivos y mostrarlos juntos.  
- Es útil para archivos pequeños, pero poco práctico para archivos muy grandes.  

**Ejemplo 1: Mostrar un archivo completo**

```bash
cat archivo.txt
```

**Resultado esperado:**

```
Primera línea del archivo
Segunda línea del archivo
Tercera línea del archivo
```

**Ejemplo 2: Concatenar varios archivos**

```bash
cat archivo1.txt archivo2.txt
```

**Resultado esperado:**

```
Contenido de archivo1
Contenido de archivo2
```

---

### 2. `less` — Visualización paginada

**Teoría:**

- Permite navegar por archivos grandes de forma interactiva.  
- Se puede avanzar con la tecla **Espacio**, retroceder con **b**, buscar con **/**.  
- Es más eficiente que `cat` para archivos extensos.  

**Ejemplo 1: Abrir un archivo grande**

```bash
less log.txt
```

**Resultado esperado:**

El archivo se abre en modo interactivo, mostrando una pantalla a la vez.  

**Ejemplo 2: Buscar dentro del archivo**

Dentro de `less`, escribir:

```
/ERROR
```

**Resultado esperado:**

Se resaltan las líneas que contienen la palabra “ERROR”.

---

### 3. `head` — Primeras líneas

**Teoría:**

- Muestra las primeras líneas de un archivo.  
- Por defecto muestra 10 líneas, pero se puede ajustar con la opción `-n`.  

**Ejemplo 1: Primeras 10 líneas**

```bash
head archivo.txt
```

**Resultado esperado:**

```
Línea 1
Línea 2
...
Línea 10
```

**Ejemplo 2: Primeras 5 líneas**

```bash
head -n 5 archivo.txt
```

**Resultado esperado:**

```
Línea 1
Línea 2
Línea 3
Línea 4
Línea 5
```

---

### 4. `tail` — Últimas líneas

**Teoría:**

- Muestra las últimas líneas de un archivo.  
- Por defecto muestra 10 líneas, pero se puede ajustar con `-n`.  
- Con la opción `-f`, sigue mostrando nuevas líneas en tiempo real (muy usado para logs).  

**Ejemplo 1: Últimas 10 líneas**

```bash
tail archivo.txt
```

**Resultado esperado:**

```
Línea final-9
Línea final-8
...
Línea final
```

**Ejemplo 2: Monitorear un log en tiempo real**

```bash
tail -f log.txt
```

**Resultado esperado:**

Se muestran las últimas líneas del archivo y se actualizan automáticamente cuando se agregan nuevas entradas.

---

## Reto de práctica

1. Crea un archivo `ejemplo.txt` con 20 líneas numeradas.  
2. Usa `cat` para mostrarlo completo.  
3. Usa `less` para navegarlo.  
4. Usa `head -n 3` para ver las primeras 3 líneas.  
5. Usa `tail -n 5` para ver las últimas 5 líneas.  

**Resultado esperado:**

```
Línea 1
Línea 2
Línea 3
...
Línea 16
Línea 17
Línea 18
Línea 19
Línea 20
```

---

## Conclusión de la Parte 1

Los comandos de visualización (`cat`, `less`, `head`, `tail`) son la base para
inspeccionar archivos antes de procesarlos.
- **`cat`** → útil para archivos pequeños o concatenar.  
- **`less`** → ideal para archivos grandes y navegación interactiva.  
- **`head`** → muestra el inicio.  
- **`tail`** → muestra el final y permite monitoreo en tiempo real.  

---

## Parte 2 — Transformación y filtrado básico

### Introducción

Una vez que sabemos visualizar archivos, el siguiente paso es **transformar
y filtrar su contenido**. Unix/Linux ofrece comandos que permiten extraer
columnas, modificar caracteres, contar elementos, ordenar, eliminar duplicados
y buscar patrones. Estos comandos son la base del procesamiento de datos en
texto plano.

---

### 1. `cut` — Extraer columnas o campos

**Teoría:**

- Permite seleccionar partes específicas de cada línea de un archivo.  
- Se puede usar con delimitadores (`-d`) para dividir por campos, o con posiciones de caracteres (`-c`).  

**Ejemplo 1: Extraer la primera columna de un CSV**

```bash
cut -d',' -f1 datos.csv
```

**Resultado esperado:**

```
ID001
ID002
ID003
```

**Ejemplo 2: Extraer caracteres específicos**

```bash
echo "abcdef" | cut -c2-4
``` 

**Resultado esperado:**

```
bcd
```

---

### 2. `tr` — Transformar caracteres
**Teoría:**

- Sustituye o elimina caracteres.  
- Muy usado para cambiar mayúsculas/minúsculas o eliminar espacios.  

**Ejemplo 1: Convertir a mayúsculas**

```bash
echo "hola mundo" | tr 'a-z' 'A-Z'
```

**Resultado esperado:**

```
HOLA MUNDO
```

**Ejemplo 2: Eliminar dígitos**

```bash
echo "abc123xyz" | tr -d '0-9'
```

**Resultado esperado:**

```
abcxyz
```

---

### 3. `wc` — Word Count

**Teoría:**

- Cuenta líneas, palabras y caracteres de un archivo.  
- Opciones: `-l` (líneas), `-w` (palabras), `-c` (bytes/caracteres).  

**Ejemplo 1: Contar líneas**

```bash
wc -l archivo.txt
```

**Resultado esperado:**

```
20 archivo.txt
```

**Ejemplo 2: Contar palabras**

```bash
wc -w archivo.txt
```

**Resultado esperado:**

```
120 archivo.txt
```

---

### 4. `sort` — Ordenar contenido

**Teoría:**

- Ordena líneas de un archivo.  
- Opciones: `-r` (orden inverso), `-n` (orden numérico), `-u` (elimina duplicados).  

**Ejemplo 1: Ordenar alfabéticamente**

```bash
sort nombres.txt
```

**Resultado esperado:**

```
Ana
Carlos
Luis
Pedro
```

**Ejemplo 2: Ordenar numéricamente**

```bash
sort -n numeros.txt
```

**Resultado esperado:**

```
3
15
42
100
```

---

### 5. `uniq` — Eliminar duplicados

**Teoría:**

- Elimina líneas duplicadas consecutivas.  
- Opciones: `-c` cuenta ocurrencias, `-d` muestra solo duplicados.  

**Ejemplo 1: Eliminar duplicados**

```bash
sort nombres.txt | uniq
```

**Resultado esperado:**

```
Ana
Carlos
Luis
Pedro
```

**Ejemplo 2: Contar ocurrencias**

```bash
sort nombres.txt | uniq -c
```

**Resultado esperado:**

```
1 Ana
2 Carlos
1 Luis
3 Pedro
```

---

### 6. `grep` — Buscar patrones

**Teoría:** 

- Busca líneas que coincidan con un patrón.  
- Opciones: `-i` (ignorar mayúsculas), `-r` (recursivo en directorios), `-v`
  (invertir coincidencia).

**Ejemplo 1: Buscar palabra en archivo**

```bash
grep "ERROR" log.txt
```

**Resultado esperado:**

```
[2026-01-03 19:00] ERROR: conexión fallida
```

**Ejemplo 2: Buscar ignorando mayúsculas**

```bash
grep -i "warning" log.txt
```

**Resultado esperado:**

```
[2026-01-03 19:05] Warning: archivo no encontrado
```

---

## Ejemplo integrado

```bash
cat datos.csv | cut -d',' -f2 | sort | uniq -c
```

**Resultado esperado:**

```
3 Quito
5 Ambato
2 Guayaquil
```

Interpretación:

- Se extrae la segunda columna de un CSV.  
- Se ordena alfabéticamente.  
- Se cuentan las ocurrencias únicas.

---

## Conclusión de la Parte 2

Los comandos de transformación y filtrado (`cut`, `tr`, `wc`, `sort`, `uniq`,
`grep`) permiten manipular texto de manera precisa:

- **`cut`** → extrae columnas o caracteres.  
- **`tr`** → transforma o elimina caracteres.  
- **`wc`** → cuenta elementos.  
- **`sort`** → ordena contenido.  
- **`uniq`** → elimina duplicados.  
- **`grep`** → busca patrones.  

Son la base para construir flujos de procesamiento más complejos.

----

## Parte 3 — Edición y procesamiento avanzado

### Introducción

Cuando necesitamos **modificar texto en flujo** o realizar **procesamiento
estructurado**, entran en juego dos herramientas fundamentales:

- **`sed`** → editor de texto en flujo, ideal para reemplazos, borrados
  y transformaciones simples.  
- **`awk`** → lenguaje de procesamiento de texto orientado a campos, útil para
  cálculos y análisis más complejos.  

Estas herramientas permiten ir más allá de la visualización y el filtrado
básico, convirtiéndose en pilares del procesamiento avanzado de datos en
Unix/Linux.

---

### 1. `sed` — Stream Editor

**Teoría:**

- Procesa texto línea por línea.  
- Se usa para reemplazar, eliminar o insertar contenido.  
- Sintaxis básica: `sed 's/patrón/reemplazo/' archivo`.  
- Por defecto muestra el resultado en pantalla; con `-i` modifica directamente el archivo.  

**Ejemplo 1: Reemplazar texto simple**

```bash
echo "Hola mundo" | sed 's/mundo/Linux/'
```

**Resultado esperado:**

```
Hola Linux
```

**Ejemplo 2: Reemplazo global en archivo**

```bash
sed 's/error/ERROR/g' log.txt
```

**Resultado esperado:**

```
[2026-01-03 19:00] ERROR: conexión fallida
[2026-01-03 19:05] ERROR: archivo no encontrado
```

El modificador `g` aplica el reemplazo en todas las ocurrencias de cada línea.

**Ejemplo 3: Eliminar líneas que contienen un patrón**

```bash
sed '/DEBUG/d' log.txt
```
**Resultado esperado:**

Se muestran todas las líneas excepto aquellas que contienen “DEBUG”.

---

### 2. `awk` — Pattern scanning and processing

**Teoría:**

- Procesa texto dividiéndolo en **campos** (por defecto separados por espacios).  
- Permite aplicar condiciones y realizar cálculos.  
- Sintaxis básica: `awk '{acción}' archivo`.  
- Los campos se referencian como `$1`, `$2`, etc.  

**Ejemplo 1: Mostrar segunda columna**

```bash
awk '{print $2}' datos.txt
```

**Resultado esperado:**

```
Quito
Ambato
Guayaquil
```

**Ejemplo 2: Filtrar y mostrar campos**

```bash
awk '$3 > 50 {print $1, $3}' datos.txt
```

**Resultado esperado:**

```
ID001 75
ID004 90
```

Muestra solo las filas donde el tercer campo es mayor que 50.

**Ejemplo 3: Calcular suma de una columna**

```bash
awk '{suma += $3} END {print "Total:", suma}' datos.txt
```

**Resultado esperado:**

```
Total: 215
```

Acumula los valores de la tercera columna y muestra el total al final.

---

## Ejemplo integrado

```bash
cat datos.csv | sed 's/,/ /g' | awk '{print $2}' | sort | uniq -c
```

**Resultado esperado:**

```
3 Quito
5 Ambato
2 Guayaquil
```

Interpretación:

- `sed` reemplaza comas por espacios.  
- `awk` extrae la segunda columna.  
- `sort` ordena los valores.  
- `uniq -c` cuenta ocurrencias únicas.

---

## Conclusión de la Parte 3

- **`sed`** → ideal para reemplazos y edición rápida en flujo.  
- **`awk`** → poderoso para procesamiento estructurado, cálculos y análisis de
  campos.  Ambos comandos son esenciales para transformar datos de manera
  avanzada y preparar información para análisis más complejo.

---

## Parte 4 — Comparación y análisis de archivos

### Introducción

Además de visualizar y transformar texto, en Unix/Linux es común **comparar
archivos** para detectar diferencias, verificar integridad o analizar listas.
Los comandos `diff`, `cmp` y `comm` cumplen este propósito, cada uno con un
enfoque distinto:  
- **`diff`** → compara línea por línea, mostrando diferencias textuales.  
- **`cmp`** → compara byte a byte, útil para verificar si dos archivos son
  idénticos.  
- **`comm`** → compara listas ordenadas, mostrando coincidencias y diferencias.  

---

### 1. `diff` — Comparación línea por línea

**Teoría:**

- Muestra las diferencias entre dos archivos de texto.  
- Indica qué líneas deben cambiarse para transformar un archivo en otro.  
- Opciones: `-u` (formato unificado), `-c` (formato contextual).  

**Ejemplo 1: Comparar dos archivos simples**

```bash
diff archivo1.txt archivo2.txt
```

**Resultado esperado:**

```
1c1
< Hola mundo
---
> Hola Linux
```

Interpretación:

- En la línea 1, `archivo1.txt` tiene “Hola mundo” y `archivo2.txt` tiene “Hola Linux”.

**Ejemplo 2: Usar formato unificado**

```bash
diff -u archivo1.txt archivo2.txt
```

**Resultado esperado:**

```
--- archivo1.txt  2026-01-03 20:05
+++ archivo2.txt  2026-01-03 20:05
@@ -1 +1 @@
-Hola mundo
+Hola Linux
```

El formato unificado es más legible y se usa mucho en control de versiones (ejemplo: Git).

---

### 2. `cmp` — Comparación byte a byte

**Teoría:**

- Compara archivos a nivel binario.  
- Útil para verificar si dos archivos son idénticos, incluso si no son texto.  
- Muestra la primera diferencia encontrada.  

**Ejemplo 1: Archivos idénticos**

```bash
cmp archivo1.bin archivo2.bin
```

**Resultado esperado:**

(no muestra nada, porque son idénticos)

**Ejemplo 2: Archivos diferentes**

```bash
cmp archivo1.bin archivo3.bin
```

**Resultado esperado:**

```
archivo1.bin archivo3.bin differ: byte 10, line 1
```

Interpretación:

- Los archivos difieren en el byte 10, línea 1.

---

### 3. `comm` — Comparación de listas ordenadas

**Teoría:**

- Compara dos archivos ordenados línea por línea.  
- Muestra tres columnas:  
  1. Líneas únicas del primer archivo.  
  2. Líneas únicas del segundo archivo.  
  3. Líneas comunes a ambos.  
- Opciones: `-1`, `-2`, `-3` para ocultar columnas.  

**Ejemplo 1: Comparar listas ordenadas**

```bash
comm lista1.txt lista2.txt
```

**Resultado esperado:**

```
        Carlos
Ana
        Luis
Pedro
        Pedro
```

Interpretación:

- Las líneas sin tabulación pertenecen solo a `lista1.txt`.  
- Las líneas con tabulación en la segunda columna pertenecen solo a `lista2.txt`.  
- Las líneas con doble tabulación son comunes.

**Ejemplo 2: Mostrar solo coincidencias**

```bash
comm -12 lista1.txt lista2.txt
```

**Resultado esperado:**

```
Pedro
```

Muestra únicamente las líneas comunes a ambos archivos.

---

## Reto de práctica

1. Crea dos archivos de texto con listas de nombres.  
2. Usa `diff` para ver las diferencias.  
3. Usa `cmp` para verificar si son idénticos.  
4. Usa `comm` para encontrar los nombres comunes.  

**Resultado esperado:**

```
diff → muestra diferencias línea por línea.
cmp → indica si son idénticos o dónde difieren.
comm → muestra coincidencias y diferencias en columnas.
```

---

## Conclusión de la Parte 4

Los comandos de comparación (`diff`, `cmp`, `comm`) permiten analizar archivos
desde distintos niveles:

- **`diff`** → diferencias textuales línea por línea.  
- **`cmp`** → diferencias binarias byte a byte.  
- **`comm`** → comparación de listas ordenadas con coincidencias y diferencias.  

Son esenciales para verificar integridad, depurar cambios y analizar datos en
sistemas Unix/Linux.

---

## Parte 5 — Extracción y manipulación especial

### Introducción

Además de visualizar, transformar y comparar, Unix/Linux ofrece comandos para **extraer información especial** de archivos y para **dividir o unir datos**. Estos comandos son útiles en situaciones específicas: analizar binarios, manejar archivos grandes o combinar listas basadas en campos.

---

### 1. `strings` — Extraer texto legible de binarios

**Teoría:**

- Busca secuencias de caracteres imprimibles dentro de archivos binarios.  
- Muy usado para inspeccionar ejecutables y encontrar mensajes ocultos o cadenas de texto.  
- No interpreta el binario, solo extrae texto reconocible.  

**Ejemplo 1: Extraer texto de un ejecutable**

```bash
strings programa.bin | head
```

**Resultado esperado:**

```
/lib64/ld-linux-x86-64.so.2
printf
main
Error: archivo no encontrado
```

Interpretación: se muestran las primeras cadenas legibles encontradas en el binario.

**Ejemplo 2: Buscar una palabra específica en un binario**

```bash
strings programa.bin | grep "Error"
```

**Resultado esperado:**

```
Error: archivo no encontrado
Error: conexión fallida
```

---

### 2. `split` — Dividir archivos grandes

**Teoría:**

- Divide un archivo en partes más pequeñas.  
- Por defecto crea archivos de 1000 líneas cada uno.  
- Opciones: `-b` para dividir por tamaño en bytes, `-l` para dividir por número de líneas.  

**Ejemplo 1: Dividir por líneas**

```bash
split -l 5 datos.txt parte_
ls
```

**Resultado esperado:**

```
parte_aa  parte_ab  parte_ac ...
```

Cada archivo contiene 5 líneas del original.

**Ejemplo 2: Dividir por tamaño**

```bash
split -b 1K datos.txt bloque_
ls
```

**Resultado esperado:**

```
bloque_aa  bloque_ab  bloque_ac ...
```

Cada archivo contiene 1 KB de datos.

---

### 3. `join` — Unir archivos por campos comunes

**Teoría:**

- Une dos archivos basados en un campo compartido.  
- Los archivos deben estar ordenados por el campo de unión.  
- Sintaxis básica: `join archivo1 archivo2`.  

**Ejemplo 1: Unir por primera columna**

Archivo `nombres.txt`:

```
1 Ana
2 Carlos
3 Luis
```

Archivo `ciudades.txt`:

```
1 Quito
2 Ambato
3 Guayaquil
```

```bash
join nombres.txt ciudades.txt
```

**Resultado esperado:**

```
1 Ana Quito
2 Carlos Ambato
3 Luis Guayaquil
```

**Ejemplo 2: Unir por segunda columna**

```bash
join -1 2 -2 2 archivo1.txt archivo2.txt
```

**Resultado esperado:**

Une los archivos usando la segunda columna de cada uno como clave.

---

## Reto de práctica

1. Usa `strings` para extraer texto de un binario y filtra con `grep` las
   cadenas que contengan “ERROR”.  
2. Divide un archivo de 20 líneas en bloques de 4 líneas con `split`.  
3. Une dos archivos de datos (IDs y nombres, IDs y ciudades) con `join`.  

**Resultado esperado:**

```
strings → muestra cadenas legibles de un binario.
split → genera 5 archivos con 4 líneas cada uno.
join → combina nombres y ciudades por ID.
```

---

## Conclusión de la Parte 5

Los comandos de extracción y manipulación especial (`strings`, `split`, `join`)
amplían las posibilidades del procesamiento de texto:  
- **`strings`** → inspección de binarios para extraer texto legible.  
- **`split`** → división de archivos grandes en partes manejables.  
- **`join`** → unión de archivos basados en campos comunes.  

Son herramientas específicas pero muy útiles en administración de sistemas
y análisis de datos.

