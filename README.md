# cluster-docker-swarm
# Cluster Docker Swarm com 3 Nós usando Vagrant e VirtualBox

Este repositório fornece um guia passo a passo para configurar um cluster Docker Swarm com 3 nós usando **Vagrant**, **VirtualBox** e **Docker**. As máquinas virtuais serão criadas localmente e configuradas para funcionar como um cluster Docker Swarm.

## Pré-requisitos

Antes de começar, certifique-se de ter as seguintes ferramentas instaladas:

- **[Vagrant](https://www.vagrantup.com/)**
- **[VirtualBox](https://www.virtualbox.org/)**
- **[Docker](https://www.docker.com/products/docker-desktop)**

### Passo 1: Clone este repositório

Clone o repositório para sua máquina local:

```
git clone git@github.com:samrqs/docker-vagrant.git
cd docker-vagrant
```

### Passo 2: Configuração do Vagrantfile

1. O arquivo `Vagrantfile` contém a configuração para criar 3 máquinas virtuais Ubuntu.
2. O Vagrant irá provisionar as máquinas para instalar o Docker e configurar o ambiente.

### Passo 3: Iniciar as Máquinas Virtuais

No diretório onde o `Vagrantfile` está localizado, execute o comando abaixo para iniciar as 3 máquinas virtuais:

```
vagrant up
```

Este comando irá:

 * Criar 3 VMs utilizando o VirtualBox.
 * Instalar o Docker em cada VM.
 * Adicionar o usuário vagrant ao grupo docker para permitir a execução de comandos Docker sem sudo.
 * Instalar o Docker Compose.

### Passo 4: Inicializar o Docker Swarm

1. Após as VMs estarem em execução, acesse a primeira máquina virtual (node1) usando o comando:

```
vagrant ssh node1
```

2. Inicialize o Docker Swarm no node1:

```
sudo docker swarm init --advertise-addr <IP_do_node1>
```

Após executar o comando, você verá uma saída que inclui um token de junção que será necessário para adicionar os outros nós ao Swarm. Copie esse comando!

### Passo 5: Adicionar Nós ao Swarm

Agora, adicione os nós node2 e node3 ao Swarm:

1. Acesse node2:

```
vagrant ssh node2
```

2. Execute o comando de junção no node2:

```
sudo docker swarm join --token <TOKEN_COPIADO_DO_node1> <IP_do_node1>:2377
```

3. Repita o processo para o node3:

```
vagrant ssh node3
sudo docker swarm join --token <TOKEN_COPIADO_DO_node1> <IP_do_node1>:2377
```

### Passo 6: Verificar o Cluster Docker Swarm

No node1, execute o comando para verificar o status do cluster e verifique se todos os nós estão no Swarm:

```
sudo docker node ls
```

Você deverá ver os 3 nós (node1, node2, node3) listados.

### Passo 7: Implantar um Serviço no Swarm

Agora que o cluster Swarm está configurado, você pode implantar um serviço. Como exemplo, vamos implantar um serviço nginx com 3 réplicas:

```
sudo docker service create --name webserver --replicas 3 -p 80:80 nginx
```

Este comando cria um serviço chamado webserver com 3 réplicas, acessível na porta 80.

### Passo 8: Acessar o Serviço

Com o serviço em execução, acesse qualquer um dos nós pelo navegador, usando a porta 80. O NGINX responderá à sua solicitação.

### Passo 9: Verificar o Status do Serviço

Para verificar o status do serviço e das réplicas, execute:

```
sudo docker service ls
```

Para detalhes mais específicos sobre o serviço, use:

```
sudo docker service ps webserver
```

### Passo 10: Finalizar e Destruir as Máquinas Virtuais

Após concluir o uso do cluster, você pode destruir as máquinas virtuais com o comando:

```
vagrant destroy -f
```

Isso removerá as VMs criadas pelo Vagrant.