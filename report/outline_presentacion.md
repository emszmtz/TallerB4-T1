# Presentación / Report — Taller B4-T1 (5 minutos)

> Esqueleto para el PDF y la defensa de 5 min. Rellenar los `<<...>>` con los números que
> imprime el notebook. Orden pensado para que cada slide sea ~1 min.

---

## Slide 1 — Problema y enfoque (≈40 s)
- **Objetivo:** conceder crédito (`Home Credit Default Risk`) con un modelo *preciso, justo
  e incierto-consciente*. `TARGET`: 1 = impago, 0 = paga.
- **Reto de los datos:** desbalanceo fuerte (**8,1 %** impagos) → usamos `class_weight` y
  medimos **AUC**, no sólo accuracy. Variable sensible: **género** (`CODE_GENDER`).
- Las 4 piezas: capa custom · loss FAIR · Keras Tuner · incertidumbre.

## Slide 2 — Tarea 1: Capa customizada (≈50 s)
- **`DebtRatioSaturationLayer`** (solo `keras.ops`, *backend-agnóstica*):
  - Calcula internamente los **ratios de endeudamiento**: anualidad/ingresos,
    crédito/ingresos, anualidad/crédito.
  - **Restricción matemática:** saturación `ratio**p` con `p∈[0.1,1]` *entrenable*
    (constraint de clip). Con `p<1` comprime las colas pesadas (ingresos muy skew,
    *skew ≈ 392*) → asigna recursos a la zona densa de la distribución.
- **Por qué:** las redes no dividen ni saturan por sí solas; metemos ese conocimiento
  financiero como inductive bias. Exponentes aprendidos finales: `<<p1,p2,p3>>`.

## Slide 3 — Tarea 2: Aprendizaje justo (FAIR loss) (≈50 s)
- **Métrica de dependencia elegida:** correlación de **Pearson** entre la probabilidad
  predicha `ŷ` y el género `s`, calculada de forma diferenciable dentro de la loss.
- **Loss:** `BCE(y, ŷ) + λ · corr(ŷ, s)²`. Al cuadrado para que el óptimo sea corr = 0
  (no −1). El sensible se pasa concatenado en `y_true` (2 columnas `[TARGET, género]`).
- Sin restricción: `corr(ŷ, género) = <<...>>`; con FAIR (λ=`<<...>>`): `<<...>>`.
- (Mencionar alternativas: Spearman / *demographic parity gap* — también reportado.)

## Slide 4 — Tarea 3: AutoML + Curva de Pareto (≈60 s)
- **Keras Tuner** busca topología (capas, unidades, activación, dropout, lr); mejor
  arquitectura: `<<...>>`.
- **Gráfica de Pareto** (Precisión Y vs Dependencia FAIR X al barrer `λ`):
  - **¿Cuánto se sacrifica por ser justo?** Pasar de dependencia `<<X0>>` a `<<X1>>`
    cuesta `<<ΔAcc / ΔAUC>>` puntos. → El modelo elegido es el "codo": casi sin pérdida
    de precisión y con la dependencia ya cercana a 0.

## Slide 5 — Tarea 4: Incertidumbre (≈50 s)
- **MC-Dropout:** T=`<<50>>` pasadas con dropout activo → **varianza** de la predicción.
- **Gráfica de distribución** varianza "buen pagador" vs "mal pagador": `<<comentar>>`.
- **Pregunta del enunciado:** *¿más incertidumbre cuando `EXT_SOURCE` es peor?*
  → **Sí**: la varianza media sube con el nº de `EXT_SOURCE` ausentes
  (`0→<<...>>`, `1→<<...>>`, `2→<<...>>`). Coherente con que el impago también sube
  (7,3 % → 9,9 %). Caso de uso: derivar a revisión humana los perfiles de alta incertidumbre.

## Slide 6 — Resultados y conclusión (≈30 s)
- **Tabla Base vs mejor FAIR (test):**

  | Modelo | Accuracy | AUC | \|corr(ŷ,género)\| | DP gap |
  |--------|---------:|----:|------------------:|-------:|
  | Base (λ=0) | `<<...>>` | `<<...>>` | `<<...>>` | `<<...>>` |
  | **Mejor FAIR** | `<<...>>` | `<<...>>` | **`<<...>>`** | **`<<...>>`** |

- **Conclusión:** conseguimos un modelo **casi tan preciso** como el base pero con la
  dependencia del género **reducida a ~0**, y que además **señala su propia incertidumbre**
  en los perfiles con peores fuentes externas.

---

### Checklist de entregables (GitHub)
- [ ] Código que regenera todas las gráficas/tablas (el notebook).
- [ ] Curva de Pareto Precisión vs Dependencia FAIR.
- [ ] Distribución de incertidumbre buen vs mal pagador.
- [ ] Curvas de loss (convergencia) de cada entrenamiento final.
- [ ] Tabla Base vs mejor FAIR en test (mejor resaltado).
- [ ] Este PDF de presentación.
