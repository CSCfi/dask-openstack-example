# Ansible Openstack example to install nginx

This repository will use Ansible to create some instances (default 3) in Openstack, install nginx, and copy some files to it. It does not use any external tool (like Terraform or OpenStack Heat), only Ansible.

## Requirements

In order to run this code you need to first install some tools into your computer.

1. First, you need ansible to be installed. There are several methods to install ansible, one of them being `pip`:

	```sh
	pip install ansible
	```

1. You need the [openstack.cloud collection](https://docs.ansible.com/ansible/latest/collections/openstack/cloud/index.html). Type this command:

	```sh
	ansible-galaxy install -r requirements.yml
	```

1. The latest version of [openstacksdk for Python](https://pypi.org/project/openstacksdk/) is also required:

	```sh
	pip install openstacksdk
	```

1. Finaly, you need to source the OpenRC file corresponding to the project the infrastructure will be deployed to. The file can be downloaded from the [API access](https://pouta.csc.fi/dashboard/project/api_access/) page of the Pouta interface. See [Pouta access through OpenStack APIs](https://docs.csc.fi/cloud/pouta/api-access/) for more reference.

## Explore the repository variables

In `group_vars/all.yml` you will see the following variables:

* `number_instances`, number of servers to deploy.
* `instance_name`, prefix for the name of the servers
* `os_image`, image to install in the servers
* `flavor`, size/flavor of the servers to be created
* `internal_ips`, IP ranges that will be able to connect to port 22/SSH.

These are the default values.

## Launch the playbook

In order to launch the playbook:

```sh
ansible-playbook main.yaml
```

It will prompt for:

* The key that you will use to SSH to your instance
* The network that you will use (Use this command to list the different networks: `openstack network list`)

* Other option is to specify the values in the command line:

```sh
ansible-playbook \
	-e key_name=xxxxxx-key \
	-e network=project_200xxxx \
	main.yaml
```

After ansible has run you should have 3 servers running nginx listening in port 80/HTTP (`http://`) serving the same index file as the one in `files/html` of this repository

If you change the variables and rerun `ansible-playbook`, ansible will automatically apply the changes. For example, if your current ip (`curl ifconfig.me` will give you your IP) does not belong to the default range of IPs, you will need to change the range of IPs to include yours. As a side note, it is very recommended to always have a narrow range of IPs that are allowed to connect to the port 22/SSH, it adds a good extra layer of security.

## Destroy the cluster

In order to destroy the servers, one must simply run:

```sh
ansible-playbook \
	-e key_name=xxxxxx-key \
	-e network=project_200xxxx \
	-e state=absent
	main.yaml
```

## Other more complex examples

* <https://github.com/CSCfi/ansible-ftp>
* <https://github.com/CSCfi/spark-openstack>
