# Búsqueda y clasificación

**Christian Robles**

## Introducción

Aprenderemos a **buscar y clasificar archivos** dentro del sistema de archivos.
Estos comandos permiten localizar información rápidamente y obtener detalles
sobre los archivos, lo cual es esencial para la administración y organización
en Linux.

## Comandos principales

- `find` → busca archivos y directorios según nombre, extensión, tamaño, fecha,
  permisos, etc.  
- `locate` → busca archivos usando una base de datos indexada (requiere
  actualización con `updatedb`).  
- `file` → muestra el tipo de archivo.  
- `stat` → muestra metadatos detallados de un archivo (tamaño, permisos,
  fechas).  

---

## Ejemplos prácticos

### 1. Buscar por nombre

```bash
find . -name "reporte.txt"
```

**Resultado esperado:**

```
./proyectos/2026/enero/reporte.txt
```

Busca el archivo `reporte.txt` en el directorio actual y subdirectorios.

---

### 2. Buscar por extensión

```bash
find . -name "*.log"
```

**Resultado esperado:**

```
./proyectos/2026/enero/errores.log
./proyectos/2026/febrero/registro.log
```
Lista todos los archivos con extensión `.log`.

---

### 3. Buscar por fecha de modificación

```bash
find . -mtime -1
```

**Resultado esperado:**

```
./proyectos/2026/enero/reporte.txt
```

Muestra archivos modificados en las últimas 24 horas.

---

### 4. Buscar por tamaño

```bash
find . -size +1M
```

**Resultado esperado:**

```
./videos/presentacion.mp4
```

Muestra archivos mayores a 1 MB.

---

### 5. Buscar usando `locate`

```bash
locate reporte.txt
```

**Resultado esperado:**

```
/home/usuario/proyectos/2026/enero/reporte.txt
```

Busca el archivo en todo el sistema usando la base de datos indexada.

---

### 6. Ver tipo de archivo

```bash
file imagen.png
```

**Resultado esperado:**

```
imagen.png: PNG image data, 800 x 600, 8-bit/color RGB
```

Muestra el tipo de archivo y detalles básicos.

---

### 7. Ver metadatos de archivo

```bash
stat reporte.txt
```

**Resultado esperado:**

```
File: reporte.txt
Size: 120        Blocks: 8          IO Block: 4096 regular file
Device: 802h/2050d   Inode: 1234567  Links: 1
Access: (0644/-rw-r--r--)  Uid: (1000/usuario)   Gid: (1000/usuario)
Access: 2026-01-03 17:50:00.000000000
Modify: 2026-01-03 17:40:00.000000000
Change: 2026-01-03 17:40:00.000000000
```

Muestra permisos, propietario, tamaño y fechas de acceso/modificación.

---

## Reto de práctica

1. Crea un directorio `busqueda` con tres archivos: `a.txt`, `b.log`, `c.png`.  
2. Usa `find` para localizar solo los archivos `.log`.  
3. Usa `file` para verificar el tipo de `c.png`.  
4. Usa `stat` para ver los metadatos de `a.txt`.  

**Resultado esperado:**

```
./busqueda/b.log
c.png: PNG image data, 1024 x 768, 8-bit/color RGB
File: a.txt
Size: 0          Blocks: 0          IO Block: 4096 regular empty file
Access: (0644/-rw-r--r--)  Uid: (1000/usuario)   Gid: (1000/usuario)
```

---

### 8. Opciones avanzadas de `find`

- **`find . -type f -perm 644`** → busca archivos con permisos específicos.  
- **`find . -user usuario`** → busca archivos de un propietario.  
- **`find . -exec comando {} \;`** → ejecuta un comando sobre cada archivo encontrado.  
- **Nota:** esta combinación permite automatizar tareas masivas.

**Ejemplo:**

```bash
find . -name "*.log" -exec rm {} \;
```

**Resultado esperado:** 

Todos los archivos `.log` son eliminados en la jerarquía actual.

---

### 9. Uso avanzado de `locate`

- **`locate -i`** → búsqueda sin distinguir mayúsculas/minúsculas.  
- **`locate -r`** → búsqueda usando expresiones regulares.  
- **Nota:** `locate` es mucho más rápido que `find`, pero depende de la base de datos actualizada con `updatedb`.

---

### 10. Combinaciones con `grep`

- ** `find . -type f | grep "txt"` ** → filtra resultados de `find` con `grep`.  

- **Nota:** esto permite búsquedas más flexibles y precisas.

---


### 11. Buenas prácticas

- Usar `find` junto con otros comandos (`mv`, `chmod`, `tar`) para flujos
  completos.  
- Conocer diferencias entre implementaciones: `find` en GNU vs BSD puede variar
  en opciones.  
- Usar `stat` para depuración avanzada (ejemplo: verificar inodos, enlaces,
  bloques).

---

### 12. Ejemplo integrado avanzado

```bash
find ./proyectos -type f -name "*.txt" -exec chmod 644 {} \;
locate -i reporte | grep 2026
stat ./proyectos/2026/enero/reporte.txt
```

**Resultado esperado:**

```
(reporte.txt con permisos ajustados a 644)
.../proyectos/2026/enero/reporte.txt
File: ./proyectos/2026/enero/reporte.txt
Size: 120        Blocks: 8          IO Block: 4096 regular file
Access: (0644/-rw-r--r--)  Uid: (1000/usuario)   Gid: (1000/usuario)
```


