---
title: Capítulo 05
---

# 5. Configurando CPU e memória para os meus *containers*

Vamos imaginar que você precise subir quatro *containers* para um
projeto novo. Esses *containers* possuem as seguintes características:

-   Dois *web* *servers*.

-   Dois DB MySQL.

Evidentemente, por se tratar de serviços diferentes, na maioria dos
casos possuem características de consumo de recursos, como CPU e
memória, diferentes um do outro.

-   *Web server* -- 0,5 CPU \| 128 MB de memória

-   DB MySQL -- 1 CPU \| 256 MB de memória

E agora, como fazemos? :(

Por padrão, quando você executa um *container* sem especificar a
quantidade de recursos que ele irá utilizar, ele sobe sem um controle,
podendo inclusive impactar o *host* onde está sendo executado.

Portanto, é muito importante limitar a utilização de recursos de seus
*containers*, visando um melhor aproveitamento de seus recursos
computacionais, como veremos agora. :)

## 5.1. Especificando a quantidade de memória

Primeiro, vamos executar um *container* para realizarmos o nosso
exemplo.

```bash
root@linuxtips:~# docker container run -ti --name teste debian
```

Agora vamos visualizar a quantidade de memória que está configurada para
esse *container*. Uma forma fácil é utilizar a saída do comando "docker
container inspect":

```bash
root@linuxtips:~# docker container inspect teste | grep -i mem
	"CpusetMems": "",
	"KernelMemory": 0,
	"Memory": 0,
	"MemoryReservation": 0,
	"MemorySwap": 0,
	"MemorySwappiness": -1,
```

Como percebemos, os valores correspondentes à memória estão zerados, ou
seja, sem nenhum limite estabelecido.

Vamos agora subir um novo *container*, porém passando os valores para
que utilize 512 MB de memória:

```bash
root@linuxtips:~# docker container run -ti -m 512M --name novo_container debian
```

Utilizamos o parâmetro "-m" para especificar a quantidade de memória que
desejamos disponibilizar ao *container*. Vamos utilizar o "docker
container inspect" novamente para verificar se a nossa configuração
funcionou:

```bash
root@linuxtips:~# docker container inspect novo_container | grep -i mem
	"CpusetMems": "",
	"KernelMemory": 0,
	"Memory": 536870912,
	"MemoryReservation": 0,
	"MemorySwap": -1,
	"MemorySwappiness": -1,
```

Muito bom, parece que deu certo!

Podemos ver no campo "Memory" o valor utilizado na criação do
*container*. Vale ressaltar que o valor exibido é em *bytes*.

Quando você utiliza o parâmetro "-m" ou "\--memory", você está passando
o máximo de memória que o *container* utilizará do *host*.

## 5.2. Especificando a quantidade de CPU

Para que você consiga limitar a quantidade de CPUs que os *containers*
irão consumir, basta utilizar o parâmetro "\--cpu". Com ele é possível
dizer a quantidade de CPUs que você deseja disponibilizar para
determinado *container*. Por exemplo, se utilizarmos "\--cpus=0.5"
estamos dizendo ao Docker para limitar o consumo pelo *container* em
meio CPU. Fácil, não?

```bash
# docker container run --cpus=0.5 --name teste1 nginx
```

Com o comando anterior, estamos subindo um *container* utilizando a
imagem do Nginx -- até aqui nenhuma novidade, certo? Porém, agora
passamos o parâmetro "\--cpus=0.5" falando para o Docker que esse
*container* poderá utilizar no máximo 0,5 CPU, ou seja, metade de 1
*core*. :D Simples como voar.

Para verificar, vamos utilizar o comando "docker container inspect":

```bash
root@linuxtips:~# docker container inspect teste1 | grep -i cpu
	"CpuShares": 0,
	"NanoCpus": 500000000,
	"CpuPeriod": 0,
	"CpuQuota": 0,
	"CpusetCpus": "",
	"CpusetMems": "",
```

O campo "NanoCpus" traz a informação que configuramos. :)

Simples, fácil e rápido!

## 5.3. Eu consigo alterar CPU e memória dos meus *containers* em execução?

Sim! \\o/

Com o lançamento da versão 1.10 do Docker, temos o comando "docker
update", que permite alterar as configurações referentes a CPU, memória
e I/O com o *container* em execução de forma muito simples! Isso é
fantástico!

Como exemplo, iremos subir um *container* e também alterar as
informações referentes a memória e CPU:

```bash
root@linuxtips:~# docker container run -ti --cpus=4 -m 512m --name teste1 nginx

root@linuxtips:~# docker container inspect teste1 | grep -i cpu
	"CpuShares": 0,
	"NanoCpus": 4000000000,
	"CpuPeriod": 0,
	"CpuQuota": 0,
	"CpusetCpus": "",
	"CpusetMems": "",

root@linuxtips:~# docker container inspect teste1 | grep -i mem
	"CpusetMems": "",
	"KernelMemory": 0,
	"Memory": 536870912,
	"MemoryReservation": 0,
	"MemorySwap": -1,
	"MemorySwappiness": -1,
```

Agora, vamos alterar os valores de limite de CPU e memória:

```bash
root@linuxtips:~# docker container update -m 256m --cpus=1 teste1
teste1

root@linuxtips:~# docker container inspect teste1 | grep -i cpu
	"CpuShares": 0,
	"NanoCpus": 1000000000,
	"CpuPeriod": 0,
	"CpuQuota": 0,
	"CpusetCpus": "",
	"CpusetMems": "",

root@linuxtips:~# docker container inspect teste1 | grep -i mem
	"CpusetMems": "",
	"KernelMemory": 0,
	"Memory": 268435456,
	"MemoryReservation": 0,
	"MemorySwap": -1,
	"MemorySwappiness": -1,
```

Funcionou? SIMMM!

Assim, com os *containers* em execução, mudamos as informações
referentes à memória e ao CPU!

Existem outros parâmetros do "docker container update". Para verificar a
lista completa, digite "docker update \--help".
