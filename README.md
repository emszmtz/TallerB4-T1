# Taller B4-T1 — Diseño de Redes Confiables (Justicia e Incertidumbre)

Modelo de clasificación neuronal para la **concesión de créditos** (dataset *Home Credit
Default Risk*) que es a la vez **preciso**, **justo** (*fair learning*, sin discriminar por
género) y **honesto sobre su confianza** (*incertidumbre*).

Predecimos `TARGET` (1 = dificultades de pago / "mal pagador", 0 = pagó a tiempo) a partir
de ingresos, crédito, anualidad, edad y las puntuaciones de fuentes externas
`EXT_SOURCE_1/2/3`. La variable **sensible** es `CODE_GENDER`.

## Las 4 tareas del enunciado

| # | Tarea | Cómo se resuelve en el notebook |
|---|-------|----------------------------------|
| 1 | **Arquitectura customizada** | `DebtRatioSaturationLayer`: una capa Keras (solo `keras.ops`) que **calcula internamente el ratio de endeudamiento** (anualidad/ingresos, crédito/ingresos, anualidad/crédito) y aplica una **saturación** `x**p` con exponentes `p∈[0.1,1]` entrenables (restricción matemática) antes de las capas densas. |
| 2 | **Aprendizaje justo (FAIR loss)** | `CustomFairnessLoss = BinaryCrossentropy + λ · corr(ŷ, género)²`. La penalización es la **correlación de Pearson** (diferenciable) entre la probabilidad predicha y la variable sensible; se eleva al cuadrado para empujarla a 0. |
| 3 | **AutoML** | `keras_tuner` busca la topología (nº de capas, unidades, activación, dropout, learning rate). Después se barre `λ` para construir la **curva de Pareto** Precisión vs Dependencia. |
| 4 | **Incertidumbre** | **MC-Dropout**: T pasadas estocásticas con dropout activo → media y **varianza** de la predicción. Se analiza si la incertidumbre crece cuando la calidad de `EXT_SOURCE` es peor. (Se incluye además el modelo-de-error mostrado en clase como método alternativo.) |

## Estructura

```
TallerB4-T1/
├── README.md
├── requirements.txt
├── .gitignore
├── Taller_B4_T1_Solucion.ipynb     # Notebook principal: implementa y entrena las 4 tareas
│                                      y genera TODAS las gráficas y tablas del enunciado
└── report/
    └── outline_presentacion.md     # Esqueleto del PDF / presentación de 5 min
```

## Cómo ejecutarlo

El curso usa **Google Colab** (recomendado, lleva TensorFlow + GPU). TensorFlow **no**
soporta Python 3.14, así que en local necesitas un entorno con Python 3.11/3.12.

1. Consigue `application_train.csv` desde
   [Home Credit Default Risk](https://www.kaggle.com/competitions/home-credit-default-risk/data)
   (o el mirror `megancrenshaw/home-credit-default-risk`).
2. Abre `Taller_B4_T1_Solucion.ipynb`.
3. En la celda de **configuración** ajusta `CSV_PATH` a la ruta del CSV
   (en Colab puedes subirlo o montar Drive; en local apunta a tu fichero).
4. Ejecuta de arriba a abajo. Para una primera pasada rápida deja `SUBSAMPLE` activado;
   para los resultados finales ponlo a `None` y sube las épocas / `MAX_TRIALS`.

### Local (entorno con TensorFlow)
```bash
python3.11 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
jupyter lab Taller_B4_T1_Solucion.ipynb
```

## Entregables que genera el notebook

- **Curva de Pareto** Precisión (eje Y) vs Medida de Dependencia FAIR (eje X) para varios `λ`.
- **Distribución de la incertidumbre** (varianza de las predicciones) "buen pagador" vs "mal pagador".
- **Curvas de loss** de cada entrenamiento final (convergencia).
- **Tabla** modelo Base (sin FAIR) vs mejor modelo FAIR en *test*, resaltando el mejor.

## Notas sobre los datos (decisiones de preprocesado)

- **Desbalanceo:** sólo el 8,1 % son impagos. Se entrena con `class_weight` y se reporta
  **AUC** además de *accuracy* (la accuracy sola es engañosa).
- **Bug del loader del esqueleto:** `AMT_ANNUITY` tiene 12 nulos que el loader original no
  imputaba (dejaba NaN en `X`). Aquí se imputa con la mediana, igual que `EXT_SOURCE_*`.
- **Incertidumbre:** se guarda el **nº de `EXT_SOURCE` ausentes por persona ANTES de imputar**
  (`EXT_SOURCE_1` falta en el 56 % de los casos) como proxy de la calidad de las fuentes
  externas, para responder a la pregunta de incertidumbre del enunciado.
