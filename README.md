# cubic-server

## Versões dos programas utilizados

### docker

Docker version 20.10.18

### docker-compose

docker-compose version 1.24.1

### composer

Composer 2.0.9

### node

v16.16.0

## Explicações gerais

__*AVISO: serviços desse repositório foram extraídos do projeto <https://www.github.com/Laradock/laradock>*__

Este repositório corresponde ao repositório do servidor, onde vão estar apenas os arquivos referentes a configuração do servidor do ecossistema cubic.

Aqui você vai encontrar os arquivos de configuração dos containers:

- mysql
- nginx
- php-fpm
- php-worker
- redis
- workspace

## Fluxo de ações

__*AVISO: esse passo a passo foi feito baseado em linux.*__

Ao clonar esse repositório você terá a base para rodar todos os projetos do ecossistema cubic.

Uma vez clonado em sua máquina, os outros projetos devem ser clonados dentro dele para que haja qualquer comunicação entre sistemas (apis, fronts, logs, etc).

## Configurando sistema, serviços e ambiente para rodar os projetos

Complementanto o tópico anterior, apenas clonar os projetos não será o suficiente, é preciso executar uns passos ainda antes de se divertir.

### Sistema

Um dos requisitos para rodar os projetos é configurar hosts locais para as aplicações.

Para isso, rode o comando que irá inserir os hosts necessários para cada aplicação pelo terminal.

```shell
sudo printf "127.0.0.1\tcubic-laravel.teste\r\n127.0.0.1\tcubic-react.teste" > /etc/hosts
```

Para verificar o arquivo editado basta rodar o seguinte comando:

```shell
nano /etc/hosts
```

### Serviços

#### Nginx

Dentro de `nginx/sites` há diversos exemplos de configs que servem de base para montarmos nosso servidor. Entre na pasta.

Precisamos criar uma configuração para cada um dos hosts que criamos no passo anterior.

Rode os seguintes comandos para copiar os arquivos de configuração:

```shell
cp laravel.conf.example cubic-laravel.conf && cp react.conf.example cubic-react.conf
```

Feito isso, por enquanto nossas configurações estão finalizadas.

### Ambiente

#### .env

Volte para a pasta raiz do servidor e rode o seguinte comando para copiar o arquivo de ambiente:

```shell
cp .env.example .env
```

## Configuração de projetos dentro da pasta do servidor

Para rodar cada novo projeto do ecossistema é necessário que o mesmo seja clonado dentro da pasta do servidor, ou seja, uma vez clonado o repositório do servidor, na raíz do repositório do servidor deverão ser clonados os repositórios de interesse.

### cubic-laravel

Disponível em <https://www.github.com/roneicarlos/cubic-laravel>

#### Especificidades cubic-laravel

Nenhuma até o presente momento

### cubic-react

Disponível em <https://www.github.com/roneicarlos/cubic-react>

#### Especificidades cubic-react

Até o momento, apenas o código minificado está disponível ao acessar o host local <http://cubic-react.test>. Então, por enquanto, o fluxo de trabalho se define da seguinte forma:

- Entre na pasta do projeto cubic-react;

- Suba um servidor de desenvolvimento em abiente local usando o comando `node run start`. O servidor de acesso ao app com hot reload será exibido no terminal;

- Faça o que precisa fazer no projeto se utilizando do hot reload enquanto testa e executa o desenvolvimento;

- Ao terminar o desenvolvimento, suba o container `react` novamente rodando o comando `docker build react` a partir da pasta do repositório do servidor;

- Teste as modificações feitas com o código simplificado acessando o host local <http://cubic-react.test>;

É importante ressaltar esse fluxo pois até o momento não achamos solução para implementar o hot reload no host local, mas em breve esperamos que seja possível tal ação não sendo mais necessário utilizar o comando `node run start` para fazer os desenvolvimentos.

## Subindo os containers

Os containers na primeira vez devem ser montados a partir da raiz do repositório do servidor usando o comando

```shell
docker-compose build nginx mysql php-fpm workspace docker-in-docker
```

Após montar os containers, é necessário iniciar eles com o comando

```shell
docker-compose up -d nginx mysql php-fpm workspace docker-in-docker
```

Para parar os containers é só rodar o comando

```shell
docker-compose stop nginx mysql php-fpm workspace docker-in-docker
```

Há uma especificidade que existe por enquanto no app __cubic-react__ que ele é tratado como se fosse um container também. Então, quando for fazer o teste no host definido nos passos acima, é importante subir junto o container do app.

Isso pode ser feito adicionando o `react` no final dos comandos, ficando assim respectivamente para montar, iniciar e parar:

```shell
docker-compose build nginx mysql php-fpm workspace docker-in-docker react
```

```shell
docker-compose up -d nginx mysql php-fpm workspace docker-in-docker react
```

```shell
docker-compose stop nginx mysql php-fpm workspace docker-in-docker react
```
