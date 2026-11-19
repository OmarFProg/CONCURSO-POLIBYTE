# 01. ESPECIFICACIÓN DE REQUISITOS DE SOFTWARE (SRS) — PROYECTO YUYAY
**Estándar de Referencia:** ISO/IEC/IEEE 29148:2018 / IEEE Std 830-1998 Orientado a TDD (*Test-Driven Development*) y Kanban  
**Código Documental:** `YUYAY-SRS-2026-V2.0`  
**Proyecto:** Yuyay — Tamizaje Neurológico Experimental con Minijuegos (PWA Local)  
**Equipo:** Los JackersMainsters (ESPOCH) · **Estación Redactora:** Estación Omar (`OMARFIGUEROA`)

---

## 1. Introducción

### 1.1. Propósito
Este documento especifica de forma completa, verificable y trazable los requisitos funcionales (`RF`), requisitos no funcionales (`RNF`), reglas ético-técnicas invariables (`RC`) y contratos de interfaz de usuario y hardware para el Producto Mínimo Viable (MVP) de **Yuyay** en la Fase 2 del *PoliByte Innovation Challenge 2026*. Sirve como contrato técnico único para impulsar el desarrollo guiado por pruebas (**TDD con Vitest**) y la gestión de flujo mediante **Kanban Distribuido** entre los tres desarrolladores ubicados en distintas ciudades (Andrés Gaibor, Alexander Villalva y Omar Figueroa).

### 1.2. Alcance del Producto
**Yuyay** (del kichwa: *pensamiento, memoria o recuerdo*) es una Aplicación Web Progresiva (PWA) en español, 100 % local y autónoma (sin servidor backend ni servicios externos de inferencia), diseñada para capturar y cuantificar en una sola sesión breve ($\le 6\text{ minutos}$) métricas objetivas asociadas a cinco dominios explorados en las neurociencias:

1. **Coordinación motora fina bimanual** (*Minijuego 1: "La pinza"*).
2. **Estabilidad postural de miembros superiores y frecuencia oscilatoria** (*Minijuego 2: "Sostén la bandeja"*).
3. **Simetría geométrica facial y producción vocal sostenida** (*Minijuego 3: "Sonríe y habla"*).
4. **Memoria de trabajo visoespacial y eficiencia de navegación 2D** (*Minijuego 4: "El vuelo del cóndor"*).
5. **Atención sostenida, velocidad de respuesta visomotora e inhibición Go/No-Go** (*Minijuego 5: "Semáforo"*).

**Delimitación explícita de alcance:** Yuyay es un **prototipo experimental de medición y registro trazable**. No acredita capacidad diagnóstica ni de tamizaje clínico, no atribuye enfermedades (como párkinson, ACV o alzhéimer), no conserva grabaciones de audio ni video, y ante cualquier captura defectuosa reporta explícitamente el estado **"No evaluable"** permitiendo repetir la actividad.

### 1.3. Definiciones, Acrónimos y Abreviaturas
- **PWA (*Progressive Web App*):** Aplicación web instalable que utiliza Service Workers y Cache Storage para operar sin conexión a internet.
- **TDD (*Test-Driven Development*):** Práctica de ingeniería de software basada en el ciclo corto **Rojo (escribir prueba automatizada que falla) $\rightarrow$ Verde (implementar código mínimo que pasa la prueba) $\rightarrow$ Refactorización (mejorar el diseño manteniendo las pruebas en verde)**.
- **Kanban:** Método ágil de gestión de flujo continuo basado en un tablero visual con límites estrictos de trabajo en progreso (*WIP Limits*).
- **Landmarks:** Puntos de referencia anatómicos bidimensionales/tridimensionales normalizados extraídos por MediaPipe Tasks Vision (21 por mano, 478 en rostro, 33 en pose corporal).
- **No Evaluable (`not-evaluable`):** Estado de resultado válido en Yuyay cuando la muestra incumple umbrales mínimos de calidad (pérdida de encuadre, $\text{FPS} < 20$, $\text{RMS de micrófono} < 0.001$ o permiso denegado). Nunca se representa numéricamente como `0`.
- **dBFS (*Decibels relative to Full Scale*):** Unidad logarítmica de amplitud acústica medida respecto al máximo nivel digital posible ($0\text{ dBFS}$).
- **CV (*Coeficiente de Variación*):** Razón entre la desviación estándar y la media de los intervalos temporales entre ciclos ($\sigma / \mu$), expresada como proporción o porcentaje para cuantificar la regularidad del ritmo motor.
- **u.n. (*Unidades Normalizadas*):** Medida adimensional escalada respecto a una referencia anatómica (ej. tamaño palmar) o al alto/ancho del lienzo ($0..1$).

---

## 2. Descripción General del Sistema

### 2.1. Perspectiva del Producto
Yuyay opera íntegramente en el navegador del cliente (*Client-Side Architecture*). Durante la instalación inicial descarga y verifica criptográficamente (`SHA-256`) el shell de la aplicación y los modelos preentrenados de MediaPipe (`.task` y binarios `.wasm`). Durante la ejecución de las sesiones:
- **No existe backend de aplicación ni de base de datos remota.**
- **Los algoritmos de cálculo cinemático, espectral, acústico y espacial se ejecutan en un hilo secundario (`Web Worker`)** para mantener la interfaz fluida ($\ge 20\text{ FPS}$).
- **Los resultados numéricos y el estado de calidad se almacenan únicamente en `IndexedDB` local mediante `Dexie.js` v2.**

### 2.2. Clases y Características de Usuarios
| Clase de Usuario | Rol en el Sistema | Características Técnicas y de Accesibilidad |
|---|---|---|
| **Participante (Adulto Voluntario / Adulto Mayor)** | Ejecuta las actividades frente al dispositivo siguiendo las instrucciones visuales. | Puede tener baja familiaridad digital o presbicia/daltonismo. Requiere textos claros en español, alto contraste ($\ge 4.5:1$), botones grandes ($\ge 48\text{ px}$) y redundancia de forma y color. |
| **Operador de Sesión / Evaluador Académico** | Acompaña al participante, supervisa el encuadre e iluminación, revisa el informe técnico y exporta los registros. | Conoce el protocolo operativo (`docs/protocolo-sesion.md`); requiere indicadores técnicos explícitos (FPS, resolución, estado de caché offline, causas de fallo de captura y exportación JSON/PDF). |
| **Profesional de Salud (Beneficiario Potencial Futuro)** | Revisa los registros cuantitativos exportados tras futura validación clínica. | Requiere que las medidas tengan unidades físicas claras, metadatos de calidad de captura y cero juicios diagnósticos infundados. |

### 2.3. Entorno Operativo y Estaciones de Hardware Declaradas
El sistema debe compilar, pasar su suite TDD y ejecutarse en las siguientes estaciones del equipo:
1. **Estación Omar (`OMARFIGUEROA` — Portátil ASUS Q304UAK):** Windows 10/11, Intel Core i5-7200U (2C/4T @ 2.50 GHz), 12 GB RAM DDR4, Intel HD Graphics 620, SSD 480 GB, Webcam y Micrófono integrados. *(Estación de referencia para el límite inferior de rendimiento $\ge 20\text{ FPS}$ sin GPU dedicada y desarrollo TDD del subsistema de informes).*
2. **Estación Omar Secundaria (`DESKTOP-ML70LGD`):** Windows, Intel Core i5-6400 (4C/4T @ 2.70 GHz), 8 GB RAM DDR3, NVIDIA GeForce GT 710, HDD 2 TB.
3. **Estación Andrés (`MacBook Air M1`):** macOS, Chip Apple M1, 8 GB RAM unificada, Cámara FaceTime HD y micrófonos integrados. *(Estación principal de demostración y desarrollo TDD de visión/audio).*
4. **Estación Alexander (`DESKTOP-MO7Q55S`):** Windows 11, AMD Ryzen 5 9600X (6C/12T @ 3.9–5.4 GHz), 16 GB RAM DDR5 @ 5600 MHz, SSD NVMe PCIe 4.0. *(Estación de desarrollo TDD de juegos 2D e integración de flujo).*

---

## 3. Reglas Ético-Técnicas Invariables del Dominio (`RC`)

Todo requisito funcional y toda prueba automatizada en Vitest deben verificar el cumplimiento de las siguientes 7 reglas invariables del proyecto:

- **RC-01 (Prototipo Experimental, Nunca Clínico):** Prohibido incluir en la interfaz, código de presentación o informes términos de condición médica, valoración clínica, probabilidades de patología, rangos normales por edad o semáforos clínicos. El script `bun run language` (`scripts/check-language.ts`) falla el build si detecta vocabulario clínico prohibido.
- **RC-02 (Local y Sin Conexión Primero):** Tras completar la pantalla de instalación (`InstallScreen`), la aplicación no debe realizar ninguna petición HTTP/HTTPS externa. MediaPipe se inicializa obligatoriamente con `FilesetResolver.forVisionTasks('/models/wasm')`.
- **RC-03 (Privacidad Biométrica por Diseño):** Los flujos de cámara (`MediaStream`) y micrófono (`AudioBuffer` / `Float32Array`) se procesan en memoria volátil y se descartan en cada cuadro/ventana. Prohibido guardar fotos, videos, grabaciones de voz o secuencias crudas de coordenadas en `IndexedDB` o `localStorage`.
- **RC-04 ("No Evaluable" como Resultado Válido):** Cuando una captura es insuficiente o inválida, las funciones de `src/metrics/*` retornan `null` y la UI/Informe presenta `"No evaluable"` junto con la razón técnica y la opción de repetir. Prohibido sustituir `null` por `0`.
- **RC-05 (Métricas Deterministas y Puras para TDD):** Los módulos en `src/metrics/` y `src/features/report/report-templates.ts` no acceden al DOM ni invocan `Date.now()` o `Math.random()`. Reciben arreglos tipados con marcas de tiempo explícitas y producen salidas idénticas ante la misma entrada.
- **RC-06 (Accesibilidad Universal):** Idioma español claro, contraste $\ge 4.5:1$, áreas interactivas $\ge 48 \times 48\text{ px}$, foco de teclado visible (`focus-visible:ring-2`), regiones `aria-live` para cambios de estado y soporte para `prefers-reduced-motion`.
- **RC-07 (Rendimiento Medido y Transparente):** El bucle de captura mide los FPS reales en tiempo de ejecución, marca el flag `low-fps` cuando caen por debajo de $20\text{ FPS}$ y registra el equipo y resolución en `DeviceCheckRow`.

---

## 4. Especificación de Interfaces con los Mockups (`PROPUESTA/mockups/`)

El diseño de pantallas de Yuyay se fundamenta en los tres pliegos de mockups oficiales, adaptados a las reglas invariables `RC-01` a `RC-07`:

| Pliego de Mockup | Pantallas en Mockup | Componente en Repositorio (`Desarrollo/yuyay`) | Elementos Visuales e Interacción Requeridos |
|---|---|---|---|
| **Pliego 1 (`45de6305...jpg`)**<br>Pantallas 1 y 2 | **P1:** Portada *"Yuyay · Conoce cómo estás"*<br>**P2:** *"Antes de empezar"* (Consentimiento) | `src/features/consent/ConsentScreen.tsx` | Identidad visual andina, duración estimada (~5–6 min), 3 tarjetas de garantías de privacidad (*Todo se procesa en tu dispositivo*, *No guardamos audio ni video*, *Puedes borrar tus resultados*), aviso de que no sustituye una evaluación médica, casilla obligatoria de mayoría de edad y aceptación, y botón `Continuar`. |
| **Pliego 1 (`45de6305...jpg`)**<br>Pantalla 3 | **P3:** *"Prepara tu espacio"* (Perfil de sesión y Dispositivos) | `src/features/devices/DeviceCheckScreen.tsx`<br>`src/features/install/InstallScreen.tsx` | Registro opcional/contextual de edad y mano dominante (`Izquierda` / `Derecha`), vista previa espejada de cámara con encuadre, lista de verificación en vivo (`Buena iluminación`, `Cámara lista` con FPS y landmarks, `Micrófono listo` con RMS), barra de progreso de verificación SHA-256 de modelos `.task` y botón `Empezar juegos`. |
| **Pliego 1 (P4) y Pliego 2 (`26c8584f...jpg` P16, P17)** | **P16:** Tutorial *"La pinza"*<br>**P4:** Ejecución mano derecha<br>**P17:** Transición *"Ahora, la otra mano"* | `src/features/games/pinza/PinzaScreen.tsx` | Pantalla de instrucciones en 3 pasos (*10 segundos por mano*), botones `Practicar` / `Empezar`, vista de cámara con superposición de puntos del pulgar (4) e índice (8), contador regresivo `00:10`, pantalla intermedia de descanso con cuenta regresiva `3` para cambio a mano izquierda (`Estoy listo`), botón `Pausar` y validación de encuadre. |
| **Pliego 1 (P5) y Pliego 2 (`26c8584f...jpg` P18, P19)** | **P19:** Instrucciones *"Sostén la bandeja"*<br>**P18:** Paso 1 (*En reposo*, `00:15`)<br>**P5:** Paso 2 (*Brazos extendidos*, `00:20`) | `src/features/games/bandeja/BandejaScreen.tsx` | Instrucciones con ilustración de pasos (*1. Relaja las manos*, *2. Extiende ambos brazos*), aviso de mantener el dispositivo apoyado, temporizador visible (`00:15` / `00:20`), guía visual de bandeja horizontal y detección continua de muñecas/brazos en cuadro. |
| **Pliego 1 (P6), Pliego 2 (P20, P21) y Pliego 3 (P22)** | **P6/P22:** *"Sonríe y habla"* (Rostro)<br>**P20:** *"Mantén la vocal 'aaa'"* (`00:05`) | `src/features/games/sonrisa/SonrisaScreen.tsx` | Paso visual: marco con óvalo guía facial, indicador de pasos (`Relaja` -> `Sonríe`), captura de malla de 478 puntos durante `00:05`. Paso acústico: prompt grande `"aaa" — Usa tu voz habitual`, visualizador de barras de onda en tiempo real, temporizador `00:05`, badge `Procesando en este dispositivo` y detección de señal de micrófono. |
| **Pliego 1 (P7) y Pliego 3 (`e808711f...jpg` P23, P24, P25)** | **P23:** Tutorial *"El vuelo del cóndor"*<br>**P7:** Observación del mapa<br>**P24:** Navegación a mesetas 1, 2 y 3<br>**P25:** Orientación al inicio | `src/features/games/condor/CondorScreen.tsx` | Mapa 2D del valle andino con punto `Inicio` y mesetas numeradas `1`, `2`, `3`; botón `Ya lo recuerdo / Empezar`; ocultamiento de números e indicador superior de objetivo activo (`1 ✓ -> 2 -> 3`); controles direccionales grandes en pantalla (`<-`, `^`, `->`) + flechas del teclado; temporizador y cálculo de ruta. |
| **Pliego 1 (P8) y Pliego 3 (`e808711f...jpg` P26, P27)** | **P26:** Tutorial *"Semáforo"*<br>**P8:** Estímulo Go (Verde)<br>**P27:** Estímulo No-Go (Rojo) | `src/features/games/semaforo/SemaforoScreen.tsx` | Explicación visual dual: **Verde (Círculo con check/`+`) = toca el botón**; **Rojo (Octágono/Círculo con mano de alto/`-`) = espera**. Botones `Practicar` y `Empezar`. Área central de estímulo de alto contraste, temporizador, botón grande `Tocar` (o tecla `Espacio`) y botón `Pausar`. |
| **Pliego 1 (`45de6305...jpg`)**<br>Pantalla 9 | **P9:** *"Tu perfil"* / Informe de Sesión | `src/features/report/ReportScreen.tsx` | Resumen estructurado de las 5 actividades con valores numéricos, unidades físicas, estado de calidad (`Válida` o `No evaluable` con botón de repetir actividad), explicación generada por plantilla determinista, aviso destacado de que no constituye diagnóstico médico, botón `Exportar / Guardar en el dispositivo` (JSON + Impresión/PDF) y opción de borrar datos/nueva sesión. |

---

## 5. Requisitos Funcionales (`RF`)

### 5.1. Subsistema de Flujo de Sesión y Consentimiento (`src/app/`, `src/features/consent/`)
- **RF-001 (Máquina de Estados de Sesión):** El sistema DEBE gestionar la sesión mediante una máquina de estados determinista (`SessionFlow` en `src/app/session-flow.ts`) con los estados secuenciales: `consent` $\rightarrow$ `devices` $\rightarrow$ `install` $\rightarrow$ `games` $\rightarrow$ `report`.
- **RF-002 (Persistencia y Recuperación de Sesión):** El sistema DEBE asignar un identificador único (`UUID v4`) a cada sesión, almacenarlo en `localStorage` (`yuyay.sessionId`) y persistir el objeto `SessionRow` en la tabla `sessions` de IndexedDB (Dexie), permitiendo recuperar el estado exacto y los juegos ya completados (`completedGames`) si la página se recarga.
- **RF-003 (Bloqueo por Consentimiento Informado):** En el estado `consent`, el sistema DEBE presentar las garantías de procesamiento local, no grabación de audio/video y descargo médico (Mockup P2), y DEBE impedir la transición al estado `devices` mientras el usuario no haya marcado explícitamente la casilla de aceptación.
- **RF-004 (Rutas de Depuración por Hash):** El sistema DEBE permitir el acceso directo de desarrollo y prueba a cualquiera de los cinco minijuegos mediante la ruta hash `#/game/<gameId>` (`pinza`, `bandeja`, `sonrisa`, `condor`, `semaforo`).

### 5.2. Subsistema de Prueba de Dispositivos y Calidad de Captura (`src/features/devices/`)
- **RF-005 (Activación de Periféricos tras Gesto del Usuario):** El sistema DEBE solicitar permisos de cámara (`video`) y micrófono (`audio`) mediante `navigator.mediaDevices.getUserMedia` únicamente tras una acción explícita del usuario (clic en botón), garantizando compatibilidad con políticas de privacidad de Chrome, Edge y Safari.
- **RF-006 (Evaluación Cuantitativa de Calidad de Dispositivos):** El sistema DEBE medir en vivo la resolución de cámara, la tasa de cuadros por segundo (`createFpsMeter`), el nivel RMS del micrófono (`measureMicRms`) y la detección inicial de mano (`HandLandmarker`), generando un objeto `CaptureQuality` con flags tipados (`permission-denied`, `camera-unavailable`, `mic-unavailable`, `no-signal`, `hand-absent`, `low-fps`).
- **RF-007 (Bloqueo ante Fallos Críticos de Dispositivo):** Si `CaptureQuality.ok === false` (por permiso denegado, cámara ausente o micrófono con $\text{RMS} < 0.001$), el sistema DEBE mostrar un banner de error descriptivo, ofrecer el botón `Reintentar` y bloquear el avance al estado `install`.
- **RF-008 (Registro de Verificación de Dispositivo):** Toda verificación de dispositivos DEBE guardarse en la tabla `deviceChecks` de Dexie (`DeviceCheckRow`) vinculada al `sessionId` activo, incluyendo `fps`, `resolution`, `micRms`, `flags` y `userAgent`.

### 5.3. Subsistema de Instalación PWA y Caché de Modelos (`src/pwa/`, `src/features/install/`)
- **RF-009 (Descarga y Verificación SHA-256 de Modelos):** El sistema DEBE verificar si los modelos `hand_landmarker.task`, `face_landmarker.task` y `pose_landmarker_lite.task` se encuentran en `Cache Storage` (`yuyay-models-v1`). Si faltan, DEBE descargarlos reportando progreso real y calcular su hash `SHA-256` mediante `crypto.subtle.digest`, comparándolo contra `public/models/checksums.json`.
- **RF-010 (Bloqueo por Integridad de Modelos):** Si algún archivo `.task` falla la descarga o no coincide con su checksum `SHA-256` tras un reintento automático, el sistema DEBE establecer el estado de instalación en `'error'`, listar el modelo fallido, ofrecer botón de reintento y bloquear el paso a `games`.
- **RF-011 (Aviso de Conectividad):** Si el navegador pierde conexión (`navigator.onLine === false`), el sistema DEBE mostrar un `StatusBanner` accesible (`aria-live="polite"`) informando que la conexión solo se requiere para la instalación inicial y que posteriormente Yuyay opera sin red.

### 5.4. Subsistema de Visión por Computador y Audio (`src/vision/`, `src/audio/`)
- **RF-012 (Cargadores Locales de MediaPipe):** El sistema DEBE proveer cargadores perezosos (*lazy loaders*) para `HandLandmarker` (`src/vision/hand-landmarker.ts`), `FaceLandmarker` (`src/vision/face-landmarker.ts`) y `PoseLandmarker` (`src/vision/pose-landmarker.ts`) configurados exclusivamente con archivos locales (`/models/wasm` y `/models/*.task`).
- **RF-013 (Ciclo de Vida y Liberación de Recursos Visuales):** Al desmontar cualquier pantalla de minijuego o dispositivo, el sistema DEBE detener el bucle `requestAnimationFrame`, detener todas las pistas del `MediaStream` (`track.stop()`) y cerrar la instancia del landmarker (`.close()`) para evitar fugas de memoria en equipos de 8–12 GB de RAM.
- **RF-014 (Analizador Acústico de Vocal Sostenida):** El módulo `src/audio/` DEBE capturar muestras en el dominio del tiempo desde un `AnalyserNode`, calcular la energía RMS por ventana, detectar los instantes de inicio y fin de emisión vocal por encima del umbral de ruido, y devolver la duración continua en segundos (`s`) y la intensidad media en `dBFS`.

### 5.5. Subsistema de los Cinco Minijuegos (`src/features/games/`)
- **RF-015 (Minijuego 1 — "La pinza"):**
  - DEBE mostrar instrucciones claras (Mockup P16) y guiar la ejecución de apertura y cierre entre el pulgar (landmark 4) y el índice (landmark 8) durante $10\text{ s}$ por mano (derecha e izquierda, con transición en Mockup P17).
  - DEBE normalizar la distancia pulgar-índice dividiéndola por el tamaño palmar de referencia (distancia muñeca landmark 0 a nudillo medio landmark 9) para ser invariante a la distancia a la cámara.
  - DEBE enviar la serie `HandFrame[]` al Web Worker (`kind: 'pinch'`) para obtener: ciclos totales (`cycles` con histéresis cierre $< 0.25$, apertura $> 0.35$), amplitud media normalizada (`amplitude`) y variación del ritmo (`rhythmVariation`).
- **RF-016 (Minijuego 2 — "Sostén la bandeja"):**
  - DEBE mostrar instrucciones de postura sentada (Mockup P19) y capturar las coordenadas normalizadas $(x, y)$ de las muñecas/manos durante una ventana controlada ($15\text{–}20\text{ s}$, Mockups P18 y P5).
  - DEBE verificar que ambas manos/brazos permanezcan visibles en el encuadre; si se pierde la detección de forma sostenida, DEBE marcar la captura como `"No evaluable"`.
  - DEBE enviar la serie `StabilityFrame[]` al Web Worker (`kind: 'stability'`) para calcular el desplazamiento medio, la dispersión postural (`rms`) y la frecuencia oscilatoria dominante en hercios (`Hz`) mediante remuestreo uniforme y Transformada Rápida de Fourier (FFT).
- **RF-017 (Minijuego 3 — "Sonríe y habla"):**
  - **Fase Visual (Mockup P6/P22):** DEBE capturar con `FaceLandmarker` los 478 puntos del rostro durante la sonrisa ($5\text{ s}$) y calcular el índice de simetría geométrica comparando el desplazamiento de las comisuras labiales izquierda y derecha respecto al eje medio facial (puente nasal a mentón).
  - **Fase Acústica (Mockup P20):** DEBE solicitar al participante sostener la vocal `"aaa"` durante $5\text{ s}$ con retroalimentación visual de onda sonora, calculando duración continua (`s`) e intensidad media (`dBFS`).
- **RF-018 (Minijuego 4 — "El vuelo del cóndor"):**
  - **Fase de Observación (Mockup P7/P23):** DEBE renderizar un mapa 2D en Canvas/SVG con el punto de partida del cóndor ($P_0$) y tres mesetas objetivo numeradas ($O_1, O_2, O_3$) durante una ventana de codificación ($6\text{–}10\text{ s}$) o hasta que el usuario pulse `Ya lo recuerdo`.
  - **Fase de Navegación (Mockup P24):** DEBE ocultar las etiquetas de las mesetas y permitir al usuario desplazar al cóndor mediante teclas de dirección (`Flechas` / `WASD`) o botones táctiles en pantalla ($\ge 48\text{ px}$) para visitar $O_1 \rightarrow O_2 \rightarrow O_3$ en orden dentro de un tiempo límite ($45\text{ s}$).
  - DEBE muestrear la polilínea `PathPoint[]` y calcular en el Web Worker (`kind: 'path'`): objetivos alcanzados en orden (`hits` de 0 a 3), duración en segundos (`duration`), longitud total recorrida (`length`) y longitud óptima de referencia ($L_{\text{opt}}$).
- **RF-019 (Minijuego 5 — "Semáforo"):**
  - DEBE presentar instrucciones claras del paradigma Go/No-Go (Mockup P26) y ejecutar una secuencia de $20\text{ a }30$ ensayos (~70 % estímulos `go` y ~30 % estímulos `nogo`).
  - DEBE distinguir cada estímulo simultáneamente por **color, forma geométrica y símbolo interno** (Estímulo `go`: Círculo Verde con check/`+`; Estímulo `nogo`: Octágono Rojo con mano de alto/`-`, Mockups P8 y P27).
  - DEBE registrar marcas temporales de alta resolución (`performance.now()`) y calcular en el Web Worker (`kind: 'reaction'`): latencia mediana de aciertos Go en milisegundos (`medianLatency`), total de omisiones ante Go (`omissions`) y total de falsas alarmas/comisiones ante No-Go (`falseAlarms`).
- **RF-020 (Manejo Uniforme de Pausa y Repetición por Calidad):** En los cinco minijuegos, si el resultado devuelto por el Worker es `null` o la calidad de captura es inválida (`status: 'not-evaluable'`), la pantalla del juego DEBE informar el motivo técnico, mostrar el botón `Repetir actividad` (limpiando la medición previa con `clearMeasurementsForGame`) y nunca reportar `0` como resultado.

### 5.6. Subsistema de Cálculo en Web Worker y Persistencia (`src/workers/`, `src/features/storage/`)
- **RF-021 (Despacho Asíncrono en Web Worker):** Toda ejecución de métricas al finalizar un minijuego DEBE realizarse a través de `runMetricInWorker(request)` (`src/workers/run-in-worker.ts`), manteniendo un fallback automático al hilo principal solo si el entorno de pruebas (`jsdom`) no soporta `Worker`.
- **RF-022 (Persistencia Estandarizada de Mediciones):** Al concluir cada minijuego (sea con captura válida `'ok'` o inválida `'not-evaluable'`), el sistema DEBE persistir las filas `MeasurementRow` correspondientes en la tabla `measurements` de Dexie v2 (`saveMeasurements`), registrando `id`, `sessionId`, `gameId`, `metric`, `value` (`number | null`), `unit`, `status`, `qualityFlags` y `createdAt`.

### 5.7. Subsistema de Informe por Plantillas y Exportación (`src/features/report/`)
- **RF-023 (Generación de Informe mediante Plantillas Deterministas):** Al alcanzar el estado `report`, el sistema DEBE consultar `listMeasurements(sessionId)` y `latestDeviceCheck(sessionId)` y construir el informe de sesión utilizando exclusivamente plantillas parametrizadas deterministas en TypeScript (`src/features/report/report-templates.ts`).
- **RF-024 (Regla de Renderizado de Capturas "No Evaluables" en el Informe):** Para cualquier actividad cuyas filas en `measurements` tengan `status === 'not-evaluable'` o `value === null`, la plantilla del informe DEBE ocultar los campos numéricos, mostrar el estado **"No evaluable"**, detallar las banderas de calidad (`qualityFlags`) que causaron el rechazo y ofrecer un botón para repetir dicha actividad.
- **RF-025 (Exportación Local en Formato JSON):** El informe DEBE incluir una acción `Descargar datos (JSON)` que genere en memoria un archivo `.json` estructurado con los metadatos de sesión, el chequeo de dispositivos y las mediciones derivadas, descargándolo localmente mediante `Blob` sin realizar peticiones de red.
- **RF-026 (Vista Imprimible / Exportación a PDF):** El informe DEBE contar con una acción `Imprimir / Guardar PDF` (`window.print()`) respaldada por reglas de estilo `@media print` que oculten botones de navegación y presenten un reporte técnico limpio en blanco y negro/alto contraste listo para entregar a un profesional o evaluador.
- **RF-027 (Derecho de Supresión Local de Datos):** En cumplimiento de la promesa mostrada en el Mockup P2 (*"Puedes borrar tus resultados en cualquier momento"*), la pantalla de informe y/o configuración DEBE ofrecer un botón `Borrar datos de esta sesión` que elimine los registros de `sessions`, `deviceChecks` y `measurements` en IndexedDB y reinicie el flujo.

---

## 6. Requisitos No Funcionales (`RNF`)

### 6.1. Rendimiento y Eficiencia
- **RNF-001 (Tasa de Cuadros en Visión):** El módulo visual activo (`HandLandmarker`, `FaceLandmarker` o `PoseLandmarker`) DEBE sostener una tasa media $\ge 20\text{ FPS}$ durante las ventanas de captura en las estaciones objetivo (incluyendo el portátil ASUS Core i5-7200U con Intel HD Graphics 620).
- **RNF-002 (Duración Total de Sesión):** El flujo operativo completo (Consentimiento + Prueba de Dispositivos + 5 Minijuegos + Informe) DEBE poder completarse en un tiempo total $\le 6\text{ minutos}$ bajo condiciones normales de captura.
- **RNF-003 (No Bloqueo del Hilo Principal):** El cálculo de FFT, conteo de ciclos con histéresis y longitud de trayectoria en el Web Worker DEBE completarse en $< 50\text{ ms}$ tras finalizar la captura de cada juego.
- **RNF-004 (Consumo de Memoria RAM):** El consumo de memoria del proceso de la pestaña del navegador NO DEBE superar los $600\text{ MB}$ de RAM a lo largo de los 5 minijuegos, liberando cada modelo `.task` antes de instanciar el siguiente.

### 6.2. Exactitud Metrológica y Validación Mediante TDD
- **RNF-005 (Exactitud en Conteo de Ciclos de Pinza):** El algoritmo `countPinchCycles` / `pinchMetrics` DEBE presentar un error relativo $\le 10\,\%$ frente a un conjunto de 10 secuencias anotadas manualmente (`tests/fixtures/pinch-cycles.json`).
- **RNF-006 (Exactitud en Frecuencia Oscilatoria):** El estimador espectral de `stabilityMetrics` DEBE recuperar la frecuencia dominante con un error absoluto $\le 0.5\text{ Hz}$ sobre señales sintéticas de prueba de $3\text{ Hz}$, $5\text{ Hz}$ y $8\text{ Hz}$.
- **RNF-007 (Exactitud en Guiones de Cóndor y Semáforo):** Las funciones `pathMetrics` y `reactionMetrics` DEBEN coincidir al $100\,\%$ en conteo de aciertos, omisiones, falsas alarmas y longitud de ruta frente a los guiones automatizados (`tests/fixtures/path-trace.json` y `tests/fixtures/reaction-script.json`).

### 6.3. Privacidad, Seguridad y Confiabilidad
- **RNF-008 (Cero Persistencia Multimedia):** Ninguna tabla de IndexedDB, `localStorage`, `sessionStorage` ni archivo exportado puede contener imágenes, fotogramas `ImageData`, blobs `.webm`/`.mp4`/`.wav` ni vectores crudos de audio PCM.
- **RNF-009 (Verificación Criptográfica de Modelos):** Ningún modelo de IA local (`.task`) puede ejecutarse sin haber validado previamente su resumen criptográfico `SHA-256` contra `public/models/checksums.json`.
- **RNF-010 (Autonomía Offline Total):** Una vez alcanzado el estado `ready` en `InstallScreen`, el $100\,\%$ de las funciones de la aplicación (los 5 minijuegos, el Web Worker, la base de datos Dexie y la exportación del informe) DEBEN operar con la interfaz de red física desconectada (Modo Avión / Wi-Fi apagado).

### 6.4. Usabilidad, Accesibilidad y Mantenibilidad TDD
- **RNF-011 (Accesibilidad Visual y Motora):** Todos los textos deben mantener una relación de contraste $\ge 4.5:1$ sobre su fondo; todos los botones y controles interactivos deben tener dimensiones mínimas de $48 \times 48\text{ px}$ y anillo de foco visible.
- **RNF-012 (Accesibilidad para Daltonismo):** Ninguna mecánica de juego ni indicador de estado puede depender exclusivamente del color (en particular, los estímulos de *Semáforo* y los banners de estado deben combinar color, forma geométrica e iconografía/texto).
- **RNF-013 (Cobertura TDD y Tipado Estricto):** Todo el código fuente DEBE estar escrito en TypeScript 7 con `strict: true` y `noUncheckedIndexedAccess: true`, contando con pruebas automatizadas en Vitest (`*.test.ts`) y pasando sin errores los gates `bun run typecheck`, `bun run lint` (Biome), `bun run language`, `bun run pm:check`, `bun run test` y `bun run build`.
