
#Ansible Plybook for AWS 

#Ansible contorl server should be created with proper IAM role. This will help to avoid stroing IAM cerdantials Locally



Steps to Install ansible 2.0 on centos

# Install ansible 1.X


yum install ansible



# Remove ansible 1.X but will keep the dependent packages for ansible 2.X

yum remove ansible 


# create a tmp directory in your home directory

cd tmp

# git clone https://github.com/ansible/ansible --recursive 

cd ansible

make 

sudo make install 

more details on https://github.com/ansible/ansible



Steps to Install ansible 2.0 on ubuntu


# Setup ubuntu ppa for ansible 2.X

https://launchpad.net/~ansible/+archive/ubuntu/ansible

# Install ansible 2.X

sudo apt-get update

sudo apt-get install ansible




# Example

INSTANCE_NAME=testservers
ansible-playbook -e Name=$INSTANCE_NAME awsec2_create.yml
ansible-playbook -e Name=$INSTANCE_NAME awsec2_remote_facts.yml
INSTANCE_ID=`ansible-playbook -e Name=$INSTANCE_NAME awsec2_remote_facts.yml | awk '/Instance_ID/ { print $NF}' | sed s/\"//g`
IP=`ansible-playbook -e Name=$INSTANCE_NAME awsec2_remote_facts.yml | awk '/Private DNS/ { print $NF}' | sed s/\"//g|tail -1`
ELB=ELB-TEST [ instan
echo [host] > inventory
echo $IP >> inventory
ansible-playbook -e "instance_id=$INSTANCE_ID my_elb_name=$ELB"  awsec2_register_elb.yml
ansible-playbook -e "instance_id=$INSTANCE_ID my_elb_name=$ELB"  awsec2_dregister_elb.yml
ansible-playbook -e instance_id=$INSTANCE_ID awsec2_snapshot.yml
ansible-playbook -i inventory -e var_hosts=host  ubuntu_os_upgrade.yml
ansible-playbook -e "instance_id=$INSTANCE_ID my_elb_name=$ELB"  awsec2_register_elb.yml
ansible-playbook -e instance_id=$INSTANCE_ID awsec2_terminate.yml
ansible-playbook -e instance_id=i-7b454da4 awsec2_patching.yml
ansible-playbook -e instance_id=$INSTANCE_ID awsec2_remote_facts_instance_id.yml






ansible-playbook -i local -e "var_hosts=$INSTANCE_ID var_remote_user=ubuntu" awsec2_ubuntu_package_update.yml

ansible-playbook -i local -e "var_hosts=$INSTANCE_ID var_remote_user=ubuntu" awsec2_ubuntu_package_remove.yml

ansible-playbook -i local -e "var_hosts=$INSTANCE_ID var_remote_user=centos" awsec2_redhat_package_update.yml 

ansible-playbook -i local -e "var_hosts=$INSTANCE_ID var_remote_user=centos" awsec2_redhat_package_remove.yml

ansible-playbook -i local -e "var_hosts=$INSTANCE_ID var_remote_user=ubuntu" awsec2_ubuntu_package_hold.yml 

ansible-playbook -i local -e "var_hosts=$INSTANCE_ID  var_remote_user=ubuntu" awsec2_ubuntu_package_unhold.yml




In Case of Centos/Redhat it is possible with latest version of ansible 2.X yum module to provide the exclude list and it works but i have issue putting it to variable file and working on fixing it  

Playbook change 
===============

  - name: Running yum update to patch the system
    yum: name=* state=latest update_cache=yes exclude="httpd* telnet*"




EC2 DYNAMIC INVENTORY
=====================


export EC2_INI_PATH=/etc/ansible/ec2.ini

/etc/ansible/inventory/hosts  --list


INSTANCE_ID=i-d61d0d09


If you are using private IP

IP=`ansible-playbook -i local -e instance_id=$INSTANCE_ID awsec2_remote_facts_instance_id.yml | awk '/Private DNS/ { print $NF}' | sed s/\"//g|tail -1`

If you are using public IP

IP=`ansible-playbook -i local -e instance_id=$INSTANCE_ID awsec2_remote_facts_instance_id.yml | awk '/Public DNS/ { print $NF}' | sed s/\"//g|tail -1`




echo [$INSTANCE_ID] >> local 



echo $IP >> local



[centos@ip-172-31-2-87 simp]$ cat local 
[local]
localhost
[i-4352589c]
ip-172-31-9-41.ap-southeast-2.compute.internal

[centos@ip-172-31-2-87 simp]$ ansible-playbook -i local -e instance_id=$INSTANCE_ID --step awsec2_patching.yml

PLAY [local] *******************************************************************
Perform task: TASK: setup (N)o/(y)es/(c)ontinue: y

Perform task: TASK: setup (N)o/(y)es/(c)ontinue: *******************************

TASK [setup] *******************************************************************
ok: [localhost]
Perform task: TASK: De-register Instance from ELB (N)o/(y)es/(c)ontinue: y

Perform task: TASK: De-register Instance from ELB (N)o/(y)es/(c)ontinue: *******

TASK [De-register Instance from ELB] *******************************************
changed: [localhost]
Perform task: TASK: Taking the snapshot of the an instance (N)o/(y)es/(c)ontinue: y

TASK [Taking the snapshot of the an instance] **********************************
changed: [localhost]
Perform task: TASK: Register Instance from ELB (N)o/(y)es/(c)ontinue: 





[centos@ip-172-31-2-87 simp]$ ansible-playbook -i local -e var_hosts=$INSTANCE_ID ubuntu_os_upgrade.yml

PLAY [i-4352589c] **************************************************************

TASK [setup] *******************************************************************
ok: [ip-172-31-9-41.ap-southeast-2.compute.internal]

TASK [ubuntu_os_upgrade : Checking for if the Server OS is Debian Based Distributions] ***
skipping: [ip-172-31-9-41.ap-southeast-2.compute.internal]

TASK [ubuntu_os_upgrade : Updating the apt cache] ******************************
ok: [ip-172-31-9-41.ap-southeast-2.compute.internal]

TASK [ubuntu_os_upgrade : Running apt-get dist-upgrade to patch the system] ****
0ok: [ip-172-31-9-41.ap-southeast-2.compute.internal]

TASK [ubuntu_os_upgrade : Determine Reboot is needed or not] *******************
ok: [ip-172-31-9-41.ap-southeast-2.compute.internal]

TASK [ubuntu_os_upgrade : Rebooting ...] ***************************************
skipping: [ip-172-31-9-41.ap-southeast-2.compute.internal]

PLAY RECAP *********************************************************************
ip-172-31-9-41.ap-southeast-2.compute.internal : ok=4    changed=0    unreachable=0    failed=0



[centos@ip-172-31-2-87 simp]$ ansible-playbook -i local -e instance_id=$INSTANCE_ID --step awsec2_patching.yml

PLAY [local] *******************************************************************
Perform task: TASK: setup (N)o/(y)es/(c)ontinue: y

Perform task: TASK: setup (N)o/(y)es/(c)ontinue: *******************************

TASK [setup] *******************************************************************
ok: [localhost]
Perform task: TASK: De-register Instance from ELB (N)o/(y)es/(c)ontinue: y

Perform task: TASK: De-register Instance from ELB (N)o/(y)es/(c)ontinue: *******

TASK [De-register Instance from ELB] *******************************************
changed: [localhost]
Perform task: TASK: Taking the snapshot of the an instance (N)o/(y)es/(c)ontinue: y

Perform task: TASK: Taking the snapshot of the an instance (N)o/(y)es/(c)ontinue: ***

TASK [Taking the snapshot of the an instance] **********************************
changed: [localhost]
Perform task: TASK: Register Instance from ELB (N)o/(y)es/(c)ontinue: y

Perform task: TASK: Register Instance from ELB (N)o/(y)es/(c)ontinue: **********

TASK [Register Instance from ELB] **********************************************
changed: [localhost] => (item=ELB-TEST)

PLAY RECAP *********************************************************************
localhost                  : ok=4    changed=3    unreachable=0    failed=0   

[centos@ip-172-31-2-87 simp]$ 












APTLY WEBSERVER SETUP
=====================

<VirtualHost *:80>
        ServerName apt.example.com
        ServerAdmin moritz@example.com

        DocumentRoot /home/ubuntu/.aptly/public/
        <Directory /home/aptly/.aptly/public/>
                Options +Indexes +FollowSymLinks

                Require all granted
        </Directory>

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel notice
        CustomLog /var/log/apache2/apt/access.log combined
        ErrorLog /var/log/apache2/apt/error.log
        ServerSignature On
</VirtualHost>


Adding the gpg Key and repo to the Remote server
================================================

apt-key add aptly.asc

echo "deb htt://ip-172-31-3-58.ap-southeast-2.compute.internal trusty main" > /etc/apt/source.list.d/internal
