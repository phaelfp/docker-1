## Como criar um localhost HTTPS com mkcert e docker

> Este repositório trata-se de **anotações pessoais** e não um repositório oficial que deva ser levado em consideração junto aos documentos oficiais de cada ferramenta mencionada aqui

### Relação de ferramentas

```
docker
docker-compose
mkcert (brew para a instalação do mkcert dependendo do so)
```

### Para Manjaro KDE - AUR

Habilite o repositório AUR na interface gráfica em: ``Adicione ou remova programas instalados no sistema > menu no canto superior direito > Preferências > Tab AUR``

![](img/prefencias-aur.png)

Agora faça a busca por:
```
docker (atualmente está como docker  1:19.03.4-1)
docker-compose (atualmente está como docker-compose  1.24.1-1)
mkcert (atualmente está como mkcert  1.4.1-1)
```
E as compile, lembrando que este processo são todas pela interface gráfica do ``Adicione ou remova programas instalados no sistema``

_Agora que já temos todas as ferramentas necessárias, mãos à obra._

## Montando docker localhost https

### mkcert

Primeiro, necessitamos fazer a instalação do ``mkcert`` para que possamos gerar nossos certificados, para isso execute: ``mkcert -install``

Já estamos aptos a gerar nossos certificados. Agora salve toda essa estrutura de pastas e arquivos onde desejar.

Acesse o caminho em que foi salvo os arquivos e pastas e vá para ``./certs`` faça: 

1. delete os dois certificados salvos, pois estes pertecem somente a minha máquina

1. agora execute: ``mkcert localhost``, e você obterá os mesmos arquivos novamente, porém agora destinados a sua máquina.

Sua estrutura está pronta para ser usada junto aos certificados do ``mkcert`` para uma conexão ``https://localhost``


### mysql

Para o acesso ao banco de dados tente como ``mysql`` mas provalmente funcionará como ``loocalhost`` assim como funcionou no ~~JetBrains DataGrip~~ pra mim.
Caso queira mudar a senha do root e o nome do primeiro database:
```
mysql:
    ...
    environment:
          - MYSQL_ROOT_PASSWORD=password
          - MYSQL_DATABASE=dashboard_adm
    ...

```

### php myadmin
Para o acesso no phpAdmin, usaremos http://localhost:8082

```
server: mysql
user: root
passaword: password
```

Caso queira mudar a porta:
```
phpmyadmin:
    ...
    ports:
          - "8082:80"
    ...
```

### Montagem do docker

Agora vamos para a montagem do nosso ``docker``, volte para a nossa pasta raiz (que nesta estrutura trata-se da pasta ``./docker``) e execute: ``sudo docker-compose up``

Aguarde a montagem de todas das imagens e _Pronto!_ Agora temos um acesso ``https://localhost`` para a pasta ``./www`` e ``https://localhost:8080`` para a pasta ``./app``


Caso obtenha um erro e queira remontar todo o ``docker``, segue abaixo alguns comandos.

**observação:** deixarei na ordem exata para que todos os comandos funcionem com sucesso

#### Pare os contêiners para remoção

Para listar todos os seus contêiners utilize ``sudo docker ps -a``

Veja se a coluna `STATUS` dos contêiners que deseja remove está como `Up ...`, caso positivo execute o comando abaixo com a informação que temos na nossa lista em `contâiner ID`:

```
sudo docker stop contâiner_ID
```

Para mais de um contâiner separe-os com espaçoes:

```
sudo docker stop contâiner_ID contâiner_ID contâiner_ID
```

#### Desmonte os contêiners

O ``sudo docker system prune`` removerá todos os contêineres parados, todas as imagens pendentes e todas as redes não utilizadas.

```
[wfonseca@laptop docker]$ sudo docker system prune
WARNING! This will remove:
  - all stopped contêiners
  - all networks not used by at least one contâiner
  - all dangling images
  - all dangling build cache

Are you sure you want to continue? [y/N]
```

Confirme o processo com ``y`` e depois ``enter``.

##### segunda limpeza

Se você também deseja remover todos os volumes não utilizados, utilize ``sudo docker system prune --volumes``

```
[wfonseca@laptop docker]$ sudo docker system prune
WARNING! This will remove:
  - all stopped contêiners
  - all networks not used by at least one contâiner
  - all dangling images
  - all dangling build cache

Are you sure you want to continue? [y/N]
```

Confirme o processo com ``y`` e depois ``enter``.

#### Delete as imagens

Para listar todas as suas imagens utilize ``sudo docker images``.

Para remover todas as imagens que não são referenciadas por nenhum contêiner existente (que no caso foram removido anteriormente) utilizaremos ``sudo docker image prune -a``:
```
[wfonseca@laptop docker]$ sudo docker image prune -a
WARNING! This will remove all images without at least one contâiner associated to them.
Are you sure you want to continue? [y/N]
```

Confirme o processo com ``y`` e depois ``enter``.

#### Limpeza mysql

Agora que já limpamos as imagens, caso queria resetar todas as configurações estabelecidas no myql, faça o seguin:
1. Acesse a pasta ``./mysql``
1. Execute ``sudo rm -rf data``

A pasta que continha todas as informações de nosso database foi deletada.

_Pronto!_

Agora estamos prontos para refazer toda a montagem do docker

##### Agradecimentos

Telegram:

@sistematico

@tcarreira

[dockerbr](https://t.me/dockerbr)
