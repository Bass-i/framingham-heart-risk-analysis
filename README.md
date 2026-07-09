# Framingham Heart Risk Analysis

Análisis estadístico e inferencial de factores de riesgo cardiovascular utilizando el
**Framingham Heart Study**, desarrollado como proyecto del curso **MCDI501: Estadística
Computacional para la Toma de Decisiones** (Magíster en Ciencia de Datos e Inteligencia
Artificial, Universidad Andrés Bello), bajo la metodología de Aprendizaje Basado en Proyectos
(ABP).

**Integrantes:** Fabian Castillo · Francisco Santelices · Renato Villazón
**Docente:** PhD. Jean Paul Maidana González

---

## 📋 Propósito del proyecto

El objetivo general es estimar y validar factores de riesgo asociados al desarrollo de
enfermedad coronaria a 10 años (`TenYearCHD`), avanzando progresivamente a través de las tres
fases del método de proyectos del curso:

| Fase | Entregable | Foco |
|---|---|---|
| **Fase 2** | Sumativa 1 | Estadística descriptiva, intervalos de confianza, pruebas de hipótesis |
| **Fase 3** | Sumativa 2 | Simulación y remuestreo: bootstrap, permutación, Monte Carlo, robustez |
| **Fase 4** | Sumativa 3 *(próxima)* | Modelado predictivo: regresión logística, árboles, Random Forest |

Cada fase se construye explícitamente sobre los resultados de la anterior: la Sumativa 2 no
repite el análisis exploratorio de la Sumativa 1, sino que **valida computacionalmente** sus
intervalos de confianza, pruebas de hipótesis y correlaciones mediante técnicas de remuestreo
y simulación, dejando además un conjunto de resultados e insumos ya preparados para el
modelado predictivo de la Sumativa 3.

---

## 🗂️ Arquitectura del repositorio

```
framingham-heart-risk-analysis/
├── data/
│   └── framingham.csv          # Dataset original (Kaggle: aasheesh200/framingham-heart-study-dataset)
├── figs/
│   ├── sumativa1/               # Figuras generadas por el notebook de la Sumativa 1
│   └── sumativa2/               # Figuras generadas por el notebook de la Sumativa 2
├── notebook/
│   ├── Sumativa_1.ipynb         # Fase 2: descriptiva, IC, pruebas de hipótesis
│   └── Sumativa_2.ipynb         # Fase 3: bootstrap, permutación, Monte Carlo, robustez
├── .gitignore
├── requirements.txt
└── README.md
```

**Convención de rutas:** ambos notebooks viven en `notebook/` y referencian los datos y las
figuras con rutas relativas (`../data/framingham.csv`, `../figs/sumativa1/` o
`../figs/sumativa2/`), por lo que **deben ejecutarse desde su ubicación original** dentro de
`notebook/` para que las rutas relativas funcionen correctamente.

---

## 📊 Dataset

- **Fuente:** [Framingham Heart Study](https://www.kaggle.com/datasets/aasheesh200/framingham-heart-study-dataset) (Kaggle)
- **Tamaño original:** 4.240 registros × 16 variables demográficas, conductuales y clínicas
- **Variable objetivo:** `TenYearCHD` (desarrollo de enfermedad coronaria a 10 años, binaria)
- **Limpieza:** `dropna()` (listwise deletion) → **3.658 registros completos** (582 registros
  excluidos, 13,7% del total). La Sumativa 2 evalúa explícitamente si estos registros excluidos
  difieren sistemáticamente de los conservados (Sección 6 del notebook de Sumativa 2).

---

## 🔬 Contenido de cada notebook

### `notebook/Sumativa_1.ipynb` — Fase 2

1. Preparación y carga de datos (faltantes por variable, `dropna()`)
2. Análisis exploratorio (estadística descriptiva, correlaciones, visualizaciones)
3. Estimación de parámetros: 4 intervalos de confianza (edad, colesterol, presión sistólica —
   distribución t de Student — y proporción de CHD — método de Wilson)
4. Pruebas de hipótesis: edad~sexo (t de Welch), tabaquismo~CHD (chi-cuadrado), glucosa~diabetes
   (t de Welch)

### `notebook/Sumativa_2.ipynb` — Fase 3

Todo el trabajo de esta fase usa **exclusivamente parámetros y resultados de la Sumativa 1**
como punto de partida, con semilla fija (`SEED = 42`) y un mínimo de 10.000 iteraciones en cada
procedimiento de remuestreo/simulación:

1. **Bootstrap de parámetros poblacionales** — validación de los 4 IC de S1 (percentil y BCa)
2. **Permutación** — validación de dos pruebas de hipótesis de S1 (glucosa~diabetes y
   tabaquismo~CHD), incluyendo **tamaño del efecto** (d de Cohen, V de Cramér)
3. **Estabilidad de correlaciones** — IC bootstrap para 4 correlaciones relevantes
4. **Simulación Monte Carlo** — escenario de riesgo conjunto (hipertensión + hipercolesterolemia)
   basado en parámetros de S1, con verificación de convergencia
5. **Análisis de robustez** — jackknife y sensibilidad a outliers
6. **Representatividad de los registros excluidos** — comparación estadística entre los 582
   registros excluidos por `dropna()` y los 3.658 conservados
7. **Diagnóstico y resolución de colinealidad** — VIF sobre `sysBP`/`diaBP` y recomendación de
   variable combinada para el modelo predictivo de S3
8. **Síntesis de resultados validados** — tabla resumen e insumos preparados para la Sumativa 3

---

## ⚙️ Cómo usar este repositorio

### 1. Clonar el repositorio

```bash
git clone https://github.com/Bass-i/framingham-heart-risk-analysis.git
cd framingham-heart-risk-analysis
```

### 2. Crear un entorno virtual (recomendado)

```bash
python3 -m venv venv

# Activar el entorno
source venv/bin/activate        # Linux / macOS
venv\Scripts\activate           # Windows
```

### 3. Instalar dependencias

Todas las librerías necesarias están fijadas en `requirements.txt`:

```bash
pip install -r requirements.txt
```

**Contenido de `requirements.txt`:**

```
pandas>=2.0.0
numpy>=1.24.0
matplotlib>=3.7.0
seaborn>=0.12.0
scipy>=1.10.0
statsmodels>=0.14.0
jupyter>=1.0.0
ipykernel>=6.0.0
kagglehub
```

> Si aparece un error de instalación en sistemas Linux recientes (Debian/Ubuntu con Python
> gestionado externamente), agregar el flag `--break-system-packages`:
> `pip install -r requirements.txt --break-system-packages`

### 4. Ejecutar los notebooks

Abrir Jupyter desde la raíz del proyecto:

```bash
jupyter notebook
```

Y navegar a `notebook/Sumativa_1.ipynb` o `notebook/Sumativa_2.ipynb`. Ambos notebooks están
diseñados para ejecutarse de **principio a fin sin intervención manual**
(`Cell → Run All` / *Restart Kernel and Run All*), ya que:

- La semilla (`SEED = 42`) se fija al inicio de cada notebook, garantizando resultados
  idénticos en cada ejecución.
- El dataset se descarga/carga automáticamente desde `data/framingham.csv` con rutas relativas.
- Las figuras se guardan automáticamente en `figs/sumativa1/` o `figs/sumativa2/` según
  corresponda.

Si el archivo `data/framingham.csv` no está presente, puede obtenerse ejecutando la celda de
descarga vía `kagglehub` incluida en el notebook de la Sumativa 1, o descargándolo manualmente
desde el [enlace de Kaggle](https://www.kaggle.com/datasets/aasheesh200/framingham-heart-study-dataset)
indicado arriba y ubicándolo en `data/framingham.csv`.

### 5. Verificación rápida de reproducibilidad

Tras ejecutar cualquiera de los dos notebooks completos, los siguientes valores deben
coincidir exactamente (confirmando que el entorno está correctamente configurado):

| Verificación | Valor esperado |
|---|---|
| Registros tras `dropna()` | 3.658 |
| Edad media | 49,55 años |
| Colesterol medio | 236,85 mg/dL |
| Proporción de CHD | 15,23% |

---

## 🧩 Convenciones metodológicas del proyecto

- **Semilla:** `SEED = 42` en ambos notebooks, para reproducibilidad total.
- **Nivel de significancia:** `ALPHA = 0.05` en todas las pruebas de hipótesis e intervalos.
- **Iteraciones de remuestreo/simulación (Sumativa 2):** mínimo 10.000 (bootstrap, permutación,
  Monte Carlo), según lo exigido por la rúbrica de la evaluación.
- **Pruebas t:** prueba de Welch (`equal_var=False`) por defecto en todas las comparaciones de
  dos grupos, sin asumir homocedasticidad.
- **Intervalos de proporciones:** método de Wilson en lugar de Wald, más preciso cuando la
  proporción muestral se aleja de 0,5.
- **Interpretación de intervalos de confianza:** siempre sobre el *procedimiento* de
  construcción del intervalo (frecuentista), nunca como una afirmación probabilística sobre el
  parámetro fijo ni sobre un solo límite del intervalo.
- **Generación de números aleatorios:** `numpy.random.default_rng(seed)` (API moderna de
  NumPy), pasado explícitamente a cada función en la Sumativa 2, evitando depender de estado
  aleatorio global.
- **Estilo de código:** funciones documentadas con *docstrings* y *type hints*; constantes
  (semilla, alfa, número de iteraciones) definidas una sola vez al inicio de cada notebook.

---

## 📈 Estado actual y próximos pasos

- ✅ Sumativa 1 (Fase 2): entregada y corregida según retroalimentación docente.
- ✅ Sumativa 2 (Fase 3): notebook completo, ejecutado de principio a fin sin errores,
  incorporando las correcciones solicitadas sobre la Sumativa 1 (tamaño del efecto,
  representatividad muestral, diagnóstico de colinealidad).
- ⏳ Sumativa 3 (Fase 4): modelado predictivo (regresión logística, árboles de decisión,
  Random Forest), a partir de los resultados validados y las recomendaciones documentadas al
  cierre del notebook de la Sumativa 2 — incluyendo el uso de `pulsePressure` en lugar de
  `sysBP`/`diaBP` por separado, y la exclusión de `heartRate` como predictor bivariado aislado.

---

## 📚 Fuente de datos y referencias principales

- National Heart, Lung, and Blood Institute. (2020). *The Framingham Heart Study*. U.S.
  Department of Health and Human Services.
- Dataset en Kaggle: `aasheesh200/framingham-heart-study-dataset`
