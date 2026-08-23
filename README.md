# 📊 Proyecto ENPIC: Evaluación de Indicadores de Calidad

> *Este repositorio contiene el código en R utilizado para la limpieza, manipulación y evaluación de una serie de Indicadores de Calidad clínicos y nutricionales a partir de la "Base de datos ENPIC".*

---

## 📅 Registro de Avances

### Día 1: Importación y Diagnóstico Inicial de Datos

El objetivo de esta primera sesión fue **establecer el entorno de trabajo** y realizar un reconocimiento de la estructura y calidad de la información disponible.

*   **Importación segura:** Se logró cargar exitosamente la "Base de datos ENPIC" desde su formato original de SPSS (`.sav`) utilizando el paquete `haven`, conservando sus metadatos.
*   **Reconocimiento de estructura:** Mediante las herramientas `head()` y `dplyr::glimpse()`, se verificaron los tipos de datos asignados a cada columna (fechas, textos, números).
*   **Auditoría de completitud:** Se construyó un bloque de código combinando `dplyr` y `tidyr` para contabilizar y ordenar los valores nulos (`NA`) de todas las columnas, identificando aquellas con mayor falta de información.
*   **Extracción de diccionarios:** Se utilizó `purrr::map()` para extraer sistemáticamente las etiquetas de SPSS, logrando traducir y comprender los códigos numéricos de las variables categóricas.

---

## 🚀 Próximos Pasos

- [ ] Seleccionar el primer indicador de calidad clínico a evaluar.
- [ ] Realizar la limpieza de fechas y cálculo de tiempos de estancia.
