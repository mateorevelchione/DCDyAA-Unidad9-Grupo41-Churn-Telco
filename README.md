# Proyecto Final Integrador - Priorización de clientes para campañas de retención (churn en telco)

**Diplomado en Ciencia de Datos y Análisis Avanzado (UTN.BA) - Unidad 9**
**Equipo N.º 41 - Aguilar, Damian; Revelchione, Mateo**

[![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/mateorevelchione/DCDyAA-Unidad9-Grupo41-Churn-Telco/blob/main/proyecto_integrador.ipynb)

El equipo de retención de una telco puede contactar aproximadamente al **20 % de la cartera** por campaña y hoy no tiene un criterio sistemático para decidir a quién llamar. El proyecto construye un **ranking de clientes según su riesgo estimado de baja**, para que ese cupo se use primero con quienes tienen más probabilidad de irse.

## Resultado principal

Todos los números salen de `proyecto_integrador.ipynb` y se guardan en `results/`. Test = 1.409 clientes que no participaron de ninguna decisión.

| Métrica (test, cupo 20 %) | Contacto aleatorio | Regla de negocio | **Regresión logística** |
|---|---:|---:|---:|
| Recall @ 20 % (bajas capturadas) | 20,0 % | 44,7 % | **49,7 %** (IC95 46,2-54,7 %) |
| Precision @ 20 % (contactados que se iban) | 26,5 % | 59,2 % | **66,0 %** |
| Lift @ 20 % | 1,00 | 2,23 | **2,48** |
| Ingreso mensual en riesgo capturado | 20,0 % | 38,2 % | **54,3 %** |
| PR-AUC / ROC-AUC | 0,269 / 0,50 | 0,539 / 0,791 | **0,634 / 0,842** |

Metas fijadas en la pre-entrega, antes de entrenar: **Recall @ 20 % ≥ 40 %** (lift ≥ 2) y **ROC-AUC ≥ 0,80**. Ambas se cumplen.

### Cómo se eligió el modelo

Se compararon con CV de 5 folds los tres modelos del Módulo 5 (logística, kNN, Naive Bayes) y los dos comprometidos en la pre-entrega (Random Forest y XGBoost). La regla de selección se definió **antes** de correr la CV: entre los modelos con ROC-AUC ≥ 0,80, los que quedan a menos de un desvío del mejor Recall @ 20 % se consideran empatados y, si la logística está entre ellos, se elige la logística por simplicidad e interpretabilidad.

Logística, Naive Bayes, Random Forest y XGBoost quedaron empatados (Recall @ 20 % en CV entre 0,488 y 0,507); la regla eligió la **regresión logística**. En el test, XGBoost obtiene una PR-AUC algo mayor (+2,6 p.p., IC95 0,2-5,0), pero la diferencia en el KPI de negocio no es significativa (+1,1 p.p., IC95 −1,9 a +3,6) y no se usa para cambiar la regla.

### Recomendación

Usar el ranking en un **piloto A/B con grupo de control**, en la versión **sin atributos demográficos** (pierde solo 0,5 p.p. de recall), y construir una validación con cortes temporales antes de cualquier despliegue: el dataset es una foto sin fechas y no permite medir la estabilidad en el tiempo.

## Correcciones incorporadas desde la pre-entrega

| Observación de la devolución | Cómo se resolvió |
|---|---|
| El 26,5 % no es una tasa mensual | Se reporta como proporción de bajas de la muestra. El KPI de negocio pasó a ser la captura de bajas dentro del cupo de contacto (Recall/Precision @ 20 %). |
| Alinear métricas de negocio con Precision y Recall | Recall @ 20 % (KPI principal), Precision @ 20 %, Lift, F1 @ 20 %, matriz de confusión del cupo y tabla de recall/precision para distintos cupos. |
| Documentar la reproducibilidad del baseline | El notebook reconstruye el `train.csv` del curso desde la fuente oficial y reproduce el baseline de la pre-entrega (ROC-AUC 0,8481 / CV 0,8452), con un `assert` que lo verifica. |
| Precisar fuente y licencia | Archivo oficial de IBM (Apache License 2.0), descarga automática y validación por hash SHA-256. Ver `data/README.md`. |

## Estructura del repositorio

```text
.
├── README.md
├── proyecto_integrador.ipynb      # notebook ejecutado de punta a punta
├── requirements.txt
├── data_log.md                    # registro de decisiones
├── LICENSE
├── .gitignore
├── data/
│   ├── README.md                  # fuente, licencia, hash y partición
│   └── particion_curso.csv        # IDs de la partición train/test usada en la cursada
├── results/                       # tablas y figuras usadas en el informe
│   └── README.md
└── docs/
    ├── Pre-entrega_Proyecto_Final_Integrador_Grupo41.pdf
    ├── Informe_Final_Proyecto_Integrador_Grupo41.pdf / .docx
    └── Presentacion_Ejecutiva_Grupo41.pdf / .pptx
```

## Cómo ejecutar

### Google Colab
1. Abrir el notebook con el botón "Abrir en Colab" de arriba.
2. Ejecutar **Entorno de ejecución → Ejecutar todas**. El notebook instala `xgboost` y `shap` si faltan, descarga el dataset oficial (validando su hash) y la partición del curso desde este repositorio.

### Local

```bash
git clone https://github.com/mateorevelchione/DCDyAA-Unidad9-Grupo41-Churn-Telco
cd DCDyAA-Unidad9-Grupo41-Churn-Telco
python -m venv .venv
# Windows: .venv\Scripts\activate
# Linux/Mac: source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook proyecto_integrador.ipynb
```

Tiempo de ejecución completa: menos de 1 minuto en una notebook de 4 núcleos. Todo usa `random_state=42`.

## Flujo metodológico (CRISP-DM)

| Sección del notebook | Contenido | Salidas |
|---|---|---|
| 1. Negocio | KPI, benchmarks y meta ex ante | - |
| 2. Datos | descarga validada, calidad, outliers, EDA de H1-H4 | `eda_*.csv`, `fig_eda.png` |
| 3. Preparación | blancos de `TotalCharges`, variables derivadas, pipelines | - |
| 4. Modelado | reproducción del baseline, CV 5 folds, regla ex ante, chequeo de balanceo | `baseline_preentrega.csv`, `cv_5folds.csv`, `seleccion.csv`, `chequeo_balanceo.csv` |
| 5. Evaluación | test único, benchmarks, bootstrap, curvas, lift, calibración | `model_metrics.csv`, `bootstrap_ic.csv`, `lift_deciles.csv`, `capacidad.csv`, `fig_pr_ganancia.png` |
| 6. Validación temporal | por qué no es posible con estos datos | - |
| 7. SHAP | importancia global, por bloques y caso local | `shap_*.csv`, `fig_shap_*.png` |
| 8. Profundización | ablaciones H1-H4, auditoría por grupos, modelo sin atributos sensibles | `ablaciones.csv`, `auditoria_grupos.csv`, `modelo_sin_sensibles.csv` |
| 9. Negocio | escenario económico paramétrico y sensibilidad | `escenario_economico.csv`, `sensibilidad_economica.csv` |

## Decisiones metodológicas clave

- **El problema es de ranking bajo capacidad limitada**: el KPI es Recall @ 20 %. Accuracy no gobierna la decisión (predecir siempre "no se va" da 73,5 % de accuracy y no sirve para nada).
- **Holdout = `test.csv` del curso**: sus etiquetas nunca estuvieron disponibles durante la cursada, por lo que no participó de ninguna decisión anterior.
- **`TotalCharges` en blanco → 0**: son 11 clientes con `tenure = 0` que todavía no fueron facturados. El archivo del curso los había imputado con la mediana (1.397,475), lo que es inconsistente.
- **Sin ponderar clases**: se probó `class_weight='balanced'` y no mejora el ranking (PR-AUC 0,658 vs 0,659) pero empeora la calibración (Brier 0,166 vs 0,135).
- **Sin validación temporal**: el archivo no tiene fechas. La robustez se acota con el desvío entre folds y con intervalos bootstrap del test.
- **SHAP no prueba causalidad**, y las variables de cargo y antigüedad están relacionadas entre sí, por lo que se leen por bloque.
- **Auditoría ética**: diferencias de prevalencia entre grupos no prueban ausencia de sesgo ni justifican un trato diferencial automáticamente.

## Fuente de datos

**Telco Customer Churn** (IBM). Repositorio: https://github.com/IBM/telco-customer-churn-on-icp4d - archivo `data/Telco-Customer-Churn.csv`, distribuido bajo **Apache License 2.0**. 7.043 clientes de una telco ficticia, 19 variables predictoras + `customerID` + `Churn`. Sin datos personales reales. Detalle en `data/README.md`.

## Autores

Damian Aguilar y Mateo Revelchione - Equipo N.º 41. El código se publica bajo licencia MIT (ver `LICENSE`).
