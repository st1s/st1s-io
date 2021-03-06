---
layout: post
title:  "Gerando uma aplicação angular.js em 3min"
subtitle: "Com publicação no Heroku e Github Pages"
date:   2016-08-22
category: angularjs
tags: [angularjs, heroku, appfy]
image: https://i.imgur.com/8yh0COR.png
comments: true
description: Mais rápido que fazer miojo, hoje vou falar um pouco sobre como iniciar uma aplicação web javascript feita em angular.js, com uma boa estrutura, escalável e pronta para um ciclo rápido de desenvolvimento e publicação..
---

Mais rápido que miojo. Hoje vou falar um pouco sobre como iniciar uma aplicação web javascript feita em angular.js, com uma boa estrutura, escalável, e pronta para um ciclo rápido de desenvolvimento e publicação.

## Requerimentos

- Node 14+ e NPM (recomendo utilizar [Node Version Manager](https://www.google.com.br/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=node%20version%20manager))
- [Bower](https://bower.io/)
- [Heroku Toolbelt](https://devcenter.heroku.com/articles/heroku-command-line)
- [Appfy](https://appfy.org/)

<img src="https://i.imgur.com/8yh0COR.png" title="Criando uma aplicação angular.js em 3min" style="max-width: 175px">



Iremos trabalhar com o [Appfy](https://appfy.org) como ferramenta para auxiliar em algumas tarefas, [Heroku](https://heroku.com) e [Github Pages](https://pages.github.com/) para servir o resultado final.
Ai você me pergunta: qual solução de hospedagem eu devo escolher? Minha resposta é: depende. O Github Pages hospeda apenas arquivos estáticos, logo se você está iniciando algo simples que não vai depender de recursos que exijam processamento, recomendo fortemente que utilize o GH Pages. Você não terá dor de cabeça, o uptime é de 99% e seus arquivos serão entregues rapidamente em qualquer parte do planeta, pois serão distribuídos pela própria infra do Github.

> "Ah, mas ai todo mundo vai ver meu código"

Amigo se você quer algo de qualidade e privado, pague por isto. Eu pago e acho um dinheiro muito bem investido. Mesmo com repositórios privados, você poderá servir tranquilamente suas aplicações.

<img src="https://i.imgur.com/7FJn7ao.gif" />


#### Posso utilizar servidor compartilhado?
Nunca. Não recomendo o uso de servidores compartilhados para nada. Como o próprio nome já diz, servidores compartilhados dividem recursos com centenas, às vezes com milhares de outros sites e aplicações. Muitas vezes, por ser leigo no assunto, o cliente não entende as complicações de utilizar este tipo de serviço. Mas existem alternativas para esta situação, que é o que proponho neste post. Nada justifica você vender filé e entregar carne de terceira para o teu cliente. É isso que ele vai pensar quando te ligar para reclamar que o site dele tá fora do ar.

#### Para que devo usar essa empresa com nome esquisito então?

Você deverá utilizar o Heroku, Digital Ocean, AWS, entre outros, quando sentir que precisa de processamento (RESTful API, envio de email, banco de dados, etc..). Neste caso vamos utilizar o Heroku por dois motivos: porque eu gosto e porque até o momento o Appfy só tem implementado [deploy](https://docs.appfy.org/CLI-Deploy/) nele. Quando precisar de utilizar um Prerender por exemplo, o Heroku será um ótimo parceiro =)


> Eu curto trabalhar com a Heroku por algumas razões:

1. Possui um excelente nível gratuito de serviço
2. Recursos práticos/fáceis de entender e usar
3. Aumento de recurso on demand sem downtime
4. Preço muitíssimo acessível quando estiver, realmente, em produção (a partir de 7 obamas)

Se estiver interessando... então vem comigo:


## Criando a aplicação cliente

Para iniciarmos você precisa ter a interface de linha de comando do Appfy instalada. [Aqui ensina](https://docs.appfy.org/) como fazer. Mas se estiver "de mal" com seu inglês você só vai precisar de um:

```
$ npm install appfy-cli
```

Após a instalação rode o comando `appfy --version` apenas para ver se está tudo certo para, então, prosseguirmos.

```
$ appfy new bacon
```

Este comando vai te perguntar algumas coisas básicas sobre sua app e depois vai gerar o cliente da aplicação `bacon`. Após a instalação dos pacotes, você terá uma estrutura parecida com isso:

```
├── README.md
├── gulpfile.js                               
├── package.json                                 <- dependencias de desenvolvimento                             
├── client
|   ├── bower.json                               <- dependencias do cliente          
│   └── src                                      <- código fonte do cliente
│       ├── app
│       │   └── modules                      
│       │   │   ├── home
│       │   │   │   ├── home.config.js
│       │   │   │   ├── home.component.js
│       │   │   │   ├── home.spec.js
│       │   │   │   ├── home.html
│       │   │   │   ├── home.css
│       │   └── themes         
│       │   │   ├── default
│       │   │   │   ├── theme.html
│       │   ├── app.config.js                    <- config phase do angular
│       │   ├── app.run.js                       <- run phase do angular
│       │   ├── app.module.js                    <- carregamento de modulos terceiros
│       |   └── appfy.module.js                  <- carregamento de modulos local
│       ├── assets
│       └── index.html                           <- template principal
├── appfy.json                                   <- arquivo de configurações do Appfy
└── .gitignore                                  
```

Para quem está começando, esta estrutura pode parecer meio confusa, mas é o melhor que podemos fazer para crescermos de maneira fácil e sem muitos problemas.

Se estiver curioso sobre quais pacotes npm e bower o Appfy vai instalar, você pode conferir o [repositório oficial aqui](https://github.com/Appfy/appfy).
Ele instalará alguns pacotes básicos que são repetitivos, como `ui.router` para trabalhar com rotas no angular, `ngstorage` e etc... Você pode controlar as dependências no arquivo `bower.json` da pasta `client`.

Uma coisa legal que já vem também, é o auto-reload do browser em ambiente de desenvolvimento. Com isso você não vai precisar mais ficar apertando f5 toda vez que alterar alguma coisa no código fonte =)

```
$ cd bacon
$ appfy serve
```

Após a instalação, navegue até a pasta do projeto `bacon` e execute o comando `serve` para degustar. Agora falta iniciarmos o deploy.

## Setando configurações no Appfy

Como nosso miojo está quase ficando pronto, vamos ao que interessa:

### Configurando o Heroku

O Heroku também funciona com um conceito de `Apps`, então [crie](https://dashboard.heroku.com) sua nova aplicação e guarde o nome que vai utilizar lá para setar no aquivo `appfy.json`. Para maior lembrança e padronização, tente usar nomes curtos e no formato snake-case.
> Você terá que executar `heroku login` para que não tenha problemas mais a frente.

Este comando é necessário para executar ações na sua conta do Heroku, ele também armazena suas credenciais de acesso para que não tenha que ficar fazendo login toda hora.

Ficaria algo parecido com isso:

```
    "deployment": {
        "heroku": {
            "client": "bacon-client"
        }
    }
```

### Configurando o Github Pages

Este é mais fácil ainda: basta criar o [repositório lá](https://github.com/new), localizar a key `github` e setar no formato `user/repo` em `deployment/github/client`. 

Recomendo a instalação do [Git Credentials](https://www.google.com.br/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=microsoft%20git%20credentials) para que não tenha que ficar digitando suas credenciais de acesso toda vez que for manipular seus repositórios.

> Atualmente aplicações geradas pelo Appfy não suportam subdiretórios, então você terá de [configurar um domínio ou um subdomínio no Github](https://www.google.com.br/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=configurar+um+dom%C3%ADnio+ou+um+subdom%C3%ADnio+no+Github)

Caso ainda não esteja sendo utilizado, você pode publicar em seu dominio principal de usuario do Github, criando um repositorio no formato padrão. Ex: O usuário obama que cria um repositório de nome obama.github.io consegue acessar pela url obama.github.io - incrível não? O.o

Ficaria algo assim:

```
    "deployment": {
        "github": {
            "client": "obama/bacon-client"
        }
    }
```

Agora é só correr pro abraço

```
$ appfy build client --heroku
$ appfy build client --github
```

Vou ali comer meu miojo de bacon, flw.

-


- [demo no heroku](https://bacon-client.herokuapp.com)
- [demo no github](https://bacon-client.st1s.io)
- [código fonte deste post](https://github.com/st1s/bacon-client)