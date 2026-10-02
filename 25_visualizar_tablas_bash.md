# Guía para visualizar archivos CSV en Bash

**Autor: MD. Christian Robles**

**Fecha: 03/02/2026**


Los archivos CSV suelen verse desordenados en la terminal porque cada campo está separado por comas. Existen varias formas de mostrarlos de manera más ordenada.

---

## 1. Usar `column`

El comando `column` permite alinear columnas de texto.

```bash
column -s, -t archivo.csv | less -S
```

- `-s,` define la coma como separador.  
- `-t` organiza los datos en una tabla alineada.  
- `less -S` evita que las líneas se partan y permite desplazamiento horizontal.

Ejemplo de entrada:

```csv
Nombre,Edad,Ciudad
Ana,23,Quito
Luis,30,Guayaquil
María,27,Cuenca
```

Salida:

```
Nombre  Edad  Ciudad
Ana     23    Quito
Luis    30    Guayaquil
María   27    Cuenca
```

---

## 2. Usar `cat` con `column`

```bash
cat archivo.csv | column -s, -t
```

Esto imprime directamente en pantalla sin necesidad de `less`.

---

## 3. Usar `csvkit`

Instalar con:

```bash
pip install csvkit
```

Visualizar con:

```bash
csvlook archivo.csv
```

Esto muestra el CSV en una tabla con bordes.

---

## 4. Usar `awk`

Permite personalizar el ancho de las columnas:

```bash
awk -F, '{printf "%-15s %-10s %-15s\n", $1, $2, $3}' archivo.csv
```

- `-F,` define la coma como separador.  
- `printf` ajusta el ancho de cada columna.

---

## 5. Usar `mlr` (Miller)

Instalar con:

```bash
sudo apt install miller
```

Visualizar con:

```bash
mlr --icsv --opprint cat archivo.csv
```

- `--icsv` entrada CSV.  
- `--opprint` salida en tabla legible.

---

## 6. Comparación de métodos

| Método   | Instalación | Facilidad | Formato            |
|----------|-------------|-----------|--------------------|
| column   | No          | Alta      | Tabla simple       |
| csvlook  | Sí          | Media     | Tabla con bordes   |
| awk      | No          | Media     | Personalizable     |
| mlr      | Sí          | Alta      | Flexible y potente |

---

## 7. Recomendación rápida

- Sin instalar nada extra:
  ```bash
  column -s, -t archivo.csv | less -S
  ```
- Con herramientas adicionales:
  ```bash
  csvlook archivo.csv
  ```