# Guía: Extraer encabezados de un archivo FASTA

**Autor: MD. Christian Robles**

**Fecha: 23/01/2026**

En un archivo FASTA, los **encabezados** son las líneas que comienzan con `>`.

A continuación se muestran tres formas de obtener solo los encabezados, con explicación de cada comando y cuándo conviene usarlo.

---

## 1. Usando `grep`
```bash
grep '^>' archivo.fasta
```
### ¿Qué hace?
- `^>` → selecciona las líneas que empiezan con `>`.
- `grep` imprime esas líneas directamente.

### ¿Cuándo conviene usarlo?
- Cuando solo quieres **filtrar** encabezados de forma rápida y clara.
- Es la opción más **minimalista** y fiel a la filosofía Unix: hace una sola cosa y la hace bien.
- Ideal para pipelines simples, por ejemplo:
  ```bash
  grep '^>' archivo.fasta | wc -l
  ```
  (contar cuántas secuencias hay).

---

## 2. Usando `sed`
```bash
sed -n '/^>/p' archivo.fasta
```
### ¿Qué hace?
- `-n` → evita imprimir todas las líneas por defecto.
- `/^>/p` → imprime solo las que empiezan con `>`.

### ¿Cuándo conviene usarlo?
- Cuando ya estás usando `sed` para otras transformaciones en el mismo flujo.
- Útil si quieres **editar** o **modificar** los encabezados además de extraerlos.
- Ejemplo: eliminar el símbolo `>` de los encabezados:
  ```bash
  sed -n 's/^>//p' archivo.fasta
  ```

---

## 3. Usando `awk`
```bash
awk '/^>/' archivo.fasta
```
### ¿Qué hace?
- `/^>/` → patrón que detecta encabezados.
- Por defecto, `awk` imprime las líneas que cumplen el patrón.

### ¿Cuándo conviene usarlo?
- Cuando además de extraer encabezados quieres **procesarlos** (ej. dividir por campos, manipular texto).
- `awk` es un mini-lenguaje de procesamiento, más flexible que `grep` o `sed`.
- Ejemplo: imprimir solo el identificador sin el `>`:
  ```bash
  awk '/^>/ {print substr($0,2)}' archivo.fasta
  ```

---

## Ejemplo de entrada
```
>seq1
ATGCATGC
>seq2
TTAGGC
```

## Ejemplo de salida (con cualquiera de los tres métodos)
```
>seq1
>seq2
```

---

#  Conclusión
- **`grep`** → lo más rápido y minimalista, ideal para contar o filtrar encabezados.  
- **`sed`** → útil si además quieres editar los encabezados en el mismo paso.  
- **`awk`** → la opción más poderosa si necesitas manipular o analizar los encabezados.  


