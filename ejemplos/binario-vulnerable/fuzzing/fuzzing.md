# Fuzzing del binario vulnerable

## 1. Objetivo del fuzzing

El propósito del fuzzing es evaluar el comportamiento del binario vulnerable frente a entradas generadas de manera automática y no controlada. Este proceso permite identificar fallos de memoria, comportamientos inesperados y condiciones que puedan conducir a un desbordamiento de pila.

El fuzzing complementa el análisis estático y dinámico, proporcionando una visión práctica del impacto real de la vulnerabilidad.

---

## 2. Metodología empleada

El proceso de fuzzing se basa en la ejecución repetida del binario utilizando entradas de diferentes tamaños y patrones. Para ello se emplean:

- Cadenas aleatorias de longitud creciente.  
- Patrones repetitivos diseñados para detectar sobrescrituras.  
- Entradas especialmente largas destinadas a forzar fallos de memoria.  

El objetivo es provocar un comportamiento anómalo que permita identificar el punto exacto en el que el programa deja de funcionar correctamente.

---

## 3. Observación del comportamiento del programa

Durante el proceso de fuzzing se observan los siguientes aspectos:

- Respuestas del programa ante entradas válidas y no válidas.  
- Posibles mensajes de error o fallos de segmentación.  
- Cambios en el flujo de ejecución provocados por entradas excesivamente largas.  
- Evidencias de corrupción de memoria en la pila.  

El análisis de estos comportamientos permite confirmar la existencia del desbordamiento de pila.

---

## 4. Identificación del punto de fallo

El fuzzing permite determinar con precisión:

- El tamaño aproximado de la entrada que provoca el fallo.  
- La ubicación del buffer vulnerable dentro de la pila.  
- La relación entre el tamaño de la entrada y la sobrescritura del puntero de retorno.  

Estos datos resultan esenciales para la fase posterior de explotación, ya que permiten calcular el desplazamiento necesario para controlar el flujo de ejecución.

---

## 5. Conclusiones del fuzzing

El proceso de fuzzing confirma que:

- El binario no valida correctamente el tamaño de la entrada.  
- La función vulnerable copia datos sin comprobar límites.  
- Es posible provocar un fallo consistente mediante entradas de gran tamaño.  
- El comportamiento observado es compatible con un desbordamiento de pila explotable.  

El fuzzing proporciona así la base empírica necesaria para avanzar hacia la explotación controlada del binario.
