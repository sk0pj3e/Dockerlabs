nmap: 22, 5000

![[dockerlabs/facil/BaluFood/test1.png]]

nos vamos a la pagina con el puerto 5000


![[dockerlabs/facil/BaluFood/test2.png]]

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