# Self-contained Ansible distribution

Ansible package with required python modules. No need to install, just download, unpack and use. The main idea of this package is to run Ansible playbooks on local machine

This is a BRANCH! For the original Repository head over to https://github.com/ownport/portable-ansible

However, this BRANCH still has Python2 support for Ansible 2.9.27 (release 2021-10-11) for people that still can not go to python3.


## Included in the distribution

Version: 0.4.3

| Package    | Version |
| --------   | ------- |
| ansible    | 2.9.27  |
| jinja2     | 2.11.3  |
| PyYAML     | 5.4.1   |
| paramiko   | 2.8.0   |
| six        | 1.13.0  |
| cryptography | 3.3.2 |
| pyasn1     | 0.4.8   |
| asn1crypto | 1.4.0   |
| bcrypt     | 3.1.7   |
| cffi       | 1.15.0  |
| PyNaCl     | 1.4.0   |
| markupsafe | 1.1.1   |

## How to install and use

You just need to download latest version of portable-ansible tarball (.tar.bz2) from
Releases page https://github.com/FBnil/portable-ansible/releases/tag/v0.4.3 and unpack the files

```sh
$ wget https://github.com/FBnil/portable-ansible/releases/download/v0.4.3/portable-ansible-v0.4.3-py2.tar.bz2 -O ansible.tar.bz2
$ wget https://github.com/FBnil/portable-ansible/releases/download/v0.4.3/ansible.cfg -O ansible.cfg
$ tar -xjf ansible.tar.bz2
$ mkdir roles
$ python ansible localhost -m ping
 [WARNING]: provided hosts list is empty, only localhost is available

localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```
If you need to run ansible playbooks, after having extracted the tarball contents:

```sh
$ ln -s ansible ansible-playbook
$ python ansible-playbook playbook.yml
```

In the same fashion, to have all the ansible commands, run:
```
for l in config console doc galaxy inventory playbook pull vault;do
  if [ ! -L "ansible-$l" ];then
          ln -s ansible ansible-$l
  fi
  alias ansible-$l="ANSIBLE_CONFIG=$PWD/ansible.cfg python $PWD/ansible-$l"
done
alias ansible="ANSIBLE_CONFIG=$PWD/ansible.cfg python $PWD/ansible"
```

TIP: Put the content in a file and source the file each session to have your environment:

```
MYPATH=/home/a/path/to/your/installation/
for l in config console doc galaxy inventory playbook pull vault;do
  alias ansible-$l="ANSIBLE_CONFIG=$MYPATH/ansible.cfg python $MYPATH/ansible-$l"
done
alias ansible="ANSIBLE_CONFIG=$MYPATH/ansible.cfg python $MYPATH/ansible"
```

and now you can create a role like this:
```sh
$ cd roles
$ ansible-galaxy init mynewrole
```
and the cowsay is gone (can be enabled back in your ansible.cfg)


Note that The last cryptography file that supports Python 2.7 contains an obnoxious warning block that can be removed with this playbook:

```
---
- name: Remove pesky warning about deprecated Python2 in cryptography
  hosts: localhost
  tasks:

    - name: "wipe code from python file"
      replace:
        regexp: 'if sys.version_info[\s\S]*(\s*warnings.warn\([\s\S]*\))'
        #regexp: 'warnings.warn'
        replace: ''
        path: "./ansible/cryptography/__init__.py"
    - name: "Remove compiled pyc file"
      file:
        path: "./ansible/cryptography/__init__.pyc"
        state: absent
      ignore_errors: yes
```


## Supporting additional python packages

Install python packages into `ansible/extras` directory
```
pip install -t ansible/extras <package>
```
or 
```
pip install -t ansible/extras -r requirements.txt
```

Instead of installing the python packages to `ansible/extras`, you can also install them in user directory to be available for ansible:
```
pip install --user -r requirements.txt
```

## Hints

make aliases to portable ansible directory. In the examples below `portable-ansible` installed in `/opt` directory
```
ln -s /opt/ansible /opt/ansible-playbook
```

## Testing

```sh
python3 /opt/ansible-playbook -i 172.17.0.2,  ~/playbooks/remote-via-ssh-key.yaml
```

```sh
python3 /opt/ansible-playbook -i 172.17.0.2, -c paramiko ~/playbooks/remote-via-username-and-password.yaml  --ask-pass
```

## For developers

to create tarball with required packages just run

For python2
```
$ make tarball-py2
```
For python3
```
$ make tarball-py3
```
For both python versions
```
$ make tarballs
```

## Changelog

All notable changes to this project will be documented in the file CHANGELOG.md


## Links

- [ansible/ansible](https://github.com/ansible/ansible) Ansible is a radically simple IT automation platform that makes your applications and systems easier to deploy. Avoid writing scripts or custom code to deploy and update your applications— automate in a language that approaches plain English, using SSH, with no agents to install on remote systems. http://ansible.com/
- [ansible/ansible-modules-core](https://github.com/ansible/ansible-modules-core) Ansible modules - these modules ship with ansible
- [ansible/ansible-modules-extras](https://github.com/ansible/ansible-modules-extras) Ansible extra modules - these modules ship with ansible
