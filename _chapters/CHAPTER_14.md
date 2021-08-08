---
title: Capítulo 14
---

# 14. Docker Secrets

Ninguém tem dúvida de que a arquitetura de microsserviços já se provou
eficiente. Porém, implementar segurança, principalmente em um contexto
de infraestrutura imutável, tem sido um belo desafio.

Com questões que envolvem desde como separar a senha do *template* em
uma imagem até como trocar a senha de acesso a um serviço sem
interrompê-lo, *workarounds* não faltam. Mas como sempre dá para
melhorar, na versão 1.13 nossos queridos amigos do Docker lançaram o que
foi chamado de Docker Secrets.

O Docker Secrets é a solução do Docker para trabalhar com toda a parte
de *secrets* em um ambiente *multi-node* e *multi-container.* Em outras
palavras, um *"swarm mode" cluster*. A *secret* pode conter qualquer
coisa, porém deve ter um tamanho máximo de 500 KB. Por enquanto essa
belezinha não está disponível fora do contexto do Docker Swarm -- na
verdade, não é claro se vai algum dia ser diferente. Por enquanto somos
encorajados a usar um *service* para fazer *deploy* de *containers*
individuais.

## 14.1. O comando *docker secret*

O comando "docker secret" vem com alguns subcomandos. São eles:

```bash
docker secret --help

Usage: docker secret COMMAND

Manage Docker secrets

Options:
     --help Print usage

Commands:
    create   Create a secret from a file or STDIN as content
    inspect  Display detailed information on one or more secrets
    ls       List secrets
    rm       Remove one or more secrets

Run 'docker secret COMMAND --help' for more information on a command.
```

-   **create** -- Cria uma *secret* a partir do conteúdo de um arquivo ou STDIN.

-   **inspect** -- Mostra informações detalhadas de uma ou mais *secrets*.

-   **ls** -- Lista as *secrets* existentes.

-   **rm** -- Remove uma ou mais *secrets*.

**Create**

Como dito, o "create" aceita conteúdo tanto do STDIN\...

```bash
root@linuxtips:~# echo 'minha secret' | docker secret create minha_secret -
jxr0pilzhtqsiqi1f1fjmmg4t

root@linuxtips:~#
```

...quanto de um arquivo:

```bash
root@linuxtips:~# docker secret create minha_secret minha_secret.txt
ci7mse43i5ak378sg3qc4xt04

root@linuxtips:~#
```

**Inspect**

Fique tranquilo, o "inspect" não mostra o conteúdo da sua *secret*! :P

Em vez disso, ele mostra uma série de informações sobre a *secret*,
incluindo sua criação e modificação (apesar de não ter, na verdade, como
modificar uma *secret*; uma vez criada, ela não pode ser atualizada via
CLI, porém já há um *endpoint* na API do Docker Swarm para *update* de
*secret* -- "/secrets/{id}/update", vamos aguardar!)

```bash
root@linuxtips:~# docker secret inspect minha_secret
[
    {
        "ID": "ci7mse43i5ak378sg3qc4xt04",
        "Version": {
            "Index": 808
        },
        "CreatedAt": "2017-07-02T17:17:18.143116694Z",
        "UpdatedAt": "2017-07-02T17:17:18.143116694Z",
        "Spec": {
            "Name": "minha_secret",
            "Labels": {}
        }
    }
]

root@linuxtips:~#
```

O "inspect" aceita mais de uma *secret* por vez e mostrará o resultado
na mesma sequência.

**ls && rm**

Servem respectivamente para listar suas *secrets* e removê-las.

```bash
root@linuxtips:~# docker secret ls
ID                         NAME           CREATED             UPDATED
ci7mse43i5ak378sg3qc4xt04  minha_secret   About a minute ago  About a minute ago

root@linuxtips:~#
root@linuxtips:~# docker secret rm minha_secret
minha_secret

root@linuxtips:~#
```

## 14.2. Tudo bem, mas como uso isso?

As *secrets* são consumidas por serviços, como já citamos, e isso
acontece através de associação explícita, usando a *flag* "\--secret" na
hora de criar um serviço. Vamos para um exemplo.

Primeiro vamos criar uma *secret* com a senha do banco de dados da nossa
aplicação *fake.*

```bash
root@linuxtips:~# echo 'senha_do_banco' | docker secret create db_pass -
kxzgmhlu3ytv64hbqzg30nc8u

root@linuxtips:~#
```

Agora, vamos associá-la à nossa *app*, criando um serviço.

```bash
root@linuxtips:~# docker service create --name app --detach=false --secret db_pass minha_app:1.0
npfmry3vcol61cmcql3jiljk2
overall progress: 1 out of 1 tasks
1/1: running [==================================================>]
verify: Waiting 1 seconds to verify that tasks are stable...

root@linuxtips:~# docker service ls
ID            NAME      MODE        REPLICAS   IMAGE           PORTS
npfmry3vcol6  app       replicated  1/1        minha_app:1.0

root@linuxtips:~# docker container ls
CONTAINER ID     IMAGE          COMMAND                   CREATED           STATUS           PORTS     NAMES
65d1533f5b50     minha_app:1.0  "/bin/sh -c ./scri..."   40 seconds ago     Up 39 seconds              app.1.molbmj0649c7xkzfermkuwrx2

root@linuxtips:~#
```

Também é possível dar acesso a *keys* para serviços já criados, através
da *flag* "\--secret-add" do comando "docker service update", assim como
revogá-las, usando a *flag* "\--secret-rm" no mesmo comando. Ver o
tópico "Atualizando a *secret* de um serviço".

## 14.3. Acessando a *secret*

Com o serviço criado, a *secret* ficará disponível para todos os
*containers* daquele *service* e estará em arquivos dentro do diretório
"/run/secrets", montado em *tmpfs*. Se a sua *secret* chamar "db\_pass",
como no exemplo, o conteúdo dela estará em "/run/secrets/db\_pass".

É possível incluir alguns parâmetros na hora de adicionar uma *secret* a
um serviço, como, por exemplo, o *target*, que altera o nome do arquivo
no destino e até itens de segurança, como *uid*, *gid* e *mode*:

docker service create \--detach=false \--name app \--secret
source=db\_pass,target=password,uid=2000,gid=3000,mode=0400
minha\_app:1.0

Dentro do *container* ficaria assim:

```bash
root@4dd6b9cbff1a:/app# ls -lhart /run/secrets/
total 12K
-r-------- 1 2000 3000   15 Jul 2 17:44 password
drwxr-xr-x 7 root root 4.0K Jul 2 17:44 ..
drwxr-xr-x 2 root root 4.0K Jul 2 17:44 .

root@4dd6b9cbff1a:/app#
```

E aí basta que a sua aplicação leia o conteúdo do arquivo.

```bash
root@8b16b5335334:/app# python
Python 2.7.12 (default, Nov 19 2016, 06:48:10)
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>>
>>> secret = open('/run/secrets/password').read()
>>>
>>> print "minha secret e: %s" % (secret)
minha secret e: nova_senha
>>>
```

## 14.4. Atualizando a *secret* de um serviço

*Secrets* foram criadas para serem imutáveis, ou seja, caso você queira
trocar a *secret* de um serviço, você precisará criar outra *secret*. A
troca da *secret* é bem fácil. Por exemplo, para trocar a *secret*
"db\_pass" do exemplo anterior, teríamos que fazer o seguinte:

Criar uma nova *secret*:

```bash
root@linuxtips:~# echo 'nova_senha' | docker secret create db_pass_1 -
221uzilbsl1ybna7g7014hoqr

root@linuxtips:~#
```

Adicionar à *secret* a *app* criada anteriormente:

```bash
root@linuxtips:~# docker service update --secret-rm db_pass --detach=false --secret-add source=db_pass_1,target=password app
app
overall progress: 1 out of 1 tasks
1/1: running [==================================================>]
verify: Waiting 1 seconds to verify that tasks are stable...

root@linuxtips:~#
```

Após a atualização, basta remover a *secret* antiga:

```bash
root@linuxtips:~# docker secret rm db_pass
db_pass

root@linuxtips:~#
```
