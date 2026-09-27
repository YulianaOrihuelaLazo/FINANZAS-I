

#Nombres y apellidos: Yuliana Orihuela Lazo

#Código de matrícula:  2024200514G 

#Tema del temario :Perpetuidades y valuación de acciones con dividendo estable en la BVL


## Fuentes y endpoints

### Vía 1 — API (ejecutada)

**Fuente:** Yahoo Finance, consumida mediante la librería `yfinance` (Python).

**Endpoint declarado:** `yfinance` no expone una URL editable por el usuario;
internamente consulta la API pública de gráficos de Yahoo Finance
(`https://query1.finance.yahoo.com/v8/finance/chart/<ticker>` para precios OHLCV,
y el endpoint de acciones corporativas para dividendos), a través del método
`yfinance.Ticker(<ticker>).history()`.

**Los 10 emisores de la BVL consultados:** SCCO, BAP, CPACASC1.LM, FERREYC1.LM,
UNACEMC1.LM, ALICORC1.LM, BACKUSI1.LM, CREDITC1.LM, LUSURC1.LM, BBVAC1.LM
(ver diccionario_variables.md para el detalle completo por emisor).

**Series auxiliares (modelo CAPM):** `EPU` (proxy de mercado) y `^TNX` (tasa
libre de riesgo).

### Parámetros citados (no extraídos, no calculados)

| Parámetro | Valor | Fuente |
|---|---|---|
| ERP Perú | 6.30% | Damodaran, A. (2026). Country Default Spreads and Risk Premiums. NYU Stern. https://pages.stern.nyu.edu/~adamodar/New_Home_Page/datafile/ctryprem.html (consultado 24-09-2026) |

No se incluye vía 2 (scraping) en esta entrega: en Unidad I la segunda vía es
opcional (consigna, numeral 2.4.1).

---

## Fecha de corte

**2025-12-31** — declarada como constante (`FECHA_CORTE`) en los 3 scripts,
no calculada dinámicamente.

Fecha de extracción real (corrida de 01_extraccion_api.py): **2026-09-22**

---

## Orden de ejecución

1. `01_extraccion_api.py` → genera `datos_crudos_2024200514G.csv`
2. `03_limpieza_datos.py` → genera `datos_procesados_2024200514G.csv` y
   `beta_emisores_2024200514G.csv`
3. `04_analisis.py` → genera 13 archivos en `/salidas`

---

## Versión del lenguaje y de las librerías

**Python:** 3.13.15

| Librería | Versión |
|---|---|
| yfinance | 0.2.66 |
| pandas | 2.2.3 |
| numpy | 2.1.3 |
| scipy | 1.16.3 |
| matplotlib | 3.10.0 |

---

## Hash SHA-256 del archivo procesado

**Archivo:** `datos_procesados_2024200514G.csv`
**SHA-256:**
44c69c77ea8760f7080b73de274d2bc72154426533235171f1f1bd165dd000e7

## Enlace del repositorio

https://github.com/YulianaOrihuelaLazo/FINANZAS-I
