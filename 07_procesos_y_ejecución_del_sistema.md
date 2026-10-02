# Procesos y gestión del sistema

**Christian Robles**

## Introducción

En Linux, todo lo que se ejecuta en el sistema es un **proceso**. Comprender
cómo iniciar, detener, monitorear y gestionar procesos es fundamental para
administrar el sistema de manera eficiente.  Un proceso es simplemente un
programa en ejecución, identificado por un número único llamado **PID (Process
ID)**.

---

## Comandos principales

- `ps` → muestra procesos en ejecución.  
- `top` → monitoriza procesos en tiempo real.  
- `htop` → versión mejorada de `top` (si está instalada).  
- `kill` → termina procesos por su PID.  
- `jobs` → lista procesos en segundo plano de la sesión actual.  
- `fg` → trae un proceso en segundo plano al primer plano.  
- `bg` → envía un proceso detenido al segundo plano.  

---

## Ejemplos prácticos

### 1. Ver procesos actuales

```bash
ps
```

**Resultado esperado:**

```
  PID TTY          TIME CMD
 1234 pts/0    00:00:00 bash
 1250 pts/0    00:00:00 ps
```

Muestra los procesos asociados a la terminal actual.

---

### 2. Ver procesos con más detalle

```bash
ps aux
```

**Resultado esperado:**

```
USER       PID %CPU %MEM    VSZ   RSS TTY   STAT START   TIME COMMAND
usuario   1234  0.0  0.1  12345  2345 pts/0 Ss   18:40   0:00 bash
usuario   1250  0.0  0.0   5678   678 pts/0 R+   18:41   0:00 ps aux
```

Muestra todos los procesos del sistema con información detallada.

---

### 3. Monitorizar procesos en tiempo real

```bash
top
```

**Resultado esperado (ejemplo simplificado):**

```
top - 18:42:01 up  1:23,  2 users,  load average: 0.01, 0.05, 0.10
Tasks: 120 total,   1 running, 119 sleeping,   0 stopped,   0 zombie
%Cpu(s):  1.0 us,  0.5 sy,  0.0 ni, 98.5 id,  0.0 wa,  0.0 hi,  0.0 si
PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
1234 usuario  20   0   12345   2345   1234 S   0.0  0.1   0:00.01 bash
```

Permite ver consumo de CPU, memoria y procesos activos.

---

### 4. Terminar un proceso

```bash
kill 1234
```

**Resultado esperado:**

El proceso con PID `1234` se termina.
Si no responde, se puede usar `kill -9 1234` para forzar la terminación.

---

### 5. Procesos en segundo plano

```bash
sleep 100 &
jobs
```

**Resultado esperado:**

```
[1]+  Running  sleep 100 &
```

El comando `sleep 100` se ejecuta en segundo plano y aparece en la lista de `jobs`.

---

### 6. Traer un proceso al primer plano

```bash
fg %1
```

**Resultado esperado:**

El proceso `sleep 100` vuelve al primer plano y ocupa la terminal.

---

### 7. Enviar un proceso al segundo plano

```bash
Ctrl+Z
bg %1
```

**Resultado esperado:**

```
[1]+ sleep 100 &
```

El proceso se reanuda en segundo plano.

---

## Reto de práctica

1. Ejecuta `sleep 200 &` para iniciar un proceso en segundo plano.  
2. Usa `jobs` para verificar que está corriendo.  
3. Trae el proceso al primer plano con `fg`.  
4. Suspéndelo con `Ctrl+Z`.  
5. Reanúdalo en segundo plano con `bg`.  
6. Finalmente, termina el proceso con `kill`.  

**Resultado esperado:**

```
[1]+  Running  sleep 200 &
[1]+  Stopped  sleep 200
[1]+  Running  sleep 200 &
[1]+  Terminated  sleep 200
```

---

### 8. Opciones avanzadas de `ps` y `top`

- **`ps -ef`** → muestra todos los procesos con jerarquía.  
- **`ps -u usuario`** → muestra procesos de un usuario específico.  
- **`top -u usuario`** → filtra procesos de un usuario en tiempo real.  
- **Nota:** estas variantes permiten un control más fino sobre qué procesos
  observar.

---

### 9. Señales avanzadas con `kill`

- **`kill -15 PID`** → envía señal de terminación suave (default).  
- **`kill -9 PID`** → fuerza la terminación inmediata.  
- **`kill -STOP PID`** → pausa un proceso.  
- **`kill -CONT PID`** → reanuda un proceso pausado.  

**Ejemplo:**

```bash
kill -STOP 1234
kill -CONT 1234
```

**Resultado esperado:**

El proceso con PID `1234` se pausa y luego se reanuda.

---

### 10. Uso avanzado de `jobs`, `fg` y `bg`

- Se pueden manejar múltiples procesos en segundo plano con índices (`%1`,
  `%2`, etc.).
- **Nota:** esto es útil en sesiones interactivas largas, donde se alterna
  entre varios procesos.

---

### 11. Recomendaciones

- Entender que cada proceso es una entidad controlada por señales.  
- Usar `ps` y `top` para diagnóstico rápido de problemas de rendimiento.  
- Controlar procesos con señales en lugar de terminarlos siempre con `kill -9`.  
- Recordar que procesos mal gestionados pueden convertirse en **zombies**
  o consumir recursos innecesarios.  

---

### 12. Ejemplo integrado de nivel avanzado

```bash
sleep 300 &
ps -ef | grep sleep
kill -STOP <PID>
ps -l -p <PID>
kill -CONT <PID>
kill -9 <PID>
```

**Resultado esperado:**

```
usuario   2345  1234  0 18:55 pts/0  00:00:00 sleep 300
S   usuario   2345 ...
Process resumed
Process terminated
```

Interpretación:

- Se inicia un proceso en segundo plano.  
- Se pausa con `STOP`.  
- Se reanuda con `CONT`.  
- Finalmente se termina con `kill -9`.  

---

## Conclusión

Para tener un buen nivel en procesos y gestión del sistema es necesario
dominar:

- Opciones avanzadas de `ps` y `top` para diagnóstico.  
- Señales de `kill` para pausar, reanudar o terminar procesos.  
- Manejo de múltiples procesos en segundo plano con `jobs`, `fg` y `bg`.  
- Filosofía de control: usar señales con inteligencia para evitar pérdida de
  datos o corrupción.  

