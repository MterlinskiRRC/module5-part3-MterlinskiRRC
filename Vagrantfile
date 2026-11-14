# Deploys the full Part 3 stack inside a VM.
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
  config.vm.hostname = "prf-bank"

  # Host 8080 -> VM 80 (nginx reverse proxy)
  config.vm.network "forwarded_port", guest: 80, host: 8080

  config.vm.synced_folder ".", "/vagrant"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = 2048
    vb.cpus = 2
  end

  config.vm.provision "shell", inline: <<-SHELL
    set -e
    apt-get update -y
    apt-get install -y ca-certificates curl
    curl -fsSL https://get.docker.com | sh
    usermod -aG docker vagrant
    cd /vagrant
    docker compose up --build -d
  SHELL
end
