# K8s infrastructure [![Code Quality](https://github.com/autoreduction/k8s-infra/actions/workflows/code_quality.yml/badge.svg?branch=main)](https://github.com/autoreduction/k8s-infra/actions/workflows/code_quality.yml)

Container infrastructure on which Autoreduction is deployed.

## VM allocation

- Login to the [OpenStack dashboard](https://openstack.stfc.ac.uk/)
- Select *Launch Instance*
- *Details*
  - *Instance Name* = `ar-k8s`
  - *Count* = 3
- *Source* = *ubuntu-focal-20.04-nogui*
- *Flavour* = *c3.large*
- *Security Groups*
  - Remove *Default*
  - Add *use-host-firewall*
- *Key pair*
  - Ensure the public key associated with you in [users.yml](./group_vars/all/users.yml) is added
- Select *Launch Instance*

## Deployment

When asked for a sudo password, use your Fed ID password.
The location you choose for `<SECURE PATH>` should actually be somewhere secure, if in doubt, use `/tmp` and put up with having to run step 6 again to get another copy.

1. Ensure the [inventory](./inventory.ini) is up to date
2. Ensure the [user](./group_vars/all/users.yml) configuration is up to date
3. `ansible-galaxy install -r requirements.yml`
4. `ansible-playbook -K system.yml -e system_reboot=true`
5. `export KUBECONFIG=/<SECURE PATH>/kubeconfig`
6. `ansible-playbook -K k3s.yml`
7. `ansible-playbook -K services.yml -e longhorn_generate_iscsi_iname=true`

## Getting `kubeconfig`

1. `ansible-galaxy install -r requirements.yml`
2. `export KUBECONFIG=/<SECURE PATH>/kubeconfig`
3. `ansible-playbook -K k3s.yml -l k8s_controller`j
4. `kubectl cluster-info`
