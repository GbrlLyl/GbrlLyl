# Clasificación de Calidad de Vino Tinto
## mediante Aprendizaje Automático Supervisado

**Modelo Random Forest · Dataset Red Wine Quality (Kaggle)**

| | |
|---|---|
| **Título** | Evidencia de Aprendizaje Automático Supervisado |
| **Nombre** | ______________________________ |
| **Fecha** | ______________________________ |
| **Curso** | ______________________________ |

---

## 1. Contexto del caso

Una bodega productora de vino tinto necesita **predecir de forma automática si un vino será de alta calidad** a partir de sus propiedades fisicoquímicas medibles en laboratorio (acidez, contenido de alcohol, sulfatos, dióxido de azufre, entre otras), sin depender exclusivamente de un panel de catadores humanos, proceso lento y costoso.

El conjunto de datos es **Red Wine Quality**, público en Kaggle y el repositorio UCI. Contiene **1.599 muestras** de vino tinto portugués "Vinho Verde", cada una con **11 variables fisicoquímicas** y un puntaje sensorial de calidad (0–10) asignado por catadores expertos. La utilidad del modelo es doble: **priorizar lotes prometedores** durante la producción y **reducir costos** de evaluación sensorial.

---

## 2. Problema de análisis y justificación metodológica

**Pregunta de análisis:** ¿es un vino de alta calidad (sí / no) dadas sus 11 variables fisicoquímicas? Al predecir una **categoría** (no un valor numérico continuo), estamos ante un problema de **clasificación binaria supervisada**.

**Definición de la variable objetivo.** El puntaje original *quality* (rango observado 3–8) se transformó en una etiqueta de dos clases: **"bueno" (1) si quality ≥ 7** y **"no bueno" (0)** en caso contrario. El umbral 7 refleja la necesidad de negocio de aislar los vinos premium; además convierte un problema multiclase con categorías muy poco pobladas (había solo 10 vinos de calidad 3 y 18 de calidad 8) en uno binario más estable y accionable.

**Estrategia de evaluación.** Solo el **14% de los vinos son "buenos"**, es decir, las clases están **desbalanceadas**. En este escenario el *accuracy* es engañoso —un modelo que prediga siempre "no bueno" acertaría el 86% sin aprender nada—, por lo que la decisión se basa en el **F1-Score** (equilibrio entre precisión y sensibilidad) y el **AUC** (poder discriminante global). Adicionalmente se usó `class_weight="balanced"` para que los modelos no ignoren la clase minoritaria.

---

## 3. Preparación y transformación de los datos

La preparación siguió cinco pasos, cada uno con una finalidad concreta:

- **Carga.** Lectura del CSV con las 1.599 muestras y sus 12 columnas.
- **Auditoría de calidad.** Se verificó que el dataset **no contiene valores nulos** (no fue necesaria imputación) y se eliminaron duplicados exactos, que inflarían el peso de registros repetidos.
- **Codificación.** Todas las variables son **numéricas**; no existen categóricas de texto, por lo que **no se requiere codificación** (one-hot o label encoding). La única transformación de tipo es la creación de la etiqueta binaria *good* a partir de *quality*, descrita en la sección 2.
- **División estratificada.** Partición en **75% entrenamiento y 25% prueba** con *stratify*, que mantiene la misma proporción de clases (14% positivos) en ambos conjuntos; sin ello, el conjunto de prueba podría quedar sesgado y las métricas no serían fiables.
- **Estandarización.** Escalado de las variables a media 0 y desviación 1 con *StandardScaler*. Es imprescindible porque las variables tienen escalas muy dispares (el dióxido de azufre llega a cientos mientras los cloruros son menores a 1); sin escalar, Regresión Logística y KNN se verían dominados por las variables de mayor magnitud. El escalador se ajusta **solo con los datos de entrenamiento** para no filtrar información del conjunto de prueba.

<p align="center">
  <img src="img/fig_dist.png" width="440"><br>
  <em>Figura 1. Distribución de puntajes de calidad: predominan los valores 5 y 6, y los extremos son escasos.</em>
</p>

<p align="center">
  <img src="img/fig_corr.png" width="560"><br>
  <em>Figura 2. Correlaciones entre variables. El alcohol muestra la relación positiva más fuerte con la calidad.</em>
</p>

**Interpretación (Figuras 1 y 2).** La calidad se concentra en los puntajes 5 y 6, con extremos escasos, lo que confirma el desbalance de la etiqueta binaria. En la matriz de correlación, el **alcohol** presenta la relación positiva más fuerte con la calidad (~0.48) y la **acidez volátil** la más negativa (~-0.39): más acidez volátil implica peor calidad.

---

## 3.1 Análisis exploratorio: estadísticas y distribuciones

El resumen estadístico (media, desviación y cuartiles de las 11 variables) revela tres hechos clave: (1) el **alcohol** varía entre ~8.4 y 14.9 %vol, rango amplio que anticipa poder predictivo; (2) las variables tienen **escalas muy dispares** —el dióxido de azufre llega a cientos frente a cloruros menores a 1—, lo que justifica la estandarización; y (3) la **acidez volátil** muestra atípicos altos asociados a defectos de sabor.

<p align="center">
  <img src="img/fig_box.png" width="620"><br>
  <em>Figura 3. Boxplots de las variables clave por clase (vino bueno vs. no bueno).</em>
</p>

**Interpretación (Figura 3).** Los vinos **"buenos" presentan mayor alcohol y sulfatos y menor acidez volátil** que los "no buenos" (cajas claramente desplazadas). Esta separación anticipa que estas tres variables serán las más influyentes en el modelo.

<p align="center">
  <img src="img/fig_hist.png" width="620"><br>
  <em>Figura 4. Histogramas de las variables fisicoquímicas clave.</em>
</p>

**Interpretación (Figura 4).** Las tres variables muestran distribuciones **asimétricas** (sesgo a la derecha en alcohol y sulfatos; concentración en valores bajos con atípicos en acidez volátil). Al no seguir una distribución normal, se favorece un modelo no paramétrico como Random Forest.

---

## 4. Elección del modelo y fundamentos

Se entrenaron **tres modelos de clasificación** con lógicas distintas, para comparar enfoques antes de decidir: **Regresión Logística** (modelo lineal, sirve de línea base interpretable), **KNN con k=15** (clasifica según la mayoría de los 15 vinos más parecidos) y **Random Forest** (ensamble de 300 árboles de decisión que promedia sus votos). Sus resultados sobre el conjunto de prueba fueron:

| Modelo | Accuracy | Precision | Recall | F1 | AUC |
|---|:---:|:---:|:---:|:---:|:---:|
| Reg. Logística | 0.790 | 0.368 | 0.778 | 0.500 | 0.872 |
| KNN (k=15) | 0.887 | 0.655 | 0.352 | 0.458 | 0.843 |
| **Random Forest** | **0.940** | **0.917** | **0.611** | **0.733** | **0.946** |

**Cómo leer la tabla.** La Regresión Logística detecta muchos vinos buenos (recall 0.78) pero con precisión muy baja (0.37): se equivoca al declarar "bueno" dos de cada tres veces. KNN es más preciso pero apenas identifica un tercio de los buenos (recall 0.35). **Random Forest logra el mejor equilibrio**: la precisión más alta (0.92), un recall razonable (0.61) y, en consecuencia, el **mejor F1 (0.733) y AUC (0.946)**.

> **Modelo seleccionado: Random Forest.** Al combinar 300 árboles y promediar sus predicciones reduce el sobreajuste y captura relaciones no lineales que los modelos lineales no ven. Es la opción con mejor desempeño en las métricas relevantes para un problema desbalanceado.

---

## 5. Resultados, visualizaciones y métricas

El modelo Random Forest se evaluó con **cinco métricas**, cada una aportando una perspectiva distinta:

- **Accuracy = 0.940.** Acierta el 94% de las predicciones. En un problema desbalanceado esta cifra por sí sola sobreestima el desempeño (un modelo trivial rozaría el 86%), por lo que se interpreta junto a las demás.
- **Precision = 0.917.** De los vinos que declara "buenos", el 92% lo son realmente: falsos positivos mínimos, ideal cuando aprobar erróneamente un vino premium es costoso.
- **Recall = 0.611.** Detecta el 61% de los vinos buenos reales. Es la métrica más baja: por el desbalance, el modelo es conservador y deja escapar algunos buenos (falsos negativos). Es el principal punto de mejora.
- **F1-Score = 0.733.** Media armónica de precisión y recall; resume el equilibrio global y es el más alto de los tres modelos, lo que fundamenta la elección de Random Forest.
- **AUC = 0.946.** Capacidad de separar ambas clases a cualquier umbral; 0.95 frente a 0.5 del azar indica un poder discriminante excelente.

### Matriz de confusión

De **346 vinos "no buenos" acierta 343** (solo 3 falsos positivos) y de **54 vinos "buenos" identifica 33**. Los 21 falsos negativos reflejan visualmente el recall moderado.

<p align="center">
  <img src="img/fig_cm.png" width="380"><br>
  <em>Figura 5. Matriz de confusión de Random Forest.</em>
</p>

### Curva ROC

Mide la separación entre clases a lo largo de todos los umbrales de decisión. Un **AUC de 0.946** confirma un poder discriminante muy alto.

<p align="center">
  <img src="img/fig_roc.png" width="430"><br>
  <em>Figura 6. Curva ROC (AUC = 0.946).</em>
</p>

### Importancia de variables

El **alcohol domina claramente**, seguido de **sulphates** y **volatile acidity** (esta última con efecto negativo). Coincide con lo observado en el EDA, dando coherencia a todo el análisis.

<p align="center">
  <img src="img/fig_fi.png" width="580"><br>
  <em>Figura 7. Importancia relativa de las variables según Random Forest.</em>
</p>

---

## 6. Conclusión ejecutiva

El modelo **Random Forest** clasifica vinos tintos de alta calidad con alta fiabilidad (**Accuracy ≈ 94%, F1 ≈ 0.73, AUC ≈ 0.95**) usando únicamente mediciones de laboratorio. Esto permite a la bodega **filtrar y priorizar lotes prometedores de forma automática**, reduciendo costos y tiempos del control de calidad sensorial.

Más allá de la predicción, el análisis identifica **palancas accionables** —alcohol, sulfatos y acidez volátil— que orientan decisiones concretas de producción. Su principal límite es el **recall moderado** derivado del desbalance: el modelo prioriza la precisión y puede dejar escapar algunos vinos buenos. Como **recomendación y trabajo futuro** se sugiere aplicar técnicas de balanceo (p. ej. SMOTE) o ajustar el umbral de decisión según el costo de negocio de cada tipo de error, para recuperar más vinos buenos sin sacrificar demasiada precisión.

---

## Referencias

Cortez, P., Cerdeira, A., Almeida, F., Matos, T., & Reis, J. (2009). Modeling wine preferences by data mining from physicochemical properties. *Decision Support Systems, 47*(4), 547–553. https://doi.org/10.1016/j.dss.2009.05.016

Dua, D., & Graff, C. (2019). *UCI Machine Learning Repository: Wine Quality Data Set.* University of California, Irvine, School of Information and Computer Sciences. https://archive.ics.uci.edu/ml/datasets/wine+quality
