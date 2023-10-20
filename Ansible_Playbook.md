# This is a technical documention for Ansible PlayBook
#Hacktoberfest OpenSource Non-Code Contribution


![image](https://github.com/sumitawate2312/DSA_Hacktoberfest2023/assets/148269404/165b3935-3caf-43e2-9e6e-256958ec1537)


##Ansible Playbook Implementation


What is an Ansible Playbook?


As per the official documentation Ansible Playbooks offer a repeatable, re-usable, simple configuration management and multi-machine deployment system, one that is well suited to deploying complex applications.

Why do we need Ansible Playbooks?


In my previous article I demonstrated how we can use Ansible's ad hoc commands to manage remote servers. You use ad hoc commands when we want to perform basic tasks in an efficient manner. But you can not always use them to manage large and complex environments. Such as when you want to perform end to end deployments on your infrastructure.

There comes Ansible playbooks which provides greater flexibility, easier to write and manage.



Ansible Playbook Syntax


Ansible playbooks are written in YAML format. For those who doesn't know about it - "YAML is a human friendly data serialisation standard
 
for all programming languages". It's commonly abbreviated as YAML Ain't Markup Language.

It's very human friendly and pretty easy to write code in YAML.

An Ansible playbook consists one or multiple plays in an ordered list. Here you can think of a play is a part of code which executes instructions to achieve the overall goal of the playbook.

Each Play runs a task and each task calls an Ansible module to execute the instructions on one or more managed target nodes..

Please note that a YAML file always starts with 3 hyphens --- . Below visualisation will make it further easy for you to understand it.

![image](https://github.com/sumitawate2312/DSA_Hacktoberfest2023/assets/148269404/b40e99e8-895b-431e-8061-5b627d888c5a)



Write your First Playbook

It's time get your hands dirty     
Here I will show how we can write a very simple playbook which will go to remote hosts install the apache server packages, copy the index.html file from my controller node to the web root of remote server, allow port 80 on firewall and restart the http service.

Here is our playbook file named install_configure_apache.yml
---
-	name: Setting up Apache webserver hosts: workers
become: true tasks:
-	name: Install apache packages yum:
name: httpd
state: present


-	name: copy index.html to web root
copy: src=index.html dest=/var/www/html/index.html owner=apach


-	name: ensure httpd service is running service:
name: httpd
state: started


-	name: Open port 80 for http access firewalld:
service: http
permanent: true state: enabled

-	name: Restart the firewalld service to load in the firewall ch service:
-	name: firewalld state: restarted
  

Let's understand this file in more details.

**It starts with 3 hyphens ---

**hosts section defines on which hosts this playbook needs to be run. It's a mandatory field. You can run the tasks on the multiple machines, and the host's tag can have a group of host's entry as well as here we are running against our workers group.

**become when set to true or yes activates privilege escalation

**This playbook has 5 different tasks to perform different operations. tasks are the lists of the actions which our playbook performs on remote hosts. Every task is calling a different module such as yum , copy , service , firewalld etc. in order to execute the sequential instructions.

Please note that in YAML indentation is very important. So please take care of that.

And here is our index.html file which we will be copying over to target server's web root location.

[ansible_user@lco-ansible-master ansible]$ echo "<h1><font color=green>Hello from LearnCodeOnline! </font></h1>" > index.html

[ansible_user@lco-ansible-master ansible]$ cat index.html

<h1><font color=green>Hello from LearnCodeOnline! </font></h1>

 
Playbook Syntax Check

Before execution we should always check the syntax of our yaml playbook.

Run the below command to check the syntax of our playbook.

[ansible_user@lco-ansible-master ansible]$ ansible-playbook install_configure_apache.yml -i myinventory  --syntax-check

playbook: install_configure_apache.yml


ansible-playbook is the command to execute playbook files. Here we are supplying our custom inventory file with -i argument and the parameter to check the syntax is --syntax-check .



Playbook Dry-Run


This is the most important step which people often not perform. But I recommend you to always do it because a dry run ensures that your playbook is going to make what all changes on your systems.

When you run an ansible-playbook with --check it will not make any changes on remote systems. It's just a simulation. Modules that support check mode report the changes they would have made. In case you have conditional tasks which depends on the results of prior tasks then this might not be that useful for you.

Let's run our playbook in --check mode.

[ansible_user@lco-ansible-master ansible]$ ansible-playbook install_configure_apache.yml -i myinventory -kK --check
SSH password:
BECOME password[defaults to SSH password]:

PLAY [Setting up Apache webserver] ************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************
ok: [worker2]
ok: [worker1]

TASK [Install apache packages] ****************************************************************************************************
changed: [worker2]
changed: [worker1]

TASK [copy index.html to web root] ************************************************************************************************
changed: [worker1]
changed: [worker2]

TASK [ensure httpd service is running] ********************************************************************************************
changed: [worker2]
changed: [worker1]

TASK [Open port 80 for http access] ***********************************************************************************************
changed: [worker2]
changed: [worker1]

TASK [Restart the firewalld service to load in the firewall changes] **************************************************************
changed: [worker1]
changed: [worker2]

PLAY RECAP ************************************************************************************************************************
worker1                    : ok=6    changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
worker2                    : ok=6    changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

 
Let's understand this output.
 
All 6 tasks are in ok state (gathering facts is also considered as an task) ( ok=6 )

5 tasks are reporting changes made ( changed=5 )

No system is unreachable ( unreachable=0 )

No tasks are failing ( failed=0 )

No tasks are getting skipped ( skipped=0 )

No fatal errors reported during run ( rescued=0 )

No errors/warnings have been ignored during run ( ignored=0 )


Playbook Execution

As now we have our playbook ready it's time to execute it against the specified remote hosts.

Here is the command to execute a playbook.

[ansible_user@lco-ansible-master ansible]$ ansible-playbook install_configure_apache.yml -i myinventory -kK
SSH password:
BECOME password[defaults to SSH password]:

PLAY [Setting up Apache webserver] ************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************
ok: [worker1]
ok: [worker2]

TASK [Install apache packages] ****************************************************************************************************
changed: [worker1]
changed: [worker2]

TASK [copy index.html to web root] ************************************************************************************************
changed: [worker1]
changed: [worker2]

TASK [ensure httpd service is running] ********************************************************************************************
changed: [worker1]
changed: [worker2]

TASK [Open port 80 for http access] ***********************************************************************************************
changed: [worker1]
changed: [worker2]

TASK [Restart the firewalld service to load in the firewall changes] **************************************************************
changed: [worker1]
changed: [worker2]

PLAY RECAP ************************************************************************************************************************
worker1                    : ok=6    changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
worker2                    : ok=6    changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0



If you observe it runs all the tasks in a sequential manner. It has run successfully.

Now let's verify the changes by hitting the worker machines on port 80.

[ansible_user@lco-ansible-master ansible]$ curl lco-worker2.example.com:80
<h1><font color=green>Hello from LearnCodeOnline! </font></h1>

[ansible_user@lco-ansible-master ansible]$ curl lco-worker1.example.com:80
<h1><font color=green>Hello from LearnCodeOnline! </font></h1>


Verify the changes from Web Browser:
 
![image](https://github.com/sumitawate2312/DSA_Hacktoberfest2023/assets/148269404/f381b7cf-b5c8-444d-bbb2-01764fc811fa)

![image](https://github.com/sumitawate2312/DSA_Hacktoberfest2023/assets/148269404/aae5111e-a958-4d23-9ee3-15c372801808)

Great! These outputs clearly tells us that our playbook has done what was expected out of that.


How idempotency achieved


When we run an Ansible playbook more then one time it is going to make no changes thereafter. Most of the Ansible modules verifies whether the desired final state has already been achieved or not, if yes then they will exit without performing any actions, so that repeating the tasks does not change the final state. This is called idempotency and the modules which supports this behaviour are know as idempotent .

As an end user what we want is no matter how many times I run the playbook the outcome should be the same. However, not all playbooks and not all modules behave this way. So it's always recommended to test your playbooks in a test environment first before running them in Production.
 
To verify this behavior let's run our playbook one more time and see the output.

[ansible_user@lco-ansible-master ansible]$ ansible-playbook install_configure_apache.yml -i myinventory -kK
SSH password:
BECOME password[defaults to SSH password]:
[WARNING]: Found both group and host with same name: master

PLAY [Setting up Apache webserver] ************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************
ok: [worker1]
ok: [worker2]

TASK [Install apache packages] ****************************************************************************************************
ok: [worker1]
ok: [worker2]

TASK [copy index.html to web root] ************************************************************************************************
ok: [worker1]
ok: [worker2]

TASK [ensure httpd service is running] ********************************************************************************************
ok: [worker1]
ok: [worker2]

TASK [Open port 80 for http access] ***********************************************************************************************
ok: [worker2]
ok: [worker1]

TASK [Restart the firewalld service to load in the firewall changes] **************************************************************
changed: [worker2]
changed: [worker1]

PLAY RECAP ************************************************************************************************************************
worker1                    : ok=6    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
worker2                    : ok=6    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0


You can see here It hasn't changed anything thing for the first four tasks because there were no changes available and desired state was already achieved on target nodes. Restarting a service is always going to happen so that task has run.

That's all for now.

I believe after going through this article you will be very comfortable with the concept of Playbooks.

Hope you like the article. Stay Tuned for more. Thank you. Happy learning!

##Contributed By Sumit Awate





