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
    
  * **Mejora en los costes de gestión interna**: Actualmente, en la mayoría de las ocasiones, el precio real ofrecido es superior al simulado con el mejor scoring crediticio, lo que provoca ratios de conversión muy bajos en este entorno.

    * **Ventaja en costes**: Ofrecer un precio real similar al simulado va a mejorar los ratios de conversión, pues el cliente potencial no verá diferencias sustanciales entre ambos precios.
