# Predicción de Churn: modelado y evaluación de clasificación

Notebook de Jupyter que entrena un modelo de clasificación binaria para
predecir si un cliente de una empresa de telecomunicaciones va a dar de
baja el servicio (*churn*), y evalúa su desempeño con Accuracy,
Precision, Recall, F1-Score y matriz de confusión.

## Problema

Retener a un cliente cuesta menos que perderlo. Si un modelo detecta a
tiempo a los clientes que están por irse, el equipo comercial puede
contactarlos con una oferta de retención. El objetivo es predecir la
columna `Churn` (1 si el cliente abandona, 0 si se queda) a partir de
los datos de su contrato, los servicios contratados y sus cargos.

## Dataset

*Telco Customer Churn* (IBM): 7.043 clientes y 21 columnas. Solo el
26,5% de los clientes abandona, por lo que las clases están
desbalanceadas. Fuente:
[IBM/telco-customer-churn-on-icp4d](https://github.com/IBM/telco-customer-churn-on-icp4d).

El archivo está incluido en `data/telco_churn.csv`.

## Requisitos

- Python 3.9 o superior.
- Librerías: pandas, scikit-learn, matplotlib y notebook
  (`requirements.txt`).

## Cómo ejecutar el notebook

1. Clonar el repositorio y entrar a la carpeta:

   ```bash
   git clone <url-del-repo>
   cd <nombre-del-repo>
   ```

2. Instalar las dependencias:

   ```bash
   python -m pip install -r requirements.txt
   ```

3. Abrir Jupyter:

   ```bash
   python -m notebook
   ```

4. En el navegador, abrir `modelado_metricas.ipynb` y ejecutar todas las
   celdas (menú **Kernel > Restart & Run All**).

## Qué hace el notebook

1. Carga los datos y revisa el balance de clases.
2. Limpia los datos (`TotalCharges` a número, se elimina `customerID`).
3. Divide en 80% train y 20% test, estratificado, **antes** de ajustar
   nada.
4. Entrena una Regresión Logística dentro de un `Pipeline` con
   preprocesamiento (imputación, escalado y `OneHotEncoder`).
5. Calcula Accuracy, Precision, Recall, F1-Score y la matriz de
   confusión (mapa de calor) sobre el conjunto de prueba.
6. Entrena un segundo modelo con `class_weight="balanced"` y compara.
7. Calcula el impacto en el negocio (clientes que se van y no se
   detectan, y su facturación mensual).
8. Cierra con una conclusión que justifica la métrica más importante.

## Resultados (conjunto de prueba, 1.409 clientes)

| Métrica   | Modelo base | Modelo balanceado |
|-----------|-------------|-------------------|
| Accuracy  | 0,806       | 0,738             |
| Precision | 0,657       | 0,504             |
| Recall    | 0,559       | 0,783             |
| F1-Score  | 0,604       | 0,614             |

## Conclusión

Para churn, la métrica más importante es el **Recall**: un falso
negativo es un cliente que se va sin que nadie intente retenerlo, y
cuesta más que una falsa alarma (una oferta o llamada de más). La
Accuracy engaña: el modelo base llega a 80,6%, pero deja sin detectar a
165 de los 374 clientes que se van, unos USD 10.779 de facturación
mensual. El modelo balanceado detecta a 84 clientes más (81 no
detectados, unos USD 4.800 de facturación mensual) a cambio de 179 falsas alarmas
adicionales, por lo que se recomienda para una campaña de retención. El
análisis completo está en la última sección del notebook.

## Estructura

```
.
├── modelado_metricas.ipynb   # notebook con el entrenamiento y las métricas
├── data/telco_churn.csv      # dataset
├── requirements.txt
├── .gitignore
└── README.md
```
