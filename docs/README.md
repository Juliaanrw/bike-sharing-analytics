# bike-sharing-analytics

# AUTORES: 
Dereck Villalobos Quesada 
Julián Rodriguez Espinoza 
Carlos Vega Barrientos 

## Descripción general

La movilidad urbana representa uno de los principales retos de las ciudades modernas, debido a su relación con la congestión vehicular, la contaminación ambiental y la calidad de vida de las personas.

Los sistemas de bicicletas compartidas ofrecen una alternativa práctica, económica y sostenible. Sin embargo, su funcionamiento depende de la capacidad de anticipar la demanda para mantener una cantidad adecuada de bicicletas disponibles.

La analítica predictiva permite utilizar datos históricos y variables relacionadas con el clima, la temporada y el comportamiento de los usuarios para estimar la demanda futura.

---

## Problema de negocio

Predecir la cantidad diaria de bicicletas alquiladas puede ayudar a:

- Mejorar la disponibilidad de bicicletas.
- Organizar la distribución de unidades.
- Planificar los recursos operativos.
- Reducir costos relacionados con una mala asignación.
- Identificar periodos de alta y baja demanda.
- Apoyar decisiones relacionadas con movilidad sostenible.
- Mejorar la experiencia de los usuarios.

El problema predictivo consiste en estimar la cantidad total diaria de bicicletas alquiladas.

---

## Dataset

El dataset utilizado se denomina **Bike Sharing Demand**.

Contiene información diaria sobre el uso de un sistema de bicicletas compartidas e incluye variables relacionadas con:

- Condiciones climáticas.
- Temperatura.
- Sensación térmica.
- Humedad.
- Velocidad del viento.
- Estación del año.
- Mes.
- Día de la semana.
- Días laborales.
- Días feriados.
- Cantidad de usuarios.
- Cantidad total de alquileres.

### Dimensiones del dataset original

```text
731 observaciones
16 variables
0 valores faltantes
```

---

## Variable objetivo

La variable que se desea predecir es:

```text
cnt
```

`cnt` representa la cantidad total de bicicletas alquiladas durante un día.

En el análisis exploratorio inicial, esta variable presentó:

```text
Mínimo: 22 alquileres
Promedio: aproximadamente 4504 alquileres
Máximo: 8714 alquileres
```

---

## Revisión inicial de los datos

Para revisar el dataset se utilizaron las siguientes funciones:

```r
head(data)
str(data)
summary(data)
colSums(is.na(data))
```

Estas funciones permitieron:

- Visualizar los primeros registros.
- Identificar el tipo de cada variable.
- Revisar valores mínimos, máximos, medias y cuartiles.
- Confirmar que no existen datos faltantes.

---

## Análisis Exploratorio de Datos (EDA)

Se realizó un análisis exploratorio para comprender el comportamiento general de la demanda

### Demanda mensual

La demanda promedio presenta un comportamiento estacional.

Se observó un aumento durante los meses de clima más cálido, principalmente entre mayo y septiembre, mientras que los niveles más bajos se presentaron durante los meses de invierno.

Este comportamiento indica que el mes y la estación del año pueden influir en el uso del sistema.

### Días laborales y fines de semana

La comparación entre días laborales y fines de semana o feriados mostró medianas de alquileres relativamente similares.

Esto sugiere que la demanda total puede mantenerse estable, aunque el motivo de uso cambie:

- En días laborales puede existir un uso relacionado con transporte.
- En fines de semana puede existir un uso recreativo.

### Temperatura y demanda

Se observó una relación positiva entre la temperatura normalizada y la cantidad total de alquileres.

En general, conforme aumenta la temperatura, también tiende a aumentar la demanda.

Esto indica que la temperatura puede ser una variable importante para realizar las predicciones.

---

## Hipótesis predictiva

La hipótesis planteada es la siguiente:

> La demanda diaria de bicicletas compartidas está determinada principalmente por variables climáticas y temporales.

Entre las variables climáticas se consideran:

- Temperatura.
- Sensación térmica.
- Humedad.
- Velocidad del viento.
- Condición climática.

Entre las variables temporales se consideran:

- Estación del año.
- Año.
- Mes.
- Día de la semana.
- Día laboral.
- Día feriado.

Se espera que la combinación de estas variables permita construir un modelo capaz de anticipar la demanda diaria.

---

## Preparación de los datos

El dataset original contiene 16 variables.

Para construir el modelo se realizó una selección de variables utilizando:

```r
datos <- data %>%
  select(
    season,
    yr,
    mnth,
    holiday,
    weekday,
    workingday,
    weathersit,
    temp,
    atemp,
    hum,
    windspeed,
    cnt
  )
```

### Variables excluidas

Se excluyeron las siguientes variables:

#### `instant`

Representa únicamente un identificador para cada observación y no aporta información predictiva.

#### `dteday`

Representa la fecha completa. Su información temporal ya se encuentra representada mediante variables como año, mes y día de la semana.

#### `casual`

Representa la cantidad de alquileres realizados por usuarios casuales.

#### `registered`

Representa la cantidad de alquileres realizados por usuarios registrados.

Las variables `casual` y `registered` no se utilizaron como predictoras porque forman parte directa del total representado por `cnt`. Incluirlas produciría una relación directa con la variable objetivo y afectaría la evaluación real del modelo.

---

## Dataset preparado

Después de la selección de variables, el dataset utilizado para el modelo contiene:

```text
731 observaciones
12 variables
0 valores faltantes
```

---

## Conversión de variables categóricas

Las variables categóricas fueron convertidas a factores:

```r
datos$season <- as.factor(datos$season)
datos$yr <- as.factor(datos$yr)
datos$mnth <- as.factor(datos$mnth)
datos$holiday <- as.factor(datos$holiday)
datos$weekday <- as.factor(datos$weekday)
datos$workingday <- as.factor(datos$workingday)
datos$weathersit <- as.factor(datos$weathersit)
```

Esta conversión permite que R reconozca correctamente las variables como categorías y no como cantidades numéricas continuas.

---

## Variables utilizadas en el modelo

| Variable | Tipo | Descripción |
|---|---|---|
| `season` | Categórica | Estación del año |
| `yr` | Categórica | Año del registro |
| `mnth` | Categórica | Mes |
| `holiday` | Categórica | Indica si el día es feriado |
| `weekday` | Categórica | Día de la semana |
| `workingday` | Categórica | Indica si corresponde a un día laboral |
| `weathersit` | Categórica | Condición climática |
| `temp` | Numérica | Temperatura normalizada |
| `atemp` | Numérica | Sensación térmica normalizada |
| `hum` | Numérica | Humedad normalizada |
| `windspeed` | Numérica | Velocidad del viento normalizada |
| `cnt` | Numérica | Total diario de bicicletas alquiladas |

---

## Modelo seleccionado

Para esta parte del proyecto se seleccionó un modelo:

```text
SVM radial
```

Como la variable `cnt` es numérica, el modelo se utiliza para realizar una tarea de regresión.

### Razón de la selección

El kernel radial puede representar relaciones no lineales entre las variables predictoras y la demanda.

Esto resulta adecuado porque la cantidad de bicicletas alquiladas no depende de una sola variable y puede cambiar de manera diferente según:

- La temperatura.
- La humedad.
- La estación.
- El mes.
- Las condiciones climáticas.
- El tipo de día.

---

## División de los datos

Los datos fueron divididos en dos conjuntos:

```text
70 % para entrenamiento
30 % para prueba
```

Se utilizó el siguiente patrón:

```r
set.seed(123)

n <- nrow(datos)

indice <- sample(
  1:n,
  size = round(0.70*n)
)

entrenamiento <- datos[indice, ]
prueba <- datos[-indice, ]
```

La división genera aproximadamente:

```text
Entrenamiento: 512 observaciones
Prueba: 219 observaciones
```

El conjunto de entrenamiento se utiliza para construir el modelo y el conjunto de prueba se utiliza para evaluar su capacidad predictiva.

---

## Escalamiento de variables

Antes de entrenar el modelo SVM se centraron y escalaron las variables numéricas predictoras:

```text
temp
atemp
hum
windspeed
```

El escalamiento fue calculado únicamente con el conjunto de entrenamiento:

```r
pre <- preProcess(
  entrenamiento[,8:11],
  method = c("center", "scale")
)
```

Posteriormente se aplicó la misma transformación a ambos conjuntos:

```r
entrenamiento[,8:11] <- predict(
  pre,
  entrenamiento[,8:11]
)

prueba[,8:11] <- predict(
  pre,
  prueba[,8:11]
)
```

La variable `cnt` no fue escalada porque corresponde a la variable que se desea predecir.

---

## Construcción del modelo SVM radial

El modelo se construye mediante la función `svm()` de la librería `e1071`:

```r
modelo_radial <- svm(
  cnt ~ .,
  data = entrenamiento,
  kernel = "radial"
)
```

La fórmula:

```r
cnt ~ .
```

indica que `cnt` es la variable dependiente y que todas las demás variables del dataset preparado se utilizan como predictoras.

---

## Generación de predicciones

Las predicciones se realizan utilizando el conjunto de prueba:

```r
predicciones <- predict(
  modelo_radial,
  prueba
)
```

De esta forma, el modelo es evaluado con observaciones que no participaron durante su entrenamiento.

---

## Evaluación del modelo

El modelo será evaluado mediante tres métricas.

### RMSE

El RMSE mide la magnitud general del error y penaliza con mayor fuerza los errores grandes.

```r
RMSE <- sqrt(
  mean(
    (prueba$cnt - predicciones)^2
  )
)
```

Un RMSE menor indica un mejor desempeño.

### MAE

El MAE representa la diferencia promedio entre los valores reales y los valores predichos.

```r
MAE <- mean(
  abs(
    prueba$cnt - predicciones
  )
)
```

Un MAE de 500 indicaría que las predicciones se alejan de los valores reales en aproximadamente 500 alquileres, en promedio.

### R²

El R² representa la relación entre los valores reales y los valores predichos.

```r
R2 <- cor(
  prueba$cnt,
  predicciones
)^2
```

Un valor cercano a 1 indica una mayor capacidad predictiva.

---

## Resultados del modelo

//// AQUI TENEMOS QUE PONER CUANDO HAGAMOS EL ANALISIS ////

### Interpretación pendiente

////// Cuando tengamos los resultados, se completará la siguiente interpretación: //////

> El modelo SVM radial obtuvo un RMSE de **___**, un MAE de **___** y un R² de **___**.
>
> El MAE indica que las predicciones se alejan de los valores reales en aproximadamente **___ bicicletas**, en promedio.
>
> El R² obtenido indica que el modelo presenta una capacidad predictiva de aproximadamente **___ %**.

---

## Gráfico de valores reales y predichos

Para analizar visualmente el desempeño del modelo se utiliza un gráfico de valores reales contra valores predichos:

```r
ggplot(
  data.frame(
    real = prueba$cnt,
    pred = predicciones
  ),
  aes(
    x = real,
    y = pred
  )
) +
  geom_point() +
  geom_abline(
    slope = 1,
    intercept = 0,
    color = "red"
  ) +
  theme_minimal() +
  labs(
    title = "Valores reales y predichos por el SVM radial",
    x = "Alquileres reales",
    y = "Alquileres predichos"
  )
```

La línea roja representa una predicción ideal.

- Los puntos cercanos a la línea representan predicciones próximas a los valores reales.
- Los puntos alejados representan errores mayores.
- Una concentración general alrededor de la línea indicaría un buen desempeño del modelo.

---

## Librerías utilizadas

```r
library(tidyverse)
library(caret)
library(e1071)
library(ggplot2)
```

### Función de cada librería

- `tidyverse`: selección y preparación de variables.
- `caret`: escalamiento de variables.
- `e1071`: construcción del modelo SVM.
- `ggplot2`: creación de gráficos.

---

## Estructura del repositorio

```text
bike-sharing-analytics/
│
├── data/
│   ├── day.csv
│   └── day_preparado.csv
│
├── docs/
│   ├── README 
│   
│   
│
├── scripts/
   ├── notebook del análisis
   └── modelo SVM radial
```

---

# FLUJO DE PROYECTO (ACTUAL, AVANCE DERECK)
## 1. Carga de librerías

```r
library(tidyverse)
library(caret)
library(e1071)
library(ggplot2)
```

Cada librería se utiliza para lo siguiente:

- `tidyverse`: selección y preparación de variables.
- `caret`: escalamiento de variables numéricas.
- `e1071`: construcción del modelo SVM.
- `ggplot2`: creación de gráficos.

---

## 2. Carga del archivo

```r
data <- read.csv("day.csv")
```

Carga el archivo `day.csv` y lo guarda en el objeto llamado `data`.

El archivo debe estar en la misma carpeta que el Notebook o debe indicarse correctamente su ruta(ya tienen limpio, pueden trabajar con ese)

---

## 3. Revisión inicial

```r
head(data)
str(data)
summary(data)
colSums(is.na(data))
```

Estas funciones permiten:

- `head(data)`: mostrar las primeras filas.
- `str(data)`: revisar las variables y sus tipos.
- `summary(data)`: mostrar el resumen estadístico.
- `colSums(is.na(data))`: revisar si existen valores faltantes.

El dataset original contiene:

```text
731 observaciones
16 variables
0 valores faltantes
```

---

## 4. Selección de variables

```r
datos <- data %>%
  select(
    season,
    yr,
    mnth,
    holiday,
    weekday,
    workingday,
    weathersit,
    temp,
    atemp,
    hum,
    windspeed,
    cnt
  )
```

Se creó un nuevo dataset llamado `datos` con las variables necesarias para el modelo.

Se eliminaron:

- `instant`: identificador del registro.
- `dteday`: fecha completa.
- `casual`: cantidad de usuarios casuales.
- `registered`: cantidad de usuarios registrados.

`casual` y `registered` no se utilizaron porque su suma forma directamente la variable `cnt`.

---

## 5. Conversión de variables categóricas

```r
datos$season <- as.factor(datos$season)
datos$yr <- as.factor(datos$yr)
datos$mnth <- as.factor(datos$mnth)
datos$holiday <- as.factor(datos$holiday)
datos$weekday <- as.factor(datos$weekday)
datos$workingday <- as.factor(datos$workingday)
datos$weathersit <- as.factor(datos$weathersit)
```

Estas variables fueron convertidas a factores para que R las reconozca como categorías.

---

## 6. Comprobación del dataset preparado

```r
str(datos)
summary(datos)
colSums(is.na(datos))
nrow(datos)
```

Se comprobó que el dataset preparado contiene:

```text
731 observaciones
12 variables
0 valores faltantes
```

La variable que se desea predecir es:

```text
cnt
```

---

## 7. Distribución de la demanda

```r
ggplot(datos, aes(x = cnt)) +
  geom_histogram(
    bins = 20,
    fill = "steelblue",
    color = "black"
  ) +
  labs(
    title = "Distribución del total de alquileres",
    x = "Cantidad total de alquileres",
    y = "Frecuencia"
  )
```

Este histograma permite observar cómo se distribuye la cantidad diaria de bicicletas alquiladas.

---

## 8. Relación entre temperatura y demanda

```r
ggplot(
  datos,
  aes(
    x = temp,
    y = cnt
  )
) +
  geom_point(alpha = 0.6) +
  geom_smooth(
    method = "lm",
    se = FALSE,
    color = "red"
  ) +
  labs(
    title = "Relación entre temperatura y cantidad de alquileres",
    x = "Temperatura normalizada",
    y = "Cantidad total de alquileres"
  )
```

Este gráfico permite revisar si la demanda cambia conforme aumenta o disminuye la temperatura.

Cada punto representa un día y la línea roja muestra la tendencia general.

---

## 9. División de los datos

```r
set.seed(123)

n <- nrow(datos)

indice <- sample(
  1:n,
  size = round(0.70*n)
)

entrenamiento <- datos[indice, ]
prueba <- datos[-indice, ]
```

El dataset se dividió en:

```text
70 % para entrenamiento
30 % para prueba
```

El conjunto de entrenamiento se utiliza para construir el modelo.

El conjunto de prueba se utiliza para evaluar el modelo con datos que no participaron durante su construcción.

`set.seed(123)` permite repetir siempre la misma división.

---

## 10. Escalamiento de variables numéricas

```r
pre <- preProcess(
  entrenamiento[,8:11],
  method = c(
    "center",
    "scale"
  )
)
```

Se calcularon los valores necesarios para centrar y escalar las siguientes variables:

```text
temp
atemp
hum
windspeed
```

Después, el mismo escalamiento se aplicó a entrenamiento y prueba:

```r
entrenamiento[,8:11] <- predict(
  pre,
  entrenamiento[,8:11]
)

prueba[,8:11] <- predict(
  pre,
  prueba[,8:11]
)
```

La variable `cnt` no fue escalada porque es la variable que se desea predecir.

---

## 11. Comprobación del escalamiento

```r
summary(entrenamiento[,8:11])
summary(prueba[,8:11])
```

Se revisaron nuevamente las variables numéricas para confirmar que el escalamiento fue aplicado.

---

## 12. Construcción del modelo

```r
modelo_radial <- svm(
  cnt ~ .,
  data = entrenamiento,
  kernel = "radial"
)
```

Se construyó un modelo SVM con kernel radial.

La fórmula:

```r
cnt ~ .
```

significa que:

- `cnt` es la variable dependiente.
- Todas las demás variables se utilizan como predictoras.

Como `cnt` es numérica, el SVM funciona como un modelo de regresión.

---

## 13. Generación de predicciones

```r
predicciones <- predict(
  modelo_radial,
  prueba
)
```

El modelo utiliza el conjunto de prueba para estimar la cantidad diaria de bicicletas alquiladas.

```r
head(predicciones)
```

Muestra las primeras predicciones generadas.

---

## 14. Cálculo del RMSE

```r
RMSE <- sqrt(
  mean(
    (prueba$cnt - predicciones)^2
  )
)
```

El RMSE mide el error general del modelo y da mayor importancia a los errores grandes.

Un valor menor representa un mejor resultado.

---

## 15. Cálculo del MAE

```r
MAE <- mean(
  abs(
    prueba$cnt - predicciones
  )
)
```

El MAE muestra cuántos alquileres separan, en promedio, las predicciones de los valores reales.

Un valor menor representa mejores predicciones.

---

## 16. Cálculo del R²

```r
R2 <- cor(
  prueba$cnt,
  predicciones
)^2
```

El R² indica qué tan relacionados están los valores reales y los valores predichos.

Un resultado más cercano a `1` representa una mayor capacidad predictiva.

---

## 17. Tabla de resultados

```r
resultados <- data.frame(
  Modelo = "SVM radial",
  RMSE = RMSE,
  MAE = MAE,
  R2 = R2
)

resultados
```

Esta tabla reúne las tres métricas del modelo para facilitar su lectura y comparación con otros modelos del equipo.

El orden correcto debe ser:

```text
Calcular RMSE
Calcular MAE
Calcular R²
Crear la tabla resultados
```

---

## 18. Comparación entre valores reales y predichos

```r
ggplot(
  data.frame(
    real = prueba$cnt,
    pred = predicciones
  ),
  aes(
    x = real,
    y = pred
  )
) +
  geom_point() +
  geom_abline(
    slope = 1,
    intercept = 0,
    color = "red"
  ) +
  theme_minimal() +
  labs(
    title = "Valores reales y predichos por el SVM radial",
    x = "Alquileres reales",
    y = "Alquileres predichos"
  )
```

Este gráfico compara:

- Eje X: cantidad real de alquileres.
- Eje Y: cantidad predicha.
- Línea roja: predicción ideal.

Los puntos cercanos a la línea roja representan predicciones más próximas a los valores reales.

---

## 19. Exportación del dataset preparado

```r
write.csv(
  datos,
  "day_preparado.csv",
  row.names = FALSE
)
```

Este código guarda el dataset preparado en un nuevo archivo llamado:

```text
day_preparado.csv
```

El archivo contiene las 12 variables seleccionadas.

La opción:

```r
row.names = FALSE
```

evita que R agregue una columna adicional con el número de cada fila.

---

## Resumen completo del proceso

```text
Carga de librerías
        ↓
Carga del archivo day.csv
        ↓
Revisión de estructura y valores faltantes
        ↓
Selección de variables necesarias
        ↓
Eliminación de variables que producen información directa
        ↓
Conversión de variables categóricas a factor
        ↓
Comprobación del dataset preparado
        ↓
Análisis gráfico de la demanda
        ↓
División 70 % entrenamiento y 30 % prueba
        ↓
Escalamiento de variables numéricas
        ↓
Construcción del SVM radial
        ↓
Generación de predicciones
        ↓
Cálculo de RMSE, MAE y R²
        ↓
Creación de la tabla de resultados
        ↓
Gráfico de valores reales y predichos
        ↓
Exportación de day_preparado.csv
```

---



















## Estado actual del proyecto

### Completado

- Definición del problema.
- Presentación del dataset.
- Revisión de estructura.
- Revisión de valores faltantes.
- Análisis exploratorio inicial.
- Visualización de demanda mensual.
- Comparación entre días laborales y fines de semana.
- Análisis de la relación entre temperatura y demanda.
- Formulación de la hipótesis predictiva.
- Selección de variables.
- Conversión de variables categóricas.
- Preparación del dataset.
- División de entrenamiento y prueba.
- Escalamiento de variables numéricas.
- Creación del Notebook.
- Implementación del modelo SVM radial.
- Preparación del código para generar predicciones.
- Preparación de las métricas de evaluación.

### Pendiente

- Ejecutar completamente el modelo.
- Registrar los valores finales de RMSE, MAE y R².
- Incorporar el gráfico final de valores reales y predichos.
- Interpretar los resultados obtenidos.
- Comparar el SVM radial con el modelo desarrollado por otro integrante.
- Redactar la conclusión final del proyecto.

---

## Conclusión actual

El análisis exploratorio mostró que la demanda de bicicletas presenta patrones relacionados con la época del año y las condiciones climáticas.

La temperatura presenta una relación positiva con la cantidad de alquileres, mientras que variables como humedad, condición climática, mes y estación también pueden aportar información relevante.

A partir de estos resultados, se preparó un modelo SVM con kernel radial para estimar la cantidad total diaria de bicicletas alquiladas.

La evaluación definitiva del modelo se realizará mediante RMSE, MAE, R² y el gráfico de valores reales contra valores predichos.

---

## Autores

- Carlos Vega Barrientos
- Julián Rodríguez Espinoza
- Dereck Villalobos Quesada

Proyecto desarrollado para el curso **CD-501 Analítica Predictiva para Negocios**.
