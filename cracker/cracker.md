nmap: 22,80

![image](https://github.com/user-attachments/assets/d1cc6252-cf61-4d24-a732-3abe3ca0efd3)

en la pagina al final abajo hay un "cracker.dl"  que lo agregamos al /etc/hosts

![[dl.png]]


ahora ingresamos

![[cracker.png]]



no hay nada bueno

si buscamos directorios no hay nada, pero si si subdominios

     wfuzz -c -u cracker.dl -H "HOST: FUZZ.cracker.dl" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt  --hl=162

nos dio: 

 ![[wffuf.png]]

![[japan.png]]

si descargamos me interesa una linea:

![[file.png]]

es un ejecutable lo que es un software, así que le damos permisos y ejecutamos

![[ejecutable.png]]

hay dos opciones que funcionan seria ghidra o descompone el software en busca de alguna credencial que no este guardada de forma correcta.

vamos a intentar primero descomponiendo

     string Paneladmin

y da algo interesante 

![[serial.png]]

la guardamos como nano

![[dockerlabs/medio/cracker/nano.png]]

y logramos

![[admin.png]]

y nos da una contraseña

![[dockerlabs/medio/cracker/contraseña.png]]

entramos con la contraseña por el ssh


![[dockerlabs/medio/cracker/conexion2.png]]

luego de buscar mucho, probé el binario  como contraseña de root y funciono

![[dockerlabs/medio/cracker/root.png]]

somos root
