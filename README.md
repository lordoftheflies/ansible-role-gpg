Ansible Role - GPG Key
=========

[![Build Status](https://travis-ci.org/vkill/ansible-role-gpg_key.svg?branch=master)

](https://travis-ci.org/vkill/ansible-role-gpg_key)

Generate, import, trust GPG Key on Linux.

After running a temporary passphrase for the generated key pair is located in `cat /tmp/passwordfile` on the controller.

Requirements
------------

gpg, 

Role Variables
--------------

TODO

Dependencies
------------

* https://github.com/cjsteel/ansible-role-epel # CentOS systems
* https://github.com/cjsteel/ansible-role-haveged
* https://github.com/cjsteel/ansible-role-gpg

## Example Vagrantfile

```shell
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|

#  config.vm.define "jessie" do |jessie|
#    jessie.vm.box = "debian/jessie64"
#  end

#  config.vm.define "precise" do |precise|
#    precise.vm.box = "ubuntu/precise64"
#  end

#  config.vm.define "trusty" do |trusty|
#    trusty.vm.box = "ubuntu/trusty64"
#  end

  config.vm.define "xenial" do |xenial|
    xenial.vm.box = "ubuntu/xenial64"
  end

  config.vm.define "centos7" do |centos7|
    centos7.vm.box = "centos/7"
  end

#  config.vm.define "fedora22" do |fedora22|
#    fedora22.vm.box = "box-cutter/fedora22"
#  end

  config.vm.provision "ansible" do |ansible|
      ansible.playbook = 'tests/test.yml'
  end
  config.vm.synced_folder ".", "/vagrant", disabled: true
end
```



## Example Vagrant Testing Playbook

```yaml
--- # tests/test.yml
- hosts: all
  become: true
  gather_facts: false

  pre_tasks:

  - set_fact: ansible_user='ubuntu'
  - name: "Ensure that our target has python2.x for Ansible gather facts (Sometimes Ubuntu 16.04 does not)"
    raw: bash -c "test -e /usr/bin/python || (apt -qqy update && apt install -qqy python-minimal)"
    register: output
    changed_when:
    - output.stdout != ""
    - output.stdout != "\r\n"
    ignore_errors: yes


  - name: "Now that we are sure Python 2.7 is installed we can gather our facts"
    setup:

  roles:

    - epel
    - haveged
    - { role: gpg, ansible_user: ubuntu }

- hosts: all
  become: false
  gather_facts: true
  roles:

    - pass
```

License
-------

MIT / BSD

Author Information
------------------

vkill <vkill.net@gmail.com>

&copy; 2016
