# Comandos Docker

---

## Containers

_Rodar um container e Liberar o terminal_
``` shell
docker run -d ubuntu 
```

_Rodar um container e execultar um comando_
``` shell
docker run -d ubuntu 
```

_Remover todos os containers ativos:_

``` shell
docker rm $(docker ps -q) -f
```


##  Volume:

_Criar o Volume de nome hd-nginx_

``` shell
 docker volume create hd-nginx
``` 
_Montar o container usando a imagem do ubuntu com o Volume hd-nginx_

``` shell
docker run --rm -v hd-nginx:/codigo  ubuntu 
``` 
_Compartilhar Pasta do Computador com o container_

``` shell
docker run --rm  -v ~/Documents/Dock/nginx:/usr/share/nginx/html nginx
``` 


##  Imagem:

_Criar uma imagem baseada no ubuntu_

``` yaml
#imagem base
FROM ubuntu:latest

#comando sempre fixo, sempre será execultado
ENTRYPOINT ["echo","hello"] 

#Parâmetros váriavel do ENTRYPOINT
CMD ["WOLRD"]
``` 

_Rodando um container baseado na imagem criada_

``` shell
docker run --rm ubuntu:modificado 
> saida: hello WORD
``` 

_Subistituindo os comando do CMD na hora de execultar o container_

``` shell
docker run --rm ubuntu:modificado teste
> saida: hello teste
``` 

Ao usar um arquivo docker-entrypoint.sh para configurar a imagem,
Colocar no final exec "$@" para executar comandos  que venham a ser passados .

``` shell
exec "$@"
``` 

### Publicando a Imagem no Docker HUB

Fazer o login no Docker

``` shell
docker login
``` 

Criar a imagem passando usuário/nomedaimagem:versao

``` shell
docker build -t evaldoavelar/servidor:teste
``` 

Subir a imagem para o docker hub
``` shell
docker push evaldoavelar/servidor:teste 
``` 
 ### Network

 * Modo Bridge

    Criar uma rede de nome **dev** informando o drive como bridge

    ``` shell
    docker network create -d bridge dev
    ```

    Rodar e conectar o container na rede

    ``` shell
    docker run -d --rm --name ubuntu2 --network dev ubuntu:modificado
    ```

    Rodar e conectar o container na rede
    ``` shell
    docker network connect dev ubuntu3
    ```

 * Modo Host  
    No modo host, não é nescessário mapear a porta usando o -p 3001:3001 para que a máquina local tenha acesso ao container (não funciona no macos).    

    ``` shell
    docker run -d --rm --name ubuntu4 --network host ubuntu:modificado
    ```

 ### Docker-compose

 * Criar Serviços onde vai ser informado os parâmetros para levantar o conatainer, como se fosse feito com o docker run na linha de comando:

``` yaml
version: "3"
services:
  
  back-end:
    build: 
      context: .
      dockerfile: Dockerfile.back-end
    image: evaldoavelar/echo-back-end:1.0
    container_name: back-end
    networks: 
      - echo
    ports: 
      - "8000:80"

  front-end:
    build: 
      dockerfile: Dockerfile.front-end
      context: .
    image: evaldoavelar/echo-front-end:1.2
    container_name: front-end
    environment: 
      - API_BASE_URL=http://127.0.0.1:8000/
    networks:
      - echo
    ports:  
     - "80:80"

networks: 
  echo:
    driver: bridge
```    

* Depois de alterar o Dockerfile, passar o parametro **--build** para o docker compose para que seja gerado a imagem

``` shell
docker-compose up --build
```


#### Outras opções
``` yaml
version: "3"

services: 

  #nome do serviço levantado pelo docker-compose
  db:
    #nome da imagem a ser usada 
    image: mysql:5.7

    #nome do conainer
    container_name: mysql-5.7

    # habiltar interação via shell
    tty: true 
    
    # executado depois do entrypoint
    command: --default-authentication-plugin=mysql_native_password
    
    # mapeamento de volumes
    volumes: 
      - ./mysql-files:/var/lib/mysql 

    #restartar o conatainer sempre que ele cair  
    restart: always 
    
    # variáveis de ambiente da imagem
    environment: 
      - MYSQL_ROOT_PASSWORD=mestre
      - MYSQL_DATABASE=teste


    #redes que o conatiner vai acessar  
    networks: 
      - net-node

    # mapeamento de porta
    ports: 
      - 3306:3306

# criar networks
networks: 
  #nome da rede
  net-node:
    #driver da rede
    driver: bridge    
```


### Dependência de Container

Para aguardar até que um conatiner esteja pronto, pode se usar scripts como o dockerize no Dockerfile
https://github.com/jwilder/dockerize


* Dockerfile

``` yaml
RUN apt-get update && apt-get install -y wget

ENV DOCKERIZE_VERSION v0.6.1
RUN wget https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && tar -C /usr/local/bin -xzvf dockerize-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && rm dockerize-linux-amd64-$DOCKERIZE_VERSION.tar.gz


CMD dockerize -wait tcp://back-end:80 -timeout 5000s  
```

  * Docker Compose
  