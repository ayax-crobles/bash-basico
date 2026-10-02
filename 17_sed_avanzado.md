# Sed avanzado

**Autor: Christian Robles**

**Fecha: 20/01/2026**

## 1. Introducción avanzada

`sed` es mucho más que un editor de flujo básico:  
- En la **filosofía Unix**, cada herramienta debe hacer una cosa y hacerla bien. `sed` cumple este principio al ser un bisturí para manipular texto en streams.  
- Para un **usuario avanzado**, `sed` es clave en pipelines (`grep | sed | awk`) y en scripts automatizados.  
- La diferencia entre uso básico y avanzado está en el **control fino**: saber cuándo aplicar sustituciones selectivas, cómo manejar buffers, y cómo aprovechar expresiones regulares complejas.  

**Idea central**: piensa en `sed` como un filtro que transforma datos en tiempo real, sin necesidad de abrir editores ni modificar manualmente archivos.

---

## 2. Opciones adicionales de ejecución

Además de las opciones comunes (`-n`, `-i`, `-e`, `-f`), existen otras menos conocidas pero muy útiles en contextos avanzados.

### 2.1 `-u` (sin buffer)
- Procesa línea por línea sin usar buffer.  
- Útil en **streams en tiempo real** (por ejemplo, procesar logs mientras se generan).  

```bash
tail -f /var/log/syslog | sed -u 's/error/ALERTA/i'
```

### 2.2 `-s` (archivos separados)
- Cuando se procesan varios archivos, por defecto `sed` los trata como un único flujo.  
- Con `-s`, cada archivo se procesa **por separado**.  

```bash
sed -s '1p' archivo1.txt archivo2.txt
```

Esto imprime la primera línea de cada archivo, no solo del primero.


### 2.3 `-r` (regex extendidas)
- Activa **expresiones regulares extendidas (ERE)** sin necesidad de escapar metacaracteres.  
- Equivalente a `-E` en GNU sed.  

```bash
sed -r 's/(foo|bar)/X/g' archivo.txt
```

En BRE habría que escribir `\(foo\|bar\)`.


### Nota de usuario avanzado
- Usa `-u` en pipelines de monitoreo.  
- Usa `-s` cuando quieras resultados independientes por archivo.  
- Usa `-r`/`-E` para simplificar regex complejas y evitar escapes innecesarios.  

---

## 3. Comandos menos comunes pero útiles

Además de los comandos básicos, `sed` incluye órdenes menos conocidas que resultan muy útiles en escenarios avanzados de depuración y filtrado.

---

### 3.1 `l` (listing)
- **Función**: muestra todos los caracteres, incluidos los no imprimibles, en forma visible.  
- **Uso típico**: depuración de archivos con caracteres ocultos (tabulaciones, saltos de línea especiales, espacios extra).  

```bash
echo -e "uno\tdos" | sed -n 'l'
```

Salida:
```
uno\t dos$
```

> El carácter de tabulación se muestra explícitamente como `\t` y el final de línea como `$`.

---

### 3.2 `!` (negación)
- **Función**: aplica un comando a las líneas que **no** coinciden con el patrón.  
- **Uso típico**: filtrar inversamente, procesar solo las líneas que no cumplen una condición.  

#### Ejemplo: borrar todas las líneas que **no** contienen "INFO"
```bash
sed '/INFO/!d' log.txt
```

Salida:
```
INFO: inicio
INFO: proceso
```

#### Ejemplo: sustituir "OK" por "CORRECTO" en todas las líneas que **no** contienen "ERROR"
```bash
sed '/ERROR/!s/OK/CORRECTO/' archivo.txt
```

---

### Nota de usuario avanzado
- `l` es clave para **auditar datos** y detectar caracteres invisibles que pueden romper scripts.  
- `!` permite aplicar transformaciones selectivas sin necesidad de usar `grep -v`, manteniendo todo dentro de `sed`.  
- Filosofía Unix: menos herramientas en el pipeline, más control en un solo filtro.

---

## 4. Direccionamiento avanzado

Además de seleccionar líneas por número o patrón, `sed` ofrece formas más sofisticadas de direccionamiento que permiten trabajar con **rangos dinámicos** y **saltos regulares**.

---

### 4.1 `+N` (línea y siguientes N)
- **Función**: selecciona una línea específica y las siguientes N líneas.  
- **Uso típico**: extraer bloques de texto consecutivos a partir de una línea clave.  

#### Ejemplo: imprimir la tercera línea y las dos siguientes
```bash
sed -n '3,+2p' archivo.txt
```

Salida (si `archivo.txt` contiene):
```
linea1
linea2
linea3
linea4
linea5
```

Resultado:
```
linea3
linea4
linea5
```

---

### 4.2 `~N` (saltos regulares)
- **Función**: selecciona una línea inicial y luego cada N líneas.  
- **Uso típico**: muestreo de datos, extracción periódica de registros.  

#### Ejemplo: imprimir la primera línea y luego cada segunda línea
```bash
sed -n '1~2p' archivo.txt
```

Salida (si `archivo.txt` contiene):
```
linea1
linea2
linea3
linea4
linea5
linea6
```

Resultado:
```
linea1
linea3
linea5
```

---

### Nota de usuario avanzado
- `+N` es útil para **bloques de contexto**: extraer una línea clave y sus alrededores.  
- `~N` es ideal para **muestreo periódico**: por ejemplo, tomar cada décima línea de un log para inspección rápida.  
- Filosofía Unix: aprovecha estas formas de direccionamiento para reducir la necesidad de herramientas externas como `head`, `tail` o `awk`.

---

## 5. Sustitución por número de ocurrencia

Por defecto, el comando `s///` sustituye la **primera coincidencia** en cada línea. Con el flag `g` sustituye **todas**. Sin embargo, también podemos indicar un **número específico de ocurrencia** dentro de la línea.

### 5.1 Sintaxis
```bash
s/patrón/reemplazo/n
```
- `n` = número de ocurrencia que se desea reemplazar (2, 3, etc.).  
- Si el patrón aparece menos veces que `n`, no se realiza sustitución.

### 5.2 Ejemplo: sustituir solo la segunda ocurrencia
```bash
echo "EGFR EGFR EGFR" | sed 's/EGFR/gen_sustituto/2'
```

Salida:
```
EGFR gen_sustituto EGFR
```

### 5.3 Ejemplo: sustituir solo la tercera ocurrencia
```bash
echo "uno uno uno uno" | sed 's/uno/dos/3'
```

Salida:
```
uno uno dos uno
```

### 5.4 Combinación con `g`
Si se usa `g`, se ignora el número y se sustituyen todas las ocurrencias.  
Por tanto, `s/patrón/reemplazo/g` tiene prioridad sobre `s/patrón/reemplazo/n`.

### Nota de usuario avanzado
- Este recurso es útil cuando se necesita **control fino** sobre datos repetitivos, por ejemplo, modificar solo la segunda columna de un archivo delimitado por espacios.  
- Filosofía Unix: precisión quirúrgica, evitando sustituciones innecesarias que podrían alterar datos críticos.

---

## 6. Regex avanzadas aplicadas

Las **expresiones regulares (regex)** son el motor que convierte a `sed` en una herramienta quirúrgica. Mientras que los comandos básicos (`s`, `d`, `p`) permiten transformaciones directas, las regex permiten **describir patrones complejos de texto** y manipularlos con precisión.  

En la filosofía Unix, esto significa que no necesitas un programa pesado para procesar datos: basta con una línea de `sed` bien diseñada para extraer, transformar o reordenar información.  

---

### 6.1 Uso de anclas y cuantificadores

Las regex se basan en símbolos que representan posiciones o repeticiones:

- `^` → inicio de línea.  
- `$` → fin de línea.  
- `.` → cualquier carácter.  
- `*` → cero o más repeticiones.  
- `+` → una o más repeticiones (requiere `-r` o `-E`).  

Esto permite construir patrones que no dependen de palabras exactas, sino de **estructuras de texto**.

#### Ejemplo: sustituir todo hasta la primera `R`
```bash
sed 's/^.*R/REP/' archivo.txt
```

Si la línea es:
```
ATRX
```

Resultado:
```
REPX
```

**Explicación paso a paso**:
1. `^` → empieza desde el inicio de la línea.  
2. `.*` → consume cualquier cantidad de caracteres.  
3. `R` → se detiene en la primera `R`.  
4. Todo lo que coincide se reemplaza por `REP`.  

Esto es útil para **recortar prefijos** o limpiar datos hasta un marcador específico.

---

### 6.2 Capturas con paréntesis

Los paréntesis `( )` permiten **agrupar partes de un patrón** y reutilizarlas después con referencias como `\1`, `\2`, etc.  
Esto convierte a `sed` en un motor de **reordenamiento de texto**.

#### Ejemplo: reordenar nombre y apellido
```bash
echo "Perez Juan" | sed -E 's/(.*) (.*)/Nombre: \2, Apellido: \1/'
```

Salida:
```
Nombre: Juan, Apellido: Perez
```

**Explicación paso a paso**:
1. `(.*)` → captura todo hasta el espacio → `Perez`.  
2. `(.*)` → captura lo que sigue → `Juan`.  
3. `\1` → referencia a la primera captura.  
4. `\2` → referencia a la segunda captura.  
5. El reemplazo reorganiza los datos en formato más legible.  

Esto es clave en **procesamiento de datos tabulares** o cuando se necesita cambiar el orden de columnas.

---

### 6.3 Aplicación práctica en bioinformática

Archivo `genes.txt`:
```
EGFR
PTEN
TP53
IDH1
```

#### Ejemplo: añadir contexto con captura
```bash
sed -E 's/(.*)/El gen \1 está alterado en glioblastoma/' genes.txt
```

Salida:
```
El gen EGFR está alterado en glioblastoma
El gen PTEN está alterado en glioblastoma
El gen TP53 está alterado en glioblastoma
El gen IDH1 está alterado en glioblastoma
```

**Explicación**:
- `(.*)` captura toda la línea (el nombre del gen).  
- `\1` reutiliza esa captura en el reemplazo.  
- Se añade un contexto semántico, transformando datos crudos en frases interpretables.  

Esto demuestra cómo `sed` puede **automatizar anotaciones** en bioinformática, sin necesidad de escribir scripts largos.

---

### Nota de usuario avanzado

- **Regex son bisturís**: permiten cortar y transformar texto con precisión, evitando sustituciones masivas que podrían dañar datos.  
- **Piensa en estructuras, no en palabras**: regex describe patrones, no cadenas exactas.  
- **Unix mindset**: usa regex para que `sed` haga el trabajo de varios comandos a la vez, manteniendo los pipelines simples y potentes.  
- **Aplicación real**: desde limpiar logs hasta enriquecer datos científicos, las regex convierten a `sed` en una herramienta de **extracción semántica**.

---

## 7. Variables de sustitución y grupos de captura

Uno de los aspectos más potentes de `sed` es que no se limita a reemplazar texto literal: puede **capturar fragmentos de una línea y reutilizarlos dinámicamente** en el reemplazo. Esto convierte a `sed` en una herramienta de **reescritura estructural**, capaz de transformar datos crudos en información organizada y legible.

En la **filosofía Unix**, esto refleja la idea de que una herramienta pequeña puede realizar tareas complejas si se combina con expresiones regulares. En lugar de usar programas grandes para reordenar datos, basta con un patrón bien diseñado y un reemplazo inteligente.

---

### 7.1 Concepto básico
- Los paréntesis `( )` definen **grupos de captura** dentro de una expresión regular.  
- Cada grupo se almacena en una variable temporal: `\1`, `\2`, `\3`, etc., según el orden en que aparecen.  
- Estas referencias se pueden usar en el reemplazo para **reutilizar o reordenar** partes del texto original.  
- Para habilitar esta sintaxis, se recomienda usar `-E` (GNU sed) o `-r` (BSD sed), que activan las **expresiones regulares extendidas (ERE)**.

---

### 7.2 Ejemplo simple: duplicar un patrón
```bash
echo "ABC" | sed -E 's/(A)/\1\1/'
```

Salida:
```
AABC
```

**Explicación paso a paso**:
1. `(A)` captura la letra `A`.  
2. `\1` representa esa captura.  
3. En el reemplazo escribimos `\1\1`, lo que repite la letra capturada.  
4. El resultado es que la primera `A` se duplica.  

Este ejemplo muestra cómo `sed` puede **generar contenido nuevo a partir de lo existente**, sin necesidad de escribirlo manualmente.

---

### 7.3 Ejemplo: reordenar columnas
Archivo `nombres.txt`:
```
Perez Juan
Lopez Maria
```

```bash
sed -E 's/(.*) (.*)/Nombre: \2, Apellido: \1/' nombres.txt
```

Salida:
```
Nombre: Juan, Apellido: Perez
Nombre: Maria, Apellido: Lopez
```

**Explicación**:
- `(.*)` captura todo hasta el espacio → apellido.  
- `(.*)` captura lo que sigue → nombre.  
- `\1` y `\2` permiten reordenar los datos.  
- El reemplazo convierte un formato crudo en un formato legible y estructurado.  

Este patrón es muy útil en **procesamiento de datos tabulares**, donde las columnas deben reorganizarse.

---

### 7.4 Ejemplo aplicado en bioinformática
Archivo `genes.txt`:
```
EGFR
PTEN
TP53
```

```bash
sed -E 's/(.*)/El gen \1 está alterado en glioblastoma/' genes.txt
```

Salida:
```
El gen EGFR está alterado en glioblastoma
El gen PTEN está alterado en glioblastoma
El gen TP53 está alterado en glioblastoma
```

**Explicación**:
- `(.*)` captura toda la línea (el nombre del gen).  
- `\1` reutiliza esa captura en el reemplazo.  
- Se añade contexto semántico, transformando datos crudos en frases interpretables.  

Este ejemplo muestra cómo `sed` puede **automatizar anotaciones científicas**, generando frases completas a partir de listas simples.

---

### 7.5 Ejemplo avanzado: múltiples capturas
Archivo `datos.txt`:
```
Juan,25
Maria,30
```

```bash
sed -E 's/(.*),(.*)/Nombre: \1, Edad: \2/' datos.txt
```

Salida:
```
Nombre: Juan, Edad: 25
Nombre: Maria, Edad: 30
```

**Explicación**:
- `(.*)` antes de la coma captura el nombre.  
- `(.*)` después de la coma captura la edad.  
- `\1` y `\2` permiten reconstruir la información en un formato más claro.  

Esto demuestra cómo `sed` puede **parsear CSV simples** y convertirlos en reportes legibles.

---

### Nota de usuario avanzado
- Los grupos de captura convierten a `sed` en un **motor de plantillas dinámicas**: puedes insertar datos en frases, reorganizar columnas o enriquecer registros.  
- Filosofía Unix: en lugar de depender de programas grandes para manipular datos, usa `sed` con regex para lograr resultados rápidos y precisos.  
- Este enfoque es clave en **automatización de reportes**, **procesamiento de logs** y **bioinformática**, donde los datos crudos deben transformarse en información contextualizada.  
- Piensa en `sed` como un **traductor automático**: toma datos en bruto y los convierte en mensajes comprensibles para humanos o sistemas.

---

## 8. Ejemplos integradores avanzados

En este capítulo reunimos las técnicas vistas (opciones adicionales, comandos especiales, direccionamiento avanzado, ocurrencias específicas, regex y capturas) en **casos prácticos más complejos**. La idea es mostrar cómo `sed` se convierte en una herramienta de **automatización real**, siguiendo la filosofía Unix: *hacer más con menos*.

### 8.1 Filtrado y sustitución selectiva en logs
Archivo `log.txt`:
```
INFO: inicio
ERROR: fallo crítico
OK: proceso completado
INFO: cierre
```

#### Objetivo
- Conservar solo las líneas que **no** contienen `INFO`.  
- Reemplazar `OK` por `CORRECTO`.  

```bash
sed '/INFO/!s/OK/CORRECTO/' log.txt
```

**Explicación**:
- `/INFO/!` → aplica el comando solo a las líneas que no contienen `INFO`.  
- `s/OK/CORRECTO/` → sustituye `OK` por `CORRECTO`.  
- Resultado: se filtran y transforman las líneas relevantes sin necesidad de `grep -v`.

---

### 8.2 Muestreo periódico de registros
Archivo `datos.txt`:
```
linea1
linea2
linea3
linea4
linea5
linea6
```

#### Objetivo
- Extraer cada tercera línea para inspección rápida.  

```bash
sed -n '1~3p' datos.txt
```

Salida:
```
linea1
linea4
```

**Explicación**:
- `1~3` → imprime la primera línea y luego cada tercera.  
- Útil para **muestrear grandes logs** sin procesar todo el archivo.

---

### 8.3 Sustitución por ocurrencia específica
Archivo `genes_dup.txt`:
```
EGFR EGFR EGFR
PTEN PTEN
```

#### Objetivo
- Reemplazar solo la segunda ocurrencia de `EGFR` por `GEN_ALT`.  

```bash
sed 's/EGFR/GEN_ALT/2' genes_dup.txt
```

Salida:
```
EGFR GEN_ALT EGFR
PTEN PTEN
```

**Explicación**:
- `/2` → sustituye únicamente la segunda coincidencia por línea.  
- Control fino sobre datos repetitivos.

---

### 8.4 Reescritura semántica con capturas
Archivo `usuarios.csv`:
```
Juan,25
Maria,30
```

#### Objetivo
- Convertir el CSV en frases legibles.  

```bash
sed -E 's/(.*),(.*)/Nombre: \1, Edad: \2/' usuarios.csv
```

Salida:
```
Nombre: Juan, Edad: 25
Nombre: Maria, Edad: 30
```

**Explicación**:
- `(.*)` antes de la coma captura el nombre.  
- `(.*)` después de la coma captura la edad.  
- `\1` y `\2` permiten reconstruir la información en un formato más claro.  
- Filosofía Unix: transformar datos crudos en reportes sin depender de software pesado.

---

### 8.5 Regex como bisturí
Archivo `genes.txt`:
```
ATRX
RB1
PIK3R1
```

#### Objetivo
- Sustituir todo hasta la primera `R` por `REP`.  

```bash
sed 's/^.*R/REP/' genes.txt
```

Salida:
```
REP
REP
REP
```

**Explicación**:
- `^.*R` → consume todo desde el inicio hasta la primera `R`.  
- Se reemplaza por `REP`.  
- Ejemplo de cómo regex permite **recortar texto con precisión quirúrgica**.

---

### Nota de usuario avanzado
- Estos ejemplos muestran cómo `sed` puede actuar como **filtro inteligente** en pipelines.  
- La clave está en combinar opciones (`-n`, `-u`, `-s`), direccionamiento (`+N`, `~N`), ocurrencias específicas y regex avanzadas.  
- Filosofía Unix: cada línea de `sed` es una herramienta de precisión que evita scripts largos y complejos.  

---

## 9. Conclusiones y filosofía de uso avanzado

Llegados a este punto, hemos visto cómo `sed` pasa de ser un simple editor de flujo a convertirse en una **herramienta de precisión para usuarios avanzados**. La clave está en entender no solo la sintaxis, sino la **filosofía Unix** que lo sustenta.

### 9.1 Filosofía Unix aplicada a `sed`
- **Haz una cosa y hazla bien**: `sed` no pretende reemplazar a editores completos, sino ser un filtro rápido y potente.  
- **Composición de herramientas**: el verdadero poder surge cuando `sed` se integra en pipelines con `grep`, `awk`, `cut`, `sort`.  
- **Texto como interfaz universal**: en Unix todo es texto, y `sed` es un bisturí para manipularlo.  

### 9.2 Diferencia entre uso básico y avanzado
- **Uso básico**: sustituciones simples (`s/foo/bar/`), borrado de líneas (`d`), impresión (`p`).  
- **Uso avanzado**: direccionamiento dinámico (`+N`, `~N`), control de ocurrencias (`/2`), regex complejas (`^.*R`), capturas y reordenamiento (`\1`, `\2`).  
- El salto cualitativo está en **pensar en patrones y estructuras**, no en cadenas literales.

### 9.3 Mentalidad de usuario avanzado
- **Precisión quirúrgica**: aplicar transformaciones solo donde corresponden, evitando efectos colaterales.  
- **Portabilidad**: escribir scripts que funcionen tanto en GNU como en BSD sed.  
- **Legibilidad**: documentar y modularizar comandos largos en archivos `.sed`.  
- **Depuración**: usar comandos como `l` para detectar caracteres invisibles y `!` para aplicar negaciones.  

### 9.4 Casos de uso reales
- **Administración de sistemas**: limpieza de logs, sustitución de rutas en configuraciones, muestreo de registros.  
- **Bioinformática**: anotación automática de genes, reescritura de listas en frases interpretables.  
- **Procesamiento de datos**: parseo de CSV simples, reordenamiento de columnas, generación de reportes rápidos.  
- **Automatización**: integración en scripts para transformar datos sin intervención manual.  

### 9.5 Reflexión final
`sed` es un ejemplo perfecto de cómo una herramienta pequeña puede ser **extraordinariamente poderosa** cuando se domina. Para un usuario avanzado, la clave no está en memorizar comandos, sino en **pensar en términos de patrones y flujos de datos**.  

En la filosofía Unix, `sed` representa la idea de que el texto es maleable y que, con las herramientas adecuadas, se puede transformar en conocimiento útil. Dominar `sed` no solo mejora la productividad, sino que enseña a **pensar como un arquitecto de datos**, capaz de diseñar soluciones simples y elegantes para problemas complejos.

