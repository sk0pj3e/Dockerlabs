nmap: 22,80

![[dockerlabs/medio/Crystalteam/test1.png]]

si enumeramos encontraremos

![[dockerlabs/medio/Crystalteam/test2.png]]

si vamos al iniciar sesión e hacemos una inyección SQL básica podemos ingresar y pasar el login pero no hay mucho así que seguiremos buscando
si en el login capturamos con burpsuite podemos ver este mensaje


![[dockerlabs/medio/Crystalteam/test3.png]]

nombrando la base de datos, así que guardamos nuestra petición con un .req y lanzamos un sqlmap. 

![[dockerlabs/medio/Crystalteam/test4.png]]

![[dockerlabs/medio/Crystalteam/test5.png]]

aquí encuentra usuarios y contraseñas como "alejandro/hanka"

![[dockerlabs/medio/Crystalteam/test6.png]]

al hacer sudo -l aparece "python3" buscamos en https://gtfobins.github.io/gtfobins/python/#sudo

lanzamos: 

    sudo -u root python3 -c 'import os; os.system("/bin/sh")'


![[dockerlabs/medio/Crystalteam/test7.png]]

somos root!