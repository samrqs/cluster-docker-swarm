Vagrant.configure("2") do |config|
  (1..3).each do |i|
    config.vm.define "node#{i}" do |node|
      node.vm.box = "ubuntu/bionic64"  #imagem do ubuntu
      node.vm.network "private_network", type: "dhcp" #rede
      node.vm.hostname = "node#{i}"
      node.vm.provider "virtualbox" do |vb|
        vb.name = "node#{i}"
        vb.memory = "512"  #memória
        vb.cpus = 1
      end

      node.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update -y
        sudo apt-get upgrade -y
        
        # Docker
        sudo apt-get install -y docker.io
        
        # Adiciona o usuário vagrant ao grupo docker
        sudo usermod -aG docker vagrant

        # Instala Docker Compose
        sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
        sudo chmod +x /usr/local/bin/docker-compose
      SHELL
    end
  end
end