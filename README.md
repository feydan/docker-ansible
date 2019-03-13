# Ansible Docker Image

Docker Image of Ansible for executing ansible commands against an externally mounted Ansible project. Based on [philm/ansible_playbook](https://github.com/philm/ansible_playbook) and [walokra/docker-ansible-playbook](https://github.com/walokra/docker-ansible-playbook)

## Build

```
docker build -t feydan/docker-ansible .

EXPORT PATH="$PATH:$(pwd)/bin"
```
You can add `PATH="$PATH:<full path to this project>/bin"` to your ~/.bashrc or ~/.bash_profile to have `ansible` and `ansible-playbook` available in new terminals from any directory.

### Test

```
$ ansible --version
ansible 2.5.0
  config file = None
  configured module search path = [u'/ansible/library']
  ansible python module location = /usr/lib/python2.7/site-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.15 (default, Aug 22 2018, 13:24:18) [GCC 6.4.0]

$ ansible-playbook --version
ansible-playbook 2.5.0
  config file = None
  configured module search path = [u'/ansible/library']
  ansible python module location = /usr/lib/python2.7/site-packages/ansible
  executable location = /usr/bin/ansible-playbook
  python version = 2.7.15 (default, Aug 22 2018, 13:24:18) [GCC 6.4.0]
```

## Running Ansible Playbook

```
ansible-playbook PLAYBOOK_FILE
```

For example, assuming your project's structure follows [best practices](http://docs.ansible.com/ansible/playbooks_best_practices.html#directory-layout), the command to run ansible-playbook from the top-level directory would look like:

```
ansible-playbook playbooks/site.yml
```

Ansible playbook variables can be added after the playbook name.
```
ansible-playbook -i host_vars/prod.yml playbooks/site.yml
```

## Support for sensitive data

You may want to have access to your local ssh keys, aws credentials, ansible-vault password file, additional pem files, and/or an inventory directory separate from your project.

The `bin2` directory provides support for all of these.  Add this to your path instead of `bin` to use it.  

Add any of the following:

### Ansible Vault Password File
Add your vault password to `~/.ansible/vault.pass` or create it (example below).
```
mkdir ~/.ansible

# create large random password for ansible-vault
openssl rand -base64 2048 > ~/.ansible/vault.pass
```

### SSH Keys

Ensure you have your keys saved as `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub`.

### AWS Credentials

Ensure you have aws credentials saved in `~/.aws/credentials` with te following format:
```
[default]
aws_access_key_id = <access key>
aws_secret_access_key = <secret key>
```

### Inventory directory

Create a docker volume called `inventory_volume`
```
docker volume create --driver local --opt type=none --opt device=<local inventory directory> --opt o=bind inventory_volume
```
The volume is accessible within the container at /inventory:
```
ansible-playbook -i /inventory/host_vars/prod.yml playbooks/site.yml
```

### Pems directory

Create a docker volume called `pems_volume`
```
docker volume create --driver local --opt type=none --opt device=<local pems directory> --opt o=bind pems_volume
```
The volume is accessible within the container at /pems:
```
# group_vars/aws_ec2.yml
---
ansible_connection: ssh
ansible_user: ec2-user
ansible_ssh_private_key_file: /pems/<your.pem>
```              
