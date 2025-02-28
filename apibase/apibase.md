nmap: 22, 5000

![image](https://github.com/user-attachments/assets/b99f8501-fe25-4b4a-9532-5991897b8c04)

![image](https://github.com/user-attachments/assets/47a7c11a-dca9-4d5d-ae6e-f800286aeae5)

si entramos a la pagina veremos 

![image](https://github.com/user-attachments/assets/e45554dd-19db-4c9a-a5a5-8b1e103c879b)

lanzamos un gobuster para probar si hay algo mas

![image](https://github.com/user-attachments/assets/38ed2d4e-2253-4b62-ab45-b6a0116a2b9c)

y con el "/add" nos da esto: 

![image](https://github.com/user-attachments/assets/69c207e2-d7a1-4ed2-9eb4-a1bae5acf4ad)

pero en el código que daba la pagina aparece como que podemos crear un usuario, pero no tenemos mas allá de esa frase cuando se ingresa a la pagina. 

encontré en https://stackoverflow.com/ que podemos crearlo con "curl" así que le pregunte a una IA mas o menos para ir modificando el código para que funcione, y después de varios intentos funcionales pero no lo que buscamos, llegamos a este código. 

     curl -X POST "http://172.17.0.2:5000/add" \                           
      -H "Content-Type: application/x-www-form-urlencoded" \       
      --data "username=skopjee&email=skopjee@example.com&password=qwerty"

obtendremos 

![image](https://github.com/user-attachments/assets/4649bada-d5af-4f41-9ae3-cabd9d92640f)

ahora vamos a verificar que funciona y que el usuario esta

![image](https://github.com/user-attachments/assets/4c1e8e59-4bed-4fcf-9bb4-3d471c95e686)

y vemos que esta con el nombre y contraseña que le dimos. pero la verdad no llegue mucho mas lejos porque aun así no puedo entrar por el puerto 22. pero vi que podemos crear un .py para hacer un tipo de fuerza bruta. así que después de muchos intentos con el código me dio resultado

![image](https://github.com/user-attachments/assets/c110c091-21de-46d5-808a-6c20c6577290)

y me dio otro error pero fue porque el diccionario que cree no funciona, pero podemos añadir uno de github

> https://github.com/danielmiessler/SecLists/blob/master/Usernames/xato-net-10-million-usernames-dup.txt

lo descargamos con "wget" y le cambiamos la linea al código

![image](https://github.com/user-attachments/assets/0968d3f4-3d76-4533-ae24-5c61819eba18)

después de un rato nos da un usuario 


    1, 
    "pingu", 
    "pinguinasio"

obtenemos conexión

![image](https://github.com/user-attachments/assets/9ce72e74-9a90-44b3-9953-5313aee58cc0)

ahora buscando para escalar, obtenemos un .pacap, vemos que tiene y tiene la credencial de root así que entramos a root 

![image](https://github.com/user-attachments/assets/dee6f9d9-2eb5-41d6-88b6-33cc3e64c5b6)

somos root
