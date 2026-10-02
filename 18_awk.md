
# Guía: Awk avanzado

**Autor: MD. Christian Robles**

**Fecha: 23/01/2026**

## 1. Introducción a `awk`

`awk` es mucho más que un simple comando: es un **lenguaje de programación minimalista** diseñado para procesar texto estructurado.  
- **Origen**: creado por Alfred Aho, Peter Weinberger y Brian Kernighan (de ahí el nombre AWK).  
- **Filosofía Unix**: cada herramienta hace una cosa y la hace bien. `awk` está pensado para **procesar datos en columnas** y aplicar acciones sobre ellos.  
- **Diferencia con `sed` y `grep`**:  
  - `grep` busca patrones.  
  - `sed` transforma flujos de texto.  
  - `awk` entiende **campos y registros**, lo que lo convierte en un procesador semiestructurado.  

### Sintaxis general
```bash
awk 'patrón { acción }' archivo
```
- **Patrón**: condición que debe cumplirse (ej. `$3 == "up"`).  
- **Acción**: lo que se ejecuta si el patrón coincide (ej. `print $0`).  
- **Archivo**: fuente de datos a procesar.  

Ejemplo básico:
```bash
awk '$3 == "up" {print $0}' datos.txt
```
Imprime todas las líneas cuyo tercer campo sea `"up"`.

---

## 2. Fundamentos esenciales

Para dominar `awk`, hay que entender sus **variables internas** y cómo interpreta los datos.

### 2.1 Variables internas clave
- `$0` → toda la línea completa.  
- `$1`, `$2`, … → cada campo separado por espacios o tabulaciones (por defecto).  
- `NF` → número de campos en la línea.  
- `NR` → número de línea actual.  
- `FS` → delimitador de campos (por defecto espacio/tab).  
- `OFS` → delimitador de salida (por defecto espacio).  

Ejemplo: imprimir el número de campos y el número de línea.
```bash
awk '{print "Linea:", NR, "Campos:", NF}' datos.txt
```

---

### 2.2 Condicionales inline
Aunque `awk` permite escribir bloques completos con `if`, muchas veces se usan condiciones inline para mayor simplicidad.

Ejemplo: imprimir solo las líneas donde el primer campo sea mayor que 15.
```bash
awk '$1 > 15 {print $0}' datos.txt
```

Equivalente con bloque `if`:
```bash
awk '{if ($1 > 15) print $0}' datos.txt
```

---

### 2.3 Ejemplo práctico con `$0`
Archivo `expresion.txt`:
```
11 TP53 up
17 IDH1 up
10 NF1 up
```

Comando:
```bash
awk '$3 == "up" {print $0}' expresion.txt
```

Salida:
```
11 TP53 up
17 IDH1 up
10 NF1 up
```

Aquí `$0` asegura que se imprime **toda la línea**, no solo un campo.

---

### Nota de usuario avanzado
- `$0` es fundamental: recuerda que siempre representa la línea completa.  
- Usa `NF` y `NR` para **auditar datos** y entender su estructura.  
- Condicionales inline son más legibles en pipelines, mientras que bloques `if` son mejores en scripts largos.  
- Filosofía Unix: piensa en `awk` como un filtro que **entiende columnas**, lo que lo hace más potente que `sed` para datos tabulares.

---

## 3. Opciones de ejecución

Aunque `awk` puede usarse con su sintaxis básica, existen **opciones de ejecución** que lo hacen mucho más flexible y poderoso. Estas opciones permiten adaptar `awk` a distintos tipos de datos y escenarios, desde archivos CSV hasta pipelines complejos.

---

### 3.1 `-F` (definir delimitador de campos)

Por defecto, `awk` separa los campos usando **espacios o tabulaciones**. Sin embargo, muchos archivos usan otros delimitadores, como comas (CSV), puntos y coma, o incluso barras verticales.  
La opción `-F` permite especificar el delimitador.

Ejemplo: archivo `expresion_sep.txt`
```
12,EGFR,down
4,PTEN,down
11,TP53,up
```

Comando:
```bash
awk -F ',' '$2 ~ /R$/ {print $2}' expresion_sep.txt
```

Salida:
```
EGFR
EGFR
```

**Explicación**:  
- `-F ','` indica que los campos están separados por comas.  
- `$2 ~ /R$/` busca en la segunda columna los valores que terminan en `R`.  
- `print $2` imprime solo esa columna.  

Esto convierte a `awk` en un **procesador de CSV ligero**, sin necesidad de programas externos.

---

### 3.2 `-v` (pasar variables desde la línea de comandos)

`awk` permite definir variables internas, pero a veces necesitamos pasar valores desde fuera (por ejemplo, un umbral o un nombre dinámico). Para eso se usa `-v`.

Ejemplo:
```bash
awk -v limite=15 '$1 > limite {print $0}' datos.txt
```

**Explicación**:  
- `-v limite=15` define una variable llamada `limite` con valor 15.  
- `$1 > limite` compara el primer campo con esa variable.  
- Esto hace que el script sea **más flexible y reutilizable**, porque el valor puede cambiar sin modificar el código.

---

### 3.3 Uso en pipelines

La verdadera potencia de `awk` se ve cuando se integra en **pipelines Unix**.  
Recuerda: en Unix todo es texto, y `awk` puede procesar cualquier flujo que le llegue por `stdin`.

Ejemplo: contar cuántas veces aparece la palabra “ERROR” en un log.
```bash
grep "ERROR" /var/log/syslog | awk '{count++} END {print "Total errores:",
 count}'
```

**Explicación**:  
- `grep "ERROR"` filtra las líneas que contienen la palabra.  
- `awk` cuenta esas líneas con `count++`.  
- En el bloque `END`, imprime el total.  
- Esto evita tener que usar herramientas más pesadas como Python para tareas simples.

---

### Nota de usuario avanzado
- `-F` es esencial para trabajar con **archivos delimitados** (CSV, TSV, etc.).  
- `-v` permite escribir **scripts genéricos y parametrizables**, ideales para automatización.  
- En pipelines, `awk` se convierte en un **filtro inteligente**, capaz de calcular estadísticas, transformar datos y generar reportes en tiempo real.  
- Filosofía Unix: no pienses en `awk` como un programa aislado, sino como una pieza que se integra con otras herramientas (`grep`, `sed`, `sort`, `uniq`) para construir soluciones elegantes.

---

## 4. Acciones básicas

Las **acciones** en `awk` son lo que se ejecuta cuando una línea cumple la condición definida en el patrón. Aunque `awk` puede comportarse como un lenguaje completo, sus acciones más comunes son muy directas: imprimir, formatear y filtrar.

---

### 4.1 `print`
El comando más usado en `awk` es `print`.  
- `print $0` → imprime toda la línea.  
- `print $1` → imprime el primer campo.  
- `print $1, $3` → imprime el primer y tercer campo, separados por el delimitador de salida (`OFS`, por defecto espacio).

Ejemplo:
```bash
awk '{print $1, $2}' datos.txt
```
Salida:
```
12 EGFR
4 PTEN
11 TP53
```

**Explicación**:  
Aquí se imprimen solo las dos primeras columnas de cada línea. Esto es útil para **reducir datos** y enfocarse en lo relevante.

---

### 4.2 `printf`
`printf` funciona igual que en C: permite dar formato a la salida.  
Esto es clave cuando se necesita **alinear columnas** o mostrar números con precisión.

Ejemplo:
```bash
awk '{printf "Gen: %-10s Estado: %s\n", $2, $3}' datos.txt
```

Salida:
```
Gen: EGFR       Estado: down
Gen: PTEN       Estado: down
Gen: TP53       Estado: up
```

**Explicación**:  
- `%-10s` → imprime una cadena con ancho fijo de 10 caracteres, alineada a la izquierda.  
- `%s` → imprime otra cadena.  
- `\n` → salto de línea.  
Esto convierte la salida en un **reporte tabulado**.

---

### 4.3 Filtrado por condiciones
El verdadero poder de `awk` está en combinar condiciones con acciones.

Ejemplo: imprimir solo las líneas cuyo tercer campo sea `"up"`.
```bash
awk '$3 == "up" {print $0}' datos.txt
```

Salida:
```
11 TP53 up
17 IDH1 up
10 NF1 up
```

**Explicación**:  
- `$3 == "up"` es la condición.  
- `{print $0}` es la acción.  
- Solo se ejecuta la acción en las líneas que cumplen la condición.

---

### 4.4 Filtrado por número de campos
`NF` permite filtrar según la cantidad de columnas.

Ejemplo: imprimir solo las líneas con más de 2 campos.
```bash
awk 'NF > 2 {print $0}' datos.txt
```

**Explicación**:  
- `NF > 2` → condición: número de campos mayor a 2.  
- `{print $0}` → acción: imprimir la línea completa.  
Esto es útil para detectar **líneas incompletas o corruptas** en un archivo.

---

### 4.5 Regex simples
Además de condiciones numéricas, `awk` permite usar expresiones regulares directamente.

Ejemplo: imprimir las líneas donde el segundo campo termine en `R`.
```bash
awk '$2 ~ /R$/ {print $0}' datos.txt
```

Salida:
```
12 EGFR down
15 EGFR up
```

**Explicación**:  
- `$2 ~ /R$/` → busca coincidencias en el segundo campo que terminen en `R`.  
- `{print $0}` → imprime la línea completa.  
Esto muestra cómo `awk` puede filtrar datos con **patrones textuales**, no solo con números.

---

### Nota de usuario avanzado
- Usa `print` para salidas rápidas y `printf` para reportes formateados.  
- Combina condiciones con acciones para obtener **filtros inteligentes**.  
- `NF` y `NR` son tus aliados para auditar y validar datos.  
- Regex simples permiten transformar `awk` en un **mini motor de búsqueda** dentro de archivos tabulares.  
- Filosofía Unix: piensa en `awk` como un filtro que entiende tanto números como texto, lo que lo hace ideal para pipelines.

---

## 5. Control de flujo y lógica

Aunque `awk` suele usarse como filtro rápido, también es un **lenguaje de programación completo**. Esto significa que puedes aplicar estructuras de control como condicionales y bucles, lo que lo convierte en una herramienta capaz de realizar tareas complejas sin necesidad de otros lenguajes.

---

### 5.1 Condicionales (`if`, `else`)
Los condicionales permiten ejecutar acciones solo si se cumple una condición.

Ejemplo: imprimir si el primer campo es mayor que 15.
```bash
awk '{if ($1 > 15) print $0}' datos.txt
```

Ejemplo con `else`:
```bash
awk '{if ($3 == "up") print "Activo:", $2; else print "Inactivo:", $2}'
datos.txt
```

**Explicación**:  
- `if` evalúa la condición.  
- `else` ejecuta la acción alternativa.  
- Esto permite clasificar datos en categorías directamente desde el archivo.

---

### 5.2 Bucles (`for`, `while`)
Los bucles permiten repetir acciones sobre campos o líneas.

Ejemplo: recorrer todos los campos de una línea.
```bash
awk '{for (i=1; i<=NF; i++) print "Campo", i, ":", $i}' datos.txt
```

Salida:
```
Campo 1 : 12
Campo 2 : EGFR
Campo 3 : down
```

**Explicación**:  
- `for (i=1; i<=NF; i++)` recorre desde el primer campo hasta el último.  
- `$i` accede dinámicamente al campo actual.  
- Esto es útil para **auditar datos** o procesar archivos con columnas variables.

---

### 5.3 Operadores lógicos
`awk` soporta operadores como:
- `&&` → AND (y).  
- `||` → OR (o).  
- `!` → NOT (negación).

Ejemplo: imprimir solo las líneas donde el tercer campo sea `"up"` y el primer campo mayor que 10.
```bash
awk '$3 == "up" && $1 > 10 {print $0}' datos.txt
```

Ejemplo con OR:
```bash
awk '$2 == "EGFR" || $2 == "TP53" {print $0}' datos.txt
```

**Explicación**:  
- Los operadores lógicos permiten combinar condiciones.  
- Esto convierte a `awk` en un **motor de filtrado complejo**, capaz de aplicar reglas múltiples.

---

### 5.4 Uso práctico en pipelines
Ejemplo: contar cuántos genes están “up” y cuántos “down”.
```bash
awk '{if ($3 == "up") up++; else down++} END {print "Up:", up, "Down:",
down}' datos.txt
```

Salida:
```
Up: 12 Down: 8
```

**Explicación**:  
- Se usan variables internas (`up`, `down`) para acumular conteos.  
- El bloque `END` imprime los resultados al final del procesamiento.  
- Esto convierte a `awk` en una herramienta de **estadística rápida**.

---

### Nota de usuario avanzado
- Los condicionales permiten transformar `awk` en un **clasificador de datos**.  
- Los bucles son clave para trabajar con archivos de columnas variables o desconocidas.  
- Los operadores lógicos convierten filtros simples en **reglas complejas**.  
- Filosofía Unix: aunque `awk` es minimalista, su control de flujo lo acerca a un lenguaje completo, ideal para tareas rápidas sin necesidad de scripts externos.

---

## 6. Funciones internas

`awk` incluye un conjunto de **funciones predefinidas** que permiten realizar cálculos, manipular cadenas y trabajar con tiempo. Estas funciones convierten a `awk` en un lenguaje capaz de **procesar y transformar datos en tiempo real**, sin necesidad de depender de otros programas.

---

### 6.1 Funciones numéricas
Estas funciones permiten realizar operaciones matemáticas directamente sobre los datos.

- `int(x)` → devuelve la parte entera de `x`.  
- `sqrt(x)` → raíz cuadrada.  
- `rand()` → número aleatorio entre 0 y 1.  
- `sin(x)`, `cos(x)`, `log(x)` → funciones matemáticas estándar.

Ejemplo: calcular la raíz cuadrada del primer campo.
```bash
awk '{print "Valor:", $1, "Raíz:", sqrt($1)}' datos.txt
```

Salida:
```
Valor: 12 Raíz: 3.4641
Valor: 4 Raíz: 2
Valor: 11 Raíz: 3.3166
```

**Explicación**:  
Esto muestra cómo `awk` puede hacer **cálculos matemáticos rápidos** sobre columnas numéricas.

---

### 6.2 Funciones de cadenas
Las funciones de cadenas son esenciales para manipular texto.

- `length(s)` → longitud de la cadena.  
- `substr(s, i, n)` → subcadena desde posición `i` con longitud `n`.  
- `index(s, t)` → posición de la subcadena `t` dentro de `s`.  
- `split(s, a, sep)` → divide la cadena `s` en el array `a` usando `sep`.  
- `tolower(s)` → convierte a minúsculas.  
- `toupper(s)` → convierte a mayúsculas.

Ejemplo: imprimir el nombre del gen en mayúsculas.
```bash
awk '{print toupper($2)}' datos.txt
```

Salida:
```
EGFR
PTEN
TP53
```

Ejemplo: obtener las tres primeras letras del gen.
```bash
awk '{print substr($2,1,3)}' datos.txt
```

Salida:
```
EGF
PTE
TP5
```

**Explicación**:  
Estas funciones permiten **normalizar y transformar texto**, lo que es clave en bioinformática y procesamiento de logs.

---

### 6.3 Funciones de tiempo
`awk` también puede trabajar con fechas y tiempos.

- `systime()` → devuelve el tiempo actual en segundos desde 1970 (epoch).  
- `strftime(fmt, t)` → convierte un tiempo en formato legible.

Ejemplo: imprimir la fecha actual.
```bash
awk 'BEGIN {print strftime("%Y-%m-%d %H:%M:%S", systime())}'
```

Salida:
```
2026-01-23 10:48:00
```

**Explicación**:  
Esto permite **marcar resultados con fecha y hora**, útil en reportes y auditorías.

---

### Nota de usuario avanzado
- Las funciones numéricas convierten a `awk` en una **calculadora de columnas**.  
- Las funciones de cadenas lo transforman en un **motor de manipulación de texto**.  
- Las funciones de tiempo permiten generar **reportes con contexto temporal**.  
- Filosofía Unix: aprovecha estas funciones para que `awk` sea autosuficiente, evitando depender de scripts externos en Python o Perl para tareas simples.

---

## 7. Definición de funciones propias

Aunque `awk` ya incluye muchas funciones internas, los usuarios avanzados pueden **definir sus propias funciones**. Esto convierte a `awk` en un lenguaje modular, donde puedes encapsular lógica y reutilizarla en diferentes partes de tu script.

---

### 7.1 Sintaxis de funciones
```awk
function nombre(parámetros) {
    # cuerpo de la función
    return valor
}
```

- **nombre** → identificador de la función.  
- **parámetros** → variables que recibe la función.  
- **return** → valor que devuelve.  

---

### 7.2 Ejemplo simple: calcular promedio
```bash
awk '
function promedio(a, b) {
    return (a + b) / 2
}
{print "Promedio de", $1, "y", $3, ":", promedio($1, $3)}
' datos.txt
```

**Explicación**:  
- Se define la función `promedio`.  
- Se llama dentro del bloque principal para calcular el promedio entre el primer y tercer campo.  
- Esto muestra cómo encapsular cálculos en funciones reutilizables.

---

### 7.3 Ejemplo: normalizar texto
```bash
awk '
function normalizar(cadena) {
    return tolower(cadena)
}
{print "Gen:", normalizar($2)}
' datos.txt
```

Salida:
```
Gen: egfr
Gen: pten
Gen: tp53
```

**Explicación**:  
- La función `normalizar` convierte cualquier texto a minúsculas.  
- Se aplica al segundo campo de cada línea.  
- Esto es útil para **estandarizar datos** antes de analizarlos.

---

### 7.4 Ejemplo avanzado: contar ocurrencias
```bash
awk '
function contar(nombre) {
    ocurrencias[nombre]++
}
{contar($2)}
END {
    for (gen in ocurrencias)
        print gen, ":", ocurrencias[gen]
}
' datos.txt
```

Salida:
```
EGFR : 2
PTEN : 1
TP53 : 1
```

**Explicación**:  
- La función `contar` incrementa un contador en un array asociativo.  
- En el bloque `END`, se recorren los resultados y se imprimen.  
- Esto convierte a `awk` en un **motor de conteo y estadísticas**.

---

### Nota de usuario avanzado
- Definir funciones propias permite **modularizar scripts** y mantenerlos legibles.  
- Es una forma de llevar `awk` al nivel de un lenguaje de programación completo.  
- Filosofía Unix: encapsula lógica en funciones pequeñas y reutilizables, en lugar de repetir código.  
- En bioinformática y administración de sistemas, esto permite construir **bibliotecas personales de funciones** para tareas recurrentes.

---

## 8. Casos prácticos avanzados

Hasta ahora hemos visto cómo `awk` puede filtrar, imprimir y transformar datos. En este capítulo vamos a aplicar todo lo aprendido en **escenarios reales**, donde `awk` se convierte en una herramienta de automatización y análisis.

---

### 8.1 Procesamiento de CSV
Muchos archivos de datos usan comas como delimitador. Con `-F ','`, `awk` se convierte en un procesador de CSV ligero.

Ejemplo: archivo `genes.csv`
```
12,EGFR,down
4,PTEN,down
11,TP53,up
```

Comando:
```bash
awk -F ',' '$3 == "up" {print "Gen:", $2, "Estado:", $3}' genes.csv
```

Salida:
```
Gen: TP53 Estado: up
```

**Explicación**:  
- `-F ','` define la coma como delimitador.  
- `$3 == "up"` filtra por la tercera columna.  
- Se imprime un reporte con etiquetas claras.  

Esto es útil para **bioinformática** y análisis de datos tabulares.

---

### 8.2 Estadísticas rápidas
`awk` puede calcular sumas, promedios y conteos sin necesidad de hojas de cálculo.

Ejemplo: calcular promedio del primer campo.
```bash
awk '{suma += $1; count++} END {print "Promedio:", suma/count}' \
datos.txt
```

Salida:
```
Promedio: 11.2
```

**Explicación**:  
- `suma += $1` acumula el valor del primer campo.  
- `count++` cuenta las líneas.  
- En `END`, se imprime el promedio.  
Esto convierte a `awk` en una **calculadora de columnas**.

---

### 8.3 Reordenamiento de columnas
Ejemplo: imprimir primero el estado y luego el gen.
```bash
awk '{print $3, $2}' datos.txt
```

Salida:
```
down EGFR
down PTEN
up TP53
```

**Explicación**:  
- `$3` y `$2` reordenan los campos.  
- Esto permite **reformatear datos** sin modificar el archivo original.

---

### 8.4 Generación de reportes
Ejemplo: contar cuántos genes están “up” y cuántos “down”.
```bash
awk '{if ($3 == "up") up++; else down++} \
END {print "Up:", up, "Down:", down}' datos.txt
```

Salida:
```
Up: 12 Down: 8
```

**Explicación**:  
- Se usan variables internas (`up`, `down`) para acumular.  
- En `END`, se imprime el resultado.  
- Esto convierte a `awk` en un **motor de reportes rápidos**.

---

### 8.5 Integración con otras herramientas
Ejemplo: encontrar genes únicos en un archivo.
```bash
awk '{print $2}' datos.txt | sort | uniq
```

Salida:
```
ATRX
BRAF
CDK4
EGFR
...
```

**Explicación**:  
- `awk` extrae la segunda columna.  
- `sort` ordena los resultados.  
- `uniq` elimina duplicados.  
- Este pipeline muestra cómo `awk` se integra con otras herramientas para **procesar grandes volúmenes de datos**.

---

### Nota de usuario avanzado
- `awk` puede reemplazar tareas que normalmente harías en Excel o Python, pero con **una sola línea**.  
- Su combinación con otras utilidades (`sort`, `uniq`, `grep`) lo convierte en un **pilar de la filosofía Unix**.  
- En bioinformática, administración de sistemas y análisis de logs, `awk` es la herramienta ideal para **automatización ligera y rápida**.  

---

## 9. Awk como lenguaje de programación

Aunque muchos lo ven solo como un comando de filtrado, `awk` es en realidad un **lenguaje de programación minimalista**. Tiene variables, estructuras de control, funciones internas y la posibilidad de definir funciones propias. Esto lo convierte en una herramienta capaz de manejar tareas complejas sin necesidad de lenguajes más pesados.

---

### 9.1 Arrays asociativos
Una de las características más poderosas de `awk` son los **arrays asociativos**.  
- A diferencia de otros lenguajes, en `awk` los arrays no necesitan declararse.  
- Los índices pueden ser cadenas, no solo números.  
- Esto permite construir **tablas dinámicas** directamente desde los datos.

Ejemplo: contar ocurrencias de genes.
```bash
awk '{genes[$2]++} END {for (g in genes) print g, genes[g]}' datos.txt
```

Salida:
```
EGFR 2
PTEN 1
TP53 1
```

**Explicación**:  
- `genes[$2]++` incrementa el contador para cada gen.  
- En `END`, se recorre el array y se imprime el resultado.  
- Esto convierte a `awk` en un **motor de conteo y estadísticas**.

---

### 9.2 Scripts completos en archivos `.awk`
En lugar de escribir comandos en una sola línea, los usuarios avanzados pueden guardar programas completos en archivos `.awk`.

Ejemplo: archivo `conteo.awk`
```awk
{
    if ($3 == "up") up++
    else down++
}
END {
    print "Genes up:", up
    print "Genes down:", down
}
```

Ejecutar:
```bash
awk -f conteo.awk datos.txt
```

Salida:
```
Genes up: 12
Genes down: 8
```

**Explicación**:  
- `-f conteo.awk` ejecuta el script guardado en un archivo.  
- Esto permite **modularizar y reutilizar** programas de `awk`.  
- Ideal para proyectos más grandes donde una sola línea sería difícil de mantener.

---

### 9.3 Comparación con Perl y Python
- **Perl**: más potente en manipulación de texto, pero más complejo.  
- **Python**: más versátil y con librerías extensas, pero requiere más infraestructura.  
- **Awk**: minimalista, rápido, disponible en cualquier sistema Unix/Linux.  

**Filosofía Unix**:  
- Usa `awk` cuando necesites **procesamiento rápido de texto tabular**.  
- Si el problema escala en complejidad, entonces considera lenguajes más grandes.  
- La ventaja de `awk` es que está siempre disponible y no requiere instalación adicional.

---

### Nota de usuario avanzado
- Los arrays asociativos convierten a `awk` en un **mini motor de bases de datos**.  
- Los scripts `.awk` permiten mantener lógica compleja sin perder claridad.  
- Comparar `awk` con otros lenguajes ayuda a entender su **nicho de uso**: rápido, ligero y siempre presente en sistemas Unix.  
- Filosofía Unix: piensa en `awk` como un lenguaje de programación especializado en **texto estructurado**, no como un reemplazo de lenguajes generales.

---

## 10. Ejemplos integradores para “unixsaurios”

### 10.1 Monitoreo de logs en tiempo real
Supongamos que queremos contar cuántos mensajes de error aparecen en un log del sistema.

```bash
tail -f /var/log/syslog | awk '/ERROR/ {count++} \
END {print "Total errores:", count}'
```

**Explicación**:  
- `tail -f` sigue el archivo en tiempo real.  
- `awk` filtra las líneas que contienen `ERROR` y las cuenta.  
- En `END`, imprime el total cuando se detiene el proceso.  
Esto convierte a `awk` en un **monitor ligero de logs**.

---

### 10.2 Extracción de métricas de sistemas
Queremos calcular el promedio de uso de CPU registrado en un archivo `cpu.txt` con formato:

```
12 user
20 system
30 user
25 system
```

```bash
awk '{uso[$2]+=$1; count[$2]++} \
END {for (tipo in uso) print tipo, "promedio:", \
uso[tipo]/count[tipo]}' cpu.txt
```

Salida:
```
user promedio: 21
system promedio: 22.5
```

**Explicación**:  
- Se usan arrays asociativos para acumular valores por categoría (`user`, `system`).  
- En `END`, se calcula el promedio por tipo.  
Esto muestra cómo `awk` puede generar **estadísticas categorizadas**.

---

### 10.3 Bioinformática: anotación y conteo de genes
Archivo `genes.csv`:
```
12,EGFR,down
4,PTEN,down
11,TP53,up
15,EGFR,up
```

```bash
awk -F ',' '{conteo[$2]++; if ($3=="up") up[$2]++} \
END {for (gen in conteo) print gen, "total:", \
conteo[gen], "up:", up[gen]+0}' genes.csv
```

Salida:
```
EGFR total: 2 up: 1
PTEN total: 1 up: 0
TP53 total: 1 up: 1
```

**Explicación**:  
- `conteo[$2]++` cuenta todas las ocurrencias del gen.  
- `up[$2]++` cuenta solo las que están en estado “up”.  
- En `END`, se imprime un reporte con totales y subtotales.  
Esto convierte a `awk` en un **motor de anotación genética rápida**.

---

### 10.4 Automatización de reportes
Queremos generar un reporte con fecha y hora sobre el estado de los genes.

```bash
awk -F ',' 'BEGIN {print "Reporte generado:", \
strftime("%Y-%m-%d %H:%M:%S", systime())} \
{print "Gen:", $2, "Estado:", $3}' \ 
genes.csv
```

Salida:
```
Reporte generado: 2026-01-23 10:56:00
Gen: EGFR Estado: down
Gen: PTEN Estado: down
Gen: TP53 Estado: up
Gen: EGFR Estado: up
```

**Explicación**:  
- `BEGIN` imprime la fecha y hora al inicio.  
- Cada línea se transforma en un reporte legible.  
Esto muestra cómo `awk` puede **automatizar reportes con contexto temporal**.

---

### 10.5 Pipeline integrador
Queremos obtener una lista de genes únicos que estén en estado “up”.

```bash
awk -F ',' '$3=="up" {print $2}' genes.csv | sort | uniq
```

Salida:
```
EGFR
TP53
```

**Explicación**:  
- `awk` filtra las líneas con estado “up” y extrae la segunda columna.  
- `sort` ordena los resultados.  
- `uniq` elimina duplicados.  
Este pipeline demuestra la **filosofía Unix**: herramientas pequeñas que juntas hacen tareas poderosas.

---

## Nota final de usuario avanzado
- Estos ejemplos integran **condiciones, arrays, funciones, delimitadores y pipelines**.  
- `awk` no es solo un comando: es un **lenguaje especializado en texto estructurado**.  
- Su poder está en la **simplicidad y portabilidad**: cualquier sistema Unix/Linux lo tiene disponible.  
- Para un “unixsaurio”, dominar `awk` significa tener un bisturí afilado para cortar, transformar y analizar datos en tiempo real.

---

## Uso de AWK en bioinformática

### 1. Quitar encabezados de un archivo multifasta
En un archivo FASTA, las secuencias comienzan con una línea que empieza con `>` (el encabezado).  
Si queremos quedarnos solo con las secuencias:

```bash
awk '!/^>/' secuencias.fasta
```

**Explicación**:  
- `!/^>/` → selecciona las líneas que **no** empiezan con `>`.  
- Esto elimina los encabezados y deja únicamente las secuencias.  

---

### 2. Extraer solo los encabezados de un multifasta
Si lo que queremos es obtener únicamente los nombres de las secuencias:

```bash
awk '/^>/' secuencias.fasta
```

**Explicación**:  
- `/^>/` → selecciona las líneas que empiezan con `>`.  
- Útil para generar una lista de identificadores de secuencias.

---

### 3. Contar cuántas secuencias hay en un multifasta
Cada encabezado corresponde a una secuencia. Podemos contarlos así:

```bash
awk '/^>/{count++} END {print "Total secuencias:", count}' \ 
secuencias.fasta
```

Salida:
```
Total secuencias: 120
```

**Explicación**:  
- Se incrementa `count` cada vez que aparece un encabezado.  
- En `END`, se imprime el total.  

---

### 4. Calcular la longitud de cada secuencia
Podemos sumar la longitud de las líneas que corresponden a una secuencia (no encabezados).

```bash
awk '!/^>/ {len += length($0)} /^>/ {if (NR>1) print header, \
len; header=$0; len=0} END {print header, len}' secuencias.fasta
```

Salida:
```
>seq1 1500
>seq2 980
>seq3 2100
```

**Explicación**:  
- `length($0)` calcula la longitud de cada línea de secuencia.  
- Se acumula en `len` hasta encontrar un nuevo encabezado.  
- Se imprime el encabezado junto con la longitud total.  
Esto genera un **reporte de longitudes por secuencia**.

---

### 5. Filtrar secuencias por longitud mínima
Ejemplo: queremos solo las secuencias con más de 1000 bases.

```bash
awk '!/^>/ {len += length($0)} /^>/ {if (NR>1 && len>1000) \
print header, len; header=$0; len=0} END {if (len>1000) \
print header, len}' secuencias.fasta
```

**Explicación**:  
- Se calcula la longitud como en el ejemplo anterior.  
- Solo se imprimen las secuencias que superan el umbral.  
Esto es útil para **filtrar secuencias largas** en análisis genómicos.

---

### 6. Extraer secuencias específicas por ID
Si queremos extraer solo la secuencia con encabezado que contiene “TP53”:

```bash
awk '/^>TP53/{flag=1; print; next} /^>/{flag=0} flag' secuencias.fasta
```

**Explicación**:  
- Cuando aparece el encabezado `>TP53`, se activa `flag`.  
- Mientras `flag` esté activo, se imprimen las líneas (la secuencia completa).  
- Cuando aparece otro encabezado, `flag` se desactiva.  

---

### 7. Calcular composición de nucleótidos
Podemos contar cuántas veces aparece cada nucleótido en todas las secuencias.

```bash
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
```
A 4500
C 3200
G 3100
T 4700
```

**Explicación**:  
- Se recorre cada carácter de las secuencias.  
- Se acumula en un array asociativo `count`.  
- En `END`, se imprime la composición total.  
Esto es un **conteo de bases** muy útil en bioinformática.

---

### 8. Generar un reporte de GC content por secuencia
El contenido GC es fundamental en análisis genómicos.

```bash
awk '!/^>/ {
    seq = seq $0
}
 /^>/ {
    if (seq != "") {
        gc = gsub(/[GC]/,"",seq)
        print header, "GC%:", (gc/length(seq))*100
    }
    header=$0; seq=""
}
END {
    gc = gsub(/[GC]/,"",seq)
    print header, "GC%:", (gc/length(seq))*100
}' secuencias.fasta
```

Salida:
```
>seq1 GC%: 42.5
>seq2 GC%: 51.2
>seq3 GC%: 38.9
```

**Explicación**:  
- Se acumula la secuencia completa en `seq`.  
- `gsub(/[GC]/,"",seq)` cuenta cuántas veces aparecen G y C.  
- Se calcula el porcentaje GC.  
Esto genera un **reporte de GC content por secuencia**.

---

## Nota de usuario avanzado
- Estos ejemplos muestran cómo `awk` puede reemplazar scripts largos en Python o Perl para tareas rápidas.  
- En bioinformática, `awk` es ideal para **preprocesamiento de datos**: limpiar, filtrar, contar y generar reportes.  
- Filosofía Unix: usa `awk` como un bisturí para manipular secuencias antes de pasarlas a herramientas más grandes (BLAST, Bowtie, etc.).

