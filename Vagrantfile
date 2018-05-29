# -*- mode: ruby -*-
# vi: set ft=ruby :
Dotenv.load! # vagrant plugin install dotenv

Vagrant.configure("2") do |config|
  config.vm.box = "relativkreativ/centos-7-minimal"
  # config.vm.box = "geerlingguy/centos7"
  # config.ssh.keep_alive = true

  config.vm.network :private_network, ip:"192.168.33.10"
  config.vm.hostname = "localhost"

  config.vm.network :forwarded_port, guest: 8001, host: 8001 # gitlab http
  config.vm.network :forwarded_port, guest: 8002, host: 8002 # gitlab https
  config.vm.network :forwarded_port, guest: 8003, host: 8003 # postgres
  config.vm.network :forwarded_port, guest: 8004, host: 8004 # jenkins http
  config.vm.network :forwarded_port, guest: 8005, host: 8005 # jenkins jnlp
  config.vm.network :forwarded_port, guest: 8006, host: 8006 # ldap
  config.vm.network :forwarded_port, guest: 8007, host: 8007 # phpldapadmin http
  config.vm.network :forwarded_port, guest: 8008, host: 8008 # nexus http
  config.vm.network :forwarded_port, guest: 8009, host: 8009 # nexus docker repository
  
  config.vm.synced_folder ".", "/vagrant", :create => true, :owner => 'vagrant', :group => 'vagrant', :mount_options => ['dmode=777', 'fmode=600']

  config.vm.provider "virtualbox" do |vm|
    # vm.gui = true
    vm.customize ["modifyvm", :id, "--memory", "4096"]
  end

  config.vm.provision :shell, :inline => <<-EOL
    # if [ ! -e /vagrant/ansible/env_vars.yml ]; then
      echo '---' > /vagrant/ansible/env_vars.yml
      echo 'postgresql_for_gitlab_pass: "#{ENV['postgresql_for_gitlab_pass']}"' >> /vagrant/ansible/env_vars.yml
      echo 'gitlab_smtp_user: "#{ENV['gitlab_smtp_user']}"' >> /vagrant/ansible/env_vars.yml
      echo 'gitlab_smtp_pass: "#{ENV['gitlab_smtp_pass']}"' >> /vagrant/ansible/env_vars.yml
      echo 'openldap_admin_pass: "#{ENV['openldap_admin_pass']}"' >> /vagrant/ansible/env_vars.yml
    # fi
  EOL

  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "playbook_00_init.yml"
    ansible.provisioning_path = "/vagrant/ansible"
    ansible.install = true
    ansible.verbose = false

    ansible.inventory_path = "/vagrant/hosts"
    ansible.limit = "all"
  end
end
