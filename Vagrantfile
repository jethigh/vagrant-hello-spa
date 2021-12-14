IMAGE_NAME = "generic/centos7"
N = 2

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.vm.box_version = "3.5.2"

  config.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 4
  end
  config.vm.provision "docker"

  config.vm.define "registry" do |registry|
    registry.vm.box = IMAGE_NAME
    registry.vm.hostname = "registry"
    registry.vm.synced_folder "./", "/vagrant/"
    registry.vm.network "private_network", ip: "192.168.50.90"
    registry.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "kubernetes-setup/registry_prepare.yaml"
      ansible.become = true
      ansible.become_user = "root"
    end
    registry.vm.provision "docker" do |docker|
      docker.run "registry:2", args: "-p 5000:5000 --restart always --name registry -v '/app/certs:/certs' \
                                      -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/registry.crt \
                                      -e REGISTRY_HTTP_TLS_KEY=/certs/registry.key"
    end
    registry.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "kubernetes-setup/registry_build_and_push.yaml"
    end
  end
 
  config.vm.define "k8s-master" do |master|
    master.vm.box = IMAGE_NAME
    master.vm.network "private_network", ip: "192.168.50.10"
    master.vm.network "forwarded_port", guest: 30080, host: 8080, id: "hellospa"
    master.vm.hostname = "k8s-master"
    master.vm.synced_folder "./", "/vagrant/"
    master.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "kubernetes-setup/install_kubeadm_kubelet_kubectl.yml"
      ansible.extra_vars = {
        node_ip: "192.168.50.10",
        ARCH: "amd64",
        CNI_VERSION: "v0.8.2",
        CRICTL_VERSION: "v1.22.0",
        RELEASE: "v1.22.4",
        UNIT_VERSION: "v0.4.0"
      }
    end
    master.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "kubernetes-setup/initialize_master.yml"
      ansible.extra_vars = {
        node_ip: "192.168.50.10",
        }
    end
  end
 
  config.vm.define "node-1" do |node|
    node.vm.box = IMAGE_NAME
    node.vm.network "private_network", ip: "192.168.50.11"
    node.vm.hostname = "node-1"
    node.vm.synced_folder "./", "/vagrant/"
    node.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "kubernetes-setup/install_kubeadm_kubelet_kubectl.yml"
      ansible.extra_vars = {
        node_ip: "192.168.50.11",
        ARCH: "amd64",
        CNI_VERSION: "v0.8.2",
        CRICTL_VERSION: "v1.22.0",
        RELEASE: "v1.22.4",
        UNIT_VERSION: "v0.4.0"
      }
    end
    node.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "kubernetes-setup/join_node.yml"
    end
  end

  config.vm.define "node-2" do |node|
    node.vm.box = IMAGE_NAME
    node.vm.network "private_network", ip: "192.168.50.12"
    node.vm.hostname = "node-2"
    node.vm.synced_folder "./", "/vagrant/"
    node.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "kubernetes-setup/install_kubeadm_kubelet_kubectl.yml"
      ansible.extra_vars = {
        node_ip: "192.168.50.12",
        ARCH: "amd64",
        CNI_VERSION: "v0.8.2",
        CRICTL_VERSION: "v1.22.0",
        RELEASE: "v1.22.4",
        UNIT_VERSION: "v0.4.0"
      }
    end
    node.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "kubernetes-setup/join_node.yml"
    end
    node.vm.provision "shell", inline: "kubectl create -f /vagrant/kubernetes-setup/deployment.yml"
    node.vm.provision "shell", inline: "kubectl create -f /vagrant/kubernetes-setup/service.yml"
  end

end
