StackStorm with Ansible on Vagrant demo
===========

![StackStorm with Ansible on Vagrant demo](http://i.imgur.com/wwcFk8t.png)

> Part of the blog: [New In StackStorm: Ansible Integration](https://stackstorm.com/2015/06/05/new-in-stackstorm-ansible-integration/)

### Introduction
This is quick demonstration of the [StackStorm](http://stackstorm.com/) automation platform running with [Ansible configuration management pack](https://github.com/StackStorm/st2contrib/tree/master/packs/ansible).

It will get you up and running with `master` VM running all St2 components as well as Ansible.
Additionally, it installs 2 clean Ubuntu VMs: `node1`, `node2` and performs ansible commands against them.

### Instructions
To provision the environment run:

    vagrant up

![StackStorm Ansible Integration](https://cloud.githubusercontent.com/assets/1533818/16920519/97d4a76e-4d16-11e6-817e-1edcb2e16de0.gif)

> Check the results of performed commands in StackStorm control panel:  
https://www.master/
username: `demo`
password: `demo`

Don't forget to visit: 
* http://www.node1/
* http://www.node2/

### Explanation
Everything below is performed as part of Vagrant provision:

* Install st2 platform and verify installation 
* Install st2 `ansible` pack from remote repository
* Copy ansible configuration files from vagrant shared directory into '/etc/ansible' on `master`
* Test `ansible.command_local` actions ([ad-hoc](http://docs.ansible.com/intro_adhoc.html) ansible command) against local `master` machine
* Test `ansible.command` actions ([ad-hoc](http://docs.ansible.com/intro_adhoc.html) ansible command) against both local `master` and remote `node1` `node2` machines
* Test `ansible.galaxy` actions, install, list and then remove roles installed from [Ansible Galaxy](https://galaxy.ansible.com/)
* Test `ansible.vault` actions, encrypt/decrypt playbooks and run them
* Test `ansible.playbook` action, run [nginx.yml playbook](ansible/playbooks/nginx.yml) against all machines
* Let the nginx on latest node greet your cat (what?!), have fun

Some of the commands: 
```sh
# Run simple ansible.command locally
st2 run ansible.command_local args='echo $TERM'

# Run 'hostname -i' ansible.command on all machines (master and nodes) 
st2 run ansible.command hosts=all args='hostname -i'

# Ping all machines in 'nodes' group
st2 run ansible.command hosts=nodes module_name=ping

# Install nginx via playbook on all machines 
st2 run ansible.playbook playbook=/etc/ansible/playbooks/nginx.yml

# Run nginx playbook on latest node machine, set nginx index.html welcome message
st2 run ansible.playbook playbook=/etc/ansible/playbooks/nginx.yml extra_vars='welcome_name=Tom' limit='nodes[-1]'

...
```

For all commands executed see: [`ansible.sh`](ansible.sh), [`ansible-galaxy.sh`](ansible-galaxy.sh), [`ansible-vault.sh`](ansible-vault.sh) and [`ansible-playbook.sh`](ansible-playbook.sh),
which are usual Vagrant shell provisioner scripts.

### Related Resources
* [Ansible & ChatOps w StackStorm Tutorial & Vagrant demo :rocket:](http://stackstorm.com/2015/06/24/ansible-chatops-get-started-%f0%9f%9a%80/)
* [Ansible integration Pack](https://github.com/StackStorm/st2contrib/tree/master/packs/ansible)
