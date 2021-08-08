---
title: Capítulo 13
---

# 13. Docker Swarm

Bom, agora temos uma ferramenta muito interessante e que nos permite
construir *clusters* de *containers* de forma nativa e com extrema
facilidade, como já é de costume com os produtos criados pelo time do
Docker. ;)

Com o Docker Swarm você consegue construir *clusters* de *containers*
com características importantes como balanceador de cargas e *failover*.

Para criar um *cluster* com o Docker Swarm, basta indicar quais os
*hosts* que ele irá supervisionar e o restante é com ele.

Por exemplo, quando você for criar um novo *container*, ele irá criá-lo
no *host* que possuir a menor carga, ou seja, cuidará do balanceamento
de carga e garantirá sempre que o *container* será criado no melhor
*host* disponível no momento.

A estrutura de *cluster* do Docker Swarm é bastante simples e se resume
a um *manager* e diversos *workers*. O *manager* é o responsável por
orquestrar os *containers* e distribuí-los entre os *hosts workers*. Os
*workers* são os que carregam o piano, que hospedam os *containers*.

## 13.1. Criando o nosso *cluster*!

Uma coisa importante que começou após a versão 1.12 foi a inclusão do
Docker Swarm dentro do Docker, ou seja, hoje quando você realiza a
instalação do Docker, automaticamente você está instalando o Docker
Swarm, que nada mais é do que uma forma de orquestrar seus *containers*
através da criação de um *cluster* com alta disponibilidade,
balanceamento de carga e comunicação criptografada, tudo isso nativo,
sem qualquer esforço ou dificuldade.

Para o nosso cenário, vamos utilizar três máquinas Ubuntu. A ideia é
fazer com que tenhamos dois *managers* e 1 *worker*.

Precisamos ter sempre mais do que um *node* representando o *manager*,
pois, se ficarmos sem *manager*, nosso *cluster* estará totalmente
indisponível.

Com isso temos o seguinte cenário:

-   **LINUXtips-01** -- *Manager* ativo.

-   **LINUXtips-02** -- *Manager*.

-   **LINUXtips-03** -- *Worker*.

Não precisa falar que precisamos ter o Docker instalado em todas essas
máquinas, certo, amiguinho? :D

Para iniciar, vamos executar o seguinte comando na "LINUXtips-01":

```bash
root@linuxtips-01:~# docker swarm init
Swarm initialized: current node (2qacv429fvnret8v09fqmjm16) is now a manager.

To add a worker to this swarm, run the following command:

   docker swarm join --token SWMTKN-1-100qtga34hfnf14xdbbhtv8ut6ugcvuhsx427jtzwaw1td2otj-18wccykydxte59gch2pix 172.31.58.90:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

root@linuxtips-01:~#
```

Com o comando anterior, iniciamos o nosso *cluster*!

Repare no seguinte trecho da saída do último comando:

```bash
# docker swarm join --token SWMTKN-1-100qtga34hfnf14xdbbhtv8ut6ugcvuhsx427jtzwaw1td2otj-18wccykydxte59gch2pix 172.31.58.90:2377
```

Essa linha nada mais é do que toda informação que você precisa para
adicionar *workers* ao seu *cluster*! Como assim?

Simples: o que você precisa agora é executar exatamente esse comando na
próxima máquina que você deseja incluir no *cluster* como *worker*!
Simples como voar, não?

De acordo com o nosso plano, a única máquina que seria *worker* é a
máquina "LINUXtips-03", correto? Então vamos acessá-la e executar
exatamente a linha de comando recomendada na saída do "docker swarm
init".

```bash
root@linuxtips-03:~# docker swarm join --token SWMTKN-1-100qtga34hfnf14xdbbhtv8ut6ugcvuhsx427jtzwaw1td2otj-18wccykydxte59gch2pix 172.31.58.90:2377
This node joined a swarm as a worker.

root@linuxtips-03:~#
```

Maravilha! Mais um *node* adicionado ao *cluster*!

Para que você possa ver quais os *nodes* que existem no *cluster*, basta
digitar o seguinte comando no ***manager* ativo**:

```bash
root@linuxtips-01:~# docker node ls
ID             HOSTNAME       STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
2qac           LINUXtips-01   Ready     Active         Leader           18.03.1-ce
nmxl           LINUXtips-03   Ready     Active                          18.03.1-ce

root@linuxtips-01:~#
```

Como podemos notar na saída do comando, temos dois *nodes* em nosso
*cluster*, um como *manager* e outro como *worker*. A coluna "MANAGER
STATUS" traz a informação de quem é o "Leader", ou seja, quem é o nosso
*manager*.

Em nosso plano nós teríamos dois *managers*, correto?

Agora a pergunta é: como eu sei qual é o *token* que preciso utilizar
para adicionar mais um *node* em meu *cluster*, porém dessa vez como
outro *manager*?

Lembra que, quando executamos o comando para adicionar o *worker* ao
*cluster*, nós tínhamos no comando um *token*? Pois bem, esse *token* é
quem define se o *node* será um *worker* ou um *manager*, e naquela
saída ele nos trouxe somente o *token* para adicionarmos *workers*.

Para que possamos visualizar o comando e o *token* referente aos
*managers*, precisamos executar o seguinte comando no *manager* ativo:

```bash
root@linuxtips-01:~# docker swarm join-token manager
To add a manager to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-100qtga34hfnf14xdbbhtv8ut6ugcvuhsx427jtzwaw1td2otj-3i4jsv4i70odu1mes0ebe1l1e 172.31.58.90:2377 

root@linuxtips-01:~#
```

Para visualizar o comando e o *token* referente aos *workers*:

```bash
root@linuxtips-01:~# docker swarm join-token worker
To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-100qtga34hfnf14xdbbhtv8ut6ugcvuhsx427jtzwaw1td2otj-18wccykydxte59gch2pixq9av 172.31.58.90:2377 

root@linuxtips-01:~#
```

Fácil, não?

Agora o que precisamos é executar na "LINUXtips-02" o comando para
inclusão de mais um *node* como *manager*. Portanto, execute:

```bash
root@linuxtips-02:~# docker swarm join --token SWMTKN-1-100qtga34hfnf14xdbbhtv8ut6ugcvuhsx427jtzwaw1td2otj-3i4jsv4i70odu1mes0ebe1l1e 172.31.58.90:2377

This node joined a swarm as a manager.

root@linuxtips-02:~#
```

Pronto! Agora temos o nosso *cluster* completo com dois *managers* e um
*worker*!

Vamos visualizar os *nodes* que fazem parte de nosso *cluster*.
Lembre-se: qualquer comando para administração do *cluster* ou criação
de serviços deverá obrigatoriamente ser executado no *manager* ativo,
sempre!

```bash
root@linuxtips-01:~# docker node ls
ID             HOSTNAME       STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
2qac           LINUXtips-01   Ready     Active         Leader           18.03.1-ce
j6lm           LINUXtips-02   Ready     Active         Reachable        18.03.1-ce
nmxl           LINUXtips-03   Ready     Active                          18.03.1-ce

root@linuxtips-01:~#
```

Perceba que o "MANAGER STATUS" da "LINUXtips-02" é "Reachable". Isso
indica que ela é um *manager*, porém não é o *manager* ativo, que sempre
carrega o "MANAGER STATUS" como "Leader".

Se nós quisermos saber detalhes sobre determinado *node*, podemos usar o
subcomando "inspect":

```bash
root@linuxtips-01:~# docker node inspect LINUXtips-02
[
    {
        "ID": "x3fuo6tdaqjyjl549r3lu0vbj",
        "Version": {
            "Index": 27
        },
        "CreatedAt": "2017-06-09T18:09:48.925847118Z",
        "UpdatedAt": "2017-06-09T18:09:49.053416781Z",
        "Spec": {
            "Labels": {},
            "Role": "worker",
            "Availability": "active"
        },
        "Description": {
            "Hostname": "LINUXtips-02",
            "Platform": {
                "Architecture": "x86_64",
                "OS": "linux"
            },
            "Resources": {
                "NanoCPUs": 1000000000,
                "MemoryBytes": 1038807040
            },
            "Engine": {
                "EngineVersion": "17.05.0-ce",
                "Plugins": [
                    {
                        "Type": "Network",
                        "Name": "bridge"
                    },
                    {
                        "Type": "Network",
                        "Name": "host"
                    },
                    {
                        "Type": "Network",
                        "Name": "null"
                    },
                    {
                        "Type": "Network",
                        "Name": "overlay"
                    },
                    {
                        "Type": "Volume",
                        "Name": "local"
                    }
                ]
            }
        },
        "Status": {
            "State": "ready",
            "Addr": "172.31.53.23"
        }
    }
]

root@linuxtips-01:~#
```

E se nós quisermos promover um *node worker* para *manager*, como
devemos fazer? Simples como voar, confira a seguir:

```bash
root@linuxtips-01:~# docker node promote LINUXtips-03
Node LINUXtips-03 promoted to a manager in the swarm.

root@linuxtips-01:~# docker node ls
ID             HOSTNAME       STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
2qac           LINUXtips-01   Ready     Active         Leader           18.03.1-ce
j6lm           LINUXtips-02   Ready     Active         Reachable        18.03.1-ce
nmxl           LINUXtips-03   Ready    Active          Reachable        18.03.1-ce

root@linuxtips-01:~#
```

Se quiser tornar um *node manager* em *worker*, faça:

```bash
root@linuxtips-01:~# docker node demote LINUXtips-03
Node LINUXtips-03 demoted to a manager in the swarm.
```

Vamos conferir:

```bash
root@linuxtips-01:~# docker node ls

ID             HOSTNAME       STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
2qac           LINUXtips-01   Ready     Active         Leader           18.03.1-ce
j6lm           LINUXtips-02   Ready     Active         Reachable        18.03.1-ce
nmxl           LINUXtips-03   Ready     Active                          18.03.1-ce

root@linuxtips-01:~#
```

Agora, caso você queira remover um *node* do *cluster*, basta digitar o
seguinte comando no *node* desejado:

```bash
root@linuxtips-03:~# docker swarm leave
Node left the swarm.

root@linuxtips-03:~#
```

E precisamos ainda executar o comando de remoção desse *node* também em
nosso *manager* ativo da seguinte forma:

```bash
root@linuxtips-01:~# docker node rm LINUXtips-03
LINUXtips-03

root@linuxtips-01:~#
```

Com isso, podemos executar o "docker node ls" e constatar que o *node*
foi realmente removido do *cluster*. Caso queira adicioná-lo novamente,
basta repetir o processo que foi utilizado para adicioná-lo, está
lembrado? :D

Para remover um *node* *manager* de nosso *cluster*, precisamos
adicionar a *flag* "\--force" ao comando "docker swarm leave", como
mostrado a seguir:

```bash
root@linuxtips-02:~# docker swarm leave --force
Node left the swarm.

root@linuxtips-02:~#
```

Agora, basta removê-lo também em nosso *node manager*:

```bash
root@linuxtips-01:~# docker node rm LINUXtips-02
LINUXtips-02

root@linuxtips-01:~#
```

## 13.2. O sensacional *services*!

Uma das melhores coisas que o Docker Swarm nos oferece é justamente a
possibilidade de fazer o uso dos *services*.

O *services* nada mais é do que um VIP ou DNS que realizará o
balanceamento de requisições entre os *containers*. Podemos estabelecer
um número x de *containers* respondendo por um *service* e esses
*containers* estarão espalhados pelo nosso *cluster*, entre nossos
*nodes*, garantindo alta disponibilidade e balanceamento de carga, tudo
isso nativamente!

O *services* é uma forma, já utilizada no Kubernetes, de você conseguir
gerenciar melhor seus *containers*, focando no serviço que esses
*containers* estão oferecendo e garantindo alta disponibilidade e
balanceamento de carga. É uma maneira muito simples e efetiva para
escalar seu ambiente, aumentando ou diminuindo a quantidade de
*containers* que responderá para um determinado *service*.

Meio confuso? Sim eu sei, mas vai ficar fácil. :)

Imagine que precisamos disponibilizar o serviço do Nginx para ser o novo
*web server*. Antes de criar esse *service*, precisamos de algumas
informações:

-   Nome do *service* que desejo criar 
    >**webserver**.

-   Quantidade de *containers* que desejo debaixo do *service* 
    > **5**.

-   Portas que iremos "bindar", entre o *service* e o *node* 
    > **8080:80**.

-   Imagem dos *containers* que irei utilizar 
    > **nginx**.

Agora que já temos essas informações, 'bora criar o nosso primeiro
service. :)

```bash
root@linuxtips-01:~# docker service create --name webserver --replicas 5 -p 8080:80 nginx
0azz4psgfpkf0i5i3mbfdiptk

root@linuxtips-01:~#
```

Agora já temos o nosso *service* criado. Para testá-lo, basta executar:

```bash
root@linuxtips-01:~# curl QUALQUER_IP_NODES_CLUSTER:8080
```

O resultado do comando anterior lhe trará a página de boas-vindas do
Nginx.

Como estamos utilizando o *services*, cada conexão cairá em um
*container* diferente, fazendo assim o balanceamento de cargas
"automagicamente"!

Para visualizar o *service* criado, execute:

```bash
root@linuxtips-01:~# docker service ls
ID       NAME      MODE           REPLICAS   IMAGE         PORTS
0azz4p   webserver replicated     5/5        nginx:lates   *:8080->80/tcp
```

Conforme podemos notar, temos o *service* criado e com ele cinco
réplicas em execução, ou seja, cinco *containers* em execução.

Se quisermos saber onde estão rodando nossos *containers*, em quais
*nodes* eles estão sendo executados, basta digitar o seguinte comando:

```bash
root@linuxtips-01:~# docker service ps webserver
ID       NAME          IMAGE          NODE           DESIRED STATE  CURRENT STATE              ERROR     PORTS
zbt1j    webserver.1   nginx:latest   LINUXtips-01   Running        Running 8 minutes ago
iqm9p    webserver.2   nginx:latest   LINUXtips-02   Running        Running 8 minutes ago
jliht    webserver.3   nginx:latest   LINUXtips-01   Running        Running 8 minutes ago
qcfth    webserver.4   nginx:latest   LINUXtips-03   Running        Running 8 minutes ago
e17um    webserver.5   nginx:latest   LINUXtips-02   Running        Running 8 minutes ago

root@linuxtips-01:~#
```

Assim conseguimos saber onde está rodando cada *container* e ainda o seu
*status*.

Se eu preciso saber maiores detalhes sobre o meu *service*, basta
utilizar o subcomando "inspect".

```bash
root@linuxtips-01:~# docker service inspect webserver
[
    {
        "ID": "0azz4psgfpkf0i5i3mbfdiptk",
        "Version": {
            "Index": 29
        },
        "CreatedAt": "2017-06-09T19:35:58.180235688Z",
        "UpdatedAt": "2017-06-09T19:35:58.18899891Z",
        "Spec": {
            "Name": "webserver",
            "Labels": {},
            "TaskTemplate": {
                "ContainerSpec": {
                    "Image": "nginx:latest@sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268",
                    "StopGracePeriod": 10000000000,
                    "DNSConfig": {}
                },
                "Resources": {
                    "Limits": {},
                    "Reservations": {}
                },
                "RestartPolicy": {
                    "Condition": "any",
                    "Delay": 5000000000,
                    "MaxAttempts": 0
                },
                "Placement": {},
                "ForceUpdate": 0
            },
            "Mode": {
                "Replicated": {
                    "Replicas": 5
                }
            },
            "UpdateConfig": {
                "Parallelism": 1,
                "FailureAction": "pause",
                "Monitor": 5000000000,
                "MaxFailureRatio": 0,
                "Order": "stop-first"
            },
            "RollbackConfig": {
                "Parallelism": 1,
                "FailureAction": "pause",
                "Monitor": 5000000000,
                "MaxFailureRatio": 0,
                "Order": "stop-first"
            },
            "EndpointSpec": {
                "Mode": "vip",
                "Ports": [
                    {
                        "Protocol": "tcp",
                        "TargetPort": 80,
                        "PublishedPort": 8080,
                        "PublishMode": "ingress"
                    }
                ]
            }
        },
        "Endpoint": {
            "Spec": {
                "Mode": "vip",
                "Ports": [
                    {
                        "Protocol": "tcp",
                        "TargetPort": 80,
                        "PublishedPort": 8080,
                        "PublishMode": "ingress"
                    }
                ]
            },
            "Ports": [
                {
                    "Protocol": "tcp",
                    "TargetPort": 80,
                    "PublishedPort": 8080,
                    "PublishMode": "ingress"
                }
            ],
            "VirtualIPs": [
                {
                    "NetworkID": "89t2aobeik8j7jcre8lxhj04l",
                    "Addr": "10.255.0.5/16"
                }
            ]
        }
    }
]

root@linuxtips-01:~#
```

Na saída do "inspect" conseguiremos pegar informações importantes sobre
nosso *service*, como portas expostas, volumes, *containers*,
limitações, entre outras coisas.

Uma informação muito importante é o endereço do VIP do *service*:

```json
    "VirtualIPs": [
        {
            "NetworkID": "89t2aobeik8j7jcre8lxhj04l",
            "Addr": "10.255.0.5/16"
        }
    ]
```

Esse é o endereço IP do "balanceador" desse *service*, ou seja, sempre
que acessarem via esse IP, ele distribuirá a conexão entre os
*containers*. Simples, não?

Agora, se quisermos aumentar o número de *containers* debaixo desse
*service*, é muito simples. Basta executar o comando a seguir:

```bash
root@linuxtips-01:~# docker service scale webserver=10
webserver scaled to 10

root@linuxtips-01:~#
```

Pronto, simples assim!

Agora já temos dez *containers* respondendo requisições debaixo do nosso
*service* *webserver*! Simples como voar!

Para visualizar, basta executar:

```bash
root@linuxtips-01:~# docker service ls
ID      NAME      MODE        REPLICAS   IMAGE           PORTS
0azz    webserver replicated  10/10      nginx:latest    *:8080->80/tcp

root@linuxtips-01:~#
```

Para saber em quais *nodes* eles estão em execução, lembre-se do "docker
service ls webserver".

Para acessar os *logs* desse *service*, basta digitar:

```bash
root@linuxtips-01:~# docker service logs -f webserver
webserver.5.e17umj6u6bix@LINUXtips-02 | 10.255.0.2 - - [09/Jun/2017:19:36:12 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.47.0" "-"
```

Assim, você terá acesso aos *logs* de todos os *containers* desse
*service*. Muito prático!

"Cansei de brincar! Quero remover esse meu *service*!" É tão simples
quanto criá-lo. Digite:

```bash
root@linuxtips-01:~# docker service rm webserver
webserver

root@linuxtips-01:~#
```

Pronto! Seu *service* foi excluído e você pode conferir na saída do
comando a seguir:

```bash
root@linuxtips-01:~# docker service ls
ID      NAME      MODE        REPLICAS   IMAGE           PORTS

root@linuxtips-01:~#
```

Criar um *service* com um volume conectado é bastante simples. Faça:

```bash
root@linuxtips-01:~# docker service create --name webserver --replicas 5 -p 8080:80 --mount type=volume,src=teste,dst=/app nginx
yfheu3k7b8u4d92jemglnteqa

root@linuxtips-01:~#
```

Quando eu crio um *service* com um volume conectado a ele, isso indica
que esse volume estará disponível em todos os meus *containers* desse
*service*, ou seja, o volume com o nome de "teste" estará montado em
todos os *containers* no diretório "/app"*.*
