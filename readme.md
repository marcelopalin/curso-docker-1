# 1. Curso de Docker

## 1.1. O que é o Docker?

É uma tecnologia de virtualização, mas não tradicional.
É uma engine de administração de **containers**, baseado no **Linux Containers**. De forma resumida, um container é um processo isolado do sistema, e a partir desse container eu consigo startar um serviço nesse container. Ele não é uma máquina virtual.

### 1.1.1. Definição Oficial

**_Containers Docker empacotam componentes de software em um sistema de arquivoscompleto, que contêm tudo necessário para a execução: código, runtime, ferramentas desistema - qualquer coisa que possa ser instalada em um servidor.Isto garante que o software sempre irá executar da mesma forma, independente do seuambiente._**

## 1.2. Por que não um VM?

1- O Docker tende a utilizar menos recursos que um VM tradicional.
2- Os processos rodam de forma mais rápida.

Porém nem sempre conseguiremos utilizar os Containers Docker, pois eles possuem algumas limitações em relação as VMs:

- **Todas as imagens são linux**, apesar do host poder ser qualuqer SO que use ou emule um kernel linux, as imagens em sí serão baseadas en linux.
- **Não é possível usar um kernel diferente do host**, o **Docker Engine** será executado sob uma determinada versão do kernel linux, e não é possível executar uma versão diferente, pois as imagens não possuem kernel.

## 1.3. O que são containers?

É a segregação de processos no mesmo kernel, de forma que o processo seja isolado o máximo possível de todo o resto do ambiente.

Em termos práticos são **File Systems**, criados a partir de uma "imagem" e que podem possuir também algumas características próprias.

Em resumo: **Um processo de arquivos segregados que tem a sua disposição um sistema de arquivos isolados da máquina host que tem como objetivo rodar sua aplicação e todas as dependências que ela precisa para funcionar.**

O ideal é que existe um container específico para cada demanda da aplicação. Exemplo: Um container para o backend, outro para o banco de dados, outro para o frontend, etc...

**chroot** é um comando que você redefine a pasta raíz para aprisionar um novo processo, limitando a execução dele para aquela pasta/subpastas.

O container é algo entre um **chroot** e uma VM.

## 1.4. O que são imagens Docker?

É um modelo de sistema de arquivos somente-leitura usado para criar containers. Essas imagens são criadas através de um processo chamado **build**.
A imagem é composta por camadas (layers), que são as mudanças que eu faço na imagem. Cada alteração gera uma layer e o conjunto de layers é a imagem.
Essa imagem é representada por um ou mais arquivos e pode ser armazenada em um repositório.

### 1.4.1. Docker File System

O Docker utiliza file systems especiais para otimizar o uso, transferência e armazenamento das imagens, containers e volumes.

O principal é o **AUFS**, que armazena os dados em camadas sobrepostas, e somente a camada mais recente é gravável.

## 1.5. Uso básico do Docker

### 1.5.1. Hello World, meu Docker funciona!

```terminal
docker container run hello-world
```

O comando run agrupa vários comando em sí. A partir da sua execução ele:

1. Faz o download automático das imagens não encontradas
   ```
   docker image pull
   ```
2. Cria o container
   ```
   docker container create
   ```
3. Executa o container
   ```
   docker container start
   ```
4. Uso do modo interativo
   ```
   docker container exec
   ```

### 1.5.2. Mudanças da versão 1.13

A partir da versão 1.13, o Docker reestruturou toda a interface da linha de comando, para agrupar melhor os comando por contexto.

Apesar dos comandos antigos continuarem válidos, o conselho geral é adotar a nova sintaxe.

Até a versão 17.03, ainda é possível utilizar a sintaxe antiga, porém precisamos pensar nela como atalhos.

```
docker pull
docker image pull

docker create
docker container create

docker start
docker container start

docker exec
docker container exec
```

## 1.6. Modo interativo - (19 - Ferramentas Diferentes)

Para executar um container podemos utilizar o modo Daemon e o modo interativo.

O modo interativo é extremamente útil para processos experimentais, estudo dinâmico de ferramentas e de desenvolvimento.

Exemplo: rodando de forma iterativa o Debian e mostrando a versão do bash dele é diferente do seu Bash no linux.

Para verificarmos a versão do bash faça: 
bash --version

```
bash --version
GNU bash, versão 5.0.17(1)-release (x86_64-pc-linux-gnu)
Copyright (C) 2019 Free Software Foundation, Inc.
Licença GPLv3+: GNU GPL versão 3 ou posterior <http://gnu.org/licenses/gpl.html>

Este é um programa gratuito; pode alterá-lo e distribuí-lo à vontade.
Não há QUALQUER GARANTIA, até aos limites previstos pela Lei.
```

Se fizermos:

```
docker run debian bash --version
```

Na primeira vez que executamos ele baixa a imagem (pull), cria o contâiner (create), 
inicializa o contâiner (start) e executa (exec) no modo iterativo.

```
docker run debian bash --version
Unable to find image 'debian:latest' locally
latest: Pulling from library/debian
b9a857cbf04d: Pull complete 
Digest: sha256:b16f66714660c4b3ea14d273ad8c35079b81b35d65d1e206072d226c7ff78299
Status: Downloaded newer image for debian:latest
GNU bash, version 5.0.3(1)-release (x86_64-pc-linux-gnu)
Copyright (C) 2019 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>

This is free software; you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
```

Rodando pela 2a vez é mais rápido:

```
~/github.com/curso-docker-1$ docker run debian bash --version
GNU bash, version 5.0.3(1)-release (x86_64-pc-linux-gnu)
Copyright (C) 2019 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>

This is free software; you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
```

```
docker container ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

Porém, com a flag -a listaremos todos os contâineres criados

```
docker container ps -a
CONTAINER ID   IMAGE         COMMAND            CREATED          STATUS                      PORTS     NAMES
5ed0ba915e8b   debian        "bash --version"   5 minutes ago    Exited (0) 5 minutes ago              modest_blackwell
b13ab9c9401b   debian        "bash --version"   10 minutes ago   Exited (0) 10 minutes ago             boring_meitner
3cd943a539f5   hello-world   "/hello"           3 hours ago      Exited (0) 3 hours ago                peaceful_booth
e959f97d6feb   hello-world   "/hello"           3 hours ago      Exited (0) 3 hours ago                agitated_stonebraker
```

> O método run sempre **cria um novo** contâiner!

Vamos rodar o comando run no modo iterativo:

```
docker container run -it debian bash
```

Permite você fazer testes no debian, 

### 1.6.1. Criando um container em modo interativo

Como o método run sempre cria um contâiner, para reutilizarmos vamos colocar
um <nomeContainer>

```
docker container run --name  -it imageName
```

### 1.6.2. Acessando container

```
docker container start -ai containerName
```

Resumo

1. O container criado tem ferramentas diferentes das instaladas no seu computador
2. O comando docker container run sempre cria novos containers
3. Os containers **devem ter nomes únicos**
4. É possível reutilizar containers

## 1.7. Cego, surdo e mudo, só que não!

Um container normalmente roda com o máximo de isolamento possível do host, este isolamento é possível através do **Docker Engine** e diversas características provídas pelo kernel.
Entretanto não é interessante obter um isolamento total do seu host, e sim um isolamento controlado, em que o container tenha acesso a recursos do host que são explicitamente indicados.

Os principais recursos de controle de isolamento

- Mapeamento de portas
- Mapeamento de volumes
- Cópia de arquivpos para o contianer ou partindo do container
- Comunicação entre os containers.

## 1.8. Mapeamento de portas

É possível mapear portas **TCP** e **UDP** diretamente para o host, permitindo acesso através de toda a rede, não necessitando ser a mesma porta do container. O método mais comum para este fim é o parâmetro -p no comando `docker contianer run`, a flag `-p` recebe um parâmetro composto por dois números separados por `:`. O primeiro é o a porta no host e o segundo no container.

Exemplo:

```bash
docker container run -p 8080:80 nginx
acompanhar logs de acesso
exemplo: 172.17.0.1 - - [09/Apr/2017:19:28:48 +0000] "GET / HTTP/1.1" 304 0 "-""Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko)Chrome/57.0.2987.133 Safari/537.36" "-"
CTRL-C para sair
```

Neste caso estamos exportando a porta 8080 e mapeando ela para acessar a porta 80 do contâiner.

Você pode testar usando:

curl http://localhost:8080

ou acesse o browser http://localhost:8080


## 1.9. Mapeamento de diretórios para o container

É possível mapear diretórios e volumes no host para diretório no container. No momento vamos focar em um mapeamento mais simples, um diretório no host para um diretório no container. O método mais comum para este fim é o parâmetro `-v`no comando `docker container run`. O `-v` recebe um parâmetro que geralmente é comporto por dois caminhos absolutos separados por `:`. Assim como diversos outros parâmetros, o primeiro é o diretório no host e o segundo no container.

Exemplo

```bash
docker container run -p 8080:80 -v $(pwd)/html:/usr/share/nginx/html nginx
```

## 1.10. Modo daemon

O modo daemon é onde o container executa em background. Para realizar isso é simples, para utilizar o parâmetro `-d` do `docker container run`, indicando ao Docker para iniciar o container em background (modo daemon).

Exemplo:

```bash
docker container run -d --name ex-daemon-basic -p 8080:80 -v  $(pwd)/html:/usr/share/nginx/html nginx
```

Podemos para o container com o comando

```bash
docker container stop ex-daemon-basic
```

Podemos executar um container criado com o comando

```bash
docker container start ex-daemon-basic
```

Podemos reiniciar o container com o comando

```zsh
docker container restart ex-daemon-basic
```

## 1.11. Deixando de ser apenas um usuário

### 1.11.1. Diferenças entre containers e imagens

Utilizando uma análogia de Orientação a Objetos:

Imagem:
É o equivalenta a classe

Container:
É o equivalente ao objeto

Ou seja, a partir da imagem eu consigo montar n containers.
A imagem é um modelo de sistema de arquivos somente leitura em formato de camadas, que será utilizado pelo container. E o container é o processo segredado, que pode derivar muitos sub-processos, através do uso da imagem.
O container cria outras camadas de acordo com seu modo de uso.
O container é o processo e a imagem é o modelo de arquivos para criação de containers.

### 1.11.2. Entendendo melhor as imagens

Toda imagem (bem como os containers) possuem um identificador único em formato hash usando `sha256`. Porém seu uso não é muito prático, então para simplificar isto o docker utiliza uma tag para identificar imagens. A tag normalmente é formada por um nome, seguido de : dois pontos e depois uma versão.  
É extremamente comum utilizar uma versão chamada latest para representar a versão mais atual.

Exemplos de tags de imagens:

```
nginx:latest
redis:3.2
redis:3
postgres:9.5
```

Na prática uma tag é apenas um ponteiro para o hash da imagem, e várias tags podem apontar para o mesmo hash. Com isto é comum o uso de alguns apelidos nas tags, tomando como exemplo as imagens oficiais do redis. Existem 10 imagense 30 tags.

Para mudar a tags de uma imagem podemos utilizar o comando:

```dockerfile
docker image tag redis:lastest cod3r-redis
```

### 1.11.3. Comandos básicos no gerenciamento de imagens

1. `docker image pull <tag>`
   Baixa a imagem solicitada. Esse comando pode ser executado implicitamente, quando o docker precisa de uma imagem para outra operação e não consegue localiza-la no cache local.
2. `docker image ls`
   Lista todas as imagens já baixadas.
3. `docker image rm <tag>`
   Remove uma ou várias imagens do cache local.
4. `docker inspect <tag>`
   Extrai diversas informações utilizando um formato **JSON** da imagem indicada
5. `docker image build -t <tag>`
   Permit a criação de uma nova imagem, como veremos melhor em build.
6. `docker image push <tag>`
   Permite o envio de uma imagem ou tag local para um registry

### 1.11.4. Docker Hub x Docker Registry

**Docker Registry** é uma aplicação server side para guardar e distribuir imagens Docker.

**Docker Hub** é um serviço de registro de imagens Docker em nuvem, que permite a associação com repositórios para build automatizado de imagens. Imagens marcadas como **oficiais** no Docker Hub são criadas pela própria **Docker Inc**. E o código fonte por ser encontrado [aqui](https://github.com/docker-library).

Dica: A cli possui o comando `docker search <tag>` para procurar imagens do Docker Hub.

## 1.12. Construção de uma imagem

Processo para gerar uma nova imagem a partir de um arquivo de instruções.

O comando `docker build` é responsável por ler um arquivo `Dockerfile` e produzir uma nova imagem Docker.

**Dockerfile:**

Nome default para o arquivo com instruções para o build de imagens Docker. [Documentação](https://docs.docker.com/engine/reference/builder) do Dockerfile`

Exemplo:

```docker
// Arquivo Dockerfile
FROM nginx:latest
RUN echo '<h1>Hello World!</h1>' > usr/share/nginx/html/index.html
```

Rodar no terminal
```bash
docker image build -t ex-simple-build .
docker image ls
docker container run -p 80:80 ex-simple-build
# Serviço disponível em http://localhost
# CTRL + C para sair
```

**Dica** 
O comando __build__ exige a informação do diretório aonde o __build__ será executado, bem como onde o arquivo de instruções se encontra.

### 1.12.1. Instruções para a preparação de imagem

`FROM`
   Especifica a imagem base a ser utilizada pela nova imagem

`LABEL`
   Especifica vários metadados para a imagem como o mantenedor. A especificação do mantenedor era feita usando a instrução específica, `MAINTAINER` que foi substituída pelo `LABEL`

`ENV`
   Especifica variáveis de ambiente a serem utilizadas durante o build

`ARG`
   Define argumentos que poderão ser informados ao `build` através do parâmetro `--build-arg`;


Dockerfile

```dockerfile
FROM debian
LABEL maintainer 'Vyctor Guimaraes <vyctorguimaraes@gmail.com'
ARG S3_BUCKET=files
ENV S3_BUCKET=${S3_BUCKET}
```

No terminal
```bash
docker image build -t ex-build-arg .
docker container run ex-build-arg bash -c 'echo $S3_BUCKET' 
# Saída esperada: files
```


### 1.12.2. Instruções para povoamento da imagem

`COPY`
   Copia arquivos e diretórios para dentro da imagem

`ADD`
   Similar ao anterior, mas com suporte extendido a `URLs`. Somente deve ser usado em casos que a instrução `COPY` não atenda.

`RUN`
   Executa ações/comandos durante o build dentro da imagen.

```dockerfile
FROM nginx:1.13
LABEL maintainer 'Vyctor Guimarães <vyctorguimaraes@gmail.com>'

RUN echo '<h1> Sem conteúdo </h1>' > /usr/share/nginx/html/conteudo.html

COPY *.html /usr/share/nginx/html/
```

index.html
```html
<a href="conteudo.html"> Conteúdo do site </a>
```

run.sh
```shell
docker image build -t ex-build-copy .
docker container run -p 80:80 ex-build-copy
# Serviço disponível em http://localhost
```

**Dica**
Para entendermos melhor é necesśario executá-lo uma primeira vez e navegar na porta 80. E depois copiar o arquivo `exemplo-conteudo.html` para `conteudo.html`, executá-lo novamente e verificar o resultado no browser.

## 1.13. Instruções com configuração para execução do container

`EXPOSE`
   Informa ao Docker que a imagem expõe determinadas portas remapeadas no container. A exposição da porta não é obrigatória a partir do uso do recurso de redes internas do Docker. Porém a exposição ajuda a document e permite mapear rapidamente através do parâmetro `-P` do `docker container run`

`WORKDIR`
   Indica o diretório em que o processo principal será executado.

`ENTRYPOINT`
   Especifica o procesos inicial do container

`CMD`
   Indica parâmetros para o `ENTRYPOINT`

`USER`
   Especifica qual o usuário que será usado para execução do processo no container `(ENTRYPOINT e CMD)` e instruções `RUN` durante o build.

`VOLUME`
   Instrui a execução do container a criar um volume para um diretório indicado e copia todo conteúdo do diretório na imagem para o volume criado. Isso simplificará no futuro, processos de compartilhamento destes dados para backup, por exemplo.

Exemplo:

Dockerfile
```dockerfile
FROM python:3.6
LABEL maintainer 'Vyctor <vyctorguimaraes@gmail.com>'

RUN useradd www && \
  mkdir /app && \
  mkdir /log && \
  chown www /log

USER www
VOLUME /log
WORKDIR /app
EXPOSE 8000

ENTRYPOINT  ["/usr/local/bin/python"]
CMD ["run.py"]
```

index.html
```html
<p>Hello from python</p>
```

terminal
```shell
docker build -t ex-build-dev .
docker run -it -v ${pwd}:/app -p 80:8000 ex-build-dev
# Serviço disponível em http://localhost
```

run.py
```python
import logging
import http.server
import socketserver
import getpass

class MyHTTPHandler(http.server.SimpleHTTPRequestHandler):
  def log_message(self, format, *args):
    logging.info("%s - - [%s] %s\n"% (
      self.client_address[0],
      self.log_date_time_string(),
      format%args
    ))

logging.basicConfig(
  filename='/log/http-server.log',
  format='%(asctime)s - %(levelname)s - %(message)s',
  level=logging.INFO
)
logging.getLogger().addHandler(logging.StreamHandler())
logging.info('inicializando...')
PORT = 8000

httpd = socketserver.TCPServer(("", PORT), MyHTTPHandler)
logging.info('escutando a porta: %s', PORT)
logging.info('usuario: %s', getpass.getuser())
httpd.serve_forever()
```

## 1.14. Tipos de rede de Containers Docker

### 1.14.1. None Network
É um container que não tem nenhum tipo de rede. Ele é isolado e não tem acesso ao mundo exterior, nem a outros containers. É o mais adequado para aplicações que não tem nenhum acesso a rede.

> docker container run -d --net none debian

### 1.14.2. Bridge Network

É o modo de rede padrão na criação do container.

### 1.14.3. Host Network
Nesse modo o container vai usar diretamente a interface de rede do seu host. O nível de proteção é bem menor que no modo Bridge, porém há ganho na velocidade de acesso aos recursos de rede.


## 1.15. Coordenando múltiplos containers

É interessante que para cada elemento que componha sua aplicação seja disponibilizado cada um deles em um container separado.

### 1.15.1. Docker Composer

Faz a composição de várias imagens para gerar os serviços para a aplicação. 


### 1.15.2. Gerenciamento de micro service

Micro service é o sistema divido em pequenas partes que realizam tarefas independentes, que juntos formam o todo da aplicação. Facilita o ciclo de desenvolvimento e a manutenabilidade do projeto.