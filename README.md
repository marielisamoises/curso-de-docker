# Curso de Docker e Docker-compose
curso de docker e docker compose feito pela Alura!

Anotações:


docker version - exibe a versão do docker.
docker run NOME_DA_IMAGEM - cria um container com a respectiva imagem
docker ps - exibe todos os containers que estao ativos no momento
docker ps -a - exibe todos os containers existentes

docker run -it ubuntu - o terminal que estamos usando se torna o do container em si
docker stop - mata o container
docker start - inicia o container
docker start -a -i id_do_container - inicio o container já em modo de terminal

Principais estados de um container:
docker rm id_do_container - remove o container
docker container prune - remove todos containers inativos
docker images - mostra as imagens 
docker rmi nome_da_imagem - remove as imagens


Layered filesystem - uma imagem que tem varias camadas
docker reaproveita camadas que outras imagens ja baixaram
camadas base de uma imagem são read only
docker cria uma layer "read/write" que permite ediçao
uma imagem base pode ser reaproveitada por N containers


docker run dockersamples/static-site - imagem docker de site estatico

docker run -d dockersamples/static-site - rodando em background

docker stop -t 0 nome_do_container - mata na hora

docker run -d -P dockersamples/static-site - atribui porta externa para acesso 

λ docker port 0c81f85d8681
443/tcp -> 0.0.0.0:32768
80/tcp -> 0.0.0.0:32769

docker run -d -P --name meu-site dockersamples/static-site

docker run -d -1234:80 --name meu-site dockersamples/static-site

docker run -d -P -e AUTHOR="Mariana Elisa" --name meu-site dockersamples/static-site - coloca argumento, por exemplo variavel de ambiente
resposta do site: Hello Mariana ELisa!
This is being served from a docker
container running Nginx.

docker ps -q - lista só os ids

docker stop $(docker ps -q) - lista os ids e mata os containers na sequencia


docker ps - exibe todos os containers em execução no momento.
docker ps -a - exibe todos os containers, independentemente de estarem em execução ou não.
docker run -it NOME_DA_IMAGEM - conecta o terminal que estamos utilizando com o do container.
docker start ID_CONTAINER - inicia o container com id em questão.
docker stop ID_CONTAINER - interrompe o container com id em questão.
docker start -a -i ID_CONTAINER - inicia o container com id em questão e integra os terminais, além de permitir interação entre ambos.
docker rm ID_CONTAINER - remove o container com id em questão.
docker container prune - remove todos os containers que estão parados.
docker rmi NOME_DA_IMAGEM - remove a imagem passada como parâmetro.
docker run -d -P --name NOME dockersamples/static-site - ao executar, dá um nome ao container.
docker run -d -p 12345:80 dockersamples/static-site - define uma porta específica para ser atribuída à porta 80 do container, neste caso 12345.
docker run -d -P -e AUTHOR="Fulano" dockersamples/static-site - define uma variável de ambiente AUTHOR com o valor Fulano no container criado.


Aula 3  
Container são voláteis: Onde salvar o código? Logs? Dados? - Nos volumes!
Quando crio um volume, é como se criasse uma pasta dentro do container e estou apontando pra uma pequena pasta dentro do Docker host

docker run -v "/var/www" ubuntu - crio um container ubuntu com um volume /var/www
docker inspect nome_do_container - traz todas informações do container

dentro de "Mount"deve aparecer o volume

ex:
 "Mounts": [
            {
                "Type": "volume",
                "Name": "44a11caa8783f5558eaa2abe30ad127217d916bf40ebd0c19893f35444ac431c",
                "Source": "/var/lib/docker/volumes/44a11caa8783f5558eaa2abe30ad127217d916bf40ebd0c19893f35444ac431c/_data",
                "Destination": "/var/www",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],

docker run -it -v "C:\Users\marie\OneDrive\Área de Trabalho\Cursos\Docker:/var/www" ubuntu


docker run -p 8080:3000 -v "C:\Users\marie\OneDrive\Área de Trabalho\Cursos\Docker\volume-exemplo:/var/www" -w "/var/www" node npm start

docker build -f dockerfile -t marielisa/node . - build o projeto a partir do docker file de nome "dockerfile", tagueando com nome do criador(a)/nome da imagem, dentro do contexto (lugar) onde estou (.)

docker rm -f id_do_container - remove o container forçando a parada

exemplo dockerfile:
FROM node:latest
MAINTAINER Douglas Quintanilha
ENV PORT=3000
COPY . /var/www
WORKDIR /var/www
RUN npm install
ENTRYPOINT npm start
EXPOSE $PORT

obs: quando for fazer o build tageando, ja coloca o nome do "usuario/projeto" no docker

apt-get update && apt-get install -y iputills-ping - instala o ping dentro de uma instancia ubuntu (linux)


Aula 4 - Network no Docker
docker network create --driver bridge nome-da-rede - cria uma rede com um nome especifico
λ docker network ls - lista todas redes existentes
NETWORK ID          NAME                DRIVER              SCOPE
dd5af70cd2a1        bridge              bridge              local
553bd2d68285        host                host                local
45cceb6b7d9b        my-network          bridge              local
48d7fc4b61d2        none                null                local 

docker run -it --name ubuntu-container --network my-network ubuntu - cria um container de ubuntu interativo dentro de uma rede especifica

Aula 5
docker pull douglasq/alura-books:cap05 - baixando a imagem publica do prof para o exercicio de comunicação entre containers
docker pull mongo

docker network inspect my-network - consigo ver os containers na minha rede "my-network"


Aula 6
Porque usar o docker compose:
- quando tenho + de 1 container
- muitas flags
- muito manual
- facil de errar
- garanto a ordem de execução
orquestra multiplos containers lendo um arquivo docker-compose.yml

docker-compose build - baixa todas as imagens que estão descritas no arquivo "docker-compose.yml"
docker-compose up - pega a "receita" descrita no docker-compose.yml e começa a executar todos os passos

docker-compose up -d - deteched
docker-compose down - derruba os containers e remove

docker exec -it alura-books-1 ping alura-books-2
docker exec -it node1 ping node2

docker-compose restart - reinicia os containers


λ docker-compose ps
            Name                         Command             State              Ports
------------------------------------------------------------------------------------------------
alura-books-1                  npm start                     Up      0.0.0.0:32777->3000/tcp
alura-books-2                  npm start                     Up      0.0.0.0:32778->3000/tcp
alura-books-3                  npm start                     Up      0.0.0.0:32779->3000/tcp
alura-docker-cap06_mongodb_1   docker-entrypoint.sh mongod   Up      27017/tcp
nginx                          nginx -g daemon off;          Up      443/tcp, 0.0.0.0:80->80/tcp


sobre microserviços: https://sdtimes.com/continuous-integration/martin-fowler-monolithic-apps-first-microservices-later/
