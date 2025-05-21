nmap: 22,80

![image](https://github.com/user-attachments/assets/d0bf300b-55b2-43c8-bb82-dc520e84ca0a)

si enumeramos encontraremos

![image](https://github.com/user-attachments/assets/e2f00a62-0fe5-4066-a872-4ce642f84728)

si vamos al iniciar sesión e hacemos una inyección SQL básica podemos ingresar y pasar el login pero no hay mucho así que seguiremos buscando
si en el login capturamos con burpsuite podemos ver este mensaje

![image](https://github.com/user-attachments/assets/d5b9fbbb-7298-4238-877d-c4a9dbbf2f4b)

nombrando la base de datos, así que guardamos nuestra petición con un .req y lanzamos un sqlmap. 

![image](https://github.com/user-attachments/assets/ec23f137-d54a-4254-8dab-41c1169e43e4)

![[dockerlabs/medio/Crystalteam/test5.png]]

aquí encuentra usuarios y contraseñas como "alejandro/hanka"

![[dockerlabs/medio/Crystalteam/test6.png]]

al hacer sudo -l aparece "python3" buscamos en https://gtfobins.github.io/gtfobins/python/#sudo

lanzamos: 

    sudo -u root python3 -c 'import os; os.system("/bin/sh")'


![[dockerlabs/medio/Crystalteam/test7.png]]

somos root!
