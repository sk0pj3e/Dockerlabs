nmap: 22,139,445

![image](https://github.com/user-attachments/assets/931baef1-b5a7-4fd8-b6e5-f179f512ec2b)


Hacemos una enumeración de la IP

     enum4linux 172.17.0.2

nos da

![image](https://github.com/user-attachments/assets/2d2e925e-c849-46ac-959a-32170bd1e827)

ransomware y dos 3 usuarios

![image](https://github.com/user-attachments/assets/a0c6f96b-478e-489f-9c9c-8f2ddc9d8104)

creamos una carpeta con los 3 usuarios  y lanzamos una fuerza bruta

     netexec smb 172.17.0.2 -u user.txt  -p /usr/share/wordlists/rockyou.txt  --ignore-pw-decoding

![image](https://github.com/user-attachments/assets/8bcd7bc4-4752-49dc-8183-bb58df2873fa)

probé en ssh y no sirvió pero probamos con el archivo de "ransomware" como es smb intentamos entrar al client

![image](https://github.com/user-attachments/assets/04f71239-4000-464e-b68e-a9208c39b2ca)

tenemos acceso
el contenido de lo que hay es

![image](https://github.com/user-attachments/assets/d665b555-67f7-43d4-8b1f-db9781466551)

ahora para intentar desencriptar el archivo private.txt es necesario hacerle reversing al binario, por lo que hago reversing con ghidra 
viendo el código el algoritmo de cifrado el cual es "AES-256-CBC". por lo que encontré la clave debe tener 32 bytes y el IV debe tener 16. después vemos que  "local_438" hace referencia a "local_438" y "local_430" entonces me da un valor: 1234567890123456
 podemos ver que se le pasa local_428, la cual es una variable que contiene el resultado de la ejecución de la función recon que aparece así
> y0qpfjxbd79047929ew0omqad3f4gscl

encontré un código python que sirve 

![image](https://github.com/user-attachments/assets/19a628e9-9362-4c93-8446-6b9bd9b271ae)

ejecutamos con python en una maquina virtual interna para que funcione el pip

![image](https://github.com/user-attachments/assets/725752b0-9ba5-4ed3-b184-0bb72fca4803)

y nos dio 

      las credenciales ssh son: bob:56000nmqpL

probamos por ssh y tenemos la conexión 

![image](https://github.com/user-attachments/assets/97ea4ba6-06ce-4437-9a32-9f679040763f)

hacemos sudo -l y nos aparece node así que la buscamos https://gtfobins.github.io/gtfobins/node/#sudo

![image](https://github.com/user-attachments/assets/177b0524-935e-4d8d-8321-7360e0d9bfa7)

ahora lanzamos

    sudo -u calamardo node -e 'require("child_process").spawn("/bin/sh", {stdio: [0, 1, 2]})'

![image](https://github.com/user-attachments/assets/3a53aad2-45ee-4800-8595-89d01a5cfb58)

buscando como escalar encontramos esto

![image](https://github.com/user-attachments/assets/bb2139f0-f9de-4db1-961d-1c287ca20472)

> patricio:Jap0n16ydcbd***

obtenemos conexión a patricio

![image](https://github.com/user-attachments/assets/9a67383d-2ca0-44ee-8a09-6f2cae6ea83d)

en patricio encontramos un python 

![image](https://github.com/user-attachments/assets/78499b13-420e-46d9-9775-6da6f309f711)

después de buscar encontré este código en gtfobins https://gtfobins.github.io/gtfobins/python/

![[sudo2.png]]

ejecutamos

     ./python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'


![[dockerlabs/dificil/rass/root.png]]

lanzamos y obtenemos acceso a root! 
