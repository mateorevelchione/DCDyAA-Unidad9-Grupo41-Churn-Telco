# Data log - decisiones del proyecto

Registro de las decisiones tomadas desde la pre-entrega hasta la entrega final, con su motivo y la evidencia que las respalda.

| # | Etapa | Decisión | Motivo | Evidencia / consecuencia |
|---|---|---|---|---|
| 1 | Negocio | Reportar el 26,5 % como **proporción de bajas de la muestra**, no como tasa mensual | Devolución de la pre-entrega: el archivo es una foto transversal sin fechas ni ventana de observación | El KPI deja de ser "tasa de abandono mensual" y pasa a ser la captura de bajas dentro del cupo de contacto |
| 2 | Negocio | KPI principal: **Recall @ 20 %**; eficiencia: **Precision @ 20 %** | El equipo de retención contacta ~20 % de la cartera; cada contacto a alguien que no se iba es una oferta desperdiciada | Se agregan Lift, F1 @ 20 %, matriz de confusión del cupo y tabla de distintos cupos |
| 3 | Negocio | Mantener las metas de la pre-entrega: ROC-AUC ≥ 0,80 y lift ≥ 2 (Recall @ 20 % ≥ 40 %) | No mover la meta después de ver resultados | Se cumplen en el test (ROC-AUC 0,842; Recall @ 20 % 49,7 %) |
| 4 | Datos | Usar el archivo oficial `Telco-Customer-Churn.csv` del repositorio de IBM (Apache 2.0) | Precisar fuente y licencia (devolución de la pre-entrega); el `test.csv` del curso no trae etiquetas | Descarga automática y validación por SHA-256 |
| 5 | Datos | Verificar que `train.csv` + `test.csv` del curso son una partición del archivo oficial | Trazabilidad con el Módulo 5 y la pre-entrega | 5.634 + 1.409 = 7.043 IDs, sin superposición; filas idénticas salvo 8 `TotalCharges` imputados |
| 6 | Datos | Guardar la partición del curso en `data/particion_curso.csv` (solo IDs y orden) | Reproducir el baseline y reutilizar el test del curso como holdout | Ambos lados con 26,54 % de bajas: partición estratificada |
| 7 | Validación | Holdout = `test.csv` del curso | Sus etiquetas nunca estuvieron disponibles: no participó de ninguna decisión previa | 1.409 clientes, 374 bajas; se usa una sola vez |
| 8 | Preparación | `TotalCharges` en blanco → 0 | Los 11 blancos son clientes con `tenure = 0`, todavía no facturados | El archivo del curso los imputaba con la mediana (1.397,475): inconsistente con un cliente nuevo. Cierra la decisión pendiente de la pre-entrega |
| 9 | Preparación | No tratar outliers | Criterio IQR: 0 outliers en `tenure`, `MonthlyCharges` y `TotalCharges` | Cierra la decisión pendiente de la pre-entrega |
| 10 | Preparación | No agrupar categorías | El nivel menos frecuente tiene 682 clientes | Cierra la decisión pendiente de la pre-entrega |
| 11 | Preparación | Conservar los 22 perfiles repetidos | Tienen `customerID` distinto: son clientes distintos con el mismo perfil | Sin impacto material |
| 12 | Preparación | Variables derivadas: `n_servicios_adicionales`, `pago_automatico`, `variacion_cargo` | Resumir H2, distinguir pago manual vs automático y aproximar cambios de precio | `variacion_cargo` termina con aporte casi nulo (SHAP 0,2 %) |
| 13 | Preparación | One-hot + estandarización dentro de un `Pipeline` | Ajustar transformaciones solo con el train de cada fold | Evita fuga entre particiones |
| 14 | Modelado | Reproducir el baseline de la pre-entrega desde la fuente oficial | Devolución: documentar la reproducibilidad | ROC-AUC 0,8481 (validación) y 0,8452 (CV), idénticos a lo reportado; `assert` en el notebook |
| 15 | Modelado | Comparar 5 algoritmos con configuraciones fijas | Los 3 del Módulo 5 + Random Forest y XGBoost comprometidos en la pre-entrega | Sin búsqueda de hiperparámetros sobre el test |
| 16 | Selección | Regla ex ante: elegibles con ROC-AUC CV ≥ 0,80; empate = a menos de 1 desvío del mejor Recall @ 20 %; si empata la logística, se elige la logística; si no, mayor PR-AUC | Evitar elegir por el mejor número del test | Empatados: logística, Naive Bayes, Random Forest y XGBoost. Elegida: regresión logística |
| 17 | Modelado | No ponderar clases ni usar SMOTE | El objetivo es ordenar; la ponderación cambia la escala del score | `class_weight='balanced'`: PR-AUC 0,658 vs 0,659 y Brier 0,166 vs 0,135. Cierra la decisión pendiente de la pre-entrega |
| 18 | Evaluación | Benchmark de regla de negocio: mes a mes primero, ordenados por menor antigüedad | La pre-entrega proponía "contactar a todos los mes a mes", pero son el 55 % de la cartera y el cupo es 20 % | Recall @ 20 % 44,7 %: la regla ya es un benchmark exigente |
| 19 | Evaluación | Intervalos bootstrap (1.000 remuestreos) | Medir la incertidumbre del test (374 bajas) | Modelo − regla: +5,1 p.p. (IC95 1,0-10,2); XGBoost − logística en Recall @ 20 %: +1,1 p.p. (IC95 −1,9 a 3,6) |
| 20 | Evaluación | No cambiar de modelo aunque XGBoost tenga mayor PR-AUC en test | La regla se fijó antes; la diferencia en el KPI de negocio no es significativa | XGBoost queda como alternativa a evaluar en el piloto |
| 21 | Validación | No hay validación temporal | El archivo no tiene fechas ni orden cronológico | Se declara como limitación principal; la implementación requiere cortes temporales |
| 22 | Interpretación | SHAP agrupado por variable original y por bloque de hipótesis | `tenure`, `TotalCharges` y `MonthlyCharges` están relacionadas entre sí y con los servicios; sus contribuciones individuales se compensan | Bloques: contrato/antigüedad 44 %, servicios 27 %, internet/cargo/pago 18 %, demográficas 4 % |
| 23 | Hipótesis | Ablaciones con los mismos 5 folds de desarrollo | No reutilizar el test para análisis | H1 respaldada (−7,3 p.p. de PR-AUC), H3 parcial (−2,0), H2 menor (−0,8), H4 confirmada (demográficas −0,1) |
| 24 | Ética | Auditoría por género, adulto mayor, pareja e hijos a cargo | Atributos sensibles; la retención implica decidir quién recibe ofertas | Adultos mayores: 38 % seleccionados vs 17 %; con hijos a cargo: recall 27 % vs 55 % |
| 25 | Ética | Recomendar la versión sin atributos demográficos | Pierde 0,5 p.p. de recall y deja de usar atributos sensibles | La diferencia por edad persiste (36 % vs 17 %) por variables proxy: requiere monitoreo |
| 26 | Negocio | Escenario económico paramétrico por campaña, sobre una cartera con la composición del test | No hay costos reales ni tasa temporal | Supuestos explícitos (USD 15 por contacto, 25 % de retención, 12 meses) y tabla de sensibilidad |
| 27 | Calibración | El score se usa para ordenar; Brier 0,138 y curva de calibración razonable | `predict_proba` no implica calibración perfecta | Si se usara como probabilidad absoluta, recalibrar con datos reales |
