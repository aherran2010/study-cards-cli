# Feature Specification: Flashcards desde terminal con Markdown

**Feature Branch**: `main`

**Created**: 2026-11-24

**Status**: Draft

**Input**: User description: "Crear una aplicación CLI minimalista llamada study-cards-clip para practicar flashcards desde terminal usando archivo Markdown. El usuario debe poder cargar un archivo questions.md con preguntas y respuestas, iniciar una sesión de repaso interactiva desde la terminal. responder preguntas y recibir feedback inmediato indicando si la respuesta es correcta o incorrecta. Al finalizar, la aplicación debe mostrar un resumen con total de preguntas, respuestas correctas e incorrectas. Si existen errores en el primer turno, la aplicación debe realizar automáticamente un segundo turno únicamente con las preguntas falladas. El sistema debe priorizar simplicidad, claridad de uso y facilidad de mantenimiento."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Cargar un archivo de preguntas (Priority: P1)

El usuario prepara un archivo Markdown legible con preguntas y respuestas y lo pone a disposición de la aplicación. La aplicación comprueba que el archivo existe y que su contenido tiene el formato esperado, y deja el conjunto de tarjetas listo para una sesión.

**Why this priority**: Sin una carga fiable del archivo no existe ninguna sesión de repaso; es el requisito previo de todas las demás historias y entrega valor por sí sola al validar el material de estudio antes de empezar.

**Independent Test**: Crear un `questions.md` con tres tarjetas válidas, iniciar la aplicación y confirmar que las tres tarjetas quedan listas para repasarse, en el orden del archivo, sin necesidad de ejecutar ninguna otra función.

**Acceptance Scenarios**:

1. **Given** un archivo `questions.md` en el directorio de trabajo con tres tarjetas válidas, **When** el usuario inicia la aplicación sin indicar una ruta, **Then** la aplicación carga las tres tarjetas y muestra que la sesión está lista para comenzar.
2. **Given** un archivo de preguntas en una ruta indicada explícitamente por el usuario, **When** inicia la aplicación con esa ruta, **Then** la aplicación utiliza ese archivo en lugar del `questions.md` predeterminado.
3. **Given** un archivo inexistente, vacío o con una tarjeta incompleta, **When** el usuario intenta iniciar una sesión, **Then** la aplicación muestra un mensaje claro que indica el problema y la ubicación aproximada, y no inicia ninguna sesión.

---

### User Story 2 - Repasar tarjetas con feedback inmediato (Priority: P1)

El usuario inicia una sesión interactiva. La aplicación presenta una pregunta, espera la respuesta escrita por el usuario y, en cuanto se envía, indica de forma inequívoca si fue correcta o incorrecta antes de pasar a la siguiente.

**Why this priority**: Es el valor central de la herramienta: practicar activamente y saber al instante si se acertó. Sin este bucle de pregunta/respuesta/feedback no hay experiencia de repaso.

**Independent Test**: Cargar un archivo con dos tarjetas, responder una correctamente y otra incorrectamente, y verificar que aparece un mensaje de acierto y un mensaje de error inmediatamente después de cada respuesta, en orden.

**Acceptance Scenarios**:

1. **Given** una sesión iniciada con cinco tarjetas, **When** el usuario responde correctamente a la primera, **Then** la aplicación muestra una confirmación clara de acierto y continúa con la segunda pregunta.
2. **Given** una sesión iniciada con cinco tarjetas, **When** el usuario responde incorrectamente a una pregunta, **Then** la aplicación muestra una indicación clara de error junto con la respuesta esperada, y continúa con la siguiente pregunta.
3. **Given** una tarjeta en una sesión, **When** el usuario envía una respuesta vacía, **Then** la respuesta se cuenta como incorrecta y se muestra el feedback correspondiente.
4. **Given** una sesión en curso, **When** se presenta cada pregunta, **Then** la aplicación muestra el progreso de la sesión (por ejemplo, pregunta actual de un total).

---

### User Story 3 - Ver el resumen de la sesión (Priority: P1)

Al terminar la sesión, el usuario ve un resumen compacto con el número total de preguntas respondidas, cuántas fueron correctas y cuántas incorrectas, de modo que pueda evaluar su desempeño de un vistazo.

**Why this priority**: El resumen es un requisito explícito del producto y cierra el bucle de aprendizaje; sin él el usuario no tiene una medida objetiva del resultado.

**Independent Test**: Responder tres preguntas (dos aciertos y un error) y verificar que el resumen final muestra exactamente tres preguntas respondidas, dos correctas y una incorrecta, con cifras coherentes con el feedback mostrado.

**Acceptance Scenarios**:

1. **Given** una sesión de una sola ronda con tres respuestas (dos correctas y una incorrecta), **When** se agota la última pregunta, **Then** la aplicación muestra un resumen con total 3, correctas 2 e incorrectas 1, sin necesidad de que el usuario lo solicite.
2. **Given** una sesión que incluye una ronda de repaso, **When** se muestra el resumen final, **Then** las cifras totales incluyen todas las respuestas de ambas rondas y se ofrece un desglose por ronda.
3. **Given** una sesión finalizada, **When** el usuario solicita explícitamente la salida estructurada, **Then** obtiene los mismos totales del resumen en un formato legible por máquina, además de la salida habitual para personas.

---

### User Story 4 - Repasar automáticamente solo las preguntas falladas (Priority: P1)

Si el usuario comete errores en el primer turno, la aplicación inicia por su cuenta un segundo turno que contiene exclusivamente las preguntas que falló, para que pueda corregirlas de inmediato sin relanzar la herramienta ni repetir lo que ya acertó.

**Why this priority**: Refuerza el aprendizaje en el momento y evita trabajo redundante; está solicitado explícitamente y solo tiene sentido después de la primera ronda.

**Independent Test**: Responder cuatro preguntas con dos errores y verificar que, sin intervención del usuario, se inicia una segunda ronda con exactamente las dos preguntas falladas y que el resumen final refleja las seis respuestas.

**Acceptance Scenarios**:

1. **Given** un primer turno con dos respuestas incorrectas, **When** termina el primer turno, **Then** la aplicación inicia automáticamente un segundo turno con exactamente esas dos preguntas, en el mismo orden en que aparecieron.
2. **Given** un primer turno sin errores, **When** termina el primer turno, **Then** no se inicia ningún segundo turno y se muestra directamente el resumen.
3. **Given** un segundo turno con errores, **When** termina ese segundo turno, **Then** no se inicia un tercer turno y se muestra el resumen final con el desglose de ambas rondas.

---

### Edge Cases

- El archivo indicado no existe, no se puede leer o la ruta es inválida: se muestra un mensaje de error claro y no se inicia la sesión.
- El archivo existe pero no contiene ninguna tarjeta válida: se indica al usuario y no se inicia la sesión.
- Una tarjeta tiene pregunta pero falta su respuesta, o tiene respuesta vacía: se señala la tarjeta problemática y no se inicia la sesión hasta corregirla.
- El usuario pulsa Enter sin escribir nada: la respuesta se considera incorrecta y se procesa con normalidad.
- El usuario interrumpe la sesión: la aplicación finaliza de forma ordenada, sin presentar un resumen de una sesión incompleta.
- Una pregunta admite más de una respuesta válida: se acepta cualquiera de las variantes y se cuentan como correctas.
- La respuesta del usuario difiere solo en mayúsculas o en espacios al inicio/final: se considera correcta.
- Un archivo contiene un número elevado de tarjetas (por ejemplo, 100): la sesión conserva el orden, los contadores y el progreso sin pérdida de información.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: La aplicación DEBE cargar por defecto un archivo llamado `questions.md` situado en el directorio de trabajo y DEBE permitir indicar explícitamente otra ruta de archivo.
- **FR-002**: La aplicación DEBE interpretar cada tarjeta del archivo como una pregunta seguida de su respuesta, respetando el orden en que aparecen en el documento.
- **FR-003**: La aplicación DEBE validar el archivo antes de iniciar una sesión y, ante un archivo ausente, vacío o con tarjetas incompletas, DEBE mostrar un mensaje comprensible que identifique el problema y no DEBE iniciar la sesión.
- **FR-004**: La aplicación DEBE iniciar una sesión interactiva que presente una pregunta a la vez y espere a que el usuario envíe su respuesta antes de continuar.
- **FR-005**: La aplicación DEBE mostrar un indicador de progreso con la pregunta actual y el total de preguntas de la ronda.
- **FR-006**: La aplicación DEBE aceptar respuestas de texto libre y compararlas con las respuestas esperadas tras ignorar las diferencias de mayúsculas y los espacios situados al principio o al final.
- **FR-007**: La aplicación DEBE considerar válidas todas las variantes de respuesta declaradas para una misma pregunta.
- **FR-008**: La aplicación DEBE mostrar inmediatamente después de cada respuesta si fue correcta o incorrecta, distinguiendo visualmente ambos resultados, y DEBE mostrar la respuesta esperada cuando la respuesta sea incorrecta.
- **FR-009**: Al terminar el primer turno, la aplicación DEBE iniciar automáticamente un segundo turno si hubo al menos una respuesta incorrecta, y DEBE incluir en él únicamente las preguntas falladas, en su orden original.
- **FR-010**: La aplicación NO DEBE iniciar un segundo turno cuando no haya errores, NI DEBE iniciar turnos adicionales después del segundo, incluso si quedan errores.
- **FR-011**: Al finalizar todas las rondas, la aplicación DEBE mostrar un resumen con el total de preguntas respondidas, el número de respuestas correctas y el número de respuestas incorrectas, con un desglose por ronda.
- **FR-012**: La aplicación DEBE mantener separados los mensajes de error de la salida normal de la sesión, para que el feedback y el resumen sigan siendo legibles.
- **FR-013**: La aplicación DEBE ofrecer, cuando el usuario lo solicite explícitamente, una salida en formato estructurado legible por máquina (JSON) con los mismos resultados del resumen, además de la salida habitual para personas.
- **FR-014**: La sesión DEBE funcionar sin depender de servicios externos ni de conexión de red.

### Key Entities *(include if feature involves data)*

- **Tarjeta**: unidad de estudio formada por una pregunta y una o más respuestas aceptadas; conserva su posición dentro del archivo.
- **Archivo de preguntas**: documento Markdown de texto plano que contiene la colección de tarjetas; es la única fuente de datos de la sesión.
- **Respuesta del usuario**: texto enviado para una tarjeta concreta, con un resultado asociado (correcta o incorrecta) y la ronda en que se dio.
- **Ronda de repaso**: paso de la sesión en el que se presentan todas las tarjetas asignadas a esa ronda; la primera incluye todas las tarjetas y la segunda, solo las falladas.
- **Resumen**: recuento final de preguntas respondidas, aciertos y errores, con desglose por ronda.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Con un archivo de 10 tarjetas válidas, el 100 % de las preguntas se presentan una sola vez por ronda y en el orden del archivo.
- **SC-002**: El 100 % de las respuestas reciben feedback de acierto o error antes de que se muestre la pregunta siguiente.
- **SC-003**: En el 100 % de los casos con errores en la primera ronda, la segunda ronda contiene exactamente el mismo número de preguntas falladas y ninguna otra.
- **SC-004**: En el 100 % de las sesiones, las cifras del resumen coinciden exactamente con el feedback mostrado, incluidas las respuestas de la segunda ronda.
- **SC-005**: El sistema mantiene recuentos y orden correctos al procesar sesiones de hasta 100 tarjetas, sin pérdida de resultados.
- **SC-006**: Al menos el 90 % de usuarios nuevos consiguen iniciar y completar una sesión usando únicamente la ayuda integrada de la herramienta, sin consultar documentación externa.
- **SC-007**: El 100 % de las sesiones pueden completarse sin conexión a red ni servicios externos.

## Assumptions

- La audiencia son estudiantes que se sienten cómodos editando archivos de texto plano y que guardan sus preguntas en el directorio desde el que ejecutan la herramienta.
- Formato de archivo acordado por defecto: cada tarjeta es un encabezado de segundo nivel cuyo texto es la pregunta (`## ¿Cuál es la capital de Francia?`) seguido de un párrafo con la respuesta (`París`). Si hay varias respuestas válidas, se separan con el carácter `|`. El resto del Markdown se trata como texto de apoyo y no como tarjetas.
- La corrección compara el texto del usuario con las respuestas esperadas sin distinguir mayúsculas de minúsculas e ignorando espacios al principio y al final; el resto de signos y espacios internos se consideran significativos.
- "Total de preguntas" en el resumen significa todas las respuestas dadas en la sesión, incluidas las de la segunda ronda; el total de tarjetas únicas del archivo se mantiene como contexto en el desglose.
- El repaso consta de dos rondas como máximo: la segunda es automática y no existe una tercera, conforme al alcance solicitado.
- Una tarjeta mal formada impide el inicio de la sesión para evitar resultados ambiguos; se prefiere un error claro y temprano antes que un repaso parcial.
- No se guarda progreso entre ejecuciones: cada sesión es independiente y empieza con contadores a cero.
- La interfaz de usuario está en español, idioma de la solicitud.
- La salida legible por máquina se solicita de forma explícita; la salida por defecto sigue siendo pensada para lectura humana.
- La simplicidad y la claridad de uso primen sobre funcionalidades avanzadas (barajas, horarios de repetición, cuentas de usuario o estadísticas históricas), que quedan fuera del alcance de esta versión.
