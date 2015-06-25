# Ansible OpenStack example with Spark at SLAC

This is adapted from: github.com/johandahlberg/ansible_spark_openstack


## How to use

1. Install ansible if you haven't

        pip install ansible

2. Download openstack credentials *.zip from your OpenStack distribution.
   Unzip it. (No passwords are actually stored)

3. (Can skip) python-novaclient should be included with ansible, otherwise:

        pip install python-novaclient

4. Before you can do anything with OpenStack, you need your credentials
   in you environment. Source openrc.sh from where you unzipped your credentials.
   This will ask for your password.

5. For ease of use, you should create a keypair using the nova client.
   The easiest way of doing this is using a new key from keypair-add.
   This isn't explicitly required, but it gives you the option of uploading
   the private key to the cluster so the machines can log in to each other.
   The output of keypair-add will be a private key you can use:

        nova keypair-add [KEYPAIR_NAME] > [KEYPAIR_NAME].pem

   Where KEYPAIR_NAME might be spark-keypair, for example. The private key should 
   probably be placed in ~/.ssh.

6. Modify spark-cluster.yml to use your keypair name, if it's not "spark-keypair"

7. Deploy your cluster:

        ansible-playbook -i localhost spark-cluster.yml --extra-vars "instance_state=present"

8. You will likely need to wait about ~8 minutes, depending, for all your VMs
   to become ready.

9. Now that your VMs are ready, you can install software. You'll want to use (and maybe modify)
   the openstack_inventory.py inventory if you have other unrelated VMs running.

        ansible-playbook -i openstack_inventory.py --private-key=~/.ssh/spark-keypair.pem deploy-spark.yml

10. You can destroy your cluster too:

        ansible-playbook -i localhost spark-cluster.yml --extra-vars "instance_state=absent"

