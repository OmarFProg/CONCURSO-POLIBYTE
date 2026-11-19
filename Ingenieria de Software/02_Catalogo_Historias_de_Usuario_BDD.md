# 02. CATÁLOGO DE HISTORIAS DE USUARIO Y ESPECIFICACIÓN DE TESTS (BDD -> TDD) — PROYECTO YUYAY
**Marco Metodológico:** Agile User Stories (Criterios INVEST) + Especificación de Pruebas para TDD (Vitest) y Kanban  
**Código Documental:** `YUYAY-US-2026-V2.0`  
**Equipo:** Los JackersMainsters (ESPOCH) · **Estación Redactora:** Estación Omar (`OMARFIGUEROA`)

---

## 1. De Historias de Usuario (Gherkin) al Ciclo TDD (Rojo $\rightarrow$ Verde $\rightarrow$ Refactor)

Bajo la metodología **TDD + Kanban** adoptada por el equipo, cada Historia de Usuario (`HU`) no requiere artefactos intermedios de Spec Kit: sus escenarios **Dado / Cuando / Entonces** se traducen **directamente en bloques `describe()` e `it()` de Vitest** antes de escribir el código de producción:

1. **`Dado` (*Arrange*):** Configura la fixture sintética, el estado inicial de `createSessionFlow()` o los registros en `fake-indexeddb`.
2. **`Cuando` (*Act*):** Invoca la función pura, el puente `runMetricInWorker()` o la acción del componente.
3. **`Entonces` (*Assert*):** Ejecuta `expect(...)` comprobando el resultado exacto, las unidades o el retorno de `null` $\rightarrow$ `"No evaluable"`.

| Código Épica | Nombre de la Épica | Estado en Tablero Kanban (15/11/2026) | Pantallas de Mockup Asociadas |
|---|---|---|---|
| **EP-01** | Consentimiento Informado, Privacidad y Gestión de Sesión | **Done** (`session-flow.test.ts` en verde) | Pliego 1: Pantallas 1 y 2 |
| **EP-02** | Preparación del Espacio, Chequeo de Dispositivos e Instalación Offline | **Done** (`quality.test.ts`, `model-cache.test.ts` en verde) | Pliego 1: Pantalla 3 |
| **EP-03** | Minijuego 1: "La pinza" (Coordinación Motora Fina Bimanual) | Métrica en verde (`pinch.test.ts`) / UI en **Ready** | Pliego 1: P4 · Pliego 2: P16, P17 |
| **EP-04** | Minijuego 2: "Sostén la bandeja" (Estabilidad Postural y Oscilación) | Base en verde (`stability.test.ts`) / FFT y UI en **Ready** | Pliego 1: P5 · Pliego 2: P18, P19 |
| **EP-05** | Minijuego 3: "Sonríe y habla" (Simetría Facial y Emisión Vocal) | En **Ready** para Ciclo TDD (Fase 1–3) | Pliego 1: P6 · Pliego 2: P20 · Pliego 3: P22 |
| **EP-06** | Minijuego 4: "El vuelo del cóndor" (Memoria Espacial en Mapa 2D) | Métrica en verde (`path.test.ts`) / UI 2D en **Ready** | Pliego 1: P7 · Pliego 3: P23, P24, P25 |
| **EP-07** | Minijuego 5: "Semáforo" (Atención Sostenida e Inhibición Go/No-Go) | Métrica en verde (`reaction.test.ts`) / UI en **Ready** | Pliego 1: P8 · Pliego 3: P26, P27 |
| **EP-08** | Informe Determinista por Plantillas, Exportación Local y QA en Hardware | DB en verde (`measurements.test.ts`) / Informe en **Ready (Estación Omar)** | Pliego 1: Pantalla 9 |

---

## 2. Catálogo Detallado de Historias de Usuario (`HU-001` a `HU-020`)

---

### ÉPICA 01: Consentimiento Informado, Privacidad y Gestión de Sesión

#### `HU-001` — Aceptación de Consentimiento Informado y Garantías de Privacidad
- **Declaración:** *Como* participante adulto voluntario, *quiero* leer en lenguaje claro qué mide Yuyay, que mis videos/audios nunca se guardan y que no reemplaza a un médico antes de aceptar participar, *para* decidir de forma informada y segura si inicio la sesión.
- **Prioridad:** Must Have (P0) · **Estimación:** 3 SP (4 h) · **Estado Kanban:** Done (`src/features/consent/ConsentScreen.tsx`, `src/app/session-flow.test.ts`)
- **Mockups vinculados:** Pliego 1 — Pantalla 1 (*"Yuyay · Conoce cómo estás"*) y Pantalla 2 (*"Antes de empezar"*).
- **Requisitos trazados:** `RF-001`, `RF-003`, `RC-01`, `RC-03`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Bloqueo de avance sin aceptación explícita
    Dado que el participante abre la aplicación en una sesión nueva en estado "consent"
    Cuando visualiza las garantías de privacidad local y el aviso de prototipo experimental
    Entonces el botón "Continuar" permanece deshabilitado mientras la casilla de aceptación no esté marcada.

  Escenario 2: Aceptación válida y transición a prueba de dispositivos
    Dado que el participante se encuentra en la pantalla de consentimiento
    Cuando marca la casilla de aceptación y pulsa el botón "Continuar"
    Entonces el estado de la sesión cambia de "consent" a "devices"
    Y la sesión queda registrada localmente en la tabla "sessions" de IndexedDB.
  ```

#### `HU-002` — Recuperación de Sesión Activa tras Recarga del Navegador
- **Declaración:** *Como* operador de prueba, *quiero* que la aplicación conserve el identificador de sesión y el progreso de los minijuegos completados si el navegador se recarga accidentalmente, *para* no obligar al participante a repetir toda la sesión desde el inicio.
- **Prioridad:** Must Have (P0) · **Estimación:** 3 SP (4 h) · **Estado Kanban:** Done (`src/app/session-flow.test.ts`, `src/features/storage/storage.test.ts`)
- **Requisitos trazados:** `RF-001`, `RF-002`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Restauración de estado y juegos completados desde Dexie
    Dado que existe una sesión activa en estado "games" con los juegos "pinza" y "bandeja" completados
    Cuando se recarga la página del navegador
    Entonces la aplicación lee "yuyay.sessionId" de localStorage y recupera la fila desde Dexie
    Y reanuda el flujo directamente en el tercer minijuego ("sonrisa") sin perder las mediciones previas.
  ```

---

### ÉPICA 02: Preparación del Espacio, Chequeo de Dispositivos e Instalación Offline

#### `HU-003` — Verificación de Cámara, Encuadre, FPS y Micrófono ("Prepara tu espacio")
- **Declaración:** *Como* participante acompañado por un operador, *quiero* comprobar antes de los juegos que mi cámara, iluminación, tasa de cuadros ($\ge 20\text{ FPS}$) y micrófono funcionan correctamente, *para* evitar que las actividades fallen a mitad de la sesión.
- **Prioridad:** Must Have (P0) · **Estimación:** 5 SP (8 h) · **Estado Kanban:** Done (`src/features/devices/quality.test.ts`, `src/vision/capture-loop.test.ts`)
- **Mockups vinculados:** Pliego 1 — Pantalla 3 (*"Prepara tu espacio"*).
- **Requisitos trazados:** `RF-005`, `RF-006`, `RF-007`, `RF-008`, `RNF-001`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Permiso de cámara o micrófono denegado
    Dado que el usuario se encuentra en la pantalla de prueba de dispositivos
    Cuando pulsa "Activar cámara" y deniega el permiso en el navegador
    Entonces el evaluador de calidad registra el flag "permission-denied" con "ok: false"
    Y se muestra un banner de error con el botón "Reintentar", impidiendo avanzar a los juegos.

  Escenario 2: Micrófono en silencio o desconectado (Sin señal)
    Dado que la cámara está activa pero el micrófono registra un nivel RMS menor a 0.001
    Cuando concluye la ventana de prueba de dispositivos
    Entonces se activa el flag bloqueante "no-signal" con "ok: false"
    Y la interfaz solicita verificar el micrófono y reintentar la prueba.

  Escenario 3: Dispositivos verificados exitosamente
    Dado que la cámara entrega video con mano detectada y el micrófono registra RMS >= 0.001
    Cuando el usuario finaliza la verificación
    Entonces se persiste el registro "DeviceCheckRow" en Dexie con los FPS y resolución medidos
    Y el flujo avanza al paso de instalación y verificación de modelos ("install").
  ```

#### `HU-004` — Instalación Local y Verificación SHA-256 de Modelos MediaPipe
- **Declaración:** *Como* operador en una zona sin conectividad garantizada, *quiero* que la aplicación descargue los modelos de visión (`.task` y `.wasm`), compruebe su integridad `SHA-256` y los guarde en caché local antes de iniciar los juegos, *para* ejecutar todas las sesiones posteriores 100 % sin conexión a internet.
- **Prioridad:** Must Have (P0) · **Estimación:** 5 SP (8 h) · **Estado Kanban:** Done (`src/pwa/model-cache.test.ts`)
- **Requisitos trazados:** `RF-009`, `RF-010`, `RF-011`, `RNF-009`, `RNF-010`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Descarga inicial y verificación criptográfica exitosa
    Dado que la caché "yuyay-models-v1" está vacía
    Cuando se inicia la pantalla de instalación
    Entonces la barra de progreso avanza por cada modelo ("downloading" -> "verifying" -> "ready")
    Y cada archivo se valida con SHA-256 contra "public/models/checksums.json" antes de habilitar el botón de continuar.

  Escenario 2: Bloqueo ante archivo de modelo corrupto
    Dado que un archivo ".task" descargado produce un hash SHA-256 distinto al esperado
    Cuando falla también el reintento automático
    Entonces el estado queda en "error", se muestra el nombre del recurso afectado y se bloquea el paso a los minijuegos.
  ```

---

### ÉPICA 03: Minijuego 1 — "La pinza" (Coordinación Motora Fina Bimanual)

#### `HU-005` — Instrucciones, Práctica y Captura Bimanual en "La pinza"
- **Declaración:** *Como* participante, *quiero* ver instrucciones paso a paso de cómo abrir y cerrar el pulgar y el índice durante 10 segundos con mi mano derecha y luego descansar antes de repetir con la mano izquierda, *para* realizar el ejercicio correctamente frente a la cámara.
- **Prioridad:** Must Have (P0) · **Estimación:** 5 SP (8 h) · **Responsable TDD:** Andrés Gaibor + Alexander Villalva (`PinzaScreen.test.tsx`)
- **Mockups vinculados:** Pliego 1 — Pantalla 4 · Pliego 2 — Pantalla 16 (*Tutorial La pinza*) y Pantalla 17 (*"Ahora, la otra mano"*).
- **Requisitos trazados:** `RF-012`, `RF-015`, `RF-020`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Ejecución completa de mano derecha, pausa de transición y mano izquierda
    Dado que el participante inicia el minijuego "La pinza" y lee los 3 pasos de instrucción
    Cuando pulsa "Empezar" y realiza aperturas y cierres de pulgar e índice durante 10 segundos con su mano visible
    Entonces la pantalla muestra el conteo regresivo "00:10" y la guía visual de los puntos 4 y 8 de la mano
    Y al terminar los primeros 10 segundos presenta la pantalla de transición "Ahora, la otra mano" antes de iniciar los 10 segundos de la mano izquierda.
  ```

#### `HU-006` — Cálculo de Ciclos, Amplitud Normalizada y Ritmo en Web Worker ("La pinza")
- **Declaración:** *Como* operador evaluador, *quiero* que el sistema calcule de forma determinista el número de ciclos completados, la amplitud media normalizada por el tamaño de la mano y la variación del ritmo, o reporte `"No evaluable"` si la mano salió de cuadro, *para* contar con registros objetivos sin bloquear la interfaz.
- **Prioridad:** Must Have (P0) · **Estimación:** 3 SP (5 h) · **Responsable TDD:** Andrés Gaibor (`tests/metrics/pinch.test.ts`)
- **Requisitos trazados:** `RF-015`, `RF-021`, `RF-022`, `RC-04`, `RNF-005`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Cálculo válido y almacenamiento en Dexie
    Dado que se capturaron frames válidos de "HandLandmarker" con al menos 2 ciclos completos (umbral cerrado < 0.25 y abierto > 0.35)
    Cuando el Web Worker procesa la petición "{ kind: 'pinch', frames }"
    Entonces devuelve "cycles" (ciclos), "amplitude" (u.n.) y "rhythmVariation" (proporción)
    Y se guardan en la tabla "measurements" de Dexie con estado "ok".

  Escenario 2: Mano ausente o menos de 2 ciclos -> Resultado "No evaluable"
    Dado que durante la ventana de captura la mano permaneció fuera del encuadre o no completó al menos 2 ciclos
    Cuando el Web Worker evalúa la serie de frames
    Entonces "pinchMetrics" retorna "null"
    Y la interfaz muestra "No evaluable" (nunca "0 ciclos") y ofrece el botón "Repetir actividad".
  ```

---

### ÉPICA 04: Minijuego 2 — "Sostén la bandeja" (Estabilidad Postural y Oscilación)

#### `HU-007` — Captura Guiada de Estabilidad de Manos y Brazos en "Sostén la bandeja"
- **Declaración:** *Como* participante, *quiero* seguir una guía visual clara mientras mantengo mis manos y brazos visibles frente a la cámara durante el tiempo indicado, *para* completar la actividad de estabilidad postural sin confusiones.
- **Prioridad:** Must Have (P0) · **Estimación:** 5 SP (8 h) · **Responsable TDD:** Andrés Gaibor (`BandejaScreen.test.tsx`)
- **Mockups vinculados:** Pliego 1 — Pantalla 5 · Pliego 2 — Pantalla 18 (*En reposo*) y Pantalla 19 (*Instrucciones Sostén la bandeja*).
- **Requisitos trazados:** `RF-012`, `RF-016`, `RF-020`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Seguimiento postural con encuadre válido
    Dado que el participante visualiza las instrucciones de "Sostén la bandeja" con el dispositivo apoyado
    Cuando inicia la captura y mantiene las extremidades superiores dentro del marco durante la cuenta regresiva
    Entonces el sistema registra la serie temporal de coordenadas normalizadas (x, y) con sus marcas de tiempo en milisegundos.
  ```

#### `HU-008` — Estimación de Desplazamiento Postural y Frecuencia Oscilatoria Dominante (FFT)
- **Declaración:** *Como* investigador/operador, *quiero* que el sistema calcule la dispersión postural (`rms`) y la frecuencia oscilatoria dominante en hercios (`Hz`) contrastada con señales sintéticas de 3, 5 y 8 Hz, *para* cuantificar oscilaciones posturales con un error $\le 0.5\text{ Hz}$.
- **Prioridad:** Must Have (P0) · **Estimación:** 5 SP (8 h) · **Responsable TDD:** Andrés Gaibor (`tests/metrics/stability.test.ts`)
- **Requisitos trazados:** `RF-016`, `RF-021`, `RF-022`, `RNF-006`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Estimación espectral exacta sobre señal conocida
    Dado un conjunto de frames sintéticos o reales con oscilación sinusoidal de 5.0 Hz
    Cuando se ejecuta "stabilityMetrics" a través del Web Worker
    Entonces la frecuencia dominante estimada se sitúa en el rango [4.5 Hz, 5.5 Hz] (error <= 0.5 Hz)
    Y las métricas "bandeja.rms", "bandeja.dominantFrequency" y "bandeja.duration" se persisten en Dexie.
  ```

---

### ÉPICA 05: Minijuego 3 — "Sonríe y habla" (Simetría Facial y Emisión Vocal)

#### `HU-009` — Medición de Simetría Geométrica Facial al Sonreír
- **Declaración:** *Como* participante, *quiero* posicionar mi rostro dentro de un óvalo guía en pantalla y sonreír durante 5 segundos, *para* que la aplicación compare geométricamente la posición relativa de ambos lados de mi boca sin guardar ninguna fotografía ni video.
- **Prioridad:** Must Have (P0) · **Estimación:** 5 SP (8 h) · **Responsable TDD:** Andrés Gaibor (`tests/metrics/facial.test.ts`, `SonrisaScreen.test.tsx`)
- **Mockups vinculados:** Pliego 1 — Pantalla 6 · Pliego 3 — Pantalla 22.
- **Requisitos trazados:** `RF-012`, `RF-017`, `RC-03`, `RC-04`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Rostro encuadrado y cálculo de razón de simetría
    Dado que "FaceLandmarker" detecta los 478 puntos del rostro dentro del marco guía
    Cuando el participante mantiene la sonrisa durante la ventana de 5 segundos
    Entonces el sistema calcula el índice de relación geométrica entre las comisuras labiales izquierda y derecha respecto al eje medio facial
    Y descarta inmediatamente los cuadros de video de la memoria RAM.

  Escenario 2: Rostro ausente o fuera del óvalo guía
    Dado que el participante gira la cabeza o sale del encuadre durante la captura facial
    Cuando finaliza la ventana de captura
    Entonces se activa el flag "face-absent", el resultado se marca como "No evaluable" y se ofrece repetir el paso.
  ```

#### `HU-010` — Captura de Vocal Sostenida `"aaa"` (Duración e Intensidad Relativa en dBFS)
- **Declaración:** *Como* participante, *quiero* sostener la vocal `"aaa"` frente al micrófono viendo una onda gráfica que confirme que el dispositivo me escucha, *para* registrar la duración en segundos y la intensidad relativa de mi voz sin enviar audio a internet.
- **Prioridad:** Must Have (P0) · **Estimación:** 5 SP (7 h) · **Responsable TDD:** Andrés Gaibor (`src/audio/vowel-capture.test.ts`)
- **Mockups vinculados:** Pliego 2 — Pantalla 20 (*"Sonríe y habla · Mantén la vocal 'aaa'"*).
- **Requisitos trazados:** `RF-014`, `RF-017`, `RC-01`, `RC-03`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Emisión vocal sostenida por encima del umbral de ruido
    Dado que el participante pasa a la fase de voz de "Sonríe y habla" con micrófono activo
    Cuando emite la vocal "aaa" de forma continua y el nivel supera el umbral mínimo de señal
    Entonces la interfaz anima el indicador de onda sonora y el temporizador "00:05"
    Y al terminar guarda en Dexie "sonrisa.vowelDuration" (en "s") y "sonrisa.vowelIntensity" (en "dBFS") sin almacenar audio.
  ```

---

### ÉPICA 06: Minijuego 4 — "El vuelo del cóndor" (Memoria Espacial en Mapa 2D)

#### `HU-011` — Fase de Observación y Memorización de 3 Ubicaciones en el Mapa Andino 2D
- **Declaración:** *Como* participante, *quiero* observar un mapa 2D claro con el punto de inicio del cóndor y las mesetas numeradas `1`, `2` y `3` antes de iniciar el vuelo, *para* memorizar su ubicación y orden de visita.
- **Prioridad:** Must Have (P0) · **Estimación:** 3 SP (5 h) · **Responsable TDD:** Alexander Villalva (`CondorScreen.test.tsx`)
- **Mockups vinculados:** Pliego 1 — Pantalla 7 (*"Recuerda el camino"*) · Pliego 3 — Pantalla 23 (*Tutorial del cóndor*).
- **Requisitos trazados:** `RF-018`, `RNF-011`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Presentación del mapa con las 3 mesetas numeradas
    Dado que el participante ingresa al minijuego "El vuelo del cóndor"
    Cuando se muestra la fase de observación del mapa 2D
    Entonces son visibles el punto "Inicio" y los marcadores "1", "2" y "3" en coordenadas deterministas
    Y al agotarse el tiempo de observación o pulsar "Ya lo recuerdo", los marcadores numéricos se ocultan para iniciar el recorrido.
  ```

#### `HU-012` — Navegación 2D del Cóndor y Cálculo de Aciertos, Tiempo y Longitud de Ruta
- **Declaración:** *Como* participante, *quiero* guiar al cóndor por el mapa 2D usando botones grandes en pantalla o las teclas de dirección para visitar las ubicaciones 1, 2 y 3 en orden, *para* que el sistema registre mis aciertos, el tiempo empleado y la longitud total de mi recorrido.
- **Prioridad:** Must Have (P0) · **Estimación:** 5 SP (8 h) · **Responsable TDD:** Alexander Villalva (`tests/metrics/path.test.ts`, `CondorScreen.test.tsx`)
- **Mockups vinculados:** Pliego 3 — Pantalla 24 (*"El vuelo del cóndor (en juego)"*).
- **Requisitos trazados:** `RF-018`, `RF-021`, `RF-022`, `RNF-007`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Recorrido completo alcanzando las 3 ubicaciones en orden
    Dado que los marcadores se han ocultado y el indicador superior muestra "Destino: meseta 1"
    Cuando el usuario desplaza al cóndor entrando secuencialmente en el radio de las ubicaciones 1, 2 y 3
    Entonces el indicador de progreso marca "1 ✓ -> 2 ✓ -> 3 ✓"
    Y el Web Worker calcula "hits: 3", "duration" (s) y "length" (px), coincidiendo al 100 % con los guiones de prueba.
  ```

---

### ÉPICA 07: Minijuego 5 — "Semáforo" (Atención Sostenida e Inhibición Go/No-Go)

#### `HU-013` — Presentación Accesible de Estímulos Go/No-Go (Color + Forma + Símbolo)
- **Declaración:** *Como* participante (incluyendo personas con daltonismo rojo-verde), *quiero* que los estímulos de acción ("Go") y de espera ("No-Go") se diferencien simultáneamente por color, forma geométrica y símbolo interno, *para* responder con un botón grande o barra espaciadora sin errores de percepción visual.
- **Prioridad:** Must Have (P0) · **Estimación:** 5 SP (7 h) · **Responsable TDD:** Alexander Villalva (`SemaforoScreen.test.tsx`)
- **Mockups vinculados:** Pliego 1 — Pantalla 8 · Pliego 3 — Pantalla 26 (*Tutorial del semáforo*) y Pantalla 27 (*Semáforo en juego*).
- **Requisitos trazados:** `RF-019`, `RNF-011`, `RNF-012`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Redundancia visual en ensayos Go y No-Go
    Dado que el participante inicia el minijuego "Semáforo"
    Cuando aparece un estímulo de acción ("go")
    Entonces se renderiza como un Círculo Verde con un símbolo de afirmación ("✓" / "+") e instrucción "Toca en verde"
    Y cuando aparece un estímulo de inhibición ("nogo") se renderiza como un Octágono/Forma distinta Roja con mano de alto ("✋" / "-") e instrucción "Espera".
  ```

#### `HU-014` — Medición de Latencia Mediana, Omisiones y Respuestas Incorrectas en "Semáforo"
- **Declaración:** *Como* operador evaluador, *quiero* que el sistema registre con alta precisión temporal (`performance.now()`) cada ensayo Go y No-Go y calcule la latencia mediana (`ms`), las omisiones y las falsas alarmas en el Web Worker, *para* obtener métricas reproducibles al 100 % frente a secuencias automatizadas.
- **Prioridad:** Must Have (P0) · **Estimación:** 3 SP (5 h) · **Responsable TDD:** Alexander Villalva (`tests/metrics/reaction.test.ts`)
- **Requisitos trazados:** `RF-019`, `RF-021`, `RF-022`, `RNF-007`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Evaluación exacta de secuencia de ensayos Go/No-Go
    Dado un conjunto de ensayos registrados con tiempos de estímulo y tiempos de respuesta
    Cuando el Web Worker ejecuta "{ kind: 'reaction', trials }"
    Entonces calcula exactamente la mediana de latencias de aciertos Go ("medianLatency" en ms), el conteo de "omissions" y el conteo de "falseAlarms"
    Y persiste las tres filas en la tabla "measurements" de Dexie.
  ```

---

### ÉPICA 08: Informe Determinista por Plantillas, Exportación Local y QA (Estación Omar)

#### `HU-015` — Generación de Informe de Sesión mediante Plantillas Deterministas con Unidades
- **Declaración:** *Como* participante y operador, *quiero* ver al finalizar los cinco minijuegos un informe claro en español generado mediante plantillas deterministas que muestre los valores medidos con sus unidades físicas y explique qué midió cada tarea sin emplear lenguaje clínico ni semáforos diagnósticos, *para* comprender los resultados dentro de los límites de un prototipo experimental.
- **Prioridad:** Must Have (P0) · **Estimación:** 5 SP (8 h) · **Responsable TDD:** **Omar Figueroa (Estación Omar — `report-templates.test.ts`, `ReportScreen.test.tsx`)**
- **Mockups vinculados:** Pliego 1 — Pantalla 9 (*"Tu perfil / Ver informe"*, adaptado a Regla RC-01).
- **Requisitos trazados:** `RF-023`, `RC-01`, `RC-04`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Presentación de informe con capturas válidas
    Dado que la sesión pasa al estado "report" y existen mediciones con estado "ok" en Dexie
    Cuando se monta "ReportScreen"
    Entonces el sistema consulta "listMeasurements(sessionId)" y renderiza una tarjeta por cada uno de los 5 minijuegos con sus valores numéricos, unidades ("ciclos", "Hz", "s", "dBFS", "px", "ms") y explicación descriptiva
    Y muestra de forma visible el aviso de que Yuyay es un prototipo experimental y no sustituye una evaluación médica.
  ```

#### `HU-016` — Visualización de Causa Técnica y Repetición de Actividades "No Evaluables" en el Informe
- **Declaración:** *Como* operador de sesión, *quiero* que cualquier actividad cuya captura haya resultado insuficiente aparezca en el informe explícitamente como `"No evaluable"` indicando la causa técnica detectada y un botón para repetirla, *para* evitar que un fallo de cámara o iluminación se confunda con un puntaje de cero.
- **Prioridad:** Must Have (P0) · **Estimación:** 3 SP (5 h) · **Responsable TDD:** **Omar Figueroa (Estación Omar — `report-templates.test.ts`)**
- **Requisitos trazados:** `RF-020`, `RF-024`, `RC-04`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Actividad marcada como "No evaluable" en el informe
    Dado que el minijuego "bandeja" se guardó con "status: 'not-evaluable'", "value: null" y "qualityFlags: ['hand-absent']"
    Cuando se genera el texto de la plantilla en el informe
    Entonces la tarjeta de "Sostén la bandeja" muestra el estado "No evaluable" y el motivo técnico correspondiente
    Y no muestra ningún valor numérico "0", ofreciendo una acción para repetir la actividad.
  ```

#### `HU-017` — Exportación Local de Resultados (Descarga JSON y Vista Imprimible/PDF)
- **Declaración:** *Como* operador o participante, *quiero* guardar el informe en mi dispositivo como un archivo JSON auditable o imprimirlo/guardarlo como PDF sin conexión a internet, *para* conservar un registro trazable de la sesión.
- **Prioridad:** Must Have (P1) · **Estimación:** 5 SP (7 h) · **Responsable TDD:** **Omar Figueroa (Estación Omar — `export-report.test.ts`)**
- **Mockups vinculados:** Pliego 1 — Pantalla 9 (Botón *"Guardar en el dispositivo"*).
- **Requisitos trazados:** `RF-025`, `RF-026`, `RC-02`, `RC-03`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Exportación de archivo JSON con red desconectada
    Dado que el usuario visualiza el informe final con el Wi-Fi apagado
    Cuando pulsa el botón "Guardar en el dispositivo (JSON)"
    Entonces el navegador descarga un archivo "yuyay-sesion-<id>.json" con los metadatos de sesión, calidad de dispositivo y métricas derivadas
    Y el archivo exportado no contiene ningún campo de audio, video ni coordenadas crudas.

  Escenario 2: Impresión / Guardado como PDF
    Dado que el usuario se encuentra en la pantalla de informe
    Cuando pulsa "Imprimir / Guardar PDF"
    Entonces se aplica la hoja de estilos "@media print" ocultando controles de navegación y presentando el informe completo listo para impresión.
  ```

#### `HU-018` — Borrado Local de Datos de Sesión (Derecho de Supresión del Participante)
- **Declaración:** *Como* participante, *quiero* poder borrar mis resultados almacenados en el navegador en cualquier momento con un solo botón, *para* ejercer el control total sobre mis datos prometido en la pantalla de consentimiento.
- **Prioridad:** Should Have (P1) · **Estimación:** 2 SP (3 h) · **Responsable TDD:** **Omar Figueroa (Estación Omar — `export-report.test.ts`)**
- **Mockups vinculados:** Pliego 1 — Pantalla 2 (*"Puedes borrar tus resultados en cualquier momento"*).
- **Requisitos trazados:** `RF-027`, `RC-03`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Eliminación completa de registros de la sesión actual
    Dado que el participante ha finalizado o detenido una sesión
    Cuando pulsa "Borrar mis resultados" y confirma la acción
    Entonces se eliminan las filas asociadas a "sessionId" en las tablas "measurements", "deviceChecks" y "sessions" de Dexie
    Y se limpia "yuyay.sessionId" de localStorage, regresando a la pantalla inicial de consentimiento.
  ```

#### `HU-019` — Ejecución Completa en Modo 100 % Offline tras Instalación
- **Declaración:** *Como* operador en trabajo de campo o en el Auditorio de la Gran Final, *quiero* ejecutar toda la sesión de Yuyay con el Wi-Fi apagado tras haber instalado los recursos inicialmente, *para* demostrar que el procesamiento es 100 % local y autónomo.
- **Prioridad:** Must Have (P0) · **Estimación:** 3 SP (4 h) · **Responsable TDD:** **Omar Figueroa + Andrés Gaibor**
- **Requisitos trazados:** `RC-02`, `RNF-010`.
- **Especificación para Test TDD (Gherkin):**
  ```gherkin
  Escenario 1: Recarga y sesión completa sin red
    Dado que la PWA ha sido instalada y la caché "yuyay-models-v1" tiene estado "ready"
    Cuando se desconecta la red del portátil y se recarga la aplicación
    Entonces el Service Worker sirve el shell y los modelos ".task"/".wasm" desde el almacenamiento local con cero errores de red
    Y los 5 minijuegos y la exportación del informe se ejecutan de principio a fin.
  ```

#### `HU-020` — Certificación de Rendimiento ($\ge 20\text{ FPS}$) en Portátil sin GPU Dedicada (Estación Omar)
- **Declaración:** *Como* equipo de ingeniería, *queremos* medir y documentar la tasa de cuadros por segundo (`FPS`), la resolución de cámara y el tiempo total de sesión en las tres estaciones del equipo (incluyendo el portátil ASUS Core i5-7200U con gráficos integrados Intel HD 620 de la Estación Omar), *para* demostrar con evidencia verificable ante el jurado la viabilidad técnica de Yuyay en portátiles comunes.
- **Prioridad:** Must Have (P1) · **Estimación:** 3 SP (5 h) · **Responsable:** **Omar Figueroa + Andrés Gaibor + Alexander Villalva**
- **Requisitos trazados:** `RC-07`, `RNF-001`, `RNF-002`.
- **Especificación para Test de Sistema (Gherkin):**
  ```gherkin
  Escenario 1: Registro auditable de FPS y tiempos en los equipos declarados
    Dado que se ejecuta la sesión completa en el MacBook Air M1, el PC Ryzen 5 9600X y el portátil ASUS Core i5-7200U
    Cuando se completan las pruebas de dispositivos y los minijuegos visuales
    Entonces se registran los FPS medios observados (>= 20 FPS en el módulo activo) y una duración total <= 6 minutos en el informe de validación técnica del repositorio.
  ```
