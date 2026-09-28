# Diccionario de variables

Proyecto - tema 27: Perpetuidades y valuación de acciones con dividendo estable en la BVL
Código de matrícula: 2024200514G
Fecha de corte de los datos: 2025-12-31

Este diccionario cubre las seis tablas generadas por el pipeline:
`datos_procesados`, `beta_emisores` (ambas de 03_limpieza_datos.py),
y `tabla_valuacion`, `tabla_descriptiva_general`, `tabla_resumen_emisores`,
`eventos_dividendo_atipico` (las cuatro de 04_analisis.py).

**Endpoints declarados de Yahoo Finance (usados por yfinance):**
- Precios OHLCV, dividendos y splits: `https://query1.finance.yahoo.com/v8/finance/chart/<ticker>`
- Acciones corporativas adicionales: `https://query1.finance.yahoo.com/v10/finance/quoteSummary/<ticker>`

---

## 1. datos_procesados_2024200514G.csv

Panel diario, un registro por emisor y fecha de cotización.

| Variable | Definición | Unidad | Frecuencia | Fuente exacta | URL / endpoint de origen |
|---|---|---|---|---|---|
| Categoria | Clasificación del ticker: "Emisor BVL" o "Auxiliar CAPM" | Texto (identificador) | — | Asignado en 01_extraccion_api.py | N/A (asignado en el script, no proviene de una URL externa) |
| Emisor | Nombre completo de la empresa | Texto (identificador) | — | Asignado en 01_extraccion_api.py | N/A (asignado en el script) |
| Nemonico_BVL | Código del emisor en la Bolsa de Valores de Lima | Texto (identificador) | — | Bolsa de Valores de Lima (BVL) | https://www.bvl.com.pe |
| Ticker | Símbolo exacto usado en yfinance para consultar Yahoo Finance | Texto (identificador) | — | Yahoo Finance, vía librería yfinance | https://query1.finance.yahoo.com/v8/finance/chart/\<ticker\> |
| Date | Fecha de la rueda de bolsa | Fecha (AAAA-MM-DD) | Diaria | Yahoo Finance (Ticker.history()) | https://query1.finance.yahoo.com/v8/finance/chart/\<ticker\> |
| Open | Precio de apertura | Soles o dólares, según el emisor | Diaria | Yahoo Finance (Ticker.history()) | https://query1.finance.yahoo.com/v8/finance/chart/\<ticker\> |
| High | Precio máximo del día | Soles o dólares | Diaria | Yahoo Finance (Ticker.history()) | https://query1.finance.yahoo.com/v8/finance/chart/\<ticker\> |
| Low | Precio mínimo del día | Soles o dólares | Diaria | Yahoo Finance (Ticker.history()) | https://query1.finance.yahoo.com/v8/finance/chart/\<ticker\> |
| Close | Precio de cierre (usado como "precio de mercado" en el modelo) | Soles o dólares | Diaria | Yahoo Finance (Ticker.history()) | https://query1.finance.yahoo.com/v8/finance/chart/\<ticker\> |
| Adj Close | Precio de cierre ajustado por dividendos y splits. No se usa en los cálculos del modelo; se conserva como control de calidad | Soles o dólares | Diaria | Yahoo Finance (Ticker.history()) | https://query1.finance.yahoo.com/v8/finance/chart/\<ticker\> |
| Volume | Volumen negociado ese día | Número de acciones | Diaria | Yahoo Finance (Ticker.history()) | https://query1.finance.yahoo.com/v8/finance/chart/\<ticker\> |
| Dividendo_efectivo | Dividendo por acción pagado ese día, ya convertido a número (el crudo lo entrega como texto para los tickers .LM, ej. "0.377 PEN") | Soles o dólares por acción | Diaria (0 en días sin pago) | Yahoo Finance (Ticker.history(actions=True)) | https://query1.finance.yahoo.com/v10/finance/quoteSummary/\<ticker\> |
| Retorno_diario | Retorno diario del emisor: (Close_t / Close_t-1) - 1 | Proporción (decimal) | Diaria | Calculado en 03_limpieza_datos.py a partir de Close | N/A (variable calculada, no extraída directamente) |
| Retorno_EPU | Retorno diario del índice EPU (iShares MSCI Peru ETF), proxy de mercado para el CAPM | Proporción (decimal) | Diaria | Calculado en 03_limpieza_datos.py a partir del Close de EPU | https://query1.finance.yahoo.com/v8/finance/chart/EPU |
| Rf | Tasa libre de riesgo diaria: Close de ^TNX dividido entre 100 | Proporción (decimal) | Diaria | Yahoo Finance (ticker ^TNX), transformado en 03_limpieza_datos.py | https://query1.finance.yahoo.com/v8/finance/chart/%5ETNX |

**Nota sobre moneda:** Yahoo Finance no informa la moneda de cotización para los tickers `.LM` (campo vacío). Se asume Sol peruano (PEN) para los 8 emisores que cotizan solo en la BVL, y Dólar estadounidense (USD) para SCCO y BAP (que cotizan como ADR en NYSE). Ver limitación de descalce de moneda en README.md.

---

## 2. beta_emisores_2024200514G.csv

Resumen de la regresión de beta, un registro por emisor (no diario). Todas las variables de esta tabla son **calculadas**, no extraídas de ninguna URL.

| Variable | Definición | Unidad | Frecuencia | Fuente exacta | URL / endpoint de origen |
|---|---|---|---|---|---|
| Ticker | Símbolo del emisor en yfinance | Texto (identificador) | — | — | https://query1.finance.yahoo.com/v8/finance/chart/\<ticker\> |
| Nemonico_BVL | Código del emisor en la BVL | Texto (identificador) | — | — | https://www.bvl.com.pe |
| Emisor | Nombre completo de la empresa | Texto (identificador) | — | — | N/A |
| beta | Pendiente de la regresión Retorno_diario ~ Retorno_EPU | Coeficiente (sin unidad) | Todo el periodo 2018-2025 (constante) | Calculado en 03_limpieza_datos.py (scipy.stats.linregress) | N/A (variable calculada) |
| alpha | Intercepto de la misma regresión | Proporción (decimal) | Todo el periodo (constante) | Calculado en 03_limpieza_datos.py | N/A (variable calculada) |
| r2 | Coeficiente de determinación de la regresión | Proporción (0 a 1) | Todo el periodo (constante) | Calculado en 03_limpieza_datos.py | N/A (variable calculada) |
| p_valor | Significancia estadística del beta (H0: beta = 0) | Probabilidad (0 a 1) | Todo el periodo (constante) | Calculado en 03_limpieza_datos.py | N/A (variable calculada) |
| n_obs_beta | Número de observaciones diarias usadas en la regresión | Conteo | Todo el periodo (constante) | Calculado en 03_limpieza_datos.py | N/A (variable calculada) |
| nota | Advertencia si la regresión no cumplió el mínimo de observaciones | Texto | — | Calculado en 03_limpieza_datos.py | N/A |

---

## 3. tabla_valuacion_2024200514G.csv

Resultado central del artículo: un registro por emisor y año (10 emisores x 8 años = 80 filas). Todas las variables numéricas de esta tabla son **calculadas** a partir de las tablas 1 y 2; no provienen de una URL propia.

| Variable | Definición | Unidad | Frecuencia | Fuente / cálculo | URL / endpoint de origen |
|---|---|---|---|---|---|
| Ticker, Nemonico_BVL, Emisor | Identificadores del emisor | Texto | — | Igual que en las tablas anteriores | Ver tabla 1 |
| Anio | Año calendario del registro (2018 a 2025) | Año | Anual | Derivado de Date en 04_analisis.py | N/A |
| Dividendo_anual | Suma de Dividendo_efectivo pagado por el emisor en ese año | Soles o dólares por acción | Anual | Calculado en 04_analisis.py | N/A (agregado de tabla 1) |
| Sin_dividendo_ese_anio | True si el emisor no pagó dividendo ese año | Booleano | Anual | Calculado en 04_analisis.py | N/A |
| beta | Beta del emisor (constante en el tiempo) | Coeficiente | Todo el periodo (constante) | Tomado de beta_emisores_2024200514G.csv | Ver tabla 2 |
| Rf_promedio_anual | Promedio de Rf de ese emisor en ese año | Proporción (decimal) | Anual | Calculado en 04_analisis.py | N/A (agregado de tabla 1) |
| Ke | Tasa de descuento CAPM: Rf_promedio_anual + beta x ERP_Peru | Proporción (decimal) | Anual | Calculado en 04_analisis.py | Ver parámetro ERP_Peru, sección 7 |
| Ke_valida | True si Ke > 0 | Booleano | Anual | Calculado en 04_analisis.py | N/A |
| Valor_teorico | Dividendo_anual / Ke (perpetuidad sin crecimiento) | Soles o dólares por acción | Anual | Calculado en 04_analisis.py | N/A |
| Precio_cierre_fin_anio | Último Close disponible de ese emisor en ese año | Soles o dólares | Anual | Tomado del panel diario | Ver tabla 1 |
| Brecha | Precio_cierre_fin_anio - Valor_teorico | Soles o dólares | Anual | Calculado en 04_analisis.py | N/A |
| Brecha_pct | Brecha / Precio_cierre_fin_anio | Proporción (decimal) | Anual | Calculado en 04_analisis.py | N/A |
| Evento_dividendo_atipico | True si corresponde a uno de los 3 eventos documentados | Booleano | Anual | Declarado con fuente documental en 04_analisis.py | Ver tabla 6 |
| Nota_evento_atipico | Descripción del evento, si aplica | Texto | Anual | Declarado en 04_analisis.py | Ver tabla 6 |
| r2, p_valor | Heredados de beta_emisores | Ver tabla 2 | Todo el periodo (constante) | — | Ver tabla 2 |
| Obs_anio | Número de días de cotización de ese emisor en ese año | Conteo | Anual | Calculado en 04_analisis.py | N/A |

---

## 4. tabla_descriptiva_general_2024200514G.csv

Estadísticas descriptivas generales, un registro por emisor.

| Variable | Definición | Unidad | Frecuencia | Fuente / cálculo | URL / endpoint de origen |
|---|---|---|---|---|---|
| Ticker, Nemonico_BVL, Emisor | Identificadores | Texto | — | — | Ver tabla 1 |
| Sector_economico | Sector económico del emisor (clasificación pública) | Texto | — | Declarado en 04_analisis.py | N/A (clasificación propia, no de Yahoo) |
| N_observaciones | Total de días de cotización 2018-2025 | Conteo | Todo el periodo | Calculado en 04_analisis.py | N/A |
| Fecha_inicio, Fecha_fin | Primer y último día con dato en el panel | Fecha | Todo el periodo | Calculado en 04_analisis.py | N/A |
| Precio_promedio, Precio_minimo, Precio_maximo | Estadísticos de Close en todo el periodo | Soles o dólares | Todo el periodo | Calculado en 04_analisis.py | N/A |
| Dividend_yield_promedio_anual | Promedio de (dividendo anual / precio de fin de año) | Proporción (decimal) | Promedio de 8 años | Calculado en 04_analisis.py | N/A |

---

## 5. tabla_resumen_emisores_2024200514G.csv

Un registro por emisor, promedios para la sección de Resultados.

| Variable | Definición | Unidad | Frecuencia | Fuente / cálculo | URL / endpoint de origen |
|---|---|---|---|---|---|
| Ticker, Nemonico_BVL, Emisor, Beta | Identificadores y beta del emisor | Texto / coeficiente | — / constante | Ver tablas 1 y 2 | Ver tablas 1 y 2 |
| Dividendo_anual_promedio | Promedio del dividendo anual pagado | Soles o dólares por acción | Promedio de 8 años | Calculado en 04_analisis.py | N/A |
| Ke_promedio | Promedio del Ke anual del emisor | Proporción (decimal) | Promedio de 8 años | Calculado en 04_analisis.py | N/A |
| Precio_cierre_promedio | Promedio del precio de fin de año | Soles o dólares | Promedio de 8 años | Calculado en 04_analisis.py | N/A |
| Brecha_pct_promedio_completa | Promedio de Brecha_pct, con los 79 casos | Proporción (decimal) | Promedio de 8 años | Calculado en 04_analisis.py | N/A |
| Brecha_pct_promedio_robustez | Promedio de Brecha_pct, sin eventos atípicos | Proporción (decimal) | Promedio de años válidos | Calculado en 04_analisis.py | N/A |
| N_anios_con_evento_atipico | Cuántos años están marcados como evento atípico | Conteo | Todo el periodo | Calculado en 04_analisis.py | Ver tabla 6 |

---

## 6. eventos_dividendo_atipico_2024200514G.csv

Los 3 casos de dividendo extraordinario documentados con fuente primaria.

| Variable | Definición | Unidad | Frecuencia | Fuente / cálculo | URL / endpoint de origen |
|---|---|---|---|---|---|
| Ticker, Anio | Identifica el emisor-año con evento documentado | Texto / Año | Puntual (2021) | Declarado en 04_analisis.py | N/A |
| Motivo | Descripción del evento | Texto | — | Declarado en 04_analisis.py, con cita | Ver columna Fuente de esta misma tabla |
| Fuente | Referencia bibliográfica completa (APA), con URL | Texto | — | Ver sección de Fuentes y endpoints del README | Alicorp: https://www.alicorp.com.pe/media/conference_calls/Alicorp_Earnings_Report_2Q21_ES_VF.pdf · Cementos Pacasmayo: https://www.sec.gov/Archives/edgar/data/1221029/000121390021017459/ea138327ex99-1_cementospacas.htm · Backus: https://gestion.pe/economia/empresas/backus-repartira-s-2935-millones-en-utilidades-a-sus-accionistas-mayoritarios-quienes-son-noticia/ |

---

## 7. Parámetros del modelo (no son variables extraídas, son citados o declarados)

| Parámetro | Valor | Fuente exacta | URL / endpoint de origen |
|---|---|---|---|
| ERP_Peru | 6.30% | Damodaran, A. (2026). Country Default Spreads and Risk Premiums | https://pages.stern.nyu.edu/~adamodar/New_Home_Page/datafile/ctryprem.html (consultado 24-09-2026) |
| FECHA_INICIO / FECHA_CORTE | 2018-01-01 / 2025-12-31 | Ventana de estudio, declarada como constante en los 3 scripts | N/A (parámetro propio, no de una fuente externa) |
