# TFM: Predicción del scoring crediticio en el entorno de comparación de precios

## Máster en Data Science, ed.12 ##

## Autor: Pablo Morcuende Botello ##


### Introducción ###

El objetivo de este TFM es determinar si se puede precedir el scoring crediticio a partir de los datos que el usuario rellena en la cotización del seguro del automóvil en un entorno de comparación de precios (Rastreator, acierto.com, ...).

Para su elaboración, se ha empleado un fichero de cotizaciones con información parcial al que se le ha añadido ruido. Además, se han anonimizado las columnas y se han traducido los valores. De esta forma se garantiza el cumplimiento de la legislación vigente y se protege el conocimiento interno de la compañía.

#### Antecedentes ####

El scoring crediticio es un factor de riesgo utilizado por la compañía de seguros para calcular la prima de cada individuo. No es un dato calculado por la propia compañía, sino que se compra a un proveedor externo que cobra por consulta.

Actualmente, cuando se realiza una cotización del seguro de automóvil **en un entorno diferente al del comparador de precios**, se llama a la aplicación del proveedor, quien proporciona el dato del scoring crediticio a la compañía de seguros para que pueda completar la cotización y ofrecer la prima al cliente.

**En el entorno del comparador de precios** el proceso es distinto: Debido al gran volumen de solicitudes de precio que llegan a través de estas plataformas y a su bajo ratio de conversión, para optimizar el coste de las consultas, la compañía decide ofrecer la prima simulando el mismo scoring crediticio para todos los clientes.

Si el cliente muestra interés por este primer precio simulado, se le transfiere al entorno de la compañía (web o call center), donde se realiza la consulta del scoring crediticio y se le ofrece su precio cierto.

#### ¿Por qué es un tema relevante? ####

Explorar la posibilidad de predecir el scoring crediticio es un tema muy interesante para la compañía porque supone beneficios en muchos y muy variados aspectos:

  * **Experiencia de usuario**: La solución actual sólo es válida o acertada si el cliente potencial tiene realmente el scoring crediticio que se ha simulado, pues de lo contrario, siempre va a experimentar un cambio de su prima real respecto a la que ha sido simulada en el entorno de comparador de precios.
  
    * **Ventaja en la experiencia de usuario**: Poder predecir el scoring crediticio va a mejorar la experiencia de usuario, ya que la compañía va a ser capaz de poder ofrecer un precio en el entorno del comparador que no diferirá del precio real.
    
  * **Eficacia en la conversión**: Actualmente, en la mayoría de las ocasiones, el precio real es superior al simulado, lo que provoca ratios de conversión muy bajos en este entorno.

    * **Ventaja en la conversión**: Ofrecer un precio real similar al simulado va a mejorar los ratios de conversión, pues el cliente potencial no verá diferencias sustanciales entre ambos precios.
    
  * **Mejora en los costes de gestión interna**: Ofrecer un precio simulado que suele ser inferior al real genera una gran cantidad de leads (cliks en la web de la compañía y llamadas al call center de ventas) que atender y que, debido a los bajos ratios de conversión, no van a ser convertidos en pólizas. 
  
    * **Ventaja en costes**: Tener un precio real similar al simulado va a generar menos leads (pero con mayor probabilidad de conversión), por lo que se pueden optimizar los recursos dedicados a su atención.
    
### Descripción de los datos ###

Como se ha comentado anteriormente, para la realización del TFM se han empleado datos de muestra que los usuarios rellenan en el entorno del comparador de precios y que son transferidos a las distintas compañías que participan en el panel de comparación. Además, se ha simulado el cruce de estos datos con una base de datos de la propia compañía que aporta información adicional.

### Metodología y técnicas empleadas ###

* **Importación de los datos**: Se ha optado por realizar una función con un bucle `for()` que importa las BBDD mensuales y posteriormente las une en un único fichero. Después se añade la información procedente de la BBDD interna de la compañía.

* **Tratamiento de datos**: 

    * **Missing values**: En el estudio de los missing values se ha decidido imputar la moda en las variables categóricas y el valor válido inmediatamente anterior en el caso de las variables continuas. En el caso del target, se eliminan los registros en los que no estaba informado.
    
     * **Target**: El target es una variable que tiene 23 posibles valores, algunos de ellos con muy poca masa, lo que dificulta su predicción. Para solventar este inconveniente, se ha optado por realizar cuatro agrupaciones: Score 1, score 2, score 3 y score 4.

    * **Análisis de las variables y feature engeneering**: Se realiza un análisis diferenciado de las variables categóricas y continuas.
    
        **Continuas**: Para el análisis de las variables continuas se han elaborado diferentes boxplot con el objetivo de observar su distribución y decidir qué hacer con sus valores extremos.
        
        **Categóricas**: Para el análisis de las variables categóricas se han elaborado dos funciones: `info_vars()` se crea para analizar los estadísticos básicos de cada variable y `dist_freq()` para observar la distribución de cada una de ellas.
          
* **Preprocesado de variables categóricas**: El dataset empleado tiene una gran cantidad de variables categóricas, las cuales a su vez, tienen muchos valores únicos. Por ello, se decide aplicar _mean encoding_ a las variables categóricas no binarias. Esta técnica genera únicamente 4 nuevas columnas por cada variable (una por cada valor posible del target).
   
   Haber utilizado una técnica como el _one hot encoding_ habría supuesto la creación de multitud de variables adicionales, lo que hubiese dilatado considerablemente el tiempo de computación de las técnicas de modelización elegidas.

* **Selección de variables**: Para la selección de variables se emplea un método basado en árboles de decisión. La elección de esta técnica se debe a su precisión y también a que es muy fácil de interpretar y explicar.

   En clasificación, como es el caso de este trabajo, se utiliza el coeficiente de Gini como medida de impureza. Aquellas variables que más contribuyen a que disminuya la impureza son las más importantes. En este caso, se ha seleccionado un threshold del 0.003, lo que equivale a una selección de variables que recogen el 87,3% de la impureza de los datos.
   
* **Modelización**: Para evaluar los modelos realizados se parte de un modelo Naive, que mide el acierto si se elige siempre la clase mayoritaria (en este caso ronda el 41%). La métrica de evaluación escogida es el **accuracy**, ya que lo que se prima es el acierto en la elección entre los distintos valores posibles que puede tomar el target.

  Se han realizado los siguientes modelos: Regresión logística multiclase, random forest, SVM y XGBoost. Con el objetivo de no demorar demasiado el tiempo de ejecución, para la selección de los hiperparámetros se ha optado por emplear la función `RandomizedSearhCV()`, que realiza una búsqueda aleatoria de los mismos dentro de las alternativas que se le indican.
   
  El resultado de los modelos aplicados es el siguiente:
  
     Modelo  |  Accuracy
  ---------- | -----------
  Naive      |  41,35%
  R. Log     |  51,85%
  R. Forest  |  52,24%
  SVM        |  51,40%
  XGBoost    |  54,52%
   
  Una vez observado el accuracy de los distintos modelos, **se elige el XGBoost**. 
   
  La validación del modelo en test da como resultado un accuracy del 54,79%, algo superior al accuracy de entrenamiento.
  
  Para certificar que el modelo es correcto y que el accuracy del test se mueve en valores razonables, se simulan 10.000 conjuntos aleatorios de test y se define un intervalo de confianza del accuracy con los valores correspondientes al percentil 2,5 y 97,5.
  
  Este intervalo queda definido por los valores situados entre el 54,11% y el 55,45%, con lo que el resultado de la predicción en test está dentro del mismo.
  
 ### Resultado ###
  
 Se muestra la matriz de confusión del XGBoost:
  
  ![alt text](https://github.com/pablomb08/TFM_scoring_prediction/blob/master/img/confussion_matrix_xgboost.PNG) 
  
Analizando la matriz, se observa que el modelo predice correctamente las clases 1 y 2 del target, no obstante, tiene más dificultades para clasificar las clases 3 y 4.
   
### Conclusión ###

Se puede concluir que, con los datos disponibles en la tarificación de un seguro, un modelo no es capaz de predecir con gran acierto entre las 4 posibles clases de score. Sin embargo, a través de un modelo predictivo sí se puede identificar con una precisión aceptable si el score pertenece a las clases 1 o 2, lo que permitiría elaborar reglas de negocio que contribuyan a atraer o rechazar esta tipología de cliente.
