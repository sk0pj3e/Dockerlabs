nmap: 21,22,139,445

![image](https://github.com/user-attachments/assets/8a92508a-6613-4fc4-86bd-79b8cc649807)

---
en el puerto 21 entramos con la credencial por defecto "anonymous" y sin contraseña, nos pasamos el "nota.txt" que hay con un "get nota.txt"
la leemos en nuestra terminal y dice: 
![[dockerlabs/medio/dance-samba/nota.png]]
![[cat.png]]

probamos si el usuario macarena  y donald sirve para el ssh:

     smbmap -u macarena -p donald -H 172.17.0.2 


pero no hay éxito.
pero hay una carpeta llamada "macarena"
![[maca.png]]

así que la buscamos:

    smbclient //172.17.0.2/macarena -U macarena 

>la contraseña: donald

dentro veremos un user.txt pero solo tiene la flag
![[flag.png]]

ahora seguimos estos pasos:
* creamos una carpeta ".ssh" en el smb, y creamos una clave en nuestra terminal
![[dockerlabs/medio/dance-samba/ssh.png]]
a la: id_rsa.pub le cambiamos el nombre: 

     mv id_rsa.pub authorized_keys

ahora la pasamos a la smb:

     put autorized_keys 

y ahora nos conectamos con macarena y el ssh

![[dockerlabs/medio/dance-samba/conexion.png]]
y tenemos la conexión. 

dentro hay un usuario "secret" entramos y hay un "hash" lo leemos y tiene una contraseña en base32, 
![[hash.png]]

utilizamos: https://gchq.github.io/CyberChef/#recipe=Magic(3,false,false,'')&input=TU1aVk01MjJMQkZIVVdTWEpZWVdHM0tXTzVNVlFUVDJNUVpEUzZLMklFNlQyPT09

![[base32.png]]

buscamos "magic" y ejecutamos y nos da la contraseña: supersecurepassword
ahora al usuario mismo de macarena le colocamos la contraseña y podemos ver el contenido de sudo -l
![[clave1.png]]

ahora sudo -l y como vemos dio /usr/bin/file

buscamos como escalar en: https://gtfobins.github.io/gtfobins/file/
![[gtf.png]]

ejecutamos los pasos que dice: LFILE=/opt/password.txt
o sea que ejecutamos los indicadores que da sudo -l, luego -f y por ultimo el archivo que contenga la contraseña de root o solo pueda ejecutar root.

ahora ejecutamos:

     LFILE=/opt/password.txt

nos vamos a la carpeta /opt y nos aparecerá el archivo.txt
ahora ejecutaremos este comando:

     sudo /usr/bin/file -f password.txt

y nos dará esto

![[dockerlabs/medio/dance-samba/root.png]]

al hacer "su root" y colocar la contraseña que nos muestra en el cuadro rojo y seremos root!
