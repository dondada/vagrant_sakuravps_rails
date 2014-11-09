
Vagrant.configure("2") do |config|
 
  config.vm.define :web do |web_config|
    web_config.vm.box = "centos64"
    #web_config.vm.box_url = "http://files.vagrantup.com/precise64.box"
    web_config.vm.box_url = "http://developer.nrel.gov/downloads/vagrant-boxes/CentOS-6.5-x86_64-v20140110.box"
    web_config.vm.network :private_network, ip: "33.33.33.33"
    web_config.vm.network :forwarded_port, guest: 3000, host: 8080
 
    web_config.vm.hostname = "develop-centos"
 
    web_config.vm.provider :virtualbox do |vb|
      vb.memory = 1024
    end

    config.vm.provision :ansible do |ansible|
      ansible.playbook = "provision/playbook_vagrant.yml"
      ansible.inventory_path = "provision/dev_hosts"
      ansible.limit = 'all'
      ansible.verbose = 'vvv'
    end
  end
 
end
