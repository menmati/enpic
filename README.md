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

### 4. `indicador4.Rmd` — Evaluación del Indicador 4: Adecuación del Aporte Calórico y Proteico del Soporte Nutricional
Este script evalúa la precisión terapéutica del soporte nutricional en la fase estable del paciente (día 4 de ingreso), cuantificando simultáneamente la proporción que alcanza la meta calórica (20–30 kcal/kg de peso de referencia) y la meta proteica (≥ 1,3 g/kg/día) sobre la cohorte con soporte nutricional activo durante al menos 4 días y con registros válidos. El peso de referencia se calcula de forma dinámica: peso actual si IMC < 30, o peso ajustado si IMC ≥ 30.
*   **Librerías principales:** `haven`, `dplyr`, `tidyr`, `ggplot2`, `knitr`, `nnet` (regresión logística multinomial), `pROC` (curvas ROC), `purrr` (iteración funcional), `survival` y `survminer` (análisis de supervivencia), `broom` (extracción tidy de modelos).
*   **Funciones clave:**
    *   Familias de funciones modulares (`crear_fila_kw()`, `crear_fila_anova()`, `crear_fila_chi()`, `crear_fila_chi_si()`) para la construcción automatizada de tablas univariantes con Kruskal-Wallis, ANOVA y chi-cuadrado, respectivamente.
    *   `case_when()` para la categorización tricotómica del aporte calórico (hipoalimentación / normoalimentación / sobrealimentación) y `prop.test()` para los intervalos de confianza de proporciones.
    *   `multinom()` de `nnet` para la regresión logística multinomial; `coxph()` y `cox.zph()` de `survival` para los modelos de riesgos proporcionales y la verificación del supuesto de Schoenfeld.
    *   `roc()` y `auc()` de `pROC` para la cuantificación de la capacidad discriminativa de los modelos; `ggsurvplot()` de `survminer` para las curvas de Kaplan-Meier a 28 días según grupo calórico y proteico.
    *   Bucle de evaluación masiva de modelos mediante `map()` y combinaciones generadas con `combn()` para la selección del modelo parsimonioso óptimo por equilibrio AIC-AUC.

### 5. `indicador6.Rmd` — Evaluación del Indicador 6: Nutrición Enteral Precoz
Este script cuantifica la proporción de pacientes con nutrición enteral documentada que la recibieron dentro de las primeras 48 horas desde el ingreso en UCI, aplicando previamente una depuración cronológica y clínica de los registros de fechas y excluyendo los casos con nutrición parenteral total iniciada antes de la enteral.
*   **Librerías principales:** `haven`, `dplyr`, `tidyr`, `ggplot2`, `lubridate` (manipulación de fechas), `knitr`, `pROC`, `purrr`, `survival`, `survminer`, `broom`, `scales`.
*   **Funciones clave:**
    *   `parse_date_time()` de `lubridate` con múltiples formatos posibles (`orders = c("Ymd_HMS", "dmy_HMS"...)`) para el parseo seguro y robusto de variables de fecha/hora en formato heterogéneo.
    *   `difftime()` para el cálculo del retraso en horas entre el ingreso en UCI y el inicio de la nutrición enteral.
    *   `formatear_p()` como función auxiliar de formato dinámico de p-valores y familias de funciones modulares (`crear_fila_mw_iq6()`, `crear_fila_ttest_iq6()`, `crear_fila_chi_iq6()`, `crear_fila_chi_si_iq6()`) para la tabla univariante comparativa según inicio precoz o tardío de la nutrición enteral.
    *   `evaluar_modelo_iq6()` para la evaluación masiva de combinaciones de predictores en la regresión logística binaria, seleccionando el modelo final por ranking equilibrado AIC-AUC-parsimonia.
    *   `coxph()` y análisis de supervivencia a 28 días (Kaplan-Meier log-rank) para el estudio del impacto en mortalidad según el grupo de inicio de nutrición enteral.

### 6. `indicador7.Rmd` — Evaluación del Indicador 7: Uso Adecuado de Nutrición Parenteral Total (NPT)
Este script evalúa la adecuación de la indicación de la nutrición parenteral total (NPT) en los pacientes que la recibieron, tomando como criterio de cumplimiento la existencia de una justificación clínica documentada mediante contraindicación registrada de la vía enteral. Se aplican tanto análisis univariantes como modelos multivariantes logísticos y de Cox, incluyendo la verificación del supuesto de proporcionalidad de riesgos.
*   **Librerías principales:** `haven`, `dplyr`, `tidyr`, `ggplot2`, `knitr`, `pROC`, `purrr`, `survival`, `survminer`, `broom`.
*   **Funciones clave:**
    *   `registro_valido()` como función auxiliar para la identificación de pacientes con indicación documentada de NPT a partir de variables de contraindicación de la vía enteral.
    *   Familias de funciones modulares (`crear_fila_mw_iq7()`, `crear_fila_ttest_iq7()`, `crear_fila_chi_iq7()`, `crear_fila_chi_si_iq7()`) para la construcción de la tabla univariante según justificación o ausencia de justificación documental de la NPT.
    *   `evaluar_modelo_iq7()` y `evaluar_cox_iq7()` para la evaluación masiva de combinaciones de predictores en la regresión logística binaria y en el modelo de Cox, respectivamente, con selección del modelo óptimo por ranking equilibrado AIC-AUC/concordancia-parsimonia.
    *   `cox.zph()` para la comprobación formal del supuesto de riesgos proporcionales en el modelo de Cox final; `ggsurvplot()` para las curvas de Kaplan-Meier a 28 días.

### 7. `indicador8.Rmd` — Evaluación del Indicador 8: Adecuación Temporal del Inicio de la Nutrición Parenteral Complementaria (NPC)
Este script evalúa si los pacientes con nutrición enteral insuficiente al cuarto día (< 60% de los requerimientos; < 15 kcal/kg) recibieron nutrición parenteral complementaria dentro de la ventana temporal de 96 a 120 horas desde el inicio de la nutrición enteral. Dado el número extremadamente bajo de eventos favorables, el análisis principal es descriptivo, complementado con un seguimiento longitudinal del rescate calórico entre los días 5 y 14.
*   **Librerías principales:** `haven`, `dplyr`, `tidyr`, `lubridate`, `ggplot2`, `knitr`.
*   **Funciones clave:**
    *   `parse_date_time()` y `difftime()` para el cálculo preciso de las horas transcurridas entre el inicio de la nutrición enteral y el inicio de la nutrición parenteral complementaria.
    *   `pivot_longer()` para la transformación de los registros calóricos diarios (días 5 al 14) a formato longitudinal, permitiendo el seguimiento dinámico del rescate calórico por paciente.
    *   `binom.test()` de Clopper-Pearson para el cálculo del intervalo de confianza exacto binomial del indicador.
    *   Justificación explícita de la ausencia de modelización inferencial: separación casi completa, inestabilidad estimativa e insuficiencia de eventos favorables.

### 8. `indicador9.Rmd` — Evaluación del Indicador 9: Monitorización de la Nutrición Enteral
Este script evalúa la completitud integral del seguimiento documental de los pacientes con nutrición enteral de al menos 7 días de duración, aplicando una regla de cumplimiento estricta "todo o nada" sobre cinco bloques de monitorización: aporte calórico diario (días 1 al 7), ionograma basal, analítica del séptimo día, control general de complicaciones y vigilancia digestiva. Incluye un análisis de los fallos documentales por bloque, tablas univariantes, modelos multivariantes y análisis de supervivencia.
*   **Librerías principales:** `haven`, `dplyr`, `tidyr`, `ggplot2`, `kableExtra`, `knitr`, `pROC`, `purrr`, `survival`, `survminer`, `broom`.
*   **Funciones clave:**
    *   `asegurar_columnas_iq9()` para garantizar la presencia de todas las variables esperadas en el subconjunto analítico, independientemente de la estructura de la base de datos en cada ejecución.
    *   Familias de funciones modulares (`crear_fila_mw_iq9()`, `crear_fila_ttest_iq9()`, `crear_fila_chi_iq9()`, `crear_fila_chi_si_iq9()`) para la construcción de la tabla univariante comparativa entre pacientes con cumplimiento integral y sin él.
    *   `evaluar_modelo_iq9()` y `evaluar_modelo_cox_iq9()` para la evaluación masiva de combinaciones de predictores en regresión logística binaria y modelo de Cox, con selección del modelo óptimo por ranking equilibrado AIC-AUC/concordancia-parsimonia.
    *   `kbl()` de `kableExtra` para la renderización de tablas formateadas con sombreado de filas alternado y destacado de los grupos documentales.

### 9. `indicador10.Rmd` — Evaluación del Indicador 10: Monitorización de la Aparición de Nutritrauma en Pacientes con TMN
Este script evalúa la completitud del registro de monitorización preventiva del nutritrauma en pacientes con tratamiento médico nutricional (TMN) de al menos 7 días. La operacionalización se estructuró en cuatro bloques documentales: aportes nutricionales diarios, balance hídrico, electrolitos seriados y toxicidad hepato-lipídica semanal. La tasa de cumplimiento resultante fue prácticamente residual, lo que refleja una fractura crítica de la trazabilidad documental de la seguridad metabólica del TMN.
*   **Librerías principales:** `haven`, `dplyr`, `tidyr`, `ggplot2`, `knitr`.
*   **Funciones clave:**
    *   Evaluación bloque a bloque de la disponibilidad de variables mediante `any_of()` y `all()`, identificando la ausencia de datos como incumplimiento del criterio de monitorización.
    *   Desglose microscópico de omisiones por variable individual mediante `summarise()`, para identificar los parámetros con mayor tasa de pérdida documental.
    *   Gráfico de porcentaje de omisión por parámetro clínico (`geom_bar()`) para visualizar los cuellos de botella del registro, con el bloque hídrico (diuresis de 24 horas) como principal determinante estructural del incumplimiento.
    *   Justificación explícita de la ausencia de modelización inferencial: con solo 5 eventos favorables frente a 371 incumplimientos, el análisis multivariante carecería de validez estadística.

### 10. `indicador11.Rmd` — Evaluación del Indicador 11: Incidencia de Nutritrauma en Pacientes con TMN
Este script cuantifica la incidencia de eventos compatibles con nutritrauma en la cohorte con tratamiento médico nutricional documentado, operacionalizando el indicador mediante la detección de alteraciones metabólico-orgánicas de nueva aparición: hiperuremia relevante, hipertrigliceridemia, disfunción hepática global y colestasis. El resultado supera el estándar máximo aceptable definido para el indicador (≤ 15%), por lo que se realizan análisis univariantes, modelos multivariantes (logístico y Cox) y análisis de supervivencia a 28 días.
*   **Librerías principales:** `haven`, `dplyr`, `tidyr`, `ggplot2`, `knitr`, `pROC`, `purrr`, `survival`, `survminer`, `broom`, `scales`.
*   **Funciones clave:**
    *   Lógica de detección multidominio mediante operadores `|` y `&` sobre variables bioquímicas seriadas para construir la variable de evento compuesto del IQ11.
    *   Familias de funciones modulares (`crear_fila_mw_iq11()`, `crear_fila_ttest_iq11()`, `crear_fila_chi_iq11()`, `crear_fila_chi_si_iq11()`) para la tabla univariante comparativa entre pacientes con y sin eventos compatibles con nutritrauma.
    *   `evaluar_modelo_iq11()` y `evaluar_modelo_cox_iq11()` para la selección del modelo multivariante óptimo por ranking equilibrado AIC-AUC-parsimonia en regresión logística binaria y regresión de Cox, respectivamente.
    *   `cox.zph()` para la verificación del supuesto de proporcionalidad de riesgos; `ggsurvplot()` para la visualización de las curvas de Kaplan-Meier a 28 días según presencia o ausencia de eventos compatibles con nutritrauma.

### 11. `indicador12.Rmd` — Evaluación del Indicador 12: Definición de Disfunción Hepática Asociada a la Nutrición Parenteral (DHANP)
Este script estima la incidencia de nueva disfunción hepática compatible con DHANP al séptimo día en pacientes con nutrición parenteral prolongada (≥ 7 días) y sin alteración hepática basal documentada. El algoritmo aplica una depuración clínico-analítica estricta antes de calcular el indicador, y el desglose final identifica los patrones predominantes de daño: colestásico, mixto o citolítico.
*   **Librerías principales:** `haven`, `dplyr`, `tidyr`, `ggplot2`, `forcats`.
*   **Funciones clave:**
    *   Lógica de depuración en dos etapas: primero se identifica la ausencia de alteración hepática basal (día 1) mediante umbrales clínicos sobre bilirrubina, FA, GGT y transaminasas; después se detecta la aparición de nueva alteración al séptimo día bajo los mismos criterios.
    *   `fct_infreq()` de `forcats` para la reordenación automática de los patrones clínicos de DHANP según su frecuencia, garantizando una visualización ordenada y reproducible.
    *   Justificación explícita de la ausencia de modelización inferencial: con solo 33 pacientes evaluables y 16 eventos, el análisis multivariante presenta riesgo elevado de sobreajuste e inestabilidad estimativa.

### 12. `indicador16.Rmd` — Evaluación del Indicador 16: Profilaxis de la Úlcera por Estrés en Pacientes Críticos con NE
Este script evalúa la cobertura de la profilaxis farmacológica antiulcerosa en los pacientes críticos con criterios clínicos de riesgo de hemorragia gastrointestinal por estrés: ausencia de nutrición enteral, coagulopatía, shock o fracaso renal agudo. El indicador se calcula bajo el supuesto de prescripción universal declarado por la unidad, y se realiza una auditoría complementaria de potencial sobreindicación en pacientes sin criterios objetivos documentados.
*   **Librerías principales:** `haven`, `dplyr`, `tidyr`, `ggplot2`, `knitr`.
*   **Funciones clave:**
    *   Construcción de la variable de riesgo gastrointestinal mediante operadores lógicos combinados (`|`) sobre las variables de nutrición enteral, coagulopatía, shock y fracaso renal agudo.
    *   Auditoría de sobremedicación potencial: cuantificación del subgrupo de pacientes sin criterios objetivos de riesgo que habrían recibido profilaxis bajo la política universal de la unidad.
    *   Justificación explícita de la ausencia de modelización inferencial: el desenlace del indicador no presenta variabilidad cuando se asume cobertura universal, lo que impide la construcción de comparaciones estadísticas válidas entre cumplidores e incumplidores.

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

### Indicador 4: Adecuación del Aporte Calórico y Proteico del Soporte Nutricional
El IQ4 evidenció un **cumplimiento claramente insuficiente** en ambas metas terapéuticas al cuarto día de soporte nutricional (N evaluable = 290).
*   **Adecuación calórica (IQ4 Kcal):** Solo el **44.48%** de los pacientes alcanzó el rango óptimo de 20–30 kcal/kg/día [IC 95%: 38.7% – 50.4%], situándose por debajo del estándar de ≥ 80%. El patrón predominante de inadecuación fue la **hipoalimentación** (52,0%), siendo la sobrealimentación infrecuente (7,2%). Los modelos multivariantes identificaron la nutrición enteral exclusiva y la ventilación mecánica como factores asociados a un mayor riesgo de hipoalimentación.
*   **Adecuación proteica (IQ4 Prot):** El cumplimiento fue aún menor: solo el **21.03%** alcanzó el umbral de ≥ 1,3 g/kg/día [IC 95%: 16.4% – 26.4%]. La probabilidad de cumplir la meta proteica se asoció de forma independiente a un mayor BMI y mayor aporte calórico, y disminuyó en pacientes médicos (vs. quirúrgicos) y en aquellos con ventilación mecánica (AUC logístico = 0,732).
*   **Señal de alerta para la auditoría:** El bajo cumplimiento proteico es el hallazgo con mayor relevancia clínica del IQ4. Los resultados demuestran que no basta con alcanzar el objetivo energético; la proteína debe monitorizarse de forma independiente. En consecuencia, las estrategias de mejora deben centrarse en revisar la progresión diaria del soporte, detectar precozmente a los pacientes estancados y optimizar la composición de las fórmulas.

### Indicador 6: Nutrición Enteral Precoz
El IQ6 evidenció un **cumplimiento aceptable aunque mejorable** del inicio precoz de la nutrición enteral, con el **73.19%** de los pacientes evaluables iniciando la NE dentro de las primeras 48 horas desde el ingreso en UCI [IC 95%: 68.0% – 78.0%] (N evaluable = 317, tras exclusión de fechas no parseables y pacientes con NPT previa a la NE).
*   **Patrón asistencial:** Aproximadamente tres de cada cuatro pacientes recibieron NE dentro de la ventana temporal recomendada. No se observaron diferencias significativas en gravedad basal, comorbilidades ni mortalidad a 28 días entre los grupos de inicio precoz y tardío. La diferencia más relevante fue nutricional: los pacientes con inicio precoz alcanzaron un mayor aporte proteico medio.
*   **Modelo multivariante:** Un mayor aporte proteico medio se asoció con menor probabilidad de inicio tardío (OR 0,38; p = 0,018), mientras que la necesidad de NPT complementaria posterior se asoció con mayor probabilidad de inicio tardío (OR 2,22; p = 0,030). El modelo presentó una capacidad discriminativa limitada (AUC = 0,618), por lo que no debe utilizarse con fines predictivos individuales.
*   **Señal de alerta para la auditoría:** La mejora del IQ6 debe orientarse a reducir el porcentaje de inicio tardío potencialmente evitable y a garantizar que la NE precoz se traduzca en una progresión efectiva del aporte, especialmente proteico.

### Indicador 7: Uso Adecuado de Nutrición Parenteral Total (NPT)
El IQ7 arrojó un cumplimiento del **87.73%** [IC 95%: 83.3% – 91.3%] (N = 269), situándose por debajo del estándar exigido de ≥ 90%. El indicador se operacionalizó como la existencia de justificación clínica documentada de la indicación de NPT.
*   **Caracterización:** Los pacientes con NPT no justificada presentaron perfiles clínicos basales comparables a los justificados, sin diferencias en gravedad ni comorbilidades. Las diferencias se concentraron en variables nutricionales y temporales: menor BMI, menor duración del soporte y menor estancia hospitalaria.
*   **Modelo multivariante:** El modelo logístico final (AUC = 0,761) identificó como predictores independientes de mejor documentación un mayor BMI (OR 0,89; p = 0,007), la presencia de riesgo nutricional por VGS (OR 0,28; p = 0,002) y una mayor duración de la NPT (OR 0,94; p = 0,046). El análisis de Cox, aunque concordante, incumplió el supuesto de proporcionalidad de riesgos, por lo que se considera únicamente exploratorio.
*   **Señal de alerta para la auditoría:** El problema principal reside en la falta de registro explícito en una cuarta parte de los pacientes. La prioridad es reforzar la obligatoriedad de documentar el motivo antes o en el momento de iniciar la NPT e incorporar campos estructurados en la prescripción electrónica.

### Indicador 8: Adecuación Temporal del Inicio de la Nutrición Parenteral Complementaria (NPC)
El IQ8 evidenció un **incumplimiento crítico**, con un cumplimiento del **0.85%** (solo 1 paciente favorecedor) sobre los 117 pacientes con NE que no alcanzaron el 60% de los requerimientos calóricos al día 4 [IC 95%: 0.02% – 4.68%], muy por debajo del estándar de ≥ 90%.
*   **Interpretación del fallo:** El bajo cumplimiento no refleja únicamente escaso uso de NPT, sino una discordancia entre la detección del fracaso enteral y la activación de la estrategia protocolizada de rescate nutricional. La incorporación de `TIPO_NPT == 2` como criterio de NPC real refina la interpretación: la mayoría de los inicios de NPT no respondieron específicamente al fracaso enteral detectado.
*   **Análisis complementario longitudinal:** El 72,6% de los pacientes del denominador acabó superando el umbral calórico entre los días 5 y 14, pero mayoritariamente mediante progresión de la NE o ajustes del soporte ya instaurado, sin activación formal de NPC. El bajo número de eventos favorables imposibilitó cualquier análisis inferencial.
*   **Señal de alerta para la auditoría:** El IQ8 constituye uno de los puntos más débiles de toda la auditoría. Su mejora requiere protocolizar la revisión sistemática del aporte calórico al cuarto día de NE, establecer circuitos explícitos de decisión ante fracaso enteral y mejorar la diferenciación documental entre NPT total y NPC.

### Indicador 9: Monitorización de la Nutrición Enteral
El IQ9 evidenció un **cumplimiento documental bajo** de la monitorización integral de la NE, con solo el **27.87%** de los pacientes con NE de al menos 7 días presentando registro completo de todos los bloques evaluados [IC 95%: 22.4% – 33.9%] (N = 244), muy alejado del estándar teórico del 100%.
*   **Origen del fallo:** El incumplimiento no fue uniforme. El bloque C (analítica del séptimo día, especialmente colesterol, triglicéridos y fosfatasa alcalina) y el bloque B (ionograma basal del primer día, sobre todo fósforo, magnesio y calcio) concentraron la mayor parte de las omisiones. El registro calórico diario y la vigilancia digestiva presentaron una completitud mucho mayor.
*   **Perfil de cumplidores:** Los pacientes con cumplimiento completo presentaron un perfil más complejo: mayor edad, mayor gravedad (SAPS II, NUTRIC Score), más comorbilidades y mayor estancia en UCI. El modelo logístico multivariante (AUC = 0,751) identificó la edad, la menor demora en el inicio del soporte y la mayor estancia en UCI como predictores independientes de cumplimiento documental completo.
*   **Señal de alerta para la auditoría:** El IQ9 identifica un problema de baja estandarización del registro analítico-nutricional. La mejora debe orientarse a crear perfiles analíticos predefinidos para NE prolongada, incorporar alertas automáticas para los parámetros con mayor omisión y extender la monitorización sistemática a toda la cohorte, no solo a los pacientes de mayor complejidad.

### Indicador 10: Monitorización de la Aparición de Nutritrauma en Pacientes con TMN
El IQ10 evidenció un **cumplimiento documental prácticamente nulo** de la monitorización preventiva integral del nutritrauma, con solo **5 de 376 pacientes** presentando registro completo de todos los bloques evaluados (**1.33%**; IC 95%: 0.49% – 3.26%), representando el punto de cumplimiento más bajo de toda la auditoría frente a un estándar del 100%.
*   **Cuello de botella estructural:** El principal determinante del incumplimiento fue el bloque hídrico, especialmente la diuresis de 24 horas en los cortes disponibles (omisión > 86% en todos los puntos temporales). En segundo plano, las omisiones en triglicéridos, fósforo, magnesio y perfil hepato-biliar consolidaron el incumplimiento residual.
*   **Limitación metodológica:** La base ENPIC no dispone de glucemias seriadas, impidiendo auditar uno de los componentes del indicador original. El análisis multivariante no fue realizable por el casi nulo número de eventos favorables (5 sobre 376).
*   **Señal de alerta para la auditoría:** El IQ10 constituye el indicador más revelador de la baja protocolización documental de la seguridad metabólica del TMN. Su mejora requiere transformar la vigilancia del nutritrauma en un proceso estructurado, automatizado y trazable, priorizando la integración del balance hídrico, los electrolitos críticos y el perfil hepato-lipídico en el circuito rutinario de seguimiento.

### Indicador 11: Incidencia de Nutritrauma en Pacientes con TMN
El IQ11 evidenció una **incidencia elevada de eventos compatibles con nutritrauma**, con el **68.32%** de los pacientes con TMN presentando al menos una alteración metabólico-orgánica de nueva aparición (N = 483) [IC 95%: 64.0% – 72.4%], superando ampliamente el estándar máximo aceptable de ≤ 15%.
*   **Patrón de eventos:** El dominio renal-metabólico (hiperuremia relevante) fue el componente predominante, seguido de la disfunción hepática global. La hipertrigliceridemia tuvo menor peso cuantitativo, sugiriendo que la sobrecarga lipídica no es el principal contribuyente del IQ11 en esta cohorte.
*   **Perfil de riesgo:** Los pacientes con IQ11 positivo no correspondieron a un perfil de sobrealimentación, sino a un fenotipo de paciente más difícil de nutrir: mayor gravedad basal, mayor riesgo nutricional, más comorbilidad cardiometabólica y peor tolerancia global al soporte. El modelo logístico multivariante y el análisis de Cox exploratorio identificaron de forma independiente la gravedad orgánica basal, el riesgo nutricional y el perfil clínico como predictores del evento.
*   **Impacto pronóstico:** El análisis de Kaplan-Meier mostró una reducción estadísticamente significativa de la supervivencia acumulada a 28 días en los pacientes con eventos compatibles con nutritrauma (log-rank p significativo), subrayando la relevancia clínica del indicador.
*   **Señal de alerta para la auditoría:** El IQ11 debe interpretarse como un indicador centinela de vulnerabilidad metabólico-orgánica durante el TMN. Su incumplimiento señala la necesidad de reforzar la monitorización seriada de urea, función renal, perfil hepático y balance nitrogenado, y de individualizar el soporte nutricional en pacientes de alto riesgo.

### Indicador 12: Definición de Disfunción Hepática Asociada a la Nutrición Parenteral (DHANP)
El IQ12 evidenció una **incidencia elevada de nueva alteración hepática compatible con DHANP**, con el **21.05%** de los pacientes evaluables (N depurado = 33; 4 eventos favorables, esto es, sin nueva alteración) desarrollando en el día 7 una alteración analítica de nueva aparición compatible con DHANP [IC 95%: 7.56% – 43.7%], superando el estándar máximo aceptable de ≤ 20%.
*   **Patrón predominante:** La colestasis aislada fue el patrón más frecuente (33,33%), seguida de la lesión mixta (15,15%). Este perfil orienta la atención hacia mecanismos colestásicos como principal señal de toxicidad hepatobiliar durante la NP prolongada.
*   **Prudencia interpretativa:** El IQ12 identifica alteraciones analíticas compatibles con DHANP, pero no permite demostrar causalidad directa, ya que en el paciente crítico confluyen sepsis, fallo multiorgánico, fármacos hepatotóxicos y colestasis del crítico. El análisis multivariante no fue adecuado por el reducido tamaño muestral.
*   **Señal de alerta para la auditoría:** El IQ12 muestra una señal de seguridad desfavorable que justifica reforzar la vigilancia hepatobiliar sistemática durante la NP prolongada, revisar el aporte calórico y lipídico, y mantener nutrición enteral mínima siempre que sea clínicamente posible.

### Indicador 16: Profilaxis de la Úlcera por Estrés en Pacientes Críticos
El IQ16 evidenció un **cumplimiento formal del 100%** [IC 95%: 99.3% – 100.0%] en la cobertura farmacológica antiulcerosa de los pacientes con criterios objetivos de riesgo de hemorragia gastrointestinal por estrés (N con criterios = 450), alcanzando el estándar exigido del 100%.
*   **Interpretación contextual:** El cumplimiento del 100% no refleja necesariamente una indicación individualizada, sino una política declarada de prescripción universal de la unidad. El análisis complementario identificó 75 pacientes (14,29% del total) sin criterios objetivos documentados, constituyendo un subgrupo de potencial sobremedicación.
*   **Limitaciones metodológicas:** El numerador se asumió como completo por protocolo al no disponer de una variable individual de administración farmacológica. La ausencia de registro de un factor de riesgo se interpretó como ausencia del criterio, lo que podría infraestimar el riesgo real. Estas limitaciones no permiten demostrar sobremedicación de forma definitiva, pero sí justifican revisar la indicación diaria.
*   **Señal de alerta para la auditoría:** La señal principal del IQ16 no es la infraprescripción en pacientes de riesgo, sino la posible inercia terapéutica en pacientes sin factores persistentes. La mejora debe orientarse a sustituir progresivamente la cobertura universal por una estrategia basada en riesgo clínico dinámico, documentado e individualizado.

---

## ⚙️ Especificaciones Técnicas del Entorno

*   **Lenguaje:** R (versión recomendada $\ge$ 4.0).
*   **Entorno de renderizado:** R Markdown (`.Rmd`) procesado vía consola mediante `rmarkdown::render()` o RStudio.
*   **Control de Versiones:** Git / GitHub (se aplica exclusión explícita de archivos primarios `.sav` y metadatos del SO mediante `.gitignore` para garantizar la protección de datos).
