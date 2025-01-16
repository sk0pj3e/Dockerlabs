nmap: 22,80

![image](https://github.com/user-attachments/assets/8a61b617-d6eb-43ac-9beb-1bdbd7c02fab)

como vemos solo es un apache2 y en el código fuente se encuentra esto

![image](https://github.com/user-attachments/assets/b9950ff5-03a7-4c08-b956-3a37a757ebdf)

> ZGFuaWVsYQ== : Zm9jYXJvamE=

la sacamos:

![image](https://github.com/user-attachments/assets/0d2959a5-b8d3-4830-966d-0de1e6b5a73d)

tenemos usuario y contraseña
> daniela:focaroja

![image](https://github.com/user-attachments/assets/bf5631be-59cb-4ffd-9a4b-5e459b4d7e54)

tenemos conexión

buscando encontramos esto

![image](https://github.com/user-attachments/assets/0636d695-01ce-4910-b52e-05a4c495e351)

pasamos la contraseña a https://gchq.github.io/CyberChef/#recipe=Magic(3,false,false,'')&input=WW1Gc2JHVnVZVzVsWjNKaA

y obtenemos la contraseña 

![image](https://github.com/user-attachments/assets/ca1ce87f-5334-4e9d-8936-af60bca7abb1)

     ballenanegra

en la contraseña de diego

![image](https://github.com/user-attachments/assets/446811e1-dfe6-4e2d-bbe2-0f40da3b1cd3)

si vamos a la carpeta de diego encontramos un "pass" que dice "donde estara?"

seguimos estas rutas

![image](https://github.com/user-attachments/assets/730d5619-7b3c-4f6e-b0ec-0961ab7fe3eb)

![[ruta2.png]]

como vemos es un acertijo, lo busque y encontré que la respuesta de "osoazul", o sea que es la contraseña de root

![[dockerlabs/facil/extraviado/root.png]]

somos root!
