# aphack-conference-leak
Essa é uma light talk que será apresentada no APHack conference.

## Leaks
Leak é um termo inglês e significa vazar ou vazamento e é usada para difinir quando temos algum vazamento de dados/informações.

- Uber (156 mil Brasileiros tiveram nomes,telefones e emails vazados)
- Netshoes (2 milhões de clientes)
- Facebook (Cambridge Analytica)
- Banco inter (19 mil correntistas)
- C&A (mais de 2 milhões de clientes)

## Buscando leaks
Podemos buscar por leaks de diversas formas, sejam elas:
- Pastebin (Usando tecnicas do google)
- pwndb (Usado uma base de dados)
- pwnbin (Monitorando real time o pastebin)

### pastebin
Pastebin é um projeto que nos auxilia no armazenamento de trechos de codigos e que não precisa de formatação. Ele é muito usado nos dias de hoje para hospedar trechos de codigos, anotações e infelizmente/felizmente muito usado para o armazenamento de leaks.

Conseguimos usar o operador "palavra" e assim aumentando o nosso filtro de busca.
```sh
site:pastebin.com "leak"
```

Podemos buscar melhorar nossa busca e assim conseguir filtrar melhor, buscando por exemplo por nomes de pessoas, empresas e até emails.
```sh
site:pastebin.com "leak" "@email-da-empresa.com"
```

Alem disso conseguimos buscar por dados vazados, como o CPF.
```sh
"leak" CPF site:pastebin.com
```

### pwndb (onion)
Podemos encontrar nesse projeto mais de **1.4 bilhões de dados** e ele está disponivel na rede **onion**.
```sh
http://pwndb2am4tzkvold.onion
```

Podemos acessar ele via **tor browser**.
> Podemos encontrar ele: https://www.torproject.org/pt-BR/download/

Mas alem disso tambem podemos usar uma lib disponivel no github para realizar a conexão, porem vamos precisar estar usando um proxy para acessar a rede.
> Podemos encontrar o projeto no seguinte link: https://github.com/davidtavarez/pwndb

## Construindo lab
Agora vamos automatizar a coleta de informações usando Docker, dessa forma vamos usar uma imagem do tor e uma imagem para o pwndb.

### tor
Vamos usar uma imagem criada e que está disponivel no docker hub chamada **dperson/torproxy**.
> Podemos encontrar ela aqui: https://hub.docker.com/r/dperson/torproxy/

Vamos subir a imagem
```sh
sudo docker run -it --network host -p 8118:8118 -p 9050:9050 -d dperson/torproxy
```
> Com o argumento **--network host** ele vai entender para funcionar no mesmo host e assim possibilitando o **pwnbin** de ser usado e alcançar a rede onion.

### pwndb
Vamos criar uma imagem para o projeto **pwndb**, com ela conseguimos criar um ambiente para realizar a consulta. Vamos entrar no diretorio **Labs** e em seguida vamos para o **pwndb**.

Podemos realizar o build da imagem usando
```sh
sudo docker build -t "greenmind/pwndb:1" .
```

Vamos usar ela da seguinte forma
```sh
sudo docker run -it --rm --network host "greenmind/pwndb:1" --target "@cea.com.br"
```

### Socialscan
Podemos usar o **Socialscan** para realizar testes para checar endereços de email e username disponibilidade em plataformas online.
> https://github.com/iojw/socialscan

#### Criando imagem no docker
Vamos entrar no diretorio **Labs** e em seguida vamos para o **socialscan**.
Em seguida podemos realizar o build da imagem e criar uma imagem do **socialscan**.
```sh
sudo docker build -t "greenmind/socialscan:1" .
```

#### Usando a imagem
Em seguida com a imagem criada vamos podemos usar como argumento o projeto:
```sh
sudo docker run -it --rm "greenmind/socialscan:1" "greenmind.sec@gmail.com"
```

### Sherlock
Com o **Sherlock** conseguimos caçar contas de mídia social por nome de usuário nas redes sociais
> https://github.com/sherlock-project/sherlock

#### Criando imagem
Podemos ir até o diretorio **Labs** e entrar no projeto **sherlock**.

Podemos realizar o build da imagem da seguinte forma
```sh
sudo docker build -t "greenmind/sherlock:1" .
```

#### Usando a imagem
Podemos usar a imagem da seguinte forma
```sh
sudo docker run -it --rm "greenmind/sherlock:1" greenmind
```


### pwnbin
Podemos entrar no diretorio **Labs**.
> git clone https://github.com/greenmind-sec/pwnbin/

#### Criando imagem
Vamos até o projeto **pwnbin**:
Vamos criar uma imagem usando o Docker:
```sh
sudo docker build -t "greenmind/pwnbin:1" .
```

#### Usando a imagems
Vamos agora criar um volume do diretorio /root que é onde estamos salvando o output e criar uma conexão com o nosso host.
```sh
sudo docker run -v"${PWD}:/root" -it "greenmind/pwnbin:1" -k leak,pass -o /root/teste.txt
```

Agora podemos subir um arquivo com essas palavras no pastebin que o pwnbin vai buscar reconhcer.
