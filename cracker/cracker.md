nmap: 22,80

![image](https://github.com/user-attachments/assets/d1cc6252-cf61-4d24-a732-3abe3ca0efd3)

en la pagina al final abajo hay un "cracker.dl"  que lo agregamos al /etc/hosts

![image](https://github.com/user-attachments/assets/153943c0-e94f-44ba-a48b-3c04591585cd)

ahora ingresamos

![image](https://github.com/user-attachments/assets/9a79dfd6-f078-45ec-bb28-174ad044f28e)

no hay nada bueno

si buscamos directorios no hay nada, pero si si subdominios

     wfuzz -c -u cracker.dl -H "HOST: FUZZ.cracker.dl" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt  --hl=162

nos dio: 

![image](https://github.com/user-attachments/assets/bdacec94-0d7f-4c53-9865-c3d6e559eed1)

![image](https://github.com/user-attachments/assets/865ce8e2-e7d4-4e01-bdca-4de84368367b)

si descargamos me interesa una linea:

![image](https://github.com/user-attachments/assets/0390375a-f62d-4ee2-bc81-27373055b2fa)

es un ejecutable lo que es un software, así que le damos permisos y ejecutamos

![image](https://github.com/user-attachments/assets/18240e38-dfb7-4f66-a37f-797cbfc04960)

hay dos opciones que funcionan seria ghidra o descompone el software en busca de alguna credencial que no este guardada de forma correcta.

vamos a intentar primero descomponiendo

     string Paneladmin

y da algo interesante 

![image](https://github.com/user-attachments/assets/e94ae06a-311a-49bf-ac36-ee0a9a9248d5)

la guardamos como nano

![image](https://github.com/user-attachments/assets/ecd14b19-f954-4c10-bfb5-75d49f0cd723)

y logramos

![image](https://github.com/user-attachments/assets/6d58798e-ad7b-45be-991a-ceec83117265)

y nos da una contraseña

![image](https://github.com/user-attachments/assets/f5c0b52d-19eb-4e56-aa53-421004ea64fd)

entramos con la contraseña por el ssh

![image](https://github.com/user-attachments/assets/6279d19f-99f6-40dc-b24d-a023cffd544c)

luego de buscar mucho, probé el binario  como contraseña de root y funciono

![image](https://github.com/user-attachments/assets/f8722e4f-79e5-4a49-97aa-88935c16a346)

somos root
