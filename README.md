>> **_Nota:_** Los resultados de los _notebook_ se exportaron en _.html_ para garantizar el funcionamiento de los gráficos interactivos de _Plotly_ ya renderizados y su sencilla visualización en cualquier navegador.

Debes crear un report reproducible (en un notebook en Python o R) siguiendo estos pasos:  

**1.**  **EDA sencillo y básico** (A Revenue Management le preocupan más los resultados que los insights)  
Los resultados del análisis exploratorio básico se encuentran en el Jupyter Notebook exportado como `EDA.html`. En este documento se incluyen las principales operaciones más básicas de un análisis exploratorio preliminar.     

- **¿Qué variables parecen ser las más predictivas?**  
A partir del análisis exploratorio -principalmente- bivariado, parecen tener bastante importancia las variables _IsRepeatedGuest_, _ReservedRoomType_ y _Country_; sin embargo, para conocer la cantidad de información aportada de los predictores habría que considerar métodos que tuvieran en cuenta su interacción.  
Ejemplos sencillos podrían ser las permutaciones a partir de modelos lineales, análisis de _Feature Importance_ basados en árboles de decisión ([RandomForest](https://scikit-learn.org/stable/auto_examples/ensemble/plot_forest_importances.html), [XGBoost](https://mljar.com/blog/feature-importance-xgboost/), etc.), o más complejos basados en teoría de juegos como los implementados en la librería para _Python_ [Shap](https://shap.readthedocs.io/en/latest/).  
Además, todo esto podría acompañarse de métodos estadísticos _clásicos_, como ANOVA (de una o dos vías), MANOVA, [HJ Biplot](https://diarium.usal.es/pgalindo/files/2012/07/0article-HJ-1986.pdf) (para conocer los ejes de máxima varianza) o [Biplot Canónico](http://ve.scielo.org/scielo.php?script=sci_arttext&pid=S0378-18442004000100009&lng=es&nrm=iso&tlng=es) (para conocer los ejes de máxima discriminación).  
La cuantificación de la información aportada de los predictores es algo que requiere de tiempo y cuidado para poder realizar todas las pruebas necesarias, atendiendo en todo momento al objetivo del análisis y al carácter de los datos para poder aplicar las pruebas necesarias y procedentes.


**2.** **Preparación de los datos**

- **¿Le darías el mismo tratamiento a todas las variables?**  
No. Los tratamientos de cada variable deben hacerse considerando su nivel de medición (nominal, ordinal, intervalo o razón), las características intrínsecas de cada una (distribución, curtosis, etc) y al objetivo que se quiera conseguir.
Los estadísticos aplicables a las variables _CustomerType_ y _ADR_ no son los mismos. En el caso de las variables _ADR_ y _Children_, aún siendo ambas cuantitativas (y de razón), los estadísticos para explorarlas podrían no tener mucho sentido en según que caso; por ejemplo, un rango intercuartílico podría ser mucho más descriptivo en el caso del _ADR_ (pues _Children_ es discreta y toma pocos valores), lo contrario que podría ocurrir con el valor modal; si consideráramos la variable _Children_ como cualitativa (ya que es discreta y toma pocos valores), la categoría modal ofrecería información sobre el número de niños más frecuente, cosa que no tendría sentido de aplicarse al _ADR_, ya que es continua.
Del mismo modo, también podría variar, en función del objetivo al que sirva cada una; las transformaciones que apliquemos a cada una (como por ejemplo el escalado o la imputación de valores perdidos) dependerán de la utilización de la variable y su contexto.


- **¿Utilizarías todas las variables o eliminarías alguna de ellas por no ofrecer valor?**  
La eliminación de variables por cuestiones puramente estadísticas es algo que se suele desaconsejar. Como ocurre en el ámbito del _Credit Scoring_, puede haber presencia en los modelos de riesgo que incorporen variables poco atractivas desde el punto de vista del aporte de información (algunas recogidas en los acuerdos de Basilea para microfinanzas), pero que sin embargo, no existe justificación teórica para eliminarlas. Debe existir siempre un equilibrio entre la técnica y la teoría, para lo cuál es esencial la buena comunicación entre el científico de datos y las personas que poseen un mayor conocimiento teórico en materia.  
Todo esto no hace mención a las variables de metadatos, a aquellas cuya función no es la de aportar información utilizable de manera directa en el análisis o a aquellas variables con varianza 0 (constantes), como por ejemplo identificadores de dispositivos de medición, variables de control de funcionamiento, etc. Tampoco hace referencia a variables con un alto porcentaje de valores perdidos o mediciones erróneas, ya que, aunque su inclusión en los modelos esté respaldada por la teoría, el integrarlas no ayudaría a la obtención de buenos resultados.

**3.** **Construcción del modelo**

  - **¿Qué tipo de modelo es el más adiente? Support Vector Machines, Decision Tree, Logistic Regression, K-Means, Redes Neuronales, Random Forest, Gradient Boosting, Naive Bayes, PCA, Lasso Regression...**  
 En principio, los algoritmos basados en árboles y las redes neuronales ofrecen muy buenos resultados en problemas de clasificación. Un XGBoostClassifier ofrece buenos rendimientos, y su implementación es sencilla.  
 Es probable que se consigan mejores resultados diseñando una arquitectura de red neuronal para este problema; sin embargo, el diseño e implementación de una red neuronal específica es una tarea que requiere de más cuidado a la hora del entrenamiento y puesta en producción, por lo que depende de las necesidades de cada proyecto elegir el modelo adecuado.  
 Una buena aproximación podría ser utilizar una librería de _auto machine learning_ como [TPOT](http://epistasislab.github.io/tpot/) para la elección de un modelo; sin embargo la búsqueda del modelo óptimo con técnicas de _autoML_ es un proceso que requiere tiempo y recursos computacionales, así que como siempre, la elección de la técnica debe hacerse teniendo en cuenta las particularidades de cada caso.
 
  - **No es necesario probar entre cientos de modelos con distintos hiperparámetros para saber cuál da mejores resultados, basta con justificar la elección.**   
  En el archivo `TRAINING.html` se encuentra un pequeño pipeline para el entrenamiento de un perceptrón multicapa sencillo, pero que ofrece muy buenos resultados. Su _accuracy_ en _train_ y _test_ es de 0.99 y 0.98 respectivamente.  
  Debido a que la muestra está desbalanceada, se aplica un _Random Under Sampling_ para balancearla.
Si bien un perceptrón simple no es capaz de solventar problemas que no son linealmente separables, el multicapa sí es capaz; de hecho, en este caso, se consiguen resultados que pueden ser un muy buen punto de partida para la construcción de un modelo más específico y robusto.  

**4.** **Evaluación del modelo**
 
 - **¿Cuál sería el benchmark a batir? ¿Estamos contentos con los resultados obtenidos?**  
 Los resultados pueden considerarse un buen punto de partida, ya que el _accuracy_ es realmente bueno para ser un modelo tan sencillo. Sería interesante construir una red neuronal de manera más detallada, ya que, para este caso, los errores en la predicción pueden corresponderse con pérdidas económicas o descontento de los clientes.  
Es probable conseguir mejores resultados probando distintos tipos de estandarizado y probando con distintas funciones de activación; sin embargo se puede correr el riesgo de sobreajuste.  
También podría desarrollarse un sistema de aprendizaje federado (en caso de que la centralización de datos sea costosa) con -por ejemplo- [FLWR](https://pypi.org/project/flwr/), que ofrece una implementación sencilla; de esta manera se podrían escalar y generalizar los resultados del/los modelo/s a un número mayor de hoteles y a menor coste, ya que en cada uno de los nodos se entrenarían modelos "sencillos" con las particularidades locales que posteriormente se agregan en un modelo general central y robusto, todo esto garantizando la anonimidad de los datos, ya que solamente se compartirían los pesos de los modelos y no la información de los clientes.

 - **¿Qué resultados podríamos esperar con este modelo en producción?**  
 Según las pruebas realizadas en el notebook, podríamos estar clasificando correctamente el 98% de casos, con una precisión mayor en el caso de las cancelaciones. Además, debido a la sencillez del entrenamiento, el modelo podría ponerse en producción a un coste computacional bajo, con un análisis de su ciclo de vida sencillo, ya que la necesidad de reentreno podría evaluarse y no comportaría una demanda de recursos especialmente alta en caso de ser necesaria. Esto ayudaría a aumentar la capacidad de reacción del sistema en caso de que la precisión se vea afectada por la incorporación de datos nuevos.  
 Su puesta en producción podría realizarse sencillamente con Docker y Portainer, lo que permite tener varios _deployments_ a la vez para garantizar la continuidad del sistema, así como para probar distintos modelos y poder evaluarlos en un entorno real.