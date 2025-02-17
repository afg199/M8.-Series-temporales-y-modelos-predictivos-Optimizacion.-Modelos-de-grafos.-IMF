# M8.-Series-temporales-y-modelos-predictivos-Optimizacion.-Modelos-de-grafos.-IMF
Enunciado

Se han tratado áreas diferentes de la ciencia de datos y de la inteligencia artificial. El contenido de este módulo es un poco especial en el sentido de que ninguna de las áreas parece tener una conexión directa aparente. Sin embargo, se va a proponer como caso práctico final un problema en el que se relacionarán estos tres aspectos, para mostrar cómo en muchas situaciones reales una solución factible es combinar diferentes tecnologías que, en un principio, parecen poco relacionadas.
Así pues, en este caso práctico se va a analizar una serie temporal que muestra la producción energética mensual de una central entre 1985 y 2018. En la siguiente figura se puede ver la evolución de esta producción a lo largo de los años:

Se pide

El objetivo de este caso práctico es crear un modelo SARIMA o ARIMA, que pronostique los niveles de consumo de esta planta con la mayor precisión posible.
Para ello, la clave está en elegir la mejor combinación de parámetros del modelo p, d, q, P, D y Q que mejor modelen la serie. 
Existen métodos de optimización de parámetros como auto_arima, que intentan encontrar la mejor combinación acorde a algún objetivo, tal como AIC o BIC. El problema es que ese criterio lo evalúan sobre el mismo conjunto de entrenamiento, con la posibilidad, por lo tanto, de que exista overfitting.
Se pide, por tanto, desarrollar un método propio de optimización de los parámetros de un modelo SARIMA, teniendo en cuenta los siguientes aspectos:

Desarrollar una versión propia sencilla del método simulated annealing. Para ello, se pide que el alumnado investigue y se documente cómo es una implementación sencilla de este método en pseudo código o en Python.
El criterio de evaluación se va a realizar sobre un set de validación de unos 50 casos. Esto quiere decir que el modelo se ajustará haciendo uso del set de entrenamiento y se evaluará sobre el set de validación. Esto permitirá reducir las posibilidades de overfitting.
La métrica para evaluar será RMSE.
En muchas situaciones, es recomendable usar una versión subrogada de la métrica de evaluación, que la propia métrica en sí, por razones de rendimiento. Para ello, se asumirá que, si una evaluación para un conjunto de puntos tiene un valor más bajo que para otros en la métrica original, esto también ocurrirá en la métrica subrogada (concepto denominado monoticidad). En este caso, se quiere utilizar este enfoque. Para ello, en vez de evaluar de forma móvil el pronóstico de la siguiente observación en cada uno de los 50 casos de prueba (lo cual implicaría continuamente reentrenar el modelo añadiendo cada caso de test ya evaluado), el método evaluará directamente el pronóstico de las 50 siguientes observaciones y las comparará con los datos de validación.
Hay que tener en cuenta, según la metodología que se debe comprobar al final, que los residuos no están autocorrelacionados. Para ello, se debe realizar la simplificación de que el test Ljung–Box muestra que los residuos no están correlacionados en todos los retardos inferiores a seis. En caso de que lo estén, se puede asignar un valor muy grande a la solución para que el algoritmo de optimización descarte dicha solución.
La ventana de estacionalidad, valor m, no es un parámetro que se vaya a optimizar, ya que se sabe que el modelo tiene una estacionalidad anual. Es necesario, por lo tanto, fijar este valor de antemano.
Como una primera aproximación, se va a poner un número de 30 iteraciones para el algoritmo y una temperatura inicial de 10.

Hasta aquí se ha visto que, para resolver el problema, habrá que hacer uso de lo aprendido tanto en optimización como en series temporales. Pero ¿dónde se aplicarán algunos de los conceptos adquiridos en grafos? La clave está en un aspecto fundamental del algoritmo de recocido simulado: la elección de forma aleatoria de una solución vecina a otra.

¿Cómo se elegirá en el problema una solución vecina a otra ya existente? Una primera versión sencilla sería simplemente modificando en +1 o -1 uno de los diferentes parámetros. 

El modelo de simulated annealing realmente es equivalente a un proceso de random walk, sobre un grafo. Por lo tanto, se podrá construir un grafo con todas las soluciones, donde cada nodo, cada una de las posibles configuraciones de los parámetros y cada arista una dos soluciones que sean vecinas, es decir, aquellas en las que solo cambien un valor, en +1 o -1 uno de los parámetros. Para ello, habrá que tener en cuenta las siguientes peculiaridades:
Todos los valores de los parámetros son enteros y > = 0.
Se va a limitar la búsqueda de los parámetros a valores inferiores a ocho para los parámetros p, q, D y Q.
Para afinar más la solución, los valores de d y D solo podrán tener un valor por encima o un valor por debajo del valor que se obtenga haciendo uso de los test de hipótesis correspondientes.
Por lo tanto, habrá que construir un grafo con estas especificaciones y un método para ir de un nodo a uno de sus vecinos de forma aleatoria, el cual servirá para extraer vecinos de una solución, de forma aleatoria.
Por último, quedaría elegir cuál podría ser la solución inicial en el proceso de búsqueda. Existirá un conjunto de soluciones que pueden ser buenas como solución inicial en el proceso de búsqueda, para lo cual se pide tener en cuenta dos aspectos:
Hacer uso del conocimiento experto. En este caso se recomienda empezar con valores de d y D que hayan sido elegidos por nuestros test de hipótesis, y valores de p, q, P y Q, relativamente bajos, o aquellos que se consideren adecuados visualizando los gráficos de autocorrelación.
Buscar aquellos nodos de centralidad que tengan medidas de cercanía baja, lo cual implicaría que les costaría menos llegar a todos los nodos del grafo.

En resumen, se tendrá que:
Construir un modelo SARIMA que se ajuste de la mejor forma posible a la serie temporal.
Construir un algoritmo de optimización basado en simulated annealing, para elegir los mejores parámetros de un modelo.
Construir un grafo con todas las soluciones que permita elegir vecinos de forma aleatoria.


Base de datos: fichero “Electric_Production.csv”.
