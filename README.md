# Framingham Heart Risk Analysis

Análisis estadístico, inferencial y predictivo de factores de riesgo cardiovascular utilizando
el **Framingham Heart Study**, desarrollado como proyecto del curso **MCDI501: Estadística
Computacional para la Toma de Decisiones** (Magíster en Ciencia de Datos e Inteligencia
Artificial, Universidad Andrés Bello), bajo la metodología de Aprendizaje Basado en Proyectos
(ABP).

**Integrantes:** Fabian Castillo · Francisco Santelices · Renato Villazón
**Docente:** PhD. Jean Paul Maidana González

---

## 📋 Propósito del proyecto

El objetivo general es estimar, validar y **modelar predictivamente** los factores de riesgo
asociados al desarrollo de enfermedad coronaria a 10 años (`TenYearCHD`), avanzando de forma
acumulativa a través de las fases del método de proyectos del curso:

| Fase | Entregable | Foco |
|---|---|---|
| **Fase 2** | Sumativa 1 | Estadística descriptiva, intervalos de confianza, pruebas de hipótesis |
| **Fase 3** | Sumativa 2 | Simulación y remuestreo: bootstrap, permutación, Monte Carlo, robustez |
| **Fase 3–4** | Formativa 2 | Práctica de modelamiento: primera regresión logística |
| **Fase 4** | Sumativa 3 | Modelamiento predictivo integrado: imputación, 3 modelos logísticos, estabilidad y diagnósticos |

Cada fase se construye explícitamente sobre los resultados de la anterior. La **Sumativa 3** es
el cierre integrado: no repite análisis previos, sino que lleva al modelamiento predictivo
todo lo validado en S1 (correlaciones, outliers, faltantes) y S2 (correlaciones estables,
colinealidad resuelta, tamaño del efecto, representatividad del faltante), incorporando además
las tres correcciones señaladas por el docente en la retroalimentación de S2.

---

## 🗂️ Arquitectura del repositorio

```
framingham-heart-risk-analysis/
├── data/
│   ├── framingham.csv                # Dataset original (Kaggle)
│   └── framingham_imputado.csv       # Dataset tras imputación por regresión (generado en S3)
├── figs/
│   ├── sumativa1/                    # Figuras de la Sumativa 1
│   ├── sumativa2/                    # Figuras de la Sumativa 2
│   └── sumativa3/                    # Figuras de la Sumativa 3 (8 figuras)
├── notebook/
│   ├── Sumativa_1.ipynb              # Fase 2: descriptiva, IC, pruebas de hipótesis
│   ├── Sumativa_2.ipynb              # Fase 3: bootstrap, permutación, Monte Carlo, robustez
│   ├── Formativa_2.ipynb             # Práctica: primera regresión logística
│   └── Sumativa_3.ipynb              # Fase 4: imputación + 3 modelos + estabilidad + diagnósticos
├── informe/                          # Informes técnicos (PDF/DOCX) por entrega
├── .gitignore
├── requirements.txt
└── README.md
```

**Convención de rutas:** los notebooks viven en `notebook/` y referencian datos y figuras con
rutas relativas (`../data/…`, `../figs/sumativaN/`), por lo que **deben ejecutarse desde su
ubicación original** dentro de `notebook/`.

---

## 📊 Dataset

- **Fuente:** [Framingham Heart Study](https://www.kaggle.com/datasets/aasheesh200/framingham-heart-study-dataset) (Kaggle)
- **Tamaño original:** 4.240 registros × 16 variables demográficas, conductuales y clínicas
- **Variable objetivo:** `TenYearCHD` (enfermedad coronaria a 10 años, binaria; ≈15,2% positivos)
- **Valores faltantes:** concentrados en `glucose` (9,15%), `education` (2,48%), `BPMeds`,
  `totChol`, `cigsPerDay`, `BMI`, `heartRate`.

---

## 🔬 Contenido de la Sumativa 3 (`notebook/Sumativa_3.ipynb`)

El notebook está organizado en checkpoints alineados 1:1 con la rúbrica (108 pts):

**1. Imputación y manejo de datos faltantes (21 pts)**
- Clasificación formal del patrón de faltantes: se confirma **MAR** (Missing At Random),
  probando que la ausencia de cada variable se asocia con variables observadas (p. ej. la
  ausencia de `BMI` se relaciona con `prevalentStroke` y con `TenYearCHD`).
- **Imputación por regresión lineal múltiple** de las 5 variables numéricas con faltantes, en
  orden secuencial, con predictores justificados en S1/S2.
- Comparación de 3 estrategias (eliminación, imputación simple, imputación por regresión) en
  tamaño muestral, distribución y preservación de correlaciones.

**2. Clasificación mediante regresión logística — 3 modelos (30 pts)**
- **Modelo 1** (informado por S1/S2): `age`, `pulsePressure`, `totChol`, `glucose`, `male`.
- **Modelo 2** (stepwise forward por p-valor).
- **Modelo 3** (mejor subconjunto por AIC, búsqueda exhaustiva de 8.191 combinaciones).
- Interpretación de coeficientes, odds ratios y significancia; matrices de confusión, métricas
  y curvas ROC/AUC para los tres.

**3. Estabilidad y bootstrap del modelo final (30 pts)**
- Bootstrap de coeficientes (10.000 remuestras), comparando IC bootstrap vs. Wald.
- Diagnósticos: VIF, linealidad en el logit (Box-Tidwell), observaciones influyentes
  (distancia de Cook) y análisis de residuos (Pearson y desviancia).

**4. Análisis comparativo del impacto de la imputación (9 pts)**
- El modelo final se reajusta sobre las tres estrategias de datos faltantes y se comparan
  coeficientes, significancia y desempeño, documentando el trade-off encontrado.

**4.5 Apéndice — corrección del Monte Carlo de S2** (atiende la retroalimentación docente):
reemplaza la normal bivariada por muestreo desde la distribución empírica, reduciendo el error
de estimación del riesgo conjunto de 5,54 a 0,57 puntos porcentuales.

**5. Síntesis integrada S1 → S2 → S3** con recomendaciones metodológicas futuras.

### Trazabilidad con la retroalimentación del docente (S2)

| Instrucción del docente | Dónde se atiende |
|---|---|
| Priorizar `glucose`/`diabetes` (d≈4,8) | Selección de variables del Modelo 1 (Sección 2.2) |
| Usar `pulsePressure` en vez de `sysBP`+`diaBP` | Todos los modelos; VIF verificado (Sección 3.2) |
| Descartar `heartRate` como predictor aislado | Universo de candidatas (Sección 2.2) |
| Hacerse cargo del sesgo de sexo con imputación | Estrategia final = imputación por regresión (Sección 1.5) |
| Monte Carlo con distribución empírica | Sección 4.5 |

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
source venv/bin/activate        # Linux / macOS
venv\Scripts\activate           # Windows
```

### 3. Instalar dependencias

Todas las librerías necesarias (incluidas las nuevas de S3: `statsmodels` para regresión
logística/VIF, y los módulos de `scikit-learn` para imputación y métricas) están en
`requirements.txt`:

```bash
pip install -r requirements.txt
```

> En sistemas Linux recientes con Python gestionado externamente, agregar `--break-system-packages`
> si `pip` lo solicita.

### 4. Ejecutar los notebooks

```bash
jupyter notebook
```

Todos los notebooks están diseñados para ejecutarse de principio a fin sin intervención
(*Restart Kernel and Run All*): la semilla (`SEED = 42`) se fija al inicio, los datos se cargan
con rutas relativas, y las figuras se guardan automáticamente en `figs/sumativaN/`.

**Orden recomendado de ejecución:** `Sumativa_1` → `Sumativa_2` → `Sumativa_3`. El notebook de
la Sumativa 3 es autocontenido (parte de `data/framingham.csv` y realiza su propia imputación),
por lo que puede ejecutarse de forma independiente.

### 5. Verificación rápida de reproducibilidad

Tras ejecutar `Sumativa_3.ipynb` completo, estos valores deben coincidir exactamente:

| Verificación | Valor esperado |
|---|---|
| Registros tras imputación | 4.240 (sin pérdida) |
| Patrón de faltantes | MAR |
| Modelo final (stepwise) — AUC en prueba | ≈0,705 |
| Bootstrap de coeficientes | 10.000 remuestras, coincidencia total con Wald |
| Monte Carlo empírico — error vs. real | 0,57 pp (vs. 5,54 pp de la normal bivariada) |

---

## 🧩 Convenciones metodológicas del proyecto

- **Semilla:** `SEED = 42` en todos los notebooks (reproducibilidad total).
- **Nivel de significancia:** `ALPHA = 0.05`.
- **Remuestreo:** mínimo 10.000 iteraciones (bootstrap, permutación, Monte Carlo).
- **Partición:** train/test 70/30 estratificada por la variable objetivo (por el desbalance de
  clases, ≈15,2% CHD).
- **Estandarización:** `StandardScaler` ajustado **solo** con el conjunto de entrenamiento.
- **Pruebas t:** Welch (`equal_var=False`) por defecto.
- **Intervalos de proporciones:** método de Wilson.
- **Generación aleatoria:** `numpy.random.default_rng(seed)` pasado explícitamente a cada
  función que remuestrea.
- **Colinealidad:** `sysBP`/`diaBP` reemplazadas por `pulsePressure` (VIF≈3 → ≈1,3).
- **Estilo de código:** funciones con *docstrings* (NumPy) y *type hints*; constantes definidas
  una sola vez al inicio de cada notebook.

---

## 📈 Estado del proyecto

- ✅ Sumativa 1 (Fase 2): entregada y corregida.
- ✅ Sumativa 2 (Fase 3): entregada (puntaje perfecto), con las correcciones de S1 incorporadas.
- ✅ Formativa 2: práctica de modelamiento completada.
- ✅ Sumativa 3 (Fase 4): notebook completo, ejecutado de principio a fin sin errores ni
  advertencias, con las 3 instrucciones de la retroalimentación de S2 atendidas.

---

## 📚 Fuente de datos

National Heart, Lung, and Blood Institute. *The Framingham Heart Study*. Dataset en Kaggle:
`aasheesh200/framingham-heart-study-dataset`.
