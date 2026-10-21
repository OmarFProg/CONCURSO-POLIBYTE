# Yuyay: tamizaje neurológico con minijuegos

Oct 20, 2026 · @Andres Gaibor

## Resumen ejecutivo

Yuyay es una aplicación de tamizaje neurológico: una batería de cinco minijuegos que, en unos 5 minutos y solo con la cámara, el micrófono y la pantalla de un portátil, detecta señales tempranas asociadas a párkinson, accidente cerebrovascular (ACV), deterioro cognitivo y alteraciones de la atención.

El nombre viene del kichwa *yuyay*, que significa pensamiento o memoria. Todo el procesamiento ocurre en el equipo del usuario, sin internet, y los datos de salud nunca salen de él.

El proyecto se presenta al Polibyte Innovation Challenge 2026 en la categoría Neurociencias, pero cubre también las otras dos categorías del concurso:

- **Neurociencias:** cada minijuego mide una señal con fundamento clínico (movimiento, temblor, rostro, voz, memoria espacial, atención).
- **Visión por computador:** seguimiento de manos, rostro y ojos con MediaPipe desde la cámara.
- **Inteligencia artificial local:** un clasificador de voz y un modelo de lenguaje pequeño que redacta los informes, ambos ejecutados sin conexión.

Yuyay es una herramienta de tamizaje: genera alertas y recomienda acudir a un médico. No diagnostica enfermedades.

## Alineación con las bases oficiales

Yuyay cumple las bases si se declara **Neurociencias como categoría principal**, se reduce el alcance a un núcleo que funcione impecable y se demuestra con pruebas medibles el aporte de cada componente, en especial del modelo de lenguaje.

**Por qué encaja.** Las bases piden explicar la relación concreta con las neurociencias y aclaran que usar una red neuronal no basta. Yuyay la tiene: cada juego mide un proceso del sistema nervioso (control motor, temblor, expresión facial, habla, orientación espacial, atención e inhibición), con respaldo clínico. La visión por computador y la IA local se presentan como el medio, no como la categoría.

| Criterio de la Gran Final | Puntos | Cómo lo cubre Yuyay |
| --- | --- | --- |
| Innovación y solución al problema | 30 | Batería multidominio con solo una cámara, sin internet y en español, frente a escalas clínicas que se califican a ojo o apps que exigen sensores. Usuarios definidos: médicos y promotores de atención primaria y adultos mayores |
| Funcionalidad y madurez del MVP | 25 | Flujo completo de principio a fin, caso representativo, caso de error (poca luz, mano fuera de cuadro) y límites comunicados en pantalla |
| Arquitectura y calidad técnica | 20 | Módulos separados por juego, pruebas automatizadas de las métricas, README reproducible, modelos con nombre y versión |
| Pitch y demostración | 15 | Guion de 5 minutos de pitch y 7 de demo, ensayado y cronometrado |
| Experiencia de usuario | 10 | Diseño adaptable a distintas pantallas, botones grandes, instrucciones habladas, contraste alto para adultos mayores |

**Ajustes que exigen las bases:**

- **Alcance del MVP.** Núcleo comprometido: la pinza, sostén la bandeja y sonríe y habla, más el perfil. Semáforo y cóndor quedan como extensiones si sobra tiempo. Es mejor comprometer tres funciones y cumplirlas que prometer cinco y fallar en vivo.
- **El modelo de lenguaje debe ganarse su lugar.** Las bases valoran poco añadir modelos o chatbots sin demostrar qué mejoran. Se compara el informe del modelo contra un informe de plantilla con usuarios reales; si no mejora la comprensión, se usa la plantilla. Al ser Neurociencias la categoría principal, no es obligatorio.
- **Validación con metas numéricas** (ver la tabla de criterios en Datos y validación).
- **Datos simulados declarados.** El temblor y la sonrisa asimétrica de la demo se presentan expresamente como simulaciones.
- **Originalidad.** Todo el código nace después del 15 de octubre en un repositorio nuevo de GitHub, con commits frecuentes. No se reutiliza código de asignaturas ni de trabajos de titulación.
- **Dominio de todos.** En la defensa y en la final cualquier integrante puede ser preguntado sobre cualquier módulo, el código y la base de datos.
- **Equipo interdisciplinario.** Las bases invitan a sumar estudiantes de Salud o Psicología. Un estudiante de Medicina, Fisioterapia o Psicología en el equipo fortalece la comprensión del problema y la validación.

## Contexto del concurso

El Polibyte Innovation Challenge 2026 tiene dos fases: una propuesta escrita con defensa virtual que filtra a los clasificados, y el desarrollo de un MVP que se presenta en una Gran Final presencial; el primer lugar gana una laptop y certificados.

| Fecha | Hito |
| --- | --- |
| Miércoles 21 de octubre, 23:59 | Inscripción del equipo (paso 1); se recibe el código de equipo |
| Domingo 25 de octubre | Inicio del desarrollo para todos los inscritos |
| Domingo 25 de octubre, 23:59 | Entrega de la propuesta en PDF, hasta 4 páginas, con la plantilla oficial en Word (paso 2) |
| Viernes 30 de octubre | Defensa virtual de 8 minutos (5 de exposición y 3 de preguntas) y publicación de clasificados |
| Miércoles 25 de noviembre | Congelamiento de código en GitHub; se registra el repositorio y las instrucciones de ejecución |
| Jueves 26 de noviembre | Pruebas de proyección y audio en el auditorio |
| Viernes 27 de noviembre | Gran Final: pitch de 5 minutos, demo en vivo de 7 y 5 de preguntas sobre código, base de datos y arquitectura |

**Propuesta escrita (75 puntos) y defensa (25 puntos).** Se necesita al menos 70 de 100 para clasificar. Los cuatro criterios valen 25 puntos cada uno: comprensión del problema, innovación y valor diferencial, arquitectura y viabilidad, y defensa virtual. La propuesta debe tener cuatro apartados: datos del proyecto y del equipo con la justificación de la categoría; problema y propuesta de valor; innovación y validación; arquitectura y alcance del MVP.

**Gran Final (100 puntos, todos parten de cero).** Innovación y solución 30, funcionalidad y madurez 25, arquitectura y calidad técnica 20, pitch y demo 15, experiencia de usuario 10. No se aceptan demostraciones basadas solo en diapositivas.

Equipos de 2 a 4 estudiantes de cualquier carrera e institución; cada estudiante en un solo equipo. Los gastos de traslado a Riobamba corren por cuenta de los equipos. Contacto: club.polibyte@espoch.edu.ec.

## Problema

En Ecuador las enfermedades neurológicas se detectan tarde porque hay muy pocos neurólogos y están concentrados en las ciudades grandes.

- Hasta noviembre de 2022, el sistema de salud público ecuatoriano contaba con 94 neurólogos, una tasa de 0,54 por cada 100 000 habitantes, frente al mínimo de 1 por cada 100 000 que recomienda la OMS ([Revista Ecuatoriana de Neurología](https://revecuatneurol.temp.publicknowledgeproject.org/index.php/revecuatneurol/article/download/9713/7959/30906)).
- Ocho provincias no tenían ningún neurólogo en el sistema público y cinco tenían solo uno (misma fuente).
- Enfermedades como el párkinson o el alzhéimer muestran señales sutiles años antes del diagnóstico. En el alzhéimer, por ejemplo, la pérdida de orientación espacial puede aparecer muchos años antes que los problemas de memoria ([Healio, sobre el estudio Sea Hero Quest](https://www.healio.com/psychiatry/alzheimers-disease-dementia/news/online/%7B00982760-977e-4059-8f17-09f85874ce64%7D/sea-hero-quest-gaming-app-can-detect-alzheimers-risk)).
- En un ACV, cada minuto cuenta: reconocer los signos a tiempo decide si la persona llega al hospital dentro de la ventana de tratamiento.

La consecuencia es que muchas personas llegan al especialista cuando la enfermedad ya está avanzada. Falta un primer filtro barato, accesible y rápido que diga "conviene que te vea un médico".

## Solución y principios de diseño

Yuyay convierte pruebas neurológicas conocidas en minijuegos de un minuto que cualquiera puede jugar frente a un portátil, y entrega un perfil con alertas al terminar.

Flujo de uso:

1. La persona abre Yuyay, acepta el consentimiento e ingresa edad y mano dominante (los rangos normales cambian con la edad).
2. Juega los cinco minijuegos en orden, guiada por instrucciones en pantalla y en voz.
3. Al terminar ve su perfil: cinco dimensiones, cada una en verde (dentro de lo esperado), amarillo (limítrofe) o rojo (fuera de lo esperado).
4. Recibe un informe en lenguaje sencillo y, si hay alertas, la recomendación de consultar a un médico, con un informe técnico para llevarle.

Principios que guían todas las decisiones:

- **Tamizaje, no diagnóstico.** Yuyay alerta y orienta; el diagnóstico es del médico.
- **Todo local.** Ningún dato sale del equipo. Funciona sin internet, lo que también permite usarlo en zonas rurales.
- **Hardware común.** Solo cámara, micrófono y pantalla de un portátil normal. Sin sensores ni cascos.
- **Corto y amigable.** Unos 5 minutos en total, con estética de juego para personas mayores: botones grandes, texto claro, instrucciones habladas.
- **Explicable.** Cada alerta dice qué se midió y por qué importa, no solo un número.

## Fundamento neurocientífico

Cada minijuego mide una señal que la clínica ya usa y que se altera de forma conocida en una enfermedad neurológica; Yuyay solo la mide con la cámara en lugar de a ojo.

| Señal | Qué ocurre en el sistema nervioso | Condición asociada | Evidencia |
| --- | --- | --- | --- |
| Lentitud y decremento al golpear los dedos (bradicinesia) | La pérdida de neuronas de dopamina en los ganglios basales vuelve los movimientos repetidos más lentos y cada vez más cortos | Párkinson | La bradicinesia es el rasgo motor que define el párkinson; en una prueba de golpeteo grabada con celular, la menor velocidad y el mayor decremento separaron párkinson temprano de controles con un AUC de 0,88 ([SMART test, medRxiv](https://www.medrxiv.org/content/10.1101/2021.03.24.21254234v1)) |
| Temblor y su frecuencia | El temblor parkinsoniano es lento y aparece en reposo; otros temblores son más rápidos y aparecen al mantener una postura | Párkinson, temblor esencial | El temblor del párkinson ronda los 4 a 6 Hz y el fisiológico aumentado los 6 a 12 Hz, aunque los rangos se superponen ([Zhang et al., 2017](https://pmc.ncbi.nlm.nih.gov/articles/PMC5573102)) |
| Asimetría facial y caída de un brazo | Un ACV interrumpe el flujo de sangre a una zona del cerebro y debilita un lado del cuerpo | ACV | Son los signos F (cara) y A (brazo) de la regla BE-FAST, que agrega equilibrio y ojos a la clásica FAST para reducir los ACV que pasan inadvertidos ([Aroor et al., Stroke 2017](https://pubmed.ncbi.nlm.nih.gov/28082668/)) |
| Voz monótona, baja o arrastrada | El párkinson reduce el control fino de laringe y respiración; un ACV puede afectar el habla de golpe | Párkinson, ACV | Las medidas acústicas de la voz permiten distinguir personas con párkinson de personas sanas ([dataset Oxford de la UCI](https://archive.ics.uci.edu/dataset/174/Parkinson'ss)) |
| Orientación espacial | El alzhéimer afecta temprano las zonas cerebrales que construyen mapas mentales del espacio | Deterioro cognitivo, alzhéimer | En Sea Hero Quest, adultos con alto riesgo genético tomaron rutas menos eficientes aun sin problemas de memoria ([Healio](https://www.healio.com/psychiatry/alzheimers-disease-dementia/news/online/%7B00982760-977e-4059-8f17-09f85874ce64%7D/sea-hero-quest-gaming-app-can-detect-alzheimers-risk)) |
| Tiempo de reacción y control de impulsos | La corteza prefrontal frena respuestas automáticas; su alteración produce reacciones lentas o impulsivas | TDAH, fatiga, deterioro cognitivo | Paradigma clásico go/no-go de la neuropsicología (fuente por confirmar antes de la propuesta) |

Hay un precedente directo para el enfoque de Yuyay: un estudio de 2025 analizó con MediaPipe Hands videos de golpeteo de dedos grabados con celular y detectó bradicinesia; el grupo con párkinson mostró más variación y decremento de amplitud y mayor diferencia entre manos ([Kasemsap et al., PLOS ONE](https://ideas.repec.org/a/plo/pone00/0330373.html)).

## Minijuegos en detalle

La batería tiene cinco minijuegos de unos 40 a 70 segundos cada uno; juntos suman unos 5 minutos con instrucciones incluidas.

| # | Minijuego | Señal | Entrada | Duración aprox. |
| --- | --- | --- | --- | --- |
| 1 | La pinza | Bradicinesia | Cámara (manos) | 50 s |
| 2 | Sostén la bandeja | Temblor y caída de brazo | Cámara (manos y cuerpo) | 60 s |
| 3 | Sonríe y habla | Asimetría facial y voz | Cámara (rostro) y micrófono | 50 s |
| 4 | El vuelo del cóndor | Orientación espacial | Teclado o pantalla | 70 s |
| 5 | Semáforo | Reacción y control de impulsos | Teclado o pantalla | 50 s |

### 1. La pinza

- **Mecánica:** la persona junta y separa el pulgar y el índice lo más rápido y amplio posible durante 10 segundos por mano, frente a la cámara. En pantalla, cada golpe hace crecer una flor; si los golpes se hacen más cortos, la flor se marchita.
- **Base clínica:** es la prueba de golpeteo de dedos del ítem 3.4 de la escala MDS-UPDRS, adaptada a cámara como en el estudio de Kasemsap et al.
- **Métricas:** golpes por segundo, amplitud media (distancia pulgar-índice normalizada por el tamaño de la mano), decremento de amplitud y de frecuencia entre el inicio y el final, variabilidad del ritmo y diferencia entre manos.
- **Tecnología:** MediaPipe Hand Landmarker (21 puntos por mano), puntas del pulgar e índice.

### 2. Sostén la bandeja

- **Mecánica:** primero, 15 segundos con las manos relajadas sobre los muslos (temblor de reposo). Luego, 20 segundos con ambos brazos extendidos al frente, palmas arriba, sosteniendo una bandeja virtual con vasos de agua que se derraman si la mano tiembla o baja; los ojos se cierran los últimos 10 segundos para detectar la caída de un brazo.
- **Base clínica:** temblor de reposo y postural; prueba de deriva del brazo (signo A de BE-FAST).
- **Métricas:** frecuencia dominante del temblor (análisis de Fourier de la posición de muñeca y dedos), amplitud del temblor, caída vertical de cada muñeca y diferencia entre brazos.
- **Tecnología:** MediaPipe Hand Landmarker y Pose Landmarker. A 30 cuadros por segundo se pueden medir frecuencias de hasta 15 Hz, suficiente para el rango de 4 a 12 Hz de interés.

### 3. Sonríe y habla

- **Mecánica:** la persona mira a la cámara, se relaja, sonríe ampliamente y levanta las cejas. Después sostiene la vocal "aaa" unos 5 segundos y lee en voz alta una frase corta en español.
- **Base clínica:** asimetría facial (signo F de BE-FAST), hipomimia o rostro poco expresivo (párkinson), habla arrastrada (signo S de BE-FAST) y voz monótona.
- **Métricas:** índice de asimetría entre comisuras de la boca y entre cejas, amplitud de la sonrisa, frecuencia de parpadeo; en la voz, frecuencia fundamental y su variación, jitter, shimmer y relación armónico-ruido.
- **Tecnología:** MediaPipe Face Landmarker (478 puntos y 52 coeficientes de expresión por rostro); análisis acústico local y el clasificador de voz.

### 4. El vuelo del cóndor

- **Mecánica:** se muestra un mapa de un valle andino con un punto de partida y tres mesetas numeradas. El mapa desaparece y la persona guía al cóndor en una vista desde su espalda, visitando las mesetas en orden. Al final debe señalar hacia dónde quedó el punto de partida.
- **Base clínica:** navegación orientada a metas y sentido de dirección, la misma idea de Sea Hero Quest.
- **Métricas:** eficiencia de la ruta (distancia recorrida frente a la ruta óptima), tiempo de observación del mapa, error angular al señalar el punto de partida.
- **Tecnología:** escena 3D sencilla con Three.js; registro de la trayectoria.

### 5. Semáforo

- **Mecánica:** aparecen luces verdes y rojas en orden aleatorio; hay que presionar ante la verde y no hacer nada ante la roja. La velocidad aumenta poco a poco.
- **Base clínica:** paradigma go/no-go de atención e inhibición de respuestas.
- **Métricas:** tiempo de reacción mediano, variabilidad del tiempo de reacción, errores por presionar en rojo (impulsividad) y por no presionar en verde (atención).
- **Tecnología:** lógica en el navegador con medición de tiempo de alta precisión.

Controles de calidad comunes a todos: antes de cada juego la app revisa la iluminación y que la mano o el rostro estén completos en cuadro; si la detección falla, pide repetir en lugar de registrar un dato malo.

## Perfil de resultados e informes con IA local

Los puntajes y las alertas los calcula código determinista y verificable; el modelo de lenguaje solo redacta, en español sencillo, lo que esos números ya dicen.

**Perfil.** Una gráfica de radar con cinco dimensiones: motricidad fina (pinza), temblor y postura (bandeja), rostro y voz, memoria espacial (cóndor) y atención (semáforo). Cada métrica se convierte en un puntaje de 0 a 100 comparándola con el rango de referencia de su grupo de edad, y cada dimensión se colorea en verde, amarillo o rojo.

**Clasificador de voz.** Un modelo pequeño (por ejemplo, un bosque aleatorio o una regresión logística) entrenado con el dataset de voz de la UCI devuelve una probabilidad de patrón vocal compatible con párkinson. Se exporta a ONNX para ejecutarlo localmente.

**Informes.** El modelo de lenguaje recibe un JSON con los puntajes, las alertas y la explicación de cada métrica, y genera dos textos:

1. **Para la persona:** qué se midió, qué salió bien, qué conviene revisar y si debe consultar a un médico, sin tecnicismos ni alarmismo.
2. **Para el médico:** métricas con sus valores y unidades, comparación con el rango de referencia y calidad de la captura.

Reglas del informe: prohibido nombrar un diagnóstico como hecho ("tienes párkinson"), siempre incluir la aclaración de tamizaje, y ante cualquier alerta de signos de ACV mostrar un mensaje fijo de emergencia (no generado por el modelo) que indique llamar al 911.

**Modelo de lenguaje.** Uno pequeño con buen español, por ejemplo de las familias Qwen 3 (variantes de 1,7B o 4B) o Gemma, que para portátiles de 8 a 16 GB se consideran buenas opciones en 2026 ([Ertas](https://www.ertas.ai/best/best-small-llm-for-local-deployment)). Dos formas de ejecutarlo:

- **WebLLM:** corre el modelo dentro del navegador con WebGPU, con una API al estilo de OpenAI y sin servidor ([WebLLM](https://blog.mlc.ai/2024/06/13/webllm-a-high-performance-in-browser-llm-inference-engine)). La primera carga del modelo puede tardar.
- **Ollama:** un servicio local con un solo comando y una API propia ([guía de herramientas locales](https://cut-the-saas.com/the-cut/best-local-llm-tools)). Más simple de depurar.

**Cómo justificar el modelo de lenguaje ante el jurado.** Las bases dan baja valoración a un chatbot genérico o a un modelo sin demostrar qué mejora. Por eso el modelo no conversa ni decide nada: convierte el JSON de puntajes en un informe adaptado al lector. Su aporte se prueba comparando, con al menos 10 personas, el informe del modelo frente a un informe de plantilla fija, midiendo qué tanto entienden sus resultados. Si no hay mejora, se queda la plantilla y el modelo se presenta como trabajo futuro. Se registran nombre, versión, cuantización, configuración y requisitos del modelo en el repositorio. Decisión pendiente: Ollama o WebLLM, tras medir velocidad en el portátil de la demo.

## Arquitectura técnica y stack

Yuyay es una aplicación web que corre en el navegador del portátil, apoyada por un pequeño servicio local en la misma máquina; nada viaja a internet.

&#91;embedded content: arquitectura de Yuyay · 3 entradas, procesamiento local, 2 salidas\]

Las tres entradas alimentan módulos separados; todo converge en el motor de puntajes, que es la única fuente de los números del perfil y del informe.

| Capa | Tecnología | Función |
| --- | --- | --- |
| Interfaz y juegos | React, TypeScript, Vite; Three.js para el cóndor | Pantallas, instrucciones, minijuegos, perfil |
| Visión por computador | MediaPipe Tasks Vision (`@mediapipe/tasks-vision`): Hand, Pose y Face Landmarker | Puntos de manos, cuerpo y rostro en tiempo real, en el navegador |
| Señales | TypeScript; FFT para el temblor | Métricas de cada juego |
| Voz | Servicio local en Python (FastAPI) con Parselmouth (Praat) | Medidas acústicas iguales a las del dataset |
| Clasificador | scikit-learn, exportado a ONNX | Probabilidad de patrón vocal compatible con párkinson |
| Lenguaje | Qwen 3 o Gemma pequeño, vía Ollama o WebLLM | Informes en español |
| Datos | IndexedDB del navegador o archivo local | Historial de puntajes del usuario |

Para la demo, el servicio local y la app se empaquetan para arrancar con un solo comando (por ejemplo, con Docker o un script), y todos los modelos se descargan antes, para funcionar con el wifi apagado.

## Datos y validación

La credibilidad ante el jurado depende de mostrar con qué datos se entrenó cada parte y cómo se comprobó que funciona.

**Dataset de voz (UCI, Oxford Parkinson's Disease Detection).** Contiene 195 grabaciones de voz de 31 personas, 23 de ellas con párkinson, con unas seis grabaciones por persona y una columna que marca sano o párkinson. Tiene licencia CC BY 4.0 y se descarga en Python con `ucimlrepo` ([UCI](https://archive.ics.uci.edu/dataset/174/Parkinson'ss)). Las columnas son medidas acústicas (frecuencia fundamental, jitter, shimmer, relación ruido-armónicos), así que Yuyay debe calcular esas mismas medidas a partir del micrófono.

Cuidado metodológico clave: como hay varias grabaciones por persona, la división entre entrenamiento y prueba debe hacerse **por persona**, no por grabación. Si no, el modelo memoriza voces y el resultado sale inflado. Mostrar esto en la final demuestra rigor.

Otra limitación: son voces en inglés grabadas con equipo de laboratorio. Se puede explorar un corpus en español con pacientes con párkinson (por ejemplo PC-GITA, de acceso bajo solicitud) si el tiempo lo permite.

**Rangos de referencia para los minijuegos.** No existe un dataset público listo para estos juegos, así que el plan es:

1. Tomar de la literatura los valores conocidos (por ejemplo, frecuencias de temblor y patrones de decremento en el golpeteo).
2. Recolectar una línea base con al menos 30 voluntarios sanos (compañeros, familiares de distintas edades), con consentimiento informado y datos anónimos.
3. Definir los umbrales verde, amarillo y rojo como percentiles de esa línea base, ajustados por edad.

**Validación técnica.**

- **Exactitud de la medición:** comparar la frecuencia de temblor medida por la cámara con un temblor simulado de frecuencia conocida (por ejemplo, la mano siguiendo un metrónomo a 5 Hz).
- **Repetibilidad:** la misma persona juega dos veces el mismo día y los puntajes deben ser parecidos.
- **Robustez:** probar con distinta iluminación, tonos de piel, lentes y fondos.
- **Clasificador de voz:** reportar sensibilidad, especificidad y AUC con validación cruzada agrupada por persona.

**Criterios medibles comprometidos** (las bases piden casos de prueba y metas verificables; las metas son propuestas del equipo y se reportan con honestidad aunque no se alcancen):

| Criterio | Cómo se mide | Meta |
| --- | --- | --- |
| Exactitud de la frecuencia de temblor | Error absoluto frente a una referencia conocida: video sintético de una mano oscilando a 3, 5 y 8 Hz, declarado como simulación | Error de 0,5 Hz o menos |
| Repetibilidad de los puntajes | Misma persona, dos sesiones el mismo día; coeficiente de correlación intraclase | 0,75 o más |
| Clasificador de voz | AUC, sensibilidad y especificidad con validación cruzada agrupada por persona | Reportar el valor obtenido; AUC de 0,80 como meta |
| Capturas válidas | Porcentaje de juegos que no necesitan repetirse, con iluminación normal | 90 % o más |
| Detección de condiciones difíciles | Poca luz, mano fuera de cuadro o rostro tapado: la app debe avisar y pedir repetir | 100 % de los casos de prueba avisados |
| Rendimiento local | Cuadros por segundo de MediaPipe y uso de memoria en el portátil de la demo | 20 cuadros por segundo o más |
| Duración del núcleo | Tiempo desde el inicio hasta el perfil con los tres juegos comprometidos | 4 minutos o menos |
| Usabilidad | Cuestionario SUS con al menos 10 personas, incluidos adultos mayores | 70 puntos o más |
| Fidelidad del informe (si se usa el modelo de lenguaje) | Revisión automática de 50 informes: que no contradigan los puntajes ni nombren un diagnóstico | 0 errores |

Cada criterio tiene su script o caso de prueba en el repositorio, para que el jurado pueda reproducirlo.

## Ética, privacidad y límites

Yuyay trata datos de salud, así que la privacidad y la honestidad sobre sus límites son parte del diseño, no un añadido.

- **Privacidad por diseño.** El video y el audio se procesan en memoria y no se guardan; solo se conservan los puntajes, en el propio equipo y bajo control del usuario, que puede borrarlos. No hay servidores ni cuentas. Esto se alinea con la Ley Orgánica de Protección de Datos Personales del Ecuador, que trata los datos de salud como sensibles.
- **Consentimiento.** Antes de empezar, una pantalla explica qué se mide, qué se guarda y que no es un diagnóstico. Para menores de edad no se usa.
- **No es un dispositivo médico.** Es un prototipo de tamizaje y educación. Para uso clínico real necesitaría validación con pacientes y registro sanitario (ARCSA en Ecuador).
- **Falsos positivos y negativos.** Una alerta puede asustar sin motivo y un resultado normal puede dar falsa tranquilidad. Por eso los mensajes son prudentes y siempre invitan a consultar si hay síntomas, salga lo que salga.
- **Sesgos.** La detección con cámara puede funcionar peor con poca luz o ciertos tonos de piel, y el dataset de voz es pequeño y en inglés. Se reportan estas limitaciones abiertamente.
- **Voluntarios de la línea base.** Datos anónimos, consentimiento firmado y posibilidad de retirarse.

## Plan de trabajo y roles

El código se congela el 25 de noviembre, así que el núcleo (pinza, bandeja, sonríe y habla, perfil) debe estar terminado y probado el 22; semáforo y cóndor solo entran si el núcleo ya está estable. Hay feriados el 2, 3 y 11 de noviembre.

Roles sugeridos para un equipo de 3 o 4 personas:

- **Visión por computador:** MediaPipe, extracción de métricas de manos, cuerpo y rostro, análisis de temblor.
- **Juegos e interfaz:** React, Three.js para el cóndor, diseño visual y accesibilidad.
- **IA y datos:** análisis de voz, clasificador, modelo de lenguaje local, puntajes y rangos de referencia.
- **Producto y pitch:** propuesta técnica, voluntarios de la línea base, validación, presentación (puede combinarse con otro rol).

| Fechas | Entregable | Responsable |
| --- | --- | --- |
| 21 oct | Inscripción del equipo y código de equipo | Líder |
| 22 al 25 oct | Propuesta de 4 páginas en la plantilla oficial; prueba rápida de MediaPipe en el portátil; repositorio nuevo en GitHub | Producto, todos |
| 25 al 29 oct | Esqueleto de la app y primer juego (pinza); ensayo de la defensa, con cada integrante explicando su módulo | Todos |
| 30 oct | Defensa virtual y clasificados | Todos |
| 31 oct al 8 nov | Bandeja (temblor y deriva) y sonríe y habla; clasificador de voz entrenado y validado | Visión, IA |
| 9 al 15 nov | Perfil y motor de puntajes; recolección de la línea base con voluntarios | IA, Producto |
| 16 al 22 nov | Pruebas de la tabla de criterios, diseño adaptable, informes; semáforo si hay tiempo | Todos |
| 23 al 25 nov | README reproducible, documentación de modelos y pruebas; congelamiento de código el 25 | Todos |
| 26 nov | Prueba técnica en el auditorio | Todos |
| 27 nov | Gran Final | Todos |

## Demo de la final y pitch

La demo gana si un miembro del jurado juega en vivo y ve su propio perfil en menos de 5 minutos, sin internet.

Las bases fijan 5 minutos de pitch, 7 de demostración en vivo y 5 de preguntas sobre código, base de datos y arquitectura. La demo debe mostrar un caso representativo y un caso de error, contrastar resultados con los criterios declarados y explicar los límites.

**Pitch (5 minutos):**

1. **Gancho (30 s):** Ecuador tiene unos 0,5 neurólogos públicos por cada 100 000 habitantes y ocho provincias sin ninguno. ¿Y si el primer filtro cupiera en cualquier laptop?
2. **Problema y usuarios (1 min):** detección tardía; médicos y promotores de atención primaria como operadores, adultos mayores como beneficiarios.
3. **Propuesta de valor y diferencia (1,5 min):** frente a escalas que se califican a ojo y apps que requieren sensores, Yuyay mide varias señales con una cámara, sin internet y en español.
4. **Innovación con evidencia (1,5 min):** fundamento clínico de cada juego y precedentes científicos.
5. **Escalabilidad (30 s):** centros de salud rurales, próxima validación clínica con especialistas.

**Demostración en vivo (7 minutos):**

1. **Prueba de que todo es local (30 s):** apagar el wifi frente al jurado.
2. **Caso representativo (3 min):** un miembro del jurado juega el núcleo; en pantalla grande se ven los puntos de MediaPipe y al final su perfil.
3. **Caso de error (1 min):** apagar la lámpara o sacar la mano del cuadro; la app avisa y pide repetir en lugar de dar un resultado falso.
4. **Caso con alerta (1 min):** un integrante simula temblor y una sonrisa asimétrica, anunciado expresamente como simulación; aparecen las alertas.
5. **Evidencia (1,5 min):** tabla de criterios con meta y resultado obtenido, y límites conocidos.

Preparación de la demo:

- Llevar iluminación propia (una lámpara de aro) porque la luz del auditorio es desconocida.
- Tener un video grabado de la demo completa como plan B.
- Precargar el modelo de lenguaje antes de subir al escenario.
- Ensayar con personas que nunca vieron la app para pulir las instrucciones.

Preguntas probables del jurado y respuesta corta:

- *¿Diagnostica párkinson?* No, alerta y orienta; el diagnóstico es del médico.
- *¿Qué tan preciso es?* Cifras del clasificador de voz con validación por persona y repetibilidad de los juegos; con honestidad sobre lo que falta validar.
- *¿Por qué local?* Privacidad de datos de salud y uso en zonas sin internet.
- *¿Qué sigue?* Validación con pacientes reales junto a especialistas.

## Riesgos y mitigación

El mayor riesgo es querer hacer cinco juegos perfectos en cinco semanas; la prioridad es que tres funcionen impecables en vivo.

| Riesgo | Impacto | Mitigación |
| --- | --- | --- |
| No alcanzar el núcleo comprometido | Alto | Comprometer solo pinza, bandeja y sonríe y habla; semáforo y cóndor como extensiones |
| No clasificar en la Fase 1 (menos de 70 puntos) | Alto | Propuesta centrada en el problema y la validación medible; ensayar la defensa de 5 minutos varias veces |
| Un integrante no domina un módulo en las preguntas | Alto | Causa de descalificación si el código se hizo con asistentes sin dominarlo. Revisiones de código cruzadas cada semana y una sesión donde cada uno explica los módulos ajenos |
| Dudas sobre originalidad | Alto | Repositorio nuevo desde el inicio, commits pequeños y frecuentes, librerías y modelos de terceros declarados en el README |
| Luz mala en el auditorio rompe la detección | Alto | Lámpara de aro propia, prueba del 26 de noviembre, control de calidad de cuadro |
| El modelo de lenguaje no demuestra aporte | Medio | Comparación con plantilla; si no mejora, se usa la plantilla |
| Medidas de voz del micrófono distintas a las del dataset | Medio | Calcularlas con Praat vía Parselmouth, igual que el dataset, y calibrar con grabaciones propias |
| Pocos voluntarios para la línea base | Medio | Empezar en la segunda semana de noviembre con apoyo del club y la asociación |
| Jurado cuestiona la validez clínica | Medio | Presentarlo como tamizaje, citar estudios, mostrar la tabla de criterios y los límites |

## Referencias

- [Número de neurólogos y programas de entrenamiento en el sistema de salud público del Ecuador](https://revecuatneurol.temp.publicknowledgeproject.org/index.php/revecuatneurol/article/download/9713/7959/30906), Revista Ecuatoriana de Neurología
- [Slow Motion Analysis of Repetitive Tapping (SMART) test](https://www.medrxiv.org/content/10.1101/2021.03.24.21254234v1), medRxiv
- [Screening for Parkinson's disease using computer vision](https://ideas.repec.org/a/plo/pone00/0330373.html), Kasemsap et al., PLOS ONE
- [Differential diagnosis of Parkinson disease, essential tremor and enhanced physiological tremor](https://pmc.ncbi.nlm.nih.gov/articles/PMC5573102), Zhang et al., 2017
- [BE-FAST: reducing the proportion of strokes missed using the FAST mnemonic](https://pubmed.ncbi.nlm.nih.gov/28082668/), Stroke, 2017
- [Parkinsons dataset (Oxford Parkinson's Disease Detection)](https://archive.ics.uci.edu/dataset/174/Parkinson'ss), UCI Machine Learning Repository
- [Sea Hero Quest gaming app can detect Alzheimer's risk](https://www.healio.com/psychiatry/alzheimers-disease-dementia/news/online/%7B00982760-977e-4059-8f17-09f85874ce64%7D/sea-hero-quest-gaming-app-can-detect-alzheimers-risk), Healio
- [MediaPipe Face Landmarker para web](https://ai.google.dev/edge/mediapipe/solutions/vision/face_landmarker/web_js), Google AI Edge
- [WebLLM: inferencia de modelos de lenguaje en el navegador](https://blog.mlc.ai/2024/06/13/webllm-a-high-performance-in-browser-llm-inference-engine), MLC
- [Mejores modelos pequeños para despliegue local en 2026](https://www.ertas.ai/best/best-small-llm-for-local-deployment), Ertas
- [Herramientas para ejecutar modelos de lenguaje localmente en 2026](https://cut-the-saas.com/the-cut/best-local-llm-tools)
