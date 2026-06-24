# Alcance de la integración de Keras Tuner

Esta versión parte del repositorio original del equipo y añade exclusivamente la Tarea 3.

## Cambios realizados

- Se añadió al notebook principal un bloque independiente de Keras Tuner.
- Se añadió `keras-tuner>=1.4.7` a `requirements.txt`.
- Se documentó el bloque en `README.md`.

## Elementos no modificados

- Preprocesado y EDA.
- Implementación de `MonotonicDebtRiskLayer`.
- Implementación de `FairLoss`.
- Modelos `model_base` y `model_fair`.
- Implementación de incertidumbre.
- Reparto de tareas y presentación.

El tuner reutiliza los objetos existentes, pero sus modelos se guardan en el diccionario
`lambda_models` y no sobrescriben los modelos de los compañeros.
