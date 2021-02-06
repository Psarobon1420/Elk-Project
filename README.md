## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

(Diagrams/Azure Elk Stack Diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

Elk insallation playbook: (Elk.yml)
  - ---
- name: Configure Elk VM with Docker
  hosts: elk
  remote_user: azureuser
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module (It will default to pip3)
    - name: Install Docker module
      pip:
        name: docker
        state: present

      # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: '262144'
        state: present
        reload: yes


      # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        # Please list the ports that ELK runs on
        published_ports:
          -  5601:5601
          -  9200:9200
          -  5044:5044

      # Use systemd module
    - name: Enable service docker on boot
      systemd:
        name: docker
        enabled: yes

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available in addition to restricting access to the network.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the log and system services.

The configuration details of each machine may be found below.

| Name       | Function      | IP Address                | Operating System |
|------------|---------------|---------------------------|------------------|
| Jumpbox    | Gateway       |  10.0.0.4 / 40.121.106.89 | Ubuntu Linux     |
| Pentest-LB | Load Balancer | 52.150.11.172             | Azure            |
| Web-1      | DVWA          | 10.0.0.7                  | Ubuntu Linux     |
| Web-2      | DVWA          | 10.0.0.6                  | Ubuntu Linux     |
| Web-3      | DVWA          | 10.0.0.8                  | Ubuntu Linux     |
| ELK_SERVER | Elk Server    | 10.1.0.4 / 40.91.72.125   | Ubuntu Linux     |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jumpbox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:

Machines within the network can only be accessed by SSH key.

A summary of the access policies in place can be found in the table below.

| Name       | Publicly Accessible | Allowed IP Addresses |
|------------|---------------------|----------------------|
| Jump Box   | Yes                 | 96.241.140.229       |
| Elk Server | Yes                 | 96.241.140.229       |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it is quick and consistent.

The playbook implements the following tasks:

1.  Install docker
2.  Install python
3.  Install docker module
4.  Increase virtual memory
5.  Download and Install docker elk container
6.  Enable service docker on boot

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

(Images/Docker-ELK-PS.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
1.  Web-1, 10.0.0.7
2.  Web-2, 10.0.0.6
3.  Web-3, 10.0.0.8

We have installed the following Beats on these machines:
1.  Filebeats
2.  Metricbeats

These Beats allow us to collect the following information from each machine:
1.  Filebeat collects logs events such as system logs and forwards them to Elk. Examples include: Event and audit logs.
2.  Metricsbeat collects metric from the operating system and running services. Examples include: Docker, Apache, etc.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the corresponding application configuaration file to the ansible control node directory as defined within respective playbooks.

- Update the configuration file to include detail such as Elastic search and Kibana server ip addresses.

- Run the playbook, and navigate to the Elk server public ip address to check that the installation worked as expected.

File name are: filebeat-playbook.yml and metricbeat-playbook.yml. Copy files to ansible control node directory /etc/ansible/roles.

To define which servers to deploy to, add servers to the file /etc/ansible/hosts, grouped by type, e.g.: [webservers]. Ensure this same group name exists within each beginning section of the playbook.

Confirm Kibana is running by navigating to the public IP address of the Elk server on port 5601.

As a generalized workflow: 
1.  Use Curl to download template playbook from Github. 
2.  Edit playbook to match your custom configuration.
3.  Test playbooks in sandbox environments whenever possible.
4.  Use ansible-playbook <playbook_name> to run playbook.

# Testing Bidirectional snyc with Github 20210204
