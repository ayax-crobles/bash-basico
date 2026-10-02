# Guía completa de `sed`

**Autor: MD. Christian Robles**  
**Fecha: 15/01/2026**

## 1. Introducción

### 1.1 ¿Qué es `sed`?
`sed` significa *stream editor*. Es una herramienta de línea de comandos en sistemas Unix/Linux que permite **editar flujos de texto de manera no interactiva**. A diferencia de editores como `vi` o `nano`, `sed` no abre un archivo para que el usuario lo modifique manualmente, sino que recibe texto (desde un archivo o desde la entrada estándar), lo procesa según un conjunto de instrucciones, y devuelve el resultado.

### 1.2 Contexto histórico
- `sed` fue creado en los años 70 como parte del ecosistema Unix, inspirado en el editor `ed`.  
- Su diseño se centra en la **automatización de tareas repetitivas** sobre texto, especialmente en scripts y pipelines.  
- GNU `sed` es la implementación más común en Linux, aunque existen variantes en BSD y otros sistemas.  

### 1.3 Filosofía de uso
- **Edición no interactiva**: todo se define en un *script* o en una línea de comando.  
- **Procesamiento secuencial**: `sed` lee línea por línea, aplica reglas, y produce salida.  
- **Integración con pipelines**: se usa junto con comandos como `grep`, `awk`, `cut`, `sort`, etc.  

### 1.4 Diferencias con otros editores
- **`vi`/`nano`**: interactivos, requieren intervención manual.  
- **`ed`**: precursor de `sed`, también no interactivo pero menos potente.  
- **`sed`**: especializado en sustituciones, borrado, inserciones y transformaciones rápidas.  

### 1.5 Usos típicos
- Sustituir cadenas de texto en archivos de configuración.  
- Limpiar o transformar logs.  
- Automatizar cambios masivos en múltiples archivos.  
- Procesar datos en pipelines sin necesidad de abrirlos en un editor.  

### Ejemplo introductorio
Supongamos que tenemos un archivo `usuarios.txt` con el siguiente contenido:

```
juan
maria
pedro
```

Si queremos reemplazar `maria` por `maría` (con tilde), podemos usar:

```bash
sed 's/maria/maría/' usuarios.txt
```

Salida:

```
juan
maría
pedro
```

## 2. Fundamentos de uso

### 2.1 Sintaxis general
La forma básica de invocar `sed` es:

```bash
sed [opciones] 'script' archivo
```

- **`[opciones]`**: modifican el comportamiento de `sed` (ejemplo: `-n`, `-i`).  
- **`'script'`**: conjunto de instrucciones que `sed` aplicará al texto.  
- **`archivo`**: archivo de entrada. Si no se especifica, `sed` lee de la entrada estándar (stdin).  

Ejemplo simple:

```bash
sed 's/unix/linux/' archivo.txt
```

Este comando sustituye la primera aparición de la palabra `unix` por `linux` en cada línea del archivo.

---

### 2.2 Flujo de texto
`sed` trabaja sobre **flujos de texto**:
- **Entrada estándar (stdin)**: se puede pasar texto directamente o usar pipes.  
- **Salida estándar (stdout)**: el resultado se imprime en pantalla, a menos que se redirija a un archivo.  

Ejemplo con pipe:

```bash
echo "hola mundo" | sed 's/mundo/universo/'
```

Salida:

```
hola universo
```

---

### 2.3 Procesamiento línea por línea
- `sed` lee el archivo **línea por línea**.  
- Cada línea se carga en el **espacio de patrón**.  
- Se aplican las instrucciones del script.  
- La línea resultante se envía a la salida (a menos que se use `-n`).  

Este comportamiento secuencial es clave: `sed` no carga todo el archivo en memoria, sino que procesa flujo continuo.

---

### 2.4 Redirecciones y pipes
`sed` se integra fácilmente en pipelines:

```bash
cat archivo.log | sed 's/error/ERROR/' | grep ERROR
```

- `cat` envía el contenido del archivo.  
- `sed` transforma las ocurrencias de `error` en `ERROR`.  
- `grep` filtra solo las líneas que contienen `ERROR`.  

---

### 2.5 Ejemplo introductorio con archivo
Supongamos un archivo `datos.txt`:

```
nombre: Juan
nombre: Maria
nombre: Pedro
```

Si queremos cambiar todos los nombres `Maria` por `María`:

```bash
sed 's/Maria/María/' datos.txt
```

Salida:

```
nombre: Juan
nombre: María
nombre: Pedro
```

## 3. Comandos esenciales

### 3.1 Sustitución (`s///`)
El comando más usado en `sed` es la **sustitución**. Su sintaxis es:

```bash
s/patrón/reemplazo/flags
```

- **`patrón`**: expresión regular que define qué buscar.  
- **`reemplazo`**: texto que sustituirá al patrón encontrado.  
- **`flags`**: modificadores opcionales que alteran el comportamiento.  

#### Flags comunes
- `g`: reemplaza todas las ocurrencias en la línea (global).  
- `i`: ignora mayúsculas/minúsculas.  
- `p`: imprime la línea modificada (usado con `-n`).  
- `n`: avanza a la siguiente línea después de la sustitución.  

#### Ejemplo básico
Archivo `ejemplo.txt`:

```
unix es poderoso
unix es estable
```

Comando:

```bash
sed 's/unix/linux/' ejemplo.txt
```

Salida:

```
linux es poderoso
linux es estable
```

#### Ejemplo con `g` (global)
```bash
echo "uno uno uno" | sed 's/uno/dos/g'
```

Salida:

```
dos dos dos
```

#### Ejemplo con `i` (ignorar mayúsculas)
```bash
echo "Unix UNIX unix" | sed 's/unix/linux/gi'
```

Salida:

```
linux linux linux
```

---

### 3.2 Borrado (`d`)
El comando `d` elimina líneas que coincidan con un patrón o rango.

#### Ejemplo: borrar líneas que contienen "error"
```bash
sed '/error/d' log.txt
```

#### Ejemplo: borrar la segunda línea
```bash
sed '2d' archivo.txt
```

#### Ejemplo: borrar un rango de líneas (2 a 4)
```bash
sed '2,4d' archivo.txt
```

---

### 3.3 Impresión (`p`)
El comando `p` imprime líneas seleccionadas. Se usa frecuentemente con la opción `-n` para suprimir la salida automática.

#### Ejemplo: imprimir solo la primera línea
```bash
sed -n '1p' archivo.txt
```

#### Ejemplo: imprimir líneas que contienen "INFO"
```bash
sed -n '/INFO/p' log.txt
```

---

### 3.4 Inserción (`i`) y adición (`a`)
- `i`: inserta texto **antes** de la línea seleccionada.  
- `a`: añade texto **después** de la línea seleccionada.  

#### Ejemplo: insertar antes de la primera línea
```bash
sed '1i\Encabezado' archivo.txt
```

#### Ejemplo: añadir después de la última línea
```bash
sed '$a\Pie de página' archivo.txt
```

---

### 3.5 Reemplazo de líneas completas (`c`)
El comando `c` reemplaza la línea seleccionada por un nuevo texto.

#### Ejemplo: reemplazar la tercera línea
```bash
sed '3c\Esta es la nueva línea' archivo.txt
```

---

### 3.6 Transformación de caracteres (`y///`)
El comando `y` realiza una **transliteración** de caracteres, similar a `tr`.

#### Ejemplo: convertir minúsculas a mayúsculas
```bash
echo "abc" | sed 'y/abc/ABC/'
```

Salida:

```
ABC
```

## 4. Direccionamiento y rangos

En `sed`, los comandos no siempre se aplican a todas las líneas. Podemos **dirigir** la acción a líneas específicas, ya sea por número, por patrón, o por rangos.

---

### 4.1 Selección por número de línea
Podemos indicar directamente el número de línea sobre el cual aplicar un comando.

#### Ejemplo: borrar la primera línea
```bash
sed '1d' archivo.txt
```

#### Ejemplo: imprimir la tercera línea
```bash
sed -n '3p' archivo.txt
```

---

### 4.2 Selección por patrón
En lugar de números, podemos usar expresiones regulares para seleccionar líneas.

#### Ejemplo: borrar líneas que contienen "ERROR"
```bash
sed '/ERROR/d' log.txt
```

#### Ejemplo: imprimir líneas que contienen "INFO"
```bash
sed -n '/INFO/p' log.txt
```

---

### 4.3 Rangos de líneas
Podemos definir un rango de líneas usando dos direcciones separadas por coma.

#### Ejemplo: borrar de la línea 2 a la 5
```bash
sed '2,5d' archivo.txt
```

#### Ejemplo: imprimir de la línea 10 a la 15
```bash
sed -n '10,15p' archivo.txt
```

---

### 4.4 Rangos por patrón
También es posible definir rangos usando patrones.

#### Ejemplo: borrar desde la primera línea que contiene "INICIO" hasta la primera que contiene "FIN"
```bash
sed '/INICIO/,/FIN/d' archivo.txt
```

#### Ejemplo: imprimir desde "BEGIN" hasta "END"
```bash
sed -n '/BEGIN/,/END/p' archivo.txt
```

---

### 4.5 Combinación de patrones y números
Podemos mezclar números y patrones en los rangos.

#### Ejemplo: borrar desde la línea 5 hasta la primera línea que contiene "STOP"
```bash
sed '5,/STOP/d' archivo.txt
```

---

### 4.6 Ejemplo integrador
Archivo `ejemplo.txt`:

```
1: inicio
2: datos
3: error
4: datos
5: fin
```

Comando:

```bash
sed '2,4d' ejemplo.txt
```

Salida:

```
1: inicio
5: fin
```

## 5. Expresiones regulares en `sed`

### 5.1 Importancia de las expresiones regulares
Las expresiones regulares (regex) son el motor que permite a `sed` realizar búsquedas y transformaciones complejas.  
- Permiten identificar patrones en el texto más allá de coincidencias literales.  
- Son esenciales para sustituciones, borrados y selecciones avanzadas.  

---

### 5.2 Tipos de expresiones regulares en `sed`
- **BRE (Basic Regular Expressions)**: el modo por defecto en `sed`.  
- **ERE (Extended Regular Expressions)**: disponibles en GNU `sed` con la opción `-r` o `-E`.  

#### Diferencias principales
- En BRE, los metacaracteres como `+`, `?`, `|` requieren ser escapados (`\+`, `\?`, `\|`).  
- En ERE, se usan directamente (`+`, `?`, `|`).  

---

### 5.3 Metacaracteres básicos
- `.` : cualquier carácter.  
- `^` : inicio de línea.  
- `$` : fin de línea.  
- `*` : cero o más repeticiones.  
- `[]` : conjunto de caracteres.  
- `[^]` : negación de conjunto.  

#### Ejemplo: sustituir cualquier dígito por `X`
```bash
echo "abc123" | sed 's/[0-9]/X/g'
```

Salida:

```
abcXXX
```

---

### 5.4 Uso de anclas
- `^patrón` : coincide solo si el patrón está al inicio de la línea.  
- `patrón$` : coincide solo si el patrón está al final de la línea.  

#### Ejemplo: borrar líneas que empiezan con `#`
```bash
sed '/^#/d' archivo.conf
```

---

### 5.5 Grupos y repeticiones
En BRE, los paréntesis deben escaparse: `\(...\)`.  
En ERE, se usan directamente: `(...)`.

#### Ejemplo con BRE: capturar y reordenar
```bash
echo "2026-01-15" | sed 's/\([0-9]\{4\}\)-\([0-9]\{2\}\)-\([0-9]\{2\}\)/\3\/\2\/\1/'
```

Salida:

```
15/01/2026
```

---

### 5.6 Alternancia
- En BRE: `\|`.  
- En ERE: `|`.  

#### Ejemplo con ERE: sustituir "perro" o "gato" por "animal"
```bash
echo "perro gato ave" | sed -E 's/perro|gato/animal/g'
```

Salida:

```
animal animal ave
```

---

### 5.7 Ejemplo integrador
Archivo `log.txt`:

```
INFO: proceso iniciado
ERROR: fallo en módulo
WARNING: recurso limitado
```

Comando:

```bash
sed -E 's/^(ERROR|WARNING)/ALERTA/' log.txt
```

Salida:

```
INFO: proceso iniciado
ALERTA: fallo en módulo
ALERTA: recurso limitado
```

---

## 6. Opciones de ejecución

Las opciones de `sed` permiten modificar su comportamiento y ampliar su utilidad. Aquí veremos las más importantes.

---

### 6.1 Opción `-n` (supresión de salida automática)
Por defecto, `sed` imprime todas las líneas procesadas.  
Con `-n`, se suprime la salida automática y solo se muestran las líneas que se imprimen explícitamente con `p`.

#### Ejemplo: imprimir solo las líneas que contienen "INFO"
```bash
sed -n '/INFO/p' log.txt
```

---

### 6.2 Opción `-i` (edición en el lugar)
Permite modificar directamente el archivo de entrada, sin necesidad de redirección.  
En GNU `sed`, se puede usar `-i.bak` para crear una copia de respaldo.

#### Ejemplo: reemplazar "foo" por "bar" en el archivo y guardar copia
```bash
sed -i.bak 's/foo/bar/g' archivo.txt
```

- `archivo.txt` se modifica directamente.  
- Se crea `archivo.txt.bak` como respaldo.  

---

### 6.3 Opción `-e` (múltiples scripts)
Permite ejecutar varias instrucciones en una sola invocación de `sed`.

#### Ejemplo: sustituir y borrar en un solo comando
```bash
sed -e 's/foo/bar/' -e '/baz/d' archivo.txt
```

---

### 6.4 Opción `-f` (cargar script desde archivo)
En lugar de escribir el script en la línea de comandos, se puede guardar en un archivo y cargarlo con `-f`.

#### Ejemplo:
Archivo `script.sed`:
```bash
s/foo/bar/g
/ERROR/d
```

Comando:
```bash
sed -f script.sed archivo.txt
```

---

### 6.5 Ejemplo integrador
Archivo `datos.txt`:

```
usuario: foo
usuario: baz
usuario: foo
```

Comando:
```bash
sed -i -e 's/foo/bar/g' -e '/baz/d' datos.txt
```

Resultado en `datos.txt`:
```
usuario: bar
usuario: bar
```

## 7. Buffers y espacio de trabajo

### 7.1 Concepto de espacios en `sed`
`sed` trabaja con dos áreas de memoria internas:
- **Espacio de patrón (pattern space)**: contiene la línea actual que se está procesando.  
- **Espacio de hold (hold space)**: un área secundaria donde se puede guardar temporalmente información para usarla más adelante.  

Por defecto, `sed` solo usa el **espacio de patrón**, pero con ciertos comandos podemos interactuar con el **espacio de hold**.

---

### 7.2 Comandos para manejar el espacio de hold
- **`h`**: copia el contenido del espacio de patrón al espacio de hold (sobrescribe).  
- **`H`**: añade (append) el contenido del espacio de patrón al espacio de hold.  
- **`g`**: copia el contenido del espacio de hold al espacio de patrón (sobrescribe).  
- **`G`**: añade el contenido del espacio de hold al espacio de patrón.  
- **`x`**: intercambia los contenidos del espacio de patrón y el espacio de hold.  

---

### 7.3 Ejemplo con `h` y `g`
Archivo `ejemplo.txt`:

```
uno
dos
tres
```

Comando:
```bash
sed -n '1h;2g;p' ejemplo.txt
```

Explicación:
- `1h`: guarda la primera línea en el espacio de hold.  
- `2g`: en la segunda línea, reemplaza el espacio de patrón con lo que está en el hold (la primera línea).  
- `p`: imprime el resultado.  

Salida:
```
uno
uno
```

---

### 7.4 Ejemplo con `H` y `G`
Archivo `ejemplo.txt`:

```
A
B
C
```

Comando:
```bash
sed -n '1H;2H;3G;p' ejemplo.txt
```

Explicación:
- `1H` y `2H`: añaden las dos primeras líneas al espacio de hold.  
- `3G`: en la tercera línea, añade el contenido del hold al espacio de patrón.  
- `p`: imprime el resultado.  

Salida:
```
A
B
C
A
B
```

---

### 7.5 Ejemplo con `x` (intercambio)
Archivo `ejemplo.txt`:

```
linea1
linea2
```

Comando:
```bash
sed -n '1h;2x;p' ejemplo.txt
```

Explicación:
- `1h`: guarda la primera línea en el espacio de hold.  
- `2x`: en la segunda línea, intercambia el contenido del patrón (linea2) con el hold (linea1).  
- `p`: imprime el resultado.  

Salida:
```
linea1
```

---

### 7.6 Usos prácticos
- **Duplicar líneas**: usando `G` para añadir contenido del hold.  
- **Comparar líneas consecutivas**: guardar una línea y luego recuperarla para contrastar.  
- **Procesar bloques de texto**: acumular contenido en el hold y luego volcarlo en el patrón.  


## 8. Comandos de flujo y control

Además de sustituciones y ediciones básicas, `sed` incluye comandos que permiten **controlar el flujo de procesamiento**, leer múltiples líneas, terminar la ejecución anticipadamente o interactuar con archivos externos.

---

### 8.1 Comando `n` (leer siguiente línea)
- **Función**: avanza a la siguiente línea del archivo, reemplazando el espacio de patrón actual.  
- **Uso típico**: saltar líneas o combinar con otras operaciones.  

#### Ejemplo: borrar cada segunda línea
```bash
sed 'n;d' archivo.txt
```

Explicación:
- `n`: avanza a la siguiente línea.  
- `d`: borra esa línea.  
Resultado: se eliminan las líneas pares.  

---

### 8.2 Comando `N` (añadir siguiente línea)
- **Función**: añade la siguiente línea al espacio de patrón, separada por un salto de línea.  
- **Uso típico**: trabajar con bloques de dos o más líneas.  

#### Ejemplo: unir pares de líneas
Archivo `ejemplo.txt`:
```
uno
dos
tres
cuatro
```

Comando:
```bash
sed 'N;s/\n/ /' ejemplo.txt
```

Salida:
```
uno dos
tres cuatro
```

---

### 8.3 Comando `q` (salida anticipada)
- **Función**: termina la ejecución de `sed` inmediatamente.  
- **Uso típico**: mejorar rendimiento cuando solo se necesita procesar una parte del archivo.  

#### Ejemplo: imprimir solo la primera línea y salir
```bash
sed '1q' archivo.txt
```

---

### 8.4 Comando `r` (leer archivo externo)
- **Función**: inserta el contenido de un archivo externo después de la línea seleccionada.  

#### Ejemplo: insertar contenido de `extra.txt` después de la tercera línea
```bash
sed '3r extra.txt' archivo.txt
```

---

### 8.5 Comando `w` (escribir a archivo externo)
- **Función**: escribe las líneas seleccionadas en un archivo externo.  

#### Ejemplo: guardar todas las líneas que contienen "ERROR" en `errores.txt`
```bash
sed -n '/ERROR/w errores.txt' log.txt
```

---

### 8.6 Ejemplo integrador
Archivo `log.txt`:
```
INFO: inicio
ERROR: fallo
INFO: proceso
ERROR: cierre
```

Comando:
```bash
sed -n '/ERROR/{w errores.txt;p}' log.txt
```

Explicación:
- `/ERROR/`: selecciona las líneas con "ERROR".  
- `w errores.txt`: guarda esas líneas en un archivo externo.  
- `p`: imprime esas líneas en pantalla.  

Salida en terminal:
```
ERROR: fallo
ERROR: cierre
```

Contenido de `errores.txt`:
```
ERROR: fallo
ERROR: cierre
```

## 9. Diferencias entre implementaciones

Aunque `sed` mantiene una sintaxis estándar en la mayoría de sistemas Unix, existen **variaciones entre implementaciones** que pueden afectar scripts portables. Las dos más comunes son **GNU `sed`** (Linux) y **BSD `sed`** (macOS, *BSD).

---

### 9.1 GNU `sed`
- Es la implementación más extendida en sistemas Linux.  
- Incluye extensiones adicionales que no siempre están en otros `sed`.  
- Soporta la opción `-r` (o `-E`) para expresiones regulares extendidas.  
- Permite `-i` con sufijo opcional para copias de respaldo (`-i.bak`).  
- Tiene soporte para **comandos largos** y scripts más complejos.  

#### Ejemplo: uso de `-E` en GNU `sed`
```bash
echo "abc123" | sed -E 's/[a-z]+([0-9]+)/NUM\1/'
```

Salida:
```
NUM123
```

---

### 9.2 BSD `sed`
- Presente en macOS y sistemas BSD.  
- Menos extensiones que GNU `sed`.  
- La opción `-E` existe, pero `-r` no está disponible.  
- El comportamiento de `-i` es diferente: requiere obligatoriamente un argumento (aunque sea vacío).  

#### Ejemplo: edición en el lugar en BSD `sed`
```bash
sed -i '' 's/foo/bar/g' archivo.txt
```

> Aquí `''` indica que no se creará copia de respaldo. En GNU `sed`, simplemente se usaría `-i`.

---

### 9.3 Compatibilidad y portabilidad
- **Scripts portables** deben evitar extensiones específicas de GNU.  
- Usar siempre **BRE** en lugar de ERE si se busca máxima compatibilidad.  
- Tener cuidado con `-i`: su sintaxis varía entre GNU y BSD.  
- Probar scripts en diferentes entornos antes de usarlos en producción.  

---

### 9.4 Ejemplo comparativo
Archivo `ejemplo.txt`:
```
foo
bar
baz
```

#### GNU `sed`:
```bash
sed -i 's/foo/FOO/' ejemplo.txt
```

#### BSD `sed`:
```bash
sed -i '' 's/foo/FOO/' ejemplo.txt
```

Ambos producen:
```
FOO
bar
baz
```

## 10. Buenas prácticas

El uso de `sed` en entornos Unix/Linux requiere atención a detalles que garantizan **seguridad, portabilidad y claridad**. Aquí reunimos las recomendaciones más importantes.

---

### 10.1 Escapado correcto de caracteres especiales
- Los caracteres `/`, `&`, `\`, y los usados en expresiones regulares deben escaparse adecuadamente.  
- Para evitar conflictos con `/`, se puede usar otro delimitador en la sustitución.

#### Ejemplo: usar `|` como delimitador
```bash
sed 's|/usr/bin|/usr/local/bin|' archivo.txt
```

---

### 10.2 Uso seguro de `-i`
- La opción `-i` modifica archivos directamente.  
- Siempre es recomendable crear una copia de respaldo, especialmente en producción.

#### Ejemplo:
```bash
sed -i.bak 's/foo/bar/g' archivo.txt
```

Esto crea `archivo.txt.bak` antes de modificar el archivo original.

---

### 10.3 Integración con pipelines
- `sed` funciona mejor como parte de pipelines, junto con herramientas como `grep`, `awk`, `cut`, `sort`.  
- Esto permite dividir tareas: `grep` para filtrar, `sed` para transformar, `awk` para calcular.

#### Ejemplo:
```bash
grep "ERROR" log.txt | sed 's/ERROR/ALERTA/'
```

---

### 10.4 Scripts legibles
- Guardar instrucciones en archivos `.sed` mejora la legibilidad y mantenimiento.  
- Evitar comandos demasiado compactos que dificulten la comprensión.

#### Ejemplo: archivo `script.sed`
```bash
s/foo/bar/g
/DEBUG/d
```

Ejecutar:
```bash
sed -f script.sed archivo.txt
```

---

### 10.5 Portabilidad
- Evitar extensiones específicas de GNU si se busca compatibilidad con BSD.  
- Probar scripts en diferentes entornos antes de usarlos en producción.  
- Usar BRE en lugar de ERE si no se está seguro de la implementación disponible.  

---

### 10.6 Documentación y pruebas
- Documentar cada comando en scripts largos.  
- Probar primero con redirección a un archivo temporal antes de aplicar `-i`.  

#### Ejemplo:
```bash
sed 's/foo/bar/g' archivo.txt > archivo.tmp
mv archivo.tmp archivo.txt
```

---

### 10.7 Ejemplo integrador
Archivo `config.txt`:
```
# configuración inicial
path=/usr/bin
debug=true
```

Script `ajustes.sed`:
```bash
s|/usr/bin|/usr/local/bin|
/^#/d
s/debug=true/debug=false/
```

Ejecución:
```bash
sed -f ajustes.sed config.txt
```

Salida:
```
path=/usr/local/bin
debug=false
```

## 11. Ejemplos integradores

### 11.1 Limpieza de logs
Supongamos un archivo `system.log`:

```
INFO: proceso iniciado
DEBUG: variable x=10
ERROR: fallo en módulo
DEBUG: variable y=20
INFO: proceso finalizado
```

#### Objetivo: eliminar mensajes `DEBUG` y resaltar `ERROR`.
```bash
sed -e '/DEBUG/d' -e 's/^ERROR:/ALERTA:/' system.log
```

Salida:
```
INFO: proceso iniciado
ALERTA: fallo en módulo
INFO: proceso finalizado
```

---

### 11.2 Reemplazo masivo en archivos de configuración
Archivo `config.cfg`:

```
path=/usr/bin
mode=debug
timeout=30
```

#### Objetivo: cambiar la ruta y desactivar modo debug.
```bash
sed -e 's|/usr/bin|/usr/local/bin|' -e 's/mode=debug/mode=release/' config.cfg
```

Salida:
```
path=/usr/local/bin
mode=release
timeout=30
```

---

### 11.3 Extracción de datos
Archivo `usuarios.txt`:

```
usuario: Juan, rol: admin
usuario: Maria, rol: user
usuario: Pedro, rol: user
```

#### Objetivo: extraer solo los nombres de usuario.
```bash
sed -n 's/usuario: \([A-Za-z]\+\),.*/\1/p' usuarios.txt
```

Salida:
```
Juan
Maria
Pedro
```

---

### 11.4 Procesamiento de bloques
Archivo `documento.txt`:

```
BEGIN
dato1
dato2
END
BEGIN
dato3
dato4
END
```

#### Objetivo: eliminar todo lo que esté entre `BEGIN` y `END`.
```bash
sed '/BEGIN/,/END/d' documento.txt
```

Salida:
```
```
(archivo vacío, porque todos los bloques fueron eliminados).

---

### 11.5 Inserción de contenido externo
Archivo `principal.txt`:

```
Linea A
Linea B
```

Archivo `extra.txt`:

```
Extra 1
Extra 2
```

#### Objetivo: insertar `extra.txt` después de la primera línea.
```bash
sed '1r extra.txt' principal.txt
```

Salida:
```
Linea A
Extra 1
Extra 2
Linea B
```

---

### 11.6 Ejemplo integrador con pipeline
Archivo `log.txt`:

```
INFO: inicio
ERROR: fallo
INFO: proceso
ERROR: cierre
```

#### Objetivo: filtrar solo errores y convertirlos a mayúsculas.
```bash
grep "ERROR" log.txt | sed 's/error/ERROR/i'
```

Salida:
```
ERROR: fallo
ERROR: cierre
```

## 12. Cuaderno de ejercicios

### Nivel básico
1. Sustituir la palabra `unix` por `linux` en un archivo `texto.txt`.  
   ```bash
   sed 's/unix/linux/' texto.txt
   ```
   *Ejercicio*: prueba con y sin el flag `g`.

2. Borrar la primera línea de un archivo `lista.txt`.  
   ```bash
   sed '1d' lista.txt
   ```

3. Imprimir solo las líneas que contienen la palabra `INFO` en `log.txt`.  
   ```bash
   sed -n '/INFO/p' log.txt
   ```

---

### Nivel intermedio
4. Sustituir todas las ocurrencias de `error` por `ERROR` en `log.txt`, ignorando mayúsculas/minúsculas.  
   ```bash
   sed 's/error/ERROR/gi' log.txt
   ```

5. Borrar todas las líneas entre `BEGIN` y `END` en `bloques.txt`.  
   ```bash
   sed '/BEGIN/,/END/d' bloques.txt
   ```

6. Insertar la línea `Encabezado` antes de la primera línea de `datos.txt`.  
   ```bash
   sed '1i\Encabezado' datos.txt
   ```

7. Reemplazar la tercera línea de `archivo.txt` por `Nueva línea`.  
   ```bash
   sed '3c\Nueva línea' archivo.txt
   ```

---

### Nivel avanzado
8. Usar buffers para duplicar la primera línea en la segunda posición.  
   ```bash
   sed '1h;2g' archivo.txt
   ```

9. Unir pares de líneas en un solo renglón, separadas por espacio.  
   ```bash
   sed 'N;s/\n/ /' archivo.txt
   ```

10. Guardar todas las líneas que contienen `ERROR` en un archivo externo `errores.txt`.  
    ```bash
    sed -n '/ERROR/w errores.txt' log.txt
    ```

11. Crear un script `transform.sed` que:
    - Reemplace `foo` por `bar`.  
    - Elimine líneas que comiencen con `#`.  
    - Cambie `modo=debug` por `modo=release`.  

    Ejecutar con:
    ```bash
    sed -f transform.sed config.cfg
    ```

---

### Ejercicio integrador final
Archivo `usuarios.txt`:
```
usuario: Juan, rol: admin
usuario: Maria, rol: user
usuario: Pedro, rol: user
```

**Objetivo**:  
- Extraer solo los nombres.  
- Convertirlos a mayúsculas.  
- Guardarlos en `nombres.txt`.

**Solución paso a paso**:
```bash
sed -n 's/usuario: \([A-Za-z]\+\),.*/\1/p' usuarios.txt | sed 'y/abcdefghijklmnopqrstuvwxyz/ABCDEFGHIJKLMNOPQRSTUVWXYZ/' > nombres.txt
```

Contenido de `nombres.txt`:
```
JUAN
MARIA
PEDRO
```

