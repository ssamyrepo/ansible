
**1. System Preparation**

   - **Update Package Manager**

     For Amazon Linux systems:

     ```bash
     sudo yum update -y
     ```

     For systems using DNF (e.g., Fedora):

     ```bash
     sudo dnf update -y
     ```

   - **Install Package Management Utilities**

     For Amazon Linux:

     ```bash
     sudo yum install -y amazon-linux-extras
     ```

     For DNF-based systems:

     ```bash
     sudo dnf install -y python3 python3-pip
     ```

**2. Ansible Installation**

   - **Using Amazon Linux Extras**

     ```bash
     sudo amazon-linux-extras install ansible2 -y
     ```

   - **Using Pip (Python Package Installer)**

     ```bash
     pip3 install --user ansible
     ```

     Ensure that the local bin directory is in your system's PATH:

     ```bash
     echo 'export PATH=$PATH:~/.local/bin/' >> ~/.bashrc
     source ~/.bashrc
     ```

   - **Verify Ansible Installation**

     ```bash
     ansible --version
     ```

**3. Configure SSH for Passwordless Authentication**

   - **Generate SSH Key Pair**

     ```bash
     ssh-keygen -t rsa -b 2048
     ```

     Press Enter to accept the default file location and leave the passphrase empty for passwordless authentication.

   - **Copy Public Key to Target Hosts**

     ```bash
     ssh-copy-id username@target_host_ip
     ```

     Replace `username` and `target_host_ip` with the appropriate values.

**4. Set Up Ansible Inventory**

   - **Create Inventory Directory and File**

     ```bash
     sudo mkdir -p /etc/ansible
     sudo vi /etc/ansible/hosts
     ```

     In the hosts file, define your groups and hosts:

     ```ini
     [webservers]
     192.168.1.10
     192.168.1.11

     [databases]
     192.168.1.20
     ```

**5. Test Connectivity**

   - **Ping All Hosts**

     ```bash
     ansible all -m ping
     ```

     A successful response will return "pong" from each host.

**6. Execute Ad-Hoc Commands**

   - **Check Uptime on All Hosts**

     ```bash
     ansible all -m shell -a "uptime"
     ```

   - **Install Nginx on All Hosts**

     ```bash
     ansible all -m apt -a "name=nginx state=present" --become
     ```

     Note: Adjust the package manager module (`apt`, `yum`, etc.) based on your system.

**7. Run Ansible Playbooks**

   - **Create a Playbook Directory and File**

     ```bash
     mkdir -p ~/playbooks
     vi ~/playbooks/example.yml
     ```

     Example playbook content:

     ```yaml
     ---
     - name: Install and start Nginx
       hosts: webservers
       become: yes
       tasks:
         - name: Install Nginx
           apt:
             name: nginx
             state: present
         - name: Start Nginx service
           service:
             name: nginx
             state: started
     ```

   - **Run the Playbook**

     ```bash
     ansible-playbook ~/playbooks/example.yml
     ```


 

Part 2 

1. **Setting Up Passwordless SSH Authentication Using `ssh-copy-id`:**

   ```bash
   ssh-copy-id -i ~/.ssh/id_rsa.pub ubuntu@<managed_node_ip>
   ```

   This command copies your public SSH key to the managed node, enabling passwordless authentication. Replace `<managed_node_ip>` with the actual IP address of your managed node.

2. **Creating an Ansible Inventory File:**

   ```bash
   # Create and edit the inventory file
   vim inventory.ini

   # Add the following content to the file
   [app]
   ubuntu@<managed_node_1_ip>

   [db]
   ubuntu@<managed_node_2_ip>
   ```

   This defines two groups: `app` and `db`, each containing one managed node. Replace `<managed_node_1_ip>` and `<managed_node_2_ip>` with the respective IP addresses.

3. **Pinging All Managed Nodes to Test Connectivity:**

   ```bash
   ansible all -i inventory.ini -m ping
   ```

   This command uses the `ping` module to check the connectivity of all hosts defined in `inventory.ini`.

4. **Pinging Hosts in the `app` Group:**

   ```bash
   ansible app -i inventory.ini -m ping
   ```

   This targets only the hosts in the `app` group.

5. **Executing a Shell Command on All Managed Nodes:**

   ```bash
   ansible all -i inventory.ini -m shell -a "uptime"
   ```

   This runs the `uptime` command on all managed nodes, displaying how long each system has been running.

6. **Installing a Package Using the `apt` Module:**

   ```bash
   ansible all -i inventory.ini -m apt -a "name=nginx state=present" --become
   ```

   This installs the `nginx` package on all managed nodes. The `--become` flag is used to execute the command with elevated privileges.

7. **Copying a File to Managed Nodes:**

   ```bash
   ansible all -i inventory.ini -m copy -a "src=/path/to/local/file dest=/path/to/remote/destination"
   ```

   This copies a local file to the specified path on all managed nodes. Replace `/path/to/local/file` and `/path/to/remote/destination` with the actual source and destination paths.

8. **Adding a Line to a File Using the `lineinfile` Module:**

   ```bash
   ansible all -i inventory.ini -m lineinfile -a "path=/etc/motd line='This server is managed by Ansible'" --become
   ```

   This adds the specified line to the `/etc/motd` file on all managed nodes.

9. **Gathering Facts About Managed Nodes:**

   ```bash
   ansible all -i inventory.ini -m setup
   ```

   This gathers and displays detailed information about all managed nodes.

These commands provide a foundational understanding of interacting with managed nodes using Ansible ad hoc commands. 
