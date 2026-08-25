# 📊 Proyecto ENPIC: Evaluación y Análisis de Indicadores de Calidad en UCI

> *Repositorio oficial para el almacenamiento, procesamiento analítico y evaluación de indicadores de calidad asistencial y nutricional en pacientes críticos a partir de la "Base de datos ENPIC".*

---

## 📂 Estructura y Descripción de Archivos del Proyecto

El flujo de trabajo analítico y de control de calidad se encuentra modularizado en los siguientes scripts desarrollados en R Markdown, garantizando su reproducibilidad:

### 1. `escaneo_previo.Rmd` — Diagnóstico Estructural y Auditoría de Datos
Este archivo ejecuta la toma de contacto inicial con el conjunto de datos clínicos. Su propósito fundamental es asegurar la integridad de la información, auditar los datos faltantes y estructurar un diccionario de metadatos automatizado previo a cualquier fase inferencial.
*   **Librerías principales:** `haven` (importación de archivos SPSS), `dplyr` (manipulación de datos), `tidyr` (reestructuración tabular) y `DT` (generación de tablas interactivas).
*   **Funciones clave:** 
    *   `read_sav()` para la importación segura preservando las etiquetas nativas del formato `.sav`.
    *   Iteraciones funcionales (`sapply()` y `attr(x, "labels")`) para extraer los diccionarios de códigos categóricos originales.
    *   `ks.test()` (Test de Kolmogorov-Smirnov) para la evaluación algorítmica de la normalidad en variables continuas.
    *   `datatable()` para la renderización dinámica de un diccionario de datos.

### 2. `indicador1.Rmd` — Evaluación del Indicador 1: Identificación de Enfermos en Riesgo Nutricional (RN)
Este script implementa los criterios metodológicos del primer indicador de calidad, cuantificando la proporción de pacientes con estancias en UCI prolongadas (> 5 días) que cuentan con una valoración de riesgo nutricional debidamente registrada.
*   **Librerías principales:** `haven`, `dplyr` y `ggplot2` para la visualización.
*   **Funciones clave:**
    *   Lógica de filtrado y sumarización (`filter()`, `summarise()`) para aislar la población objetivo basada en la variable `DIASUCI` y evaluar la presencia de la escala `NUTRIC_Score`.
    *   `binom.test()` para el cálculo inferencial de intervalos de confianza exactos (95%).
    *   `ggplot()` junto con capas geométrica avanzadas (`geom_col()`, `geom_errorbar()`) para crear gráficos dinámicos que incorporen directamente los límites de error estándar (aproximación de Wald).

### 3. `indicador2.Rmd` — Evaluación del Indicador 2: Valoración del Estado Nutricional (EN)
Este script evalúa el nivel de cumplimiento en la realización de una valoración nutricional completa (ya sea por VSG o mediante la combinación de BMI y CONUT Score) sobre la totalidad de la cohorte en Riesgo Nutricional.
*   **Librerías principales:** `haven`, `dplyr` y `ggplot2`.
*   **Funciones clave:**
    *   Funciones de mutación condicional (`if_else()`) combinadas con operadores lógicos booleanos (`!is.na() | (!is.na() & !is.na())`) para rastrear el cumplimiento transversal de los criterios clínicos.
    *   Implementación paralela de soporte estadístico mediante `binom.test()` y generación de reportes formateados en consola mediante `cat()`.
    *   Canalización de los resúmenes de datos hacia `ggplot2` para asegurar que las visualizaciones y sus barras de error se actualicen automáticamente si la base de datos primaria muta.

---

## 📈 Informe Analítico e Interpretativo de los Indicadores

> *Nota metodológica: El análisis estadístico en entornos clínicos persigue identificar tendencias y grados de adhesión a protocolos asistenciales. La inferencia estadística no emite juicios categóricos deterministas, sino que aporta probabilidades; en este sentido, las siguientes consideraciones se derivan de las proporciones observadas.*

### Indicador 1: Identificación de Riesgo Nutricional
La evaluación de la cohorte objetivo (N=515) arroja un **97.28% de cumplimiento** [IC 95%: 95.48% - 98.51%] en la identificación de riesgo mediante *NUTRIC Score*.
*   **Adherencia clínica:** Los datos sugieren una implantación notablemente sólida y rutinaria del protocolo de cribado al ingreso, encontrándose la unidad a un margen estadísticamente muy estrecho del estándar teórico de excelencia (100%).
*   **Limitaciones metodológicas para inferencias avanzadas:** El escrutinio de los datos revela que el subgrupo de no cumplimiento es minúsculo (14 pacientes). Desde una perspectiva analítica, este volumen muestral tan reducido resta drásticamente potencia a cualquier prueba de contraste de hipótesis posterior. Los datos nos inducen a pensar que cualquier intento de modelización estadística (por ejemplo, buscar qué factores predisponen a no ser valorado) carecería de la robustez necesaria para extraer asociaciones válidas. Por ello, la aproximación estrictamente descriptiva adoptada resulta la más prudente.

### Indicador 2: Valoración del Estado Nutricional
Los resultados para el segundo indicador evidencian un cumplimiento excepcional del **99.43%** [IC 95%: 98.34% - 99.88%] sobre el total de la cohorte en riesgo (N=525).
*   **Consistencia asistencial:** La extremada estrechez del intervalo de confianza refuerza la idea de que la valoración (cruzando VSG, BMI y CONUT Score) se aplica de forma automatizada y universal.
*   **Reflexión estadística:** De manera análoga al indicador previo, el volumen de incumplimiento es virtualmente residual (3 pacientes). La dispersión de la varianza es tan pequeña que los datos indican que el proceso está fuertemente consolidado. Por ende, la literatura estadística desaconseja sobreanalizar variaciones marginales, orientando la conclusión hacia el mantenimiento sistemático de esta favorable práctica clínica.

---

## ⚙️ Especificaciones Técnicas del Entorno

*   **Lenguaje:** R (versión recomendada $\ge$ 4.0).
*   **Entorno de renderizado:** R Markdown (`.Rmd`) procesado vía consola mediante `rmarkdown::render()` o RStudio.
*   **Control de Versiones:** Git / GitHub (se aplica exclusión explícita de archivos primarios `.sav` y metadatos del SO mediante `.gitignore` para garantizar la protección de datos).
