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
