---
title: Capítulo 12
---

# 12. Docker Machine

## 12.1. Ouvi dizer que minha vida ficaria melhor com o Docker Machine!

Certamente!

Com o Docker Machine você consegue, com apenas um comando, iniciar o seu
projeto com Docker!

Antes do Docker Machine, caso quiséssemos montar um Docker Host, era
necessário fazer a instalação do sistema operacional, instalar e
configurar o Docker e outras ferramentas que se fazem necessárias.

Perderíamos um tempo valoroso com esses passos, sendo que já poderíamos
trabalhar efetivamente com o Docker e seus *containers*.

Porém, tudo mudou com o Docker Machine! Com ele você consegue criar o
seu Docker Host com apenas um comando. O Docker Machine consegue
trabalhar com os principais *hypervisors* de VMs, como o VMware, Hyper-V
e Oracle VirtualBox, e também com os principais provedores de
infraestrutura, como AWS, Google Compute Engine, DigitalOcean,
Rackspace, Azure, etc.

Para que você possa ter acesso a todos os *drivers* que o Docker Machine
suporta, acesse:
[https://docs.docker.com/machine/drivers/](https://docs.docker.com/machine/drivers/).

Quando você utiliza o Docker Machine para instalar um Docker Host na
AWS, por exemplo, ele disponibilizará uma máquina com Linux e com o
Docker e suas dependências já instaladas.

### 12.1.1. Vamos instalar?

A instalação do Docker Machine é bastante simples, por isso, vamos parar
de conversa e brincar! Vale lembrar que é possível instalar o Docker
Machine no Linux, MacOS ou Windows.

Para fazer a instalação do Docker Machine no Linux, faça:

```bash
# curl -L https://github.com/docker/machine/releases/download/v0.12.0/docker-machine-`uname -s`-`uname -m` >/tmp/docker-machine
# chmod +x /tmp/docker-machine
# sudo cp /tmp/docker-machine /usr/local/bin/docker-machine
```

Para seguir com a instalação no MacOS:

```bash
$ curl -L https://github.com/docker/machine/releases/download/v0.15.0/docker-machine-`uname -s`-`uname -m` >/usr/local/bin/docker-machine
$ chmod +x /usr/local/bin/docker-machine
```

Para seguir com a instalação no Windows caso esteja usando o Git *bash*:

```bash
$ if [[ ! -d "$HOME/bin" ]]; then mkdir -p "$HOME/bin"; fi
$ curl -L https://github.com/docker/machine/releases/download/v0.15.0/docker-machine-Windows-x86_64.exe > "$HOME/bin/docker-machine.exe"
$ chmod +x "$HOME/bin/docker-machine.exe"
```

Para verificar se ele foi instalado e qual a sua versão, faça:

```bash
root@linuxtips:~# docker-machine version
docker-machine version 0.15.0, build b48dc28

root@linuxtips:~#
```

Pronto. Como tudo que é feito pelo Docker, é simples de instalar e fácil
de operar. :)

### 12.1.2. Vamos iniciar nosso primeiro projeto?

Agora que já temos o Docker Machine instalado em nossa máquina, já
conseguiremos fazer a instalação do Docker Host de forma bastante
simples -- lembrando que, mesmo que tivéssemos feito a instalação do
Docker Machine no Windows, conseguiríamos tranquilamente comandar a
instalação do Docker Hosts na AWS em máquinas Linux. Tenha em mente que
a máquina na qual você instalou o Docker Machine é o maestro que
determina a criação de novos Docker Hosts, seja em VMs ou em alguma
nuvem como a AWS.

Em nosso primeiro projeto, vamos fazer com que o Docker Machine instale
o Docker Host utilizando o VirtualBox.

Como utilizaremos o VirtualBox, é evidente que precisamos ter instalado
o VirtualBox em nossa máquina para que tudo funcione. ;)

Portanto:

```bash
root@linuxtips:~# apt-get install virtualbox
```

Para fazer a instalação de um novo Docker Host, utilizamos o comando
"docker-machine create". Para escolher onde criaremos o Docker Host,
utilizamos o parâmetro "\--driver", conforme segue:

```bash
root@linuxtips:~# docker-machine create --driver virtualbox linuxtips
Running pre-create checks...
(linuxtips) Default Boot2Docker ISO is out-of-date, downloading the latest release...
(linuxtips) Latest release for github.com/boot2docker/boot2docker is v17.05.0-ce
(linuxtips) Downloading /Users/linuxtips/.docker/machine/cache/boot2docker.iso from https://github.com/boot2docker/boot2docker/releases/download/v17.05.0-ce/boot2docker.iso...
(linuxtips) 0%....10%....20%....30%....40%....50%....60%....70%....80%....90%....100%
Creating machine...
(linuxtips) Copying /Users/linuxtips/.docker/machine/cache/boot2docker.iso to /Users/linuxtips/.docker/machine/machines/linuxtips/boot2docker.iso...
(linuxtips) Creating VirtualBox VM...
(linuxtips) Creating SSH key...
(linuxtips) Starting the VM...
(linuxtips) Check network to re-create if needed...
(linuxtips) Waiting for an IP...
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with boot2docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!

To see how to connect your Docker Client to the Docker Engine running on
this virtual machine, run: docker-machine env linuxtips

root@linuxtips:~#
```

Onde:

-   **docker-machine create** -- Cria um novo Docker Host.

-   **\--driver virtualbox** -- Irá criá-lo utilizando o VirtualBox.

-   **linuxtips** -- Nome da VM que será criada.

Para visualizar o *host* que acabou de criar, basta digitar o seguinte
comando:

```bash
root@linuxtips:~# docker-machine ls
NAME           ACTIVE  DRIVER     STATE   URL                         SWARM   DOCKER    ERRORS
linuxtips      -       virtualbox Running tcp://192.168.99.100:2376           v18.06.0  -
```

Como podemos notar, o nosso *host* está sendo executado perfeitamente!
Repare que temos uma coluna chamada URL, correto? Nela temos a URL para
que possamos nos comunicar com o nosso novo *host*.

Outra forma de visualizar informações sobre o *host*, mais
especificamente sobre as variáveis de ambiente dele, é digitar:

```bash
root@linuxtips:~# docker-machine env linuxtips
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.100:2376"
export DOCKER_CERT_PATH="/Users/linuxtips/.docker/machine/machines/linuxtips"
export DOCKER_MACHINE_NAME="linuxtips"
# Run this command to configure your shell:
# eval "$(docker-machine env linuxtips)"

root@linuxtips:~#
```

Serão mostradas todas as variáveis de ambiente do *host*, como URL,
certificado e nome.

Para que você possa acessar o ambiente desse *host* que acabamos de
criar, faça:

```bash
root@linuxtips:~# eval "$(docker-machine env linuxtips)"
```

O comando "eval" serve para definir variáveis de ambiente através da
saída de um comando, ou seja, as variáveis que visualizamos na saída do
"docker-machine env linuxtips".

Agora que já estamos no ambiente do *host* que criamos, vamos visualizar
os *containers* em execução:

```bash
root@linuxtips:~# docker container ls
```

Claro que ainda não temos nenhum *container* em execução; vamos iniciar
o nosso primeiro agora:

```bash
root@linuxtips:~# docker container run busybox echo "LINUXTIPS, VAIIII"
Unable to find image 'busybox:latest' locally
latest: Pulling from library/busybox
385e281300cc: Pull complete
a3ed95caeb02: Pull complete
Digest: sha256:4a887a2326ec9e0fa90cce7b4764b0e627b5d6afcb81a3f73c85dc29cea00048
Status: Downloaded newer image for busybox:latest
LINUXTIPS, VAIIII

root@linuxtips:~#
```

Como podemos observar, o *container* foi executado e imprimiu a mensagem
"**LINUXTIPS, VAIIII**", conforme solicitamos.

Lembre-se de que o *container* foi executado em nosso Docker Host, que
criamos através do Docker Machine.

Para verificar o IP do *host* que criamos, faça:

```bash
root@linuxtips:~# docker-machine ip linuxtips
192.168.99.100

root@linuxtips:~#
```

Para que possamos acessar o nosso *host*, utilizamos o parâmetro "ssh"
passando o nome do *host* que queremos acessar:

```bash
root@linuxtips:~# docker-machine ssh linuxtips
```

Para saber mais detalhes sobre o *host*, podemos utilizar o parâmetro
"inspect":

```bash
root@linuxtips:~# docker-machine inspect linuxtips
{
    "ConfigVersion": 3,
    "Driver": {
        "IPAddress": "192.168.99.100",
        "MachineName": "linuxtips",
        "SSHUser": "docker",
        "SSHPort": 57249,
        "SSHKeyPath": "/Users/jeferson/.docker/machine/machines/linuxtips/id_rsa",
        "StorePath": "/Users/jeferson/.docker/machine",
        "SwarmMaster": false,
        "SwarmHost": "tcp://0.0.0.0:3376",
        "SwarmDiscovery": "",
        "VBoxManager": {},
        "HostInterfaces": {},
        "CPU": 1,
        "Memory": 1024,
        "DiskSize": 20000,
        "NatNicType": "82540EM",
        "Boot2DockerURL": "",
        "Boot2DockerImportVM": "",
        "HostDNSResolver": false,
        "HostOnlyCIDR": "192.168.99.1/24",
        "HostOnlyNicType": "82540EM",
        "HostOnlyPromiscMode": "deny",
        "UIType": "headless",
        "HostOnlyNoDHCP": false,
        "NoShare": false,
        "DNSProxy": true,
        "NoVTXCheck": false,
        "ShareFolder": ""
    },
    "DriverName": "virtualbox",
    "HostOptions": {
        "Driver": "",
        "Memory": 0,
        "Disk": 0,
        "EngineOptions": {
            "ArbitraryFlags": [],
            "Dns": null,
            "GraphDir": "",
            "Env": [],
            "Ipv6": false,
            "InsecureRegistry": [],
            "Labels": [],
            "LogLevel": "",
            "StorageDriver": "",
            "SelinuxEnabled": false,
            "TlsVerify": true,
            "RegistryMirror": [],
            "InstallURL": "https://get.docker.com"
        },
        "SwarmOptions": {
            "IsSwarm": false,
            "Address": "",
            "Discovery": "",
            "Agent": false,
            "Master": false,
            "Host": "tcp://0.0.0.0:3376",
            "Image": "swarm:latest",
            "Strategy": "spread",
            "Heartbeat": 0,
            "Overcommit": 0,
            "ArbitraryFlags": [],
            "ArbitraryJoinFlags": [],
            "Env": null,
            "IsExperimental": false
        },
        "AuthOptions": {
            "CertDir": "/Users/jeferson/.docker/machine/certs",
            "CaCertPath": "/Users/jeferson/.docker/machine/certs/ca.pem",
            "CaPrivateKeyPath": "/Users/jeferson/.docker/machine/certs/ca-key.pem",
            "CaCertRemotePath": "",
            "ServerCertPath": "/Users/jeferson/.docker/machine/machines/linuxtips/server.pem",
            "ServerKeyPath": "/Users/jeferson/.docker/machine/machines/linuxtips/server-key.pem",
            "ClientKeyPath": "/Users/jeferson/.docker/machine/certs/key.pem",
            "ServerCertRemotePath": "",
            "ServerKeyRemotePath": "",
            "ClientCertPath": "/Users/jeferson/.docker/machine/certs/cert.pem",
            "ServerCertSANs": [],
            "StorePath": "/Users/jeferson/.docker/machine/machines/linuxtips"
        }
    },
    "Name": "linuxtips"
}
```

Para parar o *host* que criamos:

```bash
root@linuxtips:~# docker-machine stop linuxtips
```

Para que você consiga visualizar o status do seu *host* Docker, digite:

```bash
root@linuxtips:~# docker-machine ls
```

Para iniciá-lo novamente:

```bash
root@linuxtips:~# docker-machine start linuxtips
```

Para removê-lo definitivamente:

```bash
root@linuxtips:~# docker-machine rm linuxtips
Successfully removed linuxtips
```
