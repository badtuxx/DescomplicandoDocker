---
title: Capítulo 03
---

# 3. Instalando o Docker

## 3.1. Quero instalar, vamos lá?

Bom, dado que você já sabe o que é um *container* e o que é o tal do
Docker, chegou a hora de pôr a mão na massa. Vamos instalar o Docker
pela primeira vez!

O *daemon* do Docker roda nativo em distribuições Linux, e por isso a
instalação em sistemas operacionais que não sejam Linux consiste
basicamente em subir uma VM e rodar o *daemon* de lá. O cliente, no
entanto, pode ser instalado nos principais sistemas operacionais
disponíveis atualmente.

Para realizar a instalação do Docker em máquinas Linux é bastante
simples. Precisamos somente observar alguns pontos:

-   O Docker não suporta processadores 32 *bits*.

-   O Docker é suportado (*stable*) somente na versão do *kernel* 3.8 ou superior.

-   O *kernel* deve ter suporte aos sistemas de arquivos utilizados pelo Docker, como o AUFS, *Device Mapper*, OverlayFS, etc.

-   O *kernel* deverá ter suporte a *cgroups* e *namespaces*, o que normalmente já vem por *default* habilitado na maioria das *distros*.

Você também pode acessar a URL:
[https://docs.docker.com/install/](https://docs.docker.com/install/).
Lá é possível aprender a instalar o Docker em diversas distribuições
Linux, nos principais *clouds* e também no MacOS e no Windows.

Neste livro vamos utilizar a distribuição Ubuntu Linux, porém não muda
nada para outras distribuições. Chega de conversa, vamos lá!

Primeiro, vamos verificar a versão do *kernel* para saber se ele é
compatível com o Docker:

```bash
# uname -r
```

## 3.2. Instalando no Debian/Centos/Ubuntu/Suse/Fedora

A instalação do Docker é bastante simples. Você pode optar por
instalá-lo utilizando os pacotes disponíveis para sua *distro* -- por
exemplo, o *apt-get* ou *yum.*

Nós preferimos fazer a instalação através da execução do *curl* a
seguir, que irá executar um *script* e detectará qual a distribuição que
estamos utilizando, para então adicionar o repositório oficial do Docker
em nosso gerenciador de pacotes, o *rpm* ou *apt*, por exemplo.

```bash
# curl -fsSL https://get.docker.com/ | sh
```

Assim ele sempre buscará a versão mais recente do Docker. :)

## 3.3. Instalando 'manualmente' no Debian

Caso você esteja utilizando o Debian e queira realizar a instalação
através dos pacotes disponíveis no repositório, faça:

```bash
# apt-key adv --keyserver \
  hkp://pgp.mit.edu:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
```

Agora vamos criar/editar o arquivo "/etc/apt/sources.list.d/docker.list"
e adicionar o endereço do repositório correspondente à versão do seu
Debian. No nosso caso estamos utilizando a versão Debian 8, também
conhecida como Jessie.

```bash
# vim /etc/apt/sources.list.d/docker.list # Debian Jessie

deb https://apt.dockerproject.org/repo debian-jessie main
```

Após adicionar a linha anterior, é necessário atualizar a lista de
repositórios executando:

```bash
# apt-get update
```

Após finalizar a atualização da lista de repositórios disponíveis, já
podemos fazer a instalação do Docker. O nome do pacote é "docker-ce". :)

```bash
# apt-get install docker-ce
```

Vamos verificar se o Docker está em execução. Digite na linha de comando
o seguinte:

```bash
# /etc/init.d/docker status
```

Ou:

```bash
# service docker status

docker container stop/waiting
```

Com isso, podemos verificar se o processo está em execução. Como podemos
notar, o *daemon* do Docker não está em execução, portanto vamos
iniciá-lo.

```bash
# service docker start
docker container start/running, process 4303

# service docker status
docker container start/running, process 4303
```

Perfeito! Agora já temos o Docker instalado e pronto para começar a
brincar com os *containers*. \\o/

### 3.3.1. Dica importante

Por padrão, o *daemon* do Docker faz *bind* em um *socket* Unix, e não
em uma porta TCP. *Sockets* Unix, por sua vez, são de propriedade e de
uso exclusivo do usuário *root* (por isso o Docker sempre é iniciado
como *root*), mas também podem ser acessados através do *sudo* por
outros usuários.

Para evitar que você tenha que ficar usando *sudo* ao rodar comandos do
Docker, crie um grupo chamado *docker* e adicione o seu usuário a ele.
Pare o serviço e inicie-o novamente.

Infelizmente, nem tudo são flores. Esse procedimento faz com que o
usuário tenha **os mesmos privilégios do usuário *root*** em operações
relacionadas ao Docker. Mais informações no link:
[https://docs.docker.com/engine/security/](https://docs.docker.com/engine/security/).

Para criar um grupo no Linux e adicionar um usuário não tem segredo,
basta rodar:

```bash
$ sudo usermod -aG docker user
```

Dica de um milhão de dólares: **user** = **seu usuário**. :D
