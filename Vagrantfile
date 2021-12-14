# -*- mode: ruby -*-
# vi: set ft=ruby :

# User customization
HELLOSPA_IP = "192.168.200.10"
HELLOSPA_CPU = 2
HELLOSPA_MEM = 2048

REGISTRY_IP = "192.168.200.11"
REGISTRY_CPU = 2
REGISTRY_MEM = 2048

HOST_APP_PORT = 8080
# End

Vagrant.configure("2") do |config|
  config.vm.box = "generic/centos7"

  config.vm.define "registry" do |registry|
    registry.vm.hostname = "registry"
    registry.vm.synced_folder "./", "/app/hello-spa",
      create: true, owner: "vagrant", group: "vagrant"
    registry.vm.synced_folder "./playbooks", "/vagrant/playbooks"
    registry.vm.network "private_network", ip: REGISTRY_IP
    registry.vm.provider :virtualbox do |vb|
        vb.memory = REGISTRY_MEM
        vb.cpus = REGISTRY_CPU
    end
    registry.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "playbooks/registry_prepare.yaml"
      ansible.become = true
      ansible.become_user = "root"
      ansible.extra_vars = { hellospa_ip: HELLOSPA_IP }
    end
    registry.vm.provision "docker" do |docker|
      docker.run "registry:2", args: "-p 5000:5000 --restart always --name registry -v '/app/certs:/certs' \
                                      -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/registry.crt \
                                      -e REGISTRY_HTTP_TLS_KEY=/certs/registry.key"
    end
    registry.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "playbooks/registry_build_and_push.yaml"
    end
  end

  config.vm.define "hellospa" do |hellospa|
    hellospa.vm.hostname = "hellospa"
    hellospa.vm.synced_folder "./playbooks", "/vagrant/playbooks"
    hellospa.vm.network "private_network", ip: HELLOSPA_IP
    hellospa.vm.network "forwarded_port", guest: 80, host: HOST_APP_PORT, id: "hellospa"
    hellospa.vm.provider :virtualbox do |vb|
        vb.memory = HELLOSPA_MEM
        vb.cpus = HELLOSPA_CPU
    end
    hellospa.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "playbooks/hellospa.yaml"
      ansible.become = true
      ansible.become_user = "root"
      ansible.extra_vars = { registry_ip: REGISTRY_IP }
    end
    hellospa.vm.provision "docker" do |docker|
      docker.run "registry:5000/hello-spa", args: "-p 80:80 --name hellospa"
    end
  end
end
