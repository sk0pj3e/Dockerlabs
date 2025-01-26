nmap: 22, 80

![image](https://github.com/user-attachments/assets/17d47401-b6fc-4bf1-bd5f-62a66bf34e98)

---
al ingresar a la pagina no da una posible usuario "balu"
al entrar al código de la pagina podemos ver que hay un "script.js" al final, si entramos nos mandara a otro código con lo que muestra esto:

![image](https://github.com/user-attachments/assets/3c3528f8-fc03-42b2-bedf-b5f8cfd48ec3)

y si colocamos el 
> .env_de_baluchingo

en el URL queda si:

>http://172.17.0.2/.env_de_baluchingon

nos dará las credenciales

![image](https://github.com/user-attachments/assets/a32544d7-07fa-49ea-b4b5-1a768c25d73d)


![[dockerlabs/facil/balulero/conexion.png]]

estamos dentro y ahora buscamos como escalar

para escalar se puede por "php" pero no tenemos permisos de escritura o modificación.
para escalar al otro usuario chocolate 
seguí los pasos de: https://gtfobins.github.io/gtfobins/php/#sudo

![[dockerlabs/facil/balulero/sudo.png]]

![[chocolate.png]]

    CMD="/bin/sh"

     sudo -u chocolate php -r "system('$CMD');"


ahora a escalar a root

utilizamos una herramienta llamada pspy para ver que ejecuta root sin ser root nosotros y poder ver: https://github.com/DominicBreuker/pspy
lo ejecutamos y muestra que esta carpeta se ejecuta cada 5 segundos:  /opt/script.php

![[sleep5.png]]

ejecutamos los siguientes comandos:

    echo "<?php" > /opt/script.php

    echo "exec ('chmod u+s /bin/bash');" >> /opt/script.php

    echo "?>" >> /opt/script.php

por orden uno por uno, lanzamos bash -p

![[dockerlabs/facil/balulero/root.png]]

y somos root! 
