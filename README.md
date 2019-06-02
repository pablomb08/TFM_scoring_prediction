# TFM: Predicción del scoring crediticio en el entorno de comparación de precios

## Máster en Data Science, ed.12 ##

## Autor: Pablo Morcuende Botello ##


### Introducción ###

El objetivo principal de mi TFM es determinar si se puede precedir el scoring crediticio a partir de los datos que el usuario rellena para realizar una cotización del seguro del automóvil en un comparador de precios (Rastreator, acierto.com, ...).

#### Antecedentes ####

El scoring crediticio es un factor de riesgo utilizado por la compañía de seguros para calcular la prima de riesgo de cada individuo. No es un dato calculado por la propia compañía, sino que se compra a un proveedor externo que cobra por cada consulta que la compañía realiza.

Actualmente, cuando se realiza una cotización del seguro de automóvil **en un entorno diferente al del comparador de precios**, se llama a la aplicación del proveedor, quien proporciona el dato del scoring crediticio a la compañía de seguros para que pueda completar la cotización y ofrecer la prima al cliente.

**En el entorno del comparador de precios** el proceso es distinto: Debido al gran volumen de solicitudes de precio que llegan a través de estas plataformas y a su bajo ratio de conversión, para optimizar el coste de las consultas, la compañía decide ofrecer la prima simulando que el cliente tiene el mejor score crediticio posible.

Si el cliente muestra interés por este primer precio simulado, se le transfiere al entorno de la compañía (web o call center), donde se realiza la consulta del scoring crediticio y se le ofrece su precio cierto.

#### ¿Por qué es un tema relevante? ####

Explorar la posibilidad de predecir el score crediticio es un tema muy interesante para la compañía porque supone beneficios en muchos y muy variados aspectos:

  * **Experiencia de usuario**: La solución actual sólo es válida o acertada si el cliente potencial tiene realmente el mejor scoring crediticio, pues de lo contrario, siempre va a experimentar un incremento de su prima real respecto a la que ha sido simulada en el entorno de comparador de precios.
  
    * **Ventaja en la experiencia de usuario**: Poder predecir el scoring crediticio va a mejorar la experiencia de usuario, ya que la compañía va a ser capaz de poder ofrecer un precio en el entorno del comparador que no diferirá del precio real.
    
  * **Eficacia en la conversión**: Actualmente, en la mayoría de las ocasiones, el precio real ofrecido es superior al simulado con el mejor scoring crediticio, lo que provoca ratios de conversión muy bajos en este entorno.

    * **Ventaja en la conversión**: Ofrecer un precio real similar al simulado va a mejorar los ratios de conversión, pues el cliente potencial no verá diferencias sustanciales entre ambos precios.
    
  * **Mejora en los costes de gestión interna**: Ofrecer un precio simulando el mejor scoring crediticio posible genera muchos leads (cliks en la web de la compañía, pero sobre todo, llamadas al call center de ventas) que atender y que, debido a los bajos ratios de conversión, no van a ser convertidos en pólizas. 
  
    * **Ventaja en costes**: Tener un precio real similar al simulado va a generar menos leads (pero con mayor probabilidad de conversión), por lo que se pueden optimizar los recursos dedicados a su atención.
    
### Descripción de los datos ###

_Nótese que, debido a restricciones en el uso de los datos, se han anonimizado las columnas y se han traducido la gran parte de los valores. Soy consciente que esta limitación va a dificultar la lectura y comprensión de los resultados, pero era la única forma de poderlo llevar a cabo._

Para la realización del TFM se han empleado los datos que los usuarios rellenan en el entorno del comparador de precios y que son transferidos a las distintas compañías que participan en el panel de comparación con el fin de que éstas puedan ofrecer su prima.

Además, estos datos han sido cruzados con una base de datos de la propia compañía que aporta información adicional sobre los objetos de riesgo (en este caso, los vehículos).

### Metodología y técnicas empleadas ###

* **Importación de los datos**: He optado por realizar una función con un bucle `for()` que importa las BBDD mensuales y posteriormente las une en un único fichero. Posteriormente transformo a tipo "object" las variables numéricas que no van a ser tratadas como tal y por útimo añado la información procedente de la BBDD interna de la compañía.

* **Tratamiento de datos**: 

    * **Missing values**: En el estudio de los missing values he optado por imputar la moda en las variables categóricas y el valor válido inmediatamente anterior en el caso de las variables continuas. En el caso del target, he decidido eliminar los registros en los que el target no estaba informado.
    
     * **Target**: El target es una variable que tiene 23 posibles valores, algunos de ellos con muy poca masa, lo que dificulta su predicción. Para solventar este inconveniente, se ha decidido realizar cuatro agrupaciones: Score alto, score medio, score bajo e impagos.

    * **Análisis de las variables y feature engeneering**: Se realiza un análisis diferenciado de las variables categóricas y continuas.
    
        **Continuas**: Para el análisis de las variables continuas se han elaborado diferentes boxplot con el objetivo de observar su distribución y decidir qué hacer con sus valores extremos.
        
        **Categóricas**: Para el análisis de las variables categóricas se han elaborado dos funciones: `info_vars()` se crea para analizar los estadísticos básicos de cada variable y `dist_freq()` para observar la distribución de cada una de ellas.
          
* **Preprocesado de variables categóricas**: El dataset empleado tiene una gran cantidad de variables categóricas, las cuales a su vez, tienen muchos valores únicos. Haber utilizado una técnica basada en el _one hot encoding_ habría supuesto la creación de multitud de variables adicionales, lo que hubiese dilatado considerablemente el tiempo de computación.

   Para resolver esta cuestión, he decidido aplicar _mean encoding_ a las variables categóricas no binarias. Esta técnica genera      únicamente 4 nuevas columnas por cada variable (una por cada valor posible del target).

* **Selección de variables**: Para la selección de variables he utilizado un método basado en árboles de decisión. Me he decantado por esta técnica por su precisión y también porque es muy fácil de interpretar y explicar.

   En clasificación, como es mi caso, se utiliza el coeficiente de Gini como medida de impureza. Aquellas variables que más contribuyen  a que disminuya la impureza son las más importantes. En mi caso, he seleccionado un threshold del 0.003, lo que equivale a una selección de variables recogen el 89,3% de la impureza de los datos.
   
