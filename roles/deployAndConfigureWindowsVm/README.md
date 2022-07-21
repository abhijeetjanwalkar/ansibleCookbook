Role Name
=========

This role will deploy a Windows VM based on input file provided with the manager playbook.
The role will perform following tasks.
  The role will fail if the VM with same name exists in the vCenter.
  It will copy the provided input file for archival purposes.
  It will add extra hard disks to the VM and later format and provide proper drive letter and drive label.
  It will update VMware tools if those are not updated in the template.
  It will add routes defined in the input file.  
  It will add the VM to speficied domain in correct OU and later add the mentioned AD Group in local administrators group.
  It will change the CDRom drive letter from D to Z.
  It will disable features Windows Defender and Cortana.
  It will add registry entries for SSL and TLS configuration. 
  It will install following agents  
    Snow
    Splunk
    UMF or Nagios
    Tennable or Nessus
    Zabbix
    BSA or TSA
    Networker
      As a part of networker installer it will copy correct nsr.dir files on root of newly added drives based on if SQL will be installed or not. 
  As part of configuration the Role will run JEA scripts provided by customer.
  It will remove folders from C:\Temp folder as part of cleanup.
  Finally it will reboot the VM and inform Ops teams about the successful VM Deployment.
  It will send an email, informing VM is deployed from template and is configured.

Requirements
------------

For the role to run successfully input file needs to be properly filled in.
python3-pyvmomi is required to run VMWare Modules.

Role Variables
--------------

| Variable  | Description/Values                    |         Source         |
|-----------|---------------------------------------|------------------------|
| vCenter_user  |  user name to connect to vCenter to Deploy the VM | Manager Playbook               |
| vCenter_user_pass  |  Password for above user     | Manager Playbook  |
| input_file  |   The path where input file is saved for installing and configuring the VM               |Manager Playbook  |
| vCenter.server| Name of the vCenter where the VM will be deployed    | From input file|
| vCenter.dataCenter| Name of the Datacenter from the vCenter to deploy VM     | From input file|
| vCenter.cluster|   Name of the Cluster from the vCenter to deploy VM   | From input file|
| vCenter.datastore|  Name of the Datastore from the vCenter to deploy VM    | From input file|
| vCenter.vmFolder|   Name of the VM Folder from the vCenter to deploy VM   | From input file|
| vCenter.templateName|  Name of the Template from the vCenter to deploy VM    | From input file|
| vm.Name | name of the VM     | From input file|
| vm.Memory | VMHardware config amount of RAM in MBs    | From input file|
| vm.Cpu |  VMHardware config number of CPUs   | From input file|
| vm.Num_Cpu_Cores_Per_Socket | VMHardware config number of CPU Cores per Socket | From input file|
| vm_networks.name | Port group or Switch from vCenter  | From input file|
| vm_networks.ip | IP address for the NIC  | From input file|
| vm_networks.netmask | netmask for the NIC  | From input file|
| vm_networks.gateway |  default gateway for the NIC   | From input file|
| vm_networks.start_connected |  set this to yes for the NIC to be connected   | From input file|
| vm_networks.type | set this to static when providing IP Details    | From input file|
| vm_networks.connected |   set this to yes for the NIC to be connected    | From input file|
| vm_networks.dns_servers | DNS servers for this NIC    | From input file|
| domain.name | name of the domain to add the VM  | From input file|
| domain.ouPath | OU path in the AD Domain | From input file|
| domain.adminGroup | Name of the AD Group to be added in local Administrators group    | From input file|
| domain.addDomainUser | Name of the AD user with rights to add servers in Domain    | From input file|
| domain.addDomainUserPassword | Encrypted passowrd for the user to add server to the Domain    | From input file|
| local.userName  |  Local OS username from the template to configure Windows once VM is deployed | From input file|
| local.userPassword | Encrypted password for above user | From input file|
| vm_disks.datastore |  The name of datastore which will be used for the disk.   | From input file|
| vm_disks.type |  Type of disk options thin, thick, eagerzeroedthick   | From input file|
| vm_disks.size_gb |  Disk storage size in gb.   | From input file|
| vm_disks.unit_number |  Disk Unit Number.  Valid value range from 0 to 15 for SCSI controller, except 7.  | From input file|
| vm_disks.controller_type | Type of disk controller options  buslogic, lsilogic, lsilogicsas, paravirtual, sata, nvme   | From input file|
| vm_disks.controller_number |  Disk controller bus number. options 0, 1, 2, 3   | From input file|
| vm_disks_format.disk_number |  Specifies an array of disk numbers.   | From input file|
| vm_disks_format.drive_letter |  Specifies the specific drive letter to assign to the new partition.   | From input file|
| vm_disks_format.allocation_unit_size |  Specifies the allocation unit size to use when formatting the volume.   | From input file|
| vm_disks_format.file_system | Specifies the file system with which to format the volume.  Options NTFS, ReFS, exFAT, FAT32, and FAT.    | From input file|
| vm_disks_format.new_label |  Specifies the label to use for the volume.   | From input file|
| vm_disks_format.full | Performs a full format. A full format writes to every sector of the disk, takes much longer to perform than the default (quick) format, and is not recommended on storage that is thinly provisioned.    | From input file|
| vm_disks_format.force |  Specifies the override switch. set to yes   | From input file|
| routes.route | Routes to add to the VM    | From input file|
| routes.mask |  subnet mask   | From input file|
| routes.dg |   Default gateway to be used by the route  | From input file|
| smtp.server |  SMTP server address FQDN or IP reachable from the Ansible server   | From input file|
| smtp.port |  Port used on SMTP server   | From input file|
| smtp.toEmailIdsVmwareTeam | email IDs from Vmware Operations team to receive notification, one email id per line     | From input file|
| smtp.toEmailIdsWindowsTeam |  email IDs from Windows Operations team to receive notification, one email id per line     | From input file|
| smtp.fromEmailId |  Sender email ID   | From input file|
| foldersToDelete.name |  name of the folder to be deleted from C:\temp folder as clenaup, one file or foder per line   | From input file|
| agents.nessus.installer.folder |  folder where Nessus installer is kept on C:\Temp folder   | From input file|
| agents.nessus.installer.file |  File name for the Nessus Installer   | From input file|
| agents.nessus.server |  Nessus Server used by Customer  | From input file|
| agents.nessus.groups | Nessus groups to be configured  | From input file|
| agents.nessus.key |  Nessus Key to be used while installing and configuring Nessus | From input file|
| agents.zabbix.installer.folder |  folder where Zabbix installer is kept on C:\Temp folder    | From input file|
| agents.zabbix.installer.file |   File name for the Zabbix Installer  | From input file|
| agents.zabbix.hostname |     | From input file|
| agents.zabbix.server | Zabbix Server used by Customer    | From input file|
| agents.zabbix.lport | Zabbix  Server Port used by Customer   | From input file|
| agents.zabbix.rmtcmd|  Set this to 1  | From input file|
| agents.splunk.installer.folder | folder where Splunk installer is kept on C:\Temp folder    | From input file|
| agents.splunk.installer.script |  Script name for the Splink Installer    | From input file|
| agents.snow.installer.folder | folder where Snow installer is kept on C:\Temp folder    | From input file|
| agents.snow.installer.script |  Script name for the Snow Installer    | From input file|
| agents.umf.installer.folder | folder where UMF or Nagios installer is kept on C:\Temp folder    | From input file|
| agents.umf.installer.script |  Script name for the UMF or Nagios Installer    | From input file|
| agents.bbsa.installer.folder |  folder where BSA or TSA agent installer is kept on C:\Temp folder   | From input file|
| agents.bbsa.installer.file |   File name for the BSA or TSA agent Installer  | From input file|
| agents.bbsa.installer.userlocal | File name for the userlocal variable for the Installer    | From input file|
| agents.bbsa.installer.exports |   File name for the exports variable for the Installer  | From input file|
| agents.networker.installer.folder |  folder where Networker agent installer is kept on C:\Temp folder   | From input file|
| agents.networker.installer.files.client |  File name for networker Client installer  | From input file|
| agents.networker.installer.files.networker |  File name for networker Installer   | From input file|
| agents.networker.installer.files.servers |  File name for coping servers details to Program Files   | From input file|
| agents.networker.pathToProgram |  Program files path to copy servers file   | From input file|
| agents.networker.startServices |  Option to start services after installtion set to 1   | From input file|
| agents.networker.configureFirewall |   Option to configure local firewall while doing the installtion set to 0   | From input file|
| JEAUserDiscovery.pshome | $PSHome folder on  Windows OS  | From input file |
| JEAUserDiscovery.folder | folder where UMF or Nagios installer is kept on C:\Temp folder  | From input file |
| JEAUserDiscovery.registerFile| File name for regestering JEA Details | From input file|
|  |  | |
|  |  | |

Dependencies
------------

None

Example Playbook
----------------
- name: Create a Windows VM from template by running role
  hosts: localhost
  gather_facts: false
  vars_files:
    - "{{ input_file }}"
  
  vars_prompt:
    - name: "vCenter_user"
      prompt: "Enter Username to Deploy VM in vCenter"
      private: no
    - name: "vCenter_user_pass"
      prompt: "Enter Password for the above User"
      private: yes
    - name: "input_file"
      prompt: 'Enter location of input file to be used to deploy this VM like "\home\ansible\vmname.yml"'
      private: no

  tasks:
  - name: "Running Role:deployAndConfigureWindowsVm '{{ vm.Name }}'"
    include_role:
      name: deployAndConfigureWindowsVm

Ansible versions
-------
2.8

Author Information
------------------
Abhijeet Janwalkar

Changelog
------------------
| Version | Date | User | Changes |
|---------|------|------|---------|
| 0.1 |  10-06-2022 | Abhijeet Janwalkar | Initial Role Creation|
| 0.2 |  20-06-2022 | Abhijeet Janwalkar | Finalized Role Creation and updated readme|