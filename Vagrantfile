Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: <<-SHELL
    export DEBIAN_FRONTEND=noninteractive
    apt-get update
    apt-get install -y sudo python3 python3-pip
    pip3 install ansible==2.9.27  # Using a stable version of Ansible
    echo 'vagrant ALL=(ALL) NOPASSWD:ALL' > /etc/sudoers.d/vagrant
    chmod 0440 /etc/sudoers.d/vagrant
  SHELL

  config.ssh.insert_key = false
  config.ssh.private_key_path = [
    "~/.vagrant.d/insecure_private_key",
    "#{ENV['HOME']}/.vagrant.d/insecure_private_key"
  ]

  # Nginx VM
  config.vm.define "nginx" do |nginx|
    nginx.vm.box = "ubuntu/bionic64"
    nginx.vm.hostname = "nginx"
    nginx.vm.network :private_network, ip: "192.168.56.61"
    nginx.vm.network "forwarded_port", guest: 80, host: 8080
    nginx.vm.provider :virtualbox do |v|
      v.name = "nginx"
      v.memory = 1024
      v.cpus = 1
    end
  end

  # Apache VM
  config.vm.define "apache" do |apache|
    apache.vm.box = "ubuntu/bionic64"
    apache.vm.hostname = "apache"
    apache.vm.network :private_network, ip: "192.168.56.62"
    apache.vm.network "forwarded_port", guest: 80, host: 9000
    apache.vm.provider :virtualbox do |v|
      v.name = "apache"
      v.memory = 1024
      v.cpus = 1
    end
  end

  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "playbook.yml"
    ansible.become = true
    ansible.compatibility_mode = "2.0"
    ansible.extra_vars = {
      ansible_python_interpreter: "/usr/bin/python3"
    }
  end
end