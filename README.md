# 📊 Proyecto ENPIC: Evaluación y Análisis de Indicadores de Calidad en UCI

> *Repositorio oficial para el almacenamiento, procesamiento analítico y evaluación de indicadores de calidad asistencial y nutricional en pacientes críticos a partir de la "Base de datos ENPIC".*

---

## 📂 Estructura y Descripción de Archivos del Proyecto

El flujo de trabajo analítico y de control de calidad se encuentra modularizado en los siguientes scripts desarrollados en R Markdown:

### 1. `escaneo_previo.Rmd` — Diagnóstico Estructural y Auditoría de Datos
Este archivo ejecuta la toma de contacto inicial con el conjunto de datos clínicos. Su propósito fundamental es asegurar la integridad de la información, auditar los datos faltantes y estructurar un diccionario de metadatos automatizado previo a cualquier fase inferencial.

*   **Librerías principales:** `haven` (importación de SPSS), `dplyr` (manipulación de datos), `tidyr` (reestructuración tabular) y `DT` (generación de tablas interactivas).
*   **Funciones clave:** 
    *   `read_sav()` para la importación segura preservando las etiquetas nativas del formato `.sav`.
    *   Iteraciones mediante funciones anónimas (`sapply()` y `attr(x, "labels")`) para rescatar los diccionarios de códigos categóricos originales.
    *   `ks.test()` (Test de Kolmogorov-Smirnov) para la evaluación algorítmica de la normalidad en variables continuas.
    *   `datatable()` para la renderización de un diccionario de datos dinámico.

### 2. `indicador1.Rmd` — Evaluación del Indicador 1: Identificación de Enfermos en Riesgo Nutricional (RN)
Este script implementa los criterios metodológicos del primer indicador de calidad, cuyo objetivo es cuantificar el porcentaje de pacientes ingresados en UCI con estancias prolongadas que cuentan con una valoración del riesgo nutricional registrada.

*   **Librerías principales:** `dplyr` (filtrado y sumarización) y `ggplot2` (visualización de datos).
*   **Funciones clave:**
    *   `filter()` y `summarise()` para la selección de la población del denominador (pacientes con estancias superiores a 5 días) y el cálculo de proporciones de cumplimiento basadas en la escala `NUTRIC_Score`.
    *   `ggplot()` combinada con `geom_col()` y `geom_text()` para la representación gráfica automatizada y dinámica de los resultados.

---

## 📈 Informe Analítico e Interpretativo: Indicador 1

> *Nota metodológica: El análisis estadístico en entornos clínicos complejos busca identificar tendencias y grados de adhesión a los protocolos asistenciales más que emitir juicios categóricos deterministas.*

La evaluación del **Indicador 1 (Identificación de Enfermos en Riesgo Nutricional)** sobre la cohorte analizada arroja un **97.3% de cumplimiento** en la identificación mediante el uso del *NUTRIC Score* en pacientes con estancias prolongadas en la Unidad de Cuidados Intensivos. 

### Consideraciones Analíticas y Justificación de la Muestra
*   **Alto grado de adherencia:** Los datos sugieren una sólida implantación de los protocolos de cribado nutricional al ingreso en la práctica clínica habitual de la UCI, situándose muy próximos al estándar de excelencia teóricamente deseable (100%).
*   **Limitaciones para inferencias avanzadas:** El análisis descriptivo revela que únicamente 16 pacientes no cumplieron con el registro del indicador. Desde una perspectiva metodológica y estadística, tamaños de muestra tan reducidos en los grupos de no cumplimiento restan potencia a las pruebas de contraste de hipótesis. 
*   **Implicaciones:** Los datos disponibles inducen a pensar que cualquier intento de modelización estadística multivariante o estratificación analítica sobre este subgrupo carecería de la estabilidad y robustez necesarias para extraer conclusiones generalizables. Por consiguiente, se opta por una aproximación descriptiva y gráfica directa, evitando sobreinterpretar variaciones marginales en una muestra de incumplimiento tan acotada.

---

## 🛠️ Especificaciones Técnicas del Entorno

*   **Lenguaje:** R (versión recomendada $\ge$ 4.0).
*   **Entorno de renderizado:** R Markdown (`.Rmd`) exportado mediante `rmarkdown::render()` o directamente desde RStudio.
*   **Control de versiones:** Git / GitHub (con exclusión explícita de ficheros de datos primarios sensibles mediante `.gitignore`).
