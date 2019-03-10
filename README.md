# Ansible Docker Image

Docker Image of Ansible for executing ansible commands against an externally mounted Ansible project. Based on [philm/ansible_playbook](https://github.com/philm/ansible_playbook) and [walokra/docker-ansible-playbook](https://github.com/walokra/docker-ansible-playbook)

## Build

```
docker build -t feydan/ansible .

EXPORT PATH="$PATH:$(pwd)/bin"
```
You can add this export line to your ~/.bashrc or ~/.bash_profile to have `ansible` and `ansible-playbook` available in new terminals

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

Ansible playbook variables can simply be added after the playbook name.
```
ansible-playbook -i host_vars/prod.yml playbooks/site.yml
```

## SSH Keys

If Ansible is interacting with external machines, you'll need to mount an SSH key pair for the duration of the play.  Modify bin/ansible-docker if your keys are different:

```
docker run --rm -it \
    -v ~/.ssh/id_rsa:/root/.ssh/id_rsa \
    -v ~/.ssh/id_rsa.pub:/root/.ssh/id_rsa.pub \
    -v $(pwd):/ansible/playbooks \
    feydan/ansible site.yml
```

## Ansible Vault

If you've encrypted any data using [Ansible Vault](http://docs.ansible.com/ansible/playbooks_vault.html), you can decrypt during a play by either passing **--ask-vault-pass** after the playbook name, or pointing to a password file. For the latter, you can mount an external file in bin/ansible-docker:

```
docker run --rm -it -v $(pwd):/ansible/playbooks \
    -v ~/.vault_pass.txt:/root/.vault_pass.txt \
    feydan/ansible \
    site.yml --vault-password-file /root/.vault_pass.txt
```                    
