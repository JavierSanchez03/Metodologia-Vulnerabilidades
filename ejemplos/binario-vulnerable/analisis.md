# Análisis del binario vulnerable

## 1. Descripción del binario y del fallo

El binario vulnerable incluido en este proyecto es un programa sencillo escrito en C cuyo objetivo es ilustrar un caso clásico de **desbordamiento de buffer en la pila (stack buffer overflow)**.

El código contiene una función llamada `vulnerable_function()` que recibe una cadena de entrada y la copia a un buffer local mediante `strcpy()`.  
El problema es que `strcpy()` **no comprueba el tamaño del destino**, por lo que si el usuario introduce una cadena más larga que el buffer, se sobrescriben zonas adyacentes de la memoria.

Este comportamiento permite:

- Sobrescribir variables locales  
- Corromper el puntero de retorno (RIP/EIP)  
- Controlar el flujo de ejecución  
- Preparar un exploit funcional  

Este tipo de vulnerabilidad es uno de los más estudiados en seguridad ofensiva y sirve como base para comprender técnicas de explotación modernas.

## 2. Análisis del código y explicación técnica del fallo

El fallo principal del binario se encuentra en la función `vulnerable_function()`, donde se declara un buffer local de 64 bytes y posteriormente se utiliza `strcpy()` para copiar la entrada del usuario sin realizar ninguna comprobación de tamaño.

Fragmento relevante del código:

- Se declara un buffer local de tamaño fijo:  
  `char buffer[64];`

- Se copia la entrada del usuario sin validación:  
  `strcpy(buffer, input);`

El problema es que `strcpy()` copia todos los bytes de la cadena de entrada hasta encontrar un byte nulo (`\0`), sin comprobar si el destino tiene espacio suficiente.  
Si el usuario introduce una cadena mayor de 64 bytes, se produce un **desbordamiento de buffer en la pila**.

Este desbordamiento permite sobrescribir:

- Variables locales  
- El puntero base (EBP/RBP)  
- El puntero de retorno (EIP/RIP)  

Al sobrescribir el puntero de retorno, un atacante puede **controlar el flujo de ejecución** y redirigirlo hacia una dirección elegida, lo que constituye la base de un exploit funcional.

Este tipo de vulnerabilidad es clásico en binarios sin protecciones modernas y es ideal para practicar técnicas como:

- Identificación de offsets  
- Control del puntero de retorno  
- Construcción de payloads  
- Uso de patrones cíclicos  
- Explotación con o sin ASLR  

## 3. Preparación del entorno y compilación del binario vulnerable

Para analizar y explotar correctamente este binario vulnerable, es necesario preparar un entorno controlado que permita reproducir el fallo sin interferencias de protecciones modernas del sistema operativo.

### Requisitos del entorno

Para trabajar con este binario se recomienda:

- Un sistema Linux.
- Herramientas de compilación como gcc.
- Opcionalmente, herramientas de análisis como gdb, pwndbg/gef/peda, objdump, readelf, strings, y utilidades de patrones cíclicos.

### Compilación del binario vulnerable

Para que el binario sea realmente explotable, es necesario desactivar ciertas protecciones durante la compilación.

Comando recomendado:

gcc vuln.c -o vuln -fno-stack-protector -z execstack -no-pie

Explicación de las opciones:

- -fno-stack-protector → desactiva los canarios de pila.
- -z execstack → permite ejecutar código desde la pila.
- -no-pie → desactiva PIE, haciendo que las direcciones sean fijas.

### Verificación de protecciones

Se pueden comprobar las protecciones del binario con:

checksec --file=./vuln

Se debería ver:

- Canary: disabled  
- NX: disabled  
- PIE: disabled  
- RELRO: none o partial  

Con esto, el binario queda listo para análisis, fuzzing y explotación.

## 4. Análisis dinámico y fuzzing del binario

El análisis dinámico permite observar el comportamiento del binario durante su ejecución y verificar cómo responde ante entradas de diferentes tamaños. Este proceso es fundamental para identificar el punto exacto en el que se produce el desbordamiento de memoria.

### Ejecución inicial

La ejecución del binario con una entrada corta confirma que el programa funciona de manera normal y que la función vulnerable procesa la cadena sin incidentes.

### Pruebas con entradas largas

Al proporcionar una entrada significativamente mayor que los 64 bytes del buffer interno, se observa un comportamiento anómalo que indica corrupción de memoria. Este comportamiento confirma la presencia del desbordamiento de pila.

### Uso de patrones cíclicos

Para determinar el desplazamiento exacto necesario para sobrescribir el puntero de retorno, se emplean patrones cíclicos generados mediante herramientas especializadas.  
El análisis posterior permite identificar la posición exacta en la que la entrada del usuario comienza a sobrescribir la dirección de retorno.

### Depuración con herramientas de análisis

El uso de un depurador permite:

- Observar el estado de los registros en el momento del fallo.  
- Verificar la sobrescritura del puntero de retorno.  
- Confirmar la ubicación del buffer en la pila.  
- Analizar el flujo de ejecución antes y después del desbordamiento.

Este proceso proporciona la información necesaria para construir un payload que permita controlar la ejecución del programa.

## 5. Identificación del offset y control del flujo de ejecución

Una vez confirmado el desbordamiento de pila, el siguiente paso consiste en determinar el desplazamiento exacto necesario para sobrescribir la dirección de retorno de la función. Este proceso permite establecer el punto en el que la entrada del usuario comienza a influir directamente en el flujo de ejecución del programa.

### Determinación del offset

Para identificar el offset se emplean patrones cíclicos generados mediante herramientas especializadas. Estos patrones permiten localizar con precisión la posición en la que se produce la sobrescritura del puntero de retorno.  
El análisis posterior del estado del programa tras el fallo revela el valor sobrescrito, lo que permite calcular el desplazamiento exacto.

### Confirmación del control del flujo

Una vez identificado el offset, se construye una entrada de prueba que permita verificar que el programa acepta valores arbitrarios en la dirección de retorno.  
La ejecución del binario con esta entrada confirma que:

- El puntero de retorno puede ser sobrescrito de forma controlada.  
- El flujo de ejecución puede redirigirse hacia una dirección específica.  
- El comportamiento del programa depende directamente de los valores proporcionados por el usuario.

Este proceso constituye la base para la construcción de un payload funcional que permita ejecutar código arbitrario o redirigir la ejecución hacia una zona concreta de la memoria.

## 6. Construcción del payload y consideraciones de explotación

Una vez identificado el desplazamiento necesario para sobrescribir la dirección de retorno, es posible construir un payload que permita controlar la ejecución del programa. La estructura del payload depende de las protecciones presentes en el binario y del objetivo de la explotación.

### Estructura general del payload

El payload suele componerse de tres partes:

1. Una secuencia inicial destinada a rellenar el buffer hasta alcanzar el offset identificado.  
2. Los bytes que sobrescriben la dirección de retorno con un valor controlado.  
3. Opcionalmente, datos adicionales que puedan ser utilizados durante la explotación.

La naturaleza exacta de cada componente depende del enfoque de explotación seleccionado.

### Consideraciones sobre la ejecución del payload

La explotación puede variar en función de las protecciones activas o desactivadas en el binario:

- En ausencia de protecciones como NX, es posible ejecutar código ubicado en la pila.  
- Si la pila no es ejecutable, la explotación puede requerir técnicas alternativas como el uso de direcciones de funciones existentes en el binario.  
- La ausencia de PIE facilita la predicción de direcciones estáticas en memoria.  
- La desactivación de canarios de pila permite sobrescribir el puntero de retorno sin detección.

### Validación del control de flujo

Una vez construido el payload, se realizan pruebas para confirmar que:

- La dirección de retorno se sobrescribe correctamente.  
- El flujo de ejecución se redirige hacia la ubicación deseada.  
- El comportamiento del programa es coherente con los valores introducidos.

Este proceso constituye la base para desarrollar una explotación completa del binario vulnerable.

## 7. Limitaciones, riesgos y medidas de mitigación

El binario analizado presenta una vulnerabilidad clásica de desbordamiento de pila que permite el control del flujo de ejecución. Sin embargo, la explotación práctica del fallo puede verse condicionada por diversos factores relacionados con el entorno, la arquitectura y las protecciones del sistema operativo.

### Limitaciones del entorno de explotación

La explotación depende de las características del entorno en el que se ejecuta el binario. Entre las principales limitaciones se encuentran:

- La presencia de protecciones como ASLR, NX o PIE puede dificultar o impedir la ejecución de código arbitrario.  
- La arquitectura del sistema influye en la forma en que se construyen los payloads y en el tamaño de los registros.  
- La configuración del compilador y del sistema puede alterar la disposición de la pila y la ubicación del buffer.

Estas limitaciones deben tenerse en cuenta al diseñar cualquier técnica de explotación.

### Riesgos asociados al fallo

El desbordamiento de pila constituye un riesgo significativo debido a su capacidad para comprometer la integridad del programa. Entre los riesgos más relevantes se encuentran:

- Sobrescritura de direcciones críticas en la pila.  
- Ejecución de código arbitrario en el contexto del proceso afectado.  
- Posible escalada de privilegios si el binario se ejecuta con permisos elevados.  
- Inestabilidad del sistema o interrupción del servicio.

Estos riesgos justifican la necesidad de aplicar medidas de mitigación adecuadas.

### Medidas de mitigación recomendadas

Para reducir la probabilidad de explotación de este tipo de vulnerabilidades, se recomienda aplicar las siguientes medidas:

- Utilizar funciones seguras de manejo de cadenas que incluyan comprobación de límites.  
- Activar protecciones del compilador como stack canaries, NX y PIE.  
- Revisar y auditar el código para identificar operaciones inseguras.  
- Implementar políticas de compilación que impidan la ejecución de binarios sin protecciones.  
- Aplicar controles de seguridad adicionales en el entorno de ejecución.

La combinación de estas medidas contribuye a minimizar el impacto de vulnerabilidades basadas en desbordamientos de memoria.

## 8. Conclusiones del análisis del binario vulnerable

El estudio del binario vulnerable permite comprender de manera práctica cómo un desbordamiento de pila puede comprometer la integridad y el control de un programa. El análisis realizado demuestra que la ausencia de validación en operaciones de copia de memoria constituye un riesgo significativo, especialmente cuando se emplean funciones que no verifican límites.

El proceso de análisis ha permitido identificar:

- La ubicación exacta del fallo en el código fuente.  
- El comportamiento del programa ante entradas de tamaño excesivo.  
- El desplazamiento necesario para sobrescribir la dirección de retorno.  
- La posibilidad de redirigir el flujo de ejecución mediante un payload controlado.  

Asimismo, se ha puesto de manifiesto que la explotación del binario depende en gran medida de las protecciones activas en el entorno de ejecución. La desactivación de mecanismos como NX, PIE o los canarios de pila facilita la explotación, mientras que su presencia puede impedirla o requerir técnicas más avanzadas.

Este análisis subraya la importancia de aplicar buenas prácticas de programación, utilizar funciones seguras y habilitar las protecciones proporcionadas por el compilador y el sistema operativo. La combinación de estas medidas reduce de manera significativa la probabilidad de que vulnerabilidades de este tipo puedan ser explotadas con éxito.
