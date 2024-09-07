# Overview
This playbook is designed for setting up and managing an Oracle Data Guard environment. It includes tasks for RMAN duplication using secure external password store, configuring Data Guard, and managing post-setup tasks. 
The structure includes configuration files, scripts, and templates to automate the process of setting up a physical standby database.

# File Structure

![
](![https://github.com/db-repo-dev/ansible/blob/main/file_structure.jpg]))


# Replace your own wallet using the following commands:
1. Create an empty wallet:
mkstore -wrl /home/oracle/wallet -create

3. Add source and target sys credential into the wallet:
mkstore -wrl /home/oracle/wallet -createCredential source sys PASSWORD
mkstore -wrl /home/oracle/wallet -createCredential target sys PASSWORD

4. List your credentials in the wallet:
mkstore -wrl /home/oracle/wallet -listCredential
Enter wallet password:
List credential (index: connect_string username)
2: target sys
1: source sys

5. Copy the wallets to roles/dataguard/files/wallet

# How to Use
1. Configure inventory: Update inventory.ini with the correct source and target database host information.
2. Update Variables: Modify the variables in roles/dataguard/vars/main.yml according to your environment.
3. Run RMAN Duplication: Execute physical_standby.yml to perform RMAN duplication and create the standby database.
`ansible-playbook -i inventory.ini physical_standby.yml`
4. Post-setup tasks: Once the duplication is complete, run post_dg.yml to finalize Data Guard configuration, including registering the standby and setting up monitoring.
`ansible-playbook -i inventory.ini post_dg.yml`
