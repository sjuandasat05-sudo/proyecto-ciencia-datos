# Informe de calidad de datos

**Proyecto:** Accidentalidad vial en Medellín 2015-2019
**Autor:** Juan David
**Notebook:** `notebooks/data_preparation.ipynb`

Este informe explica los resultados de los códigos ejecutados para preparar, limpiar y estructurar los datos del proyecto.

---

## 1. Carga de los datos crudos

| Archivo | Filas | Columnas |
|---|---|---|
| `incidentes_viales.csv` | 270,765 | 18 |
| `Mede_Victimas_inci.csv` | 235,843 | 19 |

Al leer el archivo de víctimas completo se produjo un `MemoryError`: el archivo tiene 1,048,317 filas, pero 812,474 están totalmente vacías. Se solucionó leyéndolo por partes (`chunksize=100_000`) y eliminando las filas vacías de cada parte con `dropna(how="all")`. Así quedaron en memoria solo las 235,843 víctimas reales.

---

## 2. Duplicados

### 2.1 Duplicados exactos

```
duplicados = incidentes_raw[incidentes_raw.duplicated(keep=False)]
```

- Se encontraron **14 filas** involucradas: 7 accidentes duplicados, cada uno junto a su original.
- Con `drop_duplicates()` incidentes pasó de 270,765 a **270,758 filas**.
- En víctimas se eliminaron **197 duplicados** (235,843 → 235,646).

En víctimas no existe un identificador de la persona, así que dos víctimas reales del mismo accidente con el mismo sexo, edad y condición se verían como duplicadas. Son muy pocos casos (0.08 %) y se eliminaron, pero queda como limitación.

### 2.2 ¿Es único el radicado?

```
print(incidentes.shape, incidentes['NRO_RADICADO'].is_unique)
→ (270758, 18) False
```

A diferencia de los pedidos de Instacart, el número de radicado **no es único**:

```
23
26 de 46
```

- 23 radicados aparecen más de una vez (46 filas).
- 26 de esas filas tienen víctimas asociadas, así que no se pueden borrar sin revisarlas.

Al revisarlas aparecieron dos situaciones:

| Situación | Casos | Decisión |
|---|---|---|
| Mismo radicado y mismo expediente, con distinta fecha | 16 | Es el mismo accidente registrado dos veces: se eliminó una copia. |
| Mismo radicado pero distinto expediente | 7 | Son números reutilizados en años distintos para accidentes diferentes: se conservan. |

**Conclusión:** el identificador real de un accidente es **radicado + expediente**. Después de este paso, incidentes quedó con **270,742 filas**.

---

## 3. Tipos de dato

Se convirtieron las columnas numéricas a tipos más pequeños con `astype`:

| Columna | Antes | Después |
|---|---|---|
| `AÑO` | texto | `int16` |
| `MES` | `int64` | `int8` |
| `X`, `Y` | `float64` | `float32` |
| `Año` (víctimas) | texto | `int16` |
| `Num_dia` | texto | `int8` |
| `Hora` | texto | `Int8` |
| `Edad` | texto | `Int16` |
| `Latitud`, `Longitud` | texto con coma decimal | `float32` |

```
Antes: 96.5 MB
Después: 89.6 MB
```

Hallazgos:

- `AÑO` no se podía convertir directamente porque **26,760 registros de 2019** tenían el valor `2019\r`. Primero se quitó ese texto con `.str.replace()`.
- `Hora` y `Edad` contienen `Sin Inf`, así que se convirtieron con `pd.to_numeric(errors='coerce')`, que deja esos valores como `NaN`. Se usó `Int8`/`Int16` (con mayúscula) porque estos tipos aceptan valores nulos.
- `Latitud` y `Longitud` usaban coma decimal (`6,2669`) y hubo que cambiarla por punto.
- La memoria bajó poco (7 %) porque la mayor parte la ocupan columnas de texto.

---

## 4. Valores nulos

Antes de decidir qué hacer con cada nulo se comprobó su causa.

### 4.1 DISEÑO

```
nulos == muertos  → False
muertos[nulos]    → True
```

**Todos** los nulos de `DISEÑO` son accidentes `Con muertos`, pero no todos los accidentes mortales tienen el dato vacío. El detalle por año:

| Gravedad | Año | Nulos |
|---|---|---|
| Con muertos | 2015 | 265 |
| Con muertos | 2016 | 258 |
| Con muertos | 2017 | 245 |
| Con muertos | 2018 | 233 |
| Con muertos | 2014 | 140 |

- Los 1,141 nulos son solo de accidentes mortales entre 2014 y 2018. Desde 2019 este dato sí se registra.
- El nulo **no es aleatorio**: si se eliminaran esas filas se perdería el 77 % de los accidentes mortales de esos años.
- **Decisión:** rellenar con `"Sin información"`. Después del `fillna` quedaron **0 nulos**.

### 4.2 Otras columnas

| Columna | Causa | Decisión |
|---|---|---|
| `CLASE_ACCIDENTE` (6 nulos) | casos sueltos de 2015 y 2016 | rellenar con `"Sin información"` |
| `COMUNA`, `BARRIO`, `CBML` | accidentes sin georreferenciar (`NUMCOMUNA = Sin Inf`) | dejar `NaN`: no se puede inventar la ubicación |
| `EXPEDIENTE`, `NRO_RADICADO` | casi todos son accidentes mortales | dejar `NaN`: un identificador no se puede inventar |
| `Edad` | casi todos de 2020 y 2021 | dejar `NaN`: están fuera del periodo de estudio |

No se eliminó ninguna fila por tener nulos, porque `MES` (la columna de la pregunta analítica) no tiene nulos.

---

## 5. Limpieza de texto

```
Espacios dobles: 220
Espacio invisible: 0
0 0
```

- Se corrigieron **220 nombres de barrio** con espacios dobles (por ejemplo `B. Cerro  El Volador`). No se encontró el espacio invisible `\xa0`.
- No se aplicó `.str.title()` porque dañaría nombres como "Juan XXIII" o "Juan Pablo II".
- Se corrigió la codificación: `Solo da\xF1os` → `Solo daños`.
- Se unificaron las 4 formas de escribir "Caída de ocupante" con una expresión regular.
- Se estandarizaron `Sexo` (`Sin inf` → `Sin Inf`), `Condicion` (`Acompañante de Motocicleta` → `Acompañante de motocicleta`) y `Mes` (`Sept` → `Sep`).

---

## 6. Pipeline e informe de cambios

La limpieza se organizó en funciones de un solo paso, encadenadas con `.pipe()`. Después de cada paso, la función `anotar` registró el estado de la tabla.

```
(270742, 18) (235646, 20)
```

| Tabla | Paso | Filas | Nulos | Memoria (MB) |
|---|---|---|---|---|
| incidentes | 0 crudo | 270,765 | 51,620 | 96.3 |
| incidentes | 1 sin duplicados | 270,758 | 51,607 | 96.5 |
| incidentes | 2 tipos | 270,758 | 51,607 | 84.0 |
| incidentes | 3 sin accidentes repetidos | 270,742 | 51,602 | 84.0 |
| incidentes | 4 texto | 270,742 | 51,602 | 83.9 |
| incidentes | 5 nulos rellenados | 270,742 | 50,457 | 83.9 |
| victimas | 0 crudo | 235,843 | 1,143 | 68.4 |
| victimas | 1 sin duplicados | 235,646 | 1,128 | 68.8 |
| victimas | 2 tipos | 235,646 | 44,710 | 53.7 |
| victimas | 3 texto | 235,646 | 66,422 | 55.1 |

Cómo se interpreta:

- **Incidentes:** se eliminaron 23 filas (7 duplicados exactos y 16 accidentes repetidos) y se rellenaron 1,145 nulos. La memoria bajó 13 %.
- **Víctimas:** los nulos **aumentan** de 1,128 a 66,422. No es un error: los datos faltantes estaban escritos como texto (`Sin Inf`) y pandas no los contaba. Al convertir los tipos y separar la comuna, esos valores pasaron a `NaN`. La memoria bajó 19 %.
- La memoria sube un poco después de quitar duplicados porque `drop_duplicates()` crea una copia nueva de las columnas de texto. La diferencia es mínima.

---

## 7. Validación

```
Validación superada
True True
```

- **`Validación superada`:** los datos limpios cumplen todas las reglas. No hay accidentes repetidos, meses ni años fuera de rango, nulos en `DISEÑO`, errores de codificación ni espacios dobles, y las categorías de sexo y mes quedaron unificadas.
- **`True True`:** el resultado del pipeline es idéntico al de las funciones `limpiar_incidentes` y `limpiar_victimas`. Las dos formas de limpiar dan exactamente lo mismo.

Comprobaciones adicionales con `assert`:

| Comprobación | Valor esperado |
|---|---|
| Tamaño de incidentes | (270742, 18) |
| Nulos en `COMUNA` | 12,796 (2 nulos estaban entre los duplicados eliminados) |
| Nulos en `DISEÑO` | 0 |
| Tipo de `Edad` | `Int16` |
| Accidentes de 2019 | 45,792 (el `2019\r` quedó corregido) |

---

## 8. Archivos generados

| Archivo | Contenido |
|---|---|
| `incidentes_limpio.csv` | 270,742 accidentes limpios |
| `victimas_limpio.csv` | 235,646 víctimas limpias |
| `informe_cambios.csv` | tabla del informe de cambios |
| `accidentalidad_maestra.parquet` | cada víctima con los datos de su accidente |
| `resumen_diseno_via.csv` | víctimas y tasa de mortalidad por tipo de vía |
| `ficha_comunas.parquet` | resumen de las 21 comunas y corregimientos |
| `lideres_por_zona.csv` | zonas con mayor tasa de mortalidad |

---

## 9. Problemas que siguen pendientes

| Problema | Detectado con | Impacto |
|---|---|---|
| Edades imposibles (137, 121 años) | `nlargest(3, 'Edad')` | distorsionan el promedio de edad; se deben convertir en `NaN` |
| Diseños de vía sin tilde (`Tramo de via`, `Tunel`) | catálogo con `concat` | solo estético |
| 221 accidentes con muertos sin víctimas registradas | auditoría con `indicator=True` | inconsistencia entre las dos tablas |
| 13 accidentes de solo daños con víctimas | auditoría con `indicator=True` | inconsistencia entre las dos tablas |
| Fechas dudosas en los 16 accidentes repetidos | revisión de radicados | no se puede saber cuál fecha es la correcta |

---

## 10. Conclusión

Los datos crudos tenían problemas que habrían cambiado los resultados del análisis: filas vacías que multiplicaban por más de cuatro el número de víctimas, años mal escritos que dejaban a 2019 con menos de la mitad de sus accidentes, y categorías partidas que fragmentaban los conteos. Después de la limpieza, las columnas clave para la pregunta analítica (`AÑO` y `MES`) quedaron completas y validadas, y los datos están listos para responder en qué meses se concentran los accidentes.