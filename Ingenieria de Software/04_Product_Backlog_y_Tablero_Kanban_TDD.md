# 04. PRODUCT BACKLOG Y TABLERO KANBAN CON TDD — PROYECTO YUYAY
**Marco Metodológico:** Desarrollo Guiado por Pruebas (TDD: Rojo $\rightarrow$ Verde $\rightarrow$ Refactor) + Kanban Distribuido por Cortes Verticales  
**Código Documental:** `YUYAY-BKL-2026-V2.0`  
**Equipo:** Los JackersMainsters (ESPOCH) · **Estación Redactora:** Estación Omar (`OMARFIGUEROA`)

---

## 1. Gobernanza del Tablero Kanban y Ciclo TDD

### 1.1. Estructura de Columnas del Tablero Kanban Remoto (TDD IBM + `obra/superpowers`)
Dado que los tres integrantes trabajan de forma remota desde distintas ciudades con una ventana crítica de 10 días hasta el *Code Freeze* (25/11/2026), el flujo de trabajo descarta Spec Kit (`SDD`) y opera mediante **6 columnas Kanban sincronizadas con los pasos de TDD (IBM TDD + `obra/superpowers` en `docs/superpowers/plans/`)**:

| Columna Kanban | Regla Operativa y Límite WIP | Evidencia Técnica Requerida (`superpowers` TDD) |
|---|---|---|
| **1. Backlog** | Cola priorizada mediante MoSCoW (`Must` / `Should`). | `PBI` documentado con su Historia de Usuario (`HU`) y requisito (`RF`). |
| **2. Ready (Contrato / Slice Definido)** | **WIP $\le 2$ por dev.** Interfaces (`Consumes` / `Produces`) y archivos objetivo definidos. | Firmas en `types.ts`, `measurements.ts` o `GameScreenProps` listas. |
| **3. TDD — Fase ROJO (Red)** | **WIP $= 1$ por dev.** Se escribe primero el test y se verifica que falle. | Archivo `*.test.ts` escrito; salida de `bun run test` muestra `FAIL` en el nuevo caso. |
| **4. TDD — Fase VERDE (Green)** | **WIP $= 1$ por dev.** Se implementa el código mínimo para pasar el test. | Salida de `bun run test` muestra `PASS` al 100 % en local. |
| **5. Refactor & Verificación del Slice** | Limpieza de código, verificación del slice y apertura de PR ($< 400\text{ líneas}$). | `bun run check` en verde en local + Pipeline CI de GitHub Actions en verde. |
| **6. Done (Merged)** | Revisión por pares aprobada y *Squash & Merge* completado. | Funcionalidad integrada en `main` sin regresiones. |

### 1.2. Definición de Listo (*Definition of Ready — DoR*)
Ningún ítem del Product Backlog (`PBI`) puede pasar a `3. TDD — Fase ROJO` sin cumplir:
1. Tener definidos sus contratos de tipos en TypeScript (`GameScreenProps`, `MeasurementRow`, `MetricRequest`).
2. Contar con los datos o fixtures de entrada necesarios para escribir el test que falla (`tests/fixtures/` o mocks de Dexie con `fake-indexeddb`).
3. Cumplir el límite **WIP $= 1$ tarjeta activa por desarrollador**.

### 1.3. Definición de Terminado (*Definition of Done — DoD*)
Un `PBI` solo se considera `DONE` cuando:
1. Incluye su archivo de pruebas unitarias/integración (`*.test.ts` / `*.test.tsx`) creado bajo el ciclo TDD.
2. El comando `bun run check` pasa al 100 % en local y en GitHub Actions CI (`typecheck` + `lint` + `language` + `pm:check` + `test` + `build` + `build-storybook`).
3. Respeta estrictamente las reglas del dominio (cero lenguaje clínico, cero persistencia de audio/video, y retorno `null` $\rightarrow$ `"No evaluable"` en lugar de `0`).
4. El Pull Request ($< 400\text{ líneas}$) ha sido aprobado por al menos un compañero y fusionado en `main`.

---

## 2. Product Backlog Maestro (`PBI-001` a `PBI-032`)

| ID PBI | Épica | Ítem del Product Backlog (Tarjeta Kanban / Corte Vertical) | Prioridad (MoSCoW) | Story Points | Archivo Test TDD Asociado | Responsable (Estación) | Estado Kanban (15/11/2026) |
|---|---|---|---|---|---|---|---|
| **PBI-001** | EP-01 | Inicialización Bun + Vite 8 + React 19 + TS 7 + Tailwind 4 y tokens CSS (`src/design/tokens.css`) | Must (P0) | 3 | `tests/smoke.test.ts` | Andrés (M1) | **DONE** |
| **PBI-002** | EP-01 | Primitivas UI accesibles (`Button`, `Card`, `ProgressBar`, `StatusBanner`) y catálogo Storybook 10 con HMR | Must (P0) | 5 | `ui/*.stories.tsx` | Andrés (M1) | **DONE** |
| **PBI-003** | EP-01 | Textos de producto en español sin lenguaje clínico (`src/app/copy.ts`) y guardia `scripts/check-language.ts` | Must (P0) | 3 | `bun run language` | Andrés (M1) | **DONE** |
| **PBI-004** | EP-01 | Máquina de estados `SessionFlow` (`src/app/session-flow.ts`) y persistencia Dexie v1 (`sessions`, `deviceChecks`) | Must (P0) | 5 | `session-flow.test.ts`, `storage.test.ts` | Andrés (M1) | **DONE** |
| **PBI-005** | EP-01 | Pantalla de Consentimiento Informado (`ConsentScreen.tsx`) alineada al Mockup P2 | Must (P0) | 3 | `session-flow.test.ts` | Andrés (M1) | **DONE** |
| **PBI-006** | EP-02 | Evaluador de calidad de captura (`quality.ts`), medidor de FPS (`capture-loop.ts`) y nivel RMS de micrófono (`mic-level.ts`) | Must (P0) | 5 | `quality.test.ts`, `capture-loop.test.ts` | Andrés (M1) | **DONE** |
| **PBI-007** | EP-02 | Cargador local `HandLandmarker` (`src/vision/hand-landmarker.ts`) y pantalla `DeviceCheckScreen.tsx` (Mockup P3) | Must (P0) | 5 | `quality.test.ts` | Andrés (M1) | **DONE** |
| **PBI-008** | EP-02 | Shell PWA (`vite-plugin-pwa`), instalador de modelos con SHA-256 (`model-cache.ts`) e `InstallScreen.tsx` | Must (P0) | 5 | `model-cache.test.ts` | Andrés (M1) | **DONE** |
| **PBI-009** | EP-03..07 | Funciones puras de métricas base (`pinch`, `stability`, `reaction`, `path`) y fixtures sintéticas en `tests/fixtures/` | Must (P0) | 5 | `tests/metrics/*.test.ts` | Andrés (M1) | **DONE** |
| **PBI-010** | EP-08 | Migración Dexie v2 con tabla `measurements` y repositorio (`src/features/storage/measurements.ts`) — *Hueco (a)* | Must (P0) | 3 | `measurements.test.ts` | Andrés (M1) | **DONE** |
| **PBI-011** | EP-03..07 | Web Worker de métricas (`metric.worker.ts`, `metric-dispatch.ts`, `run-in-worker.ts`) — *Hueco (b)* | Must (P0) | 3 | `run-in-worker.test.ts` | Andrés (M1) | **DONE** |
| **PBI-012** | Transv. | Pipeline CI/CD en GitHub Actions (`.github/workflows/ci.yml`), Biome y protección de rama `main` — *Hueco (f)* | Must (P0) | 3 | `.github/workflows/ci.yml` | Andrés (M1) | **DONE** |
| **PBI-013** | Transv. | Habilitar acceso de colaborador a Omar en repo privado y validar `bun run check` en Estación Omar (`OMARFIGUEROA`) — *Hueco (h)* | Must (P0) | 1 | Suite completa (31 tests) | Andrés + **Omar** | **READY (Fase 1 - 16/11)** |
| **PBI-014** | EP-05 | Implementar cargador local `FaceLandmarker` (`src/vision/face-landmarker.ts`) con `/models/face_landmarker.task` — *Hueco (d)* | Must (P0) | 3 | `face-landmarker.test.ts` | Andrés (M1) | **READY (Fase 1 - 16/11)** |
| **PBI-015** | EP-04 | Implementar cargador local `PoseLandmarker` (`src/vision/pose-landmarker.ts`) con `/models/pose_landmarker_lite.task` — *Hueco (d)* | Must (P0) | 3 | `pose-landmarker.test.ts` | Andrés (M1) | **READY (Fase 1 - 16/11)** |
| **PBI-016** | EP-04 | Ciclo TDD en `src/metrics/stability.ts` para estimación espectral FFT (frecuencia dominante en Hz) con señales de 3, 5 y 8 Hz ($\le 0.5\text{ Hz}$) | Must (P0) | 5 | `tests/metrics/stability.test.ts` | Andrés (M1) | **READY (Fase 1 - 17/11)** |
| **PBI-017** | EP-05 | Ciclo TDD de métrica pura de simetría facial (`src/metrics/facial.ts`) y analizador de vocal sostenida (`src/audio/vowel-capture.ts`) + Worker | Must (P0) | 5 | `tests/metrics/facial.test.ts`, `vowel-capture.test.ts` | Andrés (M1) | **READY (Fase 1 - 17/11)** |
| **PBI-018** | EP-07 | Ciclo TDD de Minijuego 5 `"Semáforo"` (`SemaforoScreen.tsx`) con estímulos accesibles (Círculo Verde vs. Octágono Rojo, Mockups P8, P26, P27) | Must (P0) | 5 | `SemaforoScreen.test.tsx` | Alexander (Ryzen 5) | **READY (Fase 2 - 18-19/11)** |
| **PBI-019** | EP-07 | Conectar `"Semáforo"` con Web Worker (`kind: 'reaction'`), guardado en Dexie (`measurements`) y guiones de prueba al 100 % | Must (P0) | 3 | `tests/metrics/reaction.test.ts` | Alexander (Ryzen 5) | **BACKLOG (Fase 2 - 19/11)** |
| **PBI-020** | EP-06 | Ciclo TDD de Minijuego 4 `"El vuelo del cóndor"` (`CondorScreen.tsx`): fase de observación de 3 mesetas en mapa 2D y fase de navegación (Mockups P7, P23, P24) | Must (P0) | 5 | `CondorScreen.test.tsx` | Alexander (Ryzen 5) | **BACKLOG (Fase 2 - 19-20/11)** |
| **PBI-021** | EP-06 | Conectar `"El vuelo del cóndor"` con Web Worker (`kind: 'path'`), cálculo de distancia óptima, guardado en Dexie y tests al 100 % | Must (P0) | 3 | `tests/metrics/path.test.ts` | Alexander (Ryzen 5) | **BACKLOG (Fase 2 - 20/11)** |
| **PBI-022** | EP-03 | Ciclo TDD de Minijuego 1 `"La pinza"` (`PinzaScreen.tsx`): tutorial, captura de 10 s por mano, transición intermanual (Mockups P4, P16, P17), Worker y Dexie | Must (P0) | 5 | `PinzaScreen.test.tsx`, `pinch.test.ts` | Andrés (M1) + Alexander | **BACKLOG (Fase 2 - 18-20/11)** |
| **PBI-023** | EP-08 | Ciclo TDD del motor puro de plantillas del informe (`src/features/report/report-templates.ts`) cubriendo estados `'ok'` y `'not-evaluable'` | Must (P0) | 5 | `report-templates.test.ts` | **Omar (ASUS i5)** | **READY (Fase 1-2 - 17-19/11)** |
| **PBI-024** | EP-08 | Ciclo TDD de pantalla `ReportScreen.tsx` (reemplazando `Placeholder.tsx`): lectura de Dexie (`listMeasurements`, `latestDeviceCheck`) y renderizado de las 5 dimensiones — *Hueco (e)* | Must (P0) | 5 | `ReportScreen.test.tsx` | **Omar (ASUS i5)** | **BACKLOG (Fase 2 - 19-20/11)** |
| **PBI-025** | EP-04 | Ciclo TDD de Minijuego 2 `"Sostén la bandeja"` (`BandejaScreen.tsx`): instrucciones, captura de estabilidad de brazos/manos (Mockups P5, P18, P19), FFT en Worker y Dexie | Must (P0) | 5 | `BandejaScreen.test.tsx` | Andrés (M1) | **BACKLOG (Fase 3 - 21-22/11)** |
| **PBI-026** | EP-05 | Ciclo TDD de Minijuego 3 `"Sonríe y habla"` (`SonrisaScreen.tsx`): encuadre facial + vocal sostenida `"aaa"` con onda de audio (Mockups P6, P20, P22), Worker y Dexie | Must (P0) | 5 | `SonrisaScreen.test.tsx` | Andrés (M1) | **BACKLOG (Fase 3 - 22-23/11)** |
| **PBI-027** | EP-08 | Ciclo TDD de exportación local de resultados (`src/features/report/export-report.ts`): descarga de archivo `.json` auditable y hoja de impresión/PDF (`@media print`) — *Hueco (e)* | Must (P1) | 5 | `export-report.test.ts` | **Omar (ASUS i5)** | **BACKLOG (Fase 3 - 21-22/11)** |
| **PBI-028** | EP-08 | Ciclo TDD del botón de supresión local de datos de sesión (*"Borrar mis resultados"*) y flujo de repetición individual de actividades `"No evaluables"` | Should (P1) | 3 | `export-report.test.ts`, `ReportScreen.test.tsx` | **Omar (ASUS i5)** + Alexander | **BACKLOG (Fase 3 - 23/11)** |
| **PBI-029** | Transv. | Cableado final e integración TDD de los 5 minijuegos e informe real en `src/app/App.tsx`, eliminando los `Placeholder.tsx` | Must (P0) | 3 | `src/app/App.test.tsx` | Alexander + Andrés + **Omar** | **BACKLOG (Fase 3 - 23/11)** |
| **PBI-030** | EP-08 | Ejecutar pruebas en dispositivos reales (MacBook Air M1, Ryzen 5 9600X y ASUS Core i5-7200U), registrar FPS ($\ge 20\text{ FPS}$) y certificar operación 100 % Offline — *Hueco (g)* | Must (P0) | 5 | Protocolo Hardware / `docs/validacion-mvp.md` | **Omar** + Andrés + Alexander | **BACKLOG (Fase 4 - 24/11)** |
| **PBI-031** | Transv. | Consolidar `docs/validacion-mvp.md` y actualizar `README.md` para el **Code Freeze** oficial del 25 de noviembre | Must (P0) | 3 | `bun run check` final | **Omar** + Alexander + Andrés | **BACKLOG (Fase 4 - 25/11)** |
| **PBI-032** | Transv. | Pruebas técnicas de proyección/audio en Auditorio FIE (26/11) y Defensa en la Gran Final Presencial (27/11) | Must (P0) | 3 | Demo en Vivo (7 min) | Los 3 integrantes | **BACKLOG (Fase 5 - 26-27/11)** |

---

## 3. Guía de Ejecución TDD desde la Estación Omar (`OMARFIGUEROA`)

Al trabajar bajo **TDD + Kanban** en la **Estación Omar** (Windows 10/11 en el portátil ASUS Core i5-7200U con SSD de 480 GB), no se requiere instalar `specify-cli`, `uv`, WSL ni Git Bash. Todo el ciclo de desarrollo se ejecuta directamente en PowerShell con **Bun y Vitest**:

### 3.1. Secuencia TDD para el Subsistema de Informes y Exportación (`PBI-023`, `PBI-024`, `PBI-027`, `PBI-028`)
1. **Paso 1 (Rama Kanban):**
   ```powershell
   cd "C:\Users\User\Desktop\COMPARTIDA\CONCURSO POLIBYTE\Desarrollo\yuyay"
   git switch -c feat/report-templates
   ```
2. **Paso 2 (Fase TDD ROJO — Escribir primero los tests):**
   - Crear `src/features/report/report-templates.test.ts` definiendo los casos de prueba para las 5 actividades (`pinza`, `bandeja`, `sonrisa`, `condor`, `semaforo`) tanto con `status: 'ok'` como con `status: 'not-evaluable'` (`value: null`).
   - Ejecutar `bunx vitest run src/features/report/report-templates.test.ts` y comprobar que falla (**Rojo**).
3. **Paso 3 (Fase TDD VERDE — Implementar la lógica mínima):**
   - Crear `src/features/report/report-templates.ts` hasta que `bunx vitest run src/features/report/report-templates.test.ts` pase al 100 % (**Verde**).
4. **Paso 4 (Fase TDD para `ReportScreen.tsx` y `export-report.ts`):**
   - Repetir el ciclo Rojo $\rightarrow$ Verde creando `src/features/report/export-report.test.ts` $\rightarrow$ `export-report.ts` y `src/features/report/ReportScreen.test.tsx` $\rightarrow$ `ReportScreen.tsx`.
5. **Paso 5 (Fase REFACTOR & Gate de Calidad antes de abrir PR):**
   ```powershell
   bun run check
   ```
   *(Verifica secuencialmente `typecheck`, `lint`, `language`, `pm:check`, `test` y `build`).*
