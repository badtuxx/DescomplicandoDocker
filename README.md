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
secret' e o 'docker stack', e atualizamos todo o praticamente todo o
restante do livro, visando torna-lo super atual e didático. Adicionamos
mais exemplos práticos, para que sua experiência possa ser ainda mais
agradável e proveitosa.

# Sumário

- [**Introdução**](#introdução)

- [**1. O que é *container*?**](./chapters/CHAPTER_01.md#1-o-que-é-container)

	- [1.1. Então vamos lá, o que é um *container*?](./chapters/CHAPTER_01.md#11-então-vamos-lá-o-que-é-um-container)

	- [1.2. E quando começou que eu não vi?](./chapters/CHAPTER_01.md#12-e-quando-começou-que-eu-não-vi)

- [**2. O que é o Docker?**](./chapters/CHAPTER_02.md#2-o-que-é-o-docker)

	- [2.1. Onde entra o Docker nessa história?](./chapters/CHAPTER_02.md#21-onde-entra-o-docker-nessa-história)

	- [2.2. E esse negócio de camadas?](./chapters/CHAPTER_02.md#22-e-esse-negócio-de-camadas)

		- [2.2.1. *Copy-On-Write* (COW) e Docker](./chapters/CHAPTER_02.md#221-copy-on-write-cow-e-docker)

	- [2.3. Storage drivers](./chapters/CHAPTER_02.md#23-storage-drivers)

		- [2.3.1. AUFS (*Another Union File System*)](./chapters/CHAPTER_02.md#231-aufs-another-union-file-system)

		- [2.3.2. *Device Mapper*](./chapters/CHAPTER_02.md#232-device-mapper)

		- [2.3.3. *Overlay* e *Overlay2*](./chapters/CHAPTER_02.md#233-overlayfs-e-overlayfs2)

		- [2.3.4. BTRFS](./chapters/CHAPTER_02.md#234-btrfs)

	- [2.4. Docker Internals](./chapters/CHAPTER_02.md#24-docker-internals)

	- [2.5. *Namespaces*](./chapters/CHAPTER_02.md#25-namespaces)

		- [2.5.1. PID *namespace*](./chapters/CHAPTER_02.md#251-pid-namespace)

		- [2.5.2. *Net Namespace*](./chapters/CHAPTER_02.md#252-net-namespace)

		- [2.5.3. *Mnt Namespace*](./chapters/CHAPTER_02.md#253-mnt-namespace)

		- [2.5.4. IPC *namespace*](./chapters/CHAPTER_02.md#254-ipc-namespace)

		- [2.5.5. UTS *namespace*](./chapters/CHAPTER_02.md#255-uts-namespace)

		- [2.5.6. User *namespace*](./chapters/CHAPTER_02.md#256-user-namespace)

	- [2.6. *Cgroups*](./chapters/CHAPTER_02.md#26-cgroups)

	- [2.7. *Netfilter*](./chapters/CHAPTER_02.md#27-netfilter)

	- [2.8. Para quem ele é bom?](./chapters/CHAPTER_02.md#28-para-quem-ele-é-bom)

- [**3. Instalando o Docker**](./chapters/CHAPTER_03.md#3-instalando-o-docker)

	- [3.1. Quero instalar, vamos lá?](./chapters/CHAPTER_03.md#31-quero-instalar-vamos-lá)

	- [3.2. Instalando no Debian/Centos/Ubuntu/Suse/Fedora](./chapters/CHAPTER_03.md#32-instalando-no-debiancentosubuntususefedora)

	- [3.3. Instalando 'manualmente' no Debian](./chapters/CHAPTER_03.md#33-instalando-manualmente-no-debian)

		- [3.3.1. Dica importante](./chapters/CHAPTER_03.md#331-dica-importante)

- [**4. Executando e administrando *containers* Docker**](./chapters/CHAPTER_04.md#4-executando-e-administrando-containers-docker)

	- [4.1. Então vamos brincar com esse tal de *container*!](./chapters/CHAPTER_04.md#41-então-vamos-brincar-com-esse-tal-de-container)

	- [4.2. Legal, quero mais!](./chapters/CHAPTER_04.md#42-legal-quero-mais)

		- [4.2.1. Modo interativo](./chapters/CHAPTER_04.md#421-modo-interativo)

		- [4.2.2. *Daemonizando* o *container*](./chapters/CHAPTER_04.md#422-daemonizando-o-container)

	- [4.3. Entendi, agora vamos praticar um pouco?](./chapters/CHAPTER_04.md#43-entendi-agora-vamos-praticar-um-pouco)

	- [4.4. Tá, agora quero sair\...](./chapters/CHAPTER_04.md#44-tá-agora-quero-sair)

	- [4.5. Posso voltar ao *container*?](./chapters/CHAPTER_04.md#45-posso-voltar-ao-container)

	- [4.6. Continuando com a brincadeira\...](./chapters/CHAPTER_04.md#46-continuando-com-a-brincadeira)

	- [4.7. Subindo e matando *containers*...](./chapters/CHAPTER_04.md#47-subindo-e-matando-containers)

	- [4.8. Visualizando o consumo de recursos pelo *container*\...](./chapters/CHAPTER_04.md#48-visualizando-o-consumo-de-recursos-pelo-container)

	- [4.9. Cansei de brincar de container, quero removê-lo!](./chapters/CHAPTER_04.md#49-cansei-de-brincar-de-container-quero-removê-lo)

- [**5. Configurando CPU e memória para os meus *containers***](./chapters/CHAPTER_05.md#5-configurando-cpu-e-memória-para-os-meus-containers)

	- [5.1. Especificando a quantidade de memória](./chapters/CHAPTER_05.md#51-especificando-a-quantidade-de-memória)

	- [5.2. Especificando a quantidade de CPU](./chapters/CHAPTER_05.md#52-especificando-a-quantidade-de-cpu)

	- [5.3. Eu consigo alterar CPU e memória dos meus *containers* em execução?](./chapters/CHAPTER_05.md#53-eu-consigo-alterar-cpu-e-memória-dos-meus-containers-em-execução)

- [**6. Meu primeiro e tosco *dockerfile*\...**](./chapters/CHAPTER_06.md#6-meu-primeiro-e-tosco-dockerfile)

- [**7. Entendendo volumes**](./chapters/CHAPTER_07.md#7-entendendo-volumes)

	- [7.1. Introdução a volumes no Docker](./chapters/CHAPTER_07.md#71-introdução-a-volumes-no-docker)

	- [7.2. Criando volumes](./chapters/CHAPTER_07.md#72-criando-volumes)

	- [7.3. Localizando volumes](./chapters/CHAPTER_07.md#73-localizando-volumes)

	- [7.4. Criando e montando um *data-only container*](./chapters/CHAPTER_07.md#74-criando-e-montando-um-data-only-container)

	- [7.5. Sempre é bom um *backup*\...](./chapters/CHAPTER_07.md#75-sempre-é-bom-um-backup)

- [**8. Criando e gerenciando imagens**](./chapters/CHAPTER_08.md#8-criando-e-gerenciando-imagens)

	- [8.1. Agora eu quero criar minha imagem, posso?](./chapters/CHAPTER_08.md#81-agora-eu-quero-criar-minha-imagem-posso)

	- [8.2. Vamos começar do começo então, *dockerfile*!](./chapters/CHAPTER_08.md#82-vamos-começar-do-começo-então-dockerfile)

	- [8.3. 'Bora aprender um pouco mais sobre *dockerfile*?](./chapters/CHAPTER_08.md#83-vamos-aprender-um-pouco-mais-sobre-dockerfile)

	- [8.4. *Multi-stage*](./chapters/CHAPTER_08.md#84-multi-stage)

	- [8.5. Vamos customizar uma imagem base agora?](./chapters/CHAPTER_08.md#85-vamos-customizar-uma-imagem-base-agora)

- [**9. Compartilhando as imagens**](./chapters/CHAPTER_09.md#9-compartilhando-as-imagens)

	- [9.1. O que é o *Docker Hub*?](./chapters/CHAPTER_09.md#91-o-que-é-o-docker-hub)

	- [9.2. Vamos criar uma conta?](./chapters/CHAPTER_09.md#92-vamos-criar-uma-conta)

	- [9.3. Agora vamos compartilhar essas imagens na *interwebs*!](./chapters/CHAPTER_09.md#93-agora-vamos-compartilhar-essas-imagens-na-interwebs)

	- [9.4. Não confio na internet; posso criar o meu *registry* local?](./chapters/CHAPTER_09.md#94-não-confio-na-internet-posso-criar-o-meu-registry-local)

- [**10. Gerenciando a rede dos *containers***](./chapters/CHAPTER_10.md#10-gerenciando-a-rede-dos-containers)

	- [10.1. Consigo fazer com que a porta do *container* responda na porta do *host*?](./chapters/CHAPTER_10.md#101-consigo-fazer-com-que-a-porta-do-container-responda-na-porta-do-host)

	- [10.2. E como ele faz isso? Mágica?](./chapters/CHAPTER_10.md#102-e-como-ele-faz-isso-mágica)

- [**11. Controlando o *daemon* do Docker**](./chapters/CHAPTER_11.md#11-controlando-o-daemon-do-docker)

	- [11.1. O Docker sempre utiliza 172.16.X.X ou posso configurar outro intervalo de IP?](./chapters/CHAPTER_11.md#111-o-docker-sempre-utiliza-17216xx-ou-posso-configurar-outro-intervalo-de-ip)

	- [11.2. Opções de *sockets*](./chapters/CHAPTER_11.md#112-opções-de-sockets)

		- [11.2.1. *Unix Domain Socket*](./chapters/CHAPTER_11.md#1121-unix-domain-socket)

		- [11.2.2. TCP](./chapters/CHAPTER_11.md#1122-tcp)

	- [11.3. Opções de *storage*](./chapters/CHAPTER_11.md#113-opções-de-storage)

	- [11.4. Opções de rede](./chapters/CHAPTER_11.md#114-opções-de-rede)

	- [11.5. Opções diversas](./chapters/CHAPTER_11.md#115-opções-diversas)

- [**12. Docker Machine**](./chapters/CHAPTER_12.md#12-docker-machine)

	- [12.1. Ouvi dizer que minha vida ficaria melhor com o Docker Machine!](./chapters/CHAPTER_12.md#121-ouvi-dizer-que-minha-vida-ficaria-melhor-com-o-docker-machine)

		- [12.1.1. Vamos instalar?](./chapters/CHAPTER_12.md#1211-vamos-instalar)

		- [12.1.2. Vamos iniciar nosso primeiro projeto?](./chapters/CHAPTER_12.md#1212-vamos-iniciar-nosso-primeiro-projeto)

- [**13. Docker Swarm**](./chapters/CHAPTER_13.md#13-docker-swarm)

	- [13.1. Criando o nosso *cluster*!](./chapters/CHAPTER_13.md#131-criando-o-nosso-cluster)

	- [13.2. O sensacional services!](./chapters/CHAPTER_13.md#132-o-sensacional-services)

- [**14. Docker Secrets**](./chapters/CHAPTER_14.md#14-docker-secrets)

	- [14.1. O comando *docker secret*](./chapters/CHAPTER_14.md#141-o-comando-docker-secret)

	- [14.2. Tudo bem, mas como uso isso?](./chapters/CHAPTER_14.md#142-tudo-bem-mas-como-uso-isso)

	- [14.3. Acessando a *secret*](./chapters/CHAPTER_14.md#143-acessando-a-secret)

	- [14.4. Atualizando a *secret* de um serviço](./chapters/CHAPTER_14.md#144-atualizando-a-secret-de-um-serviço)

- [**15. Docker Compose**](./chapters/CHAPTER_15.md#15-docker-compose)

	- [15.1. O comando docker stack](./chapters/CHAPTER_15.md#151-o-comando-docker-stack)

	- [15.2. E já acabou? :(](./chapters/CHAPTER_15.md#152-e-já-acabou-)

# Introdução

Se você é um desenvolvedor que sempre sonhou em poder reproduzir o
ambiente de produção em sua máquina ou a aplicação que você desenhou e
desenvolveu poder rodar em produção facilmente, com o mesmo "binário"
que foi utilizado nos momentos de testes\...

\...Imagine conseguir instalar e testar diversas aplicações rapidamente
e ainda simular um ambiente completo, bem mais leve e inteligente do que
as máquinas virtuais em questão de minutos.

Se você é um administrador de sistemas que sempre sonhou em não precisar
mais preparar os servidores para suportar milhares de aplicações
diferentes, feitas em linguagens diferentes, com bibliotecas diferentes,
com versões diferentes (ufa!)...

\...Imagine economizar milhões de horas em instalações de novos
servidores, atualizações de aplicações, *deploys*, acionamentos na
madrugada por conta de serviços que "falharam" e não poderiam ser
executados em outros servidores por incompatibilidades diversas.

Se você precisa escalar seu ambiente em questão de minutos para suportar
uma demanda que o pegou de surpresa após uma campanha do departamento de
marketing de sua empresa\...

Se você é um gestor e está preocupado com a fatura do *datacenter* no
final do mês por conta da quantidade de recursos necessários para seu
ambiente funcionar\....

E mesmo quando você possui todos os recursos computacionais para
suportar a demanda, se você percebe que na maioria das vezes os seus
recursos estão ociosos e sua equipe está com dificuldades para
equalizá-los\...

\...Então este livro e o Docker foram feitos para você! Boa leitura e
divirta-se!
