# Flujo de datos y redirecciones

**Christian Robles**

## Introducción

En Linux, los programas trabajan con **flujos de datos**:

- **Entrada estándar (stdin)** → lo que el programa recibe.
- **Salida estándar (stdout)** → lo que el programa muestra.
- **Error estándar (stderr)** → mensajes de error.

El sistema permite **redirigir** estos flujos y **encadenar comandos** para
construir procesos más complejos. Dominar esto es esencial para aprovechar la
filosofía Unix: *“haz una cosa y hazla bien, y combina herramientas simples
para tareas más grandes”*.

---

## Comandos principales

- `>` → redirige salida estándar a un archivo (sobrescribe).  
- `>>` → redirige salida estándar a un archivo (añade al final).  
- `<` → redirige entrada estándar desde un archivo.  
- `2>` → redirige error estándar a un archivo.  
- `|` → conecta la salida de un comando con la entrada de otro (pipe).  
- `tee` → guarda la salida en un archivo y la muestra en pantalla al mismo tiempo.  

---

## Ejemplos prácticos

### 1. Redirigir salida a un archivo

```bash
echo "Hola mundo" > salida.txt
cat salida.txt
```

**Resultado esperado:**

```
Hola mundo
```

El texto se guarda en `salida.txt`. Si el archivo ya existía, se sobrescribe.

---

### 2. Añadir salida a un archivo

```bash
echo "Segunda línea" >> salida.txt
cat salida.txt
```

**Resultado esperado:**

```
Hola mundo
Segunda línea
```
La nueva línea se añade al final del archivo sin borrar lo anterior.

---

### 3. Usar un archivo como entrada

```bash
wc -l < salida.txt
```

**Resultado esperado:**

```
2
```

Cuenta las líneas del archivo `salida.txt` usando redirección de entrada.

---

### 4. Redirigir errores

```bash
ls archivo_inexistente 2> errores.txt
cat errores.txt
```

**Resultado esperado:**

```
ls: cannot access 'archivo_inexistente': No such file or directory
```

El mensaje de error se guarda en `errores.txt`.

---

### 5. Usar pipes para encadenar comandos

```bash
ls -l | grep ".txt"
```

**Resultado esperado:**

```
-rw-r--r-- 1 usuario usuario  20 Jan  3 18:50 salida.txt
```

La salida de `ls -l` se pasa a `grep`, que filtra solo los archivos `.txt`.

---

### 6. Usar `tee` para guardar y mostrar

```bash
echo "Registro importante" | tee registro.txt
cat registro.txt
```

**Resultado esperado:**

```
Registro importante
Registro importante
```

El texto se muestra en pantalla y se guarda en `registro.txt`.

---

## Reto de práctica

1. Crea un archivo `datos.txt` con tres líneas de texto.  
2. Usa `wc -l < datos.txt` para contar las líneas.  
3. Usa `ls -l | grep datos.txt > resultado.txt` para guardar la información del
   archivo en `resultado.txt`.  
4. Usa `echo "Finalizado" | tee log.txt` para registrar y mostrar un mensaje.  

**Resultado esperado:**

```
3
-rw-r--r-- 1 usuario usuario  30 Jan  3 18:55 datos.txt
Finalizado
Finalizado
```

---

### 7. Redirecciones avanzadas

- **`command > archivo 2>&1`** → redirige salida y errores al mismo archivo.  
- **`command &> archivo`** → equivalente en Bash moderno.  
- **`command < entrada.txt > salida.txt`** → usa un archivo como entrada
  y guarda la salida en otro.  
- **Nota:** esto se usa mucho en scripts para registrar tanto resultados como
  errores.

**Ejemplo:**

```bash
ls archivo_inexistente > salida.txt 2>&1
cat salida.txt
```
**Resultado esperado:**

```
ls: cannot access 'archivo_inexistente': No such file or directory
```

---

### 8. Pipes encadenados

Los veteranos suelen encadenar múltiples comandos para procesar datos paso
a paso.

**Ejemplo:**

```bash
cat salida.txt | tr 'a-z' 'A-Z' | sort | uniq
```

**Resultado esperado:**

El contenido de `salida.txt` se convierte a mayúsculas, se ordena y se eliminan
duplicados.

---

### 9. Uso avanzado de `tee`

* **`command | tee archivo | another_command`** → guarda la salida y la pasa
  a otro comando.  
* **Nota:** útil para registrar resultados mientras se siguen procesando.

**Ejemplo:**

```bash
ls -l | tee listado.txt | grep ".txt"
```

**Resultado esperado:**

```
-rw-r--r-- 1 usuario usuario  20 Jan  3 18:50 salida.txt
```

Además, todo el listado queda guardado en `listado.txt`.

---


### 10. Recomendaciones

- Usar redirecciones para **automatizar registros** de salida y errores.  
- Encadenar pipes para construir **mini-programas** sin necesidad de escribir scripts largos.  
- Usar `tee` para **auditar procesos** en tiempo real.  
- Comprender que el control de flujos es la base del principio de modularidad en la línea de comandos

---

### 11. Ejemplo integrado

```bash
grep "ERROR" log.txt | tee errores.txt | wc -l
```

**Resultado esperado:**

```
5
```

Interpretación:

- Se buscan las líneas con “ERROR” en `log.txt`.  
- Se guardan en `errores.txt` y se muestran en pantalla.  
- Se cuenta cuántas líneas contienen errores.  

---

## Conclusión

Es necesario dominar:  

- Redirecciones múltiples (`stdout`, `stderr`, combinadas).  
- Pipes encadenados para procesar datos paso a paso.  
- Uso avanzado de `tee` para guardar y procesar simultáneamente.  
- Filosofía de modularidad: construir soluciones complejas con piezas simples.  

