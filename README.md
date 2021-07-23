En este Tutorial aprenderemos a configurar un balanceador de cargas y a la vez obtener un certificado https para nuestro sitio web a travez de la plataforma aws

Primero empezaremos a describir los conceptos importantes

  Balance o balanceo de carga

El balance o balanceo de carga es un concepto usado en informática que se refiere a la técnica usada para compartir el trabajo a realizar entre varios procesos, ordenadores, discos u otros recursos. Está íntimamente ligado a los sistemas de multiprocesamiento, o que hacen uso de más de una unidad de procesamiento para realizar labores útiles.

El balance de carga se mantiene gracias a un algoritmo que divide de la manera más equitativa posible el trabajo, para evitar los así denominados cuellos de botella.

  Certificado SSL 

SSL es el acrónimo de Secure Sockets Layer (capa de sockets seguros), la tecnología estándar para mantener segura una conexión a Internet, así como para proteger cualquier información confidencial que se envía entre dos sistemas e impedir que los delincuentes lean y modifiquen cualquier dato que se transfiera, incluida información que pudiera considerarse personal. Los dos sistemas pueden ser un servidor

Ahora comenzaremos con los pasos necesarios:

Requisitos:

Tener un dominio de preferencia en la plataforma aws

Tener una cuenta en aws

Tener un servidor web con algun proyecto que desemos desplegar, en esta ocacion yo implementare este proyecto:

https://github.com/creativetimofficial/awesome-landing-page.git



Primer paso:

En el panel de EC2, seleccione el elemento de menú "Equilibrio de carga -> Equilibradores de carga".

Haga clic en el botón "Crear balanceador de carga".

![image](https://user-images.githubusercontent.com/36380066/126813766-1bd661d7-f713-47d3-a9c4-fed4f44392a2.png)

Segundo paso:

En la página "Seleccionar tipo de balanceador de carga", seleccione la opción "Balanceador de carga de aplicaciones" y haga clic en "Crear".

![image](https://user-images.githubusercontent.com/36380066/126813920-dbff4913-8780-49f9-985c-2765d343325d.png)


Tercer Paso:

En la siguiente página "Configurar Load Balancer":

   Ingrese un nombre para el balanceador de carga y especifique el esquema como "Orientado a Internet".

  En la sección "Oyentes", asegúrese de que haya un oyente HTTP en el puerto 80 del balanceador de carga. Haga clic en el botón "Agregar oyente" y agregue un segundo       oyente HTTPS en el puerto 443 del balanceador de carga. Esto configura el balanceador de carga para manejar tanto HTTP como HTTPS solicitudes de los clientes.

  En la sección "Zonas de disponibilidad", seleccione la misma VPC que utiliza su instancia de aplicación Bitnami y seleccione una subred de cada zona de disponibilidad.

  Haga clic en el botón "Siguiente: Configurar las opciones de seguridad" para continuar.
  
  ![image](https://user-images.githubusercontent.com/36380066/126817936-bda8de36-216f-496a-8e26-2d82636cbd50.png)


  ![image](https://user-images.githubusercontent.com/36380066/126818032-4efe62eb-814b-4feb-a330-b225f86a72df.png)


  Yo seleccione estas dos zonas ya que mi instancia se encuentra en una de ellas, ya que se selecionan zonas en las cuales estara disponible
  
  
En la página "Configurar ajustes de seguridad", seleccione la opción "Solicitar un nuevo certificado de ACM" para crear un nuevo certificado SSL para su equilibrador de carga. Esto iniciará AWS Certificate Manager (ACM) en una nueva ventana.


![image](https://user-images.githubusercontent.com/36380066/126818765-e35107ad-690e-47cd-a83a-40af7e232563.png)


Dentro de AWS Certificate Manager, en la página "Solicitar un certificado", ingrese su nombre de dominio u o subdominio. Haga clic en "Revisar y solicitar" para revisar y confirmar la solicitud.

en mi caso será un subdominio

NOTA: Al solicitar el certificado, especifique las formas DOMAIN y www.DOMAIN del nombre de dominio para asegurarse de que el certificado sea válido para ambos escenarios.

![image](https://user-images.githubusercontent.com/36380066/126818932-ea5d920a-fd37-4e25-9165-843d69ceee81.png)

Tenemos dos formas de validad el dominio:

Validación Correo Electronico: se enviará un correo electrónico al propietario registrado del dominio con instrucciones para confirmar la solicitud de certificado validando el dominio. El dominio aparecerá en AWS Certificate Manager con el estado establecido en "Validación pendiente".

Validación de DNS: Se agrega registro CNAME a la configuración de DNS de su dominio. El procedimiento para agregar registros CNAME depende de su proveedor de servicios DNS

En mi caso validare a traves de Dns 

![image](https://user-images.githubusercontent.com/36380066/126820803-f933881c-9200-45b1-af8e-73ba3e059864.png)


![image](https://user-images.githubusercontent.com/36380066/126820838-b5541bae-4890-4c67-8c75-aa72b987ab2a.png)


![image](https://user-images.githubusercontent.com/36380066/126820896-c6c2599a-5d47-409e-a543-b80dfde5b798.png)


![image](https://user-images.githubusercontent.com/36380066/126820968-8fea0d89-3cfd-4412-9cc2-70d7a88da8d3.png)

Seleccionamos Create Record in Route 53

Esta opción nos ayudará, a que automaticamnte aws agrege los registro CNAME a la configuración de DNS de su dominio (Esta opción solo esta disponible cuando el dns se encuentra en aws también).
![image](https://user-images.githubusercontent.com/36380066/126821024-71482ec6-21d4-40b7-a897-ea8b1a4848e5.png)

Ahora solo esperamos el tiempo que nos marca 

Una vez que se haya validado el dominio, se emitirá el certificado y aparecerá en AWS Certificate Manager con el estado establecido en "Emitido".

De vuelta en la página "Configurar ajustes de seguridad" para el equilibrador de carga, el certificado recién emitido debería aparecer ahora en la lista de certificados disponibles. Selecciónelo y haga clic en el botón "Siguiente: Configurar grupo de seguridad" para continuar.

![image](https://user-images.githubusercontent.com/36380066/126822954-01f4eda4-4a6d-4028-8371-20bf5415f951.png)

En la página "Configurar grupo de seguridad":

Seleccione la opción para "Crear un nuevo grupo de seguridad".

Agregue una regla de seguridad para permitir el tráfico entrante en el puerto 80 (el puerto HTTP) con la fuente "En cualquier lugar".

Agregue una segunda regla de seguridad para permitir el tráfico entrante en el puerto 443 (el puerto HTTPS) con la fuente "En cualquier lugar".

Haga clic en el botón "Siguiente: Configurar enrutamiento" para continuar.

![image](https://user-images.githubusercontent.com/36380066/126823720-cda6708c-c5e7-4b09-ba1a-92523d915a15.png)


En la página "Configurar enrutamiento":

En la sección "Grupo objetivo", cree un nuevo grupo objetivo y asígnele un nombre. Asegúrese de que el protocolo esté configurado en "HTTP", el puerto en "80" y el tipo de destino en "instancia". Con esta configuración, el tráfico entre el balanceador de carga y la instancia se transmitirá mediante HTTP, incluso para las solicitudes HTTPS realizadas por el cliente al balanceador de carga.

En la sección "Verificaciones de estado", defina el protocolo como "HTTP" y la ruta a "/".

Haga clic en el botón "Siguiente: Registrar destinos" para continuar.

![image](https://user-images.githubusercontent.com/36380066/126823787-c1875049-7f21-4aa0-8c74-8fb1bfe6ef9d.png)


En la página “Registrar destinos”, use el ID de instancia obtenido en el Paso 1 para identificar y seleccionar la instancia de la aplicación Bitnami. Haga clic en el botón "Agregar a registrados" para mover la instancia a la lista de objetivos registrados. Haga clic en el botón "Siguiente: Revisar" para continuar.

![image](https://user-images.githubusercontent.com/36380066/126824004-3ab3bda6-dd5e-4826-b6f8-834ff50c4b8f.png)


En la página "Revisar", revise los detalles del balanceador de carga configurado. Haga clic en "Crear" para confirmar la configuración e implementar el equilibrador de carga.

![image](https://user-images.githubusercontent.com/36380066/126824051-b276e193-71c3-48c9-83a9-426716b514d0.png)


El equilibrador de carga ahora se creará e implementará.

![image](https://user-images.githubusercontent.com/36380066/126824155-177915b0-8e5a-4228-b0e2-bd2fa8ce9e7a.png)

Paso 3: Apunte Su Nombre De Dominio A La Dirección IP Del Balanceador De Carga
Una vez que se complete la implementación, realice los siguientes pasos:

Visite el panel de EC2 y anote el nombre DNS del equilibrador de carga configurado.

![image](https://user-images.githubusercontent.com/36380066/126824950-78a0655d-d83f-45ef-a698-433576ec28aa.png)


Actualice la configuración de DNS de su dominio agregando un registro A que apunte al nombre de DNS público del equilibrador de carga. Para hacer esto, normalmente necesitará iniciar sesión en la consola de administración de su proveedor de nombre de dominio y realizar los cambios necesarios.

NOTA: Use el nombre DNS (y no la dirección IP) del Elastic Load Balancer en el registro A, porque las direcciones IP de Elastic Load Balancer son dinámicas y pueden cambiar sin previo aviso.

NOTA: Una vez que realice los cambios necesarios, el cambio puede demorar hasta 48 horas en propagarse a otros servidores DNS.


Ahora que tenemos nuestro balanceador de carga, redireccinamos con las reglas de entrada las peticiones a nuestro balanceador, y este dividira las peticiones en el numero de instancias que tengamos.

Esto lo haremos seleccionando el grupo de seguridad al momento de agregar las reglas de entrada en nuestras instancias


![image](https://user-images.githubusercontent.com/36380066/126827739-afcd5e82-4c31-4d4d-9f2c-175181a3e85b.png)


Paso 4: Modifique La Configuración Del Servidor Web En La Instancia De La Aplicación.

Paso 5: Termiando

![image](https://user-images.githubusercontent.com/36380066/126828485-b0551509-7128-4f3f-84df-bdd5bdfd999d.png)
