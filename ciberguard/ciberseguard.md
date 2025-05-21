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

![image](https://github.com/user-attachments/assets/a1e7cf67-2f04-4b11-a062-fbe373203197)

como en todos muestra el mismo panel, probamos esos mismos pero por el ssh y funciona el de "chloe"

![image](https://github.com/user-attachments/assets/4a9275be-0522-47d5-b643-1aef0f357103)

dentro veremos que "veronica" resalta 

![image](https://github.com/user-attachments/assets/4fb7d7e7-7964-4724-a4a7-078c56378435)

y tiene todos los permisos, leemos en verónica el .bash_history

![image](https://github.com/user-attachments/assets/893a04b9-e99c-4e58-9fd1-439efb6563c8)

obtendremos una contraseña en base64, la pasamos a texto claro probamos con su verónica pero no funciona así que probamos con el texto encontrado en base64 

![image](https://github.com/user-attachments/assets/626d2216-a126-4e63-b609-4d108d60d0b2)

somos verónica, descargamos pspy64 https://github.com/DominicBreuker/pspy?tab=readme-ov-file le damos permisos de ejecución y mandamos con "scp pspy64 chloe@172.17.0.2:/tmp" a la maquina victima 
al ejecutarlo obtendremos varios datos pero tenemos uno en especial 

![image](https://github.com/user-attachments/assets/4fa2b1c9-d4cd-44b2-b879-c88f9b2fe8c0)

en ".local" se encuentra un script y dentro de ese script vamos a colocar una shell "sh" 

![image](https://github.com/user-attachments/assets/c36a50d5-cf95-4703-bd3f-0cac11f72035)

lo guardamos y al colocar el puerto en escucha automáticamente nos conectamos 

![image](https://github.com/user-attachments/assets/c62f9ed7-ffdf-4e5e-9cef-97bf27980cc5)

![[dockerlabs/medio/ciberguard/test14.png]]

ahora nos vamos al tmp y antes habíamos visto un "id_rsa" pero por permisos no podíamos cambiar modificar nada, ahora le daremos permisos de 600 y ejecutamos root con el ssh y la id_rsa 

 ![[dockerlabs/medio/ciberguard/test15.png]]

al ejecutar seremos root
