nmap: 22, 5000

![image](https://github.com/user-attachments/assets/aeed78ad-a816-4c3c-870f-f006219cab59)

nos vamos a la pagina con el puerto 5000

![image](https://github.com/user-attachments/assets/58141197-295a-4422-a8ae-1426447daa2d)

no hay mucho, lanzamos un gobuster y nos da "admin y login"

vamos al login

![image](https://github.com/user-attachments/assets/3e6d42c3-6ff4-4bb7-aac7-231dde7649c3)

las credenciales son "admin/admin"

![image](https://github.com/user-attachments/assets/b2a13f76-eb0e-49ac-8b6d-f2281624408b)

nos vamos al codigo fuente y esta esto

    <!-- Backup de acceso: sysadmin:backup123 -->

![image](https://github.com/user-attachments/assets/17593745-013a-4373-9073-5f46698b5848)

entramos por el ssh

![image](https://github.com/user-attachments/assets/bb447a81-b17d-4444-8fa9-085aaf47b17c)

si leemos el app.py  tendremos una contraseña

![image](https://github.com/user-attachments/assets/fd7c56d2-d5e5-433d-a992-9ba376c58634)

somos balulero

![image](https://github.com/user-attachments/assets/a086a56c-62a6-4ce9-b662-fb3db55d65c3)

ahora encontré la contraseña de root y como se lanza el root

![image](https://github.com/user-attachments/assets/6abc213e-cb2b-496b-8558-b9fb527dce2c)

tenemos la contraseña y lanzarlo como: su - root y somos root.
