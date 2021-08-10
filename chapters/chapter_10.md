---
title: Capítulo 10
---

# 10. Gerenciando a rede dos *containers*

Quando o Docker é executado, ele cria uma *bridge* virtual chamada
"docker0", para que possa gerenciar a comunicação interna entre o
*container* e o *host* e também entre os *containers*.

Vamos conhecer alguns parâmetros do comando "Docker container run" que
irão nos ajudar com a rede em que os *containers* irão se comunicar.

-   **\--dns** -- Indica o servidor DNS.

-   **\--hostname** -- Indica um *hostname.*

-   **\--link** -- Cria um *link* entre os *containers*, sem a necessidade de se saber o IP um do outro.

-   **\--net** -- Permite configurar o modo de rede que você usará com o *container*. Temos quatro opções, mas a mais conhecida e utilizada é a "\--net=host", que permite que o *container* utilize a rede do *host* para se comunicar e não crie um *stack* de rede para o *container*.

-   **\--expose** -- Expõe a porta do *container* apenas.

-   **\--publish** -- Expõe a porta do *container* e do *host*.

-   **\--default-gateway** -- Determina a rota padrão.

-   **\--mac-address** -- Determina um MAC *address*.

Quando o *container* é iniciado, a rede passa por algumas etapas até a
sua inicialização completa:

1.  Cria-se um par de interfaces virtuais.

2.  Cria-se uma interface com nome único, como "veth1234", e em seguida *linka-se* com a *bridge* do Docker, a "docker0".

3.  Com isso, é disponibilizada a interface "eth0" dentro do *container*, em um *network namespace* único.

4.  Configura-se o MAC *address* da interface virtual do *container.*

5.  Aloca-se um IP na "eth0" do *container*. Esse IP tem que pertencer ao *range* da *bridge* "docker0"*.*

Com isso, o *container* já possui uma interface de rede e já está apto a
se comunicar com outros *containers* ou com o *host*. :D

## 10.1. Consigo fazer com que a porta do *container* responda na porta do *host*?

Sim, isso é possível e bastante utilizado.

Vamos conhecer um pouco mais sobre isso em um exemplo utilizando aquela
nossa imagem "linuxtips/apache".

Primeira coisa que temos que saber é a porta pela qual o Apache2 se
comunica. Isso é fácil, né? Se estiver com as configurações padrões de
porta de um *web server*, o Apache2 do *container* estará respondendo na
porta 80/TCP, correto?

Agora vamos fazer com que a porta 8080 do nosso *host* responda pela
porta 80 do nosso *container*, ou seja, sempre que alguém bater na porta
8080 do nosso *host*, a requisição será encaminhada para a porta 80 do
*container*. Simples, né?

Para conseguir fazer esse encaminhamento, precisamos utilizar o
parâmetro "-p" do comando "docker container run", conforme faremos no
exemplo a seguir:

```bash
root@linuxtips:~# # docker container run -ti -p 8080:80 linuxtips/apache:1.0 /bin/bash
root@4a0645de6d94:/# ps -ef
UID   PID PPID C STIME TTY      TIME CMD
root    1    0 1 18:18 ?    00:00:00 /bin/bash
root    6    1 0 18:18 ?    00:00:00 ps -ef

root@4a0645de6d94:/# /etc/init.d/apache2 start
[....] Starting web server: apache2AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.17.0.3. Set the 'ServerName' directive globally to suppress this message
. ok

root@4a0645de6d94:/# ps -ef
UID       PID PPID C STIME TTY        TIME CMD
root        1    0 0 18:18 ?      00:00:00 /bin/bash
root       30    1 0 18:19 ?      00:00:00 /usr/sbin/apache2 -k start
www-data   33   30 0 18:19 ?      00:00:00 /usr/sbin/apache2 -k start
www-data   34   30 0 18:19 ?      00:00:00 /usr/sbin/apache2 -k start
root      109    1 0 18:19 ?      00:00:00 ps -ef

root@4a0645de6d94:/# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
             valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
             valid_lft forever preferred_lft forever
74: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
        link/ether 02:42:ac:11:00:03 brd ff:ff:ff:ff:ff:ff
        inet 172.17.0.3/16 scope global eth0
             valid_lft forever preferred_lft forever
        inet6 fe80::42:acff:fe11:3/64 scope link
             valid_lft forever preferred_lft forever

root@4a0645de6d94:/#
```

Repare que passamos o parâmetro "-p" da seguinte forma:

-   **-p 8080:80** -- Onde "8080" é a porta do host e "80" a do container.

Com isso, estamos dizendo que toda requisição que chegar na porta 8080
do meu *host* deverá ser encaminhada para a porta 80 do *container.*

Já no *container*, subimos o Apache2 e verificamos o IP do *container*,
correto?

Agora vamos sair do *container* com o atalho "Ctrl + p + q". :)

A partir do *host*, vamos realizar um "curl" com destino ao IP do
*container* na porta 80, depois com destino à porta 8080 do *host* e em
seguida analisar as saídas:

```bash
root@linuxtips:~# curl <IPCONTAINER>:80
```

Se tudo ocorreu bem até aqui, você verá o código da página de
boas-vindas do Apache2.

O mesmo ocorre quando executamos o "curl" novamente, porém batendo no IP
do *host*. Veja:

```bash
root@linuxtips:~# curl <IPHOST>:8080
```

Muito fácil, chega a ser lacrimejante! \\o/

## 10.2. E como ele faz isso? Mágica?

Não, não é mágica! Na verdade, o comando apenas utiliza um módulo
bastante antigo do *kernel* do Linux chamado *netfilter*, que
disponibiliza a ferramenta *iptables*, que todos nós já cansamos de
usar.

Vamos dar uma olhada nas regras de *iptables* referentes a esse nosso
*container*. Primeiro a tabela *filter*:

```bash
root@linuxtips:~# iptables -L -n
Chain INPUT (policy ACCEPT)
target prot opt source destination

Chain FORWARD (policy ACCEPT)
target prot opt source destination
DOCKER-ISOLATION all -- 0.0.0.0/0 0.0.0.0/0
DOCKER all -- 0.0.0.0/0 0.0.0.0/0
ACCEPT all -- 0.0.0.0/0 0.0.0.0/0 ctstate RELATED,ESTABLISHED
ACCEPT all -- 0.0.0.0/0 0.0.0.0/0
ACCEPT all -- 0.0.0.0/0 0.0.0.0/0

Chain OUTPUT (policy ACCEPT)
target prot opt source destination

Chain DOCKER (1 references)
target prot opt source destination
ACCEPT tcp -- 0.0.0.0/0 172.17.0.2 tcp dpt:5000
ACCEPT tcp -- 0.0.0.0/0 172.17.0.3 tcp dpt:80

Chain DOCKER-ISOLATION (1 references)
target prot opt source destination
RETURN all -- 0.0.0.0/0 0.0.0.0/0

root@linuxtips:~#
```

Agora a tabela NAT:

```bash
root@linuxtips:~# iptables -L -n -t nat

Chain PREROUTING (policy ACCEPT)
target prot opt source destination
DOCKER all -- 0.0.0.0/0 0.0.0.0/0 ADDRTYPE match dst-type LOCAL

Chain INPUT (policy ACCEPT)
target prot opt source destination

Chain OUTPUT (policy ACCEPT)
target prot opt source destination
DOCKER all -- 0.0.0.0/0 !127.0.0.0/8 ADDRTYPE match dst-type LOCAL

Chain POSTROUTING (policy ACCEPT)
target prot opt source destination
MASQUERADE all -- 172.17.0.0/16 0.0.0.0/0
MASQUERADE tcp -- 172.17.0.2 172.17.0.2 tcp dpt:5000
MASQUERADE tcp -- 172.17.0.3 172.17.0.3 tcp dpt:80

Chain DOCKER (2 references)
target prot opt source destination
RETURN all -- 0.0.0.0/0 0.0.0.0/0
DNAT tcp -- 0.0.0.0/0 0.0.0.0/0 tcp dpt:5000 to:172.17.0.2:5000
DNAT tcp -- 0.0.0.0/0 0.0.0.0/0 tcp dpt:8080 to:172.17.0.3:80

root@linuxtips:~#
```

Como podemos notar, temos regras de NAT configuradas que permitem o DNAT
da porta 8080 do *host* para a 80 do *container.* Veja a seguir:

```bash
MASQUERADE tcp -- 172.17.0.3  172.17.0.3  tcp dpt:80
DNAT       tcp -- 0.0.0.0/0   0.0.0.0/0   tcp dpt:8080 to:172.17.0.3:80
```

Tudo isso feito "automagicamente" pelo Docker, sem a necessidade de
precisar configurar diversas regras de *iptables*. \<3
