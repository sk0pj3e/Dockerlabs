nmap: 22,139,445 

![[dockerlabs/dificil/darkweb/nmap.png]]

no tenemos pagina ni nada, probamos enumerando: 

     enum4linux 172.17.0.2

encontramos cosas importantes

![[enu1.png]]

y un usuario

![[dockerlabs/dificil/darkweb/usuario1.png]]

usamos el comando smb que mostro en nmap

      smbclient //172.17.0.2/darkshare -N

![[get.png]]

después de ver todos los txt el único importante es el "ilegal.txt"
encontré un código para descifrar ROT5 

![[dockerlabs/dificil/darkweb/codigo1.png]]

lo ejecutamos y colocamos lo que queremos descifrar. 

![[ejecucion.png]]

nos dio:
> no le compartas esta pagina a nadie, solo quiero que lo veas tu, ya que esto me puede meter el problemas: l2fhivsrcbyt2nu5rilmvmqmhpzhugai5szrmyrsyboykzvsokfd6did.onion

lo primero que veo es que es un .onion lo que funciona solo en tor o brave con tor

![[paginaprincipal.png]]

la única que funciona: access the darkest web

entramos después  

![[list.png]]

y da:

![[pass.png]]

ahora creamos con eso una password list

![[nano.png]]

y hacemos una fuerza bruta con el usuario dark que encontramos en enum

![[dockerlabs/dificil/darkweb/hydra.png]]

y vemos que nos da la contraseña de dark

![[dockerlabs/dificil/darkweb/conexion.png]]

ahora hacer el sudo -l podemos ver un script y vemos que podemos ejecutar:

![[dockerlabs/dificil/darkweb/script.png]]

si lo ejecutamos vemos que aparece: "script ejecutado con éxito" y lo podemos ejecutar porque en el código aparece: /usr/local/bin/update.sh
y si vemos podemos ver que nosotros tenemos los permisos

pero como no tenemos permisos todavía para root, borramos update.sh
nos vamos a la ruta "/usr/local/bin"
borramos el .sh y creamos otro
y dentro colocamos este comando

![[nano2.png]]

y ahora ejecutamos 

![[bin2.png]]

tenemos los permisos, ahora ejecutamos un bash -p

![[rootf.png]]

 somos root!