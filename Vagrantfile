# -*- mode: ruby -*-
# vi: set ft=ruby :

# User customization
HELLOSPA_IP = "192.168.200.10"
HELLOSPA_CPU = 2
HELLOSPA_MEM = 2048
HOST_APP_PORT = 8080
# End

Vagrant.configure("2") do |config|
  config.vm.box = "generic/centos7"

  config.vm.define "hellospa" do |hellospa|
    hellospa.vm.hostname = "hellospa"
    hellospa.vm.synced_folder "./", "/app/hello-spa"
    hellospa.vm.network "private_network", ip: HELLOSPA_IP
    hellospa.vm.network "forwarded_port", guest: 80, host: HOST_APP_PORT, id: "hellospa"
    hellospa.vm.provider :virtualbox do |vb|
        vb.memory = HELLOSPA_MEM
        vb.cpus = HELLOSPA_CPU
    end
    hellospa.vm.provision "docker" do |docker|
      docker.build_image "/app/hello-spa/", args: "-f /app/hello-spa/docker/Dockerfile -t hello-spa "
      docker.run "hello-spa", args: "-p 80:80 --name hellospa"
    end
    hellospa.vm.provision "shell", inline: "docker save hello-spa > /app/hello-spa/docker/hello-spa.tar"
  end
end
