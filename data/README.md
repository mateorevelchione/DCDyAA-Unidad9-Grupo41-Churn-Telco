# Datos

| Campo | Valor |
|---|---|
| Dataset | Telco Customer Churn (IBM) - datos de muestra de una telco ficticia |
| Repositorio oficial | https://github.com/IBM/telco-customer-churn-on-icp4d |
| Descarga directa | https://raw.githubusercontent.com/IBM/telco-customer-churn-on-icp4d/master/data/Telco-Customer-Churn.csv |
| Licencia | Apache License 2.0 (licencia del repositorio de IBM) |
| SHA-256 del archivo usado | `16320c9c1ec72448db59aa0a26a0b95401046bef5d02fd3aeb906448e3055e91` |
| Tamaño | 7.043 clientes, 21 columnas (`customerID`, 19 predictoras, `Churn`) |
| Datos personales | No contiene: `customerID` es un código anónimo de una empresa ficticia |

El CSV no se versiona. Al ejecutar el notebook se descarga y se valida el hash para garantizar que se usa exactamente la misma versión.

## `particion_curso.csv`

Durante la cursada (Módulo 5 y pre-entrega) se trabajó con `train.csv` (5.634 clientes, con `Churn`) y `test.csv` (1.409 clientes, sin `Churn`), una partición de este mismo archivo. Este CSV guarda solo los `customerID`, la partición (`train`/`test`) y el orden original de cada archivo. Sirve para:

- reproducir exactamente el baseline de la pre-entrega desde la fuente oficial;
- usar el `test.csv` del curso como holdout final, ya que sus etiquetas nunca estuvieron disponibles durante la cursada.

Ambas particiones tienen 26,54 % de bajas (partición estratificada). Única diferencia de contenido detectada: en el archivo del curso, los 11 clientes con `tenure = 0` tenían `TotalCharges` imputado con la mediana (1.397,475); en el archivo oficial están en blanco.
