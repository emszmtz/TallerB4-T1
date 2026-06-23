# Taller B4-T1 — EDA ampliado + Capa Customizada

Notebook entregable para analizar dependencias en el dataset **Home Credit Default Risk** y
desarrollar la **Tarea 1: arquitectura customizada**.

El notebook principal es `Taller_B4_T1_Solucion.ipynb`. Esta versión queda acotada a:

1. **Exploración ampliada de datos (EDA)** sobre la base completa de Kaggle.
2. **Capa customizada Keras**: `MonotonicDebtRiskLayer`, que calcula ratios financieros y
   aprende un score de endeudamiento con pesos positivos.

La variable objetivo es `TARGET`:

- `1`: dificultades de pago / impago.
- `0`: pago correcto.

La variable sensible estudiada en el EDA es `CODE_GENDER`.

## Estructura

```text
TallerB4-T1/
├── README.md
├── requirements.txt
├── .gitignore
├── Taller_B4_T1_Solucion.ipynb          # Entregable: EDA ampliado + Tarea 1
├── Taller_B4_T1_Solucion_completo.ipynb # Copia local ignorada: versión completa anterior
├── data/                                # CSVs de Kaggle, no versionados
├── outputs/eda/                         # Figuras generadas por el EDA, no versionadas
└── report/
    ├── outline_presentacion.md
    └── reparto_tareas.md
```

## Datos

La carpeta `data/` contiene los CSV completos de Home Credit:

- `application_train.csv`
- `application_test.csv`
- `bureau.csv`
- `bureau_balance.csv`
- `previous_application.csv`
- `POS_CASH_balance.csv`
- `installments_payments.csv`
- `credit_card_balance.csv`
- `HomeCredit_columns_description.csv`
- `sample_submission.csv`

Si se descarga desde Kaggle, el mirror usado localmente fue:

```bash
kaggle datasets download -d megancrenshaw/home-credit-default-risk -p data/ --unzip
```

## Qué genera el notebook

El EDA guarda figuras en `outputs/eda/`:

- panorama del dataset y desbalanceo de `TARGET`;
- datos ausentes, especialmente `EXT_SOURCE_*`;
- distribuciones por clase;
- matriz de correlación;
- correlación con `TARGET`;
- tasas de impago por deciles y categorías;
- análisis de género y variables proxy;
- análisis de `EXT_SOURCE`;
- ratios financieros usados por la capa custom;
- señales de `bureau`, `previous_application`, `installments_payments` y `POS_CASH_balance`;
- ranking final de dependencias.

## Capa customizada

`MonotonicDebtRiskLayer` recibe las features base y las tres variables financieras crudas:

- `AMT_INCOME_TOTAL`
- `AMT_CREDIT`
- `AMT_ANNUITY`

Dentro de la capa calcula:

- `AMT_CREDIT / AMT_INCOME_TOTAL`
- `AMT_ANNUITY / AMT_INCOME_TOTAL`
- `AMT_ANNUITY / AMT_CREDIT`

Después aplica `log1p` a los ratios para reducir el peso de las colas pesadas y aprende un
`debt_score` como combinación ponderada de esos ratios. La restricción matemática está en los
pesos: se parametrizan con `softplus(raw_w)`, por lo que son siempre positivos.

Interpretación: dentro de esta parte estructurada del modelo, aumentar un ratio de
endeudamiento no puede reducir el score financiero aprendido.

## Ejecución

Recomendado: Google Colab o Python 3.11/3.12 con TensorFlow/Keras 3.

```bash
python3.12 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter lab Taller_B4_T1_Solucion.ipynb
```

La copia `Taller_B4_T1_Solucion_completo.ipynb` se conserva solo en local y está incluida en
`.gitignore`.
