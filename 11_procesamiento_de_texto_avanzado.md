# Procesamiento de texto *Avanzado*

**Christian Robles**

## Parte 1 — Visualización de contenido  

### Introducción

Los comandos de visualización (`cat`, `less`, `head`, `tail`) parecen simples,
pero tienen opciones y combinaciones que los convierten en herramientas
poderosas para inspección avanzada de archivos. Aquí veremos cómo sacarles el
máximo provecho.

---

### 1. `cat` avanzado

**Opciones útiles:**

- `cat -n archivo` → numera todas las líneas.  
- `cat -b archivo` → numera solo las líneas no vacías.  
- `cat -s archivo` → suprime líneas en blanco consecutivas.  

**Ejemplo 1: Numerar todas las líneas**

```bash
cat -n archivo.txt
```

**Resultado esperado:**

```
     1  Primera línea
     2  Segunda línea
     3  Tercera línea
```

**Ejemplo 2: Suprimir líneas en blanco repetidas**

```bash
cat -s archivo.txt
```

**Resultado esperado:**

Las líneas en blanco múltiples se reducen a una sola.

---

### 2. `less` avanzado

**Opciones y trucos:**

- Dentro de `less`, usar `&patrón` para filtrar solo las líneas que coinciden.  
- `less +N archivo` → abre el archivo directamente en la línea N.  
- `less -N archivo` → muestra números de línea.  

**Ejemplo 1: Abrir en una línea específica**

```bash
less +50 log.txt
```

**Resultado esperado:**

El archivo se abre directamente en la línea 50.

**Ejemplo 2: Filtrar dentro de less**

Dentro de `less`, escribir:

```
&ERROR
```

**Resultado esperado:**

Se muestran solo las líneas que contienen “ERROR”.

---

### 3. `head` avanzado

**Opciones útiles:**

- `head -c N archivo` → muestra los primeros N bytes.  
- `head -q archivo1 archivo2` → muestra varias cabeceras sin separadores.  

**Ejemplo 1: Mostrar primeros 100 bytes**

```bash
head -c 100 archivo.txt
```

**Resultado esperado:**

Se muestran los primeros 100 caracteres del archivo.

**Ejemplo 2: Mostrar cabeceras de varios archivos**

```bash
head -q archivo1.txt archivo2.txt
```

**Resultado esperado:**

Se muestran las primeras 10 líneas de cada archivo, sin encabezados de
separación.

---

### 4. `tail` avanzado

**Opciones útiles:**

- `tail -n +N archivo` → muestra desde la línea N hasta el final.  
- `tail -c N archivo` → muestra los últimos N bytes.  
- `tail -f archivo | grep "ERROR"` → monitorea en tiempo real filtrando solo
  errores.  

**Ejemplo 1: Mostrar desde la línea 20**

```bash
tail -n +20 archivo.txt
```

**Resultado esperado:**

Se muestran todas las líneas desde la 20 en adelante.

**Ejemplo 2: Monitorear log filtrando errores**

```bash
tail -f log.txt | grep "ERROR"
```

**Resultado esperado:**

Se muestran en tiempo real solo las líneas nuevas que contienen “ERROR”.

---

### Ejemplo integrado

```bash
cat -n archivo.txt | head -n 5
tail -f log.txt | grep -i "warning"
```

**Resultado esperado:**

- Se numeran las primeras 5 líneas del archivo.  
- Se monitorean en tiempo real las advertencias en el log, ignorando
  mayúsculas/minúsculas.

---

### Modularidad en la línea de comandos

- **`cat`** no es solo para mostrar: con opciones puede limpiar y numerar
  texto.  
- **`less`** es un visor interactivo que permite búsquedas y filtrado en tiempo
  real.  
- **`head` y `tail`** no solo muestran extremos: también permiten trabajar con
  bytes y rangos.  
- Combinados con pipes (`|`), estos comandos se convierten en herramientas de
  diagnóstico y monitoreo en tiempo real.  

---

## Conclusión de la Parte 1

Para alcanzar un buen nivel en visualización:

- Domina las opciones de `cat`, `less`, `head` y `tail`.  
- Aprende a combinar `tail -f` con filtros (`grep`) para monitoreo en vivo.  
- Usa `less` como visor interactivo con búsqueda y filtrado.  
- Aprovecha `head` y `tail` para trabajar con rangos y bytes, no solo líneas.  

---

## Parte 2 — Transformación y filtrado básico  

### Introducción

Los comandos de transformación y filtrado (`cut`, `tr`, `wc`, `sort`, `uniq`,
`grep`) son la columna vertebral del procesamiento de texto en Linux.
Aunque parecen sencillos, tienen opciones y combinaciones que permiten
construir flujos de análisis muy potentes. Aquí veremos cómo sacarles el máximo
provecho.

---

### 1. `cut` avanzado

**Opciones útiles:**

- `cut -d',' -f1,3 archivo.csv` → extrae múltiples columnas.  
- `cut -c1-10 archivo.txt` → extrae un rango de caracteres.  

**Ejemplo 1: Extraer varias columnas de un CSV**

```bash
cut -d',' -f1,3 datos.csv
```

**Resultado esperado:**

```
ID001,75
ID002,60
ID003,90
```

**Ejemplo 2: Extraer un rango de caracteres**

```bash
echo "ABCDEFGHIJK" | cut -c3-7
```

**Resultado esperado:**

```
CDEFG
```

---

### 2. `tr` translate

**Opciones útiles:**

- `tr -s ' '` → comprime espacios múltiples en uno solo.  
- `tr '[:lower:]' '[:upper:]'` → convierte todo a mayúsculas usando clases de
  caracteres.  

**Ejemplo 1: Normalizar espacios**

```bash
echo "Hola    mundo   Linux" | tr -s ' '
```

**Resultado esperado:**

```
Hola mundo Linux
```

**Ejemplo 2: Convertir a mayúsculas con clases de caracteres**

```bash
echo "hola mundo" | tr '[:lower:]' '[:upper:]'
```

**Resultado esperado:**

```
HOLA MUNDO
```

---

### 3. `wc` avanzado

**Opciones útiles:**

- `wc -m archivo.txt` → cuenta caracteres (incluyendo saltos de línea).  
- `wc -L archivo.txt` → muestra la longitud de la línea más larga.  

**Ejemplo 1: Contar caracteres**

```bash
wc -m archivo.txt
```

**Resultado esperado:**

```
245 archivo.txt
```

**Ejemplo 2: Longitud de la línea más larga**

```bash
wc -L archivo.txt
```

**Resultado esperado:**

```
80 archivo.txt
```

---

### 4. `sort` avanzado

**Opciones útiles:**

- `sort -k2 archivo.txt` → ordena por la segunda columna.  
- `sort -t',' -k3n archivo.csv` → ordena numéricamente por la tercera columna de un CSV.  

**Ejemplo 1: Ordenar por columna específica**

```bash
sort -k2 nombres.txt
```

**Resultado esperado:**

```
Ana Quito
Luis Ambato
Pedro Guayaquil
```

**Ejemplo 2: Ordenar numéricamente por columna**

```bash
sort -t',' -k3n datos.csv
```

**Resultado esperado:**

```
ID002,Ambato,60
ID001,Quito,75
ID003,Guayaquil,90
```

---

### 5. `uniq` avanzado

**Opciones útiles:**

- `uniq -c archivo.txt` → cuenta ocurrencias.  
- `uniq -d archivo.txt` → muestra solo duplicados.  

**Ejemplo 1: Mostrar solo duplicados**

```bash
sort nombres.txt | uniq -d
```

**Resultado esperado:**

```
Carlos
Pedro
```

**Ejemplo 2: Contar ocurrencias con detalle**

```bash
sort nombres.txt | uniq -c
```

**Resultado esperado:**

```
1 Ana
2 Carlos
3 Pedro
```

---

### 6. `grep` avanzado

**Opciones útiles:**

- `grep -E "ERROR|WARNING" log.txt` → usa expresiones regulares extendidas.  
- `grep -r "patrón" directorio/` → busca recursivamente en un directorio.  
- `grep -n "ERROR" log.txt` → muestra número de línea junto a la coincidencia.  

**Ejemplo 1: Buscar múltiples patrones**

```bash
grep -E "ERROR|WARNING" log.txt
```
**Resultado esperado:**

```
[2026-01-03 19:00] ERROR: conexión fallida
[2026-01-03 19:05] WARNING: archivo no encontrado
```

**Ejemplo 2: Buscar recursivamente**

```bash
grep -r "TODO" proyecto/
```

**Resultado esperado:**  

Se listan todas las líneas que contienen “TODO” en los archivos del directorio
`proyecto/`.

---

### Ejemplo integrado

```bash
cut -d',' -f2 datos.csv | tr '[:lower:]' '[:upper:]' | sort | uniq -c | grep "QUITO"
```

**Resultado esperado:**

```
3 QUITO
```

Interpretación:

- Se extrae la segunda columna.  
- Se convierte a mayúsculas.  
- Se ordena y cuentan ocurrencias únicas.  
- Se filtra solo la ciudad “QUITO”.

---

### Modularidad en la línea de comandos

- **`cut`** y **`tr`** son herramientas de normalización y extracción de datos.  
- **`wc`** aporta métricas rápidas para validar contenido.  
- **`sort`** y **`uniq`** permiten análisis estadístico básico.  
- **`grep`** es el motor de búsqueda textual, y con expresiones regulares se
  convierte en un detector poderoso.  
- Combinados en pipelines, estos comandos permiten construir flujos de análisis
  comparables a herramientas de alto nivel, pero con la simplicidad
  y eficiencia de Unix.

---

## Conclusión de la Parte 2

Para alcanzar un buen nivel en transformación y filtrado:

- Domina las opciones de `cut` para múltiples columnas y rangos.  
- Usa `tr` con clases de caracteres para normalización robusta.  
- Aprovecha `wc` para métricas rápidas y validación.  
- Ordena con `sort` por columnas y tipos de datos.  
- Usa `uniq` para análisis de duplicados y frecuencias.  
- Explota `grep` con expresiones regulares y búsqueda recursiva.  

---

## Parte 3 — Edición y procesamiento avanzado  

### Introducción

Los comandos `sed` y `awk` son auténticas navajas suizas del procesamiento de
texto. Aunque ya vimos sus usos básicos, al aprenderlos en mayor profundidad se
convierten en herramientas capaces de transformar, analizar y generar reportes
complejos directamente desde la terminal. Vamos a explorar sus opciones
avanzadas y combinaciones más potentes.

---

### 1. `sed` avanzado

**Opciones y usos destacados:**

- `sed -i 's/patrón/reemplazo/g' archivo` → modifica el archivo directamente.  
- `sed -n '/ERROR/p' archivo` → imprime solo las líneas que coinciden con un
  patrón.  
- `sed 'N;s/\n/ /' archivo` → combina dos líneas consecutivas en una sola.  

**Ejemplo 1: Reemplazo directo en archivo**

```bash
sed -i 's/Linux/Unix/g' documento.txt
```

**Resultado esperado:**

Todas las ocurrencias de “Linux” en `documento.txt` se reemplazan por “Unix”
sin necesidad de crear un archivo nuevo.

**Ejemplo 2: Filtrar solo coincidencias**

```bash
sed -n '/ERROR/p' log.txt
```

**Resultado esperado:**

```
[2026-01-03 19:00] ERROR: conexión fallida
[2026-01-03 19:05] ERROR: archivo no encontrado
```

**Ejemplo 3: Combinar líneas consecutivas**

```bash
sed 'N;s/\n/ /' archivo.txt
```

**Resultado esperado:**

Cada par de líneas se combina en una sola, útil para unir registros partidos.

---

### 2. `awk` avanzado

**Opciones y usos destacados:**

- `awk -F',' '{print $1,$3}' archivo.csv` → define delimitador personalizado.
- `awk '{if($3>50) print $1,$3; else print $1,"BAJO"}' archivo.txt`
  → condiciones en flujo.
- `awk '{suma+=$3} END {print "Promedio:", suma/NR}' archivo.txt` → cálculos
  agregados.

**Ejemplo 1: Usar delimitador personalizado**

```bash
awk -F',' '{print $1,$3}' datos.csv
```

**Resultado esperado:**

```
ID001 75
ID002 60
ID003 90
```

**Ejemplo 2: Condiciones en flujo**

```bash
awk '{if($3>70) print $1,$3,"ALTO"; else print $1,$3,"BAJO"}' datos.txt
```

**Resultado esperado:**

```
ID001 75 ALTO
ID002 60 BAJO
ID003 90 ALTO
```

**Ejemplo 3: Calcular promedio**

```bash
awk '{suma+=$3} END {print "Promedio:", suma/NR}' datos.txt
```

**Resultado esperado:**

```
Promedio: 75
```

---

### Ejemplo integrado

```bash
sed 's/,/ /g' datos.csv | awk '{if($3>70) print $1,$2,$3,"ALTO"; else print $1,$2,$3,"BAJO"}'
```

**Resultado esperado:**

```
ID001 Quito 75 ALTO
ID002 Ambato 60 BAJO
ID003 Guayaquil 90 ALTO
```

Interpretación:

- `sed` reemplaza comas por espacios.  
- `awk` procesa los campos y clasifica los valores según condición.

---

### Modularidad en la línea de comandos

- **`sed`** no es solo para reemplazos: permite edición en flujo, filtrado
  y manipulación estructural.  
- **`awk`** es un lenguaje completo para análisis de texto, capaz de realizar
  cálculos y generar reportes.  
- Combinados, permiten transformar datos crudos en información procesada lista
  para análisis.  
- La clave está en **pensar en términos de flujo**: cada línea es un registro,
  cada campo es un dato, y cada comando es un filtro o transformación.

---

## Conclusión de la Parte 3

Para alcanzar un buen nivel en edición y procesamiento:

- Usa `sed -i` para ediciones directas y rápidas.  
- Aprovecha `sed -n` para filtrar sin mostrar todo el archivo.  
- Domina `awk` con delimitadores personalizados, condiciones y cálculos
  agregados.  
- Combina `sed` y `awk` en pipelines para transformar y analizar datos en un
  solo paso.  

---

## Parte 4 — Comparación y análisis de archivos  

### Introducción

Los comandos de comparación (`diff`, `cmp`, `comm`) son esenciales para
detectar diferencias entre archivos, verificar integridad y analizar listas.
Aunque sus usos básicos ya resultan útiles, al combinarlos con opciones
avanzadas y pipes se convierten en auténticas herramientas de auditoría,
diagnóstico y control de versiones. 

---

### 1. `diff` avanzado

**Opciones útiles:**

- `diff -u archivo1 archivo2` → formato unificado, usado en parches y Git.  
- `diff -c archivo1 archivo2` → formato contextual, muestra líneas alrededor de
  la diferencia.  
- `diff --side-by-side archivo1 archivo2` → muestra diferencias en columnas
  paralelas.  

**Ejemplo 1: Formato contextual**

```bash
diff -c archivo1.txt archivo2.txt
```

**Resultado esperado:**

```
*** archivo1.txt  Sat Jan  3 20:10:00 2026
--- archivo2.txt  Sat Jan  3 20:10:00 2026
***************
*** 1 ****
- Hola mundo
--- 1 ----
+ Hola Linux
```

**Ejemplo 2: Comparación lado a lado**

```bash
diff --side-by-side archivo1.txt archivo2.txt
```

**Resultado esperado:**

```
Hola mundo        | Hola Linux
Segunda línea     Segunda línea
```

---

### 2. `cmp` avanzado

**Opciones útiles:**

- `cmp -l archivo1 archivo2` → muestra todas las diferencias byte a byte.  
- `cmp -s archivo1 archivo2` → modo silencioso, solo devuelve estado de salida
  (0 si son iguales, 1 si difieren).

**Ejemplo 1: Mostrar todas las diferencias**

```bash
cmp -l archivo1.bin archivo2.bin
```

**Resultado esperado:**

```
10  141  142
25  101  105
```

Interpretación: en el byte 10, archivo1 tiene valor 141 y archivo2 tiene 142;
en el byte 25, 101 vs 105.

**Ejemplo 2: Comparación silenciosa**

```bash
cmp -s archivo1.bin archivo2.bin && echo "Son iguales" || echo "Son diferentes"
```

**Resultado esperado:**

```
Son diferentes
```

---

### 3. `comm` avanzado

**Opciones útiles:**

- `comm -1 archivo1 archivo2` → oculta la primera columna (líneas únicas del
  primer archivo).  
- `comm -2 archivo1 archivo2` → oculta la segunda columna.  
- `comm -3 archivo1 archivo2` → oculta la tercera columna (líneas comunes).  

**Ejemplo 1: Mostrar solo diferencias**

```bash
comm -3 lista1.txt lista2.txt
```

**Resultado esperado:**

```
Ana
Luis
```

Interpretación: muestra solo las líneas que no son comunes.

**Ejemplo 2: Mostrar solo coincidencias**

```bash
comm -12 lista1.txt lista2.txt
```

**Resultado esperado:**

```
Pedro
```

---

### Ejemplo integrado

```bash
diff -u archivo1.txt archivo2.txt > cambios.patch
patch archivo1.txt < cambios.patch
```

**Resultado esperado:**

- Se genera un archivo `cambios.patch` con las diferencias.  
- Se aplica el parche a `archivo1.txt` para transformarlo en `archivo2.txt`.  

---

### Modularidad en la línea de comandos

- **`diff`** es más que comparación: es la base de sistemas de control de
  versiones como Git.  
- **`cmp`** permite verificar integridad binaria, útil en copias y backups.  
- **`comm`** es ideal para análisis de listas y conjuntos, aplicando lógica de
  intersección y diferencia.  
- La clave está en usar estas herramientas no solo para ver diferencias, sino
  para **automatizar verificaciones y sincronizaciones** en scripts.

---

## Conclusión de la Parte 4

Para alcanzar un buen nivel en comparación y análisis:  

- Domina los formatos de `diff` (unificado, contextual, lado a lado).  
- Usa `cmp` para verificar integridad binaria y en scripts de validación.  
- Aprovecha `comm` para análisis de listas ordenadas y operaciones de conjuntos.  
- Integra `diff` con `patch` para aplicar cambios de forma controlada.  

---

## Parte 5 — Extracción y manipulación especial  

### Introducción

Los comandos `strings`, `split` y `join` son menos usados que los clásicos de
visualización o filtrado, pero cuando se dominan flujos a avanzados se
convierten en herramientas clave para análisis forense, manipulación de
archivos grandes y combinación de datos estructurados. 

---

### 1. `strings` avanzado

**Opciones útiles:**

- `strings -n N archivo` → muestra solo cadenas de al menos N caracteres.  
- `strings -t d archivo` → muestra la posición decimal de cada cadena
  encontrada.  
- `strings -f archivo` → muestra el nombre del archivo junto a cada cadena
  (útil en múltiples archivos).

**Ejemplo 1: Filtrar cadenas largas**

```bash
strings -n 8 programa.bin | head
```

**Resultado esperado:**

```
Error: conexión fallida
Usuario no autorizado
Archivo no encontrado
```

Interpretación: se muestran solo cadenas de 8 o más caracteres, eliminando
ruido.

**Ejemplo 2: Mostrar posiciones**

```bash
strings -t d programa.bin | grep "Error"
```

**Resultado esperado:**

```
1024 Error: conexión fallida
2048 Error: archivo no encontrado
```

Interpretación: se muestra la posición en el binario donde aparece cada cadena.

---

### 2. `split` avanzado

**Opciones útiles:**

- `split -l N archivo prefijo` → divide por número de líneas.  
- `split -b N archivo prefijo` → divide por tamaño en bytes.  
- `split -d archivo prefijo` → usa sufijos numéricos en lugar de letras.  

**Ejemplo 1: Dividir por líneas con sufijos numéricos**

```bash
split -l 100 -d log.txt parte_
ls
```

**Resultado esperado:**

```
parte_00  parte_01  parte_02 ...
```

Cada archivo contiene 100 líneas del log original.

**Ejemplo 2: Dividir por tamaño en MB**

```bash
split -b 1M datos.csv bloque_
```

**Resultado esperado:**

```
bloque_aa  bloque_ab  bloque_ac ...
```

Cada archivo contiene 1 MB de datos.

---

### 3. `join` avanzado

**Opciones útiles:**

- `join -t',' archivo1 archivo2` → especifica delimitador.  
- `join -1 N -2 M archivo1 archivo2` → une usando columnas diferentes en cada
  archivo.  
- `join -a1 archivo1 archivo2` → incluye todas las líneas del primer archivo,
  incluso si no tienen coincidencia.  

**Ejemplo 1: Unir por columnas diferentes**

Archivo `usuarios.txt`:

```
1 Ana
2 Carlos
3 Luis
```

Archivo `emails.txt`:

```
A Ana@example.com
C Carlos@example.com
L Luis@example.com
```

```bash
join -1 2 -2 1 usuarios.txt emails.txt
```

**Resultado esperado:**

```
Ana Ana@example.com
Carlos Carlos@example.com
Luis Luis@example.com
```

**Ejemplo 2: Unión con inclusión de todas las filas**

```bash
join -a1 usuarios.txt ciudades.txt
```

**Resultado esperado:**

Muestra todos los usuarios, incluso si no tienen ciudad asociada.

---

### Ejemplo integrado

```bash
strings programa.bin | grep "ERROR" > errores.txt
split -l 50 errores.txt errores_
join -t',' usuarios.csv ciudades.csv > usuarios_ciudades.csv
```

**Resultado esperado:**

- Se extraen cadenas con “ERROR” de un binario.  
- Se dividen en archivos de 50 líneas cada uno.  
- Se unen dos CSV para relacionar usuarios con ciudades.

---

### Modularidad en la línea de comandos

- **`strings`** es una herramienta forense: permite inspeccionar binarios
  y encontrar trazas de texto oculto.  
- **`split`** es clave para manejar archivos enormes, dividiéndolos en partes
  procesables.  
- **`join`** convierte listas separadas en tablas completas, ideal para
  análisis de datos.  
- La combinación de estas herramientas permite trabajar con datos crudos,
  binarios y listas de manera flexible y eficiente.  

---

## Conclusión de la Parte 5

Para alcanzar un buen nivel en extracción y manipulación especial:  

- Usa `strings` con filtros de longitud y posiciones para análisis forense.  
- Domina `split` para dividir archivos grandes por líneas o tamaño.  
- Aprovecha `join` con delimitadores y columnas personalizadas para unir datos
  estructurados.  
- Integra estas herramientas en pipelines para análisis avanzado de binarios
  y grandes volúmenes de datos.  

---

## Sección avanzada global combinada

### Introducción

Después de explorar las utilidades de visualización, filtrado, edición,
comparación y manipulación llega el momento de integrarlas en pipelines
complejos, aprovechar opciones poco conocidas y aplicar la filosofía Unix para
obtener el máximo rendimiento.

---

### 1. Visualización combinada

- **`head` y `tail` juntos** → inspeccionar rangos específicos.  

```bash
head -n 20 archivo.txt | tail -n 5
```

**Resultado esperado:**

Muestra las líneas 16 a 20 del archivo.

- **`less` con filtros** → navegar y mostrar solo coincidencias.  
Dentro de `less`:

```
&ERROR
```

Filtra únicamente las líneas con “ERROR”.

---

### 2. Transformación y filtrado encadenado

- **Extracción múltiple con `cut` y normalización con `tr`**  

```bash
cut -d',' -f2,3 datos.csv | tr '[:lower:]' '[:upper:]'
```

Convierte a mayúsculas las columnas 2 y 3 de un CSV.

- **Ordenar y contar ocurrencias únicas**  

```bash
cut -d',' -f2 datos.csv | sort | uniq -c | sort -nr
```

Cuenta ocurrencias de la segunda columna y las ordena de mayor a menor.

---

### 3. Edición y procesamiento avanzado

- **Reemplazos masivos con `sed`**  

```bash
sed -i 's/[0-9]\{4\}-[0-9]\{2\}-[0-9]\{2\}/FECHA/g' log.txt
```

Reemplaza todas las fechas en formato YYYY-MM-DD por la palabra “FECHA”.

- **Cálculos agregados con `awk`**  

```bash
awk -F',' '{suma+=$3} END {print "Total:", suma, "Promedio:", suma/NR}' datos.csv
```

Calcula el total y el promedio de la tercera columna de un CSV.

---

### 4. Comparación y verificación

- **Generar y aplicar parches con `diff` y `patch`**  
```bash
diff -u archivo1.txt archivo2.txt > cambios.patch
patch archivo1.txt < cambios.patch
```

Transforma `archivo1.txt` en `archivo2.txt` aplicando el parche.

- **Verificación binaria silenciosa con `cmp`**  

```bash
cmp -s copia1.bin copia2.bin && echo "Archivos idénticos" || echo "Archivos diferentes"
```

- **Análisis de listas con `comm`**  

```bash
comm -3 lista1.txt lista2.txt
```

Muestra solo las diferencias entre dos listas ordenadas.

---

### 5. Extracción y manipulación especial

- **Análisis forense con `strings`**  

```bash
strings -n 10 programa.bin | grep "ERROR"
```

Extrae cadenas largas de un binario y filtra las que contienen “ERROR”.

- **División de logs grandes con `split`**  

```bash
split -l 1000 log.txt log_parte_
```

Divide un log en archivos de 1000 líneas cada uno.

- **Unión de datos con `join`**  

```bash
join -t',' usuarios.csv ciudades.csv > usuarios_ciudades.csv
```

Combina usuarios y ciudades por ID.

---

### 6. Ejemplo integrado global

```bash
grep -r "ERROR" logs/ | cut -d':' -f2 | tr '[:lower:]' '[:upper:]' | sort | uniq -c | sort -nr | awk '{print $2,":", $1}' > reporte.txt
```

**Resultado esperado:**

```
CONEXIÓN: 15
ARCHIVO: 10
USUARIO: 7
```

Interpretación:

- `grep -r` busca “ERROR” en todos los archivos del directorio `logs/`.  
- `cut` extrae la parte después de los dos puntos.  
- `tr` convierte a mayúsculas.  
- `sort` ordena.  
- `uniq -c` cuenta ocurrencias.  
- `sort -nr` ordena por frecuencia descendente.  
- `awk` formatea el reporte final.  
- El resultado se guarda en `reporte.txt`.

---

### Recomendaciones

- **Modularidad:** cada comando hace una tarea simple, pero juntos forman
  pipelines poderosos.  
- **Eficiencia:** trabajar en flujo evita archivos intermedios innecesarios.  
- **Flexibilidad:** desde inspección rápida hasta análisis complejo, todo se
  logra con combinaciones de herramientas básicas.  
- **Auditoría:** usar `diff`, `cmp`, `comm` asegura integridad y control de
  cambios.  
- **Datos grandes:** `strings`, `split`, `join` permiten trabajar con
  binarios y archivos masivos.  

