nmap: 22,80

![image](https://github.com/user-attachments/assets/8a61b617-d6eb-43ac-9beb-1bdbd7c02fab)

como vemos solo es un apache2 y en el código fuente se encuentra esto

![image](https://github.com/user-attachments/assets/b9950ff5-03a7-4c08-b956-3a37a757ebdf)

> ZGFuaWVsYQ== : Zm9jYXJvamE=

la sacamos:

![[dockerlabs/facil/extraviado/base64.png]]

tenemos usuario y contraseña
> daniela:focaroja

![[dockerlabs/facil/extraviado/conexion.png]]

tenemos conexión

buscando encontramos esto

![[dockerlabs/facil/extraviado/cat.png]]

pasamos la contraseña a https://gchq.github.io/CyberChef/#recipe=Magic(3,false,false,'')&input=WW1Gc2JHVnVZVzVsWjNKaA

y obtenemos la contraseña 

![[chef.png]]

     ballenanegra

en la contraseña de diego

![[dockerlabs/facil/extraviado/usuario2.png]]

si vamos a la carpeta de diego encontramos un "pass" que dice "donde estara?"

seguimos estas rutas

![[ruta1.png]]

![[ruta2.png]]

como vemos es un acertijo, lo busque y encontré que la respuesta de "osoazul", o sea que es la contraseña de root

![[dockerlabs/facil/extraviado/root.png]]

somos root!
