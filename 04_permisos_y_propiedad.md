# Subguía 4 — Permisos y propiedad

**Autor: MD. Christian Robles**

**Fecha: 03/01/2026**

## Introducción
En sistemas Unix/Linux, cada archivo y directorio tiene un **modelo de permisos y propiedad** que define quién puede leerlo, escribirlo o ejecutarlo. Comprender este modelo es fundamental para la seguridad y la administración del sistema.  

Los permisos se dividen en tres grupos:  
- **Usuario (owner)** → el propietario del archivo.  
- **Grupo (group)** → usuarios que pertenecen al mismo grupo.  
- **Otros (others)** → todos los demás usuarios del sistema.  

Cada grupo puede tener tres tipos de permisos:  
- **r (read)** → leer el contenido.  
- **w (write)** → modificar o eliminar.  
- **x (execute)** → ejecutar el archivo (si es un programa o script).  

---

## Comandos principales
- `chmod` → cambia permisos de un archivo o directorio.  
- `chown` → cambia el propietario de un archivo o directorio.  
- `chgrp` → cambia el grupo asociado a un archivo o directorio.  

---

## Ejemplos prácticos

### 1. Ver permisos de un archivo
```bash
ls -l reporte.txt
```
**Resultado esperado:**
```
-rw-r--r-- 1 usuario usuario 120 Jan  3 18:10 reporte.txt
```
Interpretación:  
- `-rw-r--r--` → archivo regular, propietario con permisos de lectura y escritura, grupo y otros con solo lectura.  
- `usuario usuario` → propietario y grupo.  

---

### 2. Cambiar permisos con notación simbólica
```bash
chmod u+x script.sh
ls -l script.sh
```
**Resultado esperado:**
```
-rwxr--r-- 1 usuario usuario 200 Jan  3 18:12 script.sh
```
Se agregó permiso de ejecución para el propietario (`u+x`).  

---

### 3. Cambiar permisos con notación octal
```bash
chmod 755 script.sh
ls -l script.sh
```
**Resultado esperado:**
```
-rwxr-xr-x 1 usuario usuario 200 Jan  3 18:12 script.sh
```
Interpretación:  
- `7` → propietario con lectura, escritura y ejecución.  
- `5` → grupo con lectura y ejecución.  
- `5` → otros con lectura y ejecución.  

---

### 4. Cambiar propietario
```bash
sudo chown admin reporte.txt
ls -l reporte.txt
```
**Resultado esperado:**
```
-rw-r--r-- 1 admin usuario 120 Jan  3 18:15 reporte.txt
```
El propietario cambió de `usuario` a `admin`.  

---

### 5. Cambiar grupo
```bash
sudo chgrp staff reporte.txt
ls -l reporte.txt
```
**Resultado esperado:**
```
-rw-r--r-- 1 admin staff 120 Jan  3 18:15 reporte.txt
```
El grupo cambió de `usuario` a `staff`.  

---

## Reto de práctica
1. Crea un archivo `ejemplo.sh`.  
2. Asigna permisos de ejecución solo al propietario.  
3. Cambia el propietario a `admin`.  
4. Cambia el grupo a `staff`.  
5. Lista los permisos finales.  

**Resultado esperado:**
```
-rwxr--r-- 1 admin staff 0 Jan  3 18:20 ejemplo.sh
```

---

## Para estar al nivel de linuxsaurios

### 6. Opciones avanzadas de `chmod`
- **`chmod g+w archivo`** → agrega permiso de escritura al grupo.  
- **`chmod o-r archivo`** → quita permiso de lectura a otros.  
- **`chmod -R 755 directorio`** → aplica permisos recursivamente a todos los archivos y subdirectorios.  
- **Nota:** la opción `-R` es muy usada en administración de sistemas para aplicar permisos en estructuras completas.

**Ejemplo:**
```bash
chmod -R 755 proyectos/
ls -l proyectos/
```
**Resultado esperado:**  
Todos los archivos y subdirectorios dentro de `proyectos` tienen permisos `755`.

---

### 7. Opciones avanzadas de `chown` y `chgrp`
- **`chown usuario:grupo archivo`** → cambia propietario y grupo en un solo paso.  
- **`chown -R usuario:grupo directorio`** → cambia propietario y grupo de forma recursiva.  
- **Nota:** esto es común al instalar software o configurar servicios, donde se asignan permisos a usuarios específicos (ejemplo: `www-data` para servidores web).

**Ejemplo:**
```bash
sudo chown -R admin:staff proyectos/
ls -l proyectos/
```
**Resultado esperado:**  
Todos los archivos y subdirectorios dentro de `proyectos` pertenecen al propietario `admin` y al grupo `staff`.

---

### 8. Filosofía unixsauria
- Entender que los permisos son la **primera capa de seguridad** en Unix/Linux.  
- Usar permisos mínimos necesarios (principio de menor privilegio).  
- Conocer diferencias entre sistemas: en BSD, Solaris o GNU/Linux, algunas opciones de `chmod` y `chown` pueden variar.  
- Usar permisos recursivos con cuidado: un error puede abrir o bloquear acceso a todo un sistema.  

---

### 9. Ejemplo integrado de nivel linuxsaurio
```bash
mkdir proyecto_seguro
touch proyecto_seguro/conf.sh
chmod 640 proyecto_seguro/conf.sh
sudo chown admin:staff proyecto_seguro/conf.sh
ls -l proyecto_seguro/conf.sh
```

**Resultado esperado:**
```
-rw-r----- 1 admin staff 0 Jan  3 18:25 proyecto_seguro/conf.sh
```
Interpretación:  
- Propietario (`admin`) puede leer y escribir.  
- Grupo (`staff`) puede leer.  
- Otros no tienen acceso.  
Esto asegura que solo el propietario y el grupo autorizado puedan acceder al archivo.

---

### Conclusión
Para estar al nivel de los **linuxsaurios**, en permisos y propiedad es necesario dominar:  
- Opciones avanzadas de `chmod` (`-R`, notación simbólica y octal).  
- Uso combinado de `chown` y `chgrp` para asignar propietario y grupo.  
- Filosofía de seguridad: aplicar el principio de menor privilegio y usar recursividad con precaución.  

---

### 10. Dar y quitar permisos de ejecución rápidamente
Además de las formas simbólicas (`u+x`, `g-w`, etc.), existe una manera directa de agregar o quitar permisos de ejecución para todos los usuarios (propietario, grupo y otros):  
- **`chmod +x archivo`** → agrega permiso de ejecución para todos.  
- **`chmod -x archivo`** → elimina permiso de ejecución para todos.  
- **Nota:** no requiere `sudo` si eres el propietario del archivo. Solo se necesita `sudo` si el archivo pertenece a otro usuario o grupo.

**Ejemplo:**
```bash
touch script.sh
ls -l script.sh
chmod +x script.sh
ls -l script.sh
chmod -x script.sh
ls -l script.sh
```

**Resultado esperado:**
```
-rw-r--r-- 1 usuario usuario 0 Jan  3 18:30 script.sh
-rwxr-xr-x 1 usuario usuario 0 Jan  3 18:30 script.sh
-rw-r--r-- 1 usuario usuario 0 Jan  3 18:30 script.sh
```

Interpretación:  
- Inicialmente el archivo no tenía permisos de ejecución.  
- Con `chmod +x` se agregó ejecución para todos.  
- Con `chmod -x` se quitó nuevamente el permiso de ejecución.  

