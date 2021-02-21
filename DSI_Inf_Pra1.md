# Practica 1: Configuración de máquina virtual en el Iaas

```
Universidad: Universidad de La laguna
Asignatura: Desarrollo de Sistemas Informaticos
Curso: 2020 - 2021
Autor: XueMei Lin
```

## 1. Introducción

Respecto a la practica, hemos utilizamos un sistema operativo Linux para hacer la configuración de maquina virtual en el Iaas (un servicio que ofrece la Universidad de La Laguna) con el fin de poder trabajar mejor en nuestra asignatura de ¨Desarrollo de Sistemas Informático¨.

## 2. Objetivos

El objetivo de esta práctica consiste en hacer **la configuración de la máquina virtual** que tienen disponible a través del servicio Iaas que ofrece ULL (la Universidad de La Laguna). Además nos centraremos en la **instalación y configuración de todas las herramientas** necesarias para trabajar en la asignatura.

## 3. La configuración de la máquina virtual en el IaaS

###3.1. La configuración del nombre de la máquina virtual

En primer lugar, tenemos que conectar la VPN de la ULL con el fin de acceder a la máquina virtual que está disponible en Iaas.

Haciendo uso de lo siguiente comando, nos permite conectar a la vpn de la ULL.
```
sudo vpnc --gateway vpn.ull.es --id ULL --username alu0101225845
```
Una vez que hemos conectado la vpn de la ULL, ya tenemos el permiso de acceder [al Iaas de la ULL](https://iaas.ull.es/), aparece una máquina virtual que se llama DSI-28 (en mi caso), y obtengo la IP de dicha máquina: 10.6.129.16. A partir de la dirección IP, puedo conectar desde mi máquina local a la máquina virtual que hemos mensionado antes, con el comando:
```
ssh usuario@10.6.129.16
```
Por ser la primera vez que hemos conectado, nos va a pedir que cambiamos la contraseña, y volvemos a conectar otra vez dicha máquina virtual desde nuestro máquina local.

### 3.2. La configuración del nombre de la máquina virtual

Después de conectar otra vez a nuestra máquina, podemos editar /etc/hostname para cambiar el nombre de nuestra máquina.

```
usuario@ubuntu:~$ cat /etc/hostname
ubuntu
usuario@ubuntu:~$ sudo vi /etc/hostname
usuario@ubuntu:~$ cat /etc/hostname
iaas-dsi2
```
No obstante, también tenemos que cambiar algunos parámtros que está en  /etc/hosts.
```
usuario@ubuntu:~$ cat /etc/hostname
127.0.0.1	localhost
127.0.1.1	ubuntu
...
usuario@ubuntu:~$ sudo vi /etc/hosts
usuario@ubuntu:~$ cat /etc/hostname
127.0.0.1	localhost
127.0.1.1	iaas-dsi2
...
```

Una vez que modificamos todos los parámetros necesarios, hacemos lo siguiente comando para guardar los cambios.
```
usuario@ubuntu:~$ sudo apt update
...
usuario@ubuntu:~$ sudo apt upgrade
...
```

Reiniciamos la máquina virtual:

```
usuario@ubuntu:~$ sudo reboot
Connection to 10.6.129.16 closed by remote host.
Connection to 10.6.129.16 closed.
```
### 3.3. La conexión de la máquina virtual desde la máquina local
Para conectar la máquina virtual directamente sin la dirección IP, podemos incluir la información de conexión de la máquina  virtual en el fichero de hosts de la máquina local.

En primer lugar, tenemos que editar el fichero de /etc/hosts, añadimos nuestra máquina virtual:

```
lyz@lyzpc:~$ cat /etc/hosts
lyz@lyzpc:~$ sudo vi /etc/hosts
lyz@lyzpc:~$ cat /etc/hosts
127.0.0.1		localhost
127.0.1.1		lyzpc
10.6.129.16		iaas-dis2
...
```

En segundo lugar,  la configuración de la máquina local, tenemos que general la clave pública-privada, y después comprobamos que si se ha generado de forma correcta.

```
lyz@lyzpc:~$ ssh-keygen
lyz@lyzpc:~$ cat .ssh/id_rsa.pub 
```

Usando el comando:


```
lyz@lyzpc:~$ ssh-copy-id usuario@iaas-dsi2
```

Eso nos permite copiar nuestra clave pública desde la máquina local a la máquina virtual. Por lo tanto, ya podemos iniciar la sesión en la máquina virtual sin necesidad de introducir ninguna contraseña. Además, en el prompt de la máquina virtual aparece el nuevo nombre de host configurado.

Asimismo, si tampoco queremos utilizar el nombre de usuario de la máquina virtual para conectarse vía **SSH**, en este caso es **usuario**, podemos hacer lo siguiente:

```
lyz@lyzpc:~$ touch ~/.ssh/config 
lyz@lyzpc:~$ vi ~/.ssh/config 
lyz@lyzpc:~$ cat ~/.ssh/config
Host iaas-dsi2
  HostName iaas-dsi2
  User usuario
```

Los comandos simplemente hacen que crear un fichero de ssh-config, añadir la información de la máquina virtual que queremos conectar cada vez, y le ponemos el nombre como **iaas-dsi2**, así podremos iniciar una conexión SSH simplemente poniendo:

```
lyz@lyzpc:~$ ssh iaas-dsi2
```

Generamos las claves pública-privada en su máquina virtual también: 

```
lyz@lyzpc:~$ ssh-keygen
lyz@lyzpc:~$ cat .ssh/id_rsa.pub 
```

### 3.4. Instalación de git y Node.js en la máquina virtual del IaaS

Instalamos **git** en la máquina virtual: 

```
usuario@iaas-dsi2:~$ sudo apt install git
```

Para configurar git en nuestra máquina virtual, hacemos lo siguiente:

```
usuario@iaas-dsi2:~$ git config --global user.name "XueMei Lin"
usuario@iaas-dsi2:~$ git config --global user.email alu0101225845@ull.edu.es
usuario@iaas-dsi2:~$ git config --list
user.name=XueMei Lin
user.email=alu0101225845@ull.edu.es
```

Y luego necesitamos configurar el prompt de la terminal con el fin de determinar la rama actual en la que nos encontramos cuando vamos a acceder a algún directorio que resulta asociado a un repositorio git.

Descargamos el script  [**git-prompt.sh**](https://github.com/git/git/blob/master/contrib/completion/git-prompt.sh), modificamos el fichero **~/.bashrc**,  incluimos al final del fichero el siguiente contenido:

```
source ~/.git-prompt.sh
PS1='\[\033]0;\u@\h:\w\007\]\[\033[0;34m\][\[\033[0;31m\]\w\[\033[0;32m\]($(git branch 2>/dev/null | sed -n "s/\* \(.*\)/\1/p"))\[\033[0;34m\]]$'
```

y utilizamos el comendo:

```
usuario@iaas-dsi2:~$ exec bash -l
```

para reiniciar la terminal, y nos resulta que el formato del prompt ha cambiado.

Para comprobar si el prompt funciona correctamente, la rama actual de trabajo cuando vamos a acceder a un directorio asociado a un repositorio, tenemos que añadir la clave públic de la máquina virtual en la configuración de las claves de nuestra cuenta de Github. Tiene la finalidad de ser más fácil de trabajar con repositorios remotos y también clonar alguno de los respositorios para acer la prueba.

Hacemos **~/.ssh/id_rsa.pub** en el prompt, los pasos hay que seguir son los siguientes:

1. Copiar la clave pública-privada
2. Ir a la cuenta de GitHub ( **_account settings_** )
3. Ir a la parte **_SSH and GPG keys_**
4. Añadir el título: ***yo tengo usuario@iaas-dsi2***
5. Pegar la clave pública de la máquina virtual
6. Pulsar sobre el botón ***Add SSH key***

Ahora ejecutamos el siguiente comando para clonar un repositorio y comprobamos que funciona todo bien:

```
[~()]$git clone git@github.com:ULL-ESIT-INF-DSI-2021/prct01-iaas-vscode.git
Clonando en 'prct01-iaas-vscode'...
...
[~()]$ls
prct01-iaas-vscode
[~()]$cd prct01-iaas-vscode/
[~/prct01-iaas-vscode(main)]$
```

y llegamos a instalar [Node Version Manager (nvm)](https://github.com/nvm-sh/nvm), el gestor de versiones de Node.js. [Node.js](https://nodejs.org/en/) es un entorno para la ejecución de código desarrollado en JavaScript y variantes, **TypeScript** es un ejemplo de ello. Lo instalamos, y comprobamos que hemos instalado nvm de forma satisface:

```
[~()]$wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
[~()]$exec bash -l
[~()]$nvm --version
0.37.2
```

Para obtener la versión mas nueva de Node.js, podemos hacer:

```
[~()]$nvm install node
Downloading and installing node v15.8.0...
...
Now using node v15.8.0 (npm v7.5.1)
Creating default alias: default -> node (-> v15.8.0)
[~()]$node --version
v15.8.0
[~()]$npm --version
7.5.1
```

Observamos que hemos instalado la última versión de Node.js (15.8.0) y la última versión de [npm](https://www.npmjs.com/), es el gestor de paquetes de Node.js.

Si deseamos instalar una versión concreta de Node.js:

```
[~()]$nvm install 12.0.0
Downloading and installing node v12.0.0...
...
Now using node v12.0.0 (npm v6.9.0)
[~()]$node --version
v12.0.0
[~()]$npm --version
6.9.0
```

Como resultado, después de instalar la versión 12.0.0, obtenemos dos versiones de Node.js.

Por último, si queremos cambiar entre versiones, simplemente hacemos:

Mirar versiones que tenemos instalado:

```
[~()]$nvm list
```

Cambiar la versión que queremos:

```
[~()]$nvm use v15.8.0 
```

Comprobar la versión seleccionada:

```
[~()]$node --version
v15.8.0
[~()]$npm --version
7.5.1
```

De este modo, ya tenemos instalado git y Node.js en nuestra máquina virtual.

## 4. Conclusiones

Como conclusión,  he aprendido más cosas sobre la conexión entre dos máquinas y GitHub en esta práctica. No he visto mucho dificultad de hacer dicha práctica . Los pasos que he seguido para llegar a cabo a conectar a la máquina virtual me parece más fácil, más rápido y más simple. Desde mi punto de vista utilizar el GitHub para trabajar mediente el comando git. me resulta más fácil de trabajar de forma remota. Después de terminar esa práctica, me interesa mucho a aprender cosas sobre Node.js.

## 5. Bibliografía

Comando git: https://www.hostinger.es/tutoriales/comandos-de-git

Node.js: https://nodejs.org/es/

