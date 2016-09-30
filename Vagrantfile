# -*- mode: ruby -*-
# vi: set ft=ruby :

# Define virtualbox boxes
boxes = [
    {
        :name => "srv-ubuntu14",
        :eth1 => "192.168.50.100",
        :mem => "1024",
        :cpu => "1",
        :os => "ubuntu/trusty64",
    },
    {
        :name => "srv-ubuntu16",
        :eth1 => "192.168.50.101",
        :mem => "1024",
        :cpu => "1",
        :os => "ubuntu/xenial64",
    }
]

# Lets check what kind of SSH key you have generated and upload it on vm
rsa_key = File.expand_path('~') + "/.ssh/id_rsa.pub"
dsa_key = File.expand_path('~') + "/.ssh/id_dsa.pub"

if FileTest.exists?(rsa_key)
  key = rsa_key
elsif  FileTest.exists?(dsa_key)
  key = dsa_key
end

Vagrant.configure(2) do |config|

  boxes.each do |opts|
    config.vm.define opts[:name] do |config|
      config.vm.box = opts[:os]

      #config.ssh.insert_key = false
      ssh_public_key = File.read("#{key}")

      config.vm.network "private_network", ip: opts[:eth1]
      config.vm.hostname = opts[:name]
      config.vm.provider "virtualbox" do |v|
        v.memory = opts[:mem]
        v.cpus = opts[:cpu]
        v.name = opts[:name]
      end

    config.vm.provision "shell", inline: <<-SHELL
        echo "#{ssh_public_key}" >> /home/ubuntu/.ssh/authorized_keys
      SHELL

      #config.vm.provision :ansible do |ansible|
      #    ansible.playbook = "pre_provision.yml"
      #    ansible.inventory_path = "development/hosts"
      #    ansible.verbose = "v"
      #end
    end
  end
end
