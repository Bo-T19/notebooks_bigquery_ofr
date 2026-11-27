# Diccionario de datos

En BigQuery solo quedan **9 tablas**, las de datos, que arman los notebooks 01 a 06. Aquí está qué es cada una y
cada una de sus columnas.

- **Dónde quedan.** En `DATASET_DEFORESTACION`; por defecto, `Cambio_Climatico`, en el proyecto `prueba-ofr`.
- **Los notebooks 07 a 18 no dejan tablas.** Sus resultados (estadísticas, segmentos, modelos, comparación,
  demo de Landsat) se ven en el propio notebook.
- **Los nombres van en mayúscula.** En BigQuery importan las mayúsculas: en SQL se escriben igual
  (`DEFORESTACION_IDEAM_CELDA_5KM_PERIODO`, no `deforestacion_ideam_celda_5km_periodo`).
- **Las tablas se reemplazan enteras** cada vez que se corre el notebook que las escribe.
- **`orden_fila`.** Todas las tablas la tienen, y por eso no se repite abajo. Es la posición de la fila en la
  tabla que armó el notebook: BigQuery no guarda el orden de las filas. Para leer una tabla en su orden,
  `ORDER BY orden_fila`.
- **Unidades.** Hectáreas (ha), millones de pesos corrientes (M$) y porcentajes de 0 a 100, salvo donde se dice
  que algo va de 0 a 1.
- **"Filas en la corrida de prueba"** es cuántas filas tuvo la tabla en la prueba de noviembre de 2026. Cambia
  cuando se actualizan los datos.

## Las tablas

| Tabla | Notebook | Una fila es |
|---|---|---|
| [`GRILLA_CELDAS_5KM`](#grilla) | 01 | una celda de 5 × 5 km |
| [`BOSQUE_BASE_HANSEN_CELDA_5KM`](#bosque_base_hansen_celda_5km) | 02 | una celda |
| [`PERDIDA_COBERTURA_HANSEN_CELDA_5KM_ANIO`](#perdida_cobertura_hansen_celda_5km_anio) | 02 | una celda en un año |
| [`ALERTAS_GFW_CELDA_5KM_MES`](#alertas_gfw_celda_5km_mes) | 03 | una celda en un mes |
| [`DEFORESTACION_IDEAM_CELDA_5KM_PERIODO`](#deforestacion_ideam_celda_5km_periodo) | 04 | una celda en un periodo de dos años |
| [`DETECCIONES_TEMPRANAS_IDEAM_CELDA_5KM_TRIMESTRE`](#detecciones_tempranas_ideam_celda_5km_trimestre) | 05 | una celda en un trimestre |
| [`DEFORESTACION_CREDITO_PRODUCCION_MUNICIPIO_ANIO`](#deforestacion_credito_produccion_municipio_anio) | 06 | un municipio en un año |
| [`ALERTAS_GFW_CREDITO_MUNICIPIO_MES`](#alertas_gfw_credito_municipio_mes) | 06 | un municipio en un mes |
| [`AREA_COSECHADA_EVA_MUNICIPIO_CULTIVO_ANIO`](#area_cosechada_eva_municipio_cultivo_anio) | 06 | un municipio, un cultivo y un año |



## 01 a 06 · Carga de las tablas de deforestación


### GRILLA_CELDAS_5KM

Grilla de celdas de 5 x 5 km sobre Colombia, alineada con el origen de EPSG:3116. Es la unidad de todas las tablas de deforestación. Fuente de los límites: DANE, Marco Geoestadístico Nacional 2023 (departamentos) y 2025 (municipios), CC BY 4.0.

**Una fila es** una celda de 5 × 5 km. **La escribe** el notebook 01, en `DATASET_DEFORESTACION`. Filas en la corrida de prueba: 45.621.

| Columna | Tipo | Qué es |
|---|---|---|
| `cell_id` | STRING | Identificador de la celda: longitud y latitud del centro por 10.000, redondeadas. Llave para unir las tablas por celda. |
| `lon` | FLOAT | Longitud del centro de la celda (grados, EPSG:4326). |
| `lat` | FLOAT | Latitud del centro de la celda (grados, EPSG:4326). |
| `x3116` | FLOAT | Coordenada X del centro, en metros (EPSG:3116). |
| `y3116` | FLOAT | Coordenada Y del centro, en metros (EPSG:3116). |
| `ix` | INTEGER | Columna de la celda en la grilla: floor(x / 5000). |
| `iy` | INTEGER | Fila de la celda en la grilla: floor(y / 5000). |
| `departamento` | STRING | Departamento donde cae el centro de la celda (DANE, MGN 2023). |
| `cod_dane` | STRING | Código DANE de 5 dígitos del municipio donde cae el centro (DANE, MGN 2025). Vacío si el centro cae fuera de todo municipio. |
| `municipio` | STRING | Nombre del municipio, solo para leer. Para cruzar con otras tablas usa cod_dane. |

### BOSQUE_BASE_HANSEN_CELDA_5KM

Bosque al empezar el análisis, por celda de la grilla, según Hansen GFC-2025-v1.13: píxeles con dosel de al menos 30 % en 2000 y sin pérdida hasta 2019, reproyectados a 25 m. Es el denominador común de los paneles de deforestación.

**Una fila es** una celda. **La escribe** el notebook 02, en `DATASET_DEFORESTACION`. Filas en la corrida de prueba: 45.621.

| Columna | Tipo | Qué es |
|---|---|---|
| `cell_id` | STRING | Identificador de la celda (tabla GRILLA_CELDAS_5KM). |
| `bosque_base_ha` | FLOAT | Hectáreas de bosque al empezar 2020, según Hansen. |

### PERDIDA_COBERTURA_HANSEN_CELDA_5KM_ANIO

Pérdida anual de cobertura arbórea por celda de 5 km, según Hansen GFC-2025-v1.13. Mide pérdida de dosel por cualquier causa, no deforestación oficial: sobre Colombia equivale a 1,5-2,5 veces la cifra del IDEAM. Solo celdas con al menos 50 ha de bosque base.

**Una fila es** una celda en un año. **La escribe** el notebook 02, en `DATASET_DEFORESTACION`. Filas en la corrida de prueba: 267.696.

| Columna | Tipo | Qué es |
|---|---|---|
| `cell_id` | STRING | Identificador de la celda (tabla GRILLA_CELDAS_5KM). |
| `anio` | INTEGER | Año de la pérdida (año calendario). |
| `perdida_ha` | FLOAT | Hectáreas que perdieron el dosel ese año, entre las que tenían al menos 30 % de dosel en 2000. |
| `lon` | FLOAT | Longitud del centro de la celda. |
| `lat` | FLOAT | Latitud del centro de la celda. |
| `departamento` | STRING | Departamento del centro de la celda. |
| `bosque_base_ha` | FLOAT | Bosque al empezar el análisis (tabla BOSQUE_BASE_HANSEN_CELDA_5KM). Es el denominador de esta tabla. |
| `cod_dane` | STRING | Código DANE del municipio del centro de la celda. |
| `municipio` | STRING | Nombre del municipio, solo para leer. |

### ALERTAS_GFW_CELDA_5KM_MES

Alertas de disturbio de la vegetación de Global Forest Watch (gfw_integrated_alerts, confianza high y highest) por celda de 5 km y mes, 2020-01 a 2026-07. No es deforestación oficial: no distingue la causa. Solo celdas con al menos 50 ha de bosque base.

**Una fila es** una celda en un mes. **La escribe** el notebook 03, en `DATASET_DEFORESTACION`. Filas en la corrida de prueba: 3.524.664.

| Columna | Tipo | Qué es |
|---|---|---|
| `cell_id` | STRING | Identificador de la celda (tabla GRILLA_CELDAS_5KM). |
| `periodo` | DATETIME | Primer día del mes. |
| `area_def_ha` | FLOAT | Hectáreas con alerta de alta confianza en el mes. |
| `lon` | FLOAT | Longitud del centro de la celda. |
| `lat` | FLOAT | Latitud del centro de la celda. |
| `bosque_base_ha` | FLOAT | Bosque al empezar el análisis, según Hansen (tabla BOSQUE_BASE_HANSEN_CELDA_5KM). |
| `departamento` | STRING | Departamento del centro de la celda. |
| `def_acum_ha` | FLOAT | Hectáreas con alerta acumuladas desde el primer mes hasta este, incluido. |
| `bosque_remanente_ha` | FLOAT | Bosque base menos lo perdido antes de este mes. Denominador de tasa_def. |
| `tasa_def` | FLOAT | area_def_ha / bosque_remanente_ha, con máximo 1. |
| `evento` | INTEGER | 1 si el mes tuvo alguna alerta, 0 si no. |
| `lag_1_ha` | FLOAT | Hectáreas con alerta del mes anterior en la misma celda. |
| `lag_2_ha` | FLOAT | Hectáreas con alerta de dos meses antes. |
| `lag_3_ha` | FLOAT | Hectáreas con alerta de tres meses antes. |
| `media_movil_3` | FLOAT | Promedio de las hectáreas con alerta de los tres meses anteriores (sin el actual). |
| `anio` | INTEGER | Año del periodo. |
| `mes` | INTEGER | Mes del periodo (1 a 12). |
| `cod_dane` | STRING | Código DANE del municipio del centro de la celda. |
| `municipio` | STRING | Nombre del municipio, solo para leer. |

### DEFORESTACION_IDEAM_CELDA_5KM_PERIODO

Deforestación oficial por celda de 5 km y periodo, según las capas de cambio de la superficie cubierta por bosque natural del IDEAM (SMByC). Cada periodo compara dos años y su cifra corresponde al año final. Solo celdas con al menos 50 ha de bosque base.

**Una fila es** una celda en un periodo de dos años. **La escribe** el notebook 04, en `DATASET_DEFORESTACION`. Filas en la corrida de prueba: 223.080.

| Columna | Tipo | Qué es |
|---|---|---|
| `cell_id` | STRING | Identificador de la celda (tabla GRILLA_CELDAS_5KM). |
| `periodo` | STRING | Transición entre dos años, p. ej. 2020-2021; la cifra corresponde al año final. |
| `bosque_estable_ha` | FLOAT | Hectáreas que siguieron siendo bosque natural (clase 1). |
| `def_ha` | FLOAT | Hectáreas de bosque natural deforestadas en el periodo (clase 2). |
| `sin_info_ha` | FLOAT | Hectáreas que no se pudieron observar, sobre todo por nubes (clase 3). Una celda con mucha superficie aquí tiene un dato de deforestación poco confiable. |
| `regeneracion_ha` | FLOAT | Hectáreas que pasaron a ser bosque (clase 4). |
| `no_bosque_ha` | FLOAT | Hectáreas que no eran ni son bosque (clase 5). |
| `bosque_ideam_ha` | FLOAT | Bosque natural al inicio del periodo: bosque estable + deforestación. Denominador de la tasa de esta tabla. |
| `bosque_ideam_fin_ha` | FLOAT | Bosque natural al final del periodo: bosque estable + regeneración. |
| `lon` | FLOAT | Longitud del centro de la celda. |
| `lat` | FLOAT | Latitud del centro de la celda. |
| `departamento` | STRING | Departamento del centro de la celda. |
| `bosque_base_ha` | FLOAT | Bosque según Hansen (tabla BOSQUE_BASE_HANSEN_CELDA_5KM); no es el denominador de esta tabla. |
| `cod_dane` | STRING | Código DANE del municipio del centro de la celda. |
| `municipio` | STRING | Nombre del municipio, solo para leer. |

### DETECCIONES_TEMPRANAS_IDEAM_CELDA_5KM_TRIMESTRE

Detecciones tempranas de deforestación del IDEAM (SMByC) por celda de 5 km y trimestre. Cuenta puntos, no hectáreas, y los conteos NO son comparables entre trimestres: sirve para priorizar lugares dentro de un mismo trimestre. Solo celdas con al menos 50 ha de bosque base.

**Una fila es** una celda en un trimestre. **La escribe** el notebook 05, en `DATASET_DEFORESTACION`. Filas en la corrida de prueba: 1.160.016.

| Columna | Tipo | Qué es |
|---|---|---|
| `cell_id` | STRING | Identificador de la celda (tabla GRILLA_CELDAS_5KM). |
| `periodo` | DATETIME | Primer día del trimestre. |
| `trimestre_txt` | STRING | Trimestre en texto, p. ej. 2024-T3. |
| `anio` | INTEGER | Año. |
| `trimestre` | INTEGER | Trimestre (1 a 4). |
| `n_alertas` | INTEGER | Número de detecciones tempranas que caen en la celda en el trimestre. |
| `n_alertas_sinap` | FLOAT | De ellas, cuántas caen en un área protegida del SINAP. Vacío si el trimestre no trae ese dato. |
| `sinap_disponible` | BOOLEAN | Si el trimestre trae el dato del área protegida (campo NOM_SINAP). |
| `lon` | FLOAT | Longitud del centro de la celda. |
| `lat` | FLOAT | Latitud del centro de la celda. |
| `departamento` | STRING | Departamento del centro de la celda. |
| `bosque_base_ha` | FLOAT | Bosque según Hansen (tabla BOSQUE_BASE_HANSEN_CELDA_5KM). |
| `cod_dane` | STRING | Código DANE del municipio del centro de la celda. |
| `municipio` | STRING | Nombre del municipio, solo para leer. |

### DEFORESTACION_CREDITO_PRODUCCION_MUNICIPIO_ANIO

Panel municipal por año (2020-2025): deforestación (IDEAM y GFW), crédito de FINAGRO, cultivos de la EVA, ganado del ICA y atributos del municipio. Es la tabla de la segmentación y los modelos.

**Una fila es** un municipio en un año. **La escribe** el notebook 06, en `DATASET_DEFORESTACION`. Filas en la corrida de prueba: 6.738.

| Columna | Tipo | Qué es |
|---|---|---|
| `cod_dane` | STRING | Código DANE del municipio (5 dígitos). |
| `anio` | INTEGER | Año. Para el IDEAM, el año final del periodo (2020-2021 es 2021). |
| `def_ideam_ha` | FLOAT | Deforestación oficial del IDEAM en el municipio, en hectáreas. |
| `bosque_ideam_ha` | FLOAT | Bosque natural al inicio del periodo, según el IDEAM. Denominador de tasa_def_ideam. |
| `sin_info_ideam_ha` | FLOAT | Hectáreas que el IDEAM no pudo observar (nubes). |
| `alertas_gfw_ha` | FLOAT | Hectáreas con alerta de Global Forest Watch en el año. |
| `credito_mm` | FLOAT | Crédito de FINAGRO desembolsado en el municipio, todos los eslabones, en millones de pesos. |
| `credito_produccion_mm` | FLOAT | Crédito de FINAGRO del eslabón de producción, en millones de pesos. |
| `credito_arroz_produccion_mm` | FLOAT | Crédito de producción a la cadena del arroz, en millones de pesos. |
| `credito_cacao_produccion_mm` | FLOAT | Crédito de producción a la cadena del cacao, en millones de pesos. |
| `credito_cafe_produccion_mm` | FLOAT | Crédito de producción a la cadena del café, en millones de pesos. |
| `credito_ganaderia_produccion_mm` | FLOAT | Crédito de producción a la ganadería (doble propósito, leche y carne), en millones de pesos. |
| `credito_palma_produccion_mm` | FLOAT | Crédito de producción a la palma de aceite, en millones de pesos. |
| `area_cosechada_ha` | FLOAT | Hectáreas cosechadas de todos los cultivos (EVA). Vacío después del último año de la EVA. |
| `cosechada_palma_ha` | FLOAT | Hectáreas cosechadas de palma de aceite (EVA). |
| `cosechada_cacao_ha` | FLOAT | Hectáreas cosechadas de cacao (EVA). |
| `cosechada_cafe_ha` | FLOAT | Hectáreas cosechadas de café (EVA). |
| `cosechada_arroz_ha` | FLOAT | Hectáreas cosechadas de arroz (EVA). |
| `bovinos` | FLOAT | Inventario de bovinos (ICA). |
| `fincas_bovinas` | FLOAT | Fincas con bovinos (ICA). |
| `bosque_ideam_inicial_ha` | FLOAT | Bosque natural del IDEAM al inicio del primer periodo. Fijo para cada municipio. |
| `n_celdas` | FLOAT | Celdas de 5 km del municipio en los paneles (las de al menos 50 ha de bosque base). |
| `area_grilla_ha` | FLOAT | n_celdas x 2.500 ha: área del municipio cubierta por esas celdas. |
| `nombre_departamento` | STRING | Departamento (tabla de municipios del Observatorio). |
| `nombre_municipio` | STRING | Nombre del municipio (tabla de municipios del Observatorio). |
| `region` | STRING | Región (tabla de municipios del Observatorio). |
| `ruralidad` | STRING | Categoría de ruralidad del DNP (tabla de municipios del Observatorio). |
| `tasa_def_ideam` | FLOAT | def_ideam_ha / bosque_ideam_ha: fracción del bosque natural deforestada en el año. |

### ALERTAS_GFW_CREDITO_MUNICIPIO_MES

Panel municipal por mes (2020-01 a 2026-06): alertas de GFW y crédito de FINAGRO.

**Una fila es** un municipio en un mes. **La escribe** el notebook 06, en `DATASET_DEFORESTACION`. Filas en la corrida de prueba: 87.594.

| Columna | Tipo | Qué es |
|---|---|---|
| `cod_dane` | STRING | Código DANE del municipio (5 dígitos). |
| `mes` | DATETIME | Primer día del mes. |
| `alertas_gfw_ha` | FLOAT | Hectáreas con alerta de Global Forest Watch en el mes. |
| `credito_mm` | FLOAT | Crédito de FINAGRO desembolsado en el municipio, todos los eslabones, en millones de pesos. |
| `credito_produccion_mm` | FLOAT | Crédito de FINAGRO del eslabón de producción, en millones de pesos. |
| `credito_arroz_produccion_mm` | FLOAT | Crédito de producción a la cadena del arroz, en millones de pesos. |
| `credito_cacao_produccion_mm` | FLOAT | Crédito de producción a la cadena del cacao, en millones de pesos. |
| `credito_cafe_produccion_mm` | FLOAT | Crédito de producción a la cadena del café, en millones de pesos. |
| `credito_ganaderia_produccion_mm` | FLOAT | Crédito de producción a la ganadería (doble propósito, leche y carne), en millones de pesos. |
| `credito_palma_produccion_mm` | FLOAT | Crédito de producción a la palma de aceite, en millones de pesos. |
| `region` | STRING | Región (tabla de municipios del Observatorio). |

### AREA_COSECHADA_EVA_MUNICIPIO_CULTIVO_ANIO

Hectáreas cosechadas por municipio, cultivo y año según la EVA, desde 2019.

**Una fila es** un municipio, un cultivo y un año. **La escribe** el notebook 06, en `DATASET_DEFORESTACION`. Filas en la corrida de prueba: 93.486.

| Columna | Tipo | Qué es |
|---|---|---|
| `cod_dane` | STRING | Código DANE del municipio (5 dígitos). |
| `anio` | INTEGER | Año. |
| `grupo_cultivo` | STRING | Grupo de cultivo de la EVA. |
| `cultivo` | STRING | Cultivo de la EVA. |
| `cosechada_ha` | FLOAT | Hectáreas cosechadas en el año (semestres sumados). |

## Tablas de paso

Un notebook no puede llamar a otro, así que los de modelos se pasan algunos resultados por tablas de
`DATASET_RESULTADOS`. Son tablas de paso: las borra el último notebook que las usa y, si la cadena se queda a
medias, vencen solas a los 7 días. Por eso no se documentan como tablas de datos.

| Las escribe | Tablas | Las lee | Las borra |
|---|---|---|---|
| 12 | `SEGMENTACION_MUNICIPIOS` | 17 | 17 |
| 13 | `GRADIENT_BOOSTING_MUESTRA` | 14, 15 y 16 | 16 |
| 13, 14 y 15 | `*_PREDICCIONES_PRUEBA`, `*_METRICAS`, `*_HIPERPARAMETROS`, `*_IMPORTANCIA_PERMUTACION_CLASIFICACION`, `*_LISTA_MUNICIPIOS_MAYOR_RIESGO` y `*_ESPECIFICACIONES`, con el prefijo `GRADIENT_BOOSTING_`, `RANDOM_FOREST_` o `REGRESION_LOGISTICA_` | 16 | 16 |
| 15 | `REGRESION_LOGISTICA_COEFICIENTES_INFERENCIA` y `REGRESION_LOGISTICA_EFECTOS_MULTIPLICATIVOS_MAGNITUD` | 16 | 16 |
| 16 | `COMPARACION_MODELOS_PROBABILIDADES_PRUEBA` y `COMPARACION_MODELOS_SINTESIS_OBJETIVOS` | 17 | 17 |

Por esto mismo, para volver a correr el 16 hay que correr antes el 13, el 14 y el 15; y para el 17, el 12 y el 16.
