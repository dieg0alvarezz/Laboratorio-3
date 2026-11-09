# Laboratorio de aplicación inteligente adaptativa

## Identificación

- Integrantes: [Diego Alvarez, Ariel Carrasco, Gabriel Reyes]
- Curso y sección: [Ingenieria Web Avanzada]
- URL del repositorio: [https://github.com/dieg0alvarezz/Laboratorio-3.git]
- Fecha: [09/11/2026]

## Objetivo y alcance

Complete un recomendador de recursos de aprendizaje que represente los intereses
de cada usuario y actualice su perfil al recibir la interacción **Me interesa**.
El núcleo se implementa en Python y se ejecuta por consola. Una interfaz Angular
puede incorporarse como extensión después de esta actividad.

El catálogo, los perfiles, las validaciones, la aplicación por consola y el registro
de evidencias se proporcionan. Complete únicamente los cuatro bloques señalados
en `motor.py` y documente su interpretación. No modifique los datos de las pruebas
ni la aplicación para obtener un resultado esperado.

La representación usa **solo la columna etiquetas**. Títulos y descripciones sirven
para presentar el catálogo. Todos los recursos continúan disponibles después de
una interacción; no se excluye el recurso valorado. Esto permite comparar el mismo
conjunto de 12 candidatos antes y después de la actualización del perfil.

## Validación del catálogo y tratamiento de datos

El catálogo se encuentra en datos/recursos.csv y contiene cuatro campos: id, titulo, descripcion y etiquetas. Para calcular las recomendaciones se utiliza únicamente la columna etiquetas, cuyos términos se representan mediante TF-IDF. Los campos id, titulo y descripcion permiten identificar y presentar los recursos.

La función cargar_catalogo() valida que existan las columnas obligatorias, elimina espacios en blanco al inicio y al final de sus valores y comprueba que no existan campos vacíos. Si encuentra datos obligatorios vacíos o identificadores de recursos repetidos, genera un error mediante ValueError, evitando utilizar un catálogo inválido.

Por otra parte, los intereses vacíos de los usuarios son permitidos. Cuando un perfil no contiene intereses o sus términos no pertenecen al vocabulario del catálogo, se genera un vector nulo y el sistema devuelve una lista de recomendaciones vacía con un mensaje informativo.

## Preparación

Use Python 3.10 o posterior. Abra una terminal en esta carpeta.
Versión de Python utilizada: Python 3.13.5
```bash
python -m venv .venv
```

En Windows puede usar `py -m venv .venv`.

Activación en PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Activación en CMD:

```bat
.venv\Scripts\activate.bat
```

Activación en macOS o Linux:

```bash
source .venv/bin/activate
```

Instalación y comprobación:

```bash
python -m pip install -r requirements.txt
python -c "import sys; print(sys.executable)"
python app.py --comprobar
```

La comprobación funciona con los cuatro bloques sin completar. Debe informar
12 recursos, 4 usuarios y una matriz TF-IDF de 12 filas. Registre el comando de
activación que utilizó: .venv\Scripts\activate.bat. Al finalizar, registre las versiones instaladas:

```bash
python -m pip freeze > requirements.txt
```

## Funciones que debe completar

| Función | Entrada principal | Salida y requisito |
|---|---|---|
| `crear_perfil` | Texto de intereses y vectorizador ya ajustado | Vector NumPy de una dimensión obtenido con `transform`; no ajustar nuevamente el vocabulario. |
| `calcular_puntuaciones` | Vector del perfil y matriz de recursos | Un valor de similitud del coseno por recurso. |
| `recomendar` | Perfil y catálogo indexado | Hasta 3 resultados con puntuación positiva; orden descendente y desempate por `id` ascendente. |
| `actualizar_perfil` | Perfil anterior y vector del recurso valorado | Vector normalizado de la combinación `0.4 * perfil + 0.6 * recurso`. |

Use `construir_resultado` al construir cada recomendación. Esta función proporciona
`id`, `titulo`, `puntuacion` y una `explicacion` basada en términos compartidos.

Un perfil vacío o compuesto por palabras fuera del vocabulario produce un vector
nulo. En esos casos deben obtenerse puntuaciones cero y una lista de recomendaciones
vacía; la aplicación ya proporciona el mensaje informativo.

El parámetro 0.6 es una elección didáctica que da mayor peso a la interacción reciente.
No es un valor validado experimentalmente. Una similitud no es una probabilidad de
interés. El ejercicio no entrena un modelo supervisado ni demuestra comprensión
semántica de los recursos.

## Ejecución después de completar el motor

Modo interactivo:

```bash
python app.py
```

Seleccione un usuario. Puede consultar el catálogo, solicitar recomendaciones y
registrar **Me interesa** indicando el ID de cualquier recurso, aunque no aparezca
entre los tres primeros. Un ID desconocido muestra un aviso y no actualiza el perfil.
Los perfiles y sus historiales de interacciones se conservan por separado durante
la ejecución. La consola muestra el historial del usuario activo. Al reiniciar se
cargan nuevamente los intereses iniciales del JSON y los historiales quedan vacíos.

Demostración reproducible:

```bash
python app.py --demo
```

Este comando parte de los perfiles iniciales, ejecuta T1-T4 y genera:

- `evidencias/reporte_demo.json`: perfiles, historiales, recomendaciones y comprobaciones.
- `evidencias/puntuaciones_antes_despues.csv`: los mismos 12 recursos con sus
  puntuaciones anteriores, posteriores y diferencias, incluyendo ceros.

Que la aplicación muestre **Cumple** es una comprobación orientativa. Revise las
puntuaciones y explique qué evidencia respalda cada resultado.

## Pruebas funcionales y evidencias

| Caso | Entrada o acción | Resultado esperado | Resultado observado y evidencia | Estado |
|---|---|---|---|---|
| T1 | Recomendar para u1 y u2 sin interacciones | R02 primero para u1; R07 primero para u2; orden descendente, máximo 3 y puntuaciones positivas. | Se generan recomendaciones diferentes para ambos usuarios. Para u1 se obtiene R02, R12 y R03, mientras que para u2 aparece R07 en primer lugar. Las puntuaciones son positivas y están ordenadas de mayor a menor, con un máximo de tres recomendaciones. | Cumple |
| T2 | u1 indica Me interesa sobre R04 y se recalcula | Cambia el perfil; R04 pasa al primer lugar; aumenta la puntuación de R05; se conservan los 12 candidatos. | Al valorar R04, el perfil de u1 se actualiza automáticamente. Las nuevas recomendaciones son R04, R02 y R09. Además, aumenta la puntuación de R05 sin haber sido valorado directamente, demostrando la adaptación del sistema. Se mantienen los 12 recursos. | Cumple |
| T3 | Consultar u2 después de la interacción de u1 | El perfil, el historial y las puntuaciones de u2 se mantienen. | Después de la interacción de u1, el perfil de u2 permanece sin modificaciones. Su historial y las puntuaciones de sus recomendaciones se mantienen iguales, demostrando que los estados de los usuarios son independientes. | Cumple |
| T4 | Recomendar para u3 y u4 | Cero puntuaciones positivas, lista vacía y mensaje informativo para ambos. | Los usuarios u3 y u4 no presentan términos reconocidos en el vocabulario. El sistema devuelve una lista vacía de recomendaciones y muestra un mensaje informativo, sin generar errores ni recomendar recursos con puntuación cero. | Cumple |

### Comparación de la adaptación

- Perfil de u1 antes de valorar R04: pandas: 0.753247, datos: 0.465091, python: 0.465091.
- Perfil de u1 después de valorar R04: python: 0.637116, api: 0.446269, web: 0.446269, pandas: 0.376477, datos: 0.232455.
- Puntuación de R05 antes y después: Antes: 0.0000. Después: 0.4378. Diferencia: +0.4378.
- Por qué ese cambio es evidencia de adaptación: Inicialmente, el recurso R05 («Principios de una API HTTP») tenía una similitud de 0.0000 con el perfil de u1, debido a que no compartían términos relevantes. Después de que el usuario valorara R04 («API web con Python»), el sistema actualizó automáticamente su vector de intereses, incorporando las características api y web. Como consecuencia, la similitud de R05 aumentó a 0.4378, sin haber sido valorado directamente. Esto demuestra que el mecanismo adapta las puntuaciones de los recursos a partir de las interacciones del usuario, conservando los mismos 12 candidatos.
- Problemas detectados y acciones realizadas: No se detectaron problemas durante la ejecución de la demostración. Las cuatro pruebas funcionales (T1, T2, T3 y T4) se completaron correctamente. Se verificó la actualización del perfil de u1, el registro de la interacción con R04, la independencia del perfil de u2 y el tratamiento adecuado de usuarios con intereses vacíos o desconocidos.

No sustituya estas observaciones por cifras inventadas. Use los resultados de su ejecución.

## Preguntas evaluadas

1. ¿Qué información describe el contenido de los recursos y qué información constituye el perfil del usuario? Identifique ambas en los archivos del proyecto.
   Respuesta: El contenido de los recursos se encuentra en datos/recursos.csv, que contiene los campos id, titulo, descripcion y etiquetas. Las etiquetas representan las características utilizadas para calcular la similitud mediante TF-IDF, mientras que el título y la descripción permiten identificar y presentar los recursos. El perfil del usuario se inicializa mediante los intereses almacenados en datos/perfiles.json. Durante la ejecución, cada usuario mantiene sus intereses iniciales, un vector numérico que representa sus preferencias actuales y un historial de interacciones. Este vector puede actualizarse automáticamente cuando el usuario valora un recurso.
   
2. ¿Por qué se utiliza fit_transform sobre el catálogo y transform sobre los intereses del usuario? ¿Qué problema causaría ajustar un vectorizador diferente para cada perfil?
   Respuesta: Se utiliza fit_transform sobre el catálogo porque permite aprender el vocabulario y calcular los pesos TF-IDF de sus términos, generando las representaciones numéricas de los recursos. En cambio, se utiliza transform sobre los intereses del usuario para representarlos utilizando exactamente el mismo vocabulario y los mismos pesos aprendidos del catálogo. Si se utilizara un vectorizador independiente para cada usuario, los vectores podrían tener distintas dimensiones, vocabularios o pesos, impidiendo realizar comparaciones consistentes mediante similitud del coseno.
   
3. ¿Por qué el mismo catálogo puede producir recomendaciones distintas para u1 y u2? ¿Una similitud de 0,80 significa que existe un 80 % de probabilidad de que el recurso guste? Justifique.
   Respuesta: El mismo catálogo puede generar recomendaciones diferentes porque cada usuario tiene un perfil de intereses distinto. Por ejemplo, u1 está relacionado con Python, datos y Pandas, mientras que u2 se interesa por Angular, componentes e interfaces. Por lo tanto, sus vectores TF-IDF son diferentes y generan puntuaciones de similitud distintas respecto de los mismos recursos. Una similitud de 0,80 no representa una probabilidad del 80 % de que el recurso guste. Indica un alto grado de similitud entre las representaciones vectoriales del usuario y del recurso. Es una medida matemática de coincidencia de características, no una predicción probabilística de satisfacción.
   
4. ¿En qué se diferencia modificar manualmente los intereses iniciales de la adaptación implementada? Identifique el evento que provoca el cambio, el estado modificado y su efecto observable.
   Respuesta: Modificar manualmente los intereses significa cambiar directamente los datos iniciales del usuario, por ejemplo, editando perfiles.json. En cambio, la adaptación implementada se produce automáticamente durante la ejecución cuando el usuario selecciona la acción «Me interesa» sobre un recurso. Este evento provoca una actualización del vector actual del usuario mediante una combinación ponderada con el vector del recurso seleccionado. La interacción también se registra en su historial. El efecto observable es que cambian las puntuaciones de similitud y pueden cambiar las recomendaciones, sin modificar manualmente los intereses iniciales ni alterar los perfiles de otros usuarios.
   
5. Relacione su implementación con las etapas del ciclo MAPE-K estudiado en clase: Monitorizar, Analizar, Planificar, Ejecutar y Conocimiento. Indique una función, operación o dato de su programa para cada etapa.
   Respuesta:
   - Monitorizar: La aplicación detecta la interacción «Me interesa» realizada por el usuario sobre un recurso, por ejemplo, u1 sobre R04.
   - Analizar: Se utilizan los vectores TF-IDF y calcular_puntuaciones para evaluar la relación entre las preferencias del usuario y los recursos.
   - Planificar: Se establece la estrategia de adaptación utilizando el parámetro alpha = 0.6 y se define la selección de hasta tres recomendaciones mediante recomendar.
   - Ejecutar: La función actualizar_perfil modifica y normaliza el vector del usuario activo. Posteriormente, la aplicación recalcula y presenta las nuevas recomendaciones.
   - Conocimiento: Está constituido por el catálogo de recursos, el vocabulario TF-IDF, los perfiles actuales y los historiales de interacción almacenados durante la ejecución.
  
De esta manera, el sistema mantiene un ciclo de adaptación basado en las preferencias e interacciones de los usuarios.

6. Indique una limitación del mecanismo implementado y proponga una mejora concreta. Explique qué dato adicional o qué cambio del algoritmo exigiría esa mejora.
   Respuesta: Una limitación del sistema es que calcula las recomendaciones utilizando únicamente las etiquetas de los recursos. Esto restringe la representación del contenido, porque la descripción y el título no intervienen en la similitud. Como mejora, se propone incorporar las descripciones de los recursos al proceso de vectorización TF-IDF, junto con las etiquetas. Para implementarlo, sería necesario modificar la preparación del texto que recibe fit_transform, concatenando las etiquetas y las descripciones de cada recurso. Posteriormente, se deberían generar nuevamente los vectores y evaluar los resultados.
Esto permitiría considerar más información sobre cada recurso y potencialmente mejorar las recomendaciones.

## Archivos de la entrega

Entregue `app.py`, `motor.py`, `datos/recursos.csv`, `datos/perfiles.json`,
`requirements.txt`, `.gitignore`, este README completado y los dos archivos de
`evidencias/`. No incluya `.venv`, `__pycache__` ni dependencias instaladas.

### Reproducción desde una copia nueva

Compruebe que otra persona puede crear el entorno, instalar las dependencias y
ejecutar los comandos anteriores utilizando únicamente los archivos entregados.
Registre aquí cualquier instrucción adicional necesaria: No se requieren instrucciones adicionales. Para reproducir el proyecto, se debe crear y activar el entorno virtual, instalar las dependencias y ejecutar los comandos indicados en la sección Preparación.
