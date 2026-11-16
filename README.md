# Web Scraping Aplicado: Consumo en Spotify frente a Curiosidad en Wikipedia

Proyecto desarrollado para la asignatura **Datos No Estructurados** por **César Francisco Veintimilla** y **Hugo Carrasco Romaguera**.

## Objetivo del estudio

Analizar si un mayor volumen de reproducciones en plataformas de *streaming* (consumo pasivo) se traduce en un mayor interés orgánico y búsqueda de información en Internet (curiosidad activa), contrastando dos cohortes: los **artistas más escuchados a nivel global** frente a los **artistas con mayor tracción en España**.

## Arquitectura y metodología

El flujo de captura y análisis está íntegramente programado en **R** y documentado en **Quarto (`.qmd`)**:

1. **Scraping Estático (`rvest`, `stringr`, `dplyr`):**
   * Extracción multinivel en [Kworb.net](https://kworb.net/) (respetando su `robots.txt` permisivo y aplicando pausas con `Sys.sleep()`).
   * Captura del Top 8 histórico global y del Top 8 de artistas únicos en España (`es_daily_totals.html` tras deduplicación con `distinct()`).
   * Normalización de URLs relativas y acceso automatizado a cada perfil individual para extraer los *streams* totales y calcular el **Índice de Dependencia del Hit**.

2. **Scraping Dinámico (`RSelenium`):**
   * Automatización de navegador Firefox sobre [Wikimedia Pageviews Analysis](https://pageviews.wmcloud.org/) (SPA renderizada por JavaScript con datos CC0).
   * Ejecución secuencial de interacciones: selección de proyecto (`en.wikipedia.org` y `es.wikipedia.org`), granularidad mensual, rango histórico completo (*Todo el tiempo*, julio 2015 – octubre 2026), filtrado de tráfico humano (*Usuario*) e inserción por lotes con limpieza de formulario.
   * Control de calidad mediante lectura dinámica de cabeceras, filtrado de páginas de desambiguación (`Disambig`) y emparejamiento de resultados mediante parseo de URL.

3. **Análisis Exploratorio (`ggplot2`):**
   * Cruce de ambas fuentes y cálculo de métricas relativas: **visitas por millón de streams** y **porcentaje de peso hispano**.

## Principales resultados

* **Correlación positiva fuerte:** Existe una correlación de Spearman de **0,83** entre las reproducciones acumuladas en Spotify y las visitas totales en Wikipedia.
* **Brecha de interés relativo:** Los artistas globales presentan una mediana de *streams* ~5 veces superior a los del top español (91.000M frente a 19.500M), pero multiplican por **~15** su mediana de visitas en Wikipedia (64,3M frente a 4,4M).
* **Segregación geográfica y lingüística:** El umbral del **50 % de visitas procedentes de la Wikipedia española** separa de forma casi perfecta ambas cohortes, determinado por el idioma y el origen del artista más que por el mercado donde se consume su música.

## Estructura del repositorio

* `tarea2_qto.qmd`: Código fuente completo en Quarto (extracción, limpieza, cruce de datos y visualizaciones).
* `tarea2_qto.html`: Informe interactivo renderizado.
* `tarea2_qto.pdf`: Versión en documento portátil del estudio.
* `datos_pageviews.csv`: Dataset intermedio generado tras el scraping dinámico en Wikimedia.
* `datos_final.csv`: Dataset final consolidado y limpio utilizado para el análisis.

## Replicabilidad

El bloque de `RSelenium` está configurado con `eval=FALSE` dentro del `.qmd` para permitir compilar el informe sin depender de una sesión activa de Java/Firefox ni de variaciones temporales en los contadores diarios, cargando directamente los `.csv` extraídos el 11 de noviembre de 2026. Para ejecutar el proyecto desde cero en R:

```r
install.packages(c("rvest", "dplyr", "stringr", "readr", "RSelenium", "ggplot2", "knitr"))
