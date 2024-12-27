nmap: 22, 80

![image](https://github.com/user-attachments/assets/5a0d9537-3c14-4b1a-9da1-9e20aab84128)

---
entramos a la pagina principal 

![image](https://github.com/user-attachments/assets/c127baa8-2bcf-4421-8e08-5e3a50787fa1)

entramos al código fuente

![image](https://github.com/user-attachments/assets/8753af1b-7e27-4900-9ff7-09956b9b91bd)


hacemos un gobuster:

    gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20 -x php,html,txt -k

y nos dio 2 llamativas: 
>note.txt y file.php

![image](https://github.com/user-attachments/assets/ee4cbe5e-8eb4-42c0-be88-56d2a5f87798)


y al entrar en file no da nada, pero al entrar en nota da esto

![image](https://github.com/user-attachments/assets/c3146de1-a0b5-473b-b2f1-fd8ad248c460)


*`str_replace()` para mitigar un Local File Inclusion (LFI) puede ser inefectivo si no se valida o sanitiza adecuadamente la entrada del usuario. Un atacante podría evadir el reemplazo con técnicas como codificación doble o secuencias alternativas.*

Después de probar y suponiendo que tiene un LFI en el archivo PHP, encontré que en la ruta del URL podemos ir moviendo hasta que nos da: 

![image](https://github.com/user-attachments/assets/a23ad4ea-1651-4ac6-a2cb-0bb63a56401c)

con esa ruta nos da el contenido y también un usuario llamado bob que ya se menciono en el código fuente. ahora colocamos en el URL la ruta que se mostraba en el código fuente.

     /usr/share/bob/password.txt

y nos dio la contraseña

![image](https://github.com/user-attachments/assets/f46546f7-f494-46bd-9b49-9f2e20685cf7)


ingresamos por el SSH con el usuario y contraseña que se consiguió

![image](https://github.com/user-attachments/assets/9e097a80-884a-436a-b5f2-c3ba8b8940cd)


ahora para escalar podemos ver que hay un command_exec y probamos si tiene conexión el binario y nos pasamos el archivo:

![image](https://github.com/user-attachments/assets/5977fbd7-68e6-419a-a8e7-b8d265866975)

y tiene conexión, vamos a revisar si es posible un desbordamiento de buffer

![image](https://github.com/user-attachments/assets/d6478840-a709-4c01-8db8-defb36ca140a)

la ultima linea aparece "segmentation default" lo que podría ser la posibilidad del desbordamiento.
al revisar la arquitectura podemos ver dos cosas importantes:

![image](https://github.com/user-attachments/assets/00a4e7e0-73f6-4a9d-afe5-0d22b0a896ce)

que esta activa la protección NX y PIE.  buscando encontré que si esta con la protección NX no es posible una ejecución de shellcode y que PIE se encuentra activa igual y tiene una aleatorización en las direcciones de memoria de las funciones por la protección de PIE o sea que no es posible hacer una explotación de buffer.  pero si que se puede deshabilitar el ASLR.

ahora creare una cadena especialmente diseñada para el calculo del `offset`

    /opt/metasploit-framework/tools/exploit/pattern_create.rb -l 150

Para entender mejor el funcionamiento del binario lo voy a analizar con **ghydra**. El código descompilado es el siguiente:

![image](https://github.com/user-attachments/assets/78c6a848-8939-43b5-8c5d-2f7a886efb95)


en conclusión después del análisis del código descompilado que el offset identificado es de 76. aparte que el programa es vulnerable a un Buffer Overflow debido al uso de la función `gets()` también si logramos establecer el valor de la variable `password` en `0xdead`, será posible acceder al modo administrador y ejecutar comandos mediante la función `system()`.

ahora vamos a crear un script en python con el cual seremos capaces de interactuar con el binario, para controlar el RIP y así poder ejecutar comandos. 

![image](https://github.com/user-attachments/assets/b79c59ee-ab17-4ada-827d-50f354c35ab4)


después de muchos errores en el código nos dio lo que buscábamos  que es que saliera: 
> escribe la contraseña: Estas en modo administrador (key = dead)

ahora modificamos el código para que muestre la contraseña que ingresamos

![image](https://github.com/user-attachments/assets/870c9265-78e4-46ac-aedc-c20d2527890e)


esto nos dio. Finalmente, debemos  actualizar el script añadiendo el comando que queremos  ejecutar, pues en el binario descompilado vemos que esta es la siguiente instrucción.

![[confirmacion.png]]

al final el código deberá quedar así.
![[codigo final.png]]

lo pasamos a la maquina victima 

![[pasar.png]]

nos vamos a la carpeta /tmp y creamos un nano en la maquina victima con el código final copiado  con el comando:

      cat exploit.py | xclip -sel clip 

lo pegamos y lo guardamos

ahora tenemos que seguir ciertos comandos para poder ejecutar bien el script 

vamos a ejecutar:

      python3 exploit.py whoami

y nos saldrá: escribe un comando: root

ahora ejecutamos  el comando:

     python3 exploit.py "chmod +s /bin/bash"

ahora hacemos un: ls -la /bin/bash
y veremos que ya no pide escribir un comando porque aparece vacío
entonces lanzamos un bash -p
![[dockerlabs/medio/stack/root.png]]

y seremos root! 
