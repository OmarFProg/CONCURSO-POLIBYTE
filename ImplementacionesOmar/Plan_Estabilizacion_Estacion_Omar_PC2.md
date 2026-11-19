# Plan por Fases: Estabilización de Repositorios y Arranque de Desarrollo en Estación Omar (PC2 - `OMARFIGUEROA`)

**Proyecto:** Yuyay — Tamizaje neurológico experimental con minijuegos (PoliByte Innovation Challenge 2026)  
**Equipo:** Los JackersMainsters (ESPOCH)  
**Estación destino:** PC2 (`OMARFIGUEROA`, Ambato)  
**Estación origen actual:** PC1 (`DESKTOP-ML70LGD`)  
**Documento base:** `PROPUESTA/PROPUESTA.txt` y `Ingenieria de Software/PlanTentativoDesarrolloYuyay.txt`  
**Fecha de elaboración:** 19 de noviembre de 2026  

---

## 1. Diagnóstico de Situación Actual (Hallazgos Verificados)

Tras auditar el workspace en PC1 (`DESKTOP-ML70LGD`), el estado de las pruebas locales y los repositorios remotos en GitHub, se identificaron **tres repositorios distintos** con niveles de avance diferentes:

| Repositorio | URL | Estado Actual | Contenido / Uso |
| :--- | :--- | :--- | :--- |
| **1. Planeación y Documentación** | `https://github.com/OmarFProg/CONCURSO-POLIBYTE.git` | `origin/main` en commit `21746b7`. **PC1 tiene cambios locales sin subir** (`PROPUESTA.txt`, `Entorno/`, `Ingenieria de Software/PlanTentativoDesarrolloYuyay.txt`, `mockups/`, etc.). | Propuesta técnica, bases, factibilidad, documentación de Ingeniería de Software y carpeta `ImplementacionesOmar/`. |
| **2. Clon antiguo de código** | `https://github.com/AndresGaibor/yuyay.git` (en carpeta `yuyay/` de PC1) | En commit `589fb73` (15/11), rama `feat/informe-exportacion-offline`. **Tiene trabajo local sin commitear de Omar** (`src/pwa/model-cache.ts`, `model-cache.test.ts`, `use-asset-install.ts`, `specs/002-informe-exportacion-offline/`). Suite local: 12 archivos / 41 tests en verde. | **Obsoleto respecto al repo oficial.** No contiene los avances de visión, juegos ni reglas nuevas de CI/Linear. |
| **3. Repositorio Oficial del Concurso** | `https://github.com/Polibyte/pb2026-jackersmainsters.git` | `main` en `e81431a` (PR #12). Rama `dev` en `3e26d61` (PR #13). | **Fuente oficial de verdad para el código de Yuyay.** |

### Diferencias críticas detectadas en el Repositorio Oficial (`Polibyte/pb2026-jackersmainsters.git`)
1. **Reglas nuevas de flujo y CI:**
   - Todo PR hacia `main` pasa por el job `linear` en `.github/workflows/ci.yml`, que **exige un identificador `ALE-<número>` en el título del PR** (por ejemplo: `ALE-24: informe por plantillas y exportación`).
   - El trabajo de equipo utiliza **Linear** (`proyecto YUYAY`, equipo `ALE`, CLI `linearis`) y rama de integración **`dev`** con hook local `.githooks/pre-push` que bloquea pushes directos a `main`.
2. **Avance real de código en rama `dev` (`3e26d61`):**
   - Ya están implementados `FaceLandmarker` y `PoseLandmarker` (`ALE-8`).
   - Ya están implementados **3 de los 5 minijuegos** (con sus placeholders retirados):
     - `PinzaScreen.tsx` (`ALE-13`, *La pinza*)
     - `BandejaScreen.tsx` (`ALE-14`, *Sostén la bandeja*)
     - `SonrisaScreen.tsx` (`ALE-15`, *Sonríe y habla*, junto a `vowel-capture.ts` y `facial.ts`)
   - Se añadieron dependencias en `package.json`: `lucide-react` (`^1.74.0`) y `@types/node` (`^22.0.0`).
   - **Siguen en `Placeholder.tsx`:** `condor/`, `semaforo/` (Alexander) y **`report/` (Omar)**.

---

## 2. Validación de Pertinencia y Estrategia de Trabajo en Estación Omar (PC2)

### 2.1. ¿Hacia qué repositorio trabajar desde PC2?
- **NO trabajar código nuevo sobre `AndresGaibor/yuyay.git` ni subir código de la app a `OmarFProg/CONCURSO-POLIBYTE.git`.** Hacerlo generaría divergencia grave frente a `Pinza`, `Bandeja`, `Sonrisa`, `lucide-react` y las reglas de CI.
- **Para código de la PWA Yuyay:** Trabajar **directamente sobre `https://github.com/Polibyte/pb2026-jackersmainsters.git`**.
- **Para documentación, planificación y bitácoras propias:** Mantener `https://github.com/OmarFProg/CONCURSO-POLIBYTE.git` sincronizado entre PC1 y PC2.

### 2.2. ¿Es pertinente una implementación independiente en Estación Omar para validación del equipo?
**Sí, es totalmente pertinente y recomendable**, bajo la siguiente modalidad:
- **Implementación aislada en rama de feature dentro del repo oficial** (por ejemplo `feat/ALE-<id>-informe-offline`), nacida desde `dev` (o `main` según confirme el equipo).
- Al vivir el módulo de Omar en `src/features/report/` (y la mejora de caché offline en `src/pwa/model-cache.ts`), puede desarrollarse y probarse de forma **100 % independiente mediante TDD con Vitest y `fake-indexeddb`**, sin bloquear ni pisar los minijuegos `condor` y `semaforo` de Alexander.
- Al finalizar cada hito, Omar ejecuta `bun run check` en PC2 y abre un **Pull Request en `Polibyte/pb2026-jackersmainsters.git`** para que Andrés y Alexander lo revisen y validen antes del *Code Freeze* (25 de noviembre).

### 2.3. Avisos obligatorios al equipo de desarrollo (Andrés y Alexander)
Antes o al iniciar la sesión en PC2, comunicar en el canal del equipo:
1. **Permisos y rama base:** Confirmar que la cuenta de GitHub de Omar tiene permisos de escritura (`Write`) en `Polibyte/pb2026-jackersmainsters.git` y confirmar si la rama base para el PR del informe será `dev` (donde ya están `Pinza`, `Bandeja` y `Sonrisa`) o `main`.
2. **Issue en Linear (`ALE-NN`):** Verificar qué issue `ALE-*` corresponde a *Informe por plantillas, exportación y soporte offline* (o crearlo/asignarlo con `linearis`) para incluir su código en rama, commits y título del PR.
3. **Contrato de métricas (`MeasurementRow`):** Verificar que las claves `metric` guardadas por `PinzaScreen`, `BandejaScreen` y `SonrisaScreen` en `dev` coincidan exactamente con las documentadas en `PlanTentativoDesarrolloYuyay.txt` (sección 4.3) antes de escribir las plantillas del informe.
4. **Rescate de mejora offline de PC1:** Informar que se portará desde PC1 la mejora TDD en `src/pwa/model-cache.ts` (`loadChecksumsWithCache` y `verifyCachedModels` para operación sin red).
5. **Integridad de archivos núcleo:** Confirmar que no se modificarán `src/app/session-flow.ts`, `src/features/storage/db.ts`, `vite.config.ts` ni `public/models/*` sin aviso previo.

---

## 3. Diagnóstico de Tecnologías Requeridas en PC2 (`OMARFIGUEROA`)

> **Nota de hardware:** En `PlanTentativoDesarrolloYuyay.txt` PC2 figura como portátil ASUS Q304UAK (`OMARFIGUEROA`, Core i5-7200U, 12 GB RAM, Intel HD 620, SSD 480 GB), mientras que `AnalisisFactibilidad/Equipos Omar/OMARFIGUEROA.txt` contiene un reporte CPU-Z de i5-6400 / 8 GB DDR3. Al encender PC2, ejecutar el script de diagnóstico de la Fase 1 para confirmar hardware y software instalado.

### 3.1. Stack obligatorio para desarrollar y validar en PC2
| Herramienta | Versión requerida | Propósito en Yuyay | Estado de referencia (PC1) |
| :--- | :--- | :--- | :--- |
| **Git** | `>= 2.40` (Windows) | Control de versiones, ramas, hooks `.githooks/` | `2.50.1.windows.1` |
| **Bun** | `>= 1.4.3` | Gestor de paquetes, scripts (`bun run check`), `language`, `pm:check` | `1.4.4` |
| **Node.js** | `>= 22.0.0` | Motor requerido en `engines.node` (`package.json`) y tooling Vite/Vitest | `v22.20.0` |
| **VS Code** | Actualizado | IDE principal en PC2 (`OMARFIGUEROA`) | `1.147.0` |
| **Extensiones VS Code** | Biome, Tailwind CSS IntelliSense, Vitest | Lint/formato automático según `biome.json` y ejecución de tests | Recomendado |
| **`linearis` CLI** | Última (`npm i -g linearis`) | Gestión obligatoria de issues `ALE-*` y documentos en Linear (`YUYAY`) | Instalado en PC1 |
| **Navegador Chromium (Edge/Chrome)** | Actualizado | Pruebas PWA, Service Worker, Cache Storage (`yuyay-models-v1`), DevTools Offline y cámara/micrófono | En sistema |

### 3.2. Herramientas NO necesarias en PC2 (evitar sobrecarga)
- **Docker / nginx / Traefik:** Solo los usa el propietario del host de despliegue (`docs/instalacion.md`).
- **`uv` / `specify-cli` / `omp`:** Opcionales; el plan vigente adopta **TDD con Vitest + Kanban/Linear** de forma nativa en Windows PowerShell (`PlanTentativoDesarrolloYuyay.txt`, sección 3.1).

---

## 4. Plan por Fases para Estabilización e Inicio de Sesión de Desarrollo

### FASE 0 — Respaldo y Estabilización desde PC1 (`DESKTOP-ML70LGD`) *(Antes de moverse a PC2)*
**Objetivo:** Evitar pérdida de documentación o código avanzado que solo existe en PC1.
1. **Sincronizar repo de planeación (`CONCURSO-POLIBYTE`):**
   - Revisar cambios pendientes en `D:\FLASH 7 SEP 2026\ESPOCH OCTAVO\CONCURSO POLIBYTE` (`git status`).
   - Hacer commit y `git push origin main` de los documentos actualizados (`PROPUESTA/PROPUESTA.txt`, `Ingenieria de Software/PlanTentativoDesarrolloYuyay.txt`, `Entorno/instruccion.txt`, `ImplementacionesOmar/`, etc.), asegurando que `yuyay/` esté ignorado o no se suba como submódulo roto.
2. **Rescatar el avance local de Omar en `yuyay/`:**
   - Desde `D:\FLASH 7 SEP 2026\ESPOCH OCTAVO\CONCURSO POLIBYTE\yuyay`, generar un parche limpio con los cambios realizados en `src/pwa/model-cache.ts`, `src/pwa/model-cache.test.ts` y `src/features/install/use-asset-install.ts`:
     ```powershell
     git diff > ..\ImplementacionesOmar\pc1_model_cache_offline.patch
     ```
   - Copiar también `specs/002-informe-exportacion-offline/` y `docs/superpowers/plans/2026-11-16-omar-informe-offline.md` a una carpeta de respaldo (o incluirlos en `CONCURSO-POLIBYTE` temporalmente) para tenerlos disponibles en PC2.

---

### FASE 1 — Diagnóstico de Entorno y Credenciales en PC2 (`OMARFIGUEROA`)
**Objetivo:** Verificar que PC2 cumple todos los requisitos técnicos y de acceso antes de clonar.
1. **Ejecutar diagnóstico en PowerShell en PC2:**
   ```powershell
   $env:COMPUTERNAME
   Get-CimInstance Win32_Processor | Select-Object Name, NumberOfCores, NumberOfLogicalProcessors
   [math]::Round((Get-CimInstance Win32_ComputerSystem).TotalPhysicalMemory / 1GB, 2)
   foreach ($cmd in @("git","bun","node","npm","code","linearis")) {
     $c = Get-Command $cmd -ErrorAction SilentlyContinue
     if ($c) { "$cmd -> $(& $cmd --version 2>&1 | Select-Object -First 1)" } else { "$cmd -> FALTA INSTALAR" }
   }
   ```
2. **Instalar lo que falte en PC2:**
   - **Bun:** `powershell -c "irm bun.sh/install.ps1 | iex"` (verificar `bun --version >= 1.4.3`).
   - **Node.js `>= 22`:** Instalar LTS v22 si es inferior o no existe.
   - **Linear CLI:** `npm install -g linearis` (si falta).
3. **Configurar credenciales en PC2:**
   - Autenticar **Git** con la cuenta de GitHub que tiene acceso a `OmarFProg/CONCURSO-POLIBYTE` y a `Polibyte/pb2026-jackersmainsters`.
   - Configurar **Linear** (`AGENTS.md`, `ALE-23`): ejecutar `linearis auth login` con la **API Key personal** del workspace `alexander-villalva` y verificar con:
     ```powershell
     linearis projects read YUYAY
     ```

---

### FASE 2 — Estabilización Limpia de Repositorios en PC2 (`OMARFIGUEROA`)
**Objetivo:** Tener ambos repositorios limpios, actualizados y pasando todas las pruebas base en PC2.
1. **Clonar/actualizar el repositorio de planeación:**
   ```powershell
   git clone https://github.com/OmarFProg/CONCURSO-POLIBYTE.git
   ```
   (Si ya existía en PC2, respaldar cualquier archivo local distinto y hacer `git pull origin main`).
2. **Clonar el repositorio oficial del concurso en una ruta dedicada (separada del repo de planeación):**
   ```powershell
   git clone https://github.com/Polibyte/pb2026-jackersmainsters.git
   cd pb2026-jackersmainsters
   ```
   - Activar el hook de protección de `main`:
     ```powershell
     git config core.hooksPath .githooks
     ```
   - Verificar rama de trabajo (`dev` vs `main` según acuerdo del equipo):
     ```powershell
     git fetch origin
     git checkout dev
     ```
3. **Instalar dependencias y validar línea base:**
   ```powershell
   bun install --frozen-lockfile
   bun run check
   ```
   **Criterio de salida de Fase 2:** `typecheck`, `lint`, `language`, `pm:check`, `test` y `build` deben pasar al 100 % en verde en PC2 sin modificaciones locales.

---

### FASE 3 — Coordinación con Equipo y Portado del Avance de PC1
**Objetivo:** Alinear Linear, contratos de métricas y traer el trabajo previo de `model-cache` sin conflictos.
1. **Enviar los avisos al equipo** (sección 2.3) y consultar en Linear:
   ```powershell
   linearis issues list --project YUYAY --team ALE
   ```
2. **Inspeccionar los contratos reales implementados en `dev`:**
   - Revisar `src/features/games/pinza/`, `bandeja/`, `sonrisa/` y `src/features/storage/measurements.ts` para confirmar las claves `metric` y `unit` exactas que ya persisten en Dexie.
3. **Crear rama de trabajo trazable en `pb2026-jackersmainsters`:**
   ```powershell
   git checkout -b feat/ALE-XX-informe-exportacion-offline
   ```
   *(Reemplazar `ALE-XX` por el ID real del issue en Linear).*
4. **Aplicar y validar el trabajo rescatado de PC1:**
   - Revisar si `src/pwa/model-cache.ts` en `dev` cambió respecto a `589fb73`; si aplica, integrar `pc1_model_cache_offline.patch` (soporte offline de `checksums.json` y `verifyCachedModels`).
   - Ejecutar `bun run check` y confirmar todo en verde antes de escribir código nuevo.

---

### FASE 4 — Inicio de Sesión de Desarrollo TDD en Estación Omar (PC2)
**Objetivo:** Completar el módulo asignado a Omar (`src/features/report/` + exportación + validación offline/hardware) listo para revisión del equipo.
1. **Ciclo TDD 1 — Motor de Plantillas (`src/features/report/report-templates.test.ts` -> `report-templates.ts`):**
   - **Rojo:** Escribir tests para las 5 dimensiones (`pinza`, `bandeja`, `sonrisa`, `condor`, `semaforo`) con unidades exactas, bloqueo estricto de `"No evaluable"` (nunca emitir `0` cuando `status === 'not-evaluable'` o `value === null`), presencia de `copy.app.disclaimer` y cero términos clínicos.
   - **Verde & Refactor:** Implementar las funciones puras hasta pasar `bun run test` y `bun run language`.
2. **Ciclo TDD 2 — Pantalla de Informe (`ReportScreen.test.tsx` -> `ReportScreen.tsx` / `Placeholder.tsx`):**
   - Conectar con Dexie (`listMeasurements(sessionId)`, `latestDeviceCheck(sessionId)`), mostrar causas técnicas de `"No evaluable"`, acción para repetir actividades no evaluables y botones accesibles (`>= 48 px`, `aria-live="polite"`).
3. **Ciclo TDD 3 — Exportación Local y Borrado (`export-report.test.ts` -> `export-report.ts`):**
   - Descarga `.json` sin blobs multimedia ni landmarks crudos, vista de impresión/PDF (`@media print`) y borrado completo de sesión en Dexie.
4. **Validación Hardware y Offline en PC2:**
   - Ejecutar `bun run build && bun run preview`, instalar modelos en el navegador, desconectar red (DevTools Offline / Wi-Fi apagado), completar flujo y verificar 0 peticiones externas y generación del informe.
   - Registrar FPS observados en PC2 y tiempo total de sesión (`<= 6 minutos`) para `docs/validacion-mvp.md`.
5. **Cierre y Entrega para Validación del Equipo:**
   - Ejecutar `bun run check` completo.
   - Hacer push de la rama y abrir PR hacia `dev` (o `main`) con título `ALE-XX: <descripción>`, actualizando el estado del issue con `linearis`.
