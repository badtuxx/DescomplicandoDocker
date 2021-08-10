---
title: Capítulo 15
---

# 15. Docker Compose

Bem, agora chegamos em uma das partes mais importantes do livro, o
sensacional e completo Docker Compose!

O Docker Compose nada mais é do que uma forma de você conseguir escrever
em um único arquivo todos os detalhes do ambiente de sua aplicação.
Antes nós usávamos o *dockerfile* apenas para criar imagens, seja da
minha aplicação, do meu BD ou do meu *webserver*, mas sempre de forma
unitária, pois tenho um *dockerfile* para cada "tipo" de *container*: um
para a minha *app*, outro para o meu BD e assim por diante.

Com o Docker Compose nós falamos sobre o ambiente inteiro. Por exemplo,
no Docker Compose nós definimos quais os *services* que desejamos criar
e quais as características de cada *service* (quantidade de *containers*
debaixo daquele *service*, volumes, *network*, *secrets*, etc.).

O padrão que os *compose files* seguem é o YML, supersimples e de fácil
entendimento, porém sempre é bom ficar ligado na sintaxe que o padrão
YML lhe impõe. ;)

Bem, vamos parar de falar e começar a brincadeira!

Antes a gente precisava instalar o Docker Compose para utilizá-lo.
Porém, hoje nós temos o subcomando "docker stack", já disponível junto à
instalação do Docker. Ele é responsável por realizar o *deploy* de
nossos *services* através do Docker Compose de maneira simples, rápida e
muito efetiva.

'Bora começar! A primeira coisa que devemos realizar é a própria criação
do *compose file*. Vamos começar por um mais simples e vamos aumentando
a complexidade conforme evoluímos.

Lembre-se: para que possamos seguir com os próximos exemplos, o seu
*cluster* *swarm* deverá estar funcionando perfeitamente. Portanto, se
ainda não estiver com o *swarm* ativo, execute:

```bash
# docker swarm init
```

Vamos criar um diretório chamado "Composes", somente para que possamos
organizar melhor nossos arquivos.

```bash
# mkdir /root/Composes
# mkdir /root/Composes/1
# cd /root/Composes/1
# vim docker-compose.yml
```

```yaml
version: "3"

services:
    web:
        image: nginx
        deploy:
        replicas: 5
        resources:
        limits:
            cpus: "0.1"
            memory: 50M
        restart_policy:
            condition: on-failure
        ports:
        - "8080:80"
        networks:
        - webserver

networks:
    webserver:
```

Pronto! Agora já temos o nosso primeiro *docker-compose*. O que
precisamos agora é realizar o *deploy*, porém antes vamos conhecer
algumas opções que utilizamos anteriormente:

-   **version: \"3\" --** Versão do *compose* que estamos utilizando.

-   **services:** -- Início da definição de meu serviço.

-   **web: *--*** Nome do serviço.

-   **image: nginx** -- Imagem que vamos utilizar.

-   **deploy: --** Início da estratégia de *deploy*.

-   **replicas: 5** -- Quantidade de réplicas.

-   **resources:** -- Início da estratégia de utilização de recursos.

-   **limits:** -- Limites.

-   **cpus: \"0.1\"** -- Limite de CPU.

-   **memory: 50M** -- Limite de memória.

-   **restart\_policy:** -- Políticas de *restart*.

-   **condition: on-failure** -- Somente irá "restartar" o *container* em caso de falha.

-   **ports:** -- Quais portas desejamos expor.

-   **- \"8080:80\"** -- Portas expostas e "bindadas".

-   **networks:** -- Definição das redes que irei utilizar nesse serviço.

-   ***-* webserver** -- Nome da rede desse serviço.

-   **networks:** -- Declarando as redes que usaremos nesse *docker-compose*.

-   **webserver:** -- Nome da rede a ser criada, caso não exista.

Simples como voar, não? :D

## 15.1. O comando *docker stack*

Agora precisamos realizar o *deploy* desse *service* através do *compose
file* que criamos. Para isso, vamos utilizar o sensacional "docker
stack":

```bash
root@linuxtips-01:~/Composes/1# docker stack deploy -c docker-compose.yml primeiro
Creating network primeiro_webserver
Creating service primeiro_web

root@linuxtips-01:~/Composes/1#
```

Simples assim, e nosso service já está disponível para uso. Agora vamos
verificar se realmente o *service* subiu e se está respondendo conforme
esperado:

```bash
root@linuxtips-01:~/Composes/1# curl 0:8080
<!DOCTYPE html>
<html>
    <head>
        <title>Welcome to nginx!</title>
        <style>
        body {
            width: 35em;
            margin: 0 auto;
            font-family: Tahoma, Verdana, Arial, sans-serif;
        }
        </style>
    </head>
    <body>
        <h1>Welcome to nginx!</h1>
        <p>If you see this page, the nginx web server is successfully installed and working. Further configuration is required.</p>
        <p>For online documentation and support please refer to
        <a href="http://nginx.org/">nginx.org</a>.<br/>
        Commercial support is available at
        <a href="http://nginx.com/">nginx.com</a>.</p>
        <p><em>Thank you for using nginx.</em></p>
    </body>
</html>

root@linuxtips-01:~/Composes/1#
```

Sensacional, o nosso *service* está em pé, pois recebemos a página de
boas-vindas do Nginx!

Vamos verificar se está tudo certo com o *service*:

```bash
root@linuxtips-01:~/Composes/1# docker service ls
ID     NAME          MODE        REPLICAS    IMAGE           PORTS
mw95t  primeiro_web  replicated  5/5         nginx:latest    *:8080->80/tcp

root@linuxtips-01:~/Composes/1# docker service ps primeiro_web
ID            NAME             IMAGE         NODE           DESIRED STATE   CURRENT STATE           ERROR   PORTS
lrcqo8ifultq  primeiro_web.1   nginx:latest  LINUXtips-02   Running         Running 2 minutes ago
ty16mkcqdwyl  primeiro_web.2   nginx:latest  LINUXtips-03   Running         Running 2 minutes ago
dv670shw22o2  primeiro_web.3   nginx:latest  LINUXtips-01   Running         Running 2 minutes ago
sp0k1tnjftnr  primeiro_web.4   nginx:latest  LINUXtips-01   Running         Running 2 minutes ago
4fpl35llq1ih  primeiro_web.5   nginx:latest  LINUXtips-03   Running         Running 2 minutes ago

root@linuxtips-01:~/Composes/1#
```

Para listar todos os *stacks* criados, basta executar:

```bash
root@linuxtips-01:~/Composes/1# docker stack ls
NAME        SERVICES
primeiro    1

root@linuxtips-01:~/Composes/1#
```

Perceba: a saída diz que possuímos somente um *stack* criado e esse
*stack* possui um *service*, que é exatamente o nosso do Nginx.

Para visualizar os *services* que existem em determinado *stack*,
execute:

```bash
root@linuxtips-01:~/Composes/1# docker stack services primeiro
ID            NAME          MODE         REPLICAS   IMAGE          PORTS
mx0p4vbrzfuj  primeiro_web  replicated   5/5        nginx:latest   *:8080->80/tcp

root@linuxtips-01:~/Composes/1#
```

Podemos verificar os detalhes do nosso *stack* criado através do comando
a seguir:

```bash
root@linuxtips-01:~/Composes/1# docker stack ps primeiro
ID             NAME             IMAGE          NODE           DESIRED STATE    CURRENT STATE            ERROR   PORTS
x3u03509w9u3   primeiro_web.1   nginx:latest   LINUXtips-03   Running          Running 5 seconds ago
3hpu5lo6yvld   primeiro_web.2   nginx:latest   LINUXtips-02   Running          Running 5 seconds ago
m82wbwuwoza0   primeiro_web.3   nginx:latest   LINUXtips-03   Running          Running 5 seconds ago
y7vizedqvust   primeiro_web.4   nginx:latest   LINUXtips-02   Running          Running 5 seconds ago
wk0acjnyl6jm   primeiro_web.5   nginx:latest   LINUXtips-01   Running          Running 5 seconds ago

root@linuxtips-01:~/Composes/1#
```

Maravilha! Nosso *service* está *UP* e tudo está em paz!

Em poucos minutos subimos o nosso *service* do Nginx em nosso *cluster*
utilizando o *docker-compose* e o "docker stack", simples como voar!

Agora vamos imaginar que eu queira remover esse meu *service*. Como eu
faço? Simples:

```bash
root@linuxtips-01:~/Composes/1# docker stack rm primeiro
Removing service primeiro_web
Removing network primeiro_webserver

root@linuxtips-01:~/Composes/1#
```

Para verificar se realmente removeu o *service*:

```bash
root@linuxtips-01:~/Composes/1# docker service ls
ID    NAME    MODE     REPLICAS     IMAGE     PORTS

root@linuxtips-01:~/Composes/1#
```

Pronto! Nosso *service* está removido!

Vamos aumentar um pouco a complexidade na criação de nosso
*docker-compose* nesse novo exemplo.

Vamos criar mais um diretório, onde criaremos o nosso novo *compose
file*:

```bash
root@linuxtips-01:~/Composes# mkdir 2
root@linuxtips-01:~/Composes# cd 2
root@linuxtips-01:~/Composes# vim docker-compose.yml
```

```yaml
version: '3'
    services:
        db:
            image: mysql:5.7
            volumes:
                - db_data:/var/lib/mysql
            environment:
                MYSQL_ROOT_PASSWORD: somewordpress
                MYSQL_DATABASE: wordpress
                MYSQL_USER: wordpress
                MYSQL_PASSWORD: wordpress

        wordpress:
            depends_on:
            - db
            image: wordpress:latest
            ports:
            - "8000:80"
            environment:
                WORDPRESS_DB_HOST: db:3306
                WORDPRESS_DB_USER: wordpress
                WORDPRESS_DB_PASSWORD: wordpress

volumes:
    db_data:
```

Perfeito!

Nesse exemplo estamos conhecendo mais algumas opções que podemos
utilizar no *docker-compose*. São eles:

-   **volumes:** -- Definição dos volumes utilizados pelo *service*.

-   **- db\_data:/var/lib/mysql** -- Volume e destino.

-   **environment:** -- Definição de variáveis de ambiente utilizados pelo *service.*

-   **MYSQL\_ROOT\_PASSWORD: somewordpress** -- Variável e valor.

-   **MYSQL\_DATABASE: wordpress** -- Variável e valor.

-   **MYSQL\_USER: wordpress** -- Variável e valor.

-   **MYSQL\_PASSWORD: wordpress** -- Variável e valor.

-   **depends\_on:** -- Indica que esse *service* depende de outro para subir.

-   **- db** -- Nome do service que necessário para sua execução.

Muito simples, não?!?

Agora vamos realizar o *deploy* desse exemplo. Como se pode perceber, o
nosso *stack* é composto por dois *services*, o Wordpress e o MySQL.

```bash
root@linuxtips-01:~/Composes/2# docker stack deploy -c docker-compose.yml segundo
Creating network segundo_default
Creating service segundo_db
Creating service segundo_wordpress

root@linuxtips-01:~/Composes/2#
```

Conforme esperado, ele realizou a criação dos dois *services* e da rede
do *stack*.

Para acessar o seu Wordpress, basta acessar em um navegador:

***http://SEU\_IP:8000***

Seu Wordpress está pronto para uso!

Para verificar se correu tudo bem com os *services*, lembre-se dos
comandos:

```bash
root@linuxtips-01:~/Composes/1# docker stack ls
root@linuxtips-01:~/Composes/1# docker stack services segundo
root@linuxtips-01:~/Composes/1# docker service ls
root@linuxtips-01:~/Composes/1# docker service ps segundo_db
root@linuxtips-01:~/Composes/1# docker service ps segundo_wordpress
```

Para visualizar os *logs* de determinado *service*:

```bash
root@linuxtips-01:~/Composes/2# docker service logs segundo_wordpress
segundo_wordpress.1.r6reuq8fsil0@LINUXtips-01 | WordPress not found in /var/www/html - copying now...
segundo_wordpress.1.r6reuq8fsil0@LINUXtips-01 | Complete! WordPress has been successfully copied to /var/www/html
segundo_wordpress.1.r6reuq8fsil0@LINUXtips-01 | AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 10.0.4.5. Set the 'ServerName' directive globally to suppress this message
segundo_wordpress.1.r6reuq8fsil0@LINUXtips-01 | AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 10.0.4.5. Set the 'ServerName' directive globally to suppress this message
segundo_wordpress.1.r6reuq8fsil0@LINUXtips-01 | [Sun Jun 11 10:32:47.392836 2017] [mpm_prefork:notice] [pid 1] AH00163: Apache/2.4.10 (Debian) PHP/5.6.30 configured -- resuming normal operations
segundo_wordpress.1.r6reuq8fsil0@LINUXtips-01 | [Sun Jun 11 10:32:47.392937 2017] [core:notice] [pid 1] AH00094: Command line: 'apache2 -D FOREGROUND'

root@linuxtips-01:~/Composes/2#
```

E se for necessária uma modificação em meu *stack* e depois um
*re-deploy*, como eu faço? É possível?

Claro! Afinal, Docker é muita vida!

```bash
root@linuxtips-01:~/Composes# mkdir 3
root@linuxtips-01:~/Composes# cd 3
root@linuxtips-01:~/Composes/3# vim docker-compose.yml
```

```yaml
version: "3"
    services:
        web:
            image: nginx
            deploy:
                replicas: 5
            resources:
                limits:
                cpus: "0.1"
                memory: 50M
            restart_policy:
                condition: on-failure
            ports:
            - "8080:80"
            networks:
            - webserver
        
        visualizer:
            image: dockersamples/visualizer:stable
            ports:
            - "8888:8080"
            volumes:
            - "/var/run/docker.sock:/var/run/docker.sock"
            deploy:
                placement:
                    constraints: [node.role == manager]

    networks:
    - webserver

networks:
    webserver:
```

Perceba que apenas adicionamos um novo *service* ao nosso *stack*, o
*visualizer*. A ideia é realizar o *update* somente no *stack* para
adicionar o *visualizer*, sem deixar indisponível o *service* *web.*

Antes de realizarmos o *update* desse *stack*, vamos conhecer as novas
opções que estão no *compose file* desse exemplo:

**deploy:**

-   **placement:** -- Usado para definir a localização do nosso *service.*

-   **constraints: \[node.role == manager\]** -- Regra que obriga a criação desse *service* somente nos *nodes manager*.

Agora vamos atualizar o nosso *stack*:

```bash
root@linuxtips-01:~/Composes/3# docker stack deploy -c docker-compose.yml primeiro
Creating service primeiro_visualizer
Updating service primeiro_web (id: mx0p4vbrzfujk087c3xe2sjvo)

root@linuxtips-01:~/Composes/3#
```

Perceba que, para realizar o *update* do *stack*, utilizamos o mesmo
comando que usamos para realizar o primeiro *deploy* do *stack*, o
"docker stack deploy".

Que tal aumentar ainda mais a complexidade e o número de *services* de
um *stack*? 'Bora?

Para esse exemplo, vamos utilizar um projeto do próprio Docker
([https://github.com/dockersamples/example-voting-app](https://github.com/dockersamples/example-voting-app)),
onde teremos diversos *services*. Vamos criar mais um diretório para
receber o nosso projeto:

```bash
root@linuxtips-01:~/Composes# mkdir 4
root@linuxtips-01:~/Composes# cd 4
root@linuxtips-01:~/Composes/4# vim compose-file.yml
```

```yaml
version: "3"

services:
    redis:
        image: redis:alpine
        ports:
        - "6379"
        networks:
        - frontend
        deploy:
            replicas: 2
            update_config:
                parallelism: 2
                delay: 10s
            restart_policy:
                condition: on-failure
    
    db:
        image: postgres:9.4
        volumes:
        - db-data:/var/lib/postgresql/data
        networks:
        - backend
        deploy:
            placement:
                constraints: [node.role == manager]

    vote:
        image: dockersamples/examplevotingapp_vote:before
        ports:
        - 5000:80
        networks:
        - frontend
        depends_on:
        - redis
        deploy:
            replicas: 2
            update_config:
                parallelism: 2
            restart_policy:
                condition: on-failure
    
    result:
        image: dockersamples/examplevotingapp_result:before
        ports:
        - 5001:80
        networks:
        - backend
        depends_on:
        - db
        deploy:
            replicas: 1
            update_config:
                parallelism: 2
                delay: 10s
            restart_policy:
                condition: on-failure
        
    worker:
        image: dockersamples/examplevotingapp_worker
        networks:
        - frontend
        - backend
        deploy:
            mode: replicated
            replicas: 1
            labels: [APP=VOTING]
            restart_policy:
                condition: on-failure
                delay: 10s
                max_attempts: 3
                window: 120s
            placement:
                constraints: [node.role == manager]
    
    visualizer:
        image: dockersamples/visualizer:stable
        ports:
        - "8080:8080"
        stop_grace_period: 1m30s
        volumes:
            - "/var/run/docker.sock:/var/run/docker.sock"
        deploy:
            placement:
                constraints: [node.role == manager]
networks:
    frontend:
    backend:

volumes:
    db-data:
```

Ficou mais complexo ou não? Acho que não, pois no Docker tudo é bastante
simples!

Temos algumas novas opções nesse exemplo, vamos conhecê-las:

**deploy:**

-   **mode: replicated** -- Qual é o tipo de deployment? Temos dois, o *global* e o *replicated*. No *replicated* você escolhe a quantidade de réplicas do seu *service*, já no *global* você não escolhe a quantidade de réplicas, ele irá subir uma réplica por *node* de seu *cluster* (uma réplica em cada *node* de seu *cluster*).

**update\_config:**

-   **parallelism: 2** -- Como irão ocorrer os updates (no caso, de 2 em 2).

-   **delay: 10s** -- Com intervalo de 10 segundos.

**restart\_policy:**

-   **condition: on-failure** -- Em caso de falha, *restart.*

-   **delay: 10s** -- Com intervalo de 10 segundos.

-   **max\_attempts: 3** -- Com no máximo três tentativas.

-   **window: 120s** -- Tempo para definir se o *restart* do *container* ocorreu com sucesso.

Agora vamos realizar o *deploy* do nosso *stack*:

```bash
root@linuxtips-01:~/Composes/4# docker stack deploy -c docker-compose.yml quarto
Creating network quarto_default
Creating network quarto_frontend
Creating network quarto_backend
Creating service quarto_worker
Creating service quarto_visualizer
Creating service quarto_redis
Creating service quarto_db
Creating service quarto_vote
Creating service quarto_result

root@linuxtips-01:~/Composes/4#
```

Verificando os *services*:

```bash
root@linuxtips-01:~/Composes/4# docker service ls
ID            NAME               MODE       REPLICAS   IMAGE                                             PORTS
3hi3sx2on3t5  quarto_worker      replicated 1/1        dockersamples/examplevotingapp_worker:latest
hbsp4fdcvgnz  quarto_visualizer  replicated 1/1        dockersamples/visualizer:stable                   :8080->8080/tcp
k6xuqbq7g55a  quarto_db          replicated 1/1        postgres:9.4 
p2reijydxnsw  quarto_result      replicated 1/1        dockersamples/examplevotingapp_result:before      :5001->80/tcp
rtwnnkwftg9u  quarto_redis       replicated 2/2        redis:alpine                                      :0->6379/tcp
w2ritqiklpok  quarto_vote        replicated 2/2        dockersamples/examplevotingapp_vote:before        :5000->80/tcp

root@linuxtips-01:~/Composes/4#
```

Lembre-se de sempre utilizar os comandos que já conhecemos para
visualizar *stack*, *services*, volumes, *container*, etc.

Para acessar os services em execução, abra um navegador e vá aos
seguintes endereços:

-   **Visualizar a página de votação:** http://IP_CLUSTER:5000/

-   **Visualizar a página de resultados:** http://IP_CLUSTER:5001/

-   **Visualizar a página de com os *containers* e seus *nodes*:** http://IP_CLUSTER:8080/

Vamos para mais um exemplo. Agora vamos realizar o *deploy* de um
*stack* completo de monitoração para o nosso *cluster* e todas as demais
máquinas de nossa infraestrutura. Nesse exemplo vamos utilizar um
arquivo YML que realizará o *deploy* de diversos *containers* para que
possamos ter as seguintes ferramentas integradas:

-   **Prometheus** -- Para armazenar todas as métricas de nosso ambiente.

-   **cAdvisor** -- Para coletar informações dos *containers*.

-   **Node Exporter** -- Para coletar informações dos *nodes* do *cluster* e demais máquinas do ambiente.

-   **Netdata** -- Para coletar mais de 5 mil métricas de nossas máquinas, além de prover um *dashboard* sensacional.

-   **Rocket.Chat** -- Para que possamos nos comunicar com outros times e pessoas e também para integrá-lo ao sistema de monitoração, notificando quando os alertas acontecem. O Rocket.Chat é uma excelente alternativa ao Slack.

-   **AlertManager** -- Integrado ao Prometheus e ao Rocket.Chat, é o responsável por gerenciar nossos alertas.

-   **Grafana** -- Integrado à nossa solução de monitoração, ele é o responsável pelos *dashboards* que são produzidos através das métricas que estão armazenadas no Prometheus.

Com esse *stack* é possível monitorar *containers*, VMs e máquinas
físicas. Porém, o nosso foco agora é somente no que se refere ao livro e
a este capítulo, ou seja, as informações contidas no *compose file* que
definirão nosso *stack*.

Para maiores detalhes em relação ao *Giropops-Monitoring*, acesse o
repositório no endereço:
[https://github.com/badtuxx/giropops-monitoring](https://github.com/badtuxx/giropops-monitoring).

Antes de conhecer nosso *compose file*, precisamos realizar o clone do
projeto:

```bash
# git clone https://github.com/badtuxx/giropops-monitoring.git
```

Acesse o diretório "giropops-monitoring":

```bash
# cd giropops-monitoring
```

O nosso foco aqui será em três caras: o arquivo "grafana.config", o
diretório "conf" e o nosso querido e idolatrado "docker-compose.yml".

O arquivo "grafana.config" contém variáveis que queremos passar ao nosso
Grafana. Nesse momento a única informação importante é o *password* do
*admin*, usuário que utilizaremos para logar na interface web do
Grafana.

O diretório "conf" possui os arquivos necessários para que a integração
entre as aplicações de nosso *stack* funcionem corretamente.

Já o nosso *compose file* traz todas as informações necessárias para que
nós possamos realizar o *deploy* de nosso *stack*.

Como o nosso foco é o *compose file*, 'bora lá conhecê-lo!

```bash
# cat docker-compose.yml
version: '3.3'
services:
    prometheus:
        image: linuxtips/prometheus_alpine
        volumes:
        - ./conf/prometheus/:/etc/prometheus/
        - prometheus_data:/var/lib/prometheus
        networks:
        - backend
        ports:
        - 9090:9090

    node-exporter:
        image: linuxtips/node-exporter_alpine
        hostname: {% raw %}'{{.Node.ID}}'{% endraw %}
        volumes:
        - /proc:/usr/proc
        - /sys:/usr/sys
        - /:/rootfs
        deploy:
            mode: global
        networks:
        - backend
        ports:
        - 9100:9100

    alertmanager:
        image: linuxtips/alertmanager_alpine
        volumes:
        - ./conf/alertmanager/:/etc/alertmanager/
        networks:
        - backend
        ports:
        - 9093:9093

    cadvisor:
        image: google/cadvisor
        hostname: {% raw %}'{{.Node.ID}}'{% endraw %}
        volumes:
        - /:/rootfs:ro
        - /var/run:/var/run:rw
        - /sys:/sys:ro
        - /var/lib/docker/:/var/lib/docker:ro
        - /var/run/docker.sock:/var/run/docker.sock:ro
        networks:
        - backend
        deploy:
            mode: global
        ports:
        - 8080:8080

    grafana:
        image: nopp/grafana_alpine
        depends_on:
        - prometheus
        volumes:
        - ./conf/grafana/grafana.db:/grafana/data/grafana.db
        env_file:
        - grafana.config
        networks:
        - backend
        - frontend
        ports:
        - 3000:3000

    # If you already has a RocketChat instance running, just comment the code of rocketchat, mongo and mongo-init-replica services bellow
    rocketchat:
        image: rocketchat/rocket.chat:latest
        volumes:
        - rocket_uploads:/app/uploads
        environment:
        - PORT=3080
        - ROOT_URL=http://YOUR_IP:3080
        - MONGO_URL=mongodb://giropops_mongo:27017/rocketchat
        - MONGO_OPLOG_URL=mongodb://giropops_mongo:27017/local
        depends_on:
        - giropops_mongo
        ports:
        - 3080:3080

    mongo:
        image: mongo:3.2
        volumes:
        - mongodb_data:/data/db
        #- ./data/dump:/dump
        command: mongod --smallfiles --oplogSize 128 --replSet rs0
        mongo-init-replica:
        image: mongo:3.2
        command: 'mongo giropops_mongo/rocketchat --eval "rs.initiate({_id: ''rs0'', members: [ { _id: 0, host: ''localhost:27017''} ]})"'
        depends_on:
        - giropops_mongo

networks:
    frontend:
    backend:

volumes:
    prometheus_data:
    grafana_data:
    rocket_uploads:
    mongodb_data:
```

Perceba que já conhecemos todas as opções que estão nesse exemplo, nada
de novo. :D

O que precisamos agora é realizar o *deploy* de nosso *stack*:

```bash
# docker stack deploy -c docker-compose.yml giropops
Creating network giropops_backend
Creating network giropops_frontend
Creating network giropops_default
Creating service giropops_grafana
Creating service giropops_rocketchat
Creating service giropops_mongo
Creating service giropops_mongo-init-replica
Creating service giropops_prometheus
Creating service giropops_node-exporter
Creating service giropops_alertmanager
Creating service giropops_cadvisor
```

Caso queira verificar se os *services* estão em execução:

```bash
# docker service ls
```

Para listar os *stacks*:

```bash
# docker stack ls
```

Para acessar os serviços do quais acabamos de realizar o *deploy*, basta
acessar os seguintes endereços:

-   **Prometheus**: http://SEU_IP:9090

-   **AlertManager**: http://SEU_IP:9093

-   **Grafana**: http://SEU_IP:3000

-   **Node\_Exporter**: http://SEU_IP:9100

-   **Rocket.Chat:** http://SEU_IP:3080

-   **cAdivisor**: http://SEU_IP:8080

Para remover o *stack*:

```bash
# docker stack rm giropops
```

Lembrando: para conhecer mais sobre o *giropops-monitoring* acesse o
repositório no GitHub e assista à série de vídeos em que o Jeferson fala
detalhadamente como montou essa solução:

-   **Repo**:
    > [https://github.com/badtuxx/giropops-monitoring](https://github.com/badtuxx/giropops-monitoring)

-   **Vídeos**:
    > [https://www.youtube.com/playlist?list=PLf-O3X2-mxDls9uH8gyCQTnyXNMe10iml](https://www.youtube.com/playlist?list=PLf-O3X2-mxDls9uH8gyCQTnyXNMe10iml)

E assim termina a nossa jornada no mundo do Docker. Esperamos que você
tenha aprendido e, mais do que isso, tenha gostado de dividir esse tempo
conosco para falar sobre o que nós mais amamos, tecnologia!

## 15.2. E já acabou? :(

Esperamos que você tenha curtido viajar conosco durante o seu
aprendizado sobre *containers* e principalmente sobre o ecossistema do
Docker, que é sensacional!

Não pare de aprender mais sobre Docker! Continue acompanhando o Canal
LinuxTips no [https://www.youtube.com/linuxtips](https://www.youtube.com/linuxtips)
e fique ligado no site do Docker, pois sempre tem novidades e ótima
documentação!

Junte-se a nós no Discord para que possa acompanhar e tirar dúvidas que
possam ter surgido durante seus estudos!

\#VAIIII
