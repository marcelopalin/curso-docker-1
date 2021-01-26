# Instalando Docker 20.10.2 no Ubuntu 20.10 (groovy)


Qual é sua versão do Ubuntu?

```
lsb_release -cs
```

Verifique se tem versões anteriores intaladas de docker:

```
which docker
```

Remova-as caso estejam instaladas:

```
sudo apt remove docker docker-engine
```

Pré-requisitos para instalação do docker:

```
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

Então, adicione a chave GPG para o repositório oficial do Docker no seu sistema:

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key --keyring /etc/apt/trusted.gpg.d/docker-apt-key.gpg add
```


Adicione o repositório do Docker às fontes do APT:

```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```


Em seguida, atualize o banco de dados do pacote com os pacotes do Docker do recém adicionado repositório:

```
sudo apt update
```

Certifique-se de que você está prestes a instalar do repositório do Docker ao invés do repositório padrão do Ubuntu:

```
apt-cache policy docker-ce
```

Você verá um resultado assim, embora o número da versão para o Docker possa ser diferente:

```
apt-cache policy docker-ce
docker-ce:
  Instalado: 5:20.10.2~3-0~ubuntu-groovy
  Candidato: 5:20.10.2~3-0~ubuntu-groovy
  Tabela de Versão:
 *** 5:20.10.2~3-0~ubuntu-groovy 500
        500 https://download.docker.com/linux/ubuntu groovy/stable amd64 Packages
        100 /var/lib/dpkg/status
     5:20.10.1~3-0~ubuntu-groovy 500
        500 https://download.docker.com/linux/ubuntu groovy/stable amd64 Packages
     5:20.10.0~3-0~ubuntu-groovy 500
        500 https://download.docker.com/linux/ubuntu groovy/stable amd64 Packages
```
 

Observe que o docker-ce não está instalado, mas o candidato para a instalação é do repositório do Docker para o Ubuntu 20.10 (groovy).

Finalmente, instale o Docker:

```
sudo apt install docker-ce docker-ce-cli containerd.io
```


O Docker deve agora ser instalado, o daemon iniciado e o processo habilitado a iniciar no boot. Verifique se ele está funcionando:

```
sudo systemctl status docker
```

O resultado deve ser similar ao mostrado a seguir, mostrando que o serviço está ativo e funcionando:

```
sudo systemctl status docker
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2021-01-25 14:08:16 -03; 2min 50s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 35370 (dockerd)
      Tasks: 17
     Memory: 44.2M
     CGroup: /system.slice/docker.service
             └─35370 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

jan 25 14:08:15 mpi-Inspiron-5570 dockerd[35370]: time="2021-01-25T14:08:15.807149381-03:00" level=warning msg="Your kernel doe>
jan 25 14:08:15 mpi-Inspiron-5570 dockerd[35370]: time="2021-01-25T14:08:15.807174713-03:00" level=warning msg="Your kernel doe>
jan 25 14:08:15 mpi-Inspiron-5570 dockerd[35370]: time="2021-01-25T14:08:15.807191466-03:00" level=warning msg="Your kernel doe>
jan 25 14:08:15 mpi-Inspiron-5570 dockerd[35370]: time="2021-01-25T14:08:15.807423246-03:00" level=info msg="Loading containers>
jan 25 14:08:16 mpi-Inspiron-5570 dockerd[35370]: time="2021-01-25T14:08:16.095925864-03:00" level=info msg="Default bridge (do>
jan 25 14:08:16 mpi-Inspiron-5570 dockerd[35370]: time="2021-01-25T14:08:16.166567107-03:00" level=info msg="Loading containers>
jan 25 14:08:16 mpi-Inspiron-5570 dockerd[35370]: time="2021-01-25T14:08:16.220062798-03:00" level=info msg="Docker daemon" com>
jan 25 14:08:16 mpi-Inspiron-5570 dockerd[35370]: time="2021-01-25T14:08:16.220166245-03:00" level=info msg="Daemon has complet>
jan 25 14:08:16 mpi-Inspiron-5570 systemd[1]: Started Docker Application Container Engine.
jan 25 14:08:16 mpi-Inspiron-5570 dockerd[35370]: time="2021-01-25T14:08:16.255399820-03:00" lev

```

Instalando o Docker agora não dá apenas o serviço do Docker (daemon), mas também o utilitário de linha de comando docker, ou o cliente do Docker. Vamos explorar como usar o comando docker mais tarde neste tutorial.

Testando com a imagen hello-world sem o comando sudo:

```
docker run hello-world
```

Saída com a permissão negada:

```
docker: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/create: dial unix /var/run/docker.sock: connect: permission denied.
See 'docker run --help'.
```

Executando com o comando sudo:

```
sudo docker run hello-world
```

Saída:
```
[sudo] senha para mpi: 
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
0e03bdcc26d7: Pull complete 
Digest: sha256:31b9c7d48790f0d8c50ab433d9c3b7e17666d6993084c002c2ff1ca09b96391d
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

# Opcional: Executando o Comando Docker Sem Sudo 

Por padrão, o comando docker só pode ser executado pelo usuário root ou por um usuário no grupo docker, que é criado automaticamente no processo de instalação do Docker. Se você tentar executar o comando docker sem prefixar ele com o sudo ou sem estar no grupo docker, você terá um resultado como este:

```
docker: Cannot connect to the Docker daemon. Is the docker daemon running on this host?.
See 'docker run --help'.
```

Se você quiser evitar digitar sudo sempre que você executar o comando docker, adicione seu nome de usuário no grupo docker:

```
sudo usermod -aG docker ${USER}
```

Para inscrever o novo membro ao grupo, saia do servidor e logue novamente, ou digite o seguinte:

```
su - ${USER}
```

Você será solicitado a digitar a senha do seu usuário para continuar.

Confirme que seu usuário agora está adicionado ao grupo docker digitando:

```
id -nG
```

```
mpi adm cdrom sudo dip plugdev lpadmin lxd sambashare docker
```

Agora poderá rodar sem o comando sudo:

```
docker run hello-world
```

Saída:
```

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

Se você precisar adicionar um usuário ao grupo docker com o qual você não está logado, declare esse nome de usuário explicitamente usando:

```
sudo usermod -aG docker <outrousername>
```

Vou supor que você esteja executando o comando docker como um usuário no grupo docker. Se você escolher não fazer isso, por favor preencha os comandos com sudo.

