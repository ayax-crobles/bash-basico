# Subguía 2 — Creación, movimiento y eliminación

**Autor: MD. Christian Robles**

**Fecha: 03/01/2026**

## Introducción
En esta subguía aprenderemos a **crear, copiar, mover y eliminar archivos y directorios**. Estos comandos son esenciales para organizar la información en el sistema de archivos. La práctica debe hacerse con cuidado, ya que algunos comandos pueden eliminar datos de forma irreversible.

## Comandos principales
- `touch` → crea un archivo vacío o actualiza la fecha de modificación.  
- `cp` → copia archivos o directorios.  
- `mv` → mueve o renombra archivos o directorios.  
- `rm` → elimina archivos o directorios.  
- `mkdir` → crea directorios.  
- `rmdir` → elimina directorios vacíos.  

## Ejemplos prácticos

### 1. Crear un archivo vacío
```bash
touch reporte.txt
ls -lh reporte.txt
```
**Resultado esperado:**
```
-rw-r--r-- 1 usuario usuario 0 Jan  3 15:20 reporte.txt
```
Se crea un archivo vacío con tamaño 0 bytes.

---

### 2. Copiar un archivo
```bash
cp reporte.txt copia.txt
ls
```
**Resultado esperado:**
```
copia.txt  reporte.txt
```
Se genera una copia del archivo original.

---

### 3. Mover o renombrar un archivo
```bash
mv copia.txt informe.txt
ls
```
**Resultado esperado:**
```
informe.txt  reporte.txt
```
El archivo `copia.txt` fue renombrado a `informe.txt`.

---

### 4. Eliminar un archivo
```bash
rm informe.txt
ls
```
**Resultado esperado:**
```
reporte.txt
```
El archivo `informe.txt` fue eliminado.

---

### 5. Crear un directorio
```bash
mkdir proyectos
ls -l
```
**Resultado esperado:**
```
drwxr-xr-x 2 usuario usuario 4096 Jan  3 15:25 proyectos
-rw-r--r-- 1 usuario usuario    0 Jan  3 15:20 reporte.txt
```
Se crea un directorio llamado `proyectos`.

---

### 6. Eliminar un directorio vacío
```bash
rmdir proyectos
ls
```
**Resultado esperado:**
```
reporte.txt
```
El directorio `proyectos` fue eliminado porque estaba vacío.

---

### 7. Eliminar un directorio con contenido
```bash
mkdir proyectos
cp reporte.txt proyectos/
rm -r proyectos
ls
```
**Resultado esperado:**
```
reporte.txt
```
El directorio `proyectos` y su contenido fueron eliminados con la opción `-r`.

---

## Reto de práctica
1. Crea un directorio llamado `trabajo`.  
2. Dentro de él, crea tres archivos: `a.txt`, `b.txt`, `c.txt`.  
3. Copia `a.txt` a `a_copia.txt`.  
4. Renombra `b.txt` a `b_final.txt`.  
5. Elimina `c.txt`.  
6. Lista el contenido final del directorio.  

**Resultado esperado:**
```
a.txt  a_copia.txt  b_final.txt
```

## Para estar al nivel de linuxsaurios

### 8. Copias y movimientos avanzados
Los usuarios veteranos suelen aprovechar opciones adicionales de los comandos básicos para trabajar con mayor precisión:  
- **`cp -r`** → copiar directorios completos de forma recursiva.  
- **`cp -i` / `mv -i`** → modo interactivo, pide confirmación antes de sobrescribir.  
- **`cp -u`** → copia solo si el archivo origen es más nuevo que el destino.  
- **`mv -n`** → no sobrescribe archivos existentes.  
- **`mv -t`** → especificar destino primero, útil en scripts.  

**Ejemplo:**
```bash
mkdir datos
touch datos/a.txt
cp -r datos copia_datos
ls
```
**Resultado esperado:**
```
copia_datos  datos
```

---

### 9. Eliminación segura y controlada
Además de `rm`, existen variantes y comandos que permiten mayor control:  
- **`rm -i`** → confirma antes de borrar.  
- **`rm -f`** → fuerza la eliminación sin preguntar.  
- **`rm -r`** → elimina directorios con contenido.  
- **`unlink`** → elimina un único archivo (más bajo nivel que `rm`).  
- **`shred`** → sobrescribe un archivo antes de eliminarlo, usado para seguridad.  

**Ejemplo:**
```bash
touch prueba.txt
rm -i prueba.txt
```
**Resultado esperado:**
```
rm: remove regular empty file 'prueba.txt'? y
```
El archivo se elimina solo si se confirma con `y`.

---

### 10. Creación de directorios avanzada
- **`mkdir -p`** → crea directorios anidados en un solo paso.  
Esto evita errores cuando los directorios intermedios no existen.  

**Ejemplo:**
```bash
mkdir -p proyectos/2026/enero
ls -R proyectos
```
**Resultado esperado:**
```
proyectos:
2026

proyectos/2026:
enero

proyectos/2026/enero:
```

---

### 11. Comandos menos conocidos pero usados por veteranos
- **`install`** → copia archivos y ajusta permisos/propietarios en un solo paso (muy usado en instalación de software).  
- **`mktemp`** → crea archivos o directorios temporales con nombres únicos, muy útil en scripts.  
- **`rename`** → renombra múltiples archivos con patrones (dependiendo de la distribución).  
- **`xargs rm`** o **`find … -exec rm {}`** → eliminación masiva controlada.  

**Ejemplo con `mktemp`:**
```bash
mktemp archivoXXXX.txt
```
**Resultado esperado:**
```
archivoABCD.txt
```
Se crea un archivo con un nombre único generado automáticamente.

---

### 12. Filosofía unixsauria
Los expertos suelen aplicar principios que van más allá de los comandos:  
- Usar **combinaciones**: por ejemplo, `find` + `mv` para mover archivos según criterios.  
- Preferir **herramientas simples y composables** en lugar de comandos monolíticos.  
- Conocer las diferencias entre **GNU coreutils** y variantes BSD/Unix clásicas (las opciones de `cp` y `mv` no siempre son idénticas).  

**Ejemplo combinado:**
```bash
find . -name "*.log" -exec rm {} \;
```
**Resultado esperado:**
Todos los archivos con extensión `.log` son eliminados en la jerarquía actual.

---

### 13. Ejemplo integrado de nivel linuxsaurio
```bash
mkdir -p proyectos/2026/enero
touch proyectos/2026/enero/reporte.log
cp -u proyectos/2026/enero/reporte.log proyectos/2026/enero/reporte_copia.log
mv -i proyectos/2026/enero/reporte_copia.log proyectos/2026/enero/reporte_final.log
ls -lh proyectos/2026/enero
```

**Resultado esperado:**
```
-rw-r--r-- 1 usuario usuario 0 Jan  3 17:40 reporte.log
-rw-r--r-- 1 usuario usuario 0 Jan  3 17:40 reporte_final.log
```

---

### Conclusión
Para estar al nivel de los **linuxsaurios**, además de los comandos básicos, es necesario dominar:  
- Variantes con opciones (`-r`, `-i`, `-u`, `-n`, `-p`).  
- Comandos menos comunes pero muy útiles (`unlink`, `install`, `mktemp`, `rename`).  
- Combinaciones con `find` y `xargs` para operaciones masivas.  
