# Subguía 11 — Combinación y pipelines  

**Autor: MD. Christian Robles**

**Fecha: 03/01/2026**


## Parte 1 — Conceptos básicos de pipelines

### Introducción
En Unix/Linux, un **pipeline** es la forma de encadenar comandos usando el operador `|`. La salida de un comando se convierte en la entrada del siguiente, creando un flujo de datos continuo. Esta filosofía permite construir mini-flujos que resuelven tareas complejas con herramientas simples.

---

### 1. ¿Qué es un pipeline?
- El operador `|` conecta dos comandos.  
- El primer comando genera datos (stdout).  
- El segundo comando recibe esos datos como entrada (stdin).  
- Se pueden encadenar múltiples comandos en serie.  

**Ejemplo básico:**
```bash
cat archivo.txt | wc -l
```
**Resultado esperado:**
```
20
```
Interpretación:  
- `cat` muestra el contenido del archivo.  
- `wc -l` cuenta las líneas.  
- El resultado indica que el archivo tiene 20 líneas.

---

### 2. Ventajas de los pipelines
- **Eficiencia:** evitan archivos intermedios.  
- **Flexibilidad:** permiten combinar herramientas simples para tareas complejas.  
- **Escalabilidad:** se pueden encadenar tantos comandos como sea necesario.  
- **Filosofía Unix:** cada herramienta hace una cosa bien, y juntas hacen mucho más.

---

### 3. Ejemplo práctico: contar líneas en múltiples archivos
```bash
cat *.txt | wc -l
```
**Resultado esperado:**
```
150
```
Interpretación:  
- `cat *.txt` concatena todos los archivos `.txt`.  
- `wc -l` cuenta el total de líneas.  
- El resultado indica que entre todos los archivos hay 150 líneas.

---

### 4. Ejemplo práctico: ordenar y eliminar duplicados
```bash
sort nombres.txt | uniq
```
**Resultado esperado:**
```
Ana
Carlos
Luis
Pedro
```
Interpretación:  
- `sort` ordena los nombres.  
- `uniq` elimina duplicados consecutivos.  
- El resultado es una lista ordenada y única.

---

### 5. Ejemplo práctico: calcular frecuencia de valores
```bash
cut -d',' -f2 datos.csv | sort | uniq -c
```
**Resultado esperado:**
```
3 Quito
5 Ambato
2 Guayaquil
```
Interpretación:  
- `cut` extrae la segunda columna del CSV.  
- `sort` ordena los valores.  
- `uniq -c` cuenta ocurrencias únicas.  
- El resultado muestra la frecuencia de cada ciudad.

---

### Filosofía unixsauria
- Piensa en cada comando como un **filtro**.  
- Encadena filtros para transformar datos paso a paso.  
- Evita redundancias: usa el comando correcto en el lugar correcto.  
- Los pipelines son la esencia de la potencia Unix: simples piezas que juntas forman sistemas complejos.

---

## Conclusión de la Parte 1
Los pipelines son la base del procesamiento en Unix/Linux.  
- Con `|` se conectan comandos.  
- Se pueden construir mini-flujos para tareas como contar, ordenar o filtrar.  
- La clave está en entender cada comando como un filtro dentro de un flujo.  

---

## Parte 2 — Ejemplos prácticos de mini‑flujos

### Introducción
Ahora que entendemos qué es un pipeline, pasemos a ejemplos concretos. Los mini‑flujos son combinaciones simples de comandos que resuelven tareas cotidianas: contar, ordenar, filtrar o analizar datos. Son la base para construir pipelines más complejos.

---

### 1. Contar líneas en múltiples archivos
```bash
cat *.txt | wc -l
```
**Resultado esperado:**
```
150
```
Interpretación:  
- `cat *.txt` concatena todos los archivos `.txt`.  
- `wc -l` cuenta el total de líneas.  
- El resultado indica que entre todos los archivos hay 150 líneas.

---

### 2. Ordenar lista de nombres y eliminar duplicados
```bash
sort nombres.txt | uniq
```
**Resultado esperado:**
```
Ana
Carlos
Luis
Pedro
```
Interpretación:  
- `sort` ordena los nombres.  
- `uniq` elimina duplicados consecutivos.  
- El resultado es una lista ordenada y única.

---

### 3. Calcular frecuencia de valores en un CSV
```bash
cut -d',' -f2 datos.csv | sort | uniq -c
```
**Resultado esperado:**
```
3 Quito
5 Ambato
2 Guayaquil
```
Interpretación:  
- `cut` extrae la segunda columna del CSV.  
- `sort` ordena los valores.  
- `uniq -c` cuenta ocurrencias únicas.  
- El resultado muestra la frecuencia de cada ciudad.

---

### 4. Buscar patrones en múltiples archivos
```bash
grep "ERROR" *.log | wc -l
```
**Resultado esperado:**
```
25
```
Interpretación:  
- `grep` busca la palabra “ERROR” en todos los archivos `.log`.  
- `wc -l` cuenta cuántas líneas contienen errores.  
- El resultado indica que hay 25 errores registrados.

---

### 5. Extraer y transformar datos en flujo
```bash
cat usuarios.csv | cut -d',' -f1 | tr '[:lower:]' '[:upper:]' | sort
```
**Resultado esperado:**
```
ANA
CARLOS
LUIS
PEDRO
```
Interpretación:  
- `cut` extrae la primera columna (nombres).  
- `tr` convierte a mayúsculas.  
- `sort` ordena alfabéticamente.  

---

### Ejemplo integrado
```bash
grep "genoma" *.txt | cut -d':' -f2 | sort | uniq -c
```
**Resultado esperado:**
```
4 GENOMA HUMANO
2 GENOMA VEGETAL
1 GENOMA VIRAL
```
Interpretación:  
- `grep` busca la palabra “genoma” en todos los `.txt`.  
- `cut` extrae la parte después de los dos puntos.  
- `sort` ordena.  
- `uniq -c` cuenta ocurrencias únicas.  

---

### Filosofía unixsauria
- Cada mini‑flujo es un **bloque de construcción**.  
- La clave está en **pensar modularmente**: un comando para extraer, otro para transformar, otro para contar.  
- Estos ejemplos son la base para pipelines más complejos que veremos en las siguientes partes.  

---

## Conclusión de la Parte 2
Los mini‑flujos muestran cómo encadenar comandos simples para resolver tareas comunes:  
- Contar líneas.  
- Ordenar y eliminar duplicados.  
- Calcular frecuencias.  
- Buscar patrones.  
- Transformar datos en flujo.  

---

## Parte 3 — Combinaciones clásicas

### Introducción
Los pipelines no se limitan a unir dos comandos básicos. En la práctica, los administradores y usuarios avanzados combinan herramientas como `find`, `xargs`, `grep`, `wc` y `sort` para crear flujos más potentes. Estas combinaciones clásicas permiten búsquedas masivas, conteos, filtrado y análisis de datos en múltiples archivos.

---

### 1. Buscar archivos y contar líneas
```bash
find . -name "*.txt" | xargs cat | wc -l
```
**Resultado esperado:**
```
350
```
Interpretación:  
- `find . -name "*.txt"` localiza todos los archivos `.txt` en el directorio actual y subdirectorios.  
- `xargs cat` concatena su contenido.  
- `wc -l` cuenta el total de líneas.  

---

### 2. Buscar patrones en múltiples archivos
```bash
find . -name "*.log" | xargs grep "ERROR" | wc -l
```
**Resultado esperado:**
```
42
```
Interpretación:  
- `find` localiza todos los archivos `.log`.  
- `xargs grep "ERROR"` busca la palabra “ERROR” en cada archivo.  
- `wc -l` cuenta cuántas líneas contienen errores.  

---

### 3. Ordenar y eliminar duplicados en flujo
```bash
cat nombres.txt | sort | uniq
```
**Resultado esperado:**
```
Ana
Carlos
Luis
Pedro
```
Interpretación:  
- `sort` ordena los nombres.  
- `uniq` elimina duplicados consecutivos.  

---

### 4. Calcular frecuencia de valores en CSV
```bash
cut -d',' -f2 datos.csv | sort | uniq -c | sort -nr
```
**Resultado esperado:**
```
5 Ambato
3 Quito
2 Guayaquil
```
Interpretación:  
- `cut` extrae la segunda columna.  
- `sort` ordena.  
- `uniq -c` cuenta ocurrencias.  
- `sort -nr` ordena por frecuencia descendente.  

---

### 5. Buscar y mostrar contexto
```bash
grep -C 2 "ERROR" log.txt
```
**Resultado esperado:**
```
[2026-01-03 19:00] INFO: inicio proceso
[2026-01-03 19:01] ERROR: conexión fallida
[2026-01-03 19:02] INFO: reintento
```
Interpretación:  
- `grep -C 2` muestra 2 líneas de contexto antes y después de cada coincidencia.  

---

### Ejemplo integrado clásico
```bash
find . -name "*.txt" | xargs grep "genoma" | cut -d':' -f2 | sort | uniq -c
```
**Resultado esperado:**
```
4 GENOMA HUMANO
2 GENOMA VEGETAL
1 GENOMA VIRAL
```
Interpretación:  
- `find` localiza todos los `.txt`.  
- `grep` busca “genoma”.  
- `cut` extrae la parte después de los dos puntos.  
- `sort` ordena.  
- `uniq -c` cuenta ocurrencias únicas.  

---

### Filosofía unixsauria
- **`find` + `xargs`** → exploración masiva de archivos.  
- **`grep` + `wc`** → búsqueda y conteo de patrones.  
- **`sort` + `uniq`** → análisis de listas y frecuencias.  
- La clave está en **encadenar herramientas simples** para resolver problemas complejos sin necesidad de software externo.  

---

## Conclusión de la Parte 3
Las combinaciones clásicas muestran cómo pipelines básicos se convierten en herramientas poderosas:  
- Buscar y contar.  
- Filtrar y ordenar.  
- Analizar frecuencias.  
- Mostrar contexto.  

---

## Parte 4 — Avanzado

### Introducción
Aquí entramos en el terreno de los **linuxsaurios**: técnicas avanzadas para controlar, bifurcar, paralelizar y condicionar pipelines. Estas herramientas convierten flujos simples en sistemas robustos y eficientes, capaces de procesar grandes volúmenes de datos y adaptarse a múltiples escenarios.

---

### 1. Redirecciones avanzadas
- `>` → sobrescribe archivo.  
- `>>` → añade al final del archivo.  
- `2>` → redirige errores.  
- `&>` → redirige salida y error juntos.  

**Ejemplo:**
```bash
find . -name "*.csv" | xargs cat > salida.txt 2> errores.log
```
**Resultado esperado:**  
- `salida.txt` contiene el contenido de todos los `.csv`.  
- `errores.log` guarda los errores (por ejemplo, archivos inaccesibles).

---

### 2. Uso de `tee` para bifurcar pipelines
Permite enviar la salida a un archivo y continuar el flujo.  

**Ejemplo:**
```bash
grep "ERROR" log.txt | tee errores.txt | sort | uniq -c
```
**Resultado esperado:**  
- `errores.txt` guarda todas las líneas con “ERROR”.  
- El pipeline cuenta ocurrencias únicas y las muestra en pantalla.

---

### 3. Subshells y agrupación con paréntesis
Ejecuta varios comandos en un subshell y redirige su salida.  

**Ejemplo:**
```bash
(cat archivo1.txt; cat archivo2.txt) | sort | uniq
```
**Resultado esperado:**  
Lista combinada de ambos archivos, ordenada y sin duplicados.

---

### 4. `find -exec` vs `xargs`
- `find -exec` ejecuta directamente sobre cada archivo.  
- `xargs` construye un comando con múltiples argumentos.  

**Ejemplo con `-exec`:**
```bash
find . -name "*.log" -exec grep "ERROR" {} \;
```
**Ejemplo con `xargs`:**
```bash
find . -name "*.log" | xargs grep "ERROR"
```

---

### 5. Uso de `pv` (Pipe Viewer)
Muestra progreso de datos en un pipeline.  

**Ejemplo:**
```bash
cat archivo_grande | pv | gzip > archivo_grande.gz
```
**Resultado esperado:**  
Se comprime el archivo mostrando barra de progreso en tiempo real.

---

### 6. `xargs` avanzado
- `-n` → número de argumentos por ejecución.  
- `-P` → número de procesos en paralelo.  

**Ejemplo:**
```bash
find . -name "*.txt" | xargs -n 1 -P 4 grep "genoma"
```
**Resultado esperado:**  
Busca “genoma” en cada archivo `.txt`, procesando 4 archivos en paralelo.

---

### 7. `parallel` avanzado
Ejecuta comandos en paralelo con control de CPU.  

**Ejemplo:**
```bash
parallel -j4 grep "genoma" ::: *.txt
```
**Resultado esperado:**  
Busca “genoma” en todos los `.txt`, usando 4 procesos simultáneos.

---

### 8. Integración con `awk` y `sed` en pipelines
**Ejemplo:**
```bash
cat datos.csv | cut -d',' -f2 | sort | uniq -c | awk '{print $2, $1}'
```
**Resultado esperado:**
```
Quito 3
Ambato 5
Guayaquil 2
```

---

### 9. Process substitution `<(...)>`
Permite usar la salida de un comando como archivo temporal.  

**Ejemplo:**
```bash
diff <(sort archivo1.txt) <(sort archivo2.txt)
```
**Resultado esperado:**  
Muestra diferencias entre los archivos ya ordenados, sin necesidad de crear archivos intermedios.

---

### 10. Control de flujo con `&&` y `||`
Encadena comandos condicionalmente.  

**Ejemplo:**
```bash
grep "ERROR" log.txt && echo "Se encontraron errores" || echo "Sin errores"
```
**Resultado esperado:**  
- Si se encuentra “ERROR”, imprime “Se encontraron errores”.  
- Si no, imprime “Sin errores”.

---

### Ejemplo integrado avanzado
```bash
find . -name "*.csv" | xargs -n 1 -P 4 grep "Quito" | tee resultados.txt | sort | uniq -c | awk '{print $2, $1}' > reporte_final.txt
```
**Resultado esperado:**
```
Quito 8
```
Interpretación:  
- `find` localiza todos los `.csv`.  
- `xargs` busca “Quito” en paralelo.  
- `tee` guarda resultados y continúa el pipeline.  
- `sort | uniq -c` cuenta ocurrencias.  
- `awk` formatea salida.  
- `reporte_final.txt` contiene el reporte final.

---

### Filosofía unixsauria
- **Redirecciones** → control total de salida y errores.  
- **`tee`** → bifurcación inteligente de flujos.  
- **Subshells** → agrupación modular.  
- **`find -exec` vs `xargs`** → elegir según eficiencia y contexto.  
- **`pv`** → visibilidad en pipelines largos.  
- **Paralelismo (`xargs -P`, `parallel`)** → velocidad y escalabilidad.  
- **Process substitution** → evita archivos temporales.  
- **Control condicional** → lógica de flujo integrada.  

---

## Conclusión de la Parte 4
El repertorio avanzado convierte pipelines en sistemas robustos:  
- Control de salida y errores.  
- Bifurcación y agrupación.  
- Ejecución paralela y eficiente.  
- Integración con herramientas de procesamiento (`awk`, `sed`).  
- Filosofía modular y flexible.  

---

## Sección avanzada global para linuxsaurios

### Introducción
Ya vimos los fundamentos, ejemplos prácticos y técnicas avanzadas por separado. Ahora toca integrar todo en una **visión global**: cómo construir pipelines robustos, eficientes y flexibles que aprovechen redirecciones, bifurcaciones, subshells, paralelismo y control condicional. Esta es la síntesis definitiva para dominar la filosofía Unix aplicada a pipelines.

---

### 1. Redirecciones y control de salida
- **Sobrescribir y añadir:**  
  ```bash
  comando > salida.txt
  comando >> salida.txt
  ```
- **Errores separados:**  
  ```bash
  comando 2> errores.log
  ```
- **Salida y error juntos:**  
  ```bash
  comando &> todo.log
  ```

**Ejemplo integrado:**
```bash
find . -name "*.csv" | xargs cat > datos_combinados.txt 2> errores.log
```
Genera un archivo con todos los CSV y guarda errores aparte.

---

### 2. Bifurcación con `tee`
Permite guardar y continuar el flujo.  
```bash
grep "ERROR" log.txt | tee errores.txt | sort | uniq -c
```
- `errores.txt` guarda coincidencias.  
- El pipeline cuenta ocurrencias únicas.

---

### 3. Subshells y agrupación
Agrupa comandos y redirige su salida.  
```bash
(cat archivo1.txt; cat archivo2.txt) | sort | uniq
```
Combina dos archivos, ordena y elimina duplicados.

---

### 4. `find -exec` vs `xargs`
- `find -exec` ejecuta comando por archivo:  
  ```bash
  find . -name "*.log" -exec grep "ERROR" {} \;
  ```
- `xargs` construye comandos con múltiples argumentos:  
  ```bash
  find . -name "*.log" | xargs grep "ERROR"
  ```

---

### 5. Monitoreo con `pv`
Muestra progreso en pipelines largos.  
```bash
cat archivo_grande | pv | gzip > archivo_grande.gz
```
Visualiza el avance mientras se comprime.

---

### 6. `xargs` avanzado
- `-n` → número de argumentos por ejecución.  
- `-P` → número de procesos en paralelo.  

```bash
find . -name "*.txt" | xargs -n 1 -P 4 grep "genoma"
```
Procesa 4 archivos en paralelo.

---

### 7. `parallel` avanzado
Ejecuta comandos en paralelo con control de CPU.  
```bash
parallel -j4 grep "genoma" ::: *.txt
```
Busca “genoma” en todos los `.txt` usando 4 procesos simultáneos.

---

### 8. Integración con `awk` y `sed`
```bash
cat datos.csv | cut -d',' -f2 | sort | uniq -c | awk '{print $2, $1}'
```
Genera un reporte de frecuencias formateado.

---

### 9. Process substitution `<(...)>`
Permite usar salidas como archivos temporales.  
```bash
diff <(sort archivo1.txt) <(sort archivo2.txt)
```
Compara archivos ya ordenados sin crear archivos intermedios.

---

### 10. Control condicional con `&&` y `||`
```bash
grep "ERROR" log.txt && echo "Se encontraron errores" || echo "Sin errores"
```
Ejecuta mensajes según éxito o fallo.

---

### Ejemplo global integrado
```bash
find . -name "*.csv" | xargs -n 1 -P 4 grep "Quito" | tee resultados.txt | sort | uniq -c | awk '{print $2, $1}' > reporte_final.txt 2> errores.log
```

**Resultado esperado:**
```
Quito 8
Ambato 5
Guayaquil 2
```

Interpretación:  
- `find` localiza todos los CSV.  
- `xargs` busca “Quito” en paralelo.  
- `tee` guarda resultados y continúa el flujo.  
- `sort | uniq -c` cuenta ocurrencias.  
- `awk` formatea salida.  
- `reporte_final.txt` contiene el reporte final.  
- `errores.log` guarda errores de ejecución.

---

### Filosofía unixsauria global
- **Cada comando es un filtro.**  
- **Los pipelines son sistemas modulares:** simples piezas que juntas resuelven problemas complejos.  
- **Redirecciones y bifurcaciones** permiten control total de la salida.  
- **Paralelismo (`xargs -P`, `parallel`)** acelera el procesamiento masivo.  
- **Process substitution y subshells** evitan archivos intermedios y simplifican flujos.  
- **Control condicional** integra lógica en la línea de comandos.  

---

## Conclusión de la Sección avanzada global
La combinación de todos estos elementos convierte pipelines en **herramientas de nivel linuxsaurio**:  
- Robustos, eficientes y flexibles.  
- Capaces de manejar grandes volúmenes de datos.  
- Diseñados bajo la filosofía Unix: *haz una cosa y hazla bien, y combínala con otras*.  


