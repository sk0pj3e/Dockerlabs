nmap: 22,139,445 

![image](https://github.com/user-attachments/assets/8de1d2d8-358c-4b12-9c59-13c26a6693da)

no tenemos pagina ni nada, probamos enumerando: 

     enum4linux 172.17.0.2

encontramos cosas importantes

![image](https://github.com/user-attachments/assets/75b5009c-14fb-4fe4-97c5-ec42e818863d)

y un usuario

![image](https://github.com/user-attachments/assets/17b3866b-e075-4f2b-b042-9923c287427f)

usamos el comando smb que mostro en nmap

      smbclient //172.17.0.2/darkshare -N

![image](https://github.com/user-attachments/assets/95cfd42d-5f2a-404c-9a20-2e26d1374e7f)

después de ver todos los txt el único importante es el "ilegal.txt"
encontré un código para descifrar ROT5 

![image](https://github.com/user-attachments/assets/94da370d-b6f5-4cfc-b5a9-a6ffd2f392c7)

lo ejecutamos y colocamos lo que queremos descifrar. 

![image](https://github.com/user-attachments/assets/3f7275bc-0adc-422b-ad72-846ed484037c)

nos dio:
> no le compartas esta pagina a nadie, solo quiero que lo veas tu, ya que esto me puede meter el problemas: l2fhivsrcbyt2nu5rilmvmqmhpzhugai5szrmyrsyboykzvsokfd6did.onion

lo primero que veo es que es un .onion lo que funciona solo en tor o brave con tor

![image](https://github.com/user-attachments/assets/4e00eaa8-3b3d-49e3-bf40-9c9cd9c16481)

la única que funciona: access the darkest web

entramos después  

![image](https://github.com/user-attachments/assets/87467713-60e1-4dfa-8d87-c01843c3be05)

y da:

![image](https://github.com/user-attachments/assets/d5c77a77-b2c1-417d-85fd-082e4f9d1318)

ahora creamos con eso una password list

![image](https://github.com/user-attachments/assets/866a9772-99b3-4c7e-96ce-4c13356ea729)

y hacemos una fuerza bruta con el usuario dark que encontramos en enum

![image](https://github.com/user-attachments/assets/2010f55f-bfd6-48e4-a481-8811535d0461)

y vemos que nos da la contraseña de dark

![image](https://github.com/user-attachments/assets/10a98880-b8bc-4961-bc2c-c4eb439b22e7)

ahora hacer el sudo -l podemos ver un script y vemos que podemos ejecutar:

![image](https://github.com/user-attachments/assets/8fa1a0f5-d28f-4825-8211-e2414b90c8ff)

si lo ejecutamos vemos que aparece: "script ejecutado con éxito" y lo podemos ejecutar porque en el código aparece: /usr/local/bin/update.sh
y si vemos podemos ver que nosotros tenemos los permisos

pero como no tenemos permisos todavía para root, borramos update.sh
nos vamos a la ruta "/usr/local/bin"
borramos el .sh y creamos otro
y dentro colocamos este comando

![image](https://github.com/user-attachments/assets/85f151d9-bee0-4a2d-b461-4826fb63bdc9)

y ahora ejecutamos 

![image](https://github.com/user-attachments/assets/0cc47d60-255a-425b-8d75-1fba205009f1)

tenemos los permisos, ahora ejecutamos un bash -p

![image](https://github.com/user-attachments/assets/0596fc89-5c1b-449c-b89a-c0a0a98e4e33)

 somos root!
