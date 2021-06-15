---
title: Agentes
parent: CDSLab
nav_order: 3
---

# Interfaz para Modelos Basados en Agentes
Este modelo se implementa con el  objetivo de hacer una análisis exploratorio y no-predictivo de las dinámicas de la infección por SARS-CoV-2 bajo diferentes escenarios de control y aplicando estrategias de mitigación y contención de la epidemia. El modelo se construye usando la librería *Contagious Disease Simulation*, la cual es de acceso libre y permite diseñar diferentes modelos epidemiológicos basados en agentes, para analizar las dinámicas de la epidemia en un lugar determinado.

El modelo tiene las siguientes características:
1. Los agentes están ubicados explícitamente en el espacio y tiene un movimiento determinado.
2. La población de agentes es heterogénea.
3. El contagio depende de las reglas de comportamiento de los agentes.
4. Los agentes infectados transitan a través de una serie de estado de la infección y la enfermedad.
5. Se considera el diagnóstico de los agentes contagiados y la ocupación de camas hospitalarias.

En cada paso de tiempo los agentes se mueven, desde en una posición inicial aleatoria, siguiendo una distribución de velocidades que describe un  patrón particular de movimiento  y que determinan los perfiles de movilidad de los mismos. Las dimensiones del espacio en el que se mueven los agentes, se ajustan de tal manera que se conserva una densidad deseada. Por ejemplo, para una densidad de 0.0125 habitantes por metro cuadrado con mil agentes, se debe configurar un área de 282 x 282 metros cuadrados. La dinámica descrita, se esquematiza en la __Figura 1.__

__Poner imagen__

*__Figura 1.__ Distribución espacial y movimiento de los agentes, A. Posición inicial de los agentes en el espacio, B. Distribución de velocidades de los agentes (extraído de encuestas origen destino para el 2017 en Medellín), C. Dimensiones del espacio para mil agentes con densidad poblacional de 0.0125 habitantes por metro cuadrado, D. Heterogeneidad de la población según la vulnerabilidad por grupo etario*

Lo anterior determina los encuentros entre los agentes, pero los contactos y contagios dependen de la regla de comportamiento de estos, que indica el  acatamiento o no de las normas de distanciamiento social. Cuando los agentes no acatan esta norma, entran en el área de dispersión del virus y, por tanto, en contacto con un agente contagiado, dando lugar a una posible transmisión de la enfermedad. La __Figura 2__, presenta un diagrama de la situación anterior.

__Poner imagen__

*__Figura 2.__  Distanciamiento social y radio de contagio. Los agentes que acatan la norma de distanciamiento mantienen un radio de evasión mayor al de contagio, los demás representan un escenario de contagio.*

Una vez contagiado, el agente transita a través de una serie de estados de la infección descritos por la historia natural de la misma. Cada agente permanece en cada de uno de los estados por un tiempo determinado, el cual se asigna de forma independiente y según una distribución de tiempos de permanencia o vida media por estado. La latencia (L) es el período durante el cual el agente, aunque infectado, no es contagioso. Por el contrario, el período de infecciosidad (I) es aquel en el que el agente puede transmitir el virus, aún cuando este todavía se está incubando y el agente es asintomático o sintomático leve. Una vez terminado este período de infecciosidad, los agentes se pueden recuperar (R) o agravar ocupando una cama de hospital y/o UCI (H, U). Desde estos últimos dos estados los agentes pueden recuperarse o morir (M). La heterogeneidad de la población se refiere a la variabilidad en atributos como la edad y la presencia de comorbilidades en un conjunto variado de agentes. Esto determina el grado de vulnerabilidad a desarrollar un cuadro clínico grave y morir por la enfermedad.

Adicionalmente, el modelo permite evaluar el efecto del diagnóstico sobre las dinámicas de infección. Para lo cual se considera la probabilidad que tienen los individuos de ser diagnosticados dependiendo del estado de infección que presentan, es decir, un enfermo grave a diferencia de un asintomático tendrá mayor probabilidad de ser diagnosticado. El modelo también incluye el tiempo que tarda el diagnóstico en llevarse a cabo.

# Definiciones y atributos del modelo

El modelo gira alrededor de dos conceptos: agentes y espacio. A continuación se discute sobre estos:

__AGENTES:__

Los agentes son los individuos cuya evolución se estudia en el modelo. La escala temporal de este proceso, está dada en horas.  A cada agente, se asocian los siguientes atributos:

1. __Posición:__ Los individuos poseen una posición inicial elegida aleatoriamente y en cada paso de tiempo las coordenadas x y y de las posiciones son actualizadas como consecuencia de los cambios en la velocidad.

2. __Velocidad:__ Los individuos poseen una velocidad inicial elegida aleatoriamente y en cada paso de tiempo los individuos cambian sus velocidades con una magnitud elegida aleatoriamente a partir de una distribución de velocidades.


3. __Estado de la infección:__ Se refiere al estado que presenta el individuo dependiendo de si está o no infectado. Los estados de la infección incluyen a los individuos susceptibles de infección y a los estados a los cuales transitan cuando se infectan. Los posibles estados para un agente son: 

      3.1. __Susceptibles (S):__ El nuevo coronavirus es recientemente emergente en humanos. Por lo tanto, la población general es susceptible porque carecen de inmunidad contra este (Whang,2020). El SARS-CoV-2 puede infectar a las personas con inmunidad normal o comprometida (Whang,2020) y  en todos los rangos de edad.

      3.2. __Latentes (L):__ Son los individuos que en los primeros días de incubación del virus, tras haberse infectado. Estos no son infecciosos.

      3.3. __Infecciosos (I):__ Son aquellos individuos que pueden producir casos secundarios al infectar a otros. En este estado se incluyen los incubadores infecciosos, los asintomáticos y los sintomáticos leves.

      3.4. __Hospitalizados (H):__ Son aquellos que por el agravamiento de la enfermedad requieren el uso de de camas hospitalarias. 
 
      3.5. __Unidad de cuidados intensivos (U):__  Son aquellos que por el agravamiento de la enfermedad requieren el uso de una unidad de cuidados intensivos. Este incluye casos críticos con pacientes que pueden sufrir insuficiencia respiratoria, shock séptico y / o disfunción o falla de múltiples órganos. Además de casos severos, donde los pacientes sufren dificultad para respirar, frecuencia respiratoria ≥ 30 / minuto, saturación de oxígeno en la sangre ≤93%, razón PaO2 / FiO2 <300,35 y / o infiltraciones pulmonares> 50% entre 24-48 horas (OurWorldData-35,3). manifiesta un cuadro clínico severo en el cual es necesaria la hospitalización y/o la utilización de recursos de Unidades de Cuidados Intensivos para el tratamiento del paciente.

      3.6. __Recuperados (R):__ Son aquellos individuos que han superado la enfermedad y poseen una inmunidad tal que no se vuelven a infectar con el virus. La recuperación no implica que el individuo sea negativo en un test de detección del virus.

      3.7.__Muertos (M):__ Refiere a si el individuo está muerto por la enfermedad Covid-19.

Además de estos, exisiten otros parametros que regulan la propagación de la enfermedad y su evolución cuando los agentes se infectan. Estos son:

4. __Grupo etario:__ Los individuos poseen una edad inicial y cada paso de tiempo esta edad se actualiza, dependiendo de la edad el individuo se clasifica dentro de un grupo etario.

5. __Grado de vulnerabilidad:__ La vulnerabilidad se define como la tendencia de los pacientes con Covid-19, a agravar el cuadro clínico de la enfermedad. Para este modelo se consideran dos grupos, los vulnerables y los no-vulnerables. Los primeros tienen mayor riesgo de morir por la enfermedad  (WHO-China,2020)

6. __Tiempo en cada estado:__ Una vez un individuo entra en un estado determinado se le asigna un tiempo de permanencia en el mismo, este tiempo se asigna de forma aleatoria y ponderada por una distribución de tiempo en cada estado. 

7. __Diagnóstico:__ Se refiere a si el agente ha resultado positivo en una prueba de  PCR para la detección del virus en el cuerpo.  La probabilidad de diagnóstico depende del estado de infección.

8. __Tiempo de espera para el diagnóstico:__ Refiere al tiempo que transcurre entre la realización de la prueba y el resultado de la misma.

9. __Alerta:__ Indica si el individuo está o no alerta, en caso de estarlo el individuo toma una medida de distanciamiento social que depende de si es vulnerable o no y de quienes son los vecinos.

10. __Susceptibilidad a infección:__ Refiere a la probabilidad que tiene un individuo de infectarse. En este caso particular, todos los individuos son igual y altamente susceptibles.

__ESPACIO:__

Lugar donde los agentes interactúan durante la evolución del modelo. Está definido longitudinalmente en metros. A este espacio se asocian las siguientes características:

__Tamaño:__ Inicialmente se ajusta una longitud horizontal y vertical del bloque que representa el espacio donde se mueven los agentes, la cual permanece constante.

__Tamaño poblacional:__ Se ajusta inicialmente el número de individuos dentro del espacio, la cual disminuye en el tiempo como consecuencia de la muerte de los agentes.


__PONER REFERENCIAS__

