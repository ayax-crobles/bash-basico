# Subguía 1 — Exploración y listado

**Autor: MD. Christian Robles**

**Fecha: 03/01/2026**


## Introducción
El primer paso para dominar Bash y el entorno Unix/Linux es aprender a **explorar el sistema de archivos**. Esto implica conocer dónde estamos, qué archivos existen y cómo listarlos con diferentes opciones. Estos comandos son la base para cualquier tarea posterior.

## Comandos principales
- `pwd` → muestra el directorio actual.  
- `ls` → lista archivos y directorios.  
- `tree` → muestra la estructura de directorios en forma jerárquica (no siempre instalado por defecto).  

## Ejemplos prácticos

### 1. Ver el directorio actual
```bash
pwd
```
**Resultado esperado:**
```
/home/usuario
```
Muestra la ruta completa del directorio en el que estamos trabajando.

---

### 2. Listar archivos simples
```bash
ls
```
**Resultado esperado:**
```
documento.txt  imagen.png  proyecto
```
Lista los archivos y directorios en el directorio actual.

---

### 3. Listar con detalles
```bash
ls -l
```
**Resultado esperado:**
```
-rw-r--r--  1 usuario usuario   120 Jan  3 15:00 documento.txt
-rw-r--r--  1 usuario usuario  2048 Jan  3 15:01 imagen.png
drwxr-xr-x  2 usuario usuario  4096 Jan  3 15:02 proyecto
```
Muestra permisos, propietario, tamaño y fecha de modificación.

---

### 4. Listar con tamaños legibles
```bash
ls -lh
```
**Resultado esperado:**
```
-rw-r--r--  1 usuario usuario  120B Jan  3 15:00 documento.txt
-rw-r--r--  1 usuario usuario  2.0K Jan  3 15:01 imagen.png
drwxr-xr-x  2 usuario usuario  4.0K Jan  3 15:02 proyecto
```
El tamaño se muestra en unidades legibles (B, K, M).

---

### 5. Listar archivos ocultos
```bash
ls -a
```
**Resultado esperado:**
```
.  ..  documento.txt  imagen.png  proyecto  .bashrc
```
Incluye archivos ocultos (los que empiezan con `.`).

---

### 6. Usar comodines
```bash
ls *.txt
```
**Resultado esperado:**
```
documento.txt  notas.txt  reporte.txt
```
Lista solo los archivos que terminan en `.txt`.

---

### 7. Ver estructura jerárquica
```bash
tree
```
**Resultado esperado:**
```
.
|--- documento.txt
|--- imagen.png
|--- proyecto
    |--- datos.csv
    |--- script.sh
```
Muestra la jerarquía completa de archivos y subdirectorios.

---

## Reto de práctica
1. Crea un directorio llamado `exploracion`.  
2. Dentro de él, crea tres archivos: `a.txt`, `b.txt`, `c.log`.  
3. Lista los archivos con `ls -lh`.  
4. Usa `ls *.txt` para mostrar solo los archivos de texto.  
5. Verifica la ruta actual con `pwd`.  

**Resultado esperado:**
```
/home/usuario/exploracion
-rw-r--r-- 1 usuario usuario 0 Jan  3 15:10 a.txt
-rw-r--r-- 1 usuario usuario 0 Jan  3 15:10 b.txt
-rw-r--r-- 1 usuario usuario 0 Jan  3 15:10 c.log
a.txt  b.txt
```

### 8. Moverse entre directorios con `cd`

- **Rutas absolutas**  
  Empiezan desde la raíz `/` y describen el camino completo:  
  ```bash
  cd /home/usuario/proyecto
  ```

- **Rutas relativas**  
  Dependen del directorio actual:  
  - `cd proyecto` → entra al subdirectorio `proyecto`.  
  - `cd ..` → sube un nivel.  
  - `cd ../otro` → sube un nivel y entra a `otro`.  

- **El símbolo `~` (home)**  
  Representa **únicamente el directorio personal del usuario** (`/home/usuario`).  
  - `cd ~` → te lleva siempre a tu carpeta personal.  
  - `cd ~/proyecto` → entra al subdirectorio `proyecto` dentro de tu carpeta personal.  

Importante: `~` **no funciona para el directorio raíz `/`**, solo para la **home del usuario**.  
Por ejemplo, si tienes un archivo dentro de `~/Documentos/proyecto`, el comando correcto sería:  
```bash
cd ~/Documentos/proyecto
```

---

### Mini-reto de práctica con `cd`
1. Muévete a tu carpeta personal con `cd ~`.  
2. Crea un directorio llamado `navegacion`.  
3. Entra en él con `cd navegacion`.  
4. Sube un nivel con `cd ..`.  
5. Vuelve a entrar usando una ruta absoluta (`cd /home/usuario/navegacion`).  

**Resultado esperado:**
```
/home/usuario/navegacion
```
