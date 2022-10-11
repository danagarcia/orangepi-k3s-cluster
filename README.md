# Orange Pi Kubernetes Cluster

![Orange Pi Cluster](/img/cluster_picture.jpg?raw=true "Orange Pi Cluster")

## Purpose
Provide a collection of resources for people to setup a kubernetes cluster with Orange Pi single board computers.

## Prerequisites
- Orange Pi(s) running Ubuntu
    - NOTE: You can use as few as 2. In my setup, I used 5.
- A system with Ansible installed
    - NOTE: See Ansible's [Installation Guide](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installation-guide)

## Setup
In order for ansible to connect to your Orange Pi systems you will need to setup a couple of things:

1. Network
2. Access

To configure the network for you Orange Pi(s) you can use `nmtui`, I recommend using the onboard ethernet connection as I experienced intermittent issues with WiFi.

After network is setup, creating an account for Ansible is simple. SSH to each Orange Pi and complete the following steps.

1. `sudo adduser adm_ansible`
    - Type in the password and user details
2. `sudo adduser adm_ansible sudo`
3. `exit`

Now that we have a Ansible user account on each machine, we need to create and add our SSH key for easy and secure access. On your local system open Terminal (MacOS and Linux) or PowerShell (Windows) and complete the following steps.

1. `ssh-keygen -t ed25519 -c "adm_ansible"`
    - NOTE: I recommend naming your key id_adm_ansible
2. `ssh-copy-id -i <path to id_adm_ansible.pub> adm_ansible@<machine name>`
    - Replace <path to id_adm_ansible.pub> with the full path to the ssh key created in Step 1, adding .pub to the end.
    - Repeat for each Orange Pi system, replacing <machine name> with each Orange Pi's system name or ip address.
3. `ssh-add -K <path to id_adm_ansible>`
    - Replace <path to id_adm_ansible.pub> with the full path to the ssh key created in Step 1.

Now the final step is to update the inventory file , `src/production.yml`, to fit your configuration. Simply add or remove hosts to the `master` or `nodes` host groups. Note that you should only have a single master host.

## Usage
After setup is complete using the solution is simple, run the following commands in Terminal (MacOS and Linux) or PowerShell (Windows).

1. `cd src`
2. `ansible-playbook cluster-build.yml -i production.yml --ask-become-pass`
    - NOTE: You will be prompted for the password for adm_ansible for sudo purposes.

Now remote into the master node and run the following command to verify all nodes are in the cluster.

1. `sudo kubectl get nodes -o wide`
    ![Cluster Nodes](/img/cluster_nodes.png?raw=true "Cluster Nodes")