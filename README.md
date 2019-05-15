# Project
This code has a combination of playbooks and variable files. It is a jumping off point. It builds the basic structure of a cloud environment in the Nectar Research Cloud after which playbooks can be added for the automation of adding databases, websites, and any other program that can be pulled from github as code.

# Host Variables

The first thing that needs to be edited is host_vars/nectar.yaml.

### Common vars
You need to add your availability zone

### Volumes

Specify your volume names and volume sizes. More volumes can be added simply by adding another volume name and volume size. 

### Security Groups

When adding security groups, make sure you specify the correct port range for the use. For instance, when setting up an instance for CouchDB you will additionally need a security group with port 5984 for CouchDB access.

The remote_ip_prefix variable is the range of ip addresses from which one can access your instances. Here, the security is questionable, when testing out your system, specify only your ip address, and then widen it as needed for each security group.

### Instances

The image id, the key pair, and the flavor must be specified. I used an image id that came with Ubuntu.

To create your key pair for ssh access, follow the directions at the following website:

https://training.nectar.org.au/package07/sections/createSSHKey.html


### Attachments

Your volumes need to be attached to instances. You can attach more than one volume to each instance, but not more than one instance to any volume.

# Nectar.yaml

This implementation will default to local hosts, which means tasks will executed locally. You can optionally specify your host variables. The documentation has more information on that. https://docs.ansible.com/ansible/latest/plugins/inventory/ini.html?highlight=hosts%20ini

### Variable files

Here, I call the variable file I created above, you can create and call more.

### Gather facts

I have set gather_facts to true so that my program will automatically get all the information regarding the remote servers.

### Roles

Here, I call the playbooks that I will describe in the Playbooks section. These playbooks are set up in a way that they don't need to be modified to modify the specifications of your system, that's what the variable files are for.

You can add playbooks to add more automation to your system, i.e. if you want to create a website on one or more of your instances, add docker, or CouchDB.

# Playbooks
