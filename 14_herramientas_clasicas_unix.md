# Subguía Bonus 14 — Herramientas clásicas de Unix  

**Autor: MD. Christian Robles**

**Fecha: 03/01/2026***


## Parte 1 — Copias y gestión de discos (enriquecida con ejemplos)

---

### Introducción  
Las herramientas clásicas de Unix para gestión de discos y copias son esenciales para tareas de bajo nivel. Los linuxsaurios las usan para clonar discos, crear imágenes, sincronizar buffers y mantener sistemas de archivos íntegros. Aunque algunas son conocidas, otras son joyas ocultas que pocos recuerdan.

---

### 1. `dd` → copia de bajo nivel  
- **Función:** copia datos bit a bit, útil para clonar discos, crear imágenes y realizar conversiones.  
- **Ejemplo básico:**  
  ```bash
  dd if=/dev/sda of=/dev/sdb bs=64K status=progress
  ```
  **Resultado esperado:** clona el disco `/dev/sda` en `/dev/sdb` mostrando progreso.  

- **Ejemplo adicional (crear imagen ISO):**  
  ```bash
  dd if=/dev/cdrom of=imagen.iso bs=4M status=progress
  ```
  **Resultado esperado:** crea una imagen ISO del CD/DVD.  

- **Ejemplo con conversión:**  
  ```bash
  dd if=archivo.bin of=archivo_ascii.txt conv=ascii
  ```
  **Resultado esperado:** convierte un archivo binario a texto ASCII.  

---

### 2. `sync` → fuerza escritura de buffers en disco  
- **Función:** asegura que los datos en memoria se escriban en disco.  
- **Ejemplo básico:**  
  ```bash
  sync
  ```
  **Resultado esperado:** vacía buffers de escritura en disco.  

- **Ejemplo adicional (antes de apagar):**  
  ```bash
  sync; poweroff
  ```
  **Resultado esperado:** sincroniza datos y apaga el sistema de forma segura.  

---

### 3. `mkfs` → creación de sistemas de archivos  
- **Función:** inicializa un sistema de archivos en un dispositivo.  
- **Ejemplo básico:**  
  ```bash
  mkfs.ext4 /dev/sdb1
  ```
  **Resultado esperado:** crea un sistema de archivos ext4 en la partición `/dev/sdb1`.  

- **Ejemplo adicional (FAT32):**  
  ```bash
  mkfs.vfat /dev/sdc1
  ```
  **Resultado esperado:** crea un sistema de archivos FAT32 en `/dev/sdc1`.  

---

### 4. `fsck` → chequeo y reparación de sistemas de archivos  
- **Función:** verifica y repara inconsistencias en sistemas de archivos.  
- **Ejemplo básico:**  
  ```bash
  fsck /dev/sdb1
  ```
  **Resultado esperado:** revisa y repara errores en la partición `/dev/sdb1`.  

- **Ejemplo adicional (modo automático):**  
  ```bash
  fsck -y /dev/sdb1
  ```
  **Resultado esperado:** repara automáticamente todos los errores encontrados.  

---

### Buenas prácticas linuxsaurias  
- **Usa `dd` con cuidado:** puede sobrescribir discos completos sin advertencia.  
- **Siempre sincroniza (`sync`) antes de apagar o desmontar discos críticos.**  
- **Documenta el uso de `mkfs` y `fsck`:** evita errores fatales en producción.  
- **Recuerda:** estas herramientas son poderosas y peligrosas; un linuxsaurio las maneja con respeto.  

---

## Conclusión de la Parte 1  
Las herramientas de copias y gestión de discos (`dd`, `sync`, `mkfs`, `fsck`) son el arsenal clásico para manipular datos a bajo nivel.  
- Permiten clonar, crear imágenes y mantener integridad.  
- Son esenciales en recuperación y administración avanzada.  
- Reflejan la filosofía Unix: simples, directas y potentes.  

---

## Parte 2 — Uso de espacio en disco (enriquecida con ejemplos)

### Introducción  
Medir y entender el uso de espacio en disco es vital para la administración de sistemas. Los linuxsaurios dominan herramientas como `du`, `df` y `stat` para diagnosticar problemas, planificar almacenamiento y verificar detalles de archivos.  

---

### 1. `du` → uso de espacio por directorio  
- **Función:** muestra el tamaño de directorios y archivos.  
- **Ejemplo básico:**  
  ```bash
  du -sh /home/christian
  ```
  **Resultado esperado:**  
  ```
  2.3G    /home/christian
  ```
  - `-s` → resumen total.  
  - `-h` → formato legible (KB, MB, GB).  

- **Ejemplo adicional (listar subdirectorios):**  
  ```bash
  du -h --max-depth=1 /var/log
  ```
  **Resultado esperado:** muestra tamaños de cada subdirectorio dentro de `/var/log`.

---

### 2. `df` → espacio libre y ocupado en sistemas de archivos  
- **Función:** muestra información de sistemas de archivos montados.  
- **Ejemplo básico:**  
  ```bash
  df -h
  ```
  **Resultado esperado:**  
  ```
  Filesystem      Size  Used Avail Use% Mounted on
  /dev/sda1        50G   20G   28G  42% /
  ```
  - `-h` → formato legible.  

- **Ejemplo adicional (solo un sistema de archivos):**  
  ```bash
  df -h /home
  ```
  **Resultado esperado:** muestra uso de espacio en la partición que contiene `/home`.

---

### 3. `stat` → información detallada de un archivo  
- **Función:** muestra metadatos de un archivo.  
- **Ejemplo básico:**  
  ```bash
  stat archivo.txt
  ```
  **Resultado esperado:**  
  ```
  File: archivo.txt
  Size: 1024       Blocks: 8          IO Block: 4096 regular file
  Access: 2026-01-03 20:15:00
  Modify: 2026-01-02 18:00:00
  Change: 2026-01-02 18:05:00
  ```
  - Incluye tamaño, permisos, fechas de acceso/modificación/cambio.  

- **Ejemplo adicional (solo tamaño):**  
  ```bash
  stat -c %s archivo.txt
  ```
  **Resultado esperado:**  
  ```
  1024
  ```
  - `%s` → muestra solo el tamaño en bytes.  

---

### Ejemplo integrado  
```bash
du -sh /var/log && df -h /var && stat /var/log/syslog
```
**Resultado esperado:**  
- `du` muestra tamaño total de `/var/log`.  
- `df` muestra espacio libre en la partición `/var`.  
- `stat` muestra detalles del archivo `syslog`.  

---

### Buenas prácticas linuxsaurias  
- **Usa `du` para localizar directorios pesados.**  
- **Usa `df` para monitorear espacio libre en particiones críticas.**  
- **Usa `stat` para verificar metadatos y tamaños exactos.**  
- **Combina las tres herramientas** para diagnóstico completo de almacenamiento.  

---

## Conclusión de la Parte 2  
Las herramientas `du`, `df` y `stat` son esenciales para entender y controlar el uso de espacio en disco:  
- Diagnóstico rápido de almacenamiento.  
- Verificación detallada de archivos.  
- Planificación y prevención de problemas.  

---

## Parte 3 — Gestión de procesos (enriquecida con ejemplos)

### Introducción  
La gestión de procesos es el corazón del trabajo en sistemas Unix. Los linuxsaurios dominan estas herramientas para listar, controlar, priorizar y monitorear procesos, tanto en foreground como en background. Aquí reforzamos la teoría con ejemplos prácticos.

---

### 1. `ps` → lista procesos  
- **Ejemplo básico:**  
  ```bash
  ps
  ```
  **Resultado esperado:** muestra procesos actuales en la terminal.  

- **Ejemplo adicional (todos los procesos):**  
  ```bash
  ps aux
  ```
  **Resultado esperado:** lista todos los procesos con usuario, PID, uso de CPU y memoria.  

- **Ejemplo filtrado:**  
  ```bash
  ps aux | grep firefox
  ```
  **Resultado esperado:** muestra procesos relacionados con `firefox`.

---

### 2. `kill` → envía señales a procesos  
- **Ejemplo básico (terminar proceso):**  
  ```bash
  kill 1234
  ```
  **Resultado esperado:** termina el proceso con PID 1234.  

- **Ejemplo adicional (forzar terminación):**  
  ```bash
  kill -9 1234
  ```
  **Resultado esperado:** fuerza la terminación del proceso.  

---

### 3. `jobs`, `bg`, `fg` → control de procesos en segundo plano  
- **Ejemplo básico:**  
  ```bash
  sleep 100 &
  jobs
  ```
  **Resultado esperado:** muestra el proceso `sleep` en segundo plano.  

- **Ejemplo adicional (traer a foreground):**  
  ```bash
  fg %1
  ```
  **Resultado esperado:** trae el job número 1 al foreground.  

- **Ejemplo adicional (enviar a background):**  
  ```bash
  bg %1
  ```
  **Resultado esperado:** envía el job número 1 al background.  

---

### 4. `top` y `htop` → monitoreo interactivo  
- **Ejemplo básico:**  
  ```bash
  top
  ```
  **Resultado esperado:** muestra procesos en tiempo real con uso de CPU y memoria.  

- **Ejemplo adicional (htop):**  
  ```bash
  htop
  ```
  **Resultado esperado:** interfaz interactiva con colores y navegación más amigable.  

---

### 5. `nice` y `renice` → control de prioridad  
- **Ejemplo básico (ejecutar con prioridad baja):**  
  ```bash
  nice -n 10 comando
  ```
  **Resultado esperado:** ejecuta `comando` con prioridad reducida.  

- **Ejemplo adicional (cambiar prioridad de proceso):**  
  ```bash
  renice -n 5 -p 1234
  ```
  **Resultado esperado:** cambia la prioridad del proceso con PID 1234 a 5.  

---

### Ejemplo integrado  
```bash
ps aux | grep python
kill -9 4321
sleep 200 &
jobs
fg %1
```
**Resultado esperado:**  
- Lista procesos `python`.  
- Termina proceso con PID 4321.  
- Lanza `sleep` en segundo plano.  
- Muestra jobs activos.  
- Trae `sleep` al foreground.  

---

### Buenas prácticas linuxsaurias  
- **Usa `ps aux` para diagnóstico completo.**  
- **Prefiere `kill` sin `-9` primero:** evita terminaciones bruscas.  
- **Domina `jobs`, `bg`, `fg`:** control fino de procesos interactivos.  
- **Monitorea con `top` o `htop`:** detecta procesos que consumen recursos.  
- **Ajusta prioridades con `nice` y `renice`:** optimiza rendimiento en sistemas compartidos.  

---

## Conclusión de la Parte 3  
Las herramientas de gestión de procesos (`ps`, `kill`, `jobs`, `bg`, `fg`, `top`, `nice`, `renice`) son esenciales para controlar el corazón del sistema:  
- Listar y filtrar procesos.  
- Terminar o ajustar prioridades.  
- Controlar foreground y background.  
- Monitorear recursos en tiempo real.  

---

## Parte 4 — Información del sistema y usuarios (enriquecida con ejemplos)

### Introducción  
Los linuxsaurios saben que para entender el estado de un sistema Unix no basta con mirar procesos o discos: hay que consultar herramientas que revelan tiempo de actividad, usuarios conectados, carga del sistema y mensajes del kernel. Estas utilidades son simples pero poderosas.

---

### 1. `uptime` → tiempo encendido y carga del sistema  
- **Ejemplo básico:**  
  ```bash
  uptime
  ```
  **Resultado esperado:**  
  ```
  21:20:01 up 5 days,  3:42,  2 users,  load average: 0.15, 0.20, 0.25
  ```
  - Muestra hora actual, tiempo encendido, usuarios conectados y carga promedio.  

- **Ejemplo adicional (solo carga):**  
  ```bash
  uptime | awk '{print $NF}'
  ```
  **Resultado esperado:**  
  ```
  0.25
  ```
  - Extrae el último valor de carga promedio.

---

### 2. `who` → usuarios conectados  
- **Ejemplo básico:**  
  ```bash
  who
  ```
  **Resultado esperado:**  
  ```
  christian  tty1   2026-01-03 20:15
  guest      pts/0  2026-01-03 20:30
  ```

- **Ejemplo adicional (mostrar host):**  
  ```bash
  who -u
  ```
  **Resultado esperado:** incluye información de procesos asociados y tiempo de inactividad.

---

### 3. `w` → usuarios y procesos que ejecutan  
- **Ejemplo básico:**  
  ```bash
  w
  ```
  **Resultado esperado:**  
  ```
  21:20:01 up 5 days,  2 users,  load average: 0.15, 0.20, 0.25
  USER     TTY      FROM        LOGIN@   IDLE   JCPU   PCPU WHAT
  christian tty1     :0         20:15    1:00   0.10s  0.05s bash
  guest     pts/0    192.168.1.10 20:30   0.00s  0.20s  0.10s vim notas.txt
  ```

- **Ejemplo adicional (filtrar usuario):**  
  ```bash
  w | grep christian
  ```
  **Resultado esperado:** muestra solo la actividad del usuario `christian`.

---

### 4. `uname` → información del kernel y sistema operativo  
- **Ejemplo básico:**  
  ```bash
  uname -a
  ```
  **Resultado esperado:**  
  ```
  Linux mi-equipo 6.1.0-15-amd64 #1 SMP Debian 6.1.15 x86_64 GNU/Linux
  ```

- **Ejemplo adicional (solo kernel):**  
  ```bash
  uname -r
  ```
  **Resultado esperado:**  
  ```
  6.1.0-15-amd64
  ```

---

### 5. `dmesg` → mensajes del kernel  
- **Ejemplo básico:**  
  ```bash
  dmesg | less
  ```
  **Resultado esperado:** muestra mensajes del kernel, incluyendo arranque y detección de hardware.  

- **Ejemplo adicional (filtrar USB):**  
  ```bash
  dmesg | grep usb
  ```
  **Resultado esperado:** muestra eventos relacionados con dispositivos USB.  

- **Ejemplo adicional (últimos mensajes):**  
  ```bash
  dmesg | tail -20
  ```
  **Resultado esperado:** muestra los últimos 20 mensajes del kernel.  

---

### Ejemplo integrado  
```bash
uptime
who
w
uname -a
dmesg | tail -10
```
**Resultado esperado:**  
- `uptime` → tiempo encendido y carga.  
- `who` → usuarios conectados.  
- `w` → qué están ejecutando los usuarios.  
- `uname -a` → versión del kernel y sistema.  
- `dmesg` → últimos mensajes del kernel.  

---

### Buenas prácticas linuxsaurias  
- **Usa `uptime` y `w` juntos:** obtienes carga y actividad de usuarios.  
- **Consulta `who` para auditoría rápida de sesiones.**  
- **Verifica `uname` para compatibilidad de software.**  
- **Revisa `dmesg` para diagnóstico de hardware y kernel.**  
- **Integra estas herramientas en scripts de monitoreo.**  

---

## Conclusión de la Parte 4  
Las herramientas `uptime`, `who`, `w`, `uname` y `dmesg` son esenciales para conocer el estado del sistema y sus usuarios:  
- Tiempo de actividad y carga.  
- Usuarios conectados y procesos activos.  
- Información del kernel y mensajes críticos.  
Son la base de la observación y diagnóstico en sistemas Unix.  

---

## Parte 5 — Herramientas menos conocidas pero esenciales (enriquecida con ejemplos)

### Introducción  
Además de las utilidades más famosas, existen herramientas clásicas que los linuxsaurios usan para manipular texto, inspeccionar binarios, repetir comandos y monitorear resultados en tiempo real. Son pequeñas joyas que reflejan la filosofía Unix: simples, combinables y poderosas.

---

### 1. `tr` → traducción y eliminación de caracteres  
- **Ejemplo básico (convertir minúsculas a mayúsculas):**  
  ```bash
  echo "linuxsaurio" | tr 'a-z' 'A-Z'
  ```
  **Resultado esperado:**  
  ```
  LINUXSAURIO
  ```

- **Ejemplo adicional (eliminar dígitos):**  
  ```bash
  echo "abc123def456" | tr -d '0-9'
  ```
  **Resultado esperado:**  
  ```
  abcdef
  ```

---

### 2. `cut` → extraer columnas de texto  
- **Ejemplo básico (extraer primera columna separada por coma):**  
  ```bash
  echo "id,nombre,edad" | cut -d',' -f1
  ```
  **Resultado esperado:**  
  ```
  id
  ```

- **Ejemplo adicional (extraer segunda columna):**  
  ```bash
  echo "1,Christian,30" | cut -d',' -f2
  ```
  **Resultado esperado:**  
  ```
  Christian
  ```

---

### 3. `sort` y `uniq` → ordenar y eliminar duplicados  
- **Ejemplo básico (ordenar):**  
  ```bash
  sort nombres.txt
  ```
  **Resultado esperado:** lista ordenada alfabéticamente.  

- **Ejemplo adicional (eliminar duplicados):**  
  ```bash
  sort nombres.txt | uniq
  ```
  **Resultado esperado:** lista ordenada sin duplicados.  

- **Ejemplo combinado (contar ocurrencias):**  
  ```bash
  sort nombres.txt | uniq -c
  ```
  **Resultado esperado:** muestra cada nombre con su número de repeticiones.

---

### 4. `strings` → extraer texto legible de binarios  
- **Ejemplo básico:**  
  ```bash
  strings programa.bin | head
  ```
  **Resultado esperado:** primeras cadenas legibles encontradas en el binario.  

- **Ejemplo adicional (buscar palabra clave):**  
  ```bash
  strings programa.bin | grep "error"
  ```
  **Resultado esperado:** muestra cadenas que contienen “error”.

---

### 5. `od` → volcado en octal/hexadecimal  
- **Ejemplo básico (hexadecimal):**  
  ```bash
  od -x archivo.bin | head
  ```
  **Resultado esperado:** muestra contenido en formato hexadecimal.  

- **Ejemplo adicional (ASCII):**  
  ```bash
  od -c archivo.bin | head
  ```
  **Resultado esperado:** muestra caracteres ASCII del archivo.

---

### 6. `hexdump` → inspección en hexadecimal  
- **Ejemplo básico:**  
  ```bash
  hexdump -C archivo.bin | head
  ```
  **Resultado esperado:** volcado en hexadecimal con representación ASCII al lado.  

---

### 7. `tee` → bifurcar salida  
- **Ejemplo básico:**  
  ```bash
  ls | tee lista.txt
  ```
  **Resultado esperado:** muestra la lista en pantalla y la guarda en `lista.txt`.  

- **Ejemplo adicional (encadenado):**  
  ```bash
  dmesg | tee kernel.log | grep usb
  ```
  **Resultado esperado:** guarda mensajes del kernel en `kernel.log` y filtra los relacionados con USB.

---

### 8. `yes` → salida repetitiva infinita  
- **Ejemplo básico:**  
  ```bash
  yes "linuxsaurio"
  ```
  **Resultado esperado:** imprime “linuxsaurio” infinitamente hasta detener con `Ctrl+C`.  

- **Ejemplo adicional (automatizar confirmaciones):**  
  ```bash
  yes | rm -i *.tmp
  ```
  **Resultado esperado:** responde “y” automáticamente a todas las confirmaciones de borrado.

---

### 9. `watch` → ejecutar comandos periódicamente  
- **Ejemplo básico:**  
  ```bash
  watch -n 5 date
  ```
  **Resultado esperado:** muestra la fecha actual cada 5 segundos.  

- **Ejemplo adicional (monitorear procesos):**  
  ```bash
  watch -n 2 "ps aux | grep python"
  ```
  **Resultado esperado:** actualiza cada 2 segundos mostrando procesos relacionados con `python`.

---

### Ejemplo integrado  
```bash
watch -n 10 "df -h | tee disco.log | grep '/dev/sda'"
```
**Resultado esperado:**  
- Cada 10 segundos ejecuta `df -h`.  
- Guarda resultados en `disco.log`.  
- Filtra la línea de `/dev/sda`.  

---

### Buenas prácticas linuxsaurias  
- **Usa `tr`, `cut`, `sort`, `uniq`** para manipular texto en pipelines.  
- **Usa `strings`, `od`, `hexdump`** para inspeccionar binarios y depurar.  
- **Usa `tee` para bifurcar salida** y mantener logs mientras ves resultados.  
- **Usa `yes` y `watch`** para automatizar confirmaciones y monitorear en tiempo real.  
- **Integra estas herramientas en scripts:** son pequeñas piezas que multiplican poder cuando se combinan.  

---

## Conclusión de la Parte 5  
Las herramientas menos conocidas (`tr`, `cut`, `sort`, `uniq`, `strings`, `od`, `hexdump`, `tee`, `yes`, `watch`) son esenciales para manipulación de texto, inspección de binarios y monitoreo.  
- Reflejan la filosofía Unix: simples, combinables y poderosas.  
- Son parte del arsenal oculto que distingue a los linuxsaurios de usuarios comunes.  

---

## Parte 6 — Filosofía Unix aplicada (enriquecida con ejemplos)

### Introducción  
Los usuarios avanzados no solo usan comandos: **piensan en pipelines** y en la filosofía Unix. Cada herramienta es un bloque simple que, al combinarse, se convierte en un sistema complejo y poderoso. Aquí mostramos cómo aplicar esa filosofía con ejemplos prácticos.

---

### 1. Principios fundamentales  
- **Haz una cosa y hazla bien:** cada comando tiene un propósito claro.  
- **Combina herramientas:** usa pipes (`|`) y redirecciones (`>`, `>>`) para crear flujos.  
- **Texto como interfaz universal:** casi todo se reduce a cadenas que pueden filtrarse, ordenarse y transformarse.  
- **Simplicidad modular:** evita scripts monolíticos; construye piezas pequeñas y reutilizables.  

---

### 2. Ejemplo de modularidad  
```bash
ps aux | grep python | sort -k3 -nr | head -5
```
**Resultado esperado:**  
- Lista procesos relacionados con `python`.  
- Ordena por uso de CPU (`-k3 -nr`).  
- Muestra los 5 más intensivos.  

---

### 3. Ejemplo de precisión con comillas y escapado  
```bash
grep "ERROR\?" logs/*.txt | tee errores.log
```
**Resultado esperado:**  
- Busca literalmente `ERROR?` en todos los `.txt` dentro de `logs/`.  
- Guarda resultados en `errores.log` y los muestra en pantalla.  

---

### 4. Ejemplo de combinación de herramientas clásicas  
```bash
du -sh /var/log | tee espacio.log
strings programa.bin | grep "config" | sort | uniq -c > cadenas.log
```
**Resultado esperado:**  
- `du` mide espacio de `/var/log` y lo guarda en `espacio.log`.  
- `strings` extrae texto de `programa.bin`, filtra “config”, ordena y cuenta ocurrencias, guardando en `cadenas.log`.  

---

### 5. Ejemplo de monitoreo continuo  
```bash
watch -n 5 "df -h | grep '/dev/sda'"
```
**Resultado esperado:**  
- Cada 5 segundos muestra el uso de disco de `/dev/sda`.  
- Permite detectar cambios en tiempo real.  

---

### 6. Ejemplo de filosofía aplicada a scripts  
```bash
#!/bin/bash
# Script: diagnostico.sh
# Propósito: Diagnóstico rápido de sistema

echo "=== Espacio en disco ==="
df -h | grep '/dev/sda'

echo "=== Procesos intensivos ==="
ps aux | sort -k3 -nr | head -5

echo "=== Usuarios conectados ==="
who
```

**Resultado esperado:**  
- Diagnóstico completo en un solo script.  
- Usa herramientas clásicas combinadas.  
- Documentado y modular.  

---

### Buenas prácticas linuxsaurias  
- **Piensa en bloques:** cada comando es una pieza de Lego.  
- **Encadena con pipes:** construye flujos de datos.  
- **Documenta scripts:** claridad para humanos y máquinas.  
- **Usa herramientas clásicas:** son confiables y universales.  
- **Respeta la tradición:** estas prácticas han sobrevivido décadas porque funcionan.  

---

## Conclusión de la Parte 6  
La filosofía *Unix* aplicada convierte comandos simples en sistemas robustos:  
- Modularidad y precisión.  
- Combinación de herramientas clásicas.  
- Scripts claros y sostenibles.  
Es la esencia de Unix: **simplicidad que escala a complejidad**.  


---

## Parte 7 — Ejemplo global integrado (texto + script)


### Introducción  
El nivel usuario avanzado se alcanza cuando todas las herramientas clásicas se integran en un flujo coherente. No se trata de memorizar comandos aislados, sino de **pensar modularmente**: cada utilidad es un bloque que puede encadenarse con otros para diagnóstico, administración y depuración.  

Este ejemplo global muestra cómo unir:  
- Copias y gestión de discos (`dd`, `sync`, `du`, `df`).  
- Procesos (`ps`, `kill`, `jobs`, `fg`).  
- Información del sistema (`uptime`, `who`, `w`, `uname`, `dmesg`).  
- Manipulación de texto/binarios (`tr`, `cut`, `sort`, `uniq`, `strings`, `hexdump`).  
- Monitoreo (`watch`).  

---

### Script global integrado

```bash
#!/bin/bash
# Script: diagnostico_linuxsaurio.sh
# Autor: Christian
# Propósito: Diagnóstico integral estilo linuxsaurio
# Fecha: 2026-01-03
# -----------------------------------------------
# Este script combina herramientas clásicas de Unix
# para diagnóstico de discos, procesos, usuarios,
# manipulación de texto/binarios y monitoreo.

echo "=== COPIAS Y GESTIÓN DE DISCOS ==="
# Crear imagen de disco (ejemplo, requiere permisos root)
# dd if=/dev/sda of=/backup/sda.img bs=64K status=progress

# Sincronizar buffers
sync

# Uso de espacio en /var
du -sh /var
df -h /var

echo
echo "=== GESTIÓN DE PROCESOS ==="
# Listar procesos intensivos
ps aux | sort -k3 -nr | head -5

# Finalizar proceso (ejemplo con PID ficticio)
# kill -9 4321

# Jobs en segundo plano
sleep 5 &
jobs
fg %1

echo
echo "=== INFORMACIÓN DEL SISTEMA Y USUARIOS ==="
uptime
who
w
uname -a
dmesg | tail -10

echo
echo "=== MANIPULACIÓN DE TEXTO Y BINARIOS ==="
# Procesar logs: convertir a mayúsculas, extraer primera columna, ordenar y contar
cat logs.txt | tr 'a-z' 'A-Z' | cut -d' ' -f1 | sort | uniq -c > resumen.log
echo "Resumen generado en resumen.log"

# Inspección de binario
strings programa.bin | grep "error" | tee errores_bin.log
hexdump -C programa.bin | head

echo
echo "=== MONITOREO CONTINUO ==="
# Monitoreo de disco en tiempo real (ejecutar manualmente)
# watch -n 5 "df -h | grep '/dev/sda'"

echo
echo "=== FIN DEL DIAGNÓSTICO ==="
```

---

### Resultado esperado  
- **Discos:** crea imagen, sincroniza buffers, muestra uso de espacio.  
- **Procesos:** lista intensivos, controla jobs, finaliza procesos.  
- **Sistema:** muestra uptime, usuarios, kernel y mensajes recientes.  
- **Texto/binarios:** procesa logs y analiza binarios.  
- **Monitoreo:** vigila espacio en disco en tiempo real.  

---

### Buenas prácticas finales  
- Documenta siempre tus scripts.  
- Usa comentarios para separar secciones.  
- Ejecuta línea por línea en la terminal para pruebas, o todo junto como script.  
- Respeta la filosofía Unix: simplicidad modular que escala a sistemas complejos.  

---

### Ejemplo de salida

```
=== COPIAS Y GESTIÓN DE DISCOS ===
2.1G    /var
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        50G   20G   28G  42% /

=== GESTIÓN DE PROCESOS ===
root       1023  5.0  1.2  123456  9876 ?  R   21:30   0:05 python script.py
christian  2045  3.2  0.8   98765  5432 ?  S   21:29   0:02 firefox
christian  1987  2.5  0.5   87654  4321 ?  S   21:28   0:01 vim notas.txt
root       1500  1.8  0.3   76543  3210 ?  S   21:27   0:01 sshd
christian  2100  1.2  0.2   65432  2100 ?  S   21:26   0:00 bash

[1]+  Running    sleep 5 &
[1]+  Done       sleep 5

=== INFORMACIÓN DEL SISTEMA Y USUARIOS ===
 21:30:01 up 5 days,  2:15,  2 users,  load average: 0.15, 0.20, 0.25
christian  tty1   2026-01-03 20:15
guest      pts/0  2026-01-03 20:30

 21:30:01 up 5 days,  2 users,  load average: 0.15, 0.20, 0.25
USER     TTY      FROM        LOGIN@   IDLE   JCPU   PCPU WHAT
christian tty1     :0         20:15    1:00   0.10s  0.05s bash
guest     pts/0    192.168.1.10 20:30   0.00s  0.20s  0.10s vim notas.txt

Linux mi-equipo 6.1.0-15-amd64 #1 SMP Debian 6.1.15 x86_64 GNU/Linux

[   10.123456] usb 1-1: new high-speed USB device number 4 using xhci_hcd
[   10.123789] usb 1-1: New USB device found, idVendor=1234, idProduct=5678
[   10.124000] EXT4-fs (sda1): mounted filesystem with ordered data mode
...

=== MANIPULACIÓN DE TEXTO Y BINARIOS ===
Resumen generado en resumen.log

error: configuration failed
error: missing file
error: invalid parameter

00000000  7f 45 4c 46 02 01 01 00  00 00 00 00 00 00 00 00  |.ELF............|
00000010  02 00 3e 00 01 00 00 00  78 56 34 12 00 00 00 00  |..>.....xV4.....|
00000020  00 00 00 00 00 00 00 00  40 00 00 00 00 00 00 00  |........@.......|

=== MONITOREO CONTINUO ===
# (este bloque no se ejecuta automáticamente, se deja para correr manualmente con watch)

=== FIN DEL DIAGNÓSTICO ===
```

---

### Interpretación rápida
- **Discos:** muestra tamaño de `/var` y espacio libre en `/dev/sda1`.  
- **Procesos:** lista los 5 más intensivos en CPU.  
- **Usuarios:** indica quién está conectado y qué ejecuta.  
- **Kernel:** últimos mensajes de `dmesg`.  
- **Texto/binarios:** genera `resumen.log` con conteo de ocurrencias y `errores_bin.log` con cadenas de error.  
- **Monitoreo:** preparado para ejecutarse con `watch`.  

