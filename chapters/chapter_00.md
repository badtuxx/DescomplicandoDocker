---
title: Introdução
---

# Prefácio

Se você é um desenvolvedor, você provavelmente já ouviu falar sobre Docker.
Docker é a plataforma ideal para executar aplicações em containers. Certo.
mas o que são esses containers? Containers são uma técnica de virtualização
eficiente e que provém muitas possibilidades:
Graças a eles, nós podemos entregar aplicações mais rapidamente;
- Podemos facilmente implementar CI/CD (Integração Continua e entrega continua);
- Podemos configurar variáveis de ambiente mais rápido que nunca;
- Podemos garantir consistência entre o nosso ambiente de desenvolvimento e a produção;
- E muito mais.

Se você escreve código, se você implanta código ou opera código, então eu
prometo que containers irão fazer sua vida muito mais fácil.

Porém containers estão há *décadas* por aí, e o Docker só foi lançado em 2013.
Então o que aconteceu? Por que todo mundo está tão empolgado com o Docker,
se a tecnologia por trás dele é de mais de dez anos atrás?

**Porque de a maior inovação do Docker não é tecnológica.**
A principal inovação do Docker é tornar essa tecnologia acessível a todos os 
desenvolvedores e administradores de sistema, de forma fácil, sem a necessidade de anos 
de prática para atingir a proficiência e sem a obrigação de serem desenvolvidas várias ferramentas personalizadas.

Vou lhe contar um segredo: o mais importante não é o Docker ou os containers.
O mais importante é elevar a qualidade das nossas aplicações. Reduzir os custos de desenvolvimento e implantação, 
entregar código melhor, mais confiável e mais rápido.
E acontece que o Docker é uma forma extremamente eficiente para atingir esses objetivos.
E esse é o motivo de o Docker ser tão popular.

Esse livro irá te ensinar tudo que você precisa para começar a utilizar o Docker e utilizá-lo
para criar, entregar e executar suas aplicações.
Este será seu guia no mundo dos containers e mostrará o caminho para você entregar suas aplicações melhor que nunca antes

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
