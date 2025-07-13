# Introduction
Welcome to CTng On Sphere!
In this file, you will find 
1. Model: used to create the experiment on Sphere, file can be found in the current directory
2.  Operations from the XDC (short cut for Experiment Development Container)
3. Operations from the Control Node (created to run automation scripts other VMs, VM targets are specified by the CTngexp/inv.ini file)
   
More instructions can be found at https://launch.sphere-testbed.net/tutorials

# Model 
The model file creates a star topology as shown in the figure below: 

![image](https://github.com/user-attachments/assets/7f9ac1b2-229c-4cdc-821a-02ef8e0aa198)

Every entity (including a control node) is connected to the backbone router via a 100Mbps access link. 


# Operations from the XDC
## 1. Generate Inventory

> **Note:** Replace `<USERNAME>`, `<PATH_TO_CTNGEXP>`, and `<SOURCE_HOST>` with your actual details.

> You will also need to modify the automation scripts for replication. 

1. Open a root shell in a new terminal and navigate to your CTngexp directory:

   ```bash
   cd <PATH_TO_CTNGEXP>
   ```
   ```bash
   sudo su          
   ```
2. Generate the Ansible inventory file:

   ```bash
   python3 genini.py   
   ```
3. Switch back to your non-root user:

   ```bash
   exit              
   ```
   ```bash
   su <USERNAME>     
   ```

## 2. Distribute Playbooks
Distribute the CTngexp folder:
```bash
ansible-playbook -i inv.ini distribute.yml  
```
From the CTngexp directory, run:

```bash

ansible-playbook -i inv.ini ssh.yml         
```
# Operations from the control Node

## 3. Configure Ansible

Create or update an ansible.cfg file in the project root with:

```ini
[defaults]
forks = 60
timeout = 60
retry_files_enabled = False
async_poll_interval = 5

[ssh_connection]
pipelining = True
```

> **Reminder:** If you modify control-node settings or delete this file, recreate it accordingly.

## 4. Install Prerequisites

```bash
sudo apt update
sudo apt install ansible
```

## 5. Bootstrap All Hosts

Run these playbooks to install prerequisites and Go on every host:

```bash
ansible-playbook -i inv.ini gpp.yml      
ansible-playbook -i inv.ini newgo.yml    
```

Verify on any host:

```bash
ssh <HOSTNAME>
```
```bash
g++ --version
```
```bash
go version
```

## 6. Repository Setup & Distribution

1. **Initial checkout/setup**

   ```bash
   ansible-playbook -i inv.ini repo.yml
   ```
2. **Propagate code changes** (e.g., edits in gen\_test.go)

   ```bash
   ansible-playbook -i inv.ini redis.yml
   ```

## 7. Run the Experiment

From the control node in your CTngexp directory:

```bash
ansible-playbook -i inv.ini ctngv3.yml
```

## 8. Iterating on Experiments

#### 1. Edit your test code (e.g., CTngV3/deter/gen\_test.go).
#### 2. Redistribute

   ```bash
   ansible-playbook -i inv.ini redis.yml
   ```
#### 3. rerun and collect result
   ```bash
   go test ./CTngV3/deter
   ```
   ```bash
   ansible-playbook -i inv.ini ctngv3.yml
   ```
   ```bash
   ansible-playbook -i inv.ini collect.yml
   ```
   or Run experiment for 30 iterations and automatically collect results for every run :
   ```bash
   sh run.sh
   ```


## 9. Retrieve Results & Cleanup

1. **Copy back to XDC:**

   ```bash
   ansible-playbook -i inv.ini copy.yml
   ```
2. **Clean up temporary files on control node:**

   ```bash
   rm -rv output output.tar.gz monitor_files
   ```
## 10. Copy to local machine 
   ```bash
   mrg xdc scp download -r <path_to_folder_on_XDC> <path_local_destination_folder>
   ```
   ```bash
   mrg xdc scp download  <path_to_file_on_XDC> <path_local_destination_folder>
   ```

# Results
Results were obtained by <reducted> using CTngV3 repository with Commit ID:
```
fe0941c
```
Link:
```
https://github.com/<USERNAME_REDACTED>/CTngV3/commit/fe0941cc115a9085c43b0c48cf64c95086f338dd
```

![image](https://github.com/user-attachments/assets/edbc43b5-e579-4985-9beb-559e3ba0e6ef)
