# Resultados

Todos los archivos de esta carpeta se generan al ejecutar `proyecto_integrador.ipynb` y son los que usa el informe final.

| Archivo | Contenido |
|---|---|
| `eda_calidad.csv`, `eda_outliers.csv` | Nulos, blancos y valores distintos por variable; outliers por criterio IQR |
| `eda_h1_*.csv`, `eda_h2_*.csv`, `eda_h3_*.csv`, `eda_demograficas.csv` | Proporción de bajas por grupo para cada hipótesis |
| `eda_contrato_x_antiguedad.csv` | Cruce contrato × antigüedad (base de la regla de negocio) |
| `fig_eda.png` | Gráficos del EDA de H1-H3 |
| `baseline_preentrega.csv` | Reproducción del baseline de la pre-entrega desde la fuente oficial |
| `cv_5folds.csv`, `cv_folds_detalle.csv` | Validación cruzada de 5 folds (media, desvío y detalle por fold) |
| `seleccion.csv` | Aplicación de la regla de selección ex ante |
| `chequeo_balanceo.csv` | Logística con y sin `class_weight='balanced'` |
| `baseline_mayoritaria.csv` | Clasificador de clase mayoritaria (accuracy, PR-AUC, ROC-AUC) |
| `model_metrics.csv` | Test: contacto aleatorio, regla de negocio y los 5 modelos |
| `matriz_confusion_top20.csv`, `ingreso_en_riesgo.csv` | Resultado operativo del cupo de 20 % |
| `capacidad.csv` | Recall, precision y lift para cupos de 10 % a 50 % |
| `bootstrap_ic.csv` | Intervalos de confianza bootstrap y diferencias entre modelos |
| `lift_deciles.csv`, `fig_pr_ganancia.png` | Lift por deciles, curvas precision-recall y de ganancia |
| `calibracion.csv`, `fig_calibracion.png` | Calibración del score (Brier y curva) |
| `shap_global.csv`, `shap_bloques.csv`, `shap_direccion.csv`, `fig_shap_global.png` | SHAP global por variable y por bloque de hipótesis |
| `shap_local_case.csv`, `fig_shap_local.png` | SHAP local de un cliente real del test |
| `coeficientes_logistica.csv` | Coeficientes y odds ratios de la regresión logística |
| `ablaciones.csv` | Ablaciones de H1-H4 con CV |
| `auditoria_grupos.csv`, `modelo_sin_sensibles.csv` | Auditoría por grupos y modelo sin atributos demográficos |
| `escenario_economico.csv`, `sensibilidad_economica.csv` | Escenario económico paramétrico y sensibilidad |
| `resumen.csv` | Resumen de los números principales |
