# Comandos Docker

---

##Containers

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
