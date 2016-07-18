# -*- mode: ruby -*-
# vi: set ft=ruby :

VIRTUAL_MACHINES = {
  :node1 => {
    :ip => '192.168.90.51',
    :hostname => 'node1',
  },
  :node2 => {
    :ip => '192.168.90.52',
    :hostname => 'node2',
  },
  :master => {
    :ip => '192.168.90.50',
    :hostname => 'master',
  },
}

# Autoinstall for vagrant-hostmanager plugin
# See https://github.com/smdahlen/vagrant-hostmanager
unless Vagrant.has_plugin?('vagrant-hostmanager')
  system('vagrant plugin install vagrant-hostmanager') || exit!
  exit system('vagrant', *ARGV)
end

Vagrant.configure(2) do |config|
  # Global configuration for all boxes
  config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"
  config.hostmanager.enabled = false
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true

  # VM specific configurations
  VIRTUAL_MACHINES.each do |name, cfg|
    config.vm.define name do |vm_config|
      vm_config.vm.box = 'ubuntu/trusty64'
      vm_config.vm.hostname = cfg[:hostname]
      vm_config.hostmanager.aliases = "www.#{cfg[:hostname]}"
      vm_config.vm.network :private_network, ip: cfg[:ip]

      vm_config.vm.provider :virtualbox do |vb|
        vb.name = cfg[:hostname]
      end

      vm_config.vm.provision :hostmanager

      # Additional rules for master
      if name == :master
        vm_config.vm.provider :virtualbox do |vb|
          vb.memory = 2048
        end
        # Start shell provisioning for master
        vm_config.vm.provision :shell,
          # Use `privileged: false`, the script is initially executed from the `vagrant` user, `sudo`-ing when needed
          # Allows to set StackStorm credentials for both `vagrant` and `root` users in `~/.st2`
          privileged: false,
          path: "https://stackstorm.com/packages/install.sh",
          args: [
            '--user=demo',
            '--password=demo'
          ]
        vm_config.vm.provision :shell, path: "ansible.sh"
        vm_config.vm.provision :shell, path: "ansible-galaxy.sh"
        vm_config.vm.provision :shell, path: "ansible-vault.sh"
        vm_config.vm.provision :shell, path: "ansible-playbook.sh"
      end
    end
  end
end
