---
title: Introdução
---

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
