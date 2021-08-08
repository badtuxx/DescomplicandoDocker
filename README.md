![LOGO](.//media/image2.png)

# Prefácio

If you are a developer, you probably have heard about Docker by now:
Docker is the ideal platform to run applications in containers. Fine,
but what are those containers? Containers are a lightweight
virtualization technique providing us with lots of possibilities: thanks
to them, we can ship applications faster; we can easily implement CI/CD
(continuous integration and continuous delivery); we can set up
development environments faster than ever; we can ensure parity between
those development environments and our production servers; and much
more. If you write code, if you deploy code, if you operate code, then I
promise that containers are going to make your life easier.

But containers have been around for *decades*, and Docker was only
released in 2013. So what's the big deal? Why is everybody so excited
about Docker, if the technology behind it is more than ten years old?

Because the main innovation of Docker is not the technology. The main
innovation of Docker is to make this technology available to every
developer and sysadmin, very easily, without having to spend years of
practice to become an expert, and without requiring to develop tons of
custom tools.

I will share a secret with you: the really important thing is not Docker
and containers. What is really important is to improve the quality of
our software, reduce our development and deployment costs, release
better, more reliable code, faster. It turns out that Docker is an
insanely efficient way to achieve those goals. That's why it is so
popular.

This book will teach you all you need to know to get started with
Docker, and use it to build, ship, and run your applications. It will be
your guide in the world of containers, and on the path to ship code
better than you ever did before.

***Jérôme Petazzoni***

Docker Tinkerer Extraordinaire

# Sobre o Livro

A proposta deste livro é ajudá-lo a construir ambientes utilizando
*containers* com a ferramenta que está revolucionando as empresas de
tecnologia ao redor do mundo: vamos aprender e brincar bastante com o
sensacional Docker!

De maneira leve e totalmente prática, vamos aprender desde o que é o
Docker até a criação de um *cluster* Docker com diversos *containers* em
execução! Sempre de forma prática, vamos abordar temas importantes para
que consiga administrar ambientes que utilizam ou pretendam utilizar o
Docker.

Inclusive, vamos aprender a montar *dockerfiles* personalizados para
construção de imagens de *containers*, além de conhecer como melhor
administrá-las.

Também veremos na prática como utilizar o Docker Machine para criação de
*hosts* Docker, seja local ou na nuvem. Vamos criar um *cluster*
utilizando o Swarm e aprender como escalar o nosso ambiente através do
Compose.

Nesta segunda edição adicionamos muitas novidades, como o 'docker
secret' e o 'docker stack', e atualizamos praticamente todo o
restante do livro, visando torna-lo super atual e didático. Adicionamos
mais exemplos práticos, para que sua experiência possa ser ainda mais
agradável e proveitosa.

# Sumário

- [**Introdução**](./capitulos/CHAPTER_00.md#introdução)

- [**1. O que é *container*?**](./capitulos/CHAPTER_01.md#1-o-que-é-container)

	- [1.1. Então vamos lá, o que é um *container*?](./capitulos/CHAPTER_01.md#11-então-vamos-lá-o-que-é-um-container)

	- [1.2. E quando começou que eu não vi?](./capitulos/CHAPTER_01.md#12-e-quando-começou-que-eu-não-vi)

- [**2. O que é o Docker?**](./capitulos/CHAPTER_02.md#2-o-que-é-o-docker)

	- [2.1. Onde entra o Docker nessa história?](./capitulos/CHAPTER_02.md#21-onde-entra-o-docker-nessa-história)

	- [2.2. E esse negócio de camadas?](./capitulos/CHAPTER_02.md#22-e-esse-negócio-de-camadas)

		- [2.2.1. *Copy-On-Write* (COW) e Docker](./capitulos/CHAPTER_02.md#221-copy-on-write-cow-e-docker)

	- [2.3. Storage drivers](./capitulos/CHAPTER_02.md#23-storage-drivers)

		- [2.3.1. AUFS (*Another Union File System*)](./capitulos/CHAPTER_02.md#231-aufs-another-union-file-system)

		- [2.3.2. *Device Mapper*](./capitulos/CHAPTER_02.md#232-device-mapper)

		- [2.3.3. *Overlay* e *Overlay2*](./capitulos/CHAPTER_02.md#233-overlayfs-e-overlayfs2)

		- [2.3.4. BTRFS](./capitulos/CHAPTER_02.md#234-btrfs)

	- [2.4. Docker Internals](./capitulos/CHAPTER_02.md#24-docker-internals)

	- [2.5. *Namespaces*](./capitulos/CHAPTER_02.md#25-namespaces)

		- [2.5.1. PID *namespace*](./capitulos/CHAPTER_02.md#251-pid-namespace)

		- [2.5.2. Net *namespace*](./capitulos/CHAPTER_02.md#252-net-namespace)

		- [2.5.3. Mnt *namespace*](./capitulos/CHAPTER_02.md#253-mnt-namespace)

		- [2.5.4. IPC *namespace*](./capitulos/CHAPTER_02.md#254-ipc-namespace)

		- [2.5.5. UTS *namespace*](./capitulos/CHAPTER_02.md#255-uts-namespace)

		- [2.5.6. User *namespace*](./capitulos/CHAPTER_02.md#256-user-namespace)

	- [2.6. *Cgroups*](./capitulos/CHAPTER_02.md#26-cgroups)

	- [2.7. *Netfilter*](./capitulos/CHAPTER_02.md#27-netfilter)

	- [2.8. Para quem ele é bom?](./capitulos/CHAPTER_02.md#28-para-quem-ele-é-bom)

- [**3. Instalando o Docker**](./capitulos/CHAPTER_03.md#3-instalando-o-docker)

	- [3.1. Quero instalar, vamos lá?](./capitulos/CHAPTER_03.md#31-quero-instalar-vamos-lá)

	- [3.2. Instalando no Debian/Centos/Ubuntu/Suse/Fedora](./capitulos/CHAPTER_03.md#32-instalando-no-debiancentosubuntususefedora)

	- [3.3. Instalando 'manualmente' no Debian](./capitulos/CHAPTER_03.md#33-instalando-manualmente-no-debian)

		- [3.3.1. Dica importante](./capitulos/CHAPTER_03.md#331-dica-importante)

- [**4. Executando e administrando *containers* Docker**](./capitulos/CHAPTER_04.md#4-executando-e-administrando-containers-docker)

	- [4.1. Então vamos brincar com esse tal de *container*!](./capitulos/CHAPTER_04.md#41-então-vamos-brincar-com-esse-tal-de-container)

	- [4.2. Legal, quero mais!](./capitulos/CHAPTER_04.md#42-legal-quero-mais)

		- [4.2.1. Modo interativo](./capitulos/CHAPTER_04.md#421-modo-interativo)

		- [4.2.2. *Daemonizando* o *container*](./capitulos/CHAPTER_04.md#422-daemonizando-o-container)

	- [4.3. Entendi, agora vamos praticar um pouco?](./capitulos/CHAPTER_04.md#43-entendi-agora-vamos-praticar-um-pouco)

	- [4.4. Tá, agora quero sair\...](./capitulos/CHAPTER_04.md#44-tá-agora-quero-sair)

	- [4.5. Posso voltar ao *container*?](./capitulos/CHAPTER_04.md#45-posso-voltar-ao-container)

	- [4.6. Continuando com a brincadeira\...](./capitulos/CHAPTER_04.md#46-continuando-com-a-brincadeira)

	- [4.7. Subindo e matando *containers*...](./capitulos/CHAPTER_04.md#47-subindo-e-matando-containers)

	- [4.8. Visualizando o consumo de recursos pelo *container*\...](./capitulos/CHAPTER_04.md#48-visualizando-o-consumo-de-recursos-pelo-container)

	- [4.9. Cansei de brincar de container, quero removê-lo!](./capitulos/CHAPTER_04.md#49-cansei-de-brincar-de-container-quero-removê-lo)

- [**5. Configurando CPU e memória para os meus *containers***](./capitulos/CHAPTER_05.md#5-configurando-cpu-e-memória-para-os-meus-containers)

	- [5.1. Especificando a quantidade de memória](./capitulos/CHAPTER_05.md#51-especificando-a-quantidade-de-memória)

	- [5.2. Especificando a quantidade de CPU](./capitulos/CHAPTER_05.md#52-especificando-a-quantidade-de-cpu)

	- [5.3. Eu consigo alterar CPU e memória dos meus *containers* em execução?](./capitulos/CHAPTER_05.md#53-eu-consigo-alterar-cpu-e-memória-dos-meus-containers-em-execução)

- [**6. Meu primeiro e tosco *dockerfile*\...**](./capitulos/CHAPTER_06.md#6-meu-primeiro-e-tosco-dockerfile)

- [**7. Entendendo volumes**](./capitulos/CHAPTER_07.md#7-entendendo-volumes)

	- [7.1. Introdução a volumes no Docker](./capitulos/CHAPTER_07.md#71-introdução-a-volumes-no-docker)

	- [7.2. Criando volumes](./capitulos/CHAPTER_07.md#72-criando-volumes)

	- [7.3. Localizando volumes](./capitulos/CHAPTER_07.md#73-localizando-volumes)

	- [7.4. Criando e montando um *data-only container*](./capitulos/CHAPTER_07.md#74-criando-e-montando-um-data-only-container)

	- [7.5. Sempre é bom um *backup*\...](./capitulos/CHAPTER_07.md#75-sempre-é-bom-um-backup)

- [**8. Criando e gerenciando imagens**](./capitulos/CHAPTER_08.md#8-criando-e-gerenciando-imagens)

	- [8.1. Agora eu quero criar minha imagem, posso?](./capitulos/CHAPTER_08.md#81-agora-eu-quero-criar-minha-imagem-posso)

	- [8.2. Vamos começar do começo então, *dockerfile*!](./capitulos/CHAPTER_08.md#82-vamos-começar-do-começo-então-dockerfile)

	- [8.3. Bora aprender um pouco mais sobre *dockerfile*?](./capitulos/CHAPTER_08.md#83-bora-aprender-um-pouco-mais-sobre-dockerfile)

	- [8.4. *Multi-stage*](./capitulos/CHAPTER_08.md#84-multi-stage)

	- [8.5. Vamos customizar uma imagem base agora?](./capitulos/CHAPTER_08.md#85-vamos-customizar-uma-imagem-base-agora)

- [**9. Compartilhando as imagens**](./capitulos/CHAPTER_09.md#9-compartilhando-as-imagens)

	- [9.1. O que é o *Docker Hub*?](./capitulos/CHAPTER_09.md#91-o-que-é-o-docker-hub)

	- [9.2. Vamos criar uma conta?](./capitulos/CHAPTER_09.md#92-vamos-criar-uma-conta)

	- [9.3. Agora vamos compartilhar essas imagens na *interwebs*!](./capitulos/CHAPTER_09.md#93-agora-vamos-compartilhar-essas-imagens-na-interwebs)

	- [9.4. Não confio na internet; posso criar o meu *registry* local?](./capitulos/CHAPTER_09.md#94-não-confio-na-internet-posso-criar-o-meu-registry-local)

- [**10. Gerenciando a rede dos *containers***](./capitulos/CHAPTER_10.md#10-gerenciando-a-rede-dos-containers)

	- [10.1. Consigo fazer com que a porta do *container* responda na porta do *host*?](./capitulos/CHAPTER_10.md#101-consigo-fazer-com-que-a-porta-do-container-responda-na-porta-do-host)

	- [10.2. E como ele faz isso? Mágica?](./capitulos/CHAPTER_10.md#102-e-como-ele-faz-isso-mágica)

- [**11. Controlando o *daemon* do Docker**](./capitulos/CHAPTER_11.md#11-controlando-o-daemon-do-docker)

	- [11.1. O Docker sempre utiliza 172.16.X.X ou posso configurar outro intervalo de IP?](./capitulos/CHAPTER_11.md#111-o-docker-sempre-utiliza-17216xx-ou-posso-configurar-outro-intervalo-de-ip)

	- [11.2. Opções de *sockets*](./capitulos/CHAPTER_11.md#112-opções-de-sockets)

		- [11.2.1. *Unix Domain Socket*](./capitulos/CHAPTER_11.md#1121-unix-domain-socket)

		- [11.2.2. TCP](./capitulos/CHAPTER_11.md#1122-tcp)

	- [11.3. Opções de *storage*](./capitulos/CHAPTER_11.md#113-opções-de-storage)

	- [11.4. Opções de rede](./capitulos/CHAPTER_11.md#114-opções-de-rede)

	- [11.5. Opções diversas](./capitulos/CHAPTER_11.md#115-opções-diversas)

- [**12. Docker Machine**](./capitulos/CHAPTER_12.md#12-docker-machine)

	- [12.1. Ouvi dizer que minha vida ficaria melhor com o Docker Machine!](./capitulos/CHAPTER_12.md#121-ouvi-dizer-que-minha-vida-ficaria-melhor-com-o-docker-machine)

		- [12.1.1. Vamos instalar?](./capitulos/CHAPTER_12.md#1211-vamos-instalar)

		- [12.1.2. Vamos iniciar nosso primeiro projeto?](./capitulos/CHAPTER_12.md#1212-vamos-iniciar-nosso-primeiro-projeto)

- [**13. Docker Swarm**](./capitulos/CHAPTER_13.md#13-docker-swarm)

	- [13.1. Criando o nosso *cluster*!](./capitulos/CHAPTER_13.md#131-criando-o-nosso-cluster)

	- [13.2. O sensacional services!](./capitulos/CHAPTER_13.md#132-o-sensacional-services)

- [**14. Docker Secrets**](./capitulos/CHAPTER_14.md#14-docker-secrets)

	- [14.1. O comando *docker secret*](./capitulos/CHAPTER_14.md#141-o-comando-docker-secret)

	- [14.2. Tudo bem, mas como uso isso?](./capitulos/CHAPTER_14.md#142-tudo-bem-mas-como-uso-isso)

	- [14.3. Acessando a *secret*](./capitulos/CHAPTER_14.md#143-acessando-a-secret)

	- [14.4. Atualizando a *secret* de um serviço](./capitulos/CHAPTER_14.md#144-atualizando-a-secret-de-um-serviço)

- [**15. Docker Compose**](./capitulos/CHAPTER_15.md#15-docker-compose)

	- [15.1. O comando docker stack](./capitulos/CHAPTER_15.md#151-o-comando-docker-stack)

	- [15.2. E já acabou? :(](./capitulos/CHAPTER_15.md#152-e-já-acabou-)
