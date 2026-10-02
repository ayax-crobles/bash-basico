# Subguía 5 — Enlaces y espacio

**Autor: MD. Christian Robles**

**Fecha: 03 /01/2026**


## Introducción
En Unix/Linux, además de los archivos y directorios, existen **enlaces** que permiten acceder a un mismo archivo desde diferentes rutas. También es fundamental conocer cómo medir el **espacio en disco** que ocupan los archivos y directorios.  
Los enlaces y el control de espacio son herramientas clave para la administración avanzada del sistema.

---

## Comandos principales
- `ln` → crea enlaces duros y simbólicos.  
- `du` → muestra el espacio ocupado por archivos y directorios.  
- `df` → muestra el espacio disponible en los sistemas de archivos.  

---

## Ejemplos prácticos

### 1. Crear un enlace duro
```bash
touch original.txt
ln original.txt enlace_duro.txt
ls -li
```
**Resultado esperado:**
```
123456 -rw-r--r-- 2 usuario usuario 0 Jan  3 18:40 original.txt
123456 -rw-r--r-- 2 usuario usuario 0 Jan  3 18:40 enlace_duro.txt
```
Interpretación:  
- Ambos archivos comparten el mismo número de inodo (`123456`).  
- Son referencias al mismo contenido en disco.  

---

### 2. Crear un enlace simbólico
```bash
ln -s original.txt enlace_simbolico.txt
ls -l
```
**Resultado esperado:**
```
-rw-r--r-- 1 usuario usuario 0 Jan  3 18:40 original.txt
-rw-r--r-- 2 usuario usuario 0 Jan  3 18:40 enlace_duro.txt
lrwxrwxrwx 1 usuario usuario 12 Jan  3 18:40 enlace_simbolico.txt -> original.txt
```
Interpretación:  
- `enlace_simbolico.txt` apunta a `original.txt`.  
- Si se elimina `original.txt`, el enlace simbólico queda roto.  

---

### 3. Ver espacio ocupado por un directorio
```bash
du -sh proyectos/
```
**Resultado esperado:**
```
4.0K    proyectos/
```
Muestra el tamaño total del directorio en formato legible (`-h`).  

---

### 4. Ver espacio disponible en el sistema de archivos
```bash
df -h
```
**Resultado esperado:**
```
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        50G   20G   28G  42% /
tmpfs           2.0G  1.0M  2.0G   1% /run
```
Muestra el tamaño total, usado y disponible de cada sistema de archivos.  

---

## Reto de práctica
1. Crea un archivo `datos.txt`.  
2. Genera un enlace duro `datos_hard.txt`.  
3. Genera un enlace simbólico `datos_link.txt`.  
4. Verifica con `ls -li` que el enlace duro comparte el mismo inodo.  
5. Usa `du -sh` para ver el tamaño del directorio actual.  
6. Usa `df -h` para ver el espacio disponible en el sistema.  

**Resultado esperado:**
```
123457 -rw-r--r-- 2 usuario usuario 0 Jan  3 18:45 datos.txt
123457 -rw-r--r-- 2 usuario usuario 0 Jan  3 18:45 datos_hard.txt
123458 lrwxrwxrwx 1 usuario usuario 9 Jan  3 18:45 datos_link.txt -> datos.txt
4.0K    .
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        50G   20G   28G  42% /
```

---

## Para estar al nivel de linuxsaurios

### 6. Enlaces avanzados
- **Enlaces duros**:  
  - Comparten el mismo inodo.  
  - No se pueden crear entre diferentes sistemas de archivos.  
  - Son útiles para mantener múltiples nombres de acceso al mismo contenido.  
- **Enlaces simbólicos**:  
  - Son punteros a otro archivo o directorio.  
  - Pueden apuntar a rutas absolutas o relativas.  
  - Se rompen si el archivo original se elimina o cambia de ubicación.  

**Ejemplo comparativo:**
```bash
ln archivo.txt enlace_duro.txt
ln -s archivo.txt enlace_simbolico.txt
ls -li
```
**Resultado esperado:**
```
123459 -rw-r--r-- 2 usuario usuario 0 Jan  3 18:50 archivo.txt
123459 -rw-r--r-- 2 usuario usuario 0 Jan  3 18:50 enlace_duro.txt
123460 lrwxrwxrwx 1 usuario usuario 11 Jan  3 18:50 enlace_simbolico.txt -> archivo.txt
```

---

### 7. Opciones avanzadas de `du` y `df`
- **`du -ah`** → muestra el tamaño de cada archivo y subdirectorio.  
- **`du --max-depth=1`** → muestra el tamaño de los subdirectorios inmediatos.  
- **`df -T`** → muestra el tipo de sistema de archivos (ext4, xfs, etc.).  
- **`df -i`** → muestra el uso de inodos, útil para sistemas con muchos archivos pequeños.  

**Ejemplo:**
```bash
du --max-depth=1 -h proyectos/
df -i
```
**Resultado esperado:**
```
4.0K    proyectos/enero
8.0K    proyectos/febrero
12K     proyectos/
Filesystem     Inodes  IUsed  IFree IUse% Mounted on
/dev/sda1     3276800 120000 3156800    4% /
```

---

### 8. Filosofía unixsauria
- Usar enlaces simbólicos para crear accesos flexibles (ejemplo: apuntar a configuraciones compartidas).  
- Usar enlaces duros para asegurar redundancia de nombres sin depender de rutas.  
- Controlar espacio no solo en bytes (`df -h`) sino también en inodos (`df -i`).  
- Recordar que en sistemas con millones de archivos pequeños, el límite de inodos puede agotarse antes que el espacio en disco.  

---

### 9. Ejemplo integrado de nivel linuxsaurio
```bash
mkdir sistema
touch sistema/config.txt
ln sistema/config.txt sistema/config_hard.txt
ln -s sistema/config.txt sistema/config_link.txt
du -ah sistema/
df -Th
```

**Resultado esperado:**
```
4.0K    sistema/config.txt
4.0K    sistema/config_hard.txt
0       sistema/config_link.txt
8.0K    sistema/
Filesystem     Type  Size  Used Avail Use% Mounted on
/dev/sda1      ext4   50G   20G   28G  42% /
```

Interpretación:  
- El enlace duro ocupa el mismo espacio que el archivo original porque comparten inodo.  
- El enlace simbólico ocupa espacio mínimo, solo para almacenar la referencia.  
- `df -Th` muestra el tipo de sistema de archivos y el espacio disponible.  

---

### Conclusión
Para estar al nivel de los **linuxsaurios**, en enlaces y espacio es necesario dominar:  
- Diferencias entre enlaces duros y simbólicos.  
- Opciones avanzadas de `du` y `df` para medir espacio en bytes e inodos.  
- Filosofía de uso: enlaces para flexibilidad y redundancia, control de espacio para prevenir problemas en sistemas grandes.  

---

### 10 Enlaces duros y simbólicos

#### 10.1 Teoría: Inodos y nombres de archivo
En Unix/Linux, cada archivo está representado por un **inodo**, que contiene:
- Permisos
- Propietario
- Tamaño
- Ubicación en disco

El nombre del archivo en un directorio es simplemente una **referencia al inodo**.  
Por eso, un mismo inodo puede tener varios nombres asociados.

---

#### 10.2 Enlaces duros (hard links)

**Definición:**
- Un enlace duro es otro nombre que apunta al mismo inodo que el archivo original.
- Comparten exactamente el mismo contenido en disco.
- Si se edita uno, los cambios se reflejan en todos.
- El archivo solo se elimina cuando ya no queda ningún enlace duro apuntando a su inodo.

**Ejemplo:**
```bash
echo "Hola mundo" > original.txt
ln original.txt enlace_duro.txt
ls -li
```

**Resultado esperado:**
```
123456 -rw-r--r-- 2 usuario usuario 11 Jan  3 20:00 enlace_duro.txt
123456 -rw-r--r-- 2 usuario usuario 11 Jan  3 20:00 original.txt
```

Observa que:
- Ambos archivos tienen el mismo número de inodo (`123456`).
- El contador de enlaces es `2` (dos nombres apuntan al mismo inodo).
- Si editas `enlace_duro.txt`, el contenido también cambia en `original.txt`.

---

#### 10.3 Enlaces simbólicos (soft links)

**Definición:**
- Un enlace simbólico es un archivo especial con su propio inodo.
- No guarda el contenido del archivo original, solo la **ruta** hacia él.
- Si el archivo original se borra, el enlace simbólico queda roto.

**Ejemplo:**
```bash
ln -s original.txt enlace_simbolico.txt
ls -li
```

**Resultado esperado:**
```
123457 lrwxrwxrwx 1 usuario usuario 12 Jan  3 20:05 enlace_simbolico.txt -> original.txt
123456 -rw-r--r-- 1 usuario usuario 11 Jan  3 20:00 original.txt
```

Observa que:
- `enlace_simbolico.txt` tiene un inodo distinto (`123457`).
- El contenido del enlace simbólico es la cadena `original.txt`.
- Si borras `original.txt`:
  ```bash
  rm original.txt
  cat enlace_simbolico.txt
  ```
  Resultado esperado:
  ```
  cat: enlace_simbolico.txt: No such file or directory
  ```
  El enlace queda roto.

---

#### 10.4 Comparación práctica

| Característica        | Hard link                          | Soft link                          |
|-----------------------|------------------------------------|------------------------------------|
| Inodo                 | Comparte el mismo inodo            | Tiene su propio inodo              |
| Contenido             | Es el mismo archivo real           | Solo guarda la ruta                |
| Edición               | Cambios visibles en ambos nombres  | Cambios afectan al archivo original |
| Borrado del original  | El archivo sigue existiendo         | El enlace queda roto               |

---

#### 10.5 Mini-reto de práctica
1. Crea un archivo `datos.txt` con el texto `"Ejemplo de enlaces"`.  
2. Haz un enlace duro llamado `datos_duro.txt`.  
3. Haz un enlace simbólico llamado `datos_link.txt`.  
4. Verifica con `ls -li` los inodos y observa las diferencias.  
5. Borra `datos.txt` y prueba abrir ambos enlaces.  

**Resultado esperado:**
- `datos_duro.txt` sigue mostrando el contenido.  
- `datos_link.txt` muestra error porque quedó roto.  

---

#### Conclusión
- Los **enlaces duros** son útiles para tener múltiples nombres que apuntan al mismo archivo real, garantizando que el contenido se conserve mientras exista al menos un enlace.  
- Los **enlaces simbólicos** son útiles como accesos directos o referencias, pero dependen de que el archivo original exista en la ruta indicada.
```
