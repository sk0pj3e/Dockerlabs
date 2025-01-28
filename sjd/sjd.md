nmap: 22,80

hacemos un gobuster y nos da

> pass.txt

y en la pagina da

     sjd c2pkCg== (sjd)
     admin YWRtaW4K (admin)
     root MTk3MQo= (1971)

lo pasamos a https://gchq.github.io/CyberChef/#recipe=Magic(3,false,false,'')&input=YzJwa0NnPT0

accedemos a sjd

todas las pasamos al magic de la pagina de cyberchef, tenemos acceso a los 3 usuarios y somos root!

