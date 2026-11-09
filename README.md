# Registro público de pronósticos sísmicos para Chile

Este repositorio guarda, mes a mes, los pronósticos de sismos M ≥ 4,5 para Chile
**antes** de que ocurran, con una huella que permite comprobar que no se modificaron
después. Solo contiene los pronósticos publicados, sus huellas y sus sellos de tiempo.

> No es un sistema de alerta. Produce probabilidades. La información oficial en Chile es
> la del CSN, SENAPRED y SHOA.

## Qué es cada archivo

| Archivo | Contenido |
|---|---|
| `ledger.jsonl` | Registro de solo-agregar: una línea por pronóstico con el mes, la hora de emisión, si fue tardío, la ruta del archivo, su huella SHA-256 y el sello. |
| `AAAA-MM/forecast.json` | El pronóstico del mes: tasa esperada de sismos por celda de 0,5° y por modelo, y las huellas de sus insumos. No cambia nunca. |
| `AAAA-MM/forecast.json.ots` | Sello de tiempo OpenTimestamps de `forecast.json`: prueba que el archivo existía antes de que el sello se anclara a Bitcoin. |
| `models/*.json` | No se publican: los archivos de parámetros de los modelos quedan en un repositorio privado. Su huella SHA-256 figura en `index.json` y en `forecast.json` (`inputs.model_files_sha256`), lo que permite comprobar que no cambiaron. |
| `evaluations.jsonl` | Evaluaciones oficiales (≥ 90 días tras cerrar cada mes), de solo-agregar, con la huella del catálogo usado. |
| `AAAA-MM/evaluation_catalog.csv` | Copia del subconjunto del catálogo USGS con que se evaluó el mes. |
| `index.json` | Índice de todos los archivos publicados con su huella SHA-256. |

## Cómo verificar una huella

1. Calcula la huella del archivo:
   `sha256sum 2026-12/forecast.json` (Linux/Mac) o
   `Get-FileHash 2026-12/forecast.json -Algorithm SHA256` (PowerShell).
2. Compárala con el campo `sha256` de la línea de ese mes en `ledger.jsonl` y con la de `index.json`.
   Deben ser idénticas; si no, el archivo fue alterado.
3. El historial de commits de este repositorio muestra cuándo se publicó cada archivo.

## Cómo verificar un sello

1. Instala el cliente: `pip install opentimestamps-client`.
2. Con `forecast.json` y `forecast.json.ots` en la misma carpeta:
   `ots verify 2026-12/forecast.json.ots`.
3. Un sello recién emitido figura como «pendiente»; la prueba de Bitcoin se completa en
   unas horas y entonces el archivo `.ots` se actualiza (la huella anclada no cambia).
   La verificación completa requiere acceso a un nodo de Bitcoin o a un explorador de bloques.
