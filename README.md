Overview
This playbook is designed for setting up and managing an Oracle Data Guard environment. It includes tasks for RMAN duplication using secure external password store, configuring Data Guard, and managing post-setup tasks. 
The structure includes configuration files, scripts, and templates to automate the process of setting up a physical standby database.

File Structure

├── dg_status.txt              # Output of Data Guard Status
├── output.txt                 # Output log of the playbook execution
└── rman_dup                   # Directory containing playbook files for RMAN duplication and Data Guard setup
    ├── inventory.ini          # Inventory file for specifying hosts (source and target)
    ├── physical_standby.yml   # Playbook for creating a physical standby database
    ├── post_dg.yml            # Playbook for post-Data Guard setup tasks
    └── roles
        └── dataguard          # Role for handling Data Guard tasks
            ├── files
            │   └── wallet     # Oracle wallet files for secure connection configuration
            │       ├── cwallet.sso
            │       ├── cwallet.sso.lck
            │       ├── ewallet.p12
            │       └── ewallet.p12.lck
            ├── tasks          # Ansible task files
            │   ├── add_source_tns.yml                 # Task to configure primary TNS
            │   ├── add_target_tns.yml                 # Task to configure standby TNS              
            │   ├── configure_dataguard.yml            # Main task for Data Guard setup
            │   ├── main.yml                           # Main entry point for the role
            │   ├── post_dg.yml                        # Task for post Data Guard setup
            │   ├── register_standby_database.yml      # Task to register standby database
            │   ├── rman_dup.yml                       # Task for RMAN duplication from standby
            │   ├── source.yml                         # Task to configure primary database
            │   ├── srvctl_standby.yml                 # Task to configure srvctl on standby
            │   └── target.yml                         # Task to configure standby database
            ├── templates       # Jinja2 templates for Oracle configuration files
            │   ├── primary_listener.ora.j2            # Template for primary database listener
            │   ├── rman_dup.par.j2                    # Template for RMAN duplication parameters
            │   ├── sqlnet.ora.j2                      # Template for sqlnet.ora configuration
            │   ├── standby_listener.ora.j2            # Template for standby listener configuration
            │   └── tnsnames.ora.j2                    # Template for tnsnames.ora configuration
            └── vars            # Variables used in the playbooks
                └── main.yml                           # Variables required for Data Guard configuration


Replace your own wallet using the following commands:
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

How to Use
1. Configure inventory: Update inventory.ini with the correct source and target database host information.
2. Run RMAN Duplication: Execute physical_standby.yml to perform RMAN duplication and create the standby database.
ansible-playbook -i inventory.ini physical_standby.yml
3. Post-setup tasks: Once the duplication is complete, run post_dg.yml to finalize Data Guard configuration, including registering the standby and setting up monitoring.
ansible-playbook -i inventory.ini post_dg.yml
