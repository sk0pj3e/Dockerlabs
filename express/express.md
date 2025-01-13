nmap: 22,80

![image](https://github.com/user-attachments/assets/3de098e7-0362-404a-aec9-854d68fe19ad)

después de buscar mucho encontré que puedo añadir un express.dl  en el "/etc/hosts" 
que nos manda a una pagina un poco diferente. 

![image](https://github.com/user-attachments/assets/3fe89d75-e8bf-4aea-9667-23b62f02282f)

hacemos un feroxbuster: 

     feroxbuster -u "http://express.dl/" -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt

y nos dio
> http://express.dl/binary/

donde hay un descargable llamado game de 64 bites 

![image](https://github.com/user-attachments/assets/1223ad50-7666-4043-98b2-81f6f2ca0ecc)

ejecutamos el game

![[game2.png]]

podemos hacer eso las 100 veces que aparece ahí, pero no lo haremos por razones obvias. Encontré que podemos depurar el binario con ghidra 
y dentro vemos el hidden_key, lo que hacemos es ingeniería inversa.
como vemos en "local_28", "local_20"  del código, lo que estas variables contienen las contraseña pero al inverso y obtenemos

     P@ssw0rd!#--025163fhusGNFE

lo que podríamos hacer una fuerza bruta. 

lanzamos:

     hydra -L /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt -p 'P@ssw0rd!#--025163fhusGNFE' 172.17.0.2 ssh


nos da

![[dockerlabs/medio/express/hydra.png]]

y obtenemos la conexión

![[dockerlabs/medio/express/conexion.png]]

dentro podríamos escalar por un script

![[dockerlabs/medio/express/script.png]]

también encontré un .py

![[os.png]]

después de mucho rato, encontré otra carpeta y con permisos de ejecución

![[dockerlabs/medio/express/script2.png]]

dentro no hay nada y colocamos este os.

![[os2.png]]

![[import.png]]

ahora podremos ejecutar 

     sudo /usr/bin/python3 /opt/script.py

![[dockerlabs/medio/express/root.png]]

y ahora somos root!
