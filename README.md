# 📊 Proyecto ENPIC: Evaluación de Indicadores de Calidad

> *Este repositorio contiene el código en R utilizado para la limpieza, manipulación y evaluación de una serie de Indicadores de Calidad clínicos y nutricionales a partir de la "Base de datos ENPIC".*

---

## 🛠️ Exploración y Tratamiento Inicial de Datos

En la primera fase del proyecto, se estableció el entorno de trabajo y se generó un diagnóstico algorítmico y completo de la estructura de la base de datos:

*   **Importación y metadatos:** Se cargó la "Base de datos ENPIC" desde su formato original (`.sav`) utilizando el paquete `haven`. Para evitar la pérdida de contexto, se extrajeron las etiquetas nativas de SPSS (ej. `1 = Hombre; 2 = Mujer`) para construir un diccionario automatizado antes de cualquier coerción de datos.
*   **Auditoría de Valores Nulos:** Se iteró sobre la base de datos para contabilizar el número exacto de valores perdidos (`NA`) por columna, preservando el orden original para facilitar la inspección.
*   **Transformación Inteligente a Factores:** Se diseñó un bucle para convertir automáticamente a formato `factor` las variables categóricas (identificadas por tener $\le 5$ valores únicos), incluyendo excepciones clínicas específicas que requerían ser tratadas como categorías (`DIAGNOSP`, `SUPRE_NE`, `INDICA_NTP`, `SUPRE_NPT`).
*   **Análisis de Distribución:** Se integró un test de Kolmogorov-Smirnov (`ks.test`) para evaluar dinámicamente la normalidad de las variables numéricas continuas, clasificándolas en distribuciones "Normales" o "Asimétricas".
*   **Tabla Resumen Interactiva:** Todos los hallazgos (Nombre de la variable, Tipo de dato, Etiquetas, Valores Únicos, Nulos y Tipo de Distribución) se consolidaron y presentaron mediante una tabla interactiva utilizando la librería `DT`.

