# Diccionario de datos

Todas las tablas que crean los notebooks, con lo que es cada una y cada una de sus columnas.

- **Dónde quedan.** Las de deforestación (notebooks 01 a 06), en `DATASET_DEFORESTACION`; las de análisis y
  modelos (07 y 12 a 18), en `DATASET_RESULTADOS`. Por defecto los dos son `Cambio_Climatico`, en el proyecto
  `prueba-ofr`.
- **Los notebooks 08 a 11 no crean tablas.**
- **Los nombres van en mayúscula.** En BigQuery importan las mayúsculas: en SQL se escriben igual
  (`PANEL_IDEAM`, no `panel_ideam`).
- **Las tablas se reemplazan enteras** cada vez que se corre el notebook que las escribe. La única que crece es
  `REGISTRO_CARGAS`.

## Cómo leer las columnas

- **`orden_fila`.** Todas las tablas la tienen, y por eso no se repite abajo. Es la posición de la fila en la
  tabla que armó el notebook: BigQuery no guarda el orden de las filas, y algunos cálculos dependen de él. Para
  leer una tabla en su orden, `ORDER BY orden_fila`.
- **`index`, `level_0`, `level_1`.** Son las etiquetas de fila de la tabla del notebook, cuando no tenían nombre
  (por ejemplo, el nombre de la variable o del segmento).
- **Nombres simplificados.** En los notebooks muchas columnas de resultados tienen tildes, espacios o símbolos
  ("¿deforestó en 2025?"). En BigQuery quedan con un nombre simple (`deforesto_en_2025`) y el original se guarda
  en la descripción de la columna. Abajo va como "En el notebook".
- **Nombres que llevan un año.** Salen de los años de la celda 1 (`ANIO_INICIO`, `ANIO_FIN`, `ANIO_PRUEBA`).
  Aquí aparecen con los años por defecto: entrenar con 2021-2024 y probar con 2025. Si se cambian los años, cambia el
  nombre: con `ANIO_PRUEBA = 2026`, `deforesto_en_2025` pasa a ser `deforesto_en_2026`. La excepción es
  `prueba_temporal_2025`, que se queda con ese nombre aunque se cambien los años.
- **Año de prueba** es `ANIO_PRUEBA`; **años de entrenamiento**, de `ANIO_INICIO` a `ANIO_PRUEBA - 1`.
- **Los modelos tienen dos partes.** «¿Deforesta?» predice si el municipio tiene alguna deforestación en el
  año. «¿Cuánto?» predice la tasa, y solo se entrena con los municipios que sí deforestaron. Los modelos
  trabajan con los municipios rurales y rurales dispersos que tienen bosque.
- **Unidades.** Hectáreas (ha), millones de pesos corrientes (M$) y porcentajes de 0 a 100, salvo donde se dice
  que algo va de 0 a 1.
- **"Filas en la corrida de prueba"** (solo en las tablas de deforestación) es cuántas filas tuvo la tabla en
  la prueba de noviembre de 2026. Cambia cuando se actualizan los datos.

## Las tablas

| Tabla | Notebook | Una fila es |
|---|---|---|
| [`GRILLA`](#grilla) | 01 | una celda de 5 × 5 km |
| [`BOSQUE_BASE`](#bosque_base) | 02 | una celda |
| [`PANEL_HANSEN`](#panel_hansen) | 02 | una celda en un año |
| [`PANEL_DEFORESTACION_COLOMBIA`](#panel_deforestacion_colombia) | 03 | una celda en un mes |
| [`PANEL_IDEAM`](#panel_ideam) | 04 | una celda en un periodo de dos años |
| [`PANEL_DTD`](#panel_dtd) | 05 | una celda en un trimestre |
| [`MUNICIPIO_ANIO`](#municipio_anio) | 06 | un municipio en un año |
| [`MUNICIPIO_MES`](#municipio_mes) | 06 | un municipio en un mes |
| [`MUNICIPIO_CULTIVO_ANIO`](#municipio_cultivo_anio) | 06 | un municipio, un cultivo y un año |
| [`REGISTRO_CARGAS`](#registro_cargas) | 01 a 06 | una carga de una tabla |
| [`ESTADISTICAS_DESCRIPTIVAS`](#estadisticas_descriptivas) | 07 | una variable de una tabla |
| [`SEGMENTACION_DEFORESTACION_POR_SEGMENTO`](#segmentacion_deforestacion_por_segmento) | 12 | un segmento |
| [`SEGMENTACION_ELECCION_K`](#segmentacion_eleccion_k) | 12 | un número de segmentos (k de 2 a 10) |
| [`SEGMENTACION_ESTABILIDAD`](#segmentacion_estabilidad) | 12 | un segmento |
| [`SEGMENTACION_MUNICIPIOS`](#segmentacion_municipios) | 12 | un municipio rural |
| [`SEGMENTACION_MUNICIPIOS_CLUSTERS`](#segmentacion_municipios_clusters) | 12 | un municipio rural |
| [`SEGMENTACION_MUNICIPIOS_SOLIDEZ_PERFILES`](#segmentacion_municipios_solidez_perfiles) | 12 | un segmento |
| [`SEGMENTACION_PERFIL_MEDIANAS`](#segmentacion_perfil_medianas) | 12 | una de las variables de la segmentación |
| [`GRADIENT_BOOSTING_ESPECIFICACIONES`](#gradient_boosting_especificaciones) | 13 | una variante del modelo |
| [`GRADIENT_BOOSTING_HIPERPARAMETROS`](#gradient_boosting_hiperparametros) | 13 | un hiperparámetro |
| [`GRADIENT_BOOSTING_IMPORTANCIA_BLOQUES`](#gradient_boosting_importancia_bloques) | 13 | un grupo de variables |
| [`GRADIENT_BOOSTING_IMPORTANCIA_PERMUTACION_CLASIFICACION`](#gradient_boosting_importancia_permutacion_clasificacion) | 13 | una variable explicativa |
| [`GRADIENT_BOOSTING_IMPORTANCIA_PERMUTACION_REGRESION`](#gradient_boosting_importancia_permutacion_regresion) | 13 | una variable explicativa |
| [`GRADIENT_BOOSTING_LISTA_MUNICIPIOS_MAYOR_RIESGO`](#gradient_boosting_lista_municipios_mayor_riesgo) | 13 | un municipio |
| [`GRADIENT_BOOSTING_METRICAS`](#gradient_boosting_metricas) | 13 | una métrica de una de las dos partes del modelo |
| [`GRADIENT_BOOSTING_MUESTRA`](#gradient_boosting_muestra) | 13 | un municipio rural en un año (ANIO_INICIO a ANIO_PRUEBA) |
| [`GRADIENT_BOOSTING_PREDICCIONES_PRUEBA`](#gradient_boosting_predicciones_prueba) | 13 | un municipio rural en el año de prueba |
| [`RANDOM_FOREST_ESPECIFICACIONES`](#random_forest_especificaciones) | 14 | una variante del modelo |
| [`RANDOM_FOREST_HIPERPARAMETROS`](#random_forest_hiperparametros) | 14 | un hiperparámetro |
| [`RANDOM_FOREST_IMPORTANCIA_BLOQUES`](#random_forest_importancia_bloques) | 14 | un grupo de variables |
| [`RANDOM_FOREST_IMPORTANCIA_PERMUTACION_CLASIFICACION`](#random_forest_importancia_permutacion_clasificacion) | 14 | una variable explicativa |
| [`RANDOM_FOREST_IMPORTANCIA_PERMUTACION_REGRESION`](#random_forest_importancia_permutacion_regresion) | 14 | una variable explicativa |
| [`RANDOM_FOREST_LISTA_MUNICIPIOS_MAYOR_RIESGO`](#random_forest_lista_municipios_mayor_riesgo) | 14 | un municipio |
| [`RANDOM_FOREST_METRICAS`](#random_forest_metricas) | 14 | una métrica de una de las dos partes del modelo |
| [`RANDOM_FOREST_PREDICCIONES_DEPARTAMENTOS_FUERA`](#random_forest_predicciones_departamentos_fuera) | 14 | un municipio rural en un año (ANIO_INICIO a ANIO_PRUEBA) |
| [`RANDOM_FOREST_PREDICCIONES_PRUEBA`](#random_forest_predicciones_prueba) | 14 | un municipio rural en el año de prueba |
| [`RANDOM_FOREST_SINTESIS_OBJETIVOS`](#random_forest_sintesis_objetivos) | 14 | un objetivo o criterio |
| [`REGRESION_LOGISTICA_COEFICIENTES_INFERENCIA`](#regresion_logistica_coeficientes_inferencia) | 15 | una variable del modelo final |
| [`REGRESION_LOGISTICA_EFECTOS_MARGINALES`](#regresion_logistica_efectos_marginales) | 15 | una variable del modelo final |
| [`REGRESION_LOGISTICA_EFECTOS_MULTIPLICATIVOS_MAGNITUD`](#regresion_logistica_efectos_multiplicativos_magnitud) | 15 | una variable del modelo final |
| [`REGRESION_LOGISTICA_ESPECIFICACIONES`](#regresion_logistica_especificaciones) | 15 | una variante del modelo |
| [`REGRESION_LOGISTICA_HIPERPARAMETROS`](#regresion_logistica_hiperparametros) | 15 | un hiperparámetro |
| [`REGRESION_LOGISTICA_IMPORTANCIA_BLOQUES`](#regresion_logistica_importancia_bloques) | 15 | un grupo de variables |
| [`REGRESION_LOGISTICA_IMPORTANCIA_PERMUTACION_CLASIFICACION`](#regresion_logistica_importancia_permutacion_clasificacion) | 15 | una variable explicativa |
| [`REGRESION_LOGISTICA_IMPORTANCIA_PERMUTACION_REGRESION`](#regresion_logistica_importancia_permutacion_regresion) | 15 | una variable explicativa |
| [`REGRESION_LOGISTICA_LISTA_MUNICIPIOS_MAYOR_RIESGO`](#regresion_logistica_lista_municipios_mayor_riesgo) | 15 | un municipio |
| [`REGRESION_LOGISTICA_METRICAS`](#regresion_logistica_metricas) | 15 | una métrica de una de las dos partes del modelo |
| [`REGRESION_LOGISTICA_PREDICCIONES_DEPARTAMENTOS_FUERA`](#regresion_logistica_predicciones_departamentos_fuera) | 15 | un municipio rural en un año (ANIO_INICIO a ANIO_PRUEBA) |
| [`REGRESION_LOGISTICA_PREDICCIONES_PRUEBA`](#regresion_logistica_predicciones_prueba) | 15 | un municipio rural en el año de prueba |
| [`REGRESION_LOGISTICA_PRUEBAS_WALD`](#regresion_logistica_pruebas_wald) | 15 | un grupo de variables |
| [`REGRESION_LOGISTICA_SINTESIS_OBJETIVOS`](#regresion_logistica_sintesis_objetivos) | 15 | un objetivo o criterio |
| [`REGRESION_LOGISTICA_VIF_ITERATIVO`](#regresion_logistica_vif_iterativo) | 15 | una variable explicativa |
| [`COMPARACION_MODELOS_ACTIVIDADES`](#comparacion_modelos_actividades) | 16 | una actividad productiva |
| [`COMPARACION_MODELOS_CDM5`](#comparacion_modelos_cdm5) | 16 | un modelo con una prueba |
| [`COMPARACION_MODELOS_COBERTURA`](#comparacion_modelos_cobertura) | 16 | una fracción de municipios revisados |
| [`COMPARACION_MODELOS_ERRORES_CUARTIL_BOSQUE`](#comparacion_modelos_errores_cuartil_bosque) | 16 | un modelo en un cuartil de bosque |
| [`COMPARACION_MODELOS_FALSOS_NEGATIVOS_HECTAREAS`](#comparacion_modelos_falsos_negativos_hectareas) | 16 | una medida |
| [`COMPARACION_MODELOS_IMPORTANCIA_15_VARIABLES`](#comparacion_modelos_importancia_15_variables) | 16 | una variable explicativa |
| [`COMPARACION_MODELOS_IMPORTANCIA_INTERVENCION`](#comparacion_modelos_importancia_intervencion) | 16 | una variable de intervención |
| [`COMPARACION_MODELOS_METRICAS`](#comparacion_modelos_metricas) | 16 | una métrica en una de las dos pruebas |
| [`COMPARACION_MODELOS_MISMO_NUMERO_MARCADOS`](#comparacion_modelos_mismo_numero_marcados) | 16 | un modelo |
| [`COMPARACION_MODELOS_ODDS_CUARTIL_BOSQUE`](#comparacion_modelos_odds_cuartil_bosque) | 16 | un cuartil de bosque |
| [`COMPARACION_MODELOS_ODDS_MODELOS`](#comparacion_modelos_odds_modelos) | 16 | un modelo con una regla de marcado |
| [`COMPARACION_MODELOS_ODDS_TRAMOS_RIESGO`](#comparacion_modelos_odds_tramos_riesgo) | 16 | un modelo en un tramo de riesgo |
| [`COMPARACION_MODELOS_PROBABILIDADES_PRUEBA`](#comparacion_modelos_probabilidades_prueba) | 16 | un municipio rural en el año de prueba |
| [`COMPARACION_MODELOS_REFERENCIAS_TAMANO`](#comparacion_modelos_referencias_tamano) | 16 | una referencia de tamaño o un modelo |
| [`COMPARACION_MODELOS_SINTESIS_OBJETIVOS`](#comparacion_modelos_sintesis_objetivos) | 16 | un objetivo o criterio |
| [`COMPARACION_MODELOS_TECNICAS_BUSQUEDA`](#comparacion_modelos_tecnicas_busqueda) | 16 | un modelo |
| [`COMPARACION_MODELOS_UMBRALES`](#comparacion_modelos_umbrales) | 16 | un modelo con un umbral |
| [`SEGMENTOS_Y_RIESGO_AUC_DENTRO`](#segmentos_y_riesgo_auc_dentro) | 17 | un segmento con un modelo |
| [`SEGMENTOS_Y_RIESGO_CONCENTRACION`](#segmentos_y_riesgo_concentracion) | 17 | un segmento |
| [`SEGMENTOS_Y_RIESGO_CREDITO`](#segmentos_y_riesgo_credito) | 17 | un segmento |
| [`SEGMENTOS_Y_RIESGO_ERRORES`](#segmentos_y_riesgo_errores) | 17 | un segmento |
| [`SEGMENTOS_Y_RIESGO_MUNICIPIOS`](#segmentos_y_riesgo_municipios) | 17 | un municipio rural |
| [`SEGMENTOS_Y_RIESGO_ODDS`](#segmentos_y_riesgo_odds) | 17 | un segmento |
| [`SEGMENTOS_Y_RIESGO_SINTESIS`](#segmentos_y_riesgo_sintesis) | 17 | un segmento |
| [`DEMO_LANDSAT_CALIBRACION_UMBRAL`](#demo_landsat_calibracion_umbral) | 18 | un umbral de dNBR |
| [`DEMO_LANDSAT_COMPARACION_CELDAS`](#demo_landsat_comparacion_celdas) | 18 | una celda de 5 km de la zona piloto |
| [`DEMO_LANDSAT_CURVA_OBSERVABILIDAD`](#demo_landsat_curva_observabilidad) | 18 | un nivel de observaciones limpias |
| [`DEMO_LANDSAT_DETECCION_POR_PARCHE`](#demo_landsat_deteccion_por_parche) | 18 | un tramo de tamaño de claro en una dirección |
| [`DEMO_LANDSAT_DIAGNOSTICO_NUBES`](#demo_landsat_diagnostico_nubes) | 18 | la zona piloto (una fila) |
| [`DEMO_LANDSAT_EVALUACION_VALIDACION`](#demo_landsat_evaluacion_validacion) | 18 | el umbral elegido (una fila) |
| [`DEMO_LANDSAT_FECHA_EFECTIVA`](#demo_landsat_fecha_efectiva) | 18 | una medida de fecha |
| [`DEMO_LANDSAT_MUESTRA_VALIDACION_VISUAL`](#demo_landsat_muestra_validacion_visual) | 18 | un punto para revisar a mano |
| [`DEMO_LANDSAT_REFERENCIA_ENTRE_PRODUCTOS`](#demo_landsat_referencia_entre_productos) | 18 | un par de productos |


## 01 a 06 · Carga de las tablas de deforestación


### GRILLA

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

### BOSQUE_BASE

Bosque al empezar el análisis, por celda de la grilla, según Hansen GFC-2025-v1.13: píxeles con dosel de al menos 30 % en 2000 y sin pérdida hasta 2019, reproyectados a 25 m. Es el denominador común de los paneles de deforestación.

**Una fila es** una celda. **La escribe** el notebook 02, en `DATASET_DEFORESTACION`. Filas en la corrida de prueba: 45.621.

| Columna | Tipo | Qué es |
|---|---|---|
| `cell_id` | STRING | Identificador de la celda (tabla grilla). |
| `bosque_base_ha` | FLOAT | Hectáreas de bosque al empezar 2020, según Hansen. |

### PANEL_HANSEN

Pérdida anual de cobertura arbórea por celda de 5 km, según Hansen GFC-2025-v1.13. Mide pérdida de dosel por cualquier causa, no deforestación oficial: sobre Colombia equivale a 1,5-2,5 veces la cifra del IDEAM. Solo celdas con al menos 50 ha de bosque base.

**Una fila es** una celda en un año. **La escribe** el notebook 02, en `DATASET_DEFORESTACION`. Filas en la corrida de prueba: 267.696.

| Columna | Tipo | Qué es |
|---|---|---|
| `cell_id` | STRING | Identificador de la celda (tabla grilla). |
| `anio` | INTEGER | Año de la pérdida (año calendario). |
| `perdida_ha` | FLOAT | Hectáreas que perdieron el dosel ese año, entre las que tenían al menos 30 % de dosel en 2000. |
| `lon` | FLOAT | Longitud del centro de la celda. |
| `lat` | FLOAT | Latitud del centro de la celda. |
| `departamento` | STRING | Departamento del centro de la celda. |
| `bosque_base_ha` | FLOAT | Bosque al empezar el análisis (tabla bosque_base). Es el denominador de esta tabla. |
| `cod_dane` | STRING | Código DANE del municipio del centro de la celda. |
| `municipio` | STRING | Nombre del municipio, solo para leer. |

### PANEL_DEFORESTACION_COLOMBIA

Alertas de disturbio de la vegetación de Global Forest Watch (gfw_integrated_alerts, confianza high y highest) por celda de 5 km y mes, 2020-01 a 2026-07. No es deforestación oficial: no distingue la causa. Solo celdas con al menos 50 ha de bosque base.

**Una fila es** una celda en un mes. **La escribe** el notebook 03, en `DATASET_DEFORESTACION`. Filas en la corrida de prueba: 3.524.664.

| Columna | Tipo | Qué es |
|---|---|---|
| `cell_id` | STRING | Identificador de la celda (tabla grilla). |
| `periodo` | DATETIME | Primer día del mes. |
| `area_def_ha` | FLOAT | Hectáreas con alerta de alta confianza en el mes. |
| `lon` | FLOAT | Longitud del centro de la celda. |
| `lat` | FLOAT | Latitud del centro de la celda. |
| `bosque_base_ha` | FLOAT | Bosque al empezar el análisis, según Hansen (tabla bosque_base). |
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

### PANEL_IDEAM

Deforestación oficial por celda de 5 km y periodo, según las capas de cambio de la superficie cubierta por bosque natural del IDEAM (SMByC). Cada periodo compara dos años y su cifra corresponde al año final. Solo celdas con al menos 50 ha de bosque base.

**Una fila es** una celda en un periodo de dos años. **La escribe** el notebook 04, en `DATASET_DEFORESTACION`. Filas en la corrida de prueba: 223.080.

| Columna | Tipo | Qué es |
|---|---|---|
| `cell_id` | STRING | Identificador de la celda (tabla grilla). |
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
| `bosque_base_ha` | FLOAT | Bosque según Hansen (tabla bosque_base); no es el denominador de esta tabla. |
| `cod_dane` | STRING | Código DANE del municipio del centro de la celda. |
| `municipio` | STRING | Nombre del municipio, solo para leer. |

### PANEL_DTD

Detecciones tempranas de deforestación del IDEAM (SMByC) por celda de 5 km y trimestre. Cuenta puntos, no hectáreas, y los conteos NO son comparables entre trimestres: sirve para priorizar lugares dentro de un mismo trimestre. Solo celdas con al menos 50 ha de bosque base.

**Una fila es** una celda en un trimestre. **La escribe** el notebook 05, en `DATASET_DEFORESTACION`. Filas en la corrida de prueba: 1.160.016.

| Columna | Tipo | Qué es |
|---|---|---|
| `cell_id` | STRING | Identificador de la celda (tabla grilla). |
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
| `bosque_base_ha` | FLOAT | Bosque según Hansen (tabla bosque_base). |
| `cod_dane` | STRING | Código DANE del municipio del centro de la celda. |
| `municipio` | STRING | Nombre del municipio, solo para leer. |

### MUNICIPIO_ANIO

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

### MUNICIPIO_MES

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

### MUNICIPIO_CULTIVO_ANIO

Hectáreas cosechadas por municipio, cultivo y año según la EVA, desde 2019.

**Una fila es** un municipio, un cultivo y un año. **La escribe** el notebook 06, en `DATASET_DEFORESTACION`. Filas en la corrida de prueba: 93.486.

| Columna | Tipo | Qué es |
|---|---|---|
| `cod_dane` | STRING | Código DANE del municipio (5 dígitos). |
| `anio` | INTEGER | Año. |
| `grupo_cultivo` | STRING | Grupo de cultivo de la EVA. |
| `cultivo` | STRING | Cultivo de la EVA. |
| `cosechada_ha` | FLOAT | Hectáreas cosechadas en el año (semestres sumados). |

### REGISTRO_CARGAS

Bitácora de las cargas: cada vez que los notebooks 01 a 06 escriben una tabla de deforestación, dejan aquí una fila con qué cargaron, de dónde y cuándo. No se borra: se le van agregando filas.

**Una fila es** una carga de una tabla. **La escribe** el notebook 01 a 06, en `DATASET_DEFORESTACION`. Filas en la corrida de prueba: 24.

| Columna | Tipo | Qué es |
|---|---|---|
| `tabla` | STRING | Tabla que se cargó. |
| `filas` | INTEGER | Filas que quedaron en la tabla. |
| `fuente` | STRING | Fuente de los datos (por ejemplo, la versión de Hansen o el servicio del DANE). |
| `parametros` | STRING | Parámetros de la carga, en JSON (años, versión, tamaño de celda…). |
| `notebook` | STRING | Notebook que hizo la carga. |
| `cargado_en` | TIMESTAMP | Fecha y hora de la carga (UTC). |

## 07 · Estadísticas descriptivas


### ESTADISTICAS_DESCRIPTIVAS

Estadísticas descriptivas de las variables numéricas principales de las tablas de deforestación y de las del Observatorio (notebook 07). Cuartiles con el método 7 de R; asimetría y curtosis como psych::describe (type = 3).

**Una fila es** una variable de una tabla. **La escribe** el notebook 07, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `Grupo` | STRING | Grupo de la tabla: Deforestación, Crédito o Producción agropecuaria. |
| `Fuente` | STRING | Fuente de los datos. |
| `Tabla` | STRING | Tabla de donde sale la variable. |
| `una_fila_es` | STRING | Qué es una fila en esa tabla (celda × mes, crédito, municipio × año…). |
| `filas_de_la_tabla` | INTEGER | Filas de esa tabla. |
| `Variable` | STRING | Variable que se describe. |
| `Unidad` | STRING | Unidad de la variable. Los pesos van en millones de pesos corrientes. |
| `n` | INTEGER | Número de valores que no faltan. |
| `Faltantes` | INTEGER | Número de valores vacíos. |
| `ceros` | FLOAT | Porcentaje de valores que son exactamente cero. |
| `Media` | FLOAT | Promedio. |
| `desv_est` | FLOAT | Desviación estándar (con n - 1). |
| `min` | FLOAT | Valor mínimo. |
| `Q1` | FLOAT | Primer cuartil (método 7, el de R). |
| `Mediana` | FLOAT | Mediana. |
| `Q3` | FLOAT | Tercer cuartil (método 7). |
| `max` | FLOAT | Valor máximo. |
| `asimetria` | FLOAT | Asimetría: 0 si la distribución es simétrica; positiva si tiene una cola larga hacia los valores altos (fórmula de psych::describe, type = 3). |
| `Curtosis` | FLOAT | Curtosis en exceso: 0 para una normal; positiva si tiene colas más pesadas (fórmula de psych::describe, type = 3). |

## 12 · Segmentación


### SEGMENTACION_DEFORESTACION_POR_SEGMENTO

Deforestación de cada segmento.

**Una fila es** un segmento. **La escribe** el notebook 12, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `index` | STRING | Segmento. |
| `municipios` | INTEGER | Número de municipios. |
| `de_los_municipios` | FLOAT | % de los municipios rurales que son del segmento. |
| `de_la_deforestacion_2021_2025` | FLOAT | % de las hectáreas deforestadas de ANIO_INICIO a ANIO_FIN (todos los municipios rurales) que caen en el segmento. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `tasa_mediana_del_bosque_por_ano` | FLOAT | Mediana, entre los municipios del segmento, de la tasa de deforestación (% del bosque por año). |
| `mediana_de_ha_deforestadas_por_100_km2_al_ano` | FLOAT | Mediana de las hectáreas deforestadas por 100 km² al año. |
| `mediana_de_alertas_gfw_ha_por_100_km2_al_ano` | FLOAT | Mediana de las hectáreas con alerta de GFW por 100 km² al año. |
| `de_la_deforestacion_del_segmento_en_sus_5_municipios_con_mas` | FLOAT | % de la deforestación del segmento que se concentra en sus 5 municipios que más deforestan. |
| `los_3_que_mas_deforestan` | STRING | Los tres municipios del segmento que más hectáreas deforestan. |

### SEGMENTACION_ELECCION_K

Codo, silueta y estabilidad para cada número de segmentos.

**Una fila es** un número de segmentos (k de 2 a 10). **La escribe** el notebook 12, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `k` | INTEGER | Número de segmentos. |
| `inercia` | FLOAT | Suma de distancias de cada municipio al centro de su segmento (el codo). Menor es mejor. |
| `silueta` | FLOAT | Silueta media: qué tan separado está cada municipio de los otros segmentos (-1 a 1). |
| `silueta_sin_estructura` | FLOAT | Silueta media con las mismas variables barajadas, sin estructura (promedio de los barajados). La silueta real tiene que superarla. |
| `silueta_sin_estructura_max` | FLOAT | La mayor silueta de esos datos barajados. |
| `estabilidad_ari_medio` | FLOAT | Estabilidad por bootstrap: índice de Rand ajustado medio entre la segmentación de todos los datos y la de los remuestreos. 1 es idéntica; 0, como al azar. |
| `estabilidad_ari_percentil_10` | FLOAT | Percentil 10 de ese índice. |
| `segmento_mas_pequeno` | INTEGER | Municipios del segmento más pequeño. |

### SEGMENTACION_ESTABILIDAD

Estabilidad por bootstrap de cada segmento.

**Una fila es** un segmento. **La escribe** el notebook 12, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `index` | STRING | Segmento. |
| `municipios` | INTEGER | Número de municipios. |
| `jaccard_medio_bootstrap` | FLOAT | Jaccard medio entre el segmento y el más parecido en 200 remuestreos bootstrap (Hennig, 2007). 1 es idéntico. |
| `lectura` | STRING | Lectura del Jaccard: de inestable a muy estable. |

### SEGMENTACION_MUNICIPIOS

Los municipios rurales y rurales dispersos con su segmento, las diez variables de la segmentación y su deforestación. Las variables son promedios de ANIO_INICIO a ANIO_FIN - 1, salvo el bosque, que es de ANIO_INICIO.

**Una fila es** un municipio rural. **La escribe** el notebook 12, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `cod_dane` | STRING | Código DANE del municipio (5 dígitos). Llave para cruzar con las demás tablas. |
| `nombre` | STRING | Nombre del municipio. |
| `departamento` | STRING | Departamento. |
| `region` | STRING | Región (tabla de municipios del Observatorio). |
| `ruralidad` | STRING | Categoría de ruralidad del DNP: Rural o Rural disperso. |
| `segmento` | INTEGER | Número del segmento (1 a 8). |
| `nombre_segmento` | STRING | Nombre del segmento, con su número. |
| `bosque_del_municipio_2021` | FLOAT | Bosque natural del IDEAM en ANIO_INICIO, en % del área del municipio. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `bovinos_por_km2` | FLOAT | Bovinos por km² (promedio ANIO_INICIO a ANIO_FIN - 1). |
| `bovinos_por_finca` | FLOAT | Bovinos por finca: tamaño medio del hato. |
| `area_cosechada_del_municipio` | FLOAT | Área cosechada de todos los cultivos, en % del área del municipio. |
| `palma_del_area_cosechada` | FLOAT | Palma, en % del área cosechada. |
| `cacao_del_area_cosechada` | FLOAT | Cacao, en % del área cosechada. |
| `cafe_del_area_cosechada` | FLOAT | Café, en % del área cosechada. |
| `arroz_del_area_cosechada` | FLOAT | Arroz, en % del área cosechada. |
| `credito_de_produccion_m_por_km2_al_ano` | FLOAT | Crédito de producción de FINAGRO, en millones de pesos por km² al año. |
| `credito_ganadero_del_credito_de_produccion` | FLOAT | Crédito ganadero, en % del crédito de producción. |
| `tasa_de_deforestacion_del_bosque_por_ano` | FLOAT | Tasa media de deforestación del IDEAM, en % del bosque por año (ANIO_INICIO a ANIO_FIN). No entra en la segmentación: sirve para comparar los segmentos. |
| `deforestacion_ideam_ha_por_100_km2_al_ano` | FLOAT | Hectáreas deforestadas según el IDEAM por cada 100 km², promedio por año. |
| `alertas_gfw_ha_por_100_km2_al_ano` | FLOAT | Hectáreas con alerta de GFW por cada 100 km², promedio por año. |
| `deforestacion_ideam_2021_2025_ha` | FLOAT | Hectáreas deforestadas según el IDEAM, sumadas de ANIO_INICIO a ANIO_FIN. El año del nombre sale de la celda 1: cambia si cambian los años. |

### SEGMENTACION_MUNICIPIOS_CLUSTERS

Un municipio por fila con su cluster, el nombre y la descripción del cluster y su deforestación, de más a menos deforestación dentro de cada cluster.

**Una fila es** un municipio rural. **La escribe** el notebook 12, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `cod_dane` | STRING | Código DANE del municipio (5 dígitos). Llave para cruzar con las demás tablas. |
| `municipio` | STRING | Nombre del municipio. |
| `departamento` | STRING | Departamento. |
| `cluster` | INTEGER | Número del segmento (1 a 8). |
| `nombre_del_cluster` | STRING | Nombre del segmento. |
| `descripcion_del_cluster` | STRING | Descripción del segmento en una o dos frases. |
| `deforestacion_ideam_2021_2025_ha` | FLOAT | Hectáreas deforestadas según el IDEAM, sumadas de ANIO_INICIO a ANIO_FIN. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `deforestacion_ideam_ha_por_100_km2_al_ano` | FLOAT | Hectáreas deforestadas según el IDEAM por cada 100 km², promedio por año. |
| `tasa_de_deforestacion_del_bosque_por_ano` | FLOAT | Tasa media de deforestación del IDEAM, en % del bosque por año (ANIO_INICIO a ANIO_FIN). No entra en la segmentación: sirve para comparar los segmentos. |

### SEGMENTACION_MUNICIPIOS_SOLIDEZ_PERFILES

Qué tan bien separado está cada perfil (silueta).

**Una fila es** un segmento. **La escribe** el notebook 12, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `segmento` | STRING | Segmento. |
| `municipios` | INTEGER | Número de municipios. |
| `silueta_media` | FLOAT | Silueta media de los municipios del segmento. |
| `con_silueta_negativa` | FLOAT | % de sus municipios con silueta negativa: más cerca del segmento vecino que del suyo. |
| `segmento_vecino` | STRING | Segmento vecino más frecuente entre sus municipios. |
| `distancia_entre_centros` | FLOAT | Distancia entre el centro del segmento y el del vecino, en las variables estandarizadas. |
| `jaccard_medio_bootstrap` | FLOAT | Jaccard medio por bootstrap (ver SEGMENTACION_ESTABILIDAD). |
| `lectura_de_la_silueta` | STRING | débil (0,25 o menos), razonable (hasta 0,50) o fuerte, según Kaufman y Rousseeuw (1990). |

### SEGMENTACION_PERFIL_MEDIANAS

Mediana de cada variable de la segmentación (y de la deforestación) en cada segmento: es el perfil de cada segmento.

**Una fila es** una de las variables de la segmentación. **La escribe** el notebook 12, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `index` | STRING | Variable. |
| `c_1_selva_sin_actividad_agropecuaria` | FLOAT | Mediana de la variable en el segmento que nombra la columna. En el notebook: «1. Selva sin actividad agropecuaria». |
| `c_2_bosque_con_ganaderia_dispersa` | FLOAT | Mediana de la variable en el segmento que nombra la columna. En el notebook: «2. Bosque con ganadería dispersa». |
| `c_3_cacao_y_bosque` | FLOAT | Mediana de la variable en el segmento que nombra la columna. En el notebook: «3. Cacao y bosque». |
| `c_4_cafetero` | FLOAT | Mediana de la variable en el segmento que nombra la columna. En el notebook: «4. Cafetero». |
| `c_5_minifundio_con_mucho_credito` | FLOAT | Mediana de la variable en el segmento que nombra la columna. En el notebook: «5. Minifundio con mucho crédito». |
| `c_6_palmero` | FLOAT | Mediana de la variable en el segmento que nombra la columna. En el notebook: «6. Palmero». |
| `c_7_ganadero` | FLOAT | Mediana de la variable en el segmento que nombra la columna. En el notebook: «7. Ganadero». |
| `c_8_arrocero` | FLOAT | Mediana de la variable en el segmento que nombra la columna. En el notebook: «8. Arrocero». |

## 13 · Gradient boosting


### GRADIENT_BOOSTING_ESPECIFICACIONES

Comparación de variantes del modelo: qué aportan las 15 variables frente a lo estructural, la persistencia y la región.

**Una fila es** una variante del modelo. **La escribe** el notebook 13, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `especificacion` | STRING | Variante del modelo: persistencia (si deforestó el año anterior, sin modelo), A. solo bosque y área, B. las 15 variables, C. con persistencia, D. con región, y las demás que se prueban en el notebook. |
| `AUC` | FLOAT | AUC en el año de prueba. |
| `exactitud_balanceada` | FLOAT | Exactitud balanceada (umbral 0,5) en el año de prueba. |
| `AP` | FLOAT | Precisión promedio en el año de prueba. |

### GRADIENT_BOOSTING_HIPERPARAMETROS

Hiperparámetros elegidos para el gradient boosting en la búsqueda con validación por departamento.

**Una fila es** un hiperparámetro. **La escribe** el notebook 13, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `index` | STRING | Hiperparámetro. La fila «metrica» es el puntaje de validación cruzada de la combinación elegida (exactitud balanceada en «¿deforesta?», R² en «¿cuánto?»), y «n_estimators», el número de árboles. |
| `clasificacion` | STRING | Valor elegido para la parte «¿deforesta?». Va como texto porque mezcla números y palabras. |
| `regresion` | FLOAT | Valor elegido para la parte «¿cuánto?». |

### GRADIENT_BOOSTING_IMPORTANCIA_BLOQUES

Importancia por permutación de grupos de variables (bloques temáticos y actividades productivas) en la parte «¿deforesta?».

**Una fila es** un grupo de variables. **La escribe** el notebook 13, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `level_0` | STRING | Cómo se agrupan las variables: bloques (temáticos) o actividades (productivas). |
| `grupo` | STRING | Grupo de variables que se barajan juntas. |
| `n_variables` | INTEGER | Número de variables del grupo. |
| `caida` | FLOAT | Caída media del AUC en el año de prueba al barajar juntas las variables del grupo (30 repeticiones). |
| `desv` | FLOAT | Desviación estándar de esa caída. |
| `p_valor` | FLOAT | Valor p de la prueba t de las 30 repeticiones contra cero. |

### GRADIENT_BOOSTING_IMPORTANCIA_PERMUTACION_CLASIFICACION

Importancia por permutación de cada variable en la parte «¿deforesta?», medida en el año de prueba, con dos valores p.

**Una fila es** una variable explicativa. **La escribe** el notebook 13, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `variable` | STRING | Variable explicativa. |
| `clave` | STRING | Nombre corto de la variable, el de las columnas de GRADIENT_BOOSTING_MUESTRA. |
| `importancia_media` | FLOAT | Caída media del AUC en el año de prueba al barajar la variable (30 repeticiones). Mayor = más importante. |
| `desv` | FLOAT | Desviación estándar de esa caída entre las 30 repeticiones. |
| `p_valor` | FLOAT | Valor p de la prueba t de las 30 repeticiones contra cero. Solo recoge el azar del barajado, así que es optimista. |
| `ic95_inf_territorio` | FLOAT | Límite inferior del intervalo de 95 % de la importancia, con bootstrap por departamento (se remuestrean departamentos completos). |
| `ic95_sup_territorio` | FLOAT | Límite superior de ese intervalo. |
| `p_territorio` | FLOAT | Valor p con el bootstrap por departamento. Es la prueba que se usa para contar variables significativas. |
| `p_holm` | FLOAT | p_territorio corregido por Holm entre las variables que usa el modelo. Vacío si el modelo no usa la variable. |
| `tipo` | STRING | estructural (bosque y área del municipio) o intervención (las otras 13 variables: crédito, cultivos y ganado). |

### GRADIENT_BOOSTING_IMPORTANCIA_PERMUTACION_REGRESION

Importancia por permutación de cada variable en la parte «¿cuánto?», medida en el año de prueba entre los municipios que deforestaron.

**Una fila es** una variable explicativa. **La escribe** el notebook 13, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `variable` | STRING | Variable explicativa. |
| `clave` | STRING | Nombre corto de la variable, el de las columnas de GRADIENT_BOOSTING_MUESTRA. |
| `importancia_media` | FLOAT | Caída media del R² (solo municipios que deforestaron) en el año de prueba al barajar la variable (30 repeticiones). Mayor = más importante. |
| `desv` | FLOAT | Desviación estándar de esa caída entre las 30 repeticiones. |
| `p_valor` | FLOAT | Valor p de la prueba t de las 30 repeticiones contra cero. Solo recoge el azar del barajado, así que es optimista. |
| `ic95_inf_territorio` | FLOAT | Límite inferior del intervalo de 95 % de la importancia, con bootstrap por departamento (se remuestrean departamentos completos). |
| `ic95_sup_territorio` | FLOAT | Límite superior de ese intervalo. |
| `p_territorio` | FLOAT | Valor p con el bootstrap por departamento. Es la prueba que se usa para contar variables significativas. |
| `p_holm` | FLOAT | p_territorio corregido por Holm entre las variables que usa el modelo. Vacío si el modelo no usa la variable. |
| `tipo` | STRING | estructural (bosque y área del municipio) o intervención (las otras 13 variables: crédito, cultivos y ganado). |

### GRADIENT_BOOSTING_LISTA_MUNICIPIOS_MAYOR_RIESGO

Los 15 municipios con mayor probabilidad predicha en el año de prueba, con las variables que más empujaron su predicción.

**Una fila es** un municipio. **La escribe** el notebook 13, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `municipio` | STRING | Municipio. |
| `departamento` | STRING | Departamento. |
| `riesgo_predicho` | FLOAT | Probabilidad predicha de deforestar en el año de prueba. |
| `deforesto_en_2025` | STRING | Sí o No: si de verdad deforestó ese año. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `tasa_real_2025` | FLOAT | Tasa de deforestación real de ese año, en % del bosque. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `top_3_shap_incluye_estructurales` | STRING | Las tres variables que más subieron la predicción de ese municipio, con su contribución SHAP (en log-odds) entre paréntesis. |
| `principal_palanca_de_politica_solo_intervencion` | STRING | La variable de intervención (sin bosque ni área) que más sube el riesgo de ese municipio, con su contribución. |

### GRADIENT_BOOSTING_METRICAS

Métricas del gradient boosting en la prueba temporal y en la validación por departamentos.

**Una fila es** una métrica de una de las dos partes del modelo. **La escribe** el notebook 13, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `modelo` | STRING | Gradient boosting y parte del modelo: «¿deforesta?» (clasificación) o «¿cuánto?» (tasa, solo donde sí deforestó). |
| `metrica` | STRING | Métrica: exactitud balanceada, AUC, precisión, recall, especificidad, F1, AP; R², RMSE, MAE y MedAE para «¿cuánto?», y algunos diagnósticos. |
| `prueba_temporal_2025` | FLOAT | Valor en la prueba temporal: entrenado con ANIO_INICIO a ANIO_PRUEBA - 1 y medido en ANIO_PRUEBA. El nombre de la columna queda fijo aunque cambien los años. |
| `prueba_departamentos_fuera` | FLOAT | Valor en la validación que deja departamentos completos por fuera (5 grupos por departamento), sobre toda la muestra. |
| `brecha` | FLOAT | Brecha entre entrenamiento y prueba, en % del valor de entrenamiento (el criterio CDM-7 pide 15 % o menos). Solo en algunas métricas. |

### GRADIENT_BOOSTING_MUESTRA

La muestra con la que se modela: municipios rurales y rurales dispersos con bosque, con la tasa del IDEAM y las 15 variables explicativas, todas del mismo año. Los notebooks 14 y 15 la leen para comprobar que modelan exactamente lo mismo. Como la EVA llega un año menos, los cultivos del último año repiten el último dato conocido.

**Una fila es** un municipio rural en un año (ANIO_INICIO a ANIO_PRUEBA). **La escribe** el notebook 13, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `cod_dane` | STRING | Código DANE del municipio (5 dígitos). Llave para cruzar con las demás tablas. |
| `nombre_municipio` | STRING | Nombre del municipio. |
| `anio` | INTEGER | Año. |
| `nombre_departamento` | STRING | Departamento. |
| `tasa` | FLOAT | Tasa de deforestación del IDEAM ese año, en % del bosque natural al inicio del periodo. |
| `def_ideam_ha` | FLOAT | Hectáreas deforestadas según el IDEAM ese año. |
| `cred` | FLOAT | Crédito de producción de FINAGRO, en millones de pesos por km². |
| `cred_gan` | FLOAT | Crédito de producción a la ganadería, en millones de pesos por km². |
| `cred_palma` | FLOAT | Crédito de producción a la palma, en millones de pesos por km². |
| `cred_cacao` | FLOAT | Crédito de producción al cacao, en millones de pesos por km². |
| `cred_cafe` | FLOAT | Crédito de producción al café, en millones de pesos por km². |
| `cred_arroz` | FLOAT | Crédito de producción al arroz, en millones de pesos por km². |
| `bovinos` | FLOAT | Bovinos por km² (ICA). |
| `fincas` | FLOAT | Fincas con bovinos por 100 km² (ICA). |
| `agricola` | FLOAT | Área cosechada de todos los cultivos, en % del área del municipio (EVA). |
| `palma` | FLOAT | Área cosechada de palma, en % del área del municipio. |
| `cacao` | FLOAT | Área cosechada de cacao, en % del área del municipio. |
| `cafe` | FLOAT | Área cosechada de café, en % del área del municipio. |
| `arroz` | FLOAT | Área cosechada de arroz, en % del área del municipio. |
| `bosque` | FLOAT | Bosque natural del IDEAM al inicio del primer periodo, en % del área del municipio. |
| `area` | FLOAT | Área del municipio cubierta por la grilla de 5 km, en km². Es el denominador de las intensidades. |
| `deforesta` | INTEGER | 1 si el municipio tuvo deforestación del IDEAM mayor que cero ese año; 0 si no. Es lo que predice la parte «¿deforesta?». |

### GRADIENT_BOOSTING_PREDICCIONES_PRUEBA

Predicciones del gradient boosting para el año de prueba (ANIO_PRUEBA), que el modelo no vio al entrenar.

**Una fila es** un municipio rural en el año de prueba. **La escribe** el notebook 13, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `cod_dane` | STRING | Código DANE del municipio (5 dígitos). Llave para cruzar con las demás tablas. |
| `nombre_municipio` | STRING | Nombre del municipio. |
| `nombre_departamento` | STRING | Departamento. |
| `anio` | INTEGER | Año. |
| `deforesta` | INTEGER | 1 si el municipio tuvo deforestación del IDEAM mayor que cero ese año; 0 si no. Es lo que predice la parte «¿deforesta?». |
| `tasa` | FLOAT | Tasa de deforestación del IDEAM ese año, en % del bosque natural al inicio del periodo. |
| `prob_deforesta` | FLOAT | Probabilidad predicha de que el municipio deforeste ese año (parte «¿deforesta?»). |
| `pred_deforesta` | INTEGER | 1 si prob_deforesta es 0,5 o más; 0 si no. |
| `tasa_predicha` | FLOAT | Tasa de deforestación predicha por la parte «¿cuánto?», en % del bosque: la tasa esperada si el municipio deforesta. |

## 14 · Random forest


### RANDOM_FOREST_ESPECIFICACIONES

Comparación de variantes del modelo: qué aportan las 15 variables frente a lo estructural, la persistencia y la región.

**Una fila es** una variante del modelo. **La escribe** el notebook 14, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `especificacion` | STRING | Variante del modelo: persistencia (si deforestó el año anterior, sin modelo), A. solo bosque y área, B. las 15 variables, C. con persistencia, D. con región, y las demás que se prueban en el notebook. |
| `AUC` | FLOAT | AUC en el año de prueba. |
| `exactitud_balanceada` | FLOAT | Exactitud balanceada (umbral 0,5) en el año de prueba. |
| `AP` | FLOAT | Precisión promedio en el año de prueba. |

### RANDOM_FOREST_HIPERPARAMETROS

Hiperparámetros elegidos para el random forest en la búsqueda con validación por departamento.

**Una fila es** un hiperparámetro. **La escribe** el notebook 14, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `index` | STRING | Hiperparámetro. |
| `clasificacion` | STRING | Valor elegido para la parte «¿deforesta?». Va como texto porque mezcla números y palabras. |
| `regresion` | STRING | Valor elegido para la parte «¿cuánto?». Vacío si esa parte no lo usa. |

### RANDOM_FOREST_IMPORTANCIA_BLOQUES

Importancia por permutación de grupos de variables (bloques temáticos y actividades productivas) en la parte «¿deforesta?».

**Una fila es** un grupo de variables. **La escribe** el notebook 14, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `level_0` | STRING | Cómo se agrupan las variables: bloques (temáticos) o actividades (productivas). |
| `grupo` | STRING | Grupo de variables que se barajan juntas. |
| `n_variables` | INTEGER | Número de variables del grupo. |
| `caida` | FLOAT | Caída media del AUC en el año de prueba al barajar juntas las variables del grupo (30 repeticiones). |
| `desv` | FLOAT | Desviación estándar de esa caída. |
| `p_valor` | FLOAT | Valor p de la prueba t de las 30 repeticiones contra cero. |

### RANDOM_FOREST_IMPORTANCIA_PERMUTACION_CLASIFICACION

Importancia por permutación de cada variable en la parte «¿deforesta?», medida en el año de prueba, con dos valores p.

**Una fila es** una variable explicativa. **La escribe** el notebook 14, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `variable` | STRING | Variable explicativa. |
| `clave` | STRING | Nombre corto de la variable, el de las columnas de GRADIENT_BOOSTING_MUESTRA. |
| `importancia_media` | FLOAT | Caída media del AUC en el año de prueba al barajar la variable (30 repeticiones). Mayor = más importante. |
| `desv` | FLOAT | Desviación estándar de esa caída entre las 30 repeticiones. |
| `p_valor` | FLOAT | Valor p de la prueba t de las 30 repeticiones contra cero. Solo recoge el azar del barajado, así que es optimista. |
| `ic95_inf_territorio` | FLOAT | Límite inferior del intervalo de 95 % de la importancia, con bootstrap por departamento (se remuestrean departamentos completos). |
| `ic95_sup_territorio` | FLOAT | Límite superior de ese intervalo. |
| `p_territorio` | FLOAT | Valor p con el bootstrap por departamento. Es la prueba que se usa para contar variables significativas. |
| `p_holm` | FLOAT | p_territorio corregido por Holm entre las variables que usa el modelo. Vacío si el modelo no usa la variable. |
| `tipo` | STRING | estructural (bosque y área del municipio) o intervención (las otras 13 variables: crédito, cultivos y ganado). |

### RANDOM_FOREST_IMPORTANCIA_PERMUTACION_REGRESION

Importancia por permutación de cada variable en la parte «¿cuánto?», medida en el año de prueba entre los municipios que deforestaron.

**Una fila es** una variable explicativa. **La escribe** el notebook 14, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `variable` | STRING | Variable explicativa. |
| `clave` | STRING | Nombre corto de la variable, el de las columnas de GRADIENT_BOOSTING_MUESTRA. |
| `importancia_media` | FLOAT | Caída media del R² (solo municipios que deforestaron) en el año de prueba al barajar la variable (30 repeticiones). Mayor = más importante. |
| `desv` | FLOAT | Desviación estándar de esa caída entre las 30 repeticiones. |
| `p_valor` | FLOAT | Valor p de la prueba t de las 30 repeticiones contra cero. Solo recoge el azar del barajado, así que es optimista. |
| `ic95_inf_territorio` | FLOAT | Límite inferior del intervalo de 95 % de la importancia, con bootstrap por departamento (se remuestrean departamentos completos). |
| `ic95_sup_territorio` | FLOAT | Límite superior de ese intervalo. |
| `p_territorio` | FLOAT | Valor p con el bootstrap por departamento. Es la prueba que se usa para contar variables significativas. |
| `p_holm` | FLOAT | p_territorio corregido por Holm entre las variables que usa el modelo. Vacío si el modelo no usa la variable. |
| `tipo` | STRING | estructural (bosque y área del municipio) o intervención (las otras 13 variables: crédito, cultivos y ganado). |

### RANDOM_FOREST_LISTA_MUNICIPIOS_MAYOR_RIESGO

Los 15 municipios con mayor probabilidad predicha en el año de prueba, con las variables que más empujaron su predicción.

**Una fila es** un municipio. **La escribe** el notebook 14, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `municipio` | STRING | Municipio. |
| `departamento` | STRING | Departamento. |
| `riesgo_predicho` | FLOAT | Probabilidad predicha de deforestar en el año de prueba. |
| `deforesto_en_2025` | STRING | Sí o No: si de verdad deforestó ese año. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `tasa_real_2025` | FLOAT | Tasa de deforestación real de ese año, en % del bosque. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `top_3_shap_incluye_estructurales` | STRING | Las tres variables que más subieron la predicción de ese municipio, con su contribución SHAP (en probabilidad) entre paréntesis. |
| `principal_palanca_de_politica_solo_intervencion` | STRING | La variable de intervención (sin bosque ni área) que más sube el riesgo de ese municipio, con su contribución. |

### RANDOM_FOREST_METRICAS

Métricas del random forest en la prueba temporal y en la validación por departamentos.

**Una fila es** una métrica de una de las dos partes del modelo. **La escribe** el notebook 14, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `modelo` | STRING | Random forest y parte del modelo: «¿deforesta?» (clasificación) o «¿cuánto?» (tasa, solo donde sí deforestó). |
| `metrica` | STRING | Métrica: exactitud balanceada, AUC, precisión, recall, especificidad, F1, AP; R², RMSE, MAE y MedAE para «¿cuánto?», y algunos diagnósticos. |
| `prueba_temporal_2025` | FLOAT | Valor en la prueba temporal: entrenado con ANIO_INICIO a ANIO_PRUEBA - 1 y medido en ANIO_PRUEBA. El nombre de la columna queda fijo aunque cambien los años. |
| `prueba_departamentos_fuera` | FLOAT | Valor en la validación que deja departamentos completos por fuera (5 grupos por departamento), sobre toda la muestra. |
| `brecha` | FLOAT | Brecha entre entrenamiento y prueba, en % del valor de entrenamiento (el criterio CDM-7 pide 15 % o menos). Solo en algunas métricas. |

### RANDOM_FOREST_PREDICCIONES_DEPARTAMENTOS_FUERA

Predicciones de la validación por departamentos: cada fila se predijo con un modelo entrenado sin su departamento.

**Una fila es** un municipio rural en un año (ANIO_INICIO a ANIO_PRUEBA). **La escribe** el notebook 14, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `cod_dane` | STRING | Código DANE del municipio (5 dígitos). Llave para cruzar con las demás tablas. |
| `nombre_municipio` | STRING | Nombre del municipio. |
| `nombre_departamento` | STRING | Departamento. |
| `anio` | INTEGER | Año. |
| `deforesta` | INTEGER | 1 si el municipio tuvo deforestación del IDEAM mayor que cero ese año; 0 si no. Es lo que predice la parte «¿deforesta?». |
| `tasa` | FLOAT | Tasa de deforestación del IDEAM ese año, en % del bosque natural al inicio del periodo. |
| `prob_deforesta` | FLOAT | Probabilidad predicha de que el municipio deforeste ese año (parte «¿deforesta?»). |
| `pred_deforesta` | FLOAT | 1 si prob_deforesta es 0,5 o más; 0 si no. |
| `tasa_predicha` | FLOAT | Tasa de deforestación predicha por la parte «¿cuánto?», en % del bosque: la tasa esperada si el municipio deforesta. |

### RANDOM_FOREST_PREDICCIONES_PRUEBA

Predicciones del random forest para el año de prueba (ANIO_PRUEBA), que el modelo no vio al entrenar.

**Una fila es** un municipio rural en el año de prueba. **La escribe** el notebook 14, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `cod_dane` | STRING | Código DANE del municipio (5 dígitos). Llave para cruzar con las demás tablas. |
| `nombre_municipio` | STRING | Nombre del municipio. |
| `nombre_departamento` | STRING | Departamento. |
| `anio` | INTEGER | Año. |
| `deforesta` | INTEGER | 1 si el municipio tuvo deforestación del IDEAM mayor que cero ese año; 0 si no. Es lo que predice la parte «¿deforesta?». |
| `tasa` | FLOAT | Tasa de deforestación del IDEAM ese año, en % del bosque natural al inicio del periodo. |
| `prob_deforesta` | FLOAT | Probabilidad predicha de que el municipio deforeste ese año (parte «¿deforesta?»). |
| `pred_deforesta` | INTEGER | 1 si prob_deforesta es 0,5 o más; 0 si no. |
| `tasa_predicha` | FLOAT | Tasa de deforestación predicha por la parte «¿cuánto?», en % del bosque: la tasa esperada si el municipio deforesta. |

### RANDOM_FOREST_SINTESIS_OBJETIVOS

Qué resultado del notebook sustenta cada objetivo o criterio del trabajo de grado.

**Una fila es** un objetivo o criterio. **La escribe** el notebook 14, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `objetivo_criterio` | STRING | Objetivo o criterio del trabajo de grado (código y nombre). |
| `evidencia_en_este_notebook` | STRING | Qué resultado del notebook lo sustenta. |
| `estado` | STRING | Si se cumple, con sus salvedades. |

## 15 · Regresión logística


### REGRESION_LOGISTICA_COEFICIENTES_INFERENCIA

Coeficientes del logit de la parte «¿deforesta?», ajustado con los años de entrenamiento, con errores agrupados por departamento y por municipio. Cada variable entra en log(1 + x) y estandarizada: los efectos son por +1 desviación estándar.

**Una fila es** una variable del modelo final. **La escribe** el notebook 15, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `variable` | STRING | Variable explicativa. |
| `coef` | FLOAT | Coeficiente del logit por +1 desviación estándar de la variable. |
| `OR` | FLOAT | Odds ratio, exp(coef): por cuánto se multiplican las odds de deforestar con +1 desviación estándar. |
| `OR_IC95_inf_dep` | FLOAT | Límite inferior del intervalo de 95 % del OR, con errores agrupados por departamento. |
| `OR_IC95_sup_dep` | FLOAT | Límite superior de ese intervalo. |
| `p_dep` | FLOAT | Valor p con errores agrupados por departamento. |
| `OR_IC95_inf_mun` | FLOAT | Límite inferior del intervalo de 95 % del OR, con errores agrupados por municipio. |
| `OR_IC95_sup_mun` | FLOAT | Límite superior de ese intervalo. |
| `p_mun` | FLOAT | Valor p con errores agrupados por municipio. |
| `p_holm_dep` | FLOAT | p_dep corregido por Holm. |
| `tipo` | STRING | estructural (bosque y área del municipio) o intervención (las otras 13 variables: crédito, cultivos y ganado). |
| `signif_holm` | STRING | sí si p_holm_dep es menor que 0,05. |

### REGRESION_LOGISTICA_EFECTOS_MARGINALES

Efectos marginales promedio del logit: cuánto cambia la probabilidad de deforestar con +1 desviación estándar de cada variable (errores por departamento).

**Una fila es** una variable del modelo final. **La escribe** el notebook 15, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `index` | STRING | Variable explicativa. |
| `dy_dx` | FLOAT | Efecto marginal promedio, en probabilidad (de 0 a 1). |
| `puntos_porcentuales` | FLOAT | El mismo efecto en puntos porcentuales (dy_dx × 100). |
| `pr_z` | FLOAT | Valor p. |
| `conf_int_low` | FLOAT | Límite inferior del intervalo de 95 % de dy_dx. |
| `cont_int_hi` | FLOAT | Límite superior del intervalo de 95 % de dy_dx (el nombre viene así de statsmodels). |

### REGRESION_LOGISTICA_EFECTOS_MULTIPLICATIVOS_MAGNITUD

Parte «¿cuánto?» de la regresión: regresión Gamma con enlace log sobre la tasa, solo en los municipios-año que deforestaron, con errores agrupados por departamento.

**Una fila es** una variable del modelo final. **La escribe** el notebook 15, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `index` | STRING | Variable explicativa. |
| `efecto_multiplicativo` | FLOAT | exp(β): por cuánto se multiplica la tasa esperada con +1 desviación estándar de la variable. |
| `IC95_inf` | FLOAT | Límite inferior del intervalo de 95 %. |
| `IC95_sup` | FLOAT | Límite superior del intervalo de 95 %. |
| `p` | FLOAT | Valor p. |
| `p_holm` | FLOAT | Valor p corregido por Holm. |
| `tipo` | STRING | estructural (bosque y área del municipio) o intervención (las otras 13 variables: crédito, cultivos y ganado). |

### REGRESION_LOGISTICA_ESPECIFICACIONES

Comparación de variantes del modelo: qué aportan las 15 variables frente a lo estructural, la persistencia y la región.

**Una fila es** una variante del modelo. **La escribe** el notebook 15, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `especificacion` | STRING | Variante del modelo: persistencia (si deforestó el año anterior, sin modelo), A. solo bosque y área, B. las 15 variables, C. con persistencia, D. con región, y las demás que se prueban en el notebook. |
| `AUC` | FLOAT | AUC en el año de prueba. |
| `exactitud_balanceada` | FLOAT | Exactitud balanceada (umbral 0,5) en el año de prueba. |
| `AP` | FLOAT | Precisión promedio en el año de prueba. |

### REGRESION_LOGISTICA_HIPERPARAMETROS

Hiperparámetros elegidos para el regresión logística en la búsqueda con validación por departamento.

**Una fila es** un hiperparámetro. **La escribe** el notebook 15, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `index` | STRING | Hiperparámetro. |
| `clasificacion` | STRING | Valor elegido para la parte «¿deforesta?». Va como texto porque mezcla números y palabras. |
| `regresion` | STRING | Valor elegido para la parte «¿cuánto?». Vacío si esa parte no lo usa. |

### REGRESION_LOGISTICA_IMPORTANCIA_BLOQUES

Importancia por permutación de grupos de variables (bloques temáticos y actividades productivas) en la parte «¿deforesta?».

**Una fila es** un grupo de variables. **La escribe** el notebook 15, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `level_0` | STRING | Cómo se agrupan las variables: bloques (temáticos) o actividades (productivas). |
| `grupo` | STRING | Grupo de variables que se barajan juntas. |
| `n_variables` | INTEGER | Número de variables del grupo. |
| `caida` | FLOAT | Caída media del AUC en el año de prueba al barajar juntas las variables del grupo (30 repeticiones). |
| `desv` | FLOAT | Desviación estándar de esa caída. |
| `p_valor` | FLOAT | Valor p de la prueba t de las 30 repeticiones contra cero. |

### REGRESION_LOGISTICA_IMPORTANCIA_PERMUTACION_CLASIFICACION

Importancia por permutación de cada variable en la parte «¿deforesta?», medida en el año de prueba, con dos valores p.

**Una fila es** una variable explicativa. **La escribe** el notebook 15, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `variable` | STRING | Variable explicativa. |
| `clave` | STRING | Nombre corto de la variable, el de las columnas de GRADIENT_BOOSTING_MUESTRA. |
| `importancia_media` | FLOAT | Caída media del AUC en el año de prueba al barajar la variable (30 repeticiones). Mayor = más importante. |
| `desv` | FLOAT | Desviación estándar de esa caída entre las 30 repeticiones. |
| `p_valor` | FLOAT | Valor p de la prueba t de las 30 repeticiones contra cero. Solo recoge el azar del barajado, así que es optimista. |
| `ic95_inf_territorio` | FLOAT | Límite inferior del intervalo de 95 % de la importancia, con bootstrap por departamento (se remuestrean departamentos completos). |
| `ic95_sup_territorio` | FLOAT | Límite superior de ese intervalo. |
| `p_territorio` | FLOAT | Valor p con el bootstrap por departamento. Es la prueba que se usa para contar variables significativas. |
| `p_holm` | FLOAT | p_territorio corregido por Holm entre las variables que usa el modelo. Vacío si el modelo no usa la variable. |
| `tipo` | STRING | estructural (bosque y área del municipio) o intervención (las otras 13 variables: crédito, cultivos y ganado). |

### REGRESION_LOGISTICA_IMPORTANCIA_PERMUTACION_REGRESION

Importancia por permutación de cada variable en la parte «¿cuánto?», medida en el año de prueba entre los municipios que deforestaron.

**Una fila es** una variable explicativa. **La escribe** el notebook 15, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `variable` | STRING | Variable explicativa. |
| `clave` | STRING | Nombre corto de la variable, el de las columnas de GRADIENT_BOOSTING_MUESTRA. |
| `importancia_media` | FLOAT | Caída media del R² (solo municipios que deforestaron) en el año de prueba al barajar la variable (30 repeticiones). Mayor = más importante. |
| `desv` | FLOAT | Desviación estándar de esa caída entre las 30 repeticiones. |
| `p_valor` | FLOAT | Valor p de la prueba t de las 30 repeticiones contra cero. Solo recoge el azar del barajado, así que es optimista. |
| `ic95_inf_territorio` | FLOAT | Límite inferior del intervalo de 95 % de la importancia, con bootstrap por departamento (se remuestrean departamentos completos). |
| `ic95_sup_territorio` | FLOAT | Límite superior de ese intervalo. |
| `p_territorio` | FLOAT | Valor p con el bootstrap por departamento. Es la prueba que se usa para contar variables significativas. |
| `p_holm` | FLOAT | p_territorio corregido por Holm entre las variables que usa el modelo. Vacío si el modelo no usa la variable. |
| `tipo` | STRING | estructural (bosque y área del municipio) o intervención (las otras 13 variables: crédito, cultivos y ganado). |

### REGRESION_LOGISTICA_LISTA_MUNICIPIOS_MAYOR_RIESGO

Los 15 municipios con mayor probabilidad predicha en el año de prueba, con las variables que más empujaron su predicción.

**Una fila es** un municipio. **La escribe** el notebook 15, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `municipio` | STRING | Municipio. |
| `departamento` | STRING | Departamento. |
| `riesgo_predicho` | FLOAT | Probabilidad predicha de deforestar en el año de prueba. |
| `deforesto_en_2025` | STRING | Sí o No: si de verdad deforestó ese año. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `tasa_real_2025` | FLOAT | Tasa de deforestación real de ese año, en % del bosque. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `top_3_contribuciones_incluye_estructurales` | STRING | Las tres variables que más subieron la predicción de ese municipio, con su contribución al logit entre paréntesis. |
| `principal_palanca_de_politica_solo_intervencion` | STRING | La variable de intervención (sin bosque ni área) que más sube el riesgo de ese municipio, con su contribución. |

### REGRESION_LOGISTICA_METRICAS

Métricas del regresión logística en la prueba temporal y en la validación por departamentos.

**Una fila es** una métrica de una de las dos partes del modelo. **La escribe** el notebook 15, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `modelo` | STRING | Regresión logística y parte del modelo: «¿deforesta?» (clasificación) o «¿cuánto?» (tasa, solo donde sí deforestó). |
| `metrica` | STRING | Métrica: exactitud balanceada, AUC, precisión, recall, especificidad, F1, AP; R², RMSE, MAE y MedAE para «¿cuánto?», y algunos diagnósticos. |
| `prueba_temporal_2025` | FLOAT | Valor en la prueba temporal: entrenado con ANIO_INICIO a ANIO_PRUEBA - 1 y medido en ANIO_PRUEBA. El nombre de la columna queda fijo aunque cambien los años. |
| `prueba_departamentos_fuera` | FLOAT | Valor en la validación que deja departamentos completos por fuera (5 grupos por departamento), sobre toda la muestra. |
| `brecha` | FLOAT | Brecha entre entrenamiento y prueba, en % del valor de entrenamiento (el criterio CDM-7 pide 15 % o menos). Solo en algunas métricas. |

### REGRESION_LOGISTICA_PREDICCIONES_DEPARTAMENTOS_FUERA

Predicciones de la validación por departamentos: cada fila se predijo con un modelo entrenado sin su departamento.

**Una fila es** un municipio rural en un año (ANIO_INICIO a ANIO_PRUEBA). **La escribe** el notebook 15, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `cod_dane` | STRING | Código DANE del municipio (5 dígitos). Llave para cruzar con las demás tablas. |
| `nombre_municipio` | STRING | Nombre del municipio. |
| `nombre_departamento` | STRING | Departamento. |
| `anio` | INTEGER | Año. |
| `deforesta` | INTEGER | 1 si el municipio tuvo deforestación del IDEAM mayor que cero ese año; 0 si no. Es lo que predice la parte «¿deforesta?». |
| `tasa` | FLOAT | Tasa de deforestación del IDEAM ese año, en % del bosque natural al inicio del periodo. |
| `prob_deforesta` | FLOAT | Probabilidad predicha de que el municipio deforeste ese año (parte «¿deforesta?»). |
| `pred_deforesta` | FLOAT | 1 si prob_deforesta es 0,5 o más; 0 si no. |
| `tasa_predicha` | FLOAT | Tasa de deforestación predicha por la parte «¿cuánto?», en % del bosque: la tasa esperada si el municipio deforesta. |

### REGRESION_LOGISTICA_PREDICCIONES_PRUEBA

Predicciones del regresión logística para el año de prueba (ANIO_PRUEBA), que el modelo no vio al entrenar.

**Una fila es** un municipio rural en el año de prueba. **La escribe** el notebook 15, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `cod_dane` | STRING | Código DANE del municipio (5 dígitos). Llave para cruzar con las demás tablas. |
| `nombre_municipio` | STRING | Nombre del municipio. |
| `nombre_departamento` | STRING | Departamento. |
| `anio` | INTEGER | Año. |
| `deforesta` | INTEGER | 1 si el municipio tuvo deforestación del IDEAM mayor que cero ese año; 0 si no. Es lo que predice la parte «¿deforesta?». |
| `tasa` | FLOAT | Tasa de deforestación del IDEAM ese año, en % del bosque natural al inicio del periodo. |
| `prob_deforesta` | FLOAT | Probabilidad predicha de que el municipio deforeste ese año (parte «¿deforesta?»). |
| `pred_deforesta` | INTEGER | 1 si prob_deforesta es 0,5 o más; 0 si no. |
| `tasa_predicha` | FLOAT | Tasa de deforestación predicha por la parte «¿cuánto?», en % del bosque: la tasa esperada si el municipio deforesta. |

### REGRESION_LOGISTICA_PRUEBAS_WALD

Pruebas de Wald conjuntas en el logit: si un grupo de variables, tomado junto, se asocia con deforestar (errores por departamento).

**Una fila es** un grupo de variables. **La escribe** el notebook 15, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `level_0` | STRING | Cómo se agrupan las variables: bloque (temático) o actividad (productiva). |
| `level_1` | STRING | Grupo de variables. |
| `n_variables` | FLOAT | Variables del grupo que quedan en el modelo. |
| `chi2` | FLOAT | Estadístico de Wald conjunto. |
| `p_valor` | FLOAT | Valor p. |
| `p_holm` | FLOAT | Valor p corregido por Holm. |

### REGRESION_LOGISTICA_SINTESIS_OBJETIVOS

Qué resultado del notebook sustenta cada objetivo o criterio del trabajo de grado.

**Una fila es** un objetivo o criterio. **La escribe** el notebook 15, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `objetivo_criterio` | STRING | Objetivo o criterio del trabajo de grado (código y nombre). |
| `evidencia_en_este_notebook` | STRING | Qué resultado del notebook lo sustenta. |
| `estado` | STRING | Si se cumple, con sus salvedades. |

### REGRESION_LOGISTICA_VIF_ITERATIVO

Factor de inflación de la varianza (VIF) en cada ronda: en cada ronda se quita la variable con el VIF más alto, hasta que todos quedan por debajo del límite.

**Una fila es** una variable explicativa. **La escribe** el notebook 15, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `index` | STRING | Variable explicativa. |
| `ronda_1` | FLOAT | VIF de la variable en esa ronda. Vacío si ya se quitó. El número de rondas depende de los datos. |
| `ronda_2` | FLOAT | VIF de la variable en esa ronda. Vacío si ya se quitó. El número de rondas depende de los datos. |
| `ronda_3` | FLOAT | VIF de la variable en esa ronda. Vacío si ya se quitó. El número de rondas depende de los datos. |
| `ronda_4` | FLOAT | VIF de la variable en esa ronda. Vacío si ya se quitó. El número de rondas depende de los datos. |

## 16 · Comparación de modelos


### COMPARACION_MODELOS_ACTIVIDADES

Qué actividad productiva pesa más en cada modelo: la importancia por permutación de las variables de cada actividad, como % del total de las actividades.

**Una fila es** una actividad productiva. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `index` | STRING | Actividad: ganadería, café, cacao, palma, arroz o escala total (crédito y área cosechada). |
| `gradient_boosting` | FLOAT | % de la importancia de las actividades; cada columna suma 100 (Gradient boosting). |
| `random_forest` | FLOAT | % de la importancia de las actividades; cada columna suma 100 (Random forest). |
| `regresion_logistica` | FLOAT | % de la importancia de las actividades; cada columna suma 100 (Regresión logística). |

### COMPARACION_MODELOS_CDM5

Criterio CDM-5: cuántas variables de intervención tienen una asociación significativa en cada modelo, con la prueba exigente (corrección de Holm).

**Una fila es** un modelo con una prueba. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `modelo` | STRING | Técnica: Gradient boosting, Random forest o Regresión logística. |
| `prueba` | STRING | Prueba con la que se cuenta. |
| `n_variables` | INTEGER | Variables de intervención con valor p de Holm menor que 0,05. |
| `cuales` | STRING | Cuáles son. |

### COMPARACION_MODELOS_COBERTURA

Curva de ganancia: si se revisan primero los municipios de mayor riesgo, qué parte de los que deforestan y de las hectáreas se cubre.

**Una fila es** una fracción de municipios revisados. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `se_revisa_el` | STRING | Fracción de municipios, de mayor a menor riesgo, que se revisa: 10, 20 o 30 %. |
| `municipios_que_deforestan_cubiertos_gradient_boosting` | FLOAT | % de los municipios que deforestaron en el año de prueba que caen en esa fracción (Gradient boosting). |
| `municipios_que_deforestan_cubiertos_random_forest` | FLOAT | % de los municipios que deforestaron en el año de prueba que caen en esa fracción (Random forest). |
| `municipios_que_deforestan_cubiertos_regresion_logistica` | FLOAT | % de los municipios que deforestaron en el año de prueba que caen en esa fracción (Regresión logística). |
| `hectareas_deforestadas_cubiertas_gradient_boosting` | FLOAT | % de las hectáreas deforestadas en el año de prueba que caen en esa fracción (Gradient boosting). |
| `hectareas_deforestadas_cubiertas_random_forest` | FLOAT | % de las hectáreas deforestadas en el año de prueba que caen en esa fracción (Random forest). |
| `hectareas_deforestadas_cubiertas_regresion_logistica` | FLOAT | % de las hectáreas deforestadas en el año de prueba que caen en esa fracción (Regresión logística). |

### COMPARACION_MODELOS_ERRORES_CUARTIL_BOSQUE

Errores de cada modelo (umbral 0,5) según cuánto bosque tiene el municipio, en el año de prueba.

**Una fila es** un modelo en un cuartil de bosque. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `modelo` | STRING | Técnica: Gradient boosting, Random forest o Regresión logística. |
| `cuartil_de_bosque` | STRING | Cuartil de hectáreas de bosque del municipio (bosque % × área), de Q1 (menos) a Q4 (más). |
| `municipios` | INTEGER | Número de municipios. |
| `deforestan` | INTEGER | Cuántos de esos municipios deforestaron en el año de prueba. |
| `falsos_positivos` | INTEGER | Municipios marcados (probabilidad de 0,5 o más) que no deforestaron. |
| `falsos_negativos` | INTEGER | Municipios que deforestaron y no se marcaron (probabilidad menor que 0,5). |

### COMPARACION_MODELOS_FALSOS_NEGATIVOS_HECTAREAS

Cuánta deforestación dejan pasar los falsos negativos de cada modelo (umbral 0,5) en el año de prueba.

**Una fila es** una medida. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `index` | STRING | Medida: falsos negativos, hectáreas que dejan pasar, % de las hectáreas deforestadas y falsos positivos. |
| `gradient_boosting` | FLOAT | Valor de la medida (Gradient boosting). |
| `random_forest` | FLOAT | Valor de la medida (Random forest). |
| `regresion_logistica` | FLOAT | Valor de la medida (Regresión logística). |

### COMPARACION_MODELOS_IMPORTANCIA_15_VARIABLES

Reparto de la importancia (caída del AUC por permutación, solo la parte positiva) entre las 15 variables, en cada modelo.

**Una fila es** una variable explicativa. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `index` | STRING | Variable explicativa. |
| `gradient_boosting` | FLOAT | % de la importancia total; cada columna suma 100 (Gradient boosting). |
| `random_forest` | FLOAT | % de la importancia total; cada columna suma 100 (Random forest). |
| `regresion_logistica` | FLOAT | % de la importancia total; cada columna suma 100 (Regresión logística). |

### COMPARACION_MODELOS_IMPORTANCIA_INTERVENCION

Lo mismo que COMPARACION_MODELOS_IMPORTANCIA_15_VARIABLES, pero solo entre las 13 variables de intervención (sin bosque ni área).

**Una fila es** una variable de intervención. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `index` | STRING | Variable de intervención. |
| `gradient_boosting` | FLOAT | % de la importancia de intervención; cada columna suma 100 (Gradient boosting). |
| `random_forest` | FLOAT | % de la importancia de intervención; cada columna suma 100 (Random forest). |
| `regresion_logistica` | FLOAT | % de la importancia de intervención; cada columna suma 100 (Regresión logística). |

### COMPARACION_MODELOS_METRICAS

Las métricas de los tres modelos lado a lado.

**Una fila es** una métrica en una de las dos pruebas. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `metrica` | STRING | Métrica. |
| `gradient_boosting` | FLOAT | Valor de la métrica (Gradient boosting). |
| `random_forest` | FLOAT | Valor de la métrica (Random forest). |
| `regresion_logistica` | FLOAT | Valor de la métrica (Regresión logística). |
| `parte` | STRING | Parte del modelo: «¿deforesta?» o «¿cuánto?». |
| `prueba` | STRING | Prueba: temporal (el año de prueba) o departamentos fuera. |

### COMPARACION_MODELOS_MISMO_NUMERO_MARCADOS

Comparación sin el efecto del umbral: cada modelo marca los K municipios de mayor probabilidad, con K = los que de verdad deforestaron en el año de prueba.

**Una fila es** un modelo. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `modelo` | STRING | Técnica. |
| `marcados_con_umbral_0_5` | INTEGER | Municipios que marca con umbral 0,5. |
| `aciertos_entre_los_K_de_mayor_probabilidad` | INTEGER | Cuántos de los K de mayor probabilidad deforestaron. En BigQuery, en lugar de K va un número: los municipios que deforestaron en el año de prueba (por ejemplo, aciertos_entre_los_408_de_mayor_probabilidad). Cambia con los datos. En el notebook: «aciertos entre los K de mayor probabilidad». |
| `falsos_negativos_falsos_positivos` | INTEGER | Falsos negativos, que con esta regla son iguales a los falsos positivos. |
| `precision_recall` | FLOAT | Precisión, que con esta regla es igual al recall. |

### COMPARACION_MODELOS_ODDS_CUARTIL_BOSQUE

Odds de deforestar observado y predicho por cada modelo, en cada cuartil de bosque.

**Una fila es** un cuartil de bosque. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `cuartil_de_bosque` | STRING | Cuartil de hectáreas de bosque, de Q1 (menos) a Q4 (más). |
| `municipios` | INTEGER | Número de municipios. |
| `deforestan` | INTEGER | Cuántos de esos municipios deforestaron en el año de prueba. |
| `no_deforestan` | INTEGER | Cuántos no deforestaron en el año de prueba. |
| `odds_observado` | FLOAT | Deforestan / no deforestan. |
| `odds_predicho_gradient_boosting` | FLOAT | p / (1 - p), con p la probabilidad media predicha en el cuartil (Gradient boosting). |
| `odds_predicho_random_forest` | FLOAT | p / (1 - p), con p la probabilidad media predicha en el cuartil (Random forest). |
| `odds_predicho_regresion_logistica` | FLOAT | p / (1 - p), con p la probabilidad media predicha en el cuartil (Regresión logística). |

### COMPARACION_MODELOS_ODDS_MODELOS

Odds de deforestar entre los municipios que marca cada modelo y entre los que no, con dos reglas de marcado.

**Una fila es** un modelo con una regla de marcado. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `modelo` | STRING | Técnica: Gradient boosting, Random forest o Regresión logística. |
| `regla_de_marcado` | STRING | Regla: umbral 0,5 o los K de mayor probabilidad (K = los que deforestaron). |
| `marcados` | INTEGER | Municipios marcados. |
| `odds_entre_los_marcados` | FLOAT | Deforestan / no deforestan, entre los marcados. |
| `odds_entre_los_no_marcados` | FLOAT | Deforestan / no deforestan, entre los no marcados. |
| `razon_de_odds_diagnostica` | FLOAT | Odds entre los marcados / odds entre los no marcados. |
| `lr` | FLOAT | LR+: sensibilidad / (1 - especificidad). |
| `lr_2` | FLOAT | LR−: (1 - sensibilidad) / especificidad. En el notebook: «LR−». |

### COMPARACION_MODELOS_ODDS_TRAMOS_RIESGO

Odds de deforestar en tres tramos de riesgo de cada modelo: el 30 % más alto, el 30 % siguiente y el 40 % más bajo.

**Una fila es** un modelo en un tramo de riesgo. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `modelo` | STRING | Técnica: Gradient boosting, Random forest o Regresión logística. |
| `tramo_de_riesgo` | STRING | Tramo de riesgo. |
| `municipios` | INTEGER | Número de municipios. |
| `deforestan` | INTEGER | Cuántos de esos municipios deforestaron en el año de prueba. |
| `no_deforestan` | INTEGER | Cuántos no deforestaron en el año de prueba. |
| `odds` | FLOAT | Deforestan / no deforestan en el tramo. Vacío si ninguno dejó de deforestar. |

### COMPARACION_MODELOS_PROBABILIDADES_PRUEBA

La probabilidad y la tasa predicha de los tres modelos para cada municipio en el año de prueba.

**Una fila es** un municipio rural en el año de prueba. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `cod_dane` | STRING | Código DANE del municipio (5 dígitos). Llave para cruzar con las demás tablas. |
| `anio` | INTEGER | Año. |
| `nombre_municipio` | STRING | Nombre del municipio. |
| `nombre_departamento` | STRING | Departamento. |
| `deforesta` | INTEGER | 1 si el municipio tuvo deforestación del IDEAM mayor que cero ese año; 0 si no. Es lo que predice la parte «¿deforesta?». |
| `tasa` | FLOAT | Tasa de deforestación del IDEAM ese año, en % del bosque natural al inicio del periodo. |
| `prob_gradient_boosting` | FLOAT | Probabilidad predicha de deforestar (Gradient boosting). |
| `prob_random_forest` | FLOAT | Probabilidad predicha de deforestar (Random forest). |
| `prob_regresion_logistica` | FLOAT | Probabilidad predicha de deforestar (Regresión logística). |
| `tasa_pred_gradient_boosting` | FLOAT | Tasa predicha por la parte «¿cuánto?», en % del bosque (Gradient boosting). |
| `tasa_pred_random_forest` | FLOAT | Tasa predicha por la parte «¿cuánto?», en % del bosque (Random forest). |
| `tasa_pred_regresion_logistica` | FLOAT | Tasa predicha por la parte «¿cuánto?», en % del bosque (Regresión logística). |

### COMPARACION_MODELOS_REFERENCIAS_TAMANO

Cuánto logra una sola variable de tamaño frente a los modelos, en todo el país y dentro de cada cuartil de bosque.

**Una fila es** una referencia de tamaño o un modelo. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `referencia_modelo` | STRING | Referencia (solo hectáreas de bosque, solo área, solo % de bosque) o modelo. |
| `auc_global` | FLOAT | AUC en todos los municipios. |
| `auc_dentro_de_q1` | FLOAT | AUC dentro del cuartil Q1 de bosque (el de menos bosque). |
| `auc_dentro_de_q2` | FLOAT | AUC dentro de Q2. |
| `auc_dentro_de_q3` | FLOAT | AUC dentro de Q3. |
| `auc_en_q1_q3_juntos` | FLOAT | AUC en Q1, Q2 y Q3 juntos. En Q4 casi todos deforestan y el AUC no informa. |

### COMPARACION_MODELOS_SINTESIS_OBJETIVOS

Qué aporta la comparación a cada objetivo o criterio del trabajo de grado.

**Una fila es** un objetivo o criterio. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `objetivo_kpi` | STRING | Objetivo o criterio (código y nombre). |
| `que_aporta_la_comparacion` | STRING | Qué aporta la comparación. |
| `estado` | STRING | Si se cumple, con sus salvedades. |

### COMPARACION_MODELOS_TECNICAS_BUSQUEDA

Cómo se buscaron los hiperparámetros de cada modelo.

**Una fila es** un modelo. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `index` | STRING | Técnica. |
| `tecnica` | STRING | Técnica de búsqueda. |
| `combinaciones_deforesta` | INTEGER | Combinaciones probadas en «¿deforesta?». |
| `combinaciones_cuanto` | INTEGER | Combinaciones probadas en «¿cuánto?». |
| `hiperparametros_que_se_exploran` | STRING | Hiperparámetros que se exploran. |
| `validacion` | STRING | Validación usada en la búsqueda. |
| `metrica_de_seleccion` | STRING | Métrica con la que se elige la mejor combinación. |

### COMPARACION_MODELOS_UMBRALES

Qué pasa con los errores al mover el umbral de decisión (0,3, 0,5 y 0,7).

**Una fila es** un modelo con un umbral. **La escribe** el notebook 16, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `modelo` | STRING | Técnica: Gradient boosting, Random forest o Regresión logística. |
| `umbral` | FLOAT | Probabilidad desde la que se marca un municipio. |
| `marcados` | INTEGER | Municipios marcados. |
| `falsos_negativos` | INTEGER | Deforestaron y no se marcaron. |
| `falsos_positivos` | INTEGER | Se marcaron y no deforestaron. |
| `recall` | FLOAT | Sensibilidad: % de los que deforestaron que se marcaron (de 0 a 1). |
| `especificidad` | FLOAT | % de los que no deforestaron que no se marcaron (de 0 a 1). |

## 17 · Segmentos y riesgo


### SEGMENTOS_Y_RIESGO_AUC_DENTRO

Si el modelo ordena bien dentro de cada segmento: el AUC calculado solo entre los municipios del segmento.

**Una fila es** un segmento con un modelo. **La escribe** el notebook 17, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `segmento` | STRING | Segmento del municipio en la segmentación del notebook 12, con su número y nombre. |
| `modelo` | STRING | Técnica: Gradient boosting, Random forest o Regresión logística. |
| `municipios` | INTEGER | Número de municipios. |
| `deforestaron` | INTEGER | Cuántos de esos municipios deforestaron en el año de prueba. |
| `no_deforestaron` | INTEGER | Cuántos no deforestaron en el año de prueba. |
| `AUC` | FLOAT | AUC dentro del segmento. Vacío si hay menos de 5 que deforestaron o menos de 5 que no. |
| `ic_inf` | FLOAT | Límite inferior del intervalo de 95 % del AUC. |
| `ic_sup` | FLOAT | Límite superior del intervalo de 95 % del AUC. |
| `pares` | INTEGER | Pares de municipios (uno que deforestó y uno que no) que compara el AUC. Es el peso para promediar. |

### SEGMENTOS_Y_RIESGO_CONCENTRACION

Dónde se concentra el riesgo: por segmento, qué parte de sus municipios está en alto riesgo y qué parte de las hectáreas aporta.

**Una fila es** un segmento. **La escribe** el notebook 17, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `nombre_segmento` | STRING | Segmento, con su número y nombre (notebook 12). |
| `municipios` | INTEGER | Número de municipios. |
| `de_los_municipios` | FLOAT | % de los municipios que son del segmento. |
| `deforesto_en_2025` | FLOAT | % de los municipios del segmento que deforestaron en el año de prueba. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `riesgo_medio_gb` | FLOAT | Probabilidad media de deforestar (Gradient boosting). |
| `riesgo_medio_rf` | FLOAT | Probabilidad media de deforestar (Random forest). |
| `riesgo_medio_lr` | FLOAT | Probabilidad media de deforestar (Regresión logística). |
| `en_alto_riesgo_gb` | FLOAT | % de los municipios en alto riesgo: el 30 % de mayor probabilidad (Gradient boosting). |
| `en_alto_riesgo_rf` | FLOAT | % de los municipios en alto riesgo: el 30 % de mayor probabilidad (Random forest). |
| `en_alto_riesgo_lr` | FLOAT | % de los municipios en alto riesgo: el 30 % de mayor probabilidad (Regresión logística). |
| `en_alto_riesgo_los_tres` | FLOAT | % de los municipios en alto riesgo para los tres modelos a la vez. |
| `municipios_en_alto_riesgo_los_tres` | INTEGER | Municipios en alto riesgo para los tres modelos. |
| `del_alto_riesgo_los_tres` | FLOAT | % de todos los municipios en alto riesgo para los tres que son de este segmento. |
| `ha_deforestadas_2025` | FLOAT | Hectáreas deforestadas en el año de prueba. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `de_las_ha_2025` | FLOAT | % de las hectáreas deforestadas del año de prueba que están en el segmento. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `de_las_ha_2025_de_su_segmento_en_alto_riesgo_los_tres` | FLOAT | % de las hectáreas del segmento que están en sus municipios de alto riesgo para los tres. El año del nombre sale de la celda 1: cambia si cambian los años. |

### SEGMENTOS_Y_RIESGO_CREDITO

Dónde coinciden alto riesgo y alto crédito, por segmento. Alto riesgo es el cuarto superior de los tres modelos; alto crédito, el cuarto superior en volumen o en intensidad.

**Una fila es** un segmento. **La escribe** el notebook 17, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `nombre_segmento` | STRING | Segmento, con su número y nombre (notebook 12). |
| `en_el_cuarto_superior_de_riesgo_los_tres` | INTEGER | Municipios en el 25 % de mayor riesgo para los tres modelos. |
| `con_alto_volumen_de_credito` | INTEGER | De esos, cuántos están en el 25 % de más crédito de producción acumulado (ANIO_INICIO a ANIO_PRUEBA). |
| `con_alta_intensidad_de_credito` | INTEGER | De esos, cuántos están en el 25 % de más crédito por km² al año. |
| `credito_mediano_del_segmento_m_km2_al_ano` | FLOAT | Mediana del crédito de producción del segmento, en millones de pesos por km² al año. |
| `de_las_ha_2021_2025_de_los_que_tienen_alto_volumen` | FLOAT | % de las hectáreas deforestadas de ANIO_INICIO a ANIO_PRUEBA (todo el país rural) que están en los municipios de alto riesgo y alto volumen del segmento. El año del nombre sale de la celda 1: cambia si cambian los años. |

### SEGMENTOS_Y_RIESGO_ERRORES

Falsos negativos y falsos positivos de cada modelo (umbral 0,5) en cada segmento, en el año de prueba.

**Una fila es** un segmento. **La escribe** el notebook 17, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `segmento` | STRING | Segmento del municipio en la segmentación del notebook 12, con su número y nombre. |
| `municipios` | INTEGER | Número de municipios. |
| `deforestaron` | INTEGER | Cuántos de esos municipios deforestaron en el año de prueba. |
| `fn_gb` | INTEGER | Falsos negativos: deforestaron y no se marcaron (Gradient boosting). |
| `fp_gb` | INTEGER | Falsos positivos: se marcaron y no deforestaron (Gradient boosting). |
| `fn_rf` | INTEGER | Falsos negativos: deforestaron y no se marcaron (Random forest). |
| `fp_rf` | INTEGER | Falsos positivos: se marcaron y no deforestaron (Random forest). |
| `fn_lr` | INTEGER | Falsos negativos: deforestaron y no se marcaron (Regresión logística). |
| `fp_lr` | INTEGER | Falsos positivos: se marcaron y no deforestaron (Regresión logística). |

### SEGMENTOS_Y_RIESGO_MUNICIPIOS

Cada municipio con su segmento y su riesgo en el año de prueba, ordenado por riesgo dentro de su segmento. Es la lista para priorizar.

**Una fila es** un municipio rural. **La escribe** el notebook 17, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `cod_dane` | STRING | Código DANE del municipio (5 dígitos). Llave para cruzar con las demás tablas. |
| `municipio` | STRING | Nombre del municipio. |
| `departamento` | STRING | Departamento. |
| `region` | STRING | Región (tabla de municipios del Observatorio). |
| `segmento` | STRING | Segmento (notebook 12). |
| `puesto_en_su_segmento` | INTEGER | Puesto del municipio en su segmento, de mayor a menor riesgo de consenso (redondeado a dos decimales; los empates se resuelven por hectáreas deforestadas). |
| `riesgo_de_consenso_2025` | FLOAT | Promedio de la probabilidad de deforestar de los tres modelos en el año de prueba. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `gradient_boosting` | FLOAT | Probabilidad de deforestar en el año de prueba (Gradient boosting). |
| `random_forest` | FLOAT | Probabilidad de deforestar en el año de prueba (Random forest). |
| `regresion_logistica` | FLOAT | Probabilidad de deforestar en el año de prueba (Regresión logística). |
| `modelos_que_lo_ponen_en_alto_riesgo` | INTEGER | Cuántos de los tres modelos lo ponen en alto riesgo (el 30 % de mayor probabilidad de cada modelo). |
| `alto_riesgo_los_tres` | BOOLEAN | Verdadero si los tres modelos lo ponen en alto riesgo. |
| `deforesto_en_2025` | INTEGER | 1 si deforestó en el año de prueba; 0 si no. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `ha_deforestadas_2025` | FLOAT | Hectáreas deforestadas en el año de prueba (IDEAM). El año del nombre sale de la celda 1: cambia si cambian los años. |
| `ha_deforestadas_2021_2025` | FLOAT | Hectáreas deforestadas de ANIO_INICIO a ANIO_PRUEBA (IDEAM). El año del nombre sale de la celda 1: cambia si cambian los años. |
| `credito_de_produccion_2021_2025_millones` | FLOAT | Crédito de producción de FINAGRO acumulado de ANIO_INICIO a ANIO_PRUEBA, en millones de pesos. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `credito_m_km2_al_ano` | FLOAT | Crédito de producción, en millones de pesos por km² al año. |

### SEGMENTOS_Y_RIESGO_ODDS

Odds de deforestar de cada segmento frente al resto, y dentro del segmento entre los de alto riesgo y los demás.

**Una fila es** un segmento. **La escribe** el notebook 17, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `segmento` | STRING | Segmento del municipio en la segmentación del notebook 12, con su número y nombre. |
| `municipios` | INTEGER | Número de municipios. |
| `deforestan` | INTEGER | Cuántos de esos municipios deforestaron en el año de prueba. |
| `no_deforestan` | INTEGER | Cuántos no deforestaron en el año de prueba. |
| `odds_del_perfil` | FLOAT | Deforestan / no deforestan en el segmento. Vacío si todos deforestaron. |
| `odds_del_resto` | FLOAT | Deforestan / no deforestan en los demás segmentos. |
| `razon_de_odds_frente_al_resto` | FLOAT | Odds del segmento / odds del resto. |
| `ic_inf` | FLOAT | Límite inferior del intervalo de 95 % de la razón de odds. |
| `ic_sup` | FLOAT | Límite superior del intervalo de 95 % de la razón de odds. |
| `con_correccion` | BOOLEAN | Verdadero si se sumó 0,5 a cada celda porque alguna era cero. |
| `alto_riesgo_los_tres_municipios` | INTEGER | Municipios del segmento en alto riesgo para los tres modelos. |
| `alto_riesgo_deforestan` | INTEGER | De esos, cuántos deforestaron. |
| `alto_riesgo_no_deforestan` | INTEGER | De esos, cuántos no deforestaron. |
| `resto_del_perfil_deforestan` | INTEGER | Municipios del segmento que no están en alto riesgo y deforestaron. |
| `resto_no_deforestan` | INTEGER | Municipios del segmento que no están en alto riesgo y no deforestaron. |
| `odds_en_el_resto_del_perfil` | FLOAT | Odds de deforestar entre los municipios del segmento que no están en alto riesgo. |

### SEGMENTOS_Y_RIESGO_SINTESIS

Resumen por segmento: perfil, riesgo, hectáreas, crédito y si el modelo ayuda a escoger dentro de él, con una lectura de uso.

**Una fila es** un segmento. **La escribe** el notebook 17, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `index` | STRING | Segmento. |
| `municipios` | INTEGER | Número de municipios. |
| `bosque_del_municipio` | FLOAT | Mediana del bosque, en % del municipio. |
| `bovinos_por_km2` | FLOAT | Mediana de bovinos por km². |
| `credito_m_km2_al_ano` | FLOAT | Mediana del crédito de producción, en millones de pesos por km² al año. |
| `deforesto_en_2025` | FLOAT | % de los municipios que deforestaron en el año de prueba. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `en_alto_riesgo_los_tres` | FLOAT | % de los municipios en alto riesgo para los tres modelos. |
| `de_las_ha_2025` | FLOAT | % de las hectáreas deforestadas del año de prueba que están en el segmento. El año del nombre sale de la celda 1: cambia si cambian los años. |
| `auc_dentro_del_segmento_promedio_de_los_tres` | FLOAT | Promedio del AUC de los tres modelos dentro del segmento. Vacío si no se puede calcular. |
| `lectura_de_uso` | STRING | Regla simple: riesgo generalizado, concentrado, intermedio o bajo, y qué hacer en cada caso. |

## 18 · Demo de Landsat


### DEMO_LANDSAT_CALIBRACION_UMBRAL

Métricas de cada umbral de dNBR en la mitad oeste (calibración).

**Una fila es** un umbral de dNBR. **La escribe** el notebook 18, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `umbral_dnbr` | FLOAT | Umbral de caída del NBR (dNBR) desde el que un píxel se marca como pérdida. |
| `ambos_ha` | FLOAT | Hectáreas que marcan la demo y GLAD-L. |
| `solo_propia_ha` | FLOAT | Hectáreas que marca solo la demo. |
| `solo_glad_ha` | FLOAT | Hectáreas que marca solo GLAD-L. |
| `ninguno_ha` | FLOAT | Hectáreas que no marca ninguno. |
| `precision` | FLOAT | De lo que marca la demo, qué parte confirma GLAD-L (de 0 a 1). |
| `sensibilidad` | FLOAT | De lo que marca GLAD-L, qué parte encuentra la demo (de 0 a 1). |
| `f1` | FLOAT | Media armónica de precisión y sensibilidad. |
| `exactitud_global_no_informativa` | FLOAT | Exactitud global. Se reporta solo para mostrar que no informa: el bosque estable domina la escena. |

### DEMO_LANDSAT_COMPARACION_CELDAS

Hectáreas de pérdida por celda de 5 km, según la demo y según GLAD-L.

**Una fila es** una celda de 5 km de la zona piloto. **La escribe** el notebook 18, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `celda` | INTEGER | Número de la celda en la zona piloto. |
| `lon_centro` | FLOAT | Longitud del centro de la celda. |
| `lat_centro` | FLOAT | Latitud del centro de la celda. |
| `propia_ha` | FLOAT | Hectáreas de pérdida según la demo. |
| `glad_ha` | FLOAT | Hectáreas de pérdida según GLAD-L. |
| `dominio_ha` | FLOAT | Hectáreas de bosque observable de la celda. |

### DEMO_LANDSAT_CURVA_OBSERVABILIDAD

Concordancia con GLAD-L según las observaciones limpias exigidas.

**Una fila es** un nivel de observaciones limpias. **La escribe** el notebook 18, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `modo` | STRING | acumulado (al menos esas observaciones) o exacto (exactamente esas). |
| `obs` | STRING | Observaciones limpias exigidas, en texto. |
| `obs_minimas` | INTEGER | Observaciones limpias exigidas en las dos fechas. |
| `dominio_ha` | FLOAT | Hectáreas de bosque que cumplen la exigencia. |
| `cobertura_pct` | FLOAT | % del bosque observable que queda. |
| `reparto_ns` | FLOAT | % de esa área que está en la mitad norte de la zona. Las filas exigentes quedan casi todas en el sur. |
| `ambos_ha` | FLOAT | Hectáreas que marcan la demo y GLAD-L. |
| `solo_propia_ha` | FLOAT | Hectáreas que marca solo la demo. |
| `solo_glad_ha` | FLOAT | Hectáreas que marca solo GLAD-L. |
| `ninguno_ha` | FLOAT | Hectáreas que no marca ninguno. |
| `precision` | FLOAT | De lo que marca la demo, qué parte confirma GLAD-L (de 0 a 1). |
| `sensibilidad` | FLOAT | De lo que marca GLAD-L, qué parte encuentra la demo (de 0 a 1). |
| `f1` | FLOAT | Media armónica de precisión y sensibilidad. |
| `exactitud_global_no_informativa` | FLOAT | Exactitud global. Se reporta solo para mostrar que no informa: el bosque estable domina la escena. |

### DEMO_LANDSAT_DETECCION_POR_PARCHE

Claros detectados por tramo de tamaño, en las dos direcciones.

**Una fila es** un tramo de tamaño de claro en una dirección. **La escribe** el notebook 18, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `direccion` | STRING | glad_vs_propia: de los claros de GLAD-L, cuántos encuentra la demo (omisión). propia_vs_glad: de los de la demo, cuántos confirma GLAD-L (comisión). |
| `tamano` | STRING | Tramo de tamaño del claro (TODOS es el total). |
| `parches` | INTEGER | Claros en el tramo. |
| `parches_detectados` | INTEGER | Claros que el otro producto toca, aunque sea en parte. |
| `pct_parches` | FLOAT | % de claros detectados. |
| `area_ha` | FLOAT | Hectáreas de esos claros. |
| `area_detectada_ha` | FLOAT | Hectáreas de los claros detectados. |
| `pct_area` | FLOAT | % del área detectada. |

### DEMO_LANDSAT_DIAGNOSTICO_NUBES

Bosque observable y número de lecturas limpias por píxel.

**Una fila es** la zona piloto (una fila). **La escribe** el notebook 18, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `bosque_inicial_ha` | FLOAT | Hectáreas de bosque inicial (Hansen). |
| `con_obs_t1_ha` | FLOAT | Bosque con al menos una observación limpia en la primera fecha (T1). |
| `con_obs_t2_ha` | FLOAT | Bosque con al menos una observación limpia en la segunda fecha (T2). |
| `dominio_ha` | FLOAT | Bosque observable en las dos fechas: donde se puede medir. |
| `sin_datos_ha` | FLOAT | Bosque que queda por fuera por no tener ninguna observación limpia en alguna fecha. |
| `sin_datos_pct` | FLOAT | Lo mismo, en % del bosque inicial. |
| `obs_min_t1` | INTEGER | Mínimo de observaciones limpias por píxel en T1. |
| `obs_p10_t1` | INTEGER | Percentil 10 de observaciones limpias por píxel en T1. |
| `obs_mediana_t1` | INTEGER | Mediana de observaciones limpias por píxel en T1. |
| `obs_max_t1` | INTEGER | Máximo de observaciones limpias por píxel en T1. |
| `obs_min_t2` | INTEGER | Mínimo de observaciones limpias por píxel en T2. |
| `obs_p10_t2` | INTEGER | Percentil 10 en T2. |
| `obs_mediana_t2` | INTEGER | Mediana en T2. |
| `obs_max_t2` | INTEGER | Máximo en T2. |
| `pct_bosque_con_1_sola_obs` | FLOAT | % del bosque con una sola observación limpia: su dato es más frágil. |

### DEMO_LANDSAT_EVALUACION_VALIDACION

Matriz de acuerdo y métricas en la mitad este (validación).

**Una fila es** el umbral elegido (una fila). **La escribe** el notebook 18, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `umbral_dnbr` | FLOAT | Umbral de caída del NBR (dNBR) desde el que un píxel se marca como pérdida. |
| `ambos_ha` | FLOAT | Hectáreas que marcan la demo y GLAD-L. |
| `solo_propia_ha` | FLOAT | Hectáreas que marca solo la demo. |
| `solo_glad_ha` | FLOAT | Hectáreas que marca solo GLAD-L. |
| `ninguno_ha` | FLOAT | Hectáreas que no marca ninguno. |
| `precision` | FLOAT | De lo que marca la demo, qué parte confirma GLAD-L (de 0 a 1). |
| `sensibilidad` | FLOAT | De lo que marca GLAD-L, qué parte encuentra la demo (de 0 a 1). |
| `f1` | FLOAT | Media armónica de precisión y sensibilidad. |
| `exactitud_global_no_informativa` | FLOAT | Exactitud global. Se reporta solo para mostrar que no informa: el bosque estable domina la escena. |

### DEMO_LANDSAT_FECHA_EFECTIVA

Qué fecha representa cada compuesto y qué intervalo mide el dNBR.

**Una fila es** una medida de fecha. **La escribe** el notebook 18, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `medida` | STRING | desfase_t1_dias y desfase_t2_dias: días entre la fecha efectiva de cada compuesto y la fecha de la ventana de GLAD-L. intervalo_dias: días entre las dos fechas efectivas. |
| `p5` | INTEGER | Percentil 5, en días. |
| `p25` | INTEGER | Percentil 25. |
| `mediana` | INTEGER | Mediana. |
| `p75` | INTEGER | Percentil 75. |
| `p95` | INTEGER | Percentil 95. |
| `fecha_mediana` | STRING | Fecha efectiva mediana (solo en las filas de desfase). |
| `pct_mas_de_30d_del_anio` | FLOAT | % de píxeles cuyo intervalo se aparta del año en más de 30 días (solo en intervalo_dias). |
| `pct_mas_de_45d_del_anio` | FLOAT | Lo mismo, con 45 días. |

### DEMO_LANDSAT_MUESTRA_VALIDACION_VISUAL

Muestra estratificada de puntos para interpretar a mano (columna etiqueta_visual vacía).

**Una fila es** un punto para revisar a mano. **La escribe** el notebook 18, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `lon` | FLOAT | Longitud del punto (centro del píxel). |
| `lat` | FLOAT | Latitud del punto. |
| `estrato` | STRING | Estrato del muestreo: ambos, solo la demo, solo GLAD-L o ninguno. |
| `etiqueta_visual` | STRING | Vacía: es para que alguien la llene mirando la imagen. |

### DEMO_LANDSAT_REFERENCIA_ENTRE_PRODUCTOS

Concordancia entre la demo, GLAD-L, Hansen y el IDEAM, medida con la misma vara.

**Una fila es** un par de productos. **La escribe** el notebook 18, en `DATASET_RESULTADOS`.

| Columna | Tipo | Qué es |
|---|---|---|
| `par` | STRING | Productos que se comparan. |
| `ventana` | STRING | Periodo que cubre la comparación. |
| `area_prueba_ha` | FLOAT | Hectáreas de pérdida del primer producto. |
| `area_ref_ha` | FLOAT | Hectáreas de pérdida del de referencia. |
| `razon_areas` | FLOAT | area_prueba_ha / area_ref_ha. |
| `f1` | FLOAT | F1 por píxel. |
| `techo_f1` | FLOAT | El F1 más alto posible con esas dos áreas: 2·min(A, B) / (A + B). |
| `pct_del_techo` | FLOAT | Qué parte de ese máximo se alcanza. |
| `f1_tol_1px` | FLOAT | F1 aceptando un píxel de distancia. |
| `f1_tol_2px` | FLOAT | F1 aceptando dos píxeles de distancia. |
| `pearson_celda` | FLOAT | Correlación de Pearson de las hectáreas por celda de 5 km. |
| `lin_celda` | FLOAT | Concordancia de Lin de las hectáreas por celda: además de la correlación, castiga que una reporte más que la otra. |
| `pct_area_ref_hallada` | FLOAT | % del área de la referencia que el primero toca. |
| `pct_area_prueba_confirmada` | FLOAT | % del área del primero que la referencia confirma. |
