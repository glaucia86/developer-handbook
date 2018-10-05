# Documentação de arquitetura

<!-- TOC -->

- [Documentação de arquitetura](#documentação-de-arquitetura)
  - [Sobre a arquitetura](#sobre-a-arquitetura)
    - [Estrutura de pastas](#estrutura-de-pastas)
    - [Camada de apresentação](#camada-de-apresentação)
    - [Camada de Serviço](#camada-de-serviço)
    - [Camada de dados](#camada-de-dados)
      - [Conexões](#conexões)
      - [Repositórios](#repositórios)
    - [Coluna de domínio](#coluna-de-domínio)
    - [Coluna de utilidades](#coluna-de-utilidades)

<!-- /TOC -->

## Sobre a arquitetura

Estamos utilizando o modelo baseado em *Domain Driven Design*, com ou sem [Event Sourcing](https://martinfowler.com/eaaDev/EventSourcing.html).

Este modelo se baseia em 3 camadas base que se comunicam de forma *top-down*, ou seja, a camada mais superior irá interagir com a mais inferior, mas nunca o contrário.

Todo o cenário é baseado na imagem a seguir:

![](./images/NXCD-Architecture.jpeg)

### Estrutura de pastas

A estrutura de pastas seguirá o modelo abaixo, mas você pode checar em tempo real acessando o [diretório de exemplo](./folder-structure):

```
project-x
    |   .dockerignore
    |   .editorconfig
    |   .envrc
    |   .envrc-sample
    |   .gitignore
    |   app-config.ts
    |   ci-config.yaml
    |   Dockerfile
    |   package.json
    |
    +---.git
    \---src
        |   index.ts
        |
        +---data
        |   +---connections
        |   |       mongo.ts
        |   |       sql-server.ts
        |   |
        |   \---repositories
        |           entity-x.ts
        |           entity-y.ts
        |
        +---domains
        |   |   domain-base-error.ts
        |   |   index.ts
        |   |
        |   +---entity-x
        |   |   |   entity.ts
        |   |   |   initial.state.ts
        |   |   |
        |   |   +---commands
        |   |   |       create.ts
        |   |   |       delete.ts
        |   |   |       update.ts
        |   |   |
        |   |   +---errors
        |   |   |       entity-x-error.ts
        |   |   |
        |   |   +---events
        |   |   |       entity-x-was-created.ts
        |   |   |       entity-x-was-deleted.ts
        |   |   |       entity-x-was-finished.ts
        |   |   |       entity-x-was-updated.ts
        |   |   |
        |   |   \---interfaces
        |   |           data.ts
        |   |           filter.ts
        |   |
        |   \---entity-y
        |       +---commands
        |       +---errors
        |       +---events
        |       \---interfaces
        +---libs
        |       cpf-validator.ts
        |       helper-abc.ts
        |
        +---presentation
        |   |   app.ts
        |   |   server.ts
        |   |
        |   \---routes
        |           route-a.ts
        |           route-b.ts
        |
        \---services
                entity-x.ts
                entity-y.ts
```

- `project-x`: É a pasta inicial que vai conter o nome do projeto, tudo estará dentro desta pasta
  - **Arquivos de estrutura**: Abaixo da raiz, no primeiro nível, vamos ter os dotfiles necessários para configuração de CI/CD, Docker e outras informações; aqui será aonde vamos ter o `package.json` e o `tsconfig.json`
  - `src`: Pasta que conterá todo o código fonte da aplicação
    - `index.ts`: É o *entrypoint* da aplicação, será nele que a aplicação vai começar
    - `data`: É a camada de dados da aplicação, conforme especificada [em sua seção](#camada-de-dados)
      - `connections`: Aqui ficarão todos os arquivos referentes a conexões com bancos de dados e outras fontes
      - `repositories`: Aqui ficarão os manipuladores dos clientes de fontes de dados existentes em `connections`, esta será a pasta que conterá os arquivos que serão consumidos pela [camada de serviços](#camada-de-serviço)
    - `domains`: Será a coluna de domínio, conforme especificada [em sua seção](#coluna-de-dominio)
      - `index.ts`: Retornará um objeto com todas as entidades
      - `domain-base.error.ts`: O erro base do domínio que todas as entidades devem estender
      - `<entity-x>`: Cada pasta dentro da camada de domínio terá o nome de uma entidade deste domínio
        - `entity.ts`: Toda entidade terá um arquivo `entity.ts` que será o arquivo que juntará todos os comandos, eventos e erros. Em teoria, será o local aonde as demais camadas irão buscar a instância do domínio em si
        - `initial.state.ts`: Toda entitade tem um estado inicial que será colocado neste arquivo
        - `commands`: Comandos são as instruções necessárias para se manipular esta entidade, aqui ficarão os métodos e funções que retornam os arrays de eventos (no caso de event-sourcing) necessários para criar tal entidade
        - `errors`: Aonde ficarão as classes de erro da entidade, todas devem estender o arquivo base de erro
        - `events`: Somente utilizado no caso de event-sourcing, será aonde estarão armazenados as ações que podem ser executadas sobre esta entidade em forma de eventos, todos os arquivos devem ter o nome `<nome-da-entidade>-descrição-do-evento.ts`
        - `interfaces`: Aqui ficarão as interfaces do TypeScript para tal entidade, não só as interfaces mas também, se necessários, Enums e qualquer outra estrutura de definição de tipos
    - `libs`: Aqui ficarão os arquivos de bibliotecas, helpers e qualquer outro arquivo que sirva de repositório de utilidades
    - `presentation`: A camada de apresentação ficará descrita aqui, conforme [descrição de sua seção](#camada-de-apresentação)
      - `app.ts`: Neste arquivo serão carregadas todas as rotas da aplicação, exportará o modelo base de um webserver
      - `server.ts`: Irá receber `app.ts` e irá iniciar um servidor web
      - `routes`: Aqui ficarão os handlers das rotas, toda a rota deverá ter o nome `<verbo>-descrição.ts`
    - `services`: Camada de serviços conforme [descrição](#camada-de-serviço)
      - `<entity-x>`: Para cada entidade em `domains` temos que ter um arquivo aqui, que será o responsável por se comunicar com a camada de dados e manipular os dados no banco seguindo o comando que for executado pelo domínio

### Camada de apresentação

A camada de apresentação será responsável

### Camada de Serviço

### Camada de dados

#### Conexões

#### Repositórios

### Coluna de domínio

### Coluna de utilidades