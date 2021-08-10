---
title: Capítulo 06
---

# 6. Meu primeiro e tosco *dockerfile*\...

Tudo que nós fizemos até agora foi escrever na linha de comando, o que é
OK para aprender. Porém, principalmente nos dias de hoje, não dá para
viver mais sem automatizar as coisas -- se você, assim como nós, adora
automatizar tudo que for possível, vai gostar bastante desse assunto.

O *dockerfile* nada mais é do que um arquivo onde você determina todos
os detalhes do seu *container*, como, por exemplo, a imagem que você vai
utilizar, aplicativos que necessitam ser instalados, comandos a serem
executados, os volumes que serão montados, etc., etc., etc.!

É um *makefile* para criação de *containers*, e nele você passa todas as
instruções para a criação do seu *container*.

Vamos ver como isso funciona na prática?

Primeira coisa: vamos criar um diretório onde deixaremos o nosso arquivo
*dockerfile*, somente para ficar organizado. :D

Depois basta criar o *dockerfile* conforme exemplo a seguir:

```bash
# mkdir /root/primeiro_dockerfile
# cd /root/primeiro_dockerfile
# vim Dockerfile
```

Vamos adicionar as instruções que queremos para essa imagem de
*container* que iremos criar:

```Dockerfile
FROM debian

RUN /bin/echo "HELLO DOCKER"
```

Apenas isso por enquanto. Salve e saia do *vim*.

Agora vamos rodar o comando "docker build" para fazer a criação dessa
imagem de *container* utilizando o *dockerfile* criado.

```bash
root@linuxtips:~/primeiro_dockerfile# docker build -t tosko:1.0 .
Sending build context to Docker daemon 2.048 kB
Step 1/2 : FROM debian
latest: Pulling from library/debian
fdd5d7827f33: Pull complete 
a3ed95caeb02: Pull complete
Digest: sha256:e7d38b3517548a1c71e41bffe9c8ae6d6d29546ce46bf62159837aad072c90aa
Status: Downloaded newer image for debian:latest
   ---> f50f9524513f
Step 2/2 : RUN /bin/echo "HELLO DOCKER"
   ---> Running in df60a0644bed 
HELLO DOCKER
   ---> fd3af97a8940
Removing intermediate container df60a0644bed
Successfully built fd3af97a8940
Successfully tagged tosko:1.0

root@linuxtips:~/primeiro_dockerfile#
```

Veja que usamos o diretório corrente, representado pelo caractere ".",
para indicar o *path* do meu arquivo *dockerfile*, mas você não precisa
necessariamente estar no mesmo diretório, basta passar o *path* do
diretório onde o arquivo se encontra.

**Lembre apenas que é o *path* do diretório e não do arquivo.**
