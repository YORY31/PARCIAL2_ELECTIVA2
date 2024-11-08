Vagrant.configure("2") do |config|
  # Configuración para la máquina web1
  config.vm.define "web1" do |web1|
    web1.vm.box = "ubuntu/bionic64"
    web1.vm.network "private_network", ip: "192.168.50.10"
    web1.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    # Monta el directorio compartido
    web1.vm.synced_folder "./web_content", "/var/www/shared_content"
    # Script para instalar Apache y configurar el contenido
    web1.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y apache2
      sudo cp /var/www/shared_content/index_web1.html /var/www/html/index.html
      sudo systemctl restart apache2
    SHELL
  end

  # Configuración para la máquina web2
  config.vm.define "web2" do |web2|
    web2.vm.box = "ubuntu/bionic64"
    web2.vm.network "private_network", ip: "192.168.50.11"
    web2.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    # Monta el directorio compartido
    web2.vm.synced_folder "./web_content", "/var/www/shared_content"
    # Script para instalar Apache y configurar el contenido
    web2.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y apache2
      sudo cp /var/www/shared_content/index_web2.html /var/www/html/index.html
      sudo systemctl restart apache2
    SHELL
  end

  # Configuración para la máquina nginx (balanceador de carga)
  config.vm.define "nginx" do |nginx|
    nginx.vm.box = "ubuntu/bionic64"
    nginx.vm.network "private_network", ip: "192.168.50.12"
    nginx.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    # Script para instalar Nginx y configurarlo como balanceador de carga
    nginx.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y nginx
      sudo rm /etc/nginx/sites-enabled/default
      echo '
      upstream backend {
          server 192.168.50.10;
          server 192.168.50.11;
      }
      server {
          listen 80;
          location / {
              proxy_pass http://backend;
          }
      }
      ' | sudo tee /etc/nginx/sites-available/load_balancer
      sudo ln -s /etc/nginx/sites-available/load_balancer /etc/nginx/sites-enabled/
      sudo systemctl restart nginx
    SHELL
  end
end
