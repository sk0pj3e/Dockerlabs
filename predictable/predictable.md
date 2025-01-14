nmap: 22,1111

![image](https://github.com/user-attachments/assets/0c11c818-e3ca-405a-87e7-5051cb6da1cf)

---
si entramos por el puerto 1111 a la pagina muestra resultados de numero hasta el 99

![image](https://github.com/user-attachments/assets/4d0d9e32-9140-480a-89cc-00773291bede)

por lo que vi buscamos el valor 100, y para eso se tiene que calcular el numero pero encontré un código que facilita eso y nos da el valor del orden para obtener la clave:
necesitamos un numero que esta en el código fuente:

![image](https://github.com/user-attachments/assets/c8a999b9-28d1-4422-affe-3d24ca6c376e)

ahora dejamos un código así

![image](https://github.com/user-attachments/assets/411c6483-8afe-45e2-b9ba-1dbb37d8fd19)

el primero numero:
n = es el numero del código fuente
los result del 1 al 3 son los primeros 3 números de la pagina
y el result 17, en el numero que aparece bajo el titulo. 

al lanzarlo con: python3 solution.py
al ejecutar:

![image](https://github.com/user-attachments/assets/1e8d9675-8823-4ede-beb2-8177460e73eb)

lo que necesitamos es el numero 100 porque en la pagina llega al 99

![image](https://github.com/user-attachments/assets/647811d7-3cad-4ce9-926e-14ea0c24922d)

cuando ejecutamos el .py que creamos nos dará el numero 100, los números de ejecución tienen que ser los mismos que están en la pagina 

![image](https://github.com/user-attachments/assets/4bef0f3d-8219-4ff8-ba2a-3972c805ad77)

entramos por el ssh masha@172.17.0.2 y la contraseña: LCG_1s_E4Sy_t0_bR34k

![[dockerlabs/dificil/predictable/conexion.png]]

ahora nos pide salir del script bash con algún comando.

fuimos probando y encontré

      __builtins__.__dict__

![[test.png]]

y encontré: 

      __builtins__.__dict__['__imp'+'ort__']('o'+'s').__dict__['sys'+'tem']('bash')

![[dockerlabs/dificil/predictable/codigo2.png]]

obtenemos acceso, ahora a escalar hacia root

al hacer un sudo -l muestra 
> "/opt/shell"

vemos que tenemos permiso que escritura al hacer el ls la /opt/shell
pero no podemos entrar a nano

![[shell.png]]

lanzamos el comando:

>echo "bash -p" > /opt/shell

después de tener un erro básico, vemos que cambio el color de ejecución

![[shell2.png]]

lanzamos otra ves: sudo /opt/shell

![[dockerlabs/dificil/predictable/root.png]]

y somos root!

