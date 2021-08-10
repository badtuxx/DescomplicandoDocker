---
title: Capítulo 07
---

# 7. Entendendo volumes

## 7.1. Introdução a volumes no Docker

Bom, volumes nada mais são que diretórios externos ao *container*, que
são montados diretamente nele, e dessa forma *bypassam* seu
*filesystem*, ou seja, não seguem aquele padrão de camadas que falamos.
Decepcionei você? Que bom, sinal de que é bem simples e você não vai ter
problemas para entender. :)

A principal função do volume é persistir os dados. Diferentemente do
*filesystem* do *container,* que é volátil e toda informação escrita
nele é perdida quando o *container* morre, quando você escreve em um
volume aquele dado continua lá, independentemente do estado do
*container*.

Existem algumas particularidades entre os volumes e *containers* que
valem a pena ser mencionadas:

-   O volume é inicializado quando o *container* é criado.

-   Caso ocorra de já haver dados no diretório em que você está montando como volume, ou seja, se o diretório já existe e está "populado" na imagem base, aqueles dados serão copiados para o volume.

-   Um volume pode ser reusado e compartilhado entre *containers*.

-   Alterações em um volume são feitas diretamente no volume.

-   Alterações em um volume não irão com a imagem quando você fizer uma cópia ou *snapshot* de um *container*.

-   Volumes continuam a existir mesmo se você deletar o *container*.

Dito isso, chega de papo. Vamos aprender a adicionar um volume em um
*container*.

Primeiro, vamos ver como funciona da maneira antiga, que ainda é
suportada, porém não é elegante. :)

Essa maneira é muito utilizada quando se quer montar um diretório
específico do *host* dentro do *container*. Isso é ruim quando estamos
trabalhando em *cluster*, uma vez que teríamos que garantir esse
diretório criado em todos os *hosts* do *cluster*. Não seria legal.

Porém, podemos aprender como funciona e utilizar em algum momento, caso
se faça necessário. Para evitar erros, primeiro crie o diretório
"/volume" na sua máquina.

```bash
root@linuxtips:~# mkdir /volume
root@linuxtips:~# docker container run -ti --mount type=bind,src=/volume,dst=/volume ubuntu
root@7db02e999bf2:/# df -h

Filesystem                  Size  Used Avail Use%   Mounted on
none                         13G  6.8G 5.3G   57%   /
tmpfs                       999M     0 999M    0%   /dev
tmpfs                       999M     0 999M    0%   /sys/fs/cgroup
/dev/mapper/ubuntu--vg-root  13G  6.8G 5.3G   57%   /volume 
shm                          64M     0  64M    0%   /dev/shm

root@7db02e999bf2:/# ls
bin boot dev etc home lib lib64 media mnt opt proc root run sbin srv sys tmp usr var volume

root@7db02e999bf2:/#
```

No exemplo anterior, conhecemos um novo parâmetro do comando "docker
container run", o "\--mount".

O parâmetro "\--mount" é o responsável por indicar o volume, que em
nosso exemplo é o "/volume", e onde ele será montado no *container*.
Perceba que, quando passamos o parâmetro "\--mount
type=bind,src=/volume,dst=/volume", o Docker montou esse diretório no
*container*, porém sem nenhum conteúdo.

Podemos também montar um volume no *container linkando-o* com um
diretório do *host* já com algum conteúdo. Para exemplificar, vamos
compartilhar o diretório "/root/primeiro\_container", que utilizamos
para guardar o nosso primeiro *dockerfile*, e montá-lo no *container* em
um volume chamado "/volume" da seguinte forma:

```bash
# docker container run -ti --mount type=bind,src=/root/primeiro_container,dst=/volume ubuntu

root@3d372a410ea2:/# df -h
Filesystem                   Size Used Avail  Use%  Mounted on
none                          13G 6.8G  5.3G   57%  /
tmpfs                        999M    0  999M    0%  /dev
tmpfs                        999M    0  999M    0%  /sys/fs/cgroup
/dev/mapper/ubuntu--vg-root   13G 6.8G  5.3G   57%  /volume
shm                           64M    0   64M    0%  /dev/shm

root@3d372a410ea2:/#
```

Com isso, estamos montando o diretório "/root/primeiro\_dockerfile" do
*host* dentro do *container* com o nome de "/volume".

No *container*:

```bash
root@3d372a410ea2:/# ls /volume/
Dockerfile

root@3d372a410ea2:/#
```

No *host*:

```bash
root@linuxtips:~# ls /root/primeiro_dockerfile/
Dockerfile

root@linuxtips:~#
```

Caso eu queira deixar o volume no *container* apenas como *read-only,* é
possível. Basta passar o parâmetro "ro" após o destino onde será montado
o volume:

```bash
# docker container run -ti --mount type=bind,src=/root/primeiro_container,dst=/volume,ro ubuntu
root@8d7863b1d9af:/# df -h

Filesystem                   Size   Used  Avail  Use%  Mounted on
none                          13G   6.8G   5.3G   57%  /
tmpfs                        999M      0   999M    0%  /dev
tmpfs                        999M      0   999M    0%  /sys/fs/cgroup
/dev/mapper/ubuntu--vg-root   13G   6.8G   5.3G   57%  /volume
shm                           64M      0    64M    0%  /dev/shm

root@8d7863b1d9af:/# cd /volume/
root@8d7863b1d9af:/volume# ls
Dockerfile

root@8d7863b1d9af:/volume# mkdir teste
mkdir: cannot create directory 'teste': Read-only file system

root@8d7863b1d9af:/volume#
```

Assim como é possível montar um diretório como volume, também é possível
montar um arquivo:

```bash
# docker container run -ti --mount type=bind,src=/root/primeiro_container/Dockerfile,dst=/Dockerfile ubuntu

root@df0e3e58280a:/# df -h

Filesystem                   Size   Used  Avail  Use%  Mounted on
none                          13G   6.8G   5.3G   57%  /
tmpfs                        999M      0   999M    0%  /dev
tmpfs                        999M      0   999M    0%  /sys/fs/cgroup
/dev/mapper/ubuntu--vg-root   13G   6.8G   5.3G   57%  /Dockerfile
shm                           64M      0    64M    0%  /dev/shm

root@df0e3e58280a:/# cat Dockerfile
FROM debian
RUN /bin/echo "HELLO DOCKER"

root@df0e3e58280a:/#
```

Isso faz com que o arquivo "/root/primeiro\_dockerfile/Dockerfile" seja
montado em "/Dockerfile" no *container*.

## 7.2. Criando volumes

Agora vamos criar os volumes da maneira mais elegante e atual. Hoje
temos a possibilidade de realizar o gerenciamento de volumes de maneira
muito simples e inteligente.

Sempre que criamos um volume, ele cria um diretório com o mesmo nome
dentro de "/var/lib/docker/volumes/".

No exemplo a seguir, o volume "giropops" seria então criado em
"/var/lib/docker/volumes/giropops"; com isso, todos os arquivos
disponíveis nesse diretório também estariam disponíveis no local
indicado no *container*. Vamos aos exemplos! :D

É possível fazer a criação de volumes e toda a sua administração através
do comando:

```bash
# docker volume create giropops
```

É possível removê-lo através do comando:

```bash
# docker volume rm giropops
```

Para verificar detalhes sobre esse volume:

```bash
# docker volume inspect giropops
```

Para remover os volumes que não estão sendo utilizados (use com extrema
moderação! :D):

```bash
# docker volume prune
```

Para que você possa montar o volume criado em algum
*container*/*service*, basta executar o seguinte comando:

```bash
# docker container run -d --mount type=volume,source=giropops,destination=/var/opa nginx
```

Onde:

-   **\--mount** -- Comando utilizado para montar volumes.

-   **type=volume** -- Indica que o tipo é "volume". Ainda existe o tipo "bind", onde, em vez de indicar um volume, você indicaria um diretório como *source*.

-   **source=giropops** -- Qual o volume que pretendo montar.

-   **destination=/var/opa** -- Onde no *container* montarei esse volume.

Simples como voar, não?

## 7.3. Localizando volumes

Caso você queira obter a localização do seu volume, é simples. Mas para
isso você precisa conhecer o comando "docker volume inspect".

Com o "docker volume inspect" você consegue obter detalhes do seu
*container*, como, por exemplo, detalhes do volume.

A saída do comando "docker volume inspect" retorna mais informação do
que somente o *path* do diretório no *host*. Vamos usar a opção
"\--format" ou "-f" para filtrar a saída do "inspect".

```bash
docker volume inspect --format {% raw %}'{{ .Mountpoint }}'{% endraw %} giropops
/var/lib/docker/volumes/giropopos/_data
```

## 7.3. Criando e montando um *data-only container*

Uma opção bastante interessante em relação aos volumes diz respeito ao
*data-only container*, cuja principal função é prover volumes para
outros *containers*. Lembra do NFS *server* e do Samba? Ambos
centralizavam diretórios com a finalidade de compartilhar entre outros
servidores. Pois bem, o *data-only container* tem a mesma finalidade:
prover volumes a outros *containers*.

Um dos grandes baratos do Docker é a portabilidade. Um *container*
criado no seu *laptop* deve ser portátil a ponto de rodar em qualquer
outro ambiente que utilize o Docker, em todos os cantos do universo!

Sendo assim, o que acontece se eu criar um ambiente em Docker que diz
para os *containers* montarem um diretório do *host* local? Depende.
Depende de como está esse tal *host* local. Perguntas como "o diretório
existe?" "as permissões estão ajustadas?", entre outras mais, definirão
o sucesso na execução dos *containers*, o que foge completamente do
escopo do Docker.

Vamos ver como funciona isso na prática! :)

Para o nosso exemplo, primeiro vamos criar um *container* chamado
"dbdados", com um volume chamado "/data", que guardará os dados de um
banco PostgreSQL.

Para que possamos criar um *container* especificando um nome para ele,
utilizamos o parâmetro "\--name", conforme veremos no exemplo a seguir:

```bash
# docker container create -v /data --name dbdados centos
```

Com isso, apenas criamos o *container* e especificamos um volume para
ele, mas ainda não o iniciamos.

Sabemos que no *container* o volume se encontra montado em "/data".
Porém, qual a localização desse volume no *host*?

Lembra do "docker inspect"? Vamos utilizá-lo novamente:

```bash
root@linuxtips:~# docker inspect -f {% raw %}{{.Mounts}}{% endraw %} dbdados

[{46255137fe3f6d5f593e9ba9aaaf570b2f8b5c870f587c2fb34f29b79f97c30c /var/lib/docker/volumes/46255137fe3f6d5f593e9ba9aaaf570b2f8b5c870f587c2fb34f29b79f97c30c/_data /data local true }]
```

Perceba que agora utilizamos o nome do *container* em vez do "CONTAINER
ID". Totalmente possível e muito mais intuitivo.

Quando executamos o "docker inspect", ele nos retornou o caminho do
nosso volume. Vamos ver se existe algum conteúdo dentro dele:

```bash
root@linuxtips:~# ls \
 /var/lib/docker/volumes/46255137fe3f6d5f593e9ba9aaaf570b2f8b5c870f587c2fb34f29b79f97c30c/_data
```

Como vimos, o diretório ainda não possui conteúdo.

Agora vamos criar os *containers* que rodarão o PostgreSQL utilizando o
volume "/data" do *container* "dbdados" para guardar os dados.

Para que possamos fazer o exemplo, precisamos conhecer mais dois
parâmetros superimportantes:

-   **\--volumes-from** -- É utilizado quando queremos montar um volume disponibilizado por outro *container*.

-   **-e** -- É utilizado para informar variáveis de ambiente para o *container*. No exemplo, estamos passando as variáveis de ambiente do PostgreSQL.

Pronto, agora estamos preparados! Vamos criar os *containers* com o
PostgreSQL:

```bash
# docker run -d -p 5432:5432 --name pgsql1 --volumes-from dbdados \
    -e POSTGRESQL_USER=docker -e POSTGRESQL_PASS=docker \
    -e POSTGRESQL_DB=docker kamui/postgresql

# docker run -d -p 5433:5432 --name pgsql2 --volumes-from dbdados \
   -e POSTGRESQL_USER=docker -e POSTGRESQL_PASS=docker \
   -e POSTGRESQL_DB=docker kamui/postgresql
```

Para verificar os dois *containers* com o PostgreSQL em execução,
utilize o "docker container ls".

Pronto, agora temos os dois *containers* com PostgreSQL em execução!
Será que já temos algum dado no volume "/data" do *container* "dbdados"?

Vamos verificar novamente no *host* se o volume agora possui algum dado:

```bash
root@linuxtips:~# ls /var/lib/docker/volumes/46255137fe3f6d5f593e9ba9aaaf570b2f8b5c870f587c2fb34f29b79f97c30c/_data

base pg_clog pg_ident.conf pg_notify pg_snapshots pg_stat_tmp pg_tblspc PG_VERSION postgresql.conf postmaster.pid server.key global pg_hba.conf pg_multixact pg_serial pg_stat pg_subtrans pg_twophase pg_xlog postmaster.opts server.crt

root@linuxtips:~#
```

Sensacional! Como percebemos, os dois *containers* do PostgreSQL estão
escrevendo seus dados no volume "/data" do *container* "dbdados". Chega
a ser lacrimejante! :D

## 7.4. Sempre é bom um *backup*\...


Outra coisa bem bacana é a possibilidade de fazer *backups* dos seus
*containers* de dados de forma muito simples e rápida.

Digamos que você queira fazer o *backup* do diretório "/data" do
*container* "dbdados" que nós criamos há pouco; como faríamos?

```bash
root@linuxtips:~# cd backup/
root@linuxtips:~/backup# docker run -ti --volumes-from dbdados -v $(pwd):/backup debian tar -cvf /backup/backup.tar /data
```

Quando executamos o comando anterior, é criado um novo *container*
montando o(s) volume(s) do *container* "dbdados" (que no caso é o
"/data", lembra?). Além disso, será montado o diretório corrente do
*host* no volume "/backup" do *container*, e em seguida será executado o
comando do *tar* para empacotar o diretório "/data" dentro do diretório
"/backup". Bem simples, né?

```bash
root@linuxtips:~/backup# ls
backup.tar
root@linuxtips:~/backup#
```

Lembrando que os volumes são sempre criados dentro de
"/var/lib/docker/volumes". Caso queira fazer o *backup* de todos os
volumes, basta tratar esse diretório em suas rotinas de *backup*. ;)
