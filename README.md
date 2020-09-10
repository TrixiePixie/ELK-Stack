## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

(Images/Network_Diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the install-elk.yml file may be used to install only certain pieces of it, such as Filebeat.

  ---
- name: Configure Elk VM with Docker
  hosts: elkserver
  remote_user: ElkUser
  become: true
  tasks:

    # Use apt module

    - name: Install containerd
      apt:
        update_cache: yes
        name: containerd
        state: present

    - name: Install docker.io
      apt:
        name: docker.io
        state: present

      # Use apt module
    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module
    - name: Install Docker python module
      pip:
        name: docker
        state: present

      #start docker
    - name: start docker
      service:
        name: docker
        state: started
        enabled: yes

      # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

      # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly accessible, in addition to restricting DDOS to the network.
- Load balancers can prevent DDOS attacks, or overloading of data by segementing work flow into mulltiple servers. 
The Jump Box ensures there is a centralized server that can pivot to devices, as well as limit what IP addresses that can access the system.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the _____ and system _____.
- Filebeat monitors system logs and collects system events to then send to Elasticsearch to be displayed, usally in graphs and charts.
- Metricbeat focuses more on metrics and collects system data (memory data, CPU data, etc) to then be examined and indexed.

The configuration details of each machine may be found below.


| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.7   | Linux            |
| Web 1    | Server VM| 10.0.0.11  | Linux            |
| Web 2    | Server VM| 10.0.0.12  | Linux            |
| Web 3    | Server VM| 10.0.0.10  | Linux            |
| ELK      | Server VM| 10.1.0.4   | Linux            |


### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the 'Jumb Box' machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 24.205.85.9

Machines within the network can only be accessed by SSH. The ELK VM is accessed through an Ansible container on the Jump Box through the IP address     52.229.10.41

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 |  24.205.85.9         |
|   ELK VM | No                  |  24.205.85.9         |
|          |                     |                      |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it vastly decreases the amount of work needed to download, install, and launch containers. This frees up time to focus on other demands. Otherwise, the IT professional would need to perform these tasks every time a container was needed.

The playbook implements the following tasks:
- Install Containerd
- Install docker.io
- Install pip3
- Install Docker Python module
- Start Docker
- Increase virtual memory; use more memory
- Download and launch Elk container

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

(Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web-1, Web-2, Web-3

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat: log files, log events 
- Metricbeat: metrics, statistics

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the config file to Ansible folder.
- Update the config file to include private IP addresses and remote users
- Run the playbook, and navigate to http://[your.IP]:5601 to check that the installation worked as expected.


