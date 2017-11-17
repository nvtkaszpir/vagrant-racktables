# About

This is a Vagrant environment which installs racktables for development purposes.

Right now this is work in progress and many things can change.

# Known limitations

- tested with vagrant 1.9.7 and 2.0.1 with LXC and Libvirt provider
- vagrant box tested under Ubuntu 16.04 with Apache 2.4 and php-fpm 7.0
- REALLY dumb credentials, what did you expect from development setup? ;)
- ansible playbook uploads racktables app to virtual machine, does not use vagrant auto mounted directories

# Requirements

- locally installed [Vagrant](https://www.vagrantup.com/)
- locally installed [Ansible 2.2.1.x](http://docs.ansible.com/ansible/latest/intro_installation.html)

# Initial start

1. check out this repo to `vagrant-racktables` recursively:

```bash
git clone --recursive http://... vagrant-racktables
```


2. Start up virtual machine:

```bash
vagrant up
```

3. Go to web interface, fill in entries for user/pass, and to process creation of the racktables secret.php and db install.
4. When requested by installer (in step 4) run vagrant provisioner to set permissions on secret.php

```bash
vagrant provision --provision-with=app
````

5. Continue install finalization, it should redirect you to the login form
6. Log in to application.

# Destroy and recreate vm

Delete vm with all data:

```bash
vagrant destroy -f
```

# Tricks

## Wipe without vm destruction

Wipe racktables secret.php without destroying vm:

```bash
ansible-playbook -i .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory -e racktables_reinstall=true play_racktables.yml
```
.. but notice that db scripts may fail.

## Ansible vars

See `group_vars/` directory.
