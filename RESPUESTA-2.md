# Prueba Distribuido (Servicios)

1. Todos los días se procesan pagos de créditos de prestamos personales de los clientes.

2. Los clientes pueden realizarnos pagos desde diferentes canales: Canal Digital (APP y
Banca por Internet), Canal Agentes, Cajeros y Tiendas.

3. Los pagos diarios tienen un volumen de 10 mil diarios.

4. Los prestamos personales tienen 3 cores distintos de acuerdo al tipo de préstamo
(crédito efectivo, hipotecario, vehicular).

5. El core financiero del banco se encuentra en un computador central (mainframe).

6. Los canales se encuentran en Cloud.

Las preguntas:
- ¿Cómo diseñarías el servicio de Pago de créditos? Considerando procesos online y procesos
batch
- ¿Qué tipo de seguridad utilizarías para los servicios?
- ¿Cómo manejarías la multicanalidad?


### Respuesta 1
---
En esta pregunta usare como arquitectura de solucion nube AWS. Como tendre expuesta un __API GATEWAY__ invocado por __dyamic api https__.

1. Tendria como barrera dada la concurrencia de pago __balanceadores__ hacia los microservicios(dicho esto estaran en 4 instancias distintas), para la alta respuesta a la solicitudes de los canales, con respecto a los procesos de pagos online(aqui tambien pondria procesos batch de tener otras cosas de informacion que pueden servir al proceso).

3. Procesos batch, los microservices dejaran la informacion en colas independientes por cada canal. Tendremos dentro de un instacia EC2 __procesos worker__ por __canal__ de escucha a la cola para que se comuniquen con los cores que corresponda y al mainframe via VPC.

### Respuesta 2
---

1. Como se muestra en el diagrama de arquitectura usare API GATEWAY con invocacion dynamic api https, incluyendo un WAF.

2. Sesionar en plataformas digitales usando tokens mediante cognito y nos da la libertad de usar politicas de usuario.

### Respuesta 3
---
1. Como se muestra en el diagrama manejo la multicanidad brindando __API GATEWAY__ por cada canal, esto nos genera mayor performance en cuanto las peticion de la mismo.
