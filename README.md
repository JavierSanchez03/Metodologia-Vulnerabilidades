# Metodología de Análisis de Vulnerabilidades y Explotación  
### Trabajo Individual – Módulo 6: Vulnerabilidades  
### Autor: Javier

---

## Introducción

Este proyecto recoge mi metodología personal para el análisis de vulnerabilidades y el desarrollo de exploits, siguiendo los contenidos del Módulo 6 del Máster en Ciberseguridad.  
El objetivo es demostrar una visión técnica clara, aplicada y alineada con escenarios reales, tal como se espera de un analista profesional.

## Mentalidad del analista

El análisis de vulnerabilidades requiere una combinación de habilidades técnicas y cognitivas:

- Curiosidad técnica constante  
- Capacidad de descomponer sistemas complejos  
- Pensamiento estructurado  
- Persistencia ante fallos  
- Comprensión profunda del funcionamiento interno del software  

Esta mentalidad es clave para abordar vulnerabilidades reales de forma eficaz.

# Metodología propuesta

Mi metodología se divide en cinco fases principales, alineadas con el flujo de trabajo de un analista profesional.

## 1. Reconocimiento y superficie de ataque

El objetivo de esta fase es comprender el sistema antes de analizarlo en profundidad.

Incluye:

- Identificación de servicios expuestos  
- Enumeración de puertos  
- Fingerprinting  
- Análisis de versiones y dependencias  

Herramientas habituales: Nmap, Masscan, RustScan, Amass, Sn1per.

## 2. Identificación de vulnerabilidades

En esta fase se combinan técnicas automáticas y manuales:

- Scanners de vulnerabilidades  
- Revisión de CVEs asociadas  
- Fuzzing de entradas expuestas  
- Análisis manual de lógica y validaciones  

El objetivo es detectar fallos potenciales que requieran análisis más profundo.

## 3. Análisis técnico del fallo

Una vez identificada una vulnerabilidad, se procede a su análisis técnico:

- Ingeniería inversa  
- Análisis dinámico  
- Diffing entre versiones  
- Reproducción del crash  
- Identificación de registros controlados  
- Comprensión del flujo de ejecución  

Esta fase determina si el fallo es explotable y en qué condiciones.

## 4. Explotación

El objetivo de esta fase es convertir el fallo en un comportamiento controlado.

Incluye:

- Control del flujo de ejecución  
- Construcción del payload  
- Bypass de mitigaciones (ASLR, DEP, Stack Canaries, CFG)  
- Estabilización del exploit  

Aquí se demuestra la explotabilidad real de la vulnerabilidad.

## 5. Post‑explotación y validación

Una vez explotada la vulnerabilidad:

- Se confirma el impacto real  
- Se obtiene información relevante  
- Se evalúa la persistencia (si aplica)  
- Se documenta el proceso  
- Se proponen mitigaciones  

Esta fase cierra el ciclo de análisis.


# Ejemplos incluidos en el proyecto

Este repositorio contiene dos ejemplos prácticos:

### CVE‑2017‑5638 (Apache Struts2 RCE)  
Documentación completa en:  
`/ejemplos/CVE-2017-5638/`

### Binario vulnerable propio  
Documentación, código y análisis en:  
`/ejemplos/binario-vulnerable/`

# Aproximación a 0‑days

Mi flujo de trabajo para investigar vulnerabilidades desconocidas incluye:

- Preparación del entorno  
- Fuzzing dirigido  
- Crash triage  
- Ingeniería inversa  
- Construcción de primitives  
- Bypass de mitigaciones  
- Documentación final  

Este enfoque permite analizar fallos no documentados y evaluar su explotabilidad.

# Conclusiones

La realización de este proyecto me ha permitido comprender de manera más profunda cómo funcionan las vulnerabilidades de memoria y, sobre todo, cómo un fallo aparentemente pequeño puede convertirse en un problema serio de seguridad. Aunque al principio el análisis del binario vulnerable me resultaba abstracto, poco a poco fui entendiendo la lógica interna del programa y la forma en la que un desbordamiento de pila puede alterar su comportamiento.

El proceso de fuzzing me ayudó a ver el programa desde otra perspectiva: no solo como código, sino como un sistema que reacciona ante estímulos inesperados. Observar cómo fallaba ante entradas aleatorias me hizo entender mejor la fragilidad de ciertas funciones y la importancia de validar correctamente los datos.

La parte de explotación fue, sin duda, la más desafiante. Requirió paciencia, precisión y una comprensión clara de cómo se organiza la memoria. Aunque en algunos momentos me sentí perdido, avanzar paso a paso me permitió ver cómo cada pieza encajaba: el offset, la dirección de retorno, el payload… Todo tenía sentido cuando lo veía funcionar.

Más allá de lo técnico, este proyecto me ha enseñado algo importante: la seguridad no es un añadido, sino una responsabilidad. Un simple descuido en el código puede abrir la puerta a problemas graves, y entender cómo se explotan estas vulnerabilidades es la mejor forma de aprender a evitarlas.

En definitiva, este trabajo me ha permitido reforzar mis conocimientos, mejorar mi capacidad de análisis y, sobre todo, desarrollar una visión más crítica y consciente sobre la importancia de escribir software seguro.


# Estructura del repositorio
Metodologia-Vulnerabilidades/
│
├── README.md
├── ejemplos/
│   ├── CVE-2017-5638/
│   └── binario-vulnerable/
        └──exploit/
        └──fuzzing/
