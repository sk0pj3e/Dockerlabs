nmap: 80,22

![image](https://github.com/user-attachments/assets/fba15d80-3b23-4fea-acf4-369ee0beb7df)

---

![image](https://github.com/user-attachments/assets/1b7f8f8b-a371-44f7-b444-410e080139ee)

en la pagina veremos un login pero no tenemos credenciales ni nada, hacemos un gobuster 

![image](https://github.com/user-attachments/assets/2b0dd101-eda3-4939-a3d9-4ae6c8ebf686)

obtenemos uno interesante llama "archiv"  que al verlo tendremos dos archivos 

![image](https://github.com/user-attachments/assets/466dc1a5-f68a-43ac-8e12-683fa809dd9f)

en el script, al final del texto veremos esto

![image](https://github.com/user-attachments/assets/3b8c8f48-8b52-4a8d-b71f-ed9728aee471)

probamos el de admin

![[dockerlabs/medio/ciberguard/test6.png]]

como en todos muestra el mismo panel, probamos esos mismos pero por el ssh y funciona el de "chloe"

![[dockerlabs/medio/ciberguard/test7.png]]

dentro veremos que "veronica" resalta 

![[dockerlabs/medio/ciberguard/test8.png]]

y tiene todos los permisos, leemos en verónica el .bash_history

![[dockerlabs/medio/ciberguard/test9.png]]

obtendremos una contraseña en base64, la pasamos a texto claro probamos con su verónica pero no funciona así que probamos con el texto encontrado en base64 

![[dockerlabs/medio/ciberguard/test10.png]]

somos verónica, descargamos pspy64 https://github.com/DominicBreuker/pspy?tab=readme-ov-file le damos permisos de ejecución y mandamos con "scp pspy64 chloe@172.17.0.2:/tmp" a la maquina victima 
al ejecutarlo obtendremos varios datos pero tenemos uno en especial 

![[dockerlabs/medio/ciberguard/test11.png]]

en ".local" se encuentra un script y dentro de ese script vamos a colocar una shell "sh" 

![[dockerlabs/medio/ciberguard/test12.png]]

lo guardamos y al colocar el puerto en escucha automáticamente nos conectamos 

![[dockerlabs/medio/ciberguard/test13.png]]

![[dockerlabs/medio/ciberguard/test14.png]]

ahora nos vamos al tmp y antes habíamos visto un "id_rsa" pero por permisos no podíamos cambiar modificar nada, ahora le daremos permisos de 600 y ejecutamos root con el ssh y la id_rsa 

 ![[dockerlabs/medio/ciberguard/test15.png]]

al ejecutar seremos root
