# Playground

`vagrant` `virtualbox` backed `docker` `swarm` environment provisioned with `ansible`.

## Requirements

### Software
- [ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html "ansible")
- [vagrant](https://www.vagrantup.com/docs/installation/ "vagrant")
- [virtualbox](https://www.virtualbox.org/wiki/Downloads "virtualbox")

### Hardware

Each node of the `virtualbox` backed cluster is configured with 4GB RAM and 2CPU's. Update the [provider configuration](https://www.vagrantup.com/docs/providers/configuration.html#provider-configuration) to change the defaults.

## Build

Update the `ansible_eth0` variable with the interface that the `swarm` manager should advertise itself on: `sed -i -e "s/ansible_enp0s8/ansible_eth0/g" playbooks/deploy.yml`

Build the environment using `vagrant`: `vagrant up`

The `openssh-server:latest` image will be loaded on all nodes if the file exists at `/vagrant/openssh-server/openssh-server.tar.gz` during the provisioning.

**Note**: the `swarm` nodes are provisioned in parallel with `ansible` using the [tips & tricks](https://www.vagrantup.com/docs/provisioning/ansible.html#tips-and-tricks).

## Usage

1. connect to the manager node: `vagrant ssh node1`
2. create a passwordless `ssh` rsa key pair for the `vagrant` user i.e.: `ssh-keygen -N "" -m PEM -t rsa -b 4096 -C "vagrant@bastion" -f ~/.ssh/id_rsa`
3. set the `REGISTRY_HTTP_SECRET` environment variable and deploy the bastion stack
```bash
cd /vagrant
REGISTRY_HTTP_SECRET=$(openssl rand -base64 32) docker stack deploy --compose-file docker-compose.yml bastion
```
4. test the `openssh-server` connections: `ssh -p 443 ubuntu@localhost`
5. test the `registry` connections:
```bash
docker tag openssh-server:latest docker.localhost/openssh-server:latest
docker push docker.localhost/openssh-server:latest
```
