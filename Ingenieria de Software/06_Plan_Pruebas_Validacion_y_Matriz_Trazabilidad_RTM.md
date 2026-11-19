# 06. PLAN DE PRUEBAS TDD, VALIDACIÓN METROLÓGICA Y MATRIZ DE TRAZABILIDAD (STP / RTM)
**Estándar de Referencia:** IEEE Std 829 / ISO/IEC/IEEE 29119 + Ciclo TDD (*Test-Driven Development*) y Kanban  
**Código Documental:** `YUYAY-STP-RTM-2026-V2.0`  
**Equipo:** Los JackersMainsters (ESPOCH) · **Estación Redactora:** Estación Omar (`OMARFIGUEROA`)

---

## 1. Estrategia de Pruebas TDD y Aseguramiento de Calidad (QA)

Bajo la metodología **TDD + Kanban**, las pruebas no son una fase final posterior a la codificación, sino el **motor de diseño y desarrollo (Rojo $\rightarrow$ Verde $\rightarrow$ Refactorización)**. El proceso de verificación y validación de Yuyay se estructura en **cuatro niveles de prueba** que se ejecutan en cada tarjeta Kanban antes de fusionar en `main` y como requisito de salida hacia el *Code Freeze* del 25 de noviembre de 2026:

| Nivel | Tipo de Prueba | Herramienta / Entorno | Alcance y Puerta de Calidad (Gate) |
|---|---|---|---|
| **Nivel 1** | **Auditoría Estática y Guardias del Dominio** | `tsc -b`, `Biome`, `scripts/check-language.ts`, `scripts/check-package-manager.ts` | Garantiza 0 errores de tipos en TypeScript 7 estricto, formato consistente, uso exclusivo de Bun y **0 ocurrencias de vocabulario clínico prohibido** en `src/` y `index.html`. |
| **Nivel 2** | **Ciclo TDD de Pruebas Unitarias y Metrológicas** | `Vitest 5` + Fixtures sintéticas en `tests/fixtures/*.json` | Cada algoritmo en `src/metrics/*` y `report-templates.ts` se desarrolla escribiendo primero su test en Rojo, verificando pureza determinista, retorno `null` ante datos insuficientes y tolerancias metrológicas ($\le 10\,\%$ en pinza, $\le 0.5\text{ Hz}$ en FFT, $100\,\%$ en Cóndor y Semáforo). |
| **Nivel 3** | **Ciclo TDD de Pruebas de Integración (Flujo, Worker y Dexie)** | `Vitest` + `jsdom` + `fake-indexeddb` + `Storybook 10 build` | Verifica la máquina de estados `SessionFlow`, el despacho en `runMetricInWorker`, la inserción/lectura/borrado en Dexie v2 (`sessions`, `deviceChecks`, `measurements`), la caché SHA-256 de modelos y las pantallas de juegos e informe. |
| **Nivel 4** | **Pruebas de Sistema en Hardware Real y Offline** | Estaciones **Omar (`Core i5-7200U`)**, **Andrés (`MacBook Air M1`)** y **Alexander (`Ryzen 5 9600X`)** | Certifica $\ge 20\text{ FPS}$ con cámara real, detección de condiciones adversas (`permission-denied`, `no-signal`, mano/rostro fuera de cuadro), duración de sesión $\le 6\text{ min}$ y operación 100 % sin red (Modo Avión). |

---

## 2. Catálogo de Casos de Prueba Oficiales (`CP-001` a `CP-020`)

| ID Caso | Nombre del Caso de Prueba | Requisito / Meta Validada | Entrada / Fixture de Prueba | Resultado Esperado (Criterio de Éxito) | Tipo / Archivo de Prueba |
|---|---|---|---|---|---|
| **CP-001** | Conteo de ciclos y amplitud en *"La pinza"* con secuencia de referencia | `RF-015`, `RNF-005` (Error $\le 10\,\%$) | `tests/fixtures/pinch-cycles.json` (10 s a 30 FPS con 6 ciclos anotados) | `cycles === 6` (error $0\,\% \le 10\,\%$), `amplitude` en $[0.4, 0.7]$ y `rhythmVariation < 0.15`. | Automatizado (`tests/metrics/pinch.test.ts`) |
| **CP-002** | Rechazo de muestra insuficiente en *"La pinza"* (`null` $\neq 0$) | `RC-04`, `RF-015`, `RF-020` | Arreglo vacío `[]` o secuencia con $< 2$ ciclos completos | `pinchMetrics` retorna `null` (jamás `0`); la UI traduce `null` a `"No evaluable"`. | Automatizado (`tests/metrics/pinch.test.ts`) |
| **CP-003** | Dispersión postural (`rms`) en *"Sostén la bandeja"* | `RF-016`, `RC-05` | 60 frames alrededor de $(0.5, 0.5)$ con desviación conocida $0.01$ | `rms` calculado en $0.01 \pm 0.001$ y `duration` exacta; con 1 solo frame retorna `null`. | Automatizado (`tests/metrics/stability.test.ts`) |
| **CP-004** | Estimación de frecuencia oscilatoria en señales sintéticas de 3, 5 y 8 Hz | `RF-016`, `RNF-006` (Error $\le 0.5\text{ Hz}$) | Series temporales sinusoidales sintéticas de $3.0\text{ Hz}$, $5.0\text{ Hz}$ y $8.0\text{ Hz}$ con marcas de tiempo | La frecuencia dominante estimada difiere en $\le 0.5\text{ Hz}$ del valor real en las tres frecuencias de referencia. | Automatizado (`tests/metrics/stability.test.ts`) |
| **CP-005** | Simetría geométrica facial y duración/intensidad de vocal en *"Sonríe y habla"* | `RF-014`, `RF-017`, `RC-03` | Landmarks sintéticos simétricos/asimétricos + buffer PCM de prueba | Calcula `symmetryRatio` con tolerancia numérica documentada y duración vocal en `s` sin persistir audio ni video. | Automatizado (`tests/metrics/facial.test.ts`) |
| **CP-006** | Exactitud al 100 % en guion automatizado de *"El vuelo del cóndor"* | `RF-018`, `RNF-007` (Coincidencia $100\,\%$) | `tests/fixtures/path-trace.json`: polilínea $(0,0)\rightarrow(3,4)\rightarrow(3,10)$, `hits: 2`, `total: 3` | `duration === 2000`, `length === 11`, `hits === 2`, `total === 3` ($100\,\%$ de coincidencia con guion). | Automatizado (`tests/metrics/path.test.ts`) |
| **CP-007** | Exactitud al 100 % en guion automatizado de *"Semáforo"* (Go/No-Go) | `RF-019`, `RNF-007` (Coincidencia $100\,\%$) | `tests/fixtures/reaction-script.json`: 8 aciertos Go, 1 omisión Go y 1 falsa alarma No-Go | `medianLatency === 440`, `omissions === 1`, `falseAlarms === 1` ($100\,\%$ de coincidencia). | Automatizado (`tests/metrics/reaction.test.ts`) |
| **CP-008** | Ejecución asíncrona en Web Worker (`runMetricInWorker`) | `RF-021`, `RNF-003` | Peticiones `pinch`, `stability`, `reaction` y `path` despachadas al Worker | El puente devuelve el mismo resultado determinista que la función pura sin bloquear el hilo principal. | Automatizado (`src/workers/run-in-worker.test.ts`) |
| **CP-009** | Máquina de estados de sesión y bloqueo sin consentimiento o sin dispositivos | `RF-001`, `RF-003`, `RF-007`, `RF-010` | Intentos de transición inválidos en `createSessionFlow()` | No avanza de `consent` sin aceptar; no avanza de `devices` si `quality.ok === false`; pasa a `report` solo tras los 5 juegos. | Automatizado (`src/app/session-flow.test.ts`) |
| **CP-010** | Persistencia y recuperación de sesión y mediciones en Dexie v2 | `RF-002`, `RF-008`, `RF-022` | Inserción de `SessionRow`, `DeviceCheckRow` y `MeasurementRow[]`, limpieza por juego y recarga | `loadSession`, `latestDeviceCheck`, `listMeasurements` y `clearMeasurementsForGame` recuperan y actualizan los datos íntegros. | Automatizado (`storage.test.ts`, `measurements.test.ts`) |
| **CP-011** | Descarga, caché e integridad criptográfica `SHA-256` de modelos `.task` | `RF-009`, `RF-010`, `RNF-009` | Simulación de descarga normal, modelo ya cacheado y modelo con byte alterado | Hash válido reporta `status: 'ready'`; hash corrupto deja `status: 'error'` e incluye el archivo en `failed`. | Automatizado (`src/pwa/model-cache.test.ts`) |
| **CP-012** | Evaluación de reglas de calidad de captura (`permission-denied`, `no-signal`, `low-fps`) | `RF-006`, `RF-007` | Entradas con `NotAllowedError`, `micRms: 0.0001` y `fps: 12` | `NotAllowedError` y `micRms < 0.001` producen `ok: false`; `fps < 20` produce flag `low-fps`. | Automatizado (`src/features/devices/quality.test.ts`) |
| **CP-013** | Generación de informe por plantillas deterministas (capturas válidas y `"No evaluables"`) | `RF-023`, `RF-024`, `RC-01`, `RC-04` | Conjunto de `MeasurementRow[]` con estados `'ok'` y `'not-evaluable'` | Renderiza unidades físicas cuando `'ok'`, muestra `"No evaluable"` + motivo cuando `'not-evaluable'` (nunca `0`) y contiene aviso no médico. | Automatizado (`report-templates.test.ts` — Estación Omar) |
| **CP-014** | Exportación local de informe en JSON y vista de impresión sin datos crudos | `RF-025`, `RF-026`, `RNF-008` | Sesión finalizada con 5 actividades registradas en Dexie | Genera JSON válido con métricas derivadas y metadatos; verifica ausencia de blobs multimedia o coordenadas crudas. | Automatizado (`export-report.test.ts` — Estación Omar) |
| **CP-015** | Guardia automatizado de lenguaje no clínico (`bun run language`) | `RC-01`, `RNF-013` | Escaneo completo de `src/` e `index.html` con expresiones regulares de términos prohibidos | Código de salida `0` (cero menciones a diagnóstico, enfermedad, párkinson, ACV, alzhéimer, riesgo, probabilidad, semáforo clínico). | Automatizado en CI (`scripts/check-language.ts`) |
| **CP-016** | Prueba en Hardware Real — **Estación Omar (`OMARFIGUEROA` ASUS Core i5-7200U / HD 620)** | `RNF-001` ($\ge 20\text{ FPS}$), `RNF-002` ($\le 6\text{ min}$) | Ejecución completa de los 5 minijuegos con cámara y micrófono integrados | El módulo visual activo sostiene $\ge 20\text{ FPS}$, la memoria se mantiene estable y la sesión completa dura $\le 6\text{ minutos}$. | Manual documentado en Hardware (Estación Omar) |
| **CP-017** | Prueba en Hardware Real — **Estación Andrés (`MacBook Air M1`)** | `RNF-001` ($\ge 20\text{ FPS}$), Demo Principal | Ejecución completa de la batería y simulación de caso de error en vivo | Sostiene $\ge 30\text{ FPS}$ en `HandLandmarker`, `FaceLandmarker` y `PoseLandmarker`; transición fluida al informe. | Manual documentado en Hardware (Estación Andrés) |
| **CP-018** | Prueba en Hardware Real — **Estación Alexander (`Ryzen 5 9600X`)** | `RNF-001`, `RNF-011`, `RNF-012` | Ejecución del flujo completo, navegación por teclado/pantalla en Cóndor y Semáforo | Tiempos de respuesta precisos, foco visible, contraste $\ge 4.5:1$ y botones $\ge 48\text{ px}$. | Manual documentado en Hardware (Estación Alexander) |
| **CP-019** | Prueba de Condiciones Difíciles en Vivo (Cámara tapada / Mano ausente / Mic mudo) | `RF-007`, `RF-020`, Anexo A.4 Bases | Durante la prueba se retira la mano del encuadre y se silencia el micrófono | El sistema detecta el fallo en el100 % de los casos, informa la causa, marca `"No evaluable"` y permite repetir sin caer en error fatal. | Manual / Demo en Vivo |
| **CP-020** | Prueba de Funcionamiento 100 % Local con Red Desconectada (Modo Avión) | `RC-02`, `RNF-010` | Tras completar `InstallScreen`, se apaga el Wi-Fi, se recarga la página y se juega toda la sesión | La PWA abre desde Service Worker, carga los modelos desde `yuyay-models-v1`, guarda en Dexie y exporta el JSON con **0 peticiones externas**. | Manual / Demo en Vivo |

---

## 3. Protocolo de Certificación en la Estación Omar (`OMARFIGUEROA`)

Para documentar la evidencia exigida en la rúbrica de la Gran Final (*Arquitectura y calidad técnica — 20 puntos* y *Funcionalidad y madurez del MVP — 25 puntos*), desde la **Estación Omar** se completará la siguiente ficha experimental durante la Fase 4:

```text
================================================================================
FICHA DE REGISTRO DE RENDIMIENTO Y VALIDACIÓN — ESTACIÓN OMAR
================================================================================
- Identificador de Equipo : OMARFIGUEROA (Portátil ASUS Q304UAK)
- Procesador              : Intel Core i5-7200U (2 núcleos / 4 hilos @ 2.50 GHz)
- Memoria RAM             : 12 GB DDR4
- Unidad Gráfica          : Intel HD Graphics 620 (Sin GPU dedicada)
- Almacenamiento          : SSD 480 GB
- Navegador de Prueba     : Google Chrome / Microsoft Edge (Chromium x64)
- Resolución de Cámara    : [Registrar ej. 640x480 o 1280x720 px]
- FPS Medidos (Disposit.) : [Registrar valor >= 20 FPS]
- FPS Medidos (La pinza)  : [Registrar valor >= 20 FPS]
- FPS Medidos (Bandeja)   : [Registrar valor >= 20 FPS]
- FPS Medidos (Sonríe)    : [Registrar valor >= 20 FPS]
- Duración Total Sesión   : [Registrar mm:ss <= 06:00]
- Verificación Sin Red    : [APROBADO - 0 peticiones externas en DevTools Network]
- Exportación JSON / PDF  : [APROBADO]
================================================================================
```

---

## 4. Matriz de Trazabilidad Bidireccional de Requisitos (RTM)

La siguiente matriz vincula cada **Regla Constitucional (`RC`)** y **Requisito (`RF`/`RNF`)** con su **Historia de Usuario (`HU`)**, **Caso de Uso (`CU`)**, **Pantalla de Mockup (`M1`–`M3`)**, **Módulo de Código Fuente (`src/`)** y **Caso de Prueba (`CP`)**:

| Requisito (`RF` / `RNF` / `RC`) | Historia de Usuario (`HU`) | Caso de Uso (`CU`) | Mockup Asociado (`PROPUESTA/mockups/`) | Módulo en Repositorio (`Desarrollo/yuyay/src/`) | Caso de Prueba (`CP`) |
|---|---|---|---|---|---|
| `RC-01`, `RF-001`, `RF-003` | `HU-001` | `CU-001` | `M1-P1`, `M1-P2` | `src/features/consent/ConsentScreen.tsx`, `src/app/copy.ts` | `CP-009`, `CP-015` |
| `RF-002`, `RF-004` | `HU-002` | `CU-001` | Flujo Transversal | `src/app/App.tsx`, `src/app/session-flow.ts`, `src/features/storage/sessions.ts` | `CP-009`, `CP-010` |
| `RC-07`, `RF-005`..`RF-008`, `RNF-001` | `HU-003`, `HU-020` | `CU-002` | `M1-P3` | `src/features/devices/{DeviceCheckScreen.tsx,quality.ts}`, `src/vision/capture-loop.ts`, `src/audio/mic-level.ts` | `CP-012`, `CP-016`..`CP-019` |
| `RC-02`, `RF-009`..`RF-011`, `RNF-009`, `RNF-010` | `HU-004`, `HU-019` | `CU-003`, `CU-012` | `M1-P3` | `src/pwa/{model-cache.ts,register-sw.ts}`, `src/features/install/InstallScreen.tsx` | `CP-011`, `CP-020` |
| `RF-012`, `RF-013`, `RF-015`, `RNF-005` | `HU-005`, `HU-006` | `CU-004`, `CU-009` | `M1-P4`, `M2-P16`, `M2-P17` | `src/vision/hand-landmarker.ts`, `src/metrics/pinch.ts`, `src/features/games/pinza/PinzaScreen.tsx` | `CP-001`, `CP-002`, `CP-019` |
| `RF-012`, `RF-016`, `RNF-006` | `HU-007`, `HU-008` | `CU-005`, `CU-009` | `M1-P5`, `M2-P18`, `M2-P19` | `src/vision/pose-landmarker.ts`, `src/metrics/stability.ts`, `src/features/games/bandeja/BandejaScreen.tsx` | `CP-003`, `CP-004`, `CP-019` |
| `RC-03`, `RF-012`, `RF-014`, `RF-017` | `HU-009`, `HU-010` | `CU-006`, `CU-009` | `M1-P6`, `M2-P20`, `M3-P22` | `src/vision/face-landmarker.ts`, `src/metrics/facial.ts`, `src/audio/vowel-capture.ts`, `src/features/games/sonrisa/SonrisaScreen.tsx` | `CP-005`, `CP-019` |
| `RF-018`, `RNF-007`, `RNF-011` | `HU-011`, `HU-012` | `CU-007` | `M1-P7`, `M3-P23`, `M3-P24`, `M3-P25` | `src/metrics/path.ts`, `src/features/games/condor/CondorScreen.tsx` | `CP-006`, `CP-018` |
| `RF-019`, `RNF-007`, `RNF-012` | `HU-013`, `HU-014` | `CU-008` | `M1-P8`, `M3-P26`, `M3-P27` | `src/metrics/reaction.ts`, `src/features/games/semaforo/SemaforoScreen.tsx` | `CP-007`, `CP-018` |
| `RC-05`, `RF-021`, `RF-022`, `RNF-003` | `HU-006`, `HU-008`, `HU-012`, `HU-014` | `CU-004`..`CU-008` | Transversal | `src/workers/{metric.worker.ts,metric-dispatch.ts,run-in-worker.ts}`, `src/features/storage/{db.ts,measurements.ts}` | `CP-008`, `CP-010` |
| `RC-01`, `RC-04`, `RF-023`..`RF-027`, `RNF-008` | `HU-015`..`HU-018` | `CU-010`, `CU-011` | `M1-P2`, `M1-P9` | `src/features/report/{ReportScreen.tsx,report-templates.ts,export-report.ts}` (**Estación Omar**) | `CP-013`, `CP-014`, `CP-015` |
