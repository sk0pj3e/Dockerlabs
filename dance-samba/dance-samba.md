nmap: 21,22,139,445

![image](https://github.com/user-attachments/assets/8a92508a-6613-4fc4-86bd-79b8cc649807)

---
en el puerto 21 entramos con la credencial por defecto "anonymous" y sin contraseña, nos pasamos el "nota.txt" que hay con un "get nota.txt"
la leemos en nuestra terminal y dice: 

![image](https://github.com/user-attachments/assets/85125c7a-8a6e-4eb0-a760-0e87c374e183)

![image](https://github.com/user-attachments/assets/86766d53-0e15-4cbe-b698-c6575e4a5d86)


probamos si el usuario macarena  y donald sirve para el ssh:

     smbmap -u macarena -p donald -H 172.17.0.2 


pero no hay éxito.
pero hay una carpeta llamada "macarena"

![image](https://github.com/user-attachments/assets/72438aba-5e40-4b1e-b096-2bcc53dbd58c)

así que la buscamos:

    smbclient //172.17.0.2/macarena -U macarena 

>la contraseña: donald

dentro veremos un user.txt pero solo tiene la flag

![image](https://github.com/user-attachments/assets/5c4077fb-161c-474a-9856-ec1b24f3e608)

ahora seguimos estos pasos:
* creamos una carpeta ".ssh" en el smb, y creamos una clave en nuestra terminal
  
![image](https://github.com/user-attachments/assets/6923efc8-5fe8-406a-b9f2-d3d2f9f0cc7c)

a la: id_rsa.pub le cambiamos el nombre: 

     mv id_rsa.pub authorized_keys

ahora la pasamos a la smb:

     put autorized_keys 

y ahora nos conectamos con macarena y el ssh

![image](https://github.com/user-attachments/assets/691187de-1ab0-4334-9399-8fa71eece227)

y tenemos la conexión. 

dentro hay un usuario "secret" entramos y hay un "hash" lo leemos y tiene una contraseña en base32, 

![image](https://github.com/user-attachments/assets/3a195503-6ef1-415a-9d72-8049fe72ef85)

utilizamos: https://gchq.github.io/CyberChef/#recipe=Magic(3,false,false,'')&input=TU1aVk01MjJMQkZIVVdTWEpZWVdHM0tXTzVNVlFUVDJNUVpEUzZLMklFNlQyPT09

![image](https://github.com/user-attachments/assets/ea7bc69f-d391-4946-a072-220a051b24f4)

buscamos "magic" y ejecutamos y nos da la contraseña: supersecurepassword
ahora al usuario mismo de macarena le colocamos la contraseña y podemos ver el contenido de sudo -l

![image](https://github.com/user-attachments/assets/7f584194-a6e8-497c-9f7e-86e7c783f585)

ahora sudo -l y como vemos dio /usr/bin/file

buscamos como escalar en: https://gtfobins.github.io/gtfobins/file/

![image](https://github.com/user-attachments/assets/024d8dd2-fa59-41de-a3a9-d6136095afc6)

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
