## Ansible Plybook for AWS 

#Make sure the files/awscreds.yml file is present with below information and ansible 2.0 is installed 



files/awscreds.yml

aws_id: XXXXXXXXXXXXXXX
aws_key: XXXXXXXXXXXXX
aws_region: XXXXXXXXXXXXX

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

