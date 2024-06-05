# The book of why

## 1. Introduction: Mind over data

Capítulo donde se introduce el framework y las definiciones y conceptos principales.


### Operador 'do'

La estadística clásica sólo observa. Pero para detectar casualidad, necesitamos actuar.

P(A | B) puede ser muy distinto a P(A | do(B)). Si observo que un barómetro baja, la probabilidad de tormenta crece. Pero si fuerzo un barómetro a bajar, la probabilidad de tormenta no cambia. Porque **el barómetro no causa la tormenta**.
> Pág 8

El equivalente al operador 'do' en un diagrama causal es eliminar todas la flechas que llegan hasta la variable afectada, y fijar su valor al que corresponda.
> Pág 41


### Counterfactual reasoning

Consiste en preguntarse qué habría pasado si la variable de interés hubiese sido distinta. ¿Habría muerto el paciente si se hubiese tomado el medicamento? ¿Se habrían casado si ella no se hubiese ido a EEUU? ¿Habría salido el sol si el gallo no hubiese cantado?

Es imposible realizar experimentos para verificar counterfactuals (no podemos viajar al pasado y cambiar lo que pasó). Pero hay métodos para analizarlos, que Judea aún no me cuenta :P
> Pág 9-10

Information about the effects of actions or interventions in simply not available in raw data, unless it is collected by controlled experimental manipulation. By contrast, if we are in possession of a causal model, we can often predict the result of an intervention from hands off, intervention free data.
> Pág 16


### Definición de causalidad

Una variable X es causa de Y <=> Y "escucha" el valor de X y utiliza esa información para determinar su propio valor.

X causa Y si: P(Y | do(X)) > P(Y)

Los datos raw **no contienen (ni pueden contener) información causal**.


### Descripción del framework
> Pág 12-15


### The ladder of causation
Hay tres niveles en la escalera de la causalidad:

1. Asociación (¿Qué?)
Lo que hacemos con ML; lo que hacen los animales. Vemos que dos cosas suelen ocurrir al mismo tiempo y suponemos que eso va a seguir pasando, sin preguntarnos por qué ocurre o qué hay detrás.

2. Intervención (¿Cómo?)
Aquí ya necesitamos información causal. ¿Qué va a pasar si hago X? ¿Cómo tengo que hacer esto? ¿Qué valor tendría Y si hiciese X? Cuando hacemos experimentos, tenemos información en este nivel.

3. Counterfactuals (¿Por qué?)
El nivel superior. Ni siquiera pueden realizarse experimentos. Tenemos que imaginar. ¿Cómo habría sido Y si en vez de X1 hubiese pasado X2? ¿Me habría dejado de doler la cabeza igual si no me hubiese tomado la aspirina?
> Pág 28


### Mini-test de Turing

Dado un modelo causal, responder a todas las preguntas que un humano podría responder sobre ese modelo de forma automática.
> Pág 37

Dice que el único modelo que pasa el mini-test de Turing son los diagramas causales.
> Pág 39

---

## 2. From Buccaneers to Guinea Pigs: The Genesis of Causal Inference

Éste capítulo cuenta la historia de la 'causalidad', los intentos aislados o no de varios científicos y las causas por las que no se ha convertido en algo mainstream. Maravillosa la historia de Sewall Wright.


### Diagramas causales

Inventados por Sewall Wright en 1920 para explicar las causas del color de las cobayas. Conectan el mundo de la causalidad con el de las correlaciones (escalones 1 y 2). Cada flecha representa una correlación, y el cálculo de correlaciones entre dos variables puede hacerse multiplicando el valor de cada flecha del camino que las separa.

La ausencia de una flecha es una asunción mucho más fuerte que su presencia, pues niega la existencia de relaciones causales. Una flecha presente puede tener un peso de 0, así que supone una asunción mucho más suave.
> Pág 75-77

El objetivo del libro, de los diagramas causales, y de la inferencia causal en general, no es el de determinar causalidades (eso es causal discovery). El objetivo es representar conocimiento causal plausible en un lenguaje matemático y responder queries causales que aporten información de valor.
> Pág 79-80

---

## 3. From Evidences to Causes: Reverend Bayes Meets Mr Holmes

Empieza explicando la regla de Bayes, con ejemplos y con sus implicaciones filosóficas. También comenta su relación con causalidad, y la subjetividad inherente a la definición de un prior (mismo problema que el planteamiento de hipótesis causales). Luego explica redes bayesianas, ejemplos y limitaciones.


### Probabilidad inversa

Nos resulta más fácil calcular P(A | B) cuando B causa A (probabilidad directa), que cuando A causa B (probabilidad inversa), razón por la que la regla de Bayes es de tanta utilidad (relaciona ambas probabilidades).

P(A | B) * P(B) = P(A,B) = P(B | A) * P(A)

A = estar sano
B = tomar medicamento


### Método científico

No está de mal recordarlo, y además está muy relacionado con la regla de Bayes:
1. Formular una hipótesis
2. Deducir una consecuencia testable de la hipótesis
3. Realizar un experimento y recoger evidencias
4. Actualizar nuestra creencia en la hipótesis (la regla de Bayes nos ayuda a hacer esto en el mundo real).


### Redes bayesianas

* Aunque las flechas que conectan las variables no tienen por qué implicar causalidad, cuando hacemos que sean causales todo tiene mucho más sentido.
* Funcionan mediante "mensajes" entre nodos que van propagando la "belief" hasta alcanzar un equilibrio.
* Distintos tipos de conexiones entre grupos de tres nodos (chains, forks and colliders) y sus implicaciones (pág 113-116)
* Aunque podamos ajustar las flechas siguiendo patrones causales, las BN no están preparadas per se para queries causales. Por ejemplo, si tenemos:  
 A <- B -> C  
 Es decir, dos variables A y C correlacionadas de forma espuria, cambios en A no deberían suponer cambios en C. Sin embargo, en una BN sí se producen.

---

## 4. Confounding and Deconfounding: or Slaying the Lurking Variable


### Diseño de experimentos

Para comprobar si una variable causa otra, debemos pre-seleccionar dos grupos representativos de la población y sin diferencias sustanciales entre ellos. Entonces realizamos un experimento, fijando la variable a estudiar para cada grupo. Las diferencias en los efectos deben haberse producido por la variable a estudiar (pues el resto de condiciones no cambia).


**Randomized Controlled Trial (RCT)**

Fisher quería saber qué fertilizantes eran mejores (producían más). Para eliminar confounders, dividió el terreno en cuadraditos con distintas características: tipo de suelo, microorganismos, etc y aplicó un fertilizante a cada tipo. Resultado: mil experimentos y aún existían confounders que le impedían sacar conclusiones.

Muchas veces es difícil o imposible conocer o medir todos los factores que influyen en la relación causa-efecto entre dos variables. ¿Sólución? Pasar de ellos y aleatorizar. Es decir, elegir al azar las parcelas donde hacer cada prueba y hacer muchas pruebas con cada fertilizante. De esta manera **la distribución de terrenos sobre los que probamos cada fertilizante es similar a la distribución global de suelos, y similar a la distribución sobre la que realizamos pruebas con otros fertilizantes**. ¡Eliminamos los confounders! Nuestros resultados tendrán incertidumbre, pero es la incertidumbre creada por nosotros al aleatorizar, no la creada por confounders. Y es cuantificable. ¡Mola!

Es extremadamente importante que la selección de todo lo que no vayamos a fijar sea aleatoria (tirando datos, sacando cartas,...), pues de no ser así podemos estar favoreciendo la existencia de confounders.

Si tenemos:

Z -> X  
Z -> Y  
X -> Y  

Y queremos entender el efecto de X sobre Y, necesitamos quitarnos de en medio el confounder Z. ¿Cómo lo hacemos? Fijando X:

P(Y | do(X = x))

Esto hace que desaparezca la flecha Z -> X, y por tanto Z ya no es un confounder. Fijamos X aleatoriamente para evitar insertar otros confounders con nuestra decisión.

Cuando hacemos esto con humanos, es importante que nadie conozca los valores asignados a X, ni los "pacientes" ni los "experimentadores". Esto es lo que se conoce como "doble ciego".

**Nota:** si conocemos todos los confounders y los controlamos, los resultados deberían ser correctos. Pero esto es imposible en ocasiones.

**Nota:** En ocasiones, lo que es imposible es fijar la variable de interés a un valor, y hay que trabajar con datos observacionales sí o sí. Judea dice que no debemos dar por hecho que estos experimentos son de segunda clase. Al no intervenir en el proceso para experimentar, los datos son más "puros". Sólo hay que tener en cuenta que las conclusiones sólo serán válidas si nuestras hipótesis (nuestro modelo causal) es correcto.


### Confounders

Un confounder es una variable que afecta (causa) tanto a la causa como al efecto.

Si:

Z -> X  
Z -> Y  
X -> Y  

Z es un confounder de X e Y. Confounding significa 'mezcla'. El efecto de X sobre Y está mezclado con la relación espuria entre X e Y causada por Z.

Definición: si P(Y | X) != P(Y | do(X)), entonces tenemos un problema de counfounding.

Ejemplo:
Si damos un medicamento a un grupo de personas más jóvenes que la media, la edad afecta tanto a la causa como al efecto.

Si no tenemos datos de edad, no seremos capaces de separar el efecto real de la correlación espuria.

Si los tenemos, basta con eliminar su influencia, analizando el efecto del medicamento por grupos de edad y promediando el resultado (pesando más los grupos de edad más numerosos en la población objetivo). En estadística, a este procedimiento se le denomina "adjusting for Z" o "controlling for Z".

Al parecer, es muy típico hoy en día el pasarse de controlar variables en los estudios. "A veces hasta llegar al punto de controlar lo que tratas de medir", como por ejemplo cuando controlas una variable mediadora (X -> Z -> Y); en ese caso, parecerá que X no tiene influencia en Y, pero es porque estás eliminando la influencia al controlar por Z.
> Pág 137-138, 153

Es posible eliminar el efecto de confounders usando deconfounders, que pueden o no coincidir con aquellos. Esto nos permite realizar predicciones sobre el resultado de una intervención sin necesidad de realizarla, **incluso con datos observacionales**. Para detectar deconfounders, puede usarse el back-door criterion.


### Back-door criterion

¿Cómo sé qué variables tengo que controlar para evitar confounders?

Repaso de flujos de información:

1. Si tengo A -> B -> C (cadena, B = mediador), controlar por B evita el flujo de información entre A y C.
2. Si tengo A <- B -> C (fork), controlar por B evita el flujo de información entre A y C.
3. Si tengo A -> B <- C (B = collider), no hay flujo de información entre A y C a menos que controle por B.
4. Controlar por descendientes de una variables es como controlar parcialmente por esa variable. Controlar por un descendiente de un mediador impide parcialmente el flujo de información. Controlar por un descendiente de un collider permite que fluya algo de información.

Para desconfundir dos variables X e Y, sólo tenemos que controlar variables para bloquear el paso de información entre X e Y por cualquier path no causal. Concretamente por cualquier path que empiece con una cabeza de flecha en X (back-door paths). Además, tenemos que asegurarnos de que ninguna de las variables que controlemos sean descendientes de X en un path causal, para evitar cerrar ese path.

Hay algunos ejercicios para practicar.
> Pág 157-162

---

## 5. The Smoke Filled Debate: Clearing the Air

Habla de lo extremadamente difícil que resultó llegar a la conclusión de que el tabaco causa cáncer de pulmón. Comenta cosas que se entendieron mal, y el enfoque que podría haberse dado desde un punto de vista de inferencia causal.

### Sensitivity analysis

Lo comentan de pasada como una posible solución a problemas causales, pero no explica lo que es. Creo que más adelante comenta que no asegura causalidad.
> Pág 176

---

## 6. Paradoxes Galore!

En este capítulo se explican unas cuantas paradojas clásicas desde el punto de vista de la causalidad. Bastante rayante, pero es interesante cómo lo que parece paradójico mirando sólo los datos deja de serlo si pintamos el modelo causal.

---

## 7. Beyond Adjustment: The Conquest of Mount Intervention

Este capítulo se centra en el segundo escalón de la causalidad: intervención. ¿Cómo podemos predecir el resultado de algo que aún no hemos probado?

**Solución:** si nuestro grafo causal es correcto y eliminamos todos los confounders que afectan a la relación a estudiar (controlando variables), podemos observarla de forma directa.


### Back-door criterion y back-door adjustment formula

Este es el método más sencillo si estamos seguros de que tenemos datos de todas las variables deconfounder (las necesarias para bloquear todos los back-door paths entre la intervención y la salida.

**¿Cómo lo hacemos?**
Medimos el efecto causal medio de una intervención estimando su efecto en cada "nivel" de los deconfounders (si estos son categóricos). Luego calculamos la media pesada de esos niveles, donde el peso de cada nivel es proporcional a su prevalencia en la población.

En el caso de que las variables sean continuas, hay dos opciones:
- Discretizar (a veces tiene sentido, otras veces no).
- Usar regresión lineal, metiendo como predictores todos los deconfounders además de la variable intervención. Meter deconfounders como inputs de una regresión lineal ajusta automáticamente por ellos. Por tanto, el coeficiente asociado a la intervención será la influencia de ésta en la variable final.
- Entiendo que esto puede generalizarse para modelos no lineales, y funciones en general. Aunque no me queda claro por la forma que tiene el autor de decirlo.

La fórmula del back-door adjustment es:

P(Y | do(X)) = sumatorio_z(P(Y | X, Z = z) * P(Z = z))

> Pág 220-224


### Front-door criterion

Cada vez que el efecto causal de X en Y es confounded por un conjunto de variables C y mediado por otras M, y las variables que median no tienen flechas que provengan de C, entonces podemos estimar el efecto de X en Y con datos observacionales.

Y <- C -> X -> M -> Y

Esto es clave cuando no tenemos datos de los confounders C (o ni siquiera sabemos cuáles son) y, por tanto, no podemos usar el back-door criterion.

La solución es calcular el efecto de X en M (no hay confounders), luego de M en Y controlando X (eliminamos el efecto de los confounders usando el back-door criterion) y combinando ambos resultados.

La fórmula matemática del libro (7.1) no me queda clara. ¿Está bien? Busca fuentes alternativas y ejemplos.

Al parecer, esto puede aplicarse con buenos resultados incluso cuando existe flecha de C a M si suponemos que la relación no es extremadamente fuerte, pues estaremos eliminando la mayor parte del efecto de C sobre las variables de interés.


### Do-calculus

El do-calculus está basado en tres reglas. Está demostrado que es un sistema de axiomas completo. Esto significa que, si un problema de causalidad puede resolverse, entonces puede hacerse con estas tres reglas.

Si no somos capaces de resolver un problema con estas tres reglas, entonces no queda otra que realizar un Randomized Controlled Trial.

**REGLA 1**  
Si estamos interesados en entender el efecto de X en Y, y el conjunto de variables Z bloquea todos los paths de W a Y después de eliminar todos los paths que llevan a X (es decir, si W no influye en Y al hacer do(X)), entonces:

P(Y | do(X), Z, W) = P(Y | do(X), Z)

*Esta regla permite añadir o eliminar observaciones.*

**REGLA 2**  
Si un conjunto de variables Z bloquea todos los back-door paths de X a Y, entonces si condicionamos en Z, do(X) es equivalente a see(X):

P(Y | do(X), Z) = P(Y | X, Z)

*Esta regla permite sustituir intervenciones por observaciones.*

**REGLA 3**  
Si no hay paths causales de X a Y (paths que sólo contengan flechas dirigidas hacia Y), entonces:

P(Y | do(X)) = P(Y)

Esto tiene sentido. Si hacemos algo que no afecta a Y, la distribución de probabilidad de Y no debería cambiar. Esta regla podría contener variables adicionales:

P(Y | do(X), Z) = P(Y | Z)

*Esta regla permite añadir o eliminar intervenciones.*



### Instrumental variables

Otra forma de resolver confounders de los que no tenemos datos es usar "variables instrumentales":

Z -> X -> Y  
X <- U -> Y  

Con este grafo, podemos calcular directamente el efecto de Z en X y el efecto de Z en Y. Combinando ambos, tenemos el efecto de X en Y. Si usamos modelos lineales:

r_xy = r_zy / r_zx

Hay un ejemplo en el libro en el que usa una variable instrumental para establecer el rango de efectos en el mejor y peor caso.
> Pág 245-255

---

## 8. Counterfactuals: Mining Worlds That Could Have Been

El capítulo empieza con un resumen muy escueto de los tres capítulos anteriores (los de intervenciones).
> Pág 259-260

Hasta ahora hemos visto relaciones causales generales, a nivel de población. El tercer escalón de la causalidad nos permite saber qué habría ocurrido en casos concretos si las circunstancias hubieran sido distintas.

Es el tercer escalón porque está demostrado que no podemos capturar la expresión de un counterfactual con una do-expression. 

Desde el punto de vista de los counterfactuals, la definición de causalidad es algo distinta: A causó B si B no hubiese ocurrido de no ocurrir A, si A era necesario para la ocurrencia de B.

Aquí podemos ver que hay varios tipos de causas: necesarias, suficientes, y necesarias y suficientes. 

Una forma de atribuir importancia a las causas es determinar la probabilidad de que cada causa sea necesaria y la probabilidad de que sea suficiente. Por ejemplo, si un incendio se produce por una cerilla y la existencia de oxígeno:
La probabilidad de que el oxígeno sea necesario es muy alta. 
La probabilidad de que el oxígeno sea suficiente es muy baja. 
La probabilidad de que la cerilla sea necesaria es alta. 
La probabilidad de que la cerilla sea suficiente es alta, dado que lo normal es que en una casa haya oxígeno. 
Por eso tiene más sentido pensar que es la cerilla la que ha causado el incendio, que pensar que ha sido el oxígeno. 
> Pág 290

La representación de un counterfactual en un diagrama causal es similar a la de una intervención. "Si X hubiese tomado el valor x..." es equivalente a do(X = x).

**Nomenclatura**  
Y_{X=x}(u) es el valor que habría tenido Y para el individuo u si hubiésemos asignado a X el valor x. También puede escribirse Y_x(u) si se entiende bien por el contexto.


### Potential outcomes (método "tradicional")

La idea de este enfoque es tratar las salidas potenciales como missing values (ver tabla 8.1, pág 273), y rellenarlas con distintos métodos:
- Buscar casos similares
- Regresión

Pearl dice que este enfoque es incorrecto, porque la forma de rellenar los huecos dependerá del diagrama causal subyacente. Usa como ejemplo la experiencia y educación para determinar el salario. Si hacemos matching a lo loco, no estamos teniendo en cuenta la relación entre experiencia y educación: alguien que ha estudiado una carrera durante 5 años, de no haberla estudiado, probablemente tendría 5 años más de experiencia, lo que se vería reflejado en su salario.


### Structural Causal Models (SCM)

Basándonos en nuestro grafo causal, un SCM es el conjunto de ecuaciones que representa las relaciones causales definidas en el grafo.

Por ejemplo, si:

Educación -> Experiencia  
Educación -> Salario  
Experiencia -> Salario  

Entonces tengo dos ecuaciones:

Salario = f_s(Educación, Experiencia, Unknowns_salario)
Experiencia = f_ex(Educación, Unknowns_experiencia)

Las funciones pueden o no ser lineales, son deterministas, y los unknowns capturan todo ruido/error, todos los efectos que hacen que mis predicciones no sean perfectas.

Diferencias con redes bayesianas:
- Es determinista. Esta característica es **fundamental** para la estimación de counterfactuals.
- En redes bayesianas, las flechas hacia una variable Y indican las probabilidades condicionadas en los valores de sus padres.
- En redes bayesianas causales, las flechas hacia una variable Y indican las probabilidades condicionadas a intervenciones (do) en sus padres.
> Pág 284

Diferencias con SEM:
- Realmente está basado en SEM, así que hay pocas diferencias.
- Las relaciones no tienen por qué ser lineales.
- Podemos tener variables discretas o continuas.
> Pág 285


### Cálculo de counterfactuals / potential outcomes con SCMs

Es un proceso de tres pasos definidos en las páginas 278 y 280:

1. **Abduction**. Usamos las ecuaciones de nuestro modelo y los datos del sample concreto para conocer los unknowns concretos para ese sample.

2. **Action**. Usamos el operador 'do' en la variable que vamos a cambiar, para reflejar la asunción counterfactual que estamos realizando.

3. **Prediction**. Usamos los unknowns y las ecuaciones del modelo post-modificación para predecir el outcome de nuestro counterfactual.


### Formas de refutar un modelo usando datos

- Comprobando si dos variables que deberían ser independientes (o condicionalmente independientes) realmente lo son.
> Pág 283

- Calculando numéricamente la influencia de una variable en otra y viendo que la influencia es nula.
> Pág ?? (recuerdo haberlo leído en alguna parte del libro, y tiene sentido)

---

## 9. Mediation: The Search For A Mechanism 

Cuando preguntamos "¿Por qué?" podemos buscar dos cosas:
La causa de un efecto
La explicación del mecanismo que relaciona una causa con un efecto. 
Este capítulo trata sobre esta segunda versión. 

X -> M -> Y   
X -> Y  

Más concretamente, el capítulo trata el problema de separar efectos directos de efectos indirectos.

**Efecto directo**  
Asociado a la flecha que conecta directamente la causa X con el efecto Y. 

**Efecto indirecto**  
El que pasa a través del mediador M. 

La mediación está en el nivel 3, el de los counterfactuals, y por tanto no puede representarse con el lenguaje de las intervenciones. 


### Separando efectos directos de efectos indirectos

Si tenemos un grafo causal:

X -> M -> Y  
X -> Y  

Para calcular el efecto directo de X sobre Y, debemos ver cómo varía Y al variar X, manteniendo M constante (para cada valor de M).

Hay dos formas de calcularlo, que tienen más o menos sentido en función del caso de uso.

**Controlled Direct Effect (CDE)**  
Forzamos el valor tanto de X como de M:  
CDE(M = 0) = P(Y = 1 | do(X = 1), do(M = 0)) - P(Y = 1 | do(X = 0), do(M = 0))  
CDE(M = 1) = P(Y = 1 | do(X = 1), do(M = 1)) - P(Y = 1 | do(X = 0), do(M = 1))

Hay un valor de CDE para cada valor de M. Se puede reportar así.

**Natural Direct Effect (NDE)**  
No forzamos el valor de M (dejamos que sea el “natural”):  
NDE = P(Y_{M = M0} = 1 | do(X = 1)) - P(Y_{M = M0} = 1 | do(X = 0))

En este caso, estamos en el tercer escalón de la causalidad. La idea es calcular qué hubiese pasado con Y si M hubiese tomado el valor M0 (el valor natural, sin forzar), y forzamos X a valores X = 1 o X = 0.

El efecto indirecto sólo puede calcularse de forma natural, pues no podemos “desactivar” el path directo manteniendo ninguna variable constante. La idea del efecto indirecto es: ¿cómo cambia Y si, manteniendo X constante, cambio M como si X se hubiese incrementado en 1 unidad?

**Natural Indirect Effect (NIE)**  
NIE = P(Y_{M = M1} = 1 | do(X = 0)) - P(Y_{M = M0} = 1 | do(X = 0))

---

¿Podemos suponer que…

Efecto total = Efecto directo + Efecto indirecto ?

En general no. Si el efecto directo y el indirecto interaccionan, sólo podemos separarlos caso a caso, para cada circunstancia concreta, usando counterfactuals. Pero si las relaciones son lineales, ¡sí que podemos! Los modelos lineales suponen la ausencia de interacciones entre variables.

En el caso lineal, cada flecha lleva asociada un número (el coeficiente de regresión), y el efecto de unas variables en otras, tanto directo como indirecto, se calcula directamente multiplicando esos números.

---

# 10. Big Data, AI and the Big Questions

Este capítulo habla sobre los límites del Big Data y el machine learning, y cómo podemos complementarlos con inferencia causal (o viceversa). 

Comenta, por ejemplo, que el trabajo de convertir el estimando en estimación puede ser imposible sin usar ML. 

También habla de transportabilidad. De combinar resultados / datos de experimentos distintos con éxito usando grafos causales para entender dónde están las diferencias. O de usar un modelo en un lugar distinto, sin tener que partir de cero, o incluso sin tener que recolectar datos. Elias Bareinboim ha automatizado esta parte. 

Comenta también que podemos hacer algo si nuestros datos no son representativos de nuestra población. Simplemente consideraremos que la toma de la muestra se ha visto influida por alguna variable. Por ejemplo, si sólo tenemos datos de gente hospitalizada, basta con añadir una flecha que vaya desde la variable "Hospitalización" hasta la nueva variable "Selección de población". Esto también lo ha trabajado Elias Bareinboim. 

Compara el Deep Learning con la cueva de Platón: sólo ve sombras, y sabe predecirlas muy bien. Pero una strong AI necesita ver el mundo en 3D (causalidad), no sólo sus proyecciones (correlaciones). 

Da su opinión sobre si alcanzaremos o no la IA fuerte, cómo podríamos llegar a ella y por qué no acabará con nosotros. 
