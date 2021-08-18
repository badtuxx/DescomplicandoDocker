---
title: Capítulo 08
---

# 8. Criando e gerenciando imagens

## 8.1. Agora eu quero criar minha imagem, posso?

Claro que pode!

E mais, vamos aprender de duas formas simples e intuitivas.

Uma das coisas mais interessantes do Docker é a possibilidade de usar
imagens criadas por outras pessoas ao redor do mundo através de algum
*registry* como o Docker Hub. Isso agiliza muito a sua vida, ainda mais
quando você precisa apenas testar uma determinada tecnologia. O POC
(*Proof of Concept* -- em português, prova de conceito) se torna muito
mais ágil, fazendo com que você consiga testar diversas ferramentas no
mesmo tempo em que levaria para testar somente uma sem o Docker.

Entretanto, em determinados momentos precisamos criar a nossa própria
imagem do zero, ou então modificar uma imagem criada por terceiros e
salvar essas alterações em uma nova imagem.

Agora vamos ver os dois casos: como montar uma distribuição praticamente
do zero utilizando somente instruções através do *dockerfile* e outra
realizando modificações em uma imagem já existente e salvando em uma
imagem nova.

## 8.2. Vamos começar do começo então, *dockerfile*!

Vamos montar a nossa primeira imagem utilizando como roteiro de criação
um *dockerfile*. Você verá o quanto é simples a criação de um
*dockerfile* bem completo e prático. :)

Para começar, vamos criar um diretório chamado "/root/Dockerfiles".

```bash
# mkdir /root/Dockerfiles
```

Agora começaremos a criação do nosso *dockerfile*, nosso mapa de criação
da imagem. Para que possamos organizá-lo melhor, vamos criar um
diretório chamado "apache", onde guardaremos esse nosso primeiro
exemplo:

```bash
# cd /root/Dockerfiles/
# mkdir apache
```

Por enquanto, vamos apenas criar um arquivo chamado "Dockerfile" e
adicionar o conteúdo conforme exemplo a seguir:

```bash
# cd apache
# vim Dockerfile
```

```Dockerfile
FROM debian

RUN apt-get update && apt-get install -y apache2 && apt-get clean

ENV APACHE_LOCK_DIR="/var/lock"

ENV APACHE_PID_FILE="/var/run/apache2.pid"

ENV APACHE_RUN_USER="www-data"

ENV APACHE_RUN_GROUP="www-data"

ENV APACHE_LOG_DIR="/var/log/apache2"

LABEL description="Webserver"

VOLUME /var/www/html/

EXPOSE 80
```

Muito bom! Agora que você já adicionou as informações conforme o
exemplo, vamos entender cada seção utilizada nesse nosso primeiro
*dockerfile*:

-   **FROM** -- Indica a imagem a servir como base.

-   **RUN** -- Lista de comandos que deseja executar na criação da imagem.

-   **ENV** -- Define variáveis de ambiente.

-   **LABEL** -- Adiciona *metadata* à imagem, como descrição, versão, etc.

-   **VOLUME** -- Define um volume a ser montado no *container.*

Após a criação do arquivo, vamos *buildar* (construir a nossa imagem) da
seguinte forma:

```bash
# docker build .
```

Lembre-se: você deverá estar no diretório onde está o seu *dockerfile*.

Todos os passos que definimos em nosso *dockerfile* serão realizados,
como a instalação dos pacotes solicitados e todas as demais tarefas.

Successfully built 53de2cee9e71

Muito bem! Como podemos notar na última linha da saída do "docker
build", a imagem foi criada com sucesso! :D

Vamos executar o "docker image ls" para ver se está tudo certo com a
nossa primeira imagem!

```bash
root@linuxtips:~/Dockerfile/apache# docker image ls
REPOSITORY       TAG     IMAGE ID      CREATED         SIZE
<none>           <none>  53de2cee9e71  2 minutes ago   193.4 MB
```

A nossa imagem foi criada! Porém, temos um problema. :/

A imagem foi criada e está totalmente funcional, mas, quando a
*buildamos*, não passamos o parâmetro "-t", que é o responsável por
adicionar uma *tag* ("nome:versão") à imagem.

Vamos executar novamente o *build*, porém passando o parâmetro '-t',
conforme o exemplo a seguir:

```bash
# docker build -t linuxtips/apache:1.0 .
```

Agora vamos ver se realmente a imagem foi criada, adicionando um nome e
uma versão a ela:

```bash
root@linuxtips:~/Dockerfile/apache# docker image ls
REPOSITORY         TAG   IMAGE ID      CREATED        SIZE
linuxtips/apache   1.0   53de2cee9e71  5 minutes ago  193.4 MB
```

Maravilha! Funcionou conforme esperávamos!

Vamos executar um *container* utilizando nossa imagem como base:

```bash
# docker container run -ti linuxtips/apache:1.0
```

Agora já estamos no *container*. Vamos verificar se o Apache2 está em
execução. Se ainda não estiver, vamos iniciá-lo e verificar se a porta
80 está "LISTEN".

```bash
root@70dd36fe2d3b:/# ps -ef
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  1 21:33 ?        00:00:00 /bin/bash
root           6       1  0 21:33 ?        00:00:00 ps -ef

root@70dd36fe2d3b:/# /etc/init.d/apache2 start
[....] Starting Apache httpd web server: apache2AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.17.0.4. Set the 'ServerName' directive globally to suppress this message
. ok 

root@70dd36fe2d3b:/# ps -ef
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 21:33 ?        00:00:00 /bin/bash
root          30       1  0 21:33 ?        00:00:00 /usr/sbin/apache2 -k start
www-data      33      30  0 21:33 ?        00:00:00 /usr/sbin/apache2 -k start
www-data      34      30  0 21:33 ?        00:00:00 /usr/sbin/apache2 -k start
root         109       1  0 21:33 ?        00:00:00 ps -ef

root@70dd36fe2d3b:/# ss -atn
State      Recv-Q Send-Q     Local Address:Port    Peer Address:Port
LISTEN          0    128            :::80                :::*

root@70dd36fe2d3b:/# ip addr show eth0
50: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
        link/ether 02:42:ac:11:00:04 brd ff:ff:ff:ff:ff:ff
        inet 172.17.0.4/16 scope global eth0
             valid_lft forever preferred_lft forever
        inet6 fe80::42:acff:fe11:4/64 scope link
             valid_lft forever preferred_lft forever

root@70dd36fe2d3b:/#
```

No código anterior é possível observar o IP do *container* na saída do
"ip addr". Vamos testar a comunicação com o *container* a partir do
*host*.

No *host*, digite:

```bash
# curl <IP DO CONTAINER>
```

O "curl" retornou a página de boas-vindas do Apache2, ou seja, tudo está
funcionando muito bem e o Apache2, respondendo conforme esperado!

Maaaaaasssss, não é interessante que eu tenha que entrar no *container*
para subir o meu processo do Apache. Todo *container* deve executar seu
processo em primeiro plano e esse processo deve subir de forma
automática e não com um *serumaninho* acessando o *container* e subindo
o serviço. Vimos antes somente como primeiro exemplo, agora vamos
aperfeiçoá-lo e deixá-lo como deve estar! :D

A primeira coisa é deixar o nosso *dockerfile* como segue:

```bash
# vim Dockerfile
```

```Dockerfile
FROM debian

RUN apt-get update && apt-get install -y apache2 && apt-get clean

ENV APACHE_LOCK_DIR="/var/lock"

ENV APACHE_PID_FILE="/var/run/apache2/apache2.pid"

ENV APACHE_RUN_USER="www-data"

ENV APACHE_RUN_DIR="/var/run/apache2"

ENV APACHE_RUN_GROUP="www-data"

ENV APACHE_LOG_DIR="/var/log/apache2"

LABEL description="Webserver"

VOLUME /var/www/html/

EXPOSE 80

ENTRYPOINT ["/usr/sbin/apachectl"]

CMD ["-D", "FOREGROUND"]
```

Perceba que agora nós adicionamos mais duas opções: o ENTRYPOINT e o
CMD!

Ficou curioso sobre o que eles fazem? Então 'bora aprender muito mais
opções possíveis de serem adicionadas em um *dockerfile*!

## 8.3. Bora aprender um pouco mais sobre *dockerfile*?

Vamos agora aprender um pouco mais sobre as opções que podemos utilizar
quando estamos criando um *dockerfile*:

-   **ADD** -- Copia novos arquivos, diretórios, arquivos TAR ou arquivos remotos e os adiciona ao *filesystem* do *container*.

-   **CMD** -- Executa um comando. Diferentemente do RUN, que executa o comando no momento em que está "buildando" a imagem, o CMD irá fazê-lo somente quando o container é iniciado.

-   **LABEL** -- Adiciona metadados à imagem, como versão, descrição e fabricante.

-   **COPY** -- Copia novos arquivos e diretórios e os adiciona ao *filesystem* do *container*.

-   **ENTRYPOINT** -- Permite que você configure um *container* para rodar um executável. Quando esse executável for finalizado, o *container* também será.

-   **ENV** -- Informa variáveis de ambiente ao *container*.

-   **EXPOSE** -- Informa qual porta o *container* estará ouvindo.

-   **FROM** -- Indica qual imagem será utilizada como base. Ela precisa ser a primeira linha do *dockerfile*.

-   **MAINTAINER** -- Autor da imagem.

-   **RUN** -- Executa qualquer comando em uma nova camada no topo da imagem e "commita" as alterações. Essas alterações você poderá utilizar nas próximas instruções de seu *dockerfile*.

-   **USER** -- Determina qual usuário será utilizado na imagem. Por *default* é o *root*.

-   **VOLUME** -- Permite a criação de um ponto de montagem no *container*.

-   **WORKDIR** -- Responsável por mudar do diretório "/" (raiz) para o especificado nele.

Um detalhe superimportante de mencionar é que quando estamos trabalhando
com o ENTRYPOINT e o CMD dentro do mesmo *dockerfile*, o CMD somente
aceita parâmetros do ENTRYPOINT, conforme nosso exemplo do *dockerfile*
anterior:

```Dockerfile
ENTRYPOINT ["/usr/sbin/apachectl"]

CMD ["-D", "FOREGROUND"]
```

Onde:

-   **"/usr/sbin/apachectl"** -- Esse é o comando.

-   **"-D", "FOREGROUND"** -- Esse é o argumento, o parâmetro.

No *shell*, por exemplo, a execução ficaria assim:

```bash
# /usr/sbin/apachectl -D FOREGROUND
```

Ou seja, assim você está iniciando o Apache passando a instrução para
que ele seja iniciado em primeiro plano, como deve ser. :D

Para maiores detalhes sobre como criar imagens, veja essa apresentação
criada pelo Jeferson:
[https://www.slideshare.net/jfnredes/images-deep-dive](https://www.slideshare.net/jfnredes/images-deep-dive).

## 8.4. *Multi-stage*

Um importante e recente recurso adicionado ao *dockerfile* visa
facilitar a vida de quem pretende criar imagens de *containers* de forma
efetiva. Esse cara é o *multi-stage*!

O *multi-stage* nada mais é do que a possibilidade de você criar uma
espécie de *pipeline* em nosso *dockerfile*, podendo inclusive ter duas
entradas FROM.

Esse recurso é muito utilizado quando queremos, por exemplo, compilar a
nossa aplicação em um *container* e executá-la, porém não queremos ter
aquela quantidade enorme de pacotes instalados em nossos *containers*
necessários sempre quando se quer compilar códigos de alguma linguagem,
como C, Java ou Golang.

Vamos a um exemplo para que possamos entender melhor como isso funciona!

Para isso, preparei uma *app* escrita em Golang superavançada para o
nosso teste:

```bash
# vim goapp.go
```

```golang
package main

import "fmt"

func main() {

    fmt.Println("GIROPOPS STRIGUS GIRUS - LINUXTIPS")

}
```

Achou que seria algo avançado? Impossível, fomos nós que fizemos. :D

Bem, agora vamos criar um *dockerfile* para criar a nossa imagem e assim
executar a nossa *app*.

```bash
# vim Dockerfile
```

```Dockerfile
FROM golang

WORKDIR /app

ADD . /app

RUN go mod init goapp && go build -o goapp

ENTRYPOINT ./goapp
```

Pronto! Agora vamos realizar o *build*.

```bash
# docker build -t goapp:1.0 .
```

Listando a nossa imagem:

```bash
# docker image ls | grep goapp
goapp    1.0    50451808b384    11 seconds ago      781MB
```

Agora vamos executá-la e ver a nossa fantástica *app* em execução:

```bash
# docker container run -ti goapp:1.0
GIROPOPS STRIGUS GIRUS -- LINUXTIPS
```

Pronto! Nossa *app* e nossa imagem estão funcionando! Sucesso!

Porém, podemos melhorar muita coisa se começarmos a utilizar o nosso
poderoso recurso, o *multi-stage*!

Vamos refazer o nosso *dockerfile* utilizando o *multi-stage*, entender
como ele funciona e a diferença entre as duas imagens.

Vamos deixar nosso *dockerfile* dessa maneira:

```bash
# vim Dockerfile
```

```Dockerfile
FROM golang AS buildando

ADD . /src

WORKDIR /src

RUN go build -o goapp

FROM alpine:3.1

WORKDIR /app

COPY --from=buildando /src/goapp /app

ENTRYPOINT ./goapp
```

Perceba que agora nós temos duas entradas FROM, o que não era possível
antes do *multi-stage*. Mas por que isso?

O que está acontecendo é que agora temos o *dockerfile* dividido em duas
seções. Cada entrada FROM define o início de um bloco, uma etapa.

Então, em nosso primeiro bloco temos:

-   **FROM golang AS buildando** -- Estamos utilizando a imagem do Golang para criação da imagem de *container*, e aqui estamos apelidando esse bloco como "buildando".

-   **ADD . /src** -- Adicionando o código de nossa *app* dentro do *container* no diretório "/src".

-   **WORKDIR /src** -- Definindo que o diretório de trabalho é o "/src", ou seja, quando o *container* iniciar, estaremos nesse diretório.

-   **RUN go build -o goapp** -- Vamos executar o *build* de nossa *app* Golang.

Já no segundo bloco temos o seguinte:

-   **FROM alpine:3.1** -- Iniciando o segundo bloco e utilizando a imagem do Alpine para criação da imagem de *container*.

-   **WORKDIR /app** -- Definindo que o diretório de trabalho é o "/app", ou seja, quando o *container* iniciar, estaremos nesse diretório.

-   **COPY \--from=buildando /src/goapp /app** -- Aqui está a mágica: vamos copiar do bloco chamado "buildando" um arquivo dentro de "/src/goapp" para o diretório "/app" do *container* que estamos tratando nesse bloco, ou seja, copiamos o binário que foi compilado no bloco anterior e o trouxemos para esse.

-   **ENTRYPOINT ./goapp** -- Aqui vamos executar a nossa sensacional *app*. :)

Agora que já entendemos todas as linhas do nosso novo *dockerfile*,
'bora realizar o *build* dele.

```bash
# docker build -t goapp_multistage:1.0 .
```

Vamos executar a nossa imagem para ver se está tudo funcionando:

```bash
# docker container run -ti goapp_multistage:1.0
GIROPOPS STRIGUS GIRUS - LINUXTIPS
```

Será que existe diferença de tamanho entre elas? Vamos conferir:

```bash
# docker image ls | grep goapp
goapp_multistage  1.0    dfe57485b7f0    22 seconds ago    7.07MB
goapp             1.0    50451808b384    15 minutes ago     781MB
```

A diferença de tamanho é brutal, pois em nossa primeira imagem
precisamos ter um monte de pacotes para que o *build* da *app* Golang
ocorra. Já em nossa segunda imagem também utilizamos a imagem do Golang
e todos os seus pacotes para *buildar* a nossa *app*, porém descartamos
a primeira imagem e somente copiamos o binário para o segundo bloco,
onde estamos utilizando a imagem do Alpine, que é superenxuta.

Ou seja, utilizamos o primeiro bloco para compilar a nossa *app* e o
segundo bloco somente para executá-la. Simples assim, simples como voar!
:D

## 8.5. Vamos customizar uma imagem base agora?

Vamos agora criar uma nova imagem, porém sem utilizar o *dockerfile*.
Vamos executar um *container* com uma imagem base, realizar as
modificações que desejarmos e depois salvar esse *container* como uma
nova imagem!

Simples, rápido e fácil!

Bem, primeiro precisamos criar um *container*. Vamos dessa vez utilizar
um *container* Debian, somente para variar. :D

```bash
root@linuxtips:~# docker container run -ti debian:8 /bin/bash
root@0b7e6f606aae:/#
```

Agora vamos fazer as alterações que desejamos. Vamos fazer o mesmo que
fizemos quando montamos nossa primeira imagem com o *dockerfile*, ou
seja, fazer a instalação do Apache2. :D

```bash
root@0b7e6f606aae:/# apt-get update && apt-get install -y apache2 && apt-get clean
```

Agora que já instalamos o Apache2, vamos sair do *container* para que
possamos *commitar* a nossa imagem com base nesse *container* em
execução.

Lembre-se de que para sair do *container* e deixá-lo ainda em execução é
necessário pressionar **Ctrl + p + q**. ;)

```bash
# docker container ls
# docker commit -m "meu container" CONTAINERID
# docker image ls
REPOSITORY   TAG     IMAGE ID       CREATED         SIZE
<none>       <none>  fd131aedd43a   4 seconds ago   193.4 MB
```

Repare que nossa imagem ficou com o "\<none\>" em seu nome e "TAG". Para
que possamos ajustar e dar um nome e uma versão à nossa imagem, vamos
usar o comando "docker tag", conforme mostramos a seguir:

```bash
# docker tag IMAGEID linuxtips/apache_2:1.0
REPOSITORY          TAG   IMAGE ID       CREATED          SIZE
linuxtips/apache_2  1.0   fd131aedd43a   2 minutes ago    193.4 MB
```

Agora sim!!! Temos a nossa imagem criada e nome e versão especificados.

Vamos iniciar um *container* utilizando a imagem que acabamos de criar:

```bash
# docker container run -ti linuxtips/apache_2:1.0 /bin/bash
```

Vamos subir o Apache2 e testar a comunicação do *container*:

```bash
root@57094ec894ce:/# ps -ef
UID   PID PPID C  STIME TTY      TIME  CMD
root    1    0 0  21:48 ?    00:00:00  /bin/bash
root    6    1 0  21:48 ?    00:00:00  ps -ef

root@57094ec894ce:/# /etc/init.d/apache2 start
[....] Starting web server: apache2AH00558: apache2: Could notreliably determine the server's fully qualified domain name, using 172.17.0.6. Set the 'ServerName' directive globally to suppress this message
. ok

root@70dd36fe2d3b:/# ps -ef
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 21:43 ?        00:00:00 /bin/bash
root          30       1  0 21:44 ?        00:00:00 /usr/sbin/apache2 -k start
www-data      33      30  0 21:44 ?        00:00:00 /usr/sbin/apache2 -k start
www-data      34      30  0 21:44 ?        00:00:00 /usr/sbin/apache2 -k start
root         111       1  0 21:44 ?        00:00:00 ps -ef

root@70dd36fe2d3b:/# ss -atn
State     Recv-Q   Send-Q       Local Address:Port        Peer Address:Port
LISTEN         0      128             :::80                      :::*

root@57094ec894ce:/# ip addr show eth0
54: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
       link/ether 02:42:ac:11:00:06 brd ff:ff:ff:ff:ff:ff
       inet 172.17.0.6/16 scope global eth0
          valid_lft forever preferred_lft forever
       inet6 fe80::42:acff:fe11:6/64 scope link
          valid_lft forever preferred_lft forever
```

Boaaa! Agora já temos o Apache2 em execução. Vamos sair do *container* e
testar a comunicação com o Apache2 a partir do *host*:

```bash
# curl <Container IP>
```

Ele retornará a página de boas-vindas do Apache2! Tudo funcionando
conforme esperado!
