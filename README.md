# Sysadmin Toolbelt for VM deploy automatization

## What this playbook do? ##

This playbook automates creating and preparing new Virtual Machines. Its specially designed for user and ssh access management.

## User management ##
* Creating and removing users with or without sudo privileges
* Managing user privileges
* Setup ssh connection for users (placing public key on remote hosts)
* Setup MOTD for informational purposes

## Ubuntu 16.04 special treatment ##

Ubuntu 16.04 needs Python2.7 (default is 3.0) to run ansible. There is special role "install_python2.7" for it.
This role contains only Python installer which downgrade version to 2.7

## Prepare environment ##

Before we start we need to install a few things:

1. [Vagrant](https://www.vagrantup.com/docs/installation/ "vagrant")
2. [VirtualBox](https://www.virtualbox.org/wiki/Downloads "virtualbox")
3. [VirtualBox  Extension Pack](http://download.virtualbox.org/virtualbox/5.1.0/Oracle_VM_VirtualBox_Extension_Pack-5.1.0-108711.vbox-extpack "virtualbox extension pack")
4. [Ansible](http://docs.ansible.com/ansible/intro_installation.html "ansible")
5. Clone this repo

## How to create dev environment on local machine ##

First of all we need to create our hosts. Our [Vagrantfile](https://github.com/zerodowntime/elk_stack/blob/master/Vagrantfile "Vagrantfile") will create four nodes:

```bash
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

To deploy creating our three hosts we need to execute

```bash
vagrant up
```

Next we can see the result

```bash
vagrant status
```

which should look simmilar to this

```bash
Current machine states:

srv-ubuntu14                     running (virtualbox)
srv-ubuntu16                     running (virtualbox)

If all went well we can now deploy our playbook by executing

```bash
ansible-playbook -i dev/hosts setup_ssh_server.yml
```

## How it works ##

First step is install and configuration ssh.
Role for that is called set_ssh and contains:
* installation openssh-server on remote hosts
* disabling root login and password authentication in sshd_config file
* restarting ssh daemon

All user_* roles take information about users from dev/host_vars/"host IP file" which contains 4 user sets:
* regular_users (all users you want to create)
* sudo_users ( regular users you want to promote to sudo)
* remove_users (all types of users you want to remove)
* takesudo_users (users you want to degrade from sudo to regular)

Role user_create creates regular users, if there is user in "sudo_users" set then role will promote this user to sudo.This role also creates .ssh folder in home directories and copies public ssh keys from "keys" folder to remote hosts.

Role set_motd copies prepared motd file stored in roles/set_motd/templates/motd.j2 to remote hosts in /etc/motd and also uninstalls default motd (landscape-common package)

