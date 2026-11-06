
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  # Ubuntu virtual machine.
  config.vm.box = "ubuntu/jammy64"
  config.vm.hostname = "Ivan-Banal"

  # Network configuration.
  config.vm.network "private_network", ip: "192.168.56.58"
  config.vm.network "forwarded_port", guest: 3000, host: 3000
  config.vm.network "forwarded_port", guest: 5090, host: 5090

  # VirtualBox provider settings.
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.cpus = 2
  end

  # Automated Docker provisioning.
  config.vm.provision "shell", inline: <<-SHELL

    # Install Docker
    apt-get update
    apt-get install -y ca-certificates curl gnupg git

    install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    chmod a+r /etc/apt/keyrings/docker.gpg

    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" > /etc/apt/sources.list.d/docker.list

    apt-get update
    apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

    # Enable and start Docker
    systemctl enable --now docker
    usermod -aG docker vagrant

    # Clone repositories
    cd /home/vagrant

    git clone https://github.com/IvanBanal/COMP-4001-Module-4-Assignment-Studentportfoliotemplate
    git clone https://github.com/IvanBanal/COMP-4001-Module-4-Assignment-BankingApp

    # Docker Compose configuration
    cat > docker-compose.yml <<'EOF'
services:
  studentportfolio:
    build: ./COMP-4001-Module-4-Assignment-Studentportfoliotemplate
    ports:
      - "3000:80"
    networks:
      - prf-network

  bankingapp:
    build: ./COMP-4001-Module-4-Assignment-BankingApp
    ports:
      - "5090:5090"
    networks:
      - prf-network

networks:
  prf-network:
    name: prf-network
EOF

    # Build and start both applications
    docker compose up -d --build

  SHELL
end
