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

In the folder roles, I have stored the different playbooks. 

## Openstack-common

This playbook installs dependencies on every instance that you create. The instance ids I used were for instances that already had python installed, on Ubutu. I installed pip, vim, updated pip, installed the openstack software development kit, and curl.

### Openstacksdk

The openstack software development kit is necessary to be able to call the openstack functions necessary to execute the playbooks.

## Openstack-images

This playbook retrieves the available Openstack images. The image I used had Ubuntu and python already installed, so that I didn't need to go through the painful process of setting up the instance myself.

### Name

What you write after - name: is helpful when diagnosing issues. When running this stack, there will be a separator for each execution, where the text after - name: will also be displayed. If anything goes wrong, you will then know exactly where it went wrong.

### Loop

Setting a loop means that you can use these playbooks for more than one implementation of anything. Here, we loop through openstack images. The curly brackets mean that you are calling a variable from outside the specific playbook. If you have item. in your curly brackets, this means that you are calling a variable from instide the implementation that you're looping through.

## Openstack-instance

This is where you create your instances. We call the openstack function os_server, which has various possible variables defined (https://docs.ansible.com/ansible/latest/modules/os_server_module.html?highlight=os_server)

Notice that we are looping through instanes here, as defined in host_vars/nectar.yaml. There we have defined an instance_name, instance_imge, instane_keyname and instance_flavor variable for each instance. This is why you only need to change the host_vars/nectar.yaml file to be able to add or delete instances.

Here, I have stored the instance ids and ip addresses so that they can be easily called for use in different implementations. I looped through os_instance.results. This was defined in register: os_instance which means that the information regarding these instances is stored in the os_instance variable and this information can be used. 

I have set my debug message to print out the first ip address. Often, being able to access this ip address is important to add applications to the instances. To print out more ip addresses, just add plus and then {{ os_instance_ip[1] }} inside the apostrophe's.

## Openstack-security-group

In any company, security is everyone's responsibility. Here, Security groups are created, and a list of names have been saved. Then, the rules are created.

## Openstack-volume.

Here, the volumes are created. Setting wait: yes means that nothing else happens until the volume is created. Timeout: 600 means that this waiting is limited to 600 seconds.


# How to run

I installed Ubuntu from the Windows App Store and loaded my files into the file system there to run it. Be aware when accessing your computers C-drive from Ubuntu, you need to mount it using /mnt/c/ as the beginning of the file path.

* Log into Nectar, go to User –> OpenStack RC File.
* Go to User –> Settings, and select Reset Password.
* This password needs to be copied and pasted into the OpenStack RC file that was downloaded. I have changedthe code provided as follows:
* I hashed the lines "read -sr OS_PASSWORD_INPUT" and "export OS_PASSWORD=$OS_PASSWORD_INPUT", and instead, added "export OS_PASSWORD=’myPassword’"
* Then, go into the run-nectar.sh bash script and change the name of the bash file from your-openstack-rc-filename.sh to the name of your OpenStack RC file
* Navigate into the nectar folder while in Ubuntu, and run the bash script by typing ./run-nectar.sh.

# More information

I extensively used the Search bar in the Ansible documentation to find more information.
https://docs.ansible.com/ansible/latest/index.html
