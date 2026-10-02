# Guía práctica de expresiones regulares (regex) y sus variantes

**Autor: MD. Christian Robles**  
**Fecha: 23/01/2026**

---

## 1. Introducción a regex y sus variantes

Las **expresiones regulares (regex)** son un lenguaje de patrones usado para buscar, validar y manipular texto. Son una de las herramientas más poderosas en Unix y en bioinformática, porque permiten detectar motivos genómicos, validar secuencias y filtrar anotaciones con precisión.

El reto es que **no existe una sola sintaxis universal**: cada herramienta implementa su propia variante. Por eso, el mismo metacaracter puede significar cosas distintas según el contexto.

---

### 1.1 ¿Qué son las regex?
- Son **patrones textuales** que describen coincidencias dentro de cadenas.  
- Se usan para:  
  - Buscar motivos (`ATG`, `TATA`).  
  - Validar formatos (IDs, nombres de genes).  
  - Filtrar secuencias o anotaciones.  
  - Sustituir o transformar texto.

---

### 1.2 Diferencia con globbing
- **Globbing (pattern matching)**: usado por el shell para nombres de archivos.  
  - Ejemplo: `*.fasta` → todos los archivos FASTA.  
- **Regex**: usado por herramientas (`grep`, `sed`, `awk`) para contenido dentro de archivos.  
  - Ejemplo: `ATG.*TAA` → secuencias que empiezan con `ATG` y terminan con `TAA`.

Globbing trabaja sobre **nombres de archivos**, regex sobre **contenido textual**.

---

### 1.3 Variantes principales de regex
- **BRE (Basic Regular Expressions)**  
  - Usadas por `grep` sin opciones.  
  - Algunos metacaracteres requieren escape (`\?`, `\+`, `\|`).  

- **ERE (Extended Regular Expressions)**  
  - Usadas por `grep -E`, `egrep`, `awk`.  
  - Metacaracteres como `?`, `+`, `|`, `()` funcionan sin escape.  

- **PCRE (Perl Compatible Regular Expressions)**  
  - Usadas por `grep -P`, lenguajes como Perl, Python, R.  
  - Soportan sintaxis avanzada: lookahead/lookbehind, grupos no capturantes, etc.  

- **Regex en Bash (`[[ =~ ]]`)**  
  - Usadas en condicionales dentro de scripts.  
  - Sintaxis similar a ERE, pero con limitaciones.  

---

### 1.4 Ejemplo aplicado a bioinformática
Archivo `secuencias.fasta`:
```
>seq1
ATGCGTACGTAGCTAGCTAA
>seq2
ATCGNNNNATCG
```

- **BRE**:
  ```bash
  grep "ATG.*TAA" secuencias.fasta
  ```
  Busca secuencias que empiezan con `ATG` y terminan en `TAA`.

- **ERE**:
  ```bash
  grep -E "ATG+(TAA|TAG|TGA)" secuencias.fasta
  ```
  Busca secuencias con inicio `ATG` seguido de un codón de parada.

- **PCRE**:
  ```bash
  grep -P "^ATG(?:[ATGC]{3})+(TAA|TAG|TGA)$" secuencias.fasta
  ```
  Detecta secuencias completas con codón de inicio y parada.

---

### Nota de usuario avanzado
- Regex es un **lenguaje universal de patrones**, pero con dialectos distintos según la herramienta.  
- En bioinformática, dominar estas variantes permite construir pipelines más precisos y reproducibles.  
- Filosofía Unix: piensa en regex como un microscopio textual que revela patrones invisibles en los datos.

---

## 2. Regex básica (BRE – Basic Regular Expressions)

En este capítulo veremos la **sintaxis básica de regex (BRE)**, que es la que usan herramientas como `grep` (sin opciones), `sed` y algunas funciones de `awk`. Es el nivel más antiguo y limitado de regex en Unix, pero sigue siendo muy usado en bioinformática por su simplicidad y compatibilidad.

---

### 2.1 Características principales
- Algunos metacaracteres requieren **escape con `\`** para funcionar.  
- Ejemplo:  
  - `\?` → opcional.  
  - `\+` → una o más repeticiones.  
  - `\|` → alternativa.  
- Los cuantificadores `{n,m}` también requieren escape: `\{n,m\}`.  
- Agrupaciones con `()` requieren escape: `\(` y `\)`.

---

### 2.2 Metacaracteres soportados en BRE
- `.` → cualquier carácter.  
- `^` → inicio de línea.  
- `$` → fin de línea.  
- `*` → cero o más repeticiones del carácter anterior.  
- `\?` → cero o una repetición.  
- `\+` → una o más repeticiones.  
- `\|` → alternativa.  
- `\{n,m\}` → cuantificador entre `n` y `m`.  
- `\(` y `\)` → agrupación.  
- `[ ]` → conjunto de caracteres.  

---

### 2.3 Ejemplos prácticos

Archivo `secuencias.fasta`:
```
>seq1
ATGCGTACGTAGCTAGCTAA
>seq2
ATCGNNNNATCG
>seq3
GGGGGGGGGG
```

- **Detectar secuencias que empiezan con ATG**:
  ```bash
  grep "^ATG" secuencias.fasta
  ```

- **Detectar secuencias que terminan en TAA**:
  ```bash
  grep "TAA$" secuencias.fasta
  ```

- **Detectar secuencias con 6 o más G consecutivas**:
  ```bash
  grep "G\{6,\}" secuencias.fasta
  ```

- **Detectar secuencias con codón de inicio seguido de codón de parada (TAA, TAG, TGA)**:
  ```bash
  grep "ATG.*\(TAA\|TAG\|TGA\)" secuencias.fasta
  ```

---

### 2.4 Ejemplo con `sed`
- **Imprimir solo líneas con codón de parada TAA**:
  ```bash
  sed -n '/TAA/p' secuencias.fasta
  ```

- **Reemplazar NNNN por ---**:
  ```bash
  sed 's/NNNN/---/g' secuencias.fasta
  ```

---

### 2.5 Nota de usuario avanzado
- BRE es más limitado que ERE o PCRE, pero sigue siendo útil porque está disponible en cualquier sistema Unix sin opciones adicionales.  
- En bioinformática, BRE es suficiente para tareas simples: detectar motivos, validar secuencias, filtrar anotaciones.  
- Filosofía Unix: empieza con lo más simple (BRE) y escala a regex más avanzadas solo si lo necesitas.

---

## 3. Regex extendida (ERE – Extended Regular Expressions)

En este capítulo veremos la **sintaxis extendida de regex (ERE)**, que simplifica el uso de metacaracteres y añade nuevas posibilidades. Es la variante usada por `grep -E`, `egrep`, y por defecto en `awk`.  

---

### 3.1 Características principales
- Los metacaracteres **no requieren escapes** (a diferencia de BRE).  
- Ejemplo:  
  - `?` → opcional.  
  - `+` → una o más repeticiones.  
  - `|` → alternativa.  
  - `()` → agrupación.  
- Los cuantificadores `{n,m}` funcionan directamente sin `\`.  

---

### 3.2 Metacaracteres soportados en ERE
- `.` → cualquier carácter.  
- `^` → inicio de línea.  
- `$` → fin de línea.  
- `*` → cero o más repeticiones.  
- `+` → una o más repeticiones.  
- `?` → cero o una repetición.  
- `|` → alternativa.  
- `()` → agrupación.  
- `{n,m}` → cuantificador entre `n` y `m`.  
- `[ ]` → conjunto de caracteres.  

---

### 3.3 Ejemplos prácticos

Archivo `secuencias.fasta`:
```
>seq1
ATGCGTACGTAGCTAGCTAA
>seq2
ATCGNNNNATCG
>seq3
GGGGGGGGGG
```

- **Detectar secuencias que empiezan con ATG y terminan en codón de parada (TAA, TAG, TGA)**:
  ```bash
  grep -E "^ATG.*(TAA|TAG|TGA)$" secuencias.fasta
  ```

- **Detectar secuencias con al menos una N**:
  ```bash
  grep -E "N+" secuencias.fasta
  ```

- **Detectar secuencias con 6 o más G consecutivas**:
  ```bash
  grep -E "G{6,}" secuencias.fasta
  ```

- **Detectar secuencias que contienen color o colour (ejemplo de opcional)**:
  ```bash
  grep -E "colou?r" texto.txt
  ```

---

### 3.4 Ejemplo con `awk`
- **Imprimir secuencias que terminan en TAA**:
  ```bash
  awk '/TAA$/ {print}' secuencias.fasta
  ```

- **Imprimir secuencias con 3 o más N consecutivas**:
  ```bash
  awk '/N{3,}/ {print}' secuencias.fasta
  ```

---

### 3.5 Nota de usuario avanzado
- ERE elimina la necesidad de escapes, lo que hace la sintaxis más limpia y legible.  
- En bioinformática, ERE es ideal para detectar motivos complejos y variantes sin complicar la escritura.  
- Filosofía Unix: usa ERE cuando quieras expresividad sin la complejidad de PCRE.

---

## 4. Regex Perl (PCRE – Perl Compatible Regular Expressions)

En este capítulo exploramos las **regex compatibles con Perl (PCRE)**, que representan la variante más avanzada y expresiva. Se usan en `grep -P`, en lenguajes como **Perl, Python, R, PHP, JavaScript**, y en muchas librerías modernas.  

---

### 4.1 Características principales
- Sintaxis más rica y flexible que BRE y ERE.  
- Soporta **lookahead/lookbehind**, **grupos no capturantes**, **modificadores de contexto**, y más.  
- Permite construir patrones muy complejos para validación y análisis de texto.  
- Ideal para bioinformática cuando se necesitan motivos sofisticados.

---

### 4.2 Metacaracteres y sintaxis avanzada
- `.` → cualquier carácter.  
- `^` → inicio de línea.  
- `$` → fin de línea.  
- `*` → cero o más repeticiones.  
- `+` → una o más repeticiones.  
- `?` → cero o una repetición.  
- `|` → alternativa.  
- `()` → agrupación.  
- `(?: )` → grupo no capturante.  
- `(?= )` → lookahead positivo.  
- `(?! )` → lookahead negativo.  
- `(?<= )` → lookbehind positivo.  
- `(?<! )` → lookbehind negativo.  
- `{n,m}` → cuantificador entre `n` y `m`.  
- `[ ]` → conjunto de caracteres.  

---

### 4.3 Ejemplos prácticos

Archivo `secuencias.fasta`:
```
>seq1
ATGCGTACGTAGCTAGCTAA
>seq2
ATCGNNNNATCG
>seq3
GGGGGGGGGG
```

- **Detectar secuencias completas con inicio ATG y codón de parada (TAA, TAG, TGA)**:
  ```bash
  grep -P "^ATG(?:[ATGC]{3})+(TAA|TAG|TGA)$" secuencias.fasta
  ```

- **Detectar secuencias con al menos 6 G consecutivas**:
  ```bash
  grep -P "G{6,}" secuencias.fasta
  ```

- **Detectar secuencias que contienen N pero no al final (lookahead negativo)**:
  ```bash
  grep -P "N(?!$)" secuencias.fasta
  ```

- **Detectar secuencias que terminan en TAA usando lookbehind**:
  ```bash
  grep -P "(?<=TAA)$" secuencias.fasta
  ```

---

### 4.4 Ejemplo aplicado en Python
```python
import re

seq = "ATGCGTACGTAGCTAGCTAA"

# Detectar secuencia completa con inicio y codón de parada
pattern = re.compile(r"^ATG(?:[ATGC]{3})+(TAA|TAG|TGA)$")
if pattern.match(seq):
    print("Secuencia válida")
```

---

### 4.5 Nota de usuario avanzado
- PCRE es la variante más poderosa y flexible, pero también la más compleja.  
- En bioinformática, permite detectar motivos con condiciones avanzadas (ej. codones de inicio seguidos de repeticiones específicas).  
- Filosofía Unix: usa PCRE cuando necesitas precisión quirúrgica en tus patrones, pero documenta bien tu sintaxis para reproducibilidad.

---

## 5. Regex en Bash (`[[ =~ ]]`)

En este capítulo veremos cómo Bash implementa **regex dentro de condicionales**, usando el operador `=~`. Esto permite validar cadenas directamente en scripts sin necesidad de llamar a `grep` u otras herramientas externas.

---

### 5.1 Características principales
- La sintaxis se usa dentro de `[[ ]]`.  
- El operador `=~` compara una cadena contra una expresión regular.  
- Bash usa una variante similar a **ERE (Extended Regular Expressions)**.  
- No se requieren comillas alrededor de la regex, aunque es recomendable para evitar expansiones inesperadas.  
- Los grupos de captura no se guardan en variables, pero sí en la variable especial `BASH_REMATCH`.

---

### 5.2 Ejemplo básico
```bash
seq="ATGCGTACGTAGCTAGCTAA"

if [[ $seq =~ ^ATG ]]; then
  echo "La secuencia inicia con ATG"
fi
```

---

### 5.3 Uso de grupos y `BASH_REMATCH`
```bash
seq="ATGCGTACGTAGCTAGCTAA"

if [[ $seq =~ (ATG.*(TAA|TAG|TGA))$ ]]; then
  echo "Coincidencia completa: ${BASH_REMATCH[1]}"
  echo "Codón de parada: ${BASH_REMATCH[2]}"
fi
```
- `${BASH_REMATCH[0]}` → coincidencia completa.  
- `${BASH_REMATCH[1]}` → primer grupo capturado.  
- `${BASH_REMATCH[2]}` → segundo grupo capturado.

---

### 5.4 Ejemplos prácticos en bioinformática

Archivo `secuencias.fasta`:
```
>seq1
ATGCGTACGTAGCTAGCTAA
>seq2
ATCGNNNNATCG
```

- **Validar si una secuencia termina en codón de parada**:
  ```bash
  seq="ATGCGTACGTAGCTAGCTAA"
  if [[ $seq =~ (TAA|TAG|TGA)$ ]]; then
    echo "Secuencia válida con codón de parada"
  fi
  ```

- **Detectar si contiene N consecutivas**:
  ```bash
  seq="ATCGNNNNATCG"
  if [[ $seq =~ N{4,} ]]; then
    echo "Secuencia con al menos 4 N consecutivas"
  fi
  ```

---

### 5.5 Limitaciones
- No soporta todas las características de PCRE (ej. lookahead/lookbehind).  
- Los grupos de captura solo están disponibles en `BASH_REMATCH`.  
- Puede ser menos portable que usar `grep` o `awk` en scripts que se ejecuten en otros shells.

---

### Nota de usuario avanzado
- Regex en Bash (`[[ =~ ]]`) es muy útil para validaciones rápidas dentro de scripts.  
- Evita tener que invocar herramientas externas, lo que hace los scripts más eficientes.  
- Filosofía Unix: usa `[[ =~ ]]` para validaciones simples y directas; usa `grep`, `awk` o `sed` para análisis más complejos.

---

## 6. Tablas comparativas simples (BRE vs ERE vs PCRE vs Bash)

En este capítulo vamos a contrastar las diferencias clave entre las variantes de regex que ya vimos: **BRE, ERE, PCRE y Bash (`[[ =~ ]]`)**. La idea es que tengas una referencia rápida y legible, sin tablas demasiado complejas.

---

### 6.1 Diferencias en metacaracteres principales

### `.` (punto)
- **BRE**: cualquier carácter  
- **ERE**: igual  
- **PCRE**: igual  
- **Bash `[[ =~ ]]`**: igual  

---

### `^` y `$`
- **BRE**: inicio / fin de línea  
- **ERE**: igual  
- **PCRE**: igual  
- **Bash**: igual  

---

### `*`
- **BRE**: cero o más repeticiones del carácter anterior  
- **ERE**: igual  
- **PCRE**: igual  
- **Bash**: igual  

---

### `+`
- **BRE**: requiere `\+`  
- **ERE**: una o más repeticiones  
- **PCRE**: una o más repeticiones  
- **Bash**: una o más repeticiones  

---

### `?`
- **BRE**: requiere `\?`  
- **ERE**: cero o una repetición  
- **PCRE**: cero o una repetición  
- **Bash**: cero o una repetición  

---

### `|`
- **BRE**: requiere `\|`  
- **ERE**: alternativa  
- **PCRE**: alternativa  
- **Bash**: alternativa  

---

### `()`
- **BRE**: requiere `\(` y `\)`  
- **ERE**: agrupación  
- **PCRE**: agrupación  
- **Bash**: agrupación  

---

### `{n,m}`
- **BRE**: requiere `\{n,m\}`  
- **ERE**: cuantificador entre n y m  
- **PCRE**: cuantificador entre n y m  
- **Bash**: cuantificador entre n y m  

---

### `(?: )`
- **BRE**: no soportado  
- **ERE**: no soportado  
- **PCRE**: grupo no capturante  
- **Bash**: no soportado  

---

### `(?= )` y `(?! )`
- **BRE**: no soportado  
- **ERE**: no soportado  
- **PCRE**: lookahead positivo / negativo  
- **Bash**: no soportado  

---

### `(?<= )` y `(?<! )`
- **BRE**: no soportado  
- **ERE**: no soportado  
- **PCRE**: lookbehind positivo / negativo  
- **Bash**: no soportado  

---

### 6.2 Ejemplos prácticos comparativos

Archivo `secuencias.fasta`:
```
>seq1
ATGCGTACGTAGCTAGCTAA
>seq2
ATCGNNNNATCG
>seq3
GGGGGGGGGG
```

- **BRE** (requiere escapes):
  ```bash
  grep "ATG.*\(TAA\|TAG\|TGA\)" secuencias.fasta
  ```

- **ERE** (más limpio):
  ```bash
  grep -E "ATG.*(TAA|TAG|TGA)" secuencias.fasta
  ```

- **PCRE** (más avanzado):
  ```bash
  grep -P "^ATG(?:[ATGC]{3})+(TAA|TAG|TGA)$" secuencias.fasta
  ```

- **Bash (`[[ =~ ]]`)**:
  ```bash
  seq="ATGCGTACGTAGCTAGCTAA"
  if [[ $seq =~ ATG.*(TAA|TAG|TGA)$ ]]; then
    echo "Secuencia válida"
  fi
  ```

---

### 6.3 Nota de usuario avanzado
- BRE es más limitado y requiere escapes.  
- ERE simplifica la sintaxis y es más legible.  
- PCRE añade potencia con lookahead/lookbehind y grupos no capturantes.  
- Bash `[[ =~ ]]` es práctico para validaciones rápidas en scripts, pero no soporta todas las funciones de PCRE.  
- Filosofía Unix: elige la variante según el **nivel de complejidad que realmente necesitas**.

---

## 7. Aplicaciones bioinformáticas de regex

En este capítulo veremos cómo las **expresiones regulares** se aplican directamente en bioinformática para resolver problemas prácticos: detección de motivos genómicos, validación de secuencias, filtrado de anotaciones y análisis de datos. Aquí es donde la teoría se convierte en herramienta real.

---

### 7.1 Detección de motivos genómicos
- **Codón de inicio (ATG)**:
  ```bash
  grep -E "^ATG" secuencias.fasta
  ```
  Detecta secuencias que comienzan con `ATG`.

- **Codones de parada (TAA, TAG, TGA)**:
  ```bash
  grep -E "(TAA|TAG|TGA)$" secuencias.fasta
  ```
  Detecta secuencias que terminan en un codón de parada.

---

### 7.2 Validación de secuencias
- **Detectar secuencias con caracteres inválidos (solo A, T, G, C permitidos)**:
  ```bash
  grep -P "[^ATGC]" secuencias.fasta
  ```
  Encuentra secuencias que contienen símbolos distintos de bases válidas.

- **Validar longitud mínima (ej. al menos 20 bases)**:
  ```bash
  grep -P "^[ATGC]{20,}$" secuencias.fasta
  ```

---

### 7.3 Filtrado de anotaciones
Supongamos un archivo GFF con anotaciones genómicas:
```
chr1   source   gene   100   900   .   +   .   ID=gene1
chr1   source   CDS    120   300   .   +   .   ID=cds1
chr1   source   CDS    400   800   .   +   .   ID=cds2
```

- **Extraer solo las líneas de CDS**:
  ```bash
  grep -E "\tCDS\t" anotaciones.gff
  ```

- **Extraer genes con ID específico**:
  ```bash
  grep -E "ID=gene[0-9]+" anotaciones.gff
  ```

---

### 7.4 Análisis de repeticiones
- **Detectar secuencias con al menos 6 G consecutivas**:
  ```bash
  grep -E "G{6,}" secuencias.fasta
  ```

- **Detectar microsatélites (ej. repeticiones de CA)**:
  ```bash
  grep -E "(CA){5,}" secuencias.fasta
  ```

---

### 7.5 Ejemplo aplicado en Bash con `[[ =~ ]]`
```bash
seq="ATGCGTACGTAGCTAGCTAA"

if [[ $seq =~ ^ATG && $seq =~ (TAA|TAG|TGA)$ ]]; then
  echo "Secuencia válida con inicio y codón de parada"
fi
```

---

### Nota de usuario avanzado
- Regex permite **automatizar tareas bioinformáticas** que de otro modo requerirían scripts largos.  
- BRE y ERE son suficientes para motivos simples; PCRE aporta potencia para validaciones complejas.  
- Filosofía Unix: combina regex con pipelines (`grep`, `awk`, `sed`) para construir análisis reproducibles y eficientes.

---

## 8. Errores comunes y buenas prácticas en regex

En este capítulo vamos a revisar las **trampas más frecuentes** al trabajar con expresiones regulares y cómo evitarlas. Esto es clave para que tus scripts sean robustos y reproducibles, especialmente en bioinformática.

---

### 8.1 Errores comunes

- **Confundir globbing con regex**  
  - `*.fasta` en el shell → globbing (expande nombres de archivos).  
  - `.*fasta` en regex → coincide con cualquier cadena que termine en "fasta".  
  - Error típico: usar globbing esperando comportamiento de regex.

- **Olvidar escapes en BRE**  
  - En BRE, `+`, `?`, `|`, `{}` requieren `\`.  
  - Ejemplo incorrecto: `grep "A+" archivo` (no funciona en BRE).  
  - Correcto: `grep "A\+" archivo`.

- **Escapar de más en ERE o PCRE**  
  - En ERE y PCRE, `+`, `?`, `|`, `{}` no necesitan `\`.  
  - Error típico: `grep -E "A\+" archivo` → busca literalmente `A+`.

- **No considerar el contexto de línea**  
  - `^` y `$` aplican a inicio y fin de línea, no de archivo completo.  
  - Error típico: esperar que `$` coincida con el final del archivo.

- **Uso incorrecto de conjuntos `[ ]`**  
  - `[ATGC]` → una sola base.  
  - `(ATGC)` → grupo, no conjunto.  
  - Error típico: confundir conjuntos con grupos.

- **Portabilidad limitada**  
  - `grep -P` (PCRE) no está disponible en todos los sistemas.  
  - Scripts que dependen de PCRE pueden fallar en entornos mínimos.

---

### 8.2 Buenas prácticas

- **Identifica la variante de regex** antes de escribir el patrón (BRE, ERE, PCRE, Bash).  
- **Documenta tus scripts** indicando qué tipo de regex usas.  
- **Prueba tus patrones** con ejemplos pequeños antes de aplicarlos a grandes datasets.  
- **Usa ejemplos claros** para enseñar a otros la diferencia entre globbing y regex.  
- **Prefiere ERE (`grep -E`)** para mayor legibilidad en la mayoría de casos.  
- **Usa PCRE solo cuando sea necesario**, y documenta bien su uso.  
- **Mantén tus regex simples**: patrones demasiado complejos son difíciles de mantener.  
- **Divide problemas grandes en pasos pequeños**: mejor varias regex simples que una regex imposible de leer.

---

### 8.3 Ejemplo aplicado

Archivo `secuencias.fasta`:
```
>seq1
ATGCGTACGTAGCTAGCTAA
>seq2
ATCGNNNNATCG
```

- **Error común**:  
  ```bash
  grep "ATG.*TAA$" secuencias.fasta
  ```
  En BRE, `.*` funciona, pero si usas `+` sin escape, falla.

- **Buena práctica (ERE)**:  
  ```bash
  grep -E "^ATG.*(TAA|TAG|TGA)$" secuencias.fasta
  ```

- **Buena práctica (PCRE)**:  
  ```bash
  grep -P "^ATG(?:[ATGC]{3})+(TAA|TAG|TGA)$" secuencias.fasta
  ```

---

### Nota de usuario avanzado
- La mayoría de errores provienen de **no saber qué variante de regex está activa**.  
- En bioinformática, esto puede significar perder coincidencias críticas o filtrar mal secuencias.  
- Filosofía Unix: mantén tus regex simples, portables y bien documentadas; la claridad es más valiosa que la sofisticación.

---

## 8. Errores comunes y buenas prácticas en regex

En este capítulo vamos a revisar las **trampas más frecuentes** al trabajar con expresiones regulares y cómo evitarlas. Esto es clave para que tus scripts sean robustos y reproducibles, especialmente en bioinformática.

---

### 8.1 Errores comunes

- **Confundir globbing con regex**  
  - `*.fasta` en el shell → globbing (expande nombres de archivos).  
  - `.*fasta` en regex → coincide con cualquier cadena que termine en "fasta".  
  - Error típico: usar globbing esperando comportamiento de regex.

- **Olvidar escapes en BRE**  
  - En BRE, `+`, `?`, `|`, `{}` requieren `\`.  
  - Ejemplo incorrecto: `grep "A+" archivo` (no funciona en BRE).  
  - Correcto: `grep "A\+" archivo`.

- **Escapar de más en ERE o PCRE**  
  - En ERE y PCRE, `+`, `?`, `|`, `{}` no necesitan `\`.  
  - Error típico: `grep -E "A\+" archivo` → busca literalmente `A+`.

- **No considerar el contexto de línea**  
  - `^` y `$` aplican a inicio y fin de línea, no de archivo completo.  
  - Error típico: esperar que `$` coincida con el final del archivo.

- **Uso incorrecto de conjuntos `[ ]`**  
  - `[ATGC]` → una sola base.  
  - `(ATGC)` → grupo, no conjunto.  
  - Error típico: confundir conjuntos con grupos.

- **Portabilidad limitada**  
  - `grep -P` (PCRE) no está disponible en todos los sistemas.  
  - Scripts que dependen de PCRE pueden fallar en entornos mínimos.

---

### 8.2 Buenas prácticas

- **Identifica la variante de regex** antes de escribir el patrón (BRE, ERE, PCRE, Bash).  
- **Documenta tus scripts** indicando qué tipo de regex usas.  
- **Prueba tus patrones** con ejemplos pequeños antes de aplicarlos a grandes datasets.  
- **Usa ejemplos claros** para enseñar a otros la diferencia entre globbing y regex.  
- **Prefiere ERE (`grep -E`)** para mayor legibilidad en la mayoría de casos.  
- **Usa PCRE solo cuando sea necesario**, y documenta bien su uso.  
- **Mantén tus regex simples**: patrones demasiado complejos son difíciles de mantener.  
- **Divide problemas grandes en pasos pequeños**: mejor varias regex simples que una regex imposible de leer.

---

### 8.3 Ejemplo aplicado

Archivo `secuencias.fasta`:
```
>seq1
ATGCGTACGTAGCTAGCTAA
>seq2
ATCGNNNNATCG
```

- **Error común**:  
  ```bash
  grep "ATG.*TAA$" secuencias.fasta
  ```
  En BRE, `.*` funciona, pero si usas `+` sin escape, falla.

- **Buena práctica (ERE)**:  
  ```bash
  grep -E "^ATG.*(TAA|TAG|TGA)$" secuencias.fasta
  ```

- **Buena práctica (PCRE)**:  
  ```bash
  grep -P "^ATG(?:[ATGC]{3})+(TAA|TAG|TGA)$" secuencias.fasta
  ```

---

### Nota de usuario avanzado
- La mayoría de errores provienen de **no saber qué variante de regex está activa**.  
- En bioinformática, esto puede significar perder coincidencias críticas o filtrar mal secuencias.  
- Filosofía Unix: mantén tus regex simples, portables y bien documentadas; la claridad es más valiosa que la sofisticación.

