# Psymin (Vulnyx)

Creado: 6 de noviembre de 2024 22:59

MAQUINA PSYMIN

La máquina Psymin es una máquina de Vulnyx de nivel easy.

## Enumeración de puertos

Comenzamos con un escaneo de Nmap. Encontramos tres puertos abiertos: **22 (SSH)**, **80 (HTTP)** y **3000**, donde parece estar disponible PsySH.

![](Psymin%20(Vulnyx)/image10.png)

Primero revisamos la web del puerto 80, pero solo muestra la página por defecto de Nginx. Por eso continuamos con el puerto 3000.

![](Psymin%20(Vulnyx)/image8.png)

## Acceso a PsySH

La web nos muestra la página de nginx por defecto, nada interesante por aquí, probaremos de entrar al puerto 3000, por allí está corriendo el servicio PsyShell , PsySH es una consola interactiva (shell) para ejecutar código PHP sirve para escribir y ejecutar comandos PHP en tiempo real, depurar scripts, y probar fragmentos de código directamente desde la línea de comandos.

![](Psymin%20(Vulnyx)/image1.png)

Nos conectamos al servicio vía netcat;

![](Psymin%20(Vulnyx)/image4.png)

Encontramos una página que nos muestra comandos para Psy

[https://github.com/bobthecow/psysh/wiki/Commands](https://github.com/bobthecow/psysh/wiki/Commands)

Pero al intentar el comando pwd vemos que no nos lo permite ejecutar.

![](Psymin%20(Vulnyx)/image12.png)

Ya que no nos permite ejecutar los comandos intentaremos leer archivos con el comando en php file_get_contents, lo probamos con el archivo /etc/passwd

![](Psymin%20(Vulnyx)/image5.png)

El archivo nos deja ver un potencial usuario llamado Alfred, 

## Obtención de la clave SSH

Intentamos fuerza bruta al puerto SSH, pero nos dice que no es posible el acceso con contraseña, así que probamos de leer el archivo .ssh/id_rsa para ver si nos muestra la clave.

![](Psymin%20(Vulnyx)/image13.png)

Se trata de una clave cifrada así que usaremos ssh2john para sacar el hash y poder sacar la contraseña de la clave.

![](Psymin%20(Vulnyx)/image11.png)

![](Psymin%20(Vulnyx)/image9.png)

Sacamos el password de la clave, alfredo, ahora ya podemos conectarnos vía ssh.

![](Psymin%20(Vulnyx)/image7.png)

Una vez dentro encontramos la primera flag.

## Enumeración interna y acceso a Webmin

Después de varias pruebas para la escalada de privilegios nos damos cuenta que hay un servicio Webmin corriendo por el localhost en el puerto 10000.

![](Psymin%20(Vulnyx)/image2.png)

Probaremos Port Forwarding para redirigir el tráfico de ese puerto a nuestra máquina local con el comando:

`ssh -L 1000:127.0.0.1:10000 -i id_rsa alfred@192.168.1.96`

## Escalada de privilegios

Una vez redirigido ya tenemos acceso desde nuestra máquina al panel de login de webmin.

![](Psymin%20(Vulnyx)/image6.png)

No disponemos de credenciales para acceder pero sabemos que el usuario para webmin por defecto es root, así que después de probar diferentes combinaciones clásicas, acabamos accediendo con root:root.

Una vez dentro y como ya somos root solo nos dirigimos al terminal que hay en las herramientas de webmin y ya podremos leer la flag, máquina pwneada.

![](Psymin%20(Vulnyx)/image3.png)