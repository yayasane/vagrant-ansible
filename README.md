# Ansible Deployment with Vagrant

This repository contains a Vagrant configuration with Ansible to deploy Nginx on a virtual client.

## Usage

1. Clone this repository.

2. Ensure you have Vagrant and VirtualBox installed on your machine.

3. Run `vagrant up` to start the virtual machines.

4. Once the machines are started, make sure to have the following configurations:

   ### On the Ansible machine (ansible)

   - **SSH Passwordless Connection:** Ensure that you can connect to the client1 machine without a password using SSH from the Ansible machine. You can generate an SSH key pair with `ssh-keygen` on the Ansible machine if it's not already generated, and then copy the public key to `~/.ssh/authorized_keys` on the client1 machine. On the Ansible machine, you can do this with the following commands:

      ```bash
      ssh-keygen -t ed25519 -C "ansible"
      ```

     Ensure that `/home/vagrant/.ssh/ansible` is the correct file to your private key file.

     Additionally, you may need to add the IP address of the client1 machine to the `known_hosts` file on the Ansible machine to avoid SSH host key verification issues:

      ```bash
      ssh-keyscan -H 192.168.99.11 >> ~/.ssh/known_hosts
      ```

     You can test the ssh connection to clent1 machine using the following command from the Ansible machine:

     ```bash
      ssh -i /home/vagrant/.ssh/ansible vagrant@192.168.99.11
      ```

  - **Install Ansible:** If Ansible is not already installed on the ansible machine, you can install it using the following commands:

       ```bash
       sudo apt update
       sudo apt install ansible
       ```

   - **Create Ansible Inventory:** Create a `hosts` file in the `/home/vagrant/playbook1/inventory/host` directory (for example, `/home/vagrant/playbook1/inventory/host/hosts`) and add the IP addresses and connection information of the hosts. For example:

     ```
     [web-servers]
     192.168.99.11
     ```

   ### On the client1 machine

   - **Authorization to Execute Commands as Superuser:** Ensure that the user used for SSH connection from Ansible to client1 has authorization to execute commands as superuser (sudo) without entering a password. You can add this user to the sudo group and configure sudoers to allow passwordless commands.

5. Run the ping command to test the connection between Ansible and the hosts using the following command from the Ansible machine:

    ```bash
    ansible all --key-file /home/vagrant/.ssh/ansible -i /home/vagrant/playbook1/inventory/host/hosts -m ping
    ```

6. Create an Ansible playbook to install Nginx. For example, you can create a file named `install_nginx.yml` in /home/vagrant/playbook1 with the following content:

    ```yaml
    ---
    - hosts: web-servers
      become: yes

      tasks:
        - name: Update apt cache
          apt:
            update_cache: yes

        - name: Install Nginx
          apt:
            name: nginx
            state: present

        - name: Ensure Nginx is running
          service:
            name: nginx
            state: started
            enabled: yes
    ```

7. Run the Ansible playbook to install Nginx using the following command from the directory containing your Vagrant configuration:

    ```bash
    ansible-playbook --key-file /home/vagrant/.ssh/ansible --inventory /home/vagrant/playbook1/inventory/host/hosts /home/vagrant/playbook1/install_nginx.yml
    ```

8. Once the playbook is successfully executed, you can access the Nginx server by opening a web browser and accessing the IP address of the client1 machine.

Feel free to refer to the documentation of Ansible and Vagrant for more information on configuration and usage.

### Ansible Machine

![](https://github.com/yayasane/vagrant-ansible/blob/main/screenshots/ansible.png)


### Client1 Machine

![](https://github.com/yayasane/vagrant-ansible/blob/main/screenshots/client1.png)


### Ansible Machine results preview

![](https://github.com/yayasane/vagrant-ansible/blob/main/screenshots/ansible-result-1.png)
![](https://github.com/yayasane/vagrant-ansible/blob/main/screenshots/ansible-result-2.png)


### Ansible Client1 results preview

![](https://github.com/yayasane/vagrant-ansible/blob/main/screenshots/client-result-1.png)
