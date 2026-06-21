# Reparto de tareas — Taller B4-T1 (grupo de 3)

**Perfil del equipo:** 1 técnico/quant (Emilio) + 2 economistas.
**Entrega:** 25 junio 18:00 (GitHub 30% + presentación PDF 70%). Presentación de **5 min** (el
profe penaliza pasarse). Si alguien no puede asistir el 25 → se permite **vídeo de 5 min**.

**Idea clave:** los 3 puntos que el enunciado pide defender en la presentación encajan 1:1 con
las 3 personas. Cada uno **es dueño** de su parte y la **defiende** en el turno de preguntas.

| Punto a presentar (enunciado) | Responsable |
|---|---|
| Restricción matemática de la Capa Custom + métrica de dependencia de la Loss | **Emilio** |
| Análisis de la curva de Pareto: ¿cuánto se sacrifica por ser justo? | **Economista 1** |
| Reflexión sobre la incertidumbre: ¿más incertidumbre con peor `EXT_SOURCE`? | **Economista 2** |

---

## 👤 Emilio — Ingeniero del modelo (parte técnica)

**Hace:**
1. Mantiene y ejecuta el notebook `Taller_B4_T1_Solucion.ipynb` (las 4 tareas ya están: capa
   custom, FAIR loss en `keras.ops`, Keras Tuner, MC-Dropout).
2. Lanza la **corrida final** en Colab/GPU: `SUBSAMPLE = None`, subir `EPOCHS` y `MAX_TRIALS`.
   Exporta a `outputs/` las gráficas (Pareto, incertidumbre, curvas de loss) y la tabla.
3. Sube el repo a **GitHub** (código + README) → nota del 30%.
4. Pasa a los economistas: las **figuras finales + los números** (tabla, λ elegido, exponentes
   aprendidos) y les explica los conceptos en cristiano.

**Presenta:** Slide 2 (capa custom) + parte técnica del Slide 3 (la métrica de la loss).

**Para defender en preguntas:** por qué `corr²` y no `corr`; qué hace la saturación `x**p`;
qué es `keras.ops` (backend-agnóstico); cómo MC-Dropout estima la varianza.

---

## 👤 Economista 1 — Analista de Justicia y Trade-off

**Hace (sin programar; usa las gráficas que genera el notebook):**
1. **EDA interpretativo** (sección 2 del notebook): relación género↔impago (hombres 10,1 % vs
   mujeres 7,0 %), ingresos, fuentes externas. Contexto: **¿por qué discriminar por género en
   crédito es un problema ético y legal?** (igualdad de trato, normativa).
2. **Interpreta la curva de Pareto**: redacta el análisis *¿cuánto se sacrifica en precisión por
   ser justo?* con números concretos (cuánto baja Accuracy/AUC al reducir la dependencia).
   Justifica el "codo" (mejor λ) desde una óptica de **negocio/riesgo**.
3. Redacta en el PDF: **contexto del problema, justicia (Fair Learning) y análisis de Pareto**.

**Presenta:** Slide 1 (contexto/problema) + Slide 4 (Pareto).

**Para defender:** qué es *demographic parity gap*; diferencia Pearson vs Spearman como medida de
dependencia; por qué un modelo más justo puede perder algo de precisión.

---

## 👤 Economista 2 — Analista de Incertidumbre y Caso de Negocio

**Hace (sin programar):**
1. **Interpreta la incertidumbre** (sección 7): distribución de la varianza "buen vs mal pagador"
   y su relación con la **calidad de `EXT_SOURCE`** (incluido el matiz: imputar con la mediana
   puede *enmascarar* la incertidumbre de los datos faltantes). Lo traduce a una **política de
   negocio**: derivar a revisión humana los perfiles de alta incertidumbre.
2. **Interpreta la tabla final** Base vs mejor FAIR (test): qué implica para el banco
   (riesgo, cumplimiento, rentabilidad).
3. **Monta el documento PDF y la baraja de slides**: formato, narrativa global, juntar las partes
   de los 3 y **cuidar que quepa en 5 min**.

**Presenta:** Slide 5 (incertidumbre) + Slide 6 (tabla + conclusiones de negocio).

**Para defender:** qué es la varianza de predicción y por qué MC-Dropout la estima; por qué un
`EXT_SOURCE` ausente debería dar más incertidumbre; qué decisión tomaría el banco con eso.

---

## 🤝 Compartido / coordinación
- **Ensayo conjunto cronometrado** de los 5 min (el profe penaliza pasarse de tiempo).
- **Revisión cruzada:** cada uno comprueba que su narrativa coincide con lo que hace el código.
- Base del PDF: usar `report/outline_presentacion.md` (ya tiene los 6 slides y los huecos `<<...>>`).

## 📅 Mini-calendario (hoy 21 → entrega 25 jun 18:00)
| Día | Quién | Qué |
|-----|-------|-----|
| 21–22 | Emilio | Corrida final en Colab, exportar figuras/tabla, subir GitHub, pasar material |
| 22–23 | Econ 1 y 2 | Escribir sus secciones del PDF con los números reales |
| 24 | Econ 2 (+todos) | Montar PDF + slides completos; 1er ensayo |
| 25 mañana | Todos | Ensayo final cronometrado; entregar PDF + repo antes de 18:00; presentar |
