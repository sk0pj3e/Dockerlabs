nmap: 22, 5000

![image](https://github.com/user-attachments/assets/aeed78ad-a816-4c3c-870f-f006219cab59)

nos vamos a la pagina con el puerto 5000

![image](https://github.com/user-attachments/assets/58141197-295a-4422-a8ae-1426447daa2d)

no hay mucho, lanzamos un gobuster y nos da "admin y login"

vamos al login

![[dockerlabs/facil/BaluFood/test3.png]]

las credenciales son "admin/admin"

![[dockerlabs/facil/BaluFood/test4.png]]

nos vamos al codigo fuente y esta esto

    <!-- Backup de acceso: sysadmin:backup123 -->

![[dockerlabs/facil/BaluFood/test5.png]]

entramos por el ssh

![[dockerlabs/facil/BaluFood/test6.png]]

si leemos el app.py  tendremos una contraseña

![[dockerlabs/facil/BaluFood/test7.png]]

somos balulero

![[dockerlabs/facil/BaluFood/test8.png]]

ahora encontré la contraseña de root y como se lanza el root

![[dockerlabs/facil/BaluFood/test9.png]]

tenemos la contraseña y lanzarlo como: su - root y somos root.
