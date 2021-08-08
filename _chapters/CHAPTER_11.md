---
title: Capítulo 11
---

# 11. Controlando o *daemon* do Docker

Antes de tudo, vamos tentar entender o que é um *daemon*. Sabemos que,
em sistemas operacionais *multitask*, isto é, em um sistema operacional
capaz de executar mais de uma tarefa por vez (*not really*), um *daemon*
é um software que roda de forma independente em *background*. Ele
executa certas ações predefinidas em resposta a certos eventos. Pois
bem, o *daemon* do Docker é exatamente isso: uma espécie de processo-pai
que controla tudo, *containers*, imagens, etc., etc., etc.

Até o Docker 1.7 as configurações referentes especificamente ao *daemon*
se confundiam bastante com configurações globais -- isso porque quando
você digitava lá o "docker -help" um monte de coisas retornava, e você
não sabia o que era o quê. A partir da versão 1.8 tivemos o "docker
daemon", e agora, mais recentemente, acreditamos que na versão 18.03 do
Docker, ele foi substituído pelo "dockerd", que resolve de vez esse
problema e trata especificamente de configurações referentes,
obviamente, ao *daemon* do Docker.

## 11.1. O Docker sempre utiliza 172.16.X.X ou posso configurar outro intervalo de IP?

Sim, você pode configurar outro *range* para serem utilizados pela
*bridge* "docker0" e também pelas interfaces dos *containers*.

Para que você consiga configurar um *range* diferente para utilização do
Docker é necessário iniciá-lo com o parâmetro "\--bip".

```bash
# dockerd --bip 192.168.0.1/24
```

Assim, você estará informando ao Docker que deseja utilizar o IP
"192.168.0.1" para sua *bridge* "docker0" e, consequentemente, para a
*subnet* dos *containers*.

Você também poderá utilizar o parâmetro "\--fixed-cidr" para restringir
o *range* que o Docker irá utilizar para a *bridge* "docker0" e para a
*subnet* dos *containers*.

```bash
# dockerd --fixed-cidr 192.168.0.0/24
```

## 11.2. Opções de *sockets*

*Sockets* são *end-points* com as quais duas ou mais aplicações ou
processos se comunicam em um ambiente, geralmente um "IP:porta" ou um
arquivo, como no caso do *Unix Domain Sockets*.

Atualmente o Docker consegue trabalhar com três tipos de *sockets*,
Unix, TCP e FD, e por *default* ele usa *unix sockets*. Você deve ter
notado que, ao *startar* seu Docker, foi criado um arquivo em
"/var/run/docker.sock". Para fazer alterações nele você vai precisar ou
de permissão de *root* ou de que o usuário que esteja executando as
ações faça parte do grupo "docker", como dissemos no começo deste livro,
lembra?

Por mais prático que isso seja, existem algumas limitações, como, por
exemplo, o *daemon* só poder ser acessado localmente. Para resolver isso
usamos geralmente o TCP. Nesse modelo nós definimos um IP, que pode ser
tanto "qualquer um" (0.0.0.0 e uma porta) como um IP específico e uma
porta.

Nos sistemas baseados em *systemd* você ainda pode se beneficiar do
*systemd socket activation*, uma tecnologia que visa economia de
recursos. Consiste basicamente em ativar um *socket* somente enquanto
uma conexão nova chega e desativar quando não está sendo mais usado.

Além disso tudo, dependendo do seu ambiente, você também pode fazer o
Docker escutar em diferentes tipos de *sockets*, o que é feito através
do parâmetro "-H" do comando "dockerd".

Exemplos:

### 11.2.1. *Unix Domain Socket*

```bash
root@linuxtips:~# dockerd -H unix:///var/run/docker.sock
INFO[0000] [graphdriver] using prior storage driver "aufs"
INFO[0000] Graph migration to content-addressability took 0.00 seconds
INFO[0000] Firewalld running: false
INFO[0000] Default bridge (docker0) is assigned with an IP address 172.17.0.0/16. Daemon option --bip can be used to set a preferred IP
address
WARN[0000] Your kernel does not support swap memory limit.
INFO[0000] Loading containers: start.
..........................
INFO[0000] Loading containers: done.
INFO[0000] Daemon has completed initialization
INFO[0000] Docker daemon commit=c3959b1 execdriver=native-0.2 graphdriver=aufs version=1.10.2
INFO[0000] API listen on /var/run/docker.sock
```

### 11.2.2. TCP

```bash
root@linuxtips:~# dockerd -H tcp://0.0.0.0:2375
WARN[0000] /! DON'T BIND ON ANY IP ADDRESS WITHOUT setting -tlsverify IF YOU DON'T KNOW WHAT YOU'RE DOING /!
INFO[0000] [graphdriver] using prior storage driver "aufs"
INFO[0000] Graph migration to content-addressability took 0.01 seconds
INFO[0000] Firewalld running: false
INFO[0000] Default bridge (docker0) is assigned with an IP address 172.17.0.0/16. Daemon option --bip can be used to set a preferred IP address
WARN[0000] Your kernel does not support swap memory limit.
INFO[0000] Loading containers: start.
..........................
INFO[0000] Loading containers: done.
INFO[0000] Daemon has completed initialization
INFO[0000] Docker daemon commit=c3959b1 execdriver=native-0.2 graphdriver=aufs version=1.10.2
INFO[0000] API listen on [::]:2375
```

## 11.3. Opções de *storage*

Sendo o cara que controla tudo, naturalmente é possível passar opções
que mudam a forma como o Docker se comporta ao trabalhar com *storages*.
Como falamos anteriormente, o Docker suporta alguns *storage drivers*,
todos baseados no esquema de *layers*.

Essas opções são passadas para o *daemon* pelo parâmetro
"\--storage-opt", com o qual itens relacionados ao *Device Mapper*
recebem o prefixo "dm" e "zfs" para (adivinha?) o ZFS. A seguir vamos
demonstrar algumas opções mais comuns:

-   **dm.thinpooldev** -- Com esta opção você consegue especificar o *device* que será usado pelo *Device Mapper* para desenvolver o *thin-pool* que ele usa para criar os *snapshots* usados por *containers* e imagens.

Exemplo:

```bash
root@linuxtips:~# dockerd --storage-opt dm.thinpooldev=/dev/mapper/thin-pool
INFO[0000] [graphdriver] using prior storage driver "aufs"
INFO[0000] Graph migration to content-addressability took 0.00 seconds
INFO[0000] Firewalld running: false
INFO[0000] Default bridge (docker0) is assigned with an IP address 172.17.0.0/16. Daemon option --bip can be used to set a preferred IP address
WARN[0000] Your kernel does not support swap memory limit.
INFO[0000] Loading containers: start.
................................
INFO[0000] Loading containers: done. 
INFO[0000] Daemon has completed initialization 
INFO[0000] Docker daemon commit=c3959b1 execdriver=native-0.2 graphdriver=aufs version=1.10.2
INFO[0000] API listen on /var/run/docker.sock
```

-   **dm.basesize** -- Este parâmetro define o tamanho máximo do *container*. O chato disso é que você precisa deletar tudo dentro de "/var/lib/docker" (o que implica em matar todos os *containers* e imagens) e *restartar* o serviço do Docker.


```bash
root@linuxtips:~# dockerd --storage-opt dm.basesize=10G
INFO[0000] [graphdriver] using prior storage driver "aufs"
INFO[0000] Graph migration to content-addressability took 0.00 seconds
INFO[0000] Firewalld running: false
INFO[0000] Default bridge (docker0) is assigned with an IP address 172.17.0.0/16. Daemon option --bip can be used to set a preferred IP address
WARN[0000] Your kernel does not support swap memory limit.
INFO[0000] Loading containers: start.
..........................
INFO[0000] Loading containers: done.
INFO[0000] Daemon has completed initialization
INFO[0000] Docker daemon commit=c3959b1 execdriver=native-0.2 graphdriver=aufs version=1.10.2
INFO[0000] API listen on /var/run/docker.sock
```

-   **dm.fs** -- Especifica o *filesystem* do *container*. As opções suportadas são: **EXT4** e **XFS**.

## 11.4. Opções de rede

Também é possível controlar como o *daemon* se comportará em relação à
rede:

-   **\--default-gateway** -- Autoexplicativo, né? Todos os *containers* receberão esse IP como *gateway*.

-   **\--dns** -- Também sem segredo: é o DNS que será usado para consultas.

-   **\--dns-search** -- Especifica o domínio a ser procurado, assim você consegue pesquisar máquinas sem usar o fqdn.

-   **\--ip-forward** -- Esta opção habilita o roteamento entre *containers.* Por padrão, ela já vem *setada* como *true*.

## 11.5. Opções diversas

-   **\--default-ulimit** -- Passando isso para o *daemon*, todos os *containers* serão iniciados com esse valor para o "ulimit". Esta opção é sobrescrita pelo parâmetro "\--ulimit" do comando "docker container run", que geralmente vai dar uma visão mais específica.

-   **\--icc** -- "icc" vem de *inter container comunication*. Por padrão, ele vem marcado como *true*; caso você não queira esse tipo de comunicação, você pode marcar no *daemon* como *false*.

-   **\--log-level** -- É possível alterar também a forma como o Docker trabalha com *log*; em algumas situações (geralmente *troubleshoot*) você pode precisar de um *log* mais "verboso", por exemplo.
