nmap: 22,139,445

![image](https://github.com/user-attachments/assets/931baef1-b5a7-4fd8-b6e5-f179f512ec2b)


Hacemos una enumeración de la IP

     enum4linux 172.17.0.2

nos da

![image](https://github.com/user-attachments/assets/2d2e925e-c849-46ac-959a-32170bd1e827)

ransomware y dos 3 usuarios

![[enu2.png]]

creamos una carpeta con los 3 usuarios  y lanzamos una fuerza bruta

     netexec smb 172.17.0.2 -u user.txt  -p /usr/share/wordlists/rockyou.txt  --ignore-pw-decoding

![[net.png]]

probé en ssh y no sirvió pero probamos con el archivo de "ransomware" como es smb intentamos entrar al client

![[smb.png]]

tenemos acceso
el contenido de lo que hay es

![[dockerlabs/dificil/rass/cat.png]]

ahora para intentar desencriptar el archivo private.txt es necesario hacerle reversing al binario, por lo que hago reversing con ghidra 
viendo el código el algoritmo de cifrado el cual es "AES-256-CBC". por lo que encontré la clave debe tener 32 bytes y el IV debe tener 16. después vemos que  "local_438" hace referencia a "local_438" y "local_430" entonces me da un valor: 1234567890123456
 podemos ver que se le pasa local_428, la cual es una variable que contiene el resultado de la ejecución de la función recon que aparece así
> y0qpfjxbd79047929ew0omqad3f4gscl

encontré un código python que sirve 

![[py.png]]

ejecutamos con python en una maquina virtual interna para que funcione el pip

![[descrypt.png]]

y nos dio 

      las credenciales ssh son: bob:56000nmqpL

probamos por ssh y tenemos la conexión 

![[dockerlabs/dificil/rass/conexion.png]]

hacemos sudo -l y nos aparece node así que la buscamos https://gtfobins.github.io/gtfobins/node/#sudo

![[node.png]]

ahora lanzamos

    sudo -u calamardo node -e 'require("child_process").spawn("/bin/sh", {stdio: [0, 1, 2]})'

![[calamardo.png]]

buscando como escalar encontramos esto

![[bash2.png]]

> patricio:Jap0n16ydcbd***

obtenemos conexión a patricio

![[patricio.png]]

en patricio encontramos un python 

![[dockerlabs/dificil/rass/python1.png]]

después de buscar encontré este código en gtfobins https://gtfobins.github.io/gtfobins/python/

![[sudo2.png]]

ejecutamos

     ./python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'


![[dockerlabs/dificil/rass/root.png]]

lanzamos y obtenemos acceso a root! 
