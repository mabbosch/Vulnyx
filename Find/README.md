# Find (Vulnyx)

Creado: 6 de noviembre de 2024 22:58

RESOLUCIÓN DE LA MÁQUINA FING (Vulnyx)

Empezamos la resolución de la máquina Fing de nivel low de Vulnyx.

Enumeración de puertos

Enumeramos los puertos abiertos con nmap.

![](Find%20(Vulnyx)/image5.png)

Encontramos el puerto SSH, HTTP y el puerto 79 que corre un servicio llamado finger.

En el puerto 80 solo tenemos una página por defecto de apache, así que investigaremos de qué se trata finger, que es un protocolo que se usa para obtener información sobre los usuarios de un sistema linux. Miramos de enumerar usuarios pero no tenemos suerte, aunque probamos con usuario root, y si existe pero tampoco nos sirve demasiado.

![](Find%20(Vulnyx)/image6.png)

## Enumeración de usuarios

Buscamos información sobre vulnerabilidades de Finger y encontramos una herramienta de Pentestmonkey para enumerar usuarios.

[https://pentestmonkey.net/tools/user-enumeration/finger-user-enum](https://pentestmonkey.net/tools/user-enumeration/finger-user-enum)

La descargamos y la ejecutamos.

![](Find%20(Vulnyx)/image2.png)

Después de probar varios diccionarios nos sale un potencial usuario llamado Adam.

## Acceso inicial por SSH

Como tenemos el puerto SSH abierto probamos de usar Hydra para sacar la contraseña de adam (Tarda un poco)

![](Find%20(Vulnyx)/image1.png)

Ya tenemos la contraseña de adam así que ya podemos conectarnos por SSH, y conseguir la bandera de usuario.

![](Find%20(Vulnyx)/image7.png)

## Escalada de privilegios

Para la escalada de privilegios sudo -l no nos sirve, pero los binarios SUID, nos da una pista. Nos sale un binario llamado doas, que no es habitual, investigamos sobre el i se trata de una utilidad que permite a los usuarios ejecutar comandos con los privilegios de otro usuario, generalmente el usuario root. Es una alternativa a sudo. el archivo de configuración se encuentra en el directorio /etc. Le echamos un vistazo y nos dice que podemos ejecutar el comando find como root sin contraseña.

![](Find%20(Vulnyx)/image8.png)

Nos vamos a la página de GTFObins para buscar instrucciones.

![](Find%20(Vulnyx)/image3.png)

Como no disponemos del comando sudo adaptaremos el comando con doas y la ruta a find.

Nos convertimos en root y ya podemos leer la flag. 

![](Find%20(Vulnyx)/image4.png)