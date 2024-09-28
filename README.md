# Automate Your k3s Cluster Setup on Raspberry Pi with Ansible

![ansi_01](https://github.com/user-attachments/assets/8423fe6e-df31-4b86-bab7-c912cc386896)

In my previous blog post about creating a lightweight Kubernetes cluster on Raspberry Pi, I explored the process of setting up a kubernetes cluster using Raspberry Pi devices. To take this a step further and simplify the process, I have decided to create a way to automate the provisioning of this cluster using Ansible. In this blog post, I will guide you through the steps to create a simple playbook that will automate the provisioning of your k3s environment on Raspberry Pi, building upon the knowledge and insights gained from my previous blog post.

Before we get started with the playbook, there are a few pre-requisites that you need to have:

Operating System (OS) installed on the Raspberry Pi’s: Make sure that you have an operating system installed on your Raspberry Pi’s. You can use Raspberry Pi OS or any other operating system that is compatible with k3s. In my setup, I’ve used Debian OS.
To use Ansible to automate the provisioning of your kubernetes cluster on Raspberry Pi, you will need to have Ansible installed on your local machine or any other host that can connect to the Raspberry Pi devices. You can install Ansible by following the instructions provided in the [official Ansible documentation](https://docs.ansible.com/ansible/latest/installation_guide/index.html). In my case, I’ve installed Ansible in my laptop (Mac). _Refer to for the pre-requisites and instructions_.

Assuming that you have the prerequisites in place, installing Ansible on your local machine is a straightforward process. To get started, open your terminal and run the command below (or check the specific command based on your operating system, which can be found in the [Ansible documentation](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html).

```bash
python3 -m pip install --user ansible
```

or in macOs:

```bash
brew install ansible
```

Once installation is completed, verify with:

```bash
GLP/~ $ ansible --version
ansible [core 2.14.5]
  config file = None
  configured module search path = ['/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/Cellar/ansible/7.5.0/libexec/lib/python3.11/site-packages/ansible
  ansible collection location = /.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.11.3 (main, Apr  7 2023, 19:25:52) [Clang 14.0.0 (clang-1400.0.29.202)] (/usr/local/opt/python/bin/python3.7)
  jinja version = 3.1.2
  libyaml = True
```

### Setting up Ansible to deploy our cluster

To run our Ansible playbook from our laptop, we need to ensure that the IP and hostnames of our nodes are added to the /etc/hosts file. This is a crucial step that ensures Ansible can connect to the correct nodes and perform the necessary actions. By following this step, we can add just the hostnames of our hosts in the inventory host file.

```bash
GLP/playbooks $ cat /etc/hosts

##Raspberry Cluster
192.168.18.35   k3s-master
192.168.18.52   worker01
192.168.18.53   worker02
```

Before you can start using Ansible to manage your hosts, it’s essential to establish secure communication between your control node and target nodes. This involves generating SSH keys and copying them to each node to ensure secure authentication and communication without the need for passwords.

```bash
GLP/Work $ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/gerardpontino/.ssh/id_rsa):

GLP/ansible $ ssh-copy-id iamroot@k3s-master
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/Users/glp/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
iamroot@192.168.18.35's password: 

Number of key(s) added:        1

Now try logging into the machine, with:   "ssh 'iamroot@k3s-master'"
and check to make sure that only the key(s) you wanted were added.
```

#### Inventory

The next step is to define the nodes and master in the `inventory/my-cluster/hosts` file. You will need to update the `[master]` and `[workers]` attributes to reflect the hostnames or IP addresses of your Raspberry Pi devices. Ensure that each device is included in the appropriate category, with the master listed under [master] and nodes listed under [workers].

The default location for this file is `/etc/ansible/hosts`. You can specify a different inventory file at the command line using the `-i <path>` option or in configuration using `inventory`. (see https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html)

```bash
[masternode]
k3s-master

[workers]
worker01
worker02
```

With the configuration of the inventory file complete, it’s time to test whether Ansible is working correctly. To do this, you can perform an Ansible ping command to verify that Ansible can successfully communicate with the hosts.

![1_-vi1rjKVDwThLUW-aGOJ-g](https://github.com/user-attachments/assets/43301ead-33da-486f-a634-cff6a5687b27)

### Ansible Playbook
- By cloning my playbook, you can automate your Kubernetes cluster setup without having to start from scratch. To get started, simply use the Git clone command and the following repository URL: https://github.com/gerardpontino/Kubernetes-Cluster-Setup-Automation. This will allow you to quickly and easily set up your cluster with minimal effort.

```bash
git clone https://github.com/gerardpontino/Kubernetes-Cluster-Setup-Automation
```
- Running the Ansible Playbook
```bash
ansible-playbook -i ../inventory/hosts install_k3s.yaml --user iamroot
```

![1_iTgkO9wbVf25591qSCw2WQ](https://github.com/user-attachments/assets/34e41f80-822c-485a-8710-128d713f455f)

To verify whether your cluster is configured correctly, you can log in to your master node and perform a simple check.

```bash
iamroot@k3s-master:~ $ sudo kubectl get nodes
NAME         STATUS   ROLES                  AGE   VERSION
k3s-master   Ready    control-plane,master   10h   v1.26.4+k3s1
worker01     Ready    <none>                 10h   v1.26.4+k3s1
worker02     Ready    <none>                 10h   v1.26.4+k3s1
```

## Conclusion

In this article, you learned how to use Ansible to deploy a simple lightweight Kubernetes cluster, which can sound intimidating at first but is actually simpler than you might expect.

>Ansible provides excellent documentation that is easy to use and navigate. You can find the official documentation for Ansible at https://docs.ansible.com/. This community-friendly resource provides detailed information on how to use Ansible, from beginner to advanced topics. Whether you’re looking to automate system provisioning, application deployment, or infrastructure management, Ansible’s documentation is a valuable resource that can help you get started and achieve your goals.>

In the future, I plan to share more information about additional ways to use Ansible for automation, as well as more detailed use cases that demonstrate its full potential. With Ansible’s flexibility and versatility, there are countless possibilities for automation, from simple tasks to complex workflows. I look forward to exploring these possibilities in more detail and sharing my insights and experiences with you. Stay tuned for more updates and ideas on how you can use Ansible to streamline your workflows and achieve your goals.
