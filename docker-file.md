# Dockerfile
Dockerfile é um arquivo de texto que contêm instruções e comandos para criar uma imagem.

# Instruções do Dockerfile
```
FROM: Indica qual imagem será utilizada como base. Essa propriedade deve ser a primeira linha do Dockerfile

LABEL: Adiciona metadados à imagem. Exemplos: version, description, author, etc

ENV: Infoma variáveis de ambiente da imagem

ADD: copia novos arquivos, diretórios, arquivos tar ou arquivos remotos e os adicionam ao file system da imagem. Para arquivos .tar esses serão descompactados e os arquivos .tar serão excluídos, ou seja, copia apenas o conteúdo

COPY: Copia novos arquivos e diretórios e os adicionam ao file system da imagem

WORKDIR: Define o diretório de trabalho da imagem. O diretório de trabalho default é /

RUN: executa comandos para criação da imagem. Exemplo: instalação de pacotes, execução de scripts, etc

CMD: Executa um comando. Diferente do RUN que executa o comando no momento em que está "buildando" a imagem, o CMD executa no início da execução do container

ENTRYPOINT: Quando configurado, é o processo principal do container. Assim que o container é iniciado o processo definido no entrypoint também é. Quando o processo do entrypoint é finalido o container também será.

EXPOSE: Define a porta da imagem

USER: Determina qual o usuário será utilizado na imagem. Por default é o root

VOLUME: Permite a criação de um ponto de montagem (volume) à imagem
```

## ENTRYPOINT x CMD
Modo exec: ["/usr/sbin/apachectl", "-D", "FOREGROUND"]
Modo shell: /usr/sbin/apachectl -D FOREGROUND

## ADD x COPY
O ADD é mais eficiente que o COPY, pois ele não descompacta o arquivo .tar.


## Criar arquivo Dockerfile e incluir o conteúdo abaixo
```
FROM debian

LABEL description="Apache2"
LABEL version="1.0.0"

ENV APACHE_LOCK_DIR="/var/lock"
ENV APACHE_PID_FILE="/var/run/apache2.pid"
ENV APACHE_RUN_USER="www-data"
ENV APACHE_RUN_GROUP="www-data"
ENV APACHE_LOG_DIR="/var/log/apache2"

RUN apt-get update && apt-get install -y apache2 && apt-get clean
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

VOLUME /var/www/html
EXPOSE 80

ENTRYPOINT ["/usr/sbin/apachectl", "-D", "FOREGROUND"]

```

## Criar uma imagem a partir do Dockerfile
```
docker image build -t apache:1.0.0 .                  # cria imagem com o nome apache:1.0.0
docker image build -t apache:1.0.0 . --no-cache       # cria imagem com o nome apache:1.0.0 sem utilizar cache
```

## Executar o container a partir da imagem criada
```
docker container run -d -p 8080:80 apache:1.0.0       # executa container associando a porta 8080 do host com a porta 80 do container
docker container run -it -P                           # executa o container conectando à porta expose do container e associando a um porta aleatória do host
```

## Acessar o container
```
docker container exec -it CONTAINER_ID /bin/bash
```

## Testar o Apache
```
curl localhost:8080
localhost:8080
```


# Multistage

## Imagem sem uso de multi-stage

### Dockerfile
```
FROM golang

WORKDIR /app
ADD ./test-go.go /app
RUN go build test-go.go

ENTRYPOINT ./test-go
```

### Criar uma imagem a partir do Dockerfile
```
docker image build -t test-go:1.0.0 .
```

### Tamanho da imagem
```
REPOSITORY                                          TAG       IMAGE ID       CREATED         SIZE
test-go                                             1.0.0     ed3118ddf086   4 minutes ago   966MB
```

## Executar o container a partir da imagem criada
```
docker container run test-go:1.0.0
```

## Imagem com uso de multi-stage

### Dockerfile
```
FROM debian

LABEL description="Apache2"
LABEL version="1.0.0"

ENV APACHE_LOCK_DIR="/var/lock"
ENV APACHE_PID_FILE="/var/run/apache2.pid"
ENV APACHE_RUN_USER="www-data"
ENV APACHE_RUN_GROUP="www-data"
ENV APACHE_LOG_DIR="/var/log/apache2"

RUN apt-get update && apt-get install -y apache2 && apt-get clean
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

VOLUME /var/www/html
EXPOSE 80

ENTRYPOINT ["/usr/sbin/apachectl", "-D", "FOREGROUND"]
```

### Criar uma imagem a partir do Dockerfile
```
docker image build -t test-go-multi:1.0.0 .
```

## Executar o container a partir da imagem criada
```
docker container run test-go-multi:1.0.0
```

### Tamanho da imagem
```
REPOSITORY                                          TAG       IMAGE ID       CREATED        SIZE
test-go-multi                                       1.0.0     c94a867146db   11 hours ago   7.29MB
test-go                                             1.0.0     ed3118ddf086   11 hours ago   966MB
```

# Estudar multi-stage


# Docker registry
## Criar docker registry local
```
docker container run -d -p 5000:5000 --restart=always --name registry registry
```