# Archivos comprimidos y empaquetados

**Christian Robles**

## Introducción

En Linux es común trabajar con **archivos comprimidos y empaquetados** para
ahorrar espacio y facilitar la transferencia de datos.
- **Empaquetar** significa agrupar varios archivos en uno solo (ejemplo:
  `tar`).  
- **Comprimir** significa reducir el tamaño de un archivo usando algoritmos
  (ejemplo: `gzip`, `bzip2`, `xz`).  Muchas veces se usan juntos: primero se
  empaqueta con `tar` y luego se comprime con `gzip` o similares.

---

## Comandos principales

- `tar` → empaqueta y desempaqueta archivos.  
- `gzip` / `gunzip` → comprime y descomprime con formato `.gz`.  
- `bzip2` / `bunzip2` → comprime y descomprime con formato `.bz2`.  
- `xz` / `unxz` → comprime y descomprime con formato `.xz`.  

---

## Ejemplos prácticos

### 1. Empaquetar archivos con `tar`

```bash
tar -cvf archivos.tar archivo1.txt archivo2.txt
ls
```

**Resultado esperado:**

```
archivo1.txt  archivo2.txt  archivos.tar
```
Se crea un archivo `archivos.tar` que contiene los dos archivos.

---

### 2. Desempaquetar archivos con `tar`

```bash
tar -xvf archivos.tar
```

**Resultado esperado:**

```
archivo1.txt
archivo2.txt
```

Se extraen los archivos contenidos en `archivos.tar`.

---

### 3. Comprimir un archivo con `gzip`

```bash
gzip archivo1.txt
ls
```

**Resultado esperado:**

```
archivo1.txt.gz  archivo2.txt  archivos.tar
```

El archivo `archivo1.txt` se comprime y se convierte en `archivo1.txt.gz`.

---

### 4. Descomprimir un archivo con `gunzip`

```bash
gunzip archivo1.txt.gz
ls
```

**Resultado esperado:**

```
archivo1.txt  archivo2.txt  archivos.tar
```

El archivo vuelve a su forma original.

---

### 5. Empaquetar y comprimir al mismo tiempo

```bash
tar -czvf proyecto.tar.gz proyecto/
ls
```

**Resultado esperado:**

```
proyecto/  proyecto.tar.gz
```

Se crea un archivo comprimido `proyecto.tar.gz` que contiene todo el directorio
`proyecto`.

---

### 6. Desempaquetar y descomprimir

```bash
tar -xzvf proyecto.tar.gz
```

**Resultado esperado:**

```
proyecto/
proyecto/archivo1.txt
proyecto/archivo2.txt
```

Se extrae el contenido del archivo comprimido.

---

## Reto de práctica

1. Crea un directorio `ejemplo` con tres archivos de texto.  
2. Empaquétalo en `ejemplo.tar`.  
3. Comprime el archivo con `gzip`.  
4. Elimina el directorio original.  
5. Desempaqueta y descomprime el archivo para recuperar los archivos originales.  

**Resultado esperado:**

```
ejemplo/
ejemplo/a.txt
ejemplo/b.txt
ejemplo/c.txt
```

---

### 7. Opciones avanzadas de `tar`

- **`tar -tvf archivo.tar`** → lista el contenido sin extraerlo.  
- **`tar -xvf archivo.tar -C destino/`** → extrae en un directorio específico.  
- **`tar --exclude=*.log -czvf proyecto.tar.gz proyecto/`** → excluye ciertos
  archivos al empaquetar.  

> **Nota:** estas opciones permiten un control más fino sobre empaquetado
  y extracción.

---

### 8. Compresión avanzada

- **`bzip2 archivo`** → comprime con mejor ratio que `gzip`, genera `.bz2`.  
- **`xz archivo`** → compresión aún más eficiente, genera `.xz`.  
- **`tar -cJvf archivo.tar.xz directorio/`** → empaqueta y comprime con `xz`.  

> **Nota:** Se debe eligir el algoritmo según la necesidad: velocidad (`gzip`)
  o tamaño (`xz`).  

---

### 9. Combinaciones con pipes

Se puede comprimir directamente usando pipes:

```bash
tar -cvf - proyecto/ | gzip > proyecto.tar.gz
```

**Resultado esperado:**

Se crea `proyecto.tar.gz` sin necesidad de archivo intermedio.

---

### 10. Recomendaciones

- Usar `tar` como empaquetador universal y combinarlo con diferentes algoritmos
  de compresión.  
- Elegir el algoritmo según contexto: rapidez (`gzip`), eficiencia (`xz`).  
- Usar exclusiones y directorios destino para empaquetados más limpios.  
- Aprovechar pipes para flujos más eficientes en scripts y automatización.  

---

### 11. Ejemplo integrado de nivel avanzado

```bash
tar --exclude=*.tmp -czvf backup.tar.gz proyecto/
tar -tzvf backup.tar.gz
tar -xzvf backup.tar.gz -C restaurado/
```

**Resultado esperado:**

```
proyecto/archivo1.txt
proyecto/archivo2.txt
(restaurado/ contiene los archivos extraídos)
```

Interpretación:

- Se crea un backup excluyendo archivos `.tmp`.  
- Se lista el contenido sin extraerlo.  
- Se extrae en un directorio diferente (`restaurado/`).  

---

## Conclusión

Para tener un buen nivel en archivos comprimidos y empaquetados es necesario
dominar:  

- Opciones avanzadas de `tar` (listar, excluir, extraer en destino).  
- Diferentes algoritmos de compresión (`gzip`, `bzip2`, `xz`).  
- Uso de pipes para empaquetar y comprimir en un solo paso.  
- Filosofía de elegir la herramienta adecuada según velocidad o eficiencia.  

