# MOMENTO 1: RECONOCIMIENTO DEL PROYECTO Y DE LOS DATASETS

# Accidentalidad vial en Medellín 2019-2023

AUTOR:Juan David 

# PROPOSITO

Ayudar a la Secretaría de Movilidad a planear controles viales, campañas de prevención y presencia de agentes de tránsito en las épocas más críticas.

## PREGUNTA ANALITICA

¿En qué meses del año se concentra el mayor número de accidentes de tránsito en Medellín entre 2015 y 2019?


# PAGINAS DE EXTRACCION DE INFORMACION

FUENTE: Alcaldia de Medellin ,Secretaria de Movilidad ,Portal MEData



Conjunto: (Identificador 1-023-25-000360) Licencia CC BY 4.0

Descargado el 2/11/2026

# Datasets

Incidentes viales | `incidentes_viales.csv` | Accidentes de tránsito registrados por la Secretaría de Movilidad de Medellín. Cada fila es un accidente, con fecha, mes, clase, gravedad, diseño de la vía, comuna, barrio y coordenadas. | Es la fuente principal: permite contar accidentes por mes entre 2015 y 2019. 

Victimas en incidentes viales | `Mede_Victimas_inci.csv` | Víctimas de accidentes de tránsito registradas por la Secretaría de Movilidad. Cada fila es una víctima, con gravedad, sexo, edad, condición, fecha, comuna y barrio. | Complementa el análisis con el número y el perfil de las víctimas en los meses críticos. 

# MOMENTO 3:TABLA INFORME DIAGNOSTICO



![alt text](../imagenes/informe_diagnistico.png)


# MOMENTO 4 : DIAGNOSTICO INICIAL DE CALIDAD

![tabla diagnsotica](../imagenes/tabla_diagnostica.png)

# ACCION DE LIMPIEZA PROPUESTA

# 1- Tratamiento de registros duplicados

Eliminar los registros duplicados 

# 2- Tratamiento de valores faltantes

Se propone analizar los valores faltantes de cada variable para determinar su importancia dentro del estudio. Dependiendo de la naturaleza de los datos, se evaluará si es conveniente conservarlos, recuperarlos mediante fuentes confiables o excluir determinados registros cuando exista una justificación técnica.

 # 3- Normalizacion de variable categorica

 Se realizará una estandarización de los valores de texto para evitar diferencias en la clasificación de una misma categoría. Se corregirán inconsistencias relacionadas con mayúsculas, minúsculas, espacios y abreviaciones, como ocurre con los valores Sep y Sept en la variable Mes.

 # 4- Correcion de tipos de datos

 Se revisarán los tipos de datos de las columnas para garantizar que cada variable tenga un formato adecuado según su naturaleza. Se prestará especial atención a las variables numéricas, categóricas y temporales, facilitando así su utilización en operaciones estadísticas.

 # 5- Validacion de fechas

 Se verificará la coherencia de las variables relacionadas con fechas, incluyendo día, mes y año. Se buscarán valores fuera de los rangos permitidos y posibles inconsistencias que puedan afectar el análisis temporal de los accidentes de tránsito.

 # 6- Validacion y limpieza de coordenadas geograficas

 Se propone transformar los valores no informados de latitud y longitud en valores nulos, además de verificar que las coordenadas numéricas se encuentren dentro de los rangos geográficos válidos. Esto permitirá preparar la información para posibles análisis de distribución espacial.

 # 7- Identificacion y valoracion de valores atipicos

 Se analizarán los valores extremos presentes en las variables numéricas, con el objetivo de determinar si corresponden a situaciones reales o posibles errores de registro. No se eliminarán automáticamente, ya que podrían representar características importantes de los accidentes.


# MOMENTO 5


# INFORME DEL MOMENTO 4: DIAGNÓSTICO DE CALIDAD DE LOS DATOS

## 1. Nombre del proyecto y pregunta analítica

**Nombre del proyecto:** Análisis de datos de accidentes de tránsito.

**Pregunta analítica:**

¿Qué meses presentan el mayor número de accidentes de tránsito en una ciudad durante un período determinado?

**Propósito del proyecto:**

Analizar el comportamiento mensual de los accidentes de tránsito mediante técnicas de análisis descriptivo, con el propósito de identificar los meses con mayor y menor cantidad de incidentes, reconocer patrones temporales y obtener información que permita comprender mejor el comportamiento de la accidentalidad vial.

# 2. Descripción de los datasets utilizados

Para el desarrollo del proyecto se utilizaron dos conjuntos de datos relacionados con los accidentes de tránsito: Incidentes y Víctimas.

# Dataset de Incidentes

Este conjunto contiene información relacionada con los accidentes de tránsito registrados. Incluye variables que permiten identificar características de los incidentes, ubicación geográfica, clasificación del accidente, fechas y otros datos asociados a los eventos.

Su estructura inicial de análisis presenta 270.765 registros y 18 columnas.

# Dataset de Víctimas

Este conjunto contiene información relacionada con las personas involucradas en los accidentes de tránsito. Incluye variables como edad, sexo, ubicación del incidente y otros datos asociados a las víctimas.

Inicialmente contenía 1.048.317 registros y 19 columnas. Durante la inspección se identificaron 812.474 filas completamente vacías, las cuales fueron eliminadas. Después de esta eliminación, el conjunto quedó con 235.843 registros y 19 columnas.

Ambos conjuntos permiten abordar el problema desde dos perspectivas complementarias: las características de los accidentes y las características de las personas involucradas.

## 3. Resultados de la inspección estructural

Durante la inspección estructural se revisaron las dimensiones de los datasets, los nombres de las columnas, los tipos de datos, los valores nulos y la distribución general de las variables.

Los resultados fueron los siguientes:

# Dataset de Incidentes:
- Registros: 270.765.
- Columnas: 18.
- Se identificaron variables relacionadas con la ubicación, clasificación y características de los accidentes.
- Se encontraron valores faltantes en diferentes columnas.
- Se identificaron registros duplicados.

# Dataset de Víctimas:
- Registros iniciales: 1.048.317.
- Columnas: 19.
- Filas completamente vacías eliminadas: 812.474.
- Registros restantes: 235.843.
- Se identificaron valores faltantes, inconsistencias en variables categóricas y registros duplicados.

La inspección permitió reconocer que ambos conjuntos contienen información útil para el proyecto, pero requieren procesos adicionales de preparación y validación antes de desarrollar el análisis estadístico.

## 4. Identificación de valores faltantes, tipos de datos y posibles inconsistencias

Durante el diagnóstico se identificaron diferentes situaciones que pueden afectar la calidad de la información.

### 4.1. Valores faltantes en el dataset de Incidentes

Se encontraron valores faltantes en las siguientes variables:

- BARRIO: 19.006 valores faltantes.
- CBML: 18.156 valores faltantes.
- COMUNA: 12.798 valores faltantes.
- DISEÑO: 1.148 valores faltantes.
- DIRECCION ENCASILLADA: 391 valores faltantes.
- EXPEDIENTE: 110 valores faltantes.
- CLASE_ACCIDENTE: 6 valores faltantes.
- NRO_RADICADO: 5 valores faltantes.

Estos faltantes afectan principalmente la disponibilidad de información geográfica y algunos atributos de clasificación de los incidentes.

### 4.2. Valores faltantes en el dataset de Víctimas

Se identificaron cantidades importantes de valores faltantes en diferentes variables:

- Barrio: 813.092 valores faltantes.
- Edad: 812.982 valores faltantes.
- Dirección_incidente: 812.486 valores faltantes.
- Radicado: 812.479 valores faltantes.
- Otras columnas presentaron aproximadamente 812.474 valores faltantes.

La presencia de estos valores está relacionada, en parte, con las filas completamente vacías que fueron identificadas y eliminadas durante la preparación inicial.

### 4.3. Tipos de datos

Se realizó una revisión de los tipos de datos de las columnas para identificar posibles diferencias entre el contenido de las variables y su representación dentro de Python.

Se consideró necesario prestar especial atención a:
- Variables numéricas, como edad y número de día.
- Variables categóricas, como sexo, mes y clasificación del accidente.
- Variables temporales, relacionadas con las fechas de los incidentes.
- Variables geográficas, como latitud y longitud.

La revisión permitió establecer la necesidad de validar y normalizar los tipos de datos antes de realizar operaciones estadísticas.

### 4.4. Posibles inconsistencias

Durante la exploración se encontraron las siguientes inconsistencias:

# Variable Mes:

Se identificaron dos representaciones diferentes para septiembre: `Sep` y `Sept`. Esto genera categorías separadas para un mismo mes.

# Variable Año:

Se encontraron valores como `2019` y `2019` acompañado de un carácter de retorno de carro (`\r`), lo que puede generar diferencias al agrupar los registros.

# Variable Sexo:

Se identificaron diferencias en la escritura de categorías como `Sin Inf` y `Sin inf`, que deben normalizarse.

# Variables Latitud y Longitud:

Se encontraron valores de texto como `Sin Inf`, con una frecuencia de 20.845 registros en cada variable según la inspección realizada. Estos valores no representan coordenadas numéricas válidas.

# Variable Num_dia:

Se identificó un valor mínimo de 0, por lo que es necesario revisar los registros para determinar si corresponden a errores o inconsistencias en la fecha.

## 5. Principales hallazgos de calidad por dataset

### 5.1. Dataset de Incidentes

Los principales problemas identificados fueron:

- Presencia de valores faltantes en variables geográficas y descriptivas.
- Categorías inconsistentes en la variable Mes.
- Diferencias de formato en la variable Año.
- Existencia de 3 registros duplicados.
- Necesidad de revisar los tipos de datos y la consistencia de las variables.

Estos hallazgos indican que el conjunto contiene información aprovechable para estudiar el comportamiento mensual de los accidentes, aunque requiere normalización y validación antes de realizar agrupaciones y comparaciones estadísticas.

### 5.2. Dataset de Víctimas

Los principales problemas identificados fueron:

- Existencia inicial de 812.474 filas completamente vacías.
- Cantidades elevadas de valores faltantes en variables como Edad y Barrio.
- Inconsistencias en las categorías de la variable Sexo.
- Valores no numéricos en las variables Latitud y Longitud.
- Existencia de 197 registros duplicados.
- Necesidad de validar variables numéricas y geográficas.

La eliminación inicial de las filas completamente vacías permitió reducir considerablemente el tamaño del conjunto. Sin embargo, todavía es necesario evaluar los valores faltantes y las inconsistencias restantes.

## 6. Acciones de limpieza y preparación propuestas

A partir de los resultados obtenidos en el diagnóstico, se propone desarrollar los siguientes procedimientos:

1. **Tratamiento de duplicados:** identificar y eliminar los registros duplicados confirmados para evitar repeticiones en los análisis.

2. **Tratamiento de valores faltantes:** evaluar los valores ausentes por variable y determinar si deben conservarse, recuperarse mediante fuentes confiables o excluirse cuando exista una justificación técnica.

3. **Normalización de variables categóricas:** unificar las categorías de texto, corregir diferencias de escritura y eliminar espacios o caracteres innecesarios.

4. **Corrección de tipos de datos:** garantizar que las variables numéricas, categóricas y temporales tengan formatos adecuados para su procesamiento.

5. **Validación de fechas:** comprobar la coherencia entre día, mes y año para identificar posibles registros incorrectos.

6. **Limpieza de coordenadas geográficas:** transformar los valores no informados en valores nulos y validar los rangos de latitud y longitud.

7. **Identificación de valores atípicos:** evaluar los valores extremos para determinar si corresponden a situaciones reales o errores de registro, evitando eliminarlos automáticamente.

8. **Validación de integridad entre datasets:** comprobar la correspondencia entre los registros de Incidentes y Víctimas mediante sus identificadores.

9. **Validación final:** comparar los resultados antes y después de la limpieza para comprobar que las transformaciones hayan mejorado la calidad de la información.

Estas acciones permitirán preparar los conjuntos de datos para las siguientes etapas del proyecto.

## 7. Conclusiones sobre las condiciones actuales de los datos

El diagnóstico realizado permitió identificar que los datasets de Incidentes y Víctimas contienen información relevante para desarrollar el análisis de accidentes de tránsito, especialmente para estudiar su comportamiento temporal y las características de las personas involucradas.

Sin embargo, se encontraron problemas relacionados con valores faltantes, registros duplicados, inconsistencias en variables categóricas, diferencias en los formatos de algunos campos y valores no válidos en las coordenadas geográficas.

Uno de los hallazgos más importantes fue la presencia de 812.474 filas completamente vacías en el dataset de Víctimas, cuya eliminación permitió reducir el conjunto de 1.048.317 a 235.843 registros.

También se identificaron inconsistencias en la variable Mes del dataset de Incidentes, particularmente en la representación de septiembre, situación que debe corregirse para evitar errores al realizar el análisis mensual de la accidentalidad.

En conclusión, los datos presentan una base de información aprovechable para continuar con el proyecto, pero todavía requieren procesos de limpieza, normalización y validación. Por esta razón, se considera necesario desarrollar el proceso de preparación antes de avanzar hacia el análisis estadístico y la interpretación de los resultados.

El diagnóstico del Momento 4 establece así el punto de partida para la siguiente etapa: la limpieza y transformación de los datos.




