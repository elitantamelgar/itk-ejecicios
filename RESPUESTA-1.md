# Prueba Distribuido (Base de Datos)

1. Todos los meses se carga información de potenciales clientes de fuentes externas.

2. En cada proceso de carga de información, por cada potencial cliente se tiene más de 100
variables (1 registro por cada variable) y se tiene alrededor de 15MM de potenciales
clientes.

3. Por normativa se almacena información del cliente los últimos 48 meses.

4. Para el score de riesgos se necesita las variables del último mes, así como variables de
meses anteriores. Para el score también se utiliza otras fuentes internas de información.

5. Apenas llega la información mensual, se debe de obtener en el menor tiempo posible el
procesamiento en Batch del score de riesgo de los clientes potenciales

Las preguntas:

- ¿Cómo diseñarías la base de datos en Oracle?
- ¿Qué herramientas y mecanismos usarías para maximizar el rendimiento de la Base de
datos?

## __GRAFICA__
---



## __Respuesta 1__
---

Bueno en la grafica muestro ciertas funciones y tablas que detallo en la respuesta 2.

- Usar tablas intermedia
- Usar tabla particionada
- Uso de data pump 
- Uso de insert bulk

## __Respuesta 2__
---
En mencion a una herramienta como tal oracle cuenta con su propia herramienta para monitoreo y como es habitual pl/sql.

Dado el caso al tema de cargar de fuentes externas lo mas recomendable en este caso seria tener la data un archivo csv(no es tan corruptible como un texto plano) separado por comas; dado ello tendriamos un proceso(__process_billing__) que se encargue de cargar mediante la opcion de __insert bulk__ la informacion, como podemos apreciar en la grafica. 

Recomendaria a la tabla historia generarle particiones dado la cantidad de informacion.

• __process_billing__: Este proceso como se menciona usara la funcion de insert masiva a una tabla(__TABLE_STEP__) y luego de ello usaremos __data pump__(esta opcion es manual pero dada la cantidad de informacion es necesaria) para poblar la tabla historia(__TABLE_CUSTOMER_POTENTIAL_RECORD__)

• __TABLE_STEP__: Esta tabla unicamente tendra una llave primaria, dado que los indices generan lentitud en el proceso de insercion masiva y sera recreada cada vez que el proceso se ejecute(quiere decir que se eliminara y volvera a crear), el motivo principal es por si es usada para eliminacion de data, ello genera que la tabla se fragmente a la larga esto hace que el proceso de insercion sea lento. __Adicinaria dentro de la variables año y mes.__

• __TABLE_CUSTOMER_POTENTIAL_RECORD__: Esta tabla como se menciona en el enunciado sera una historica, yo usaria esta tabla particionada por el volumen de informacion y los meses en menciono, ya desde la opcion de __data pump__ podemos contralar ciertas cosas que sean necesarias o no dentro del proceso de llenado. Dicho esto tambien la tabla como tal puede tener los indices necesarios; asi como cualquier tipo de funcion.

<br>

Segun el enunciado indica que el final de esta data es el score.

• __process_batch_score__: Indica el enunciado que se tendra otras fuentes para sacar dicha informacion, en tal caso como ya se tiene informacion en la tabla historica(__TABLE_CUSTOMER_POTENTIAL_RECORD__ particionada) se extraeria la informacion de cual sea necesaria en un archivo csv en un bucket y el proceso tendria que realizar el cruzado de informacion; como ouput dejaria un archivo csv en otra ruta bucket con la data tratada y insertada por insert bulk a la tabla __TABLE_CUSTOMER_POTENTIAL_SCORE__

Esta infomacion entenderia que se tendria almacenada luego para su consultal; aqui recomendaria pasarla a base de datos no relacionales en tal caso un dynamobd.