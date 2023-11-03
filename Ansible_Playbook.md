# This is a technical documention for Ansible PlayBook
#Hacktoberfest OpenSource Non-Code Contribution
Understanding Ansible Playbooks: 
A Comprehensive Guide

 ![image](https://github.com/sumitawate2312/DSA_Hacktoberfest2023/assets/148269404/a88b0ef4-1858-49aa-bd1c-a8debb460705)


Ansible 
Playbook Implementation

  ![image](https://github.com/sumitawate2312/DSA_Hacktoberfest2023/assets/148269404/9d8495b1-f19a-40d8-89c2-36fc21146f52)


 

What is an Ansible Playbook?


Ansible Playbooks, as defined by the official documentation, provide a powerful solution for repeatable, re-usable, and straightforward configuration management and multi-machine deployments. Especially tailored for deploying intricate applications, Ansible Playbooks are essential for managing large and complex environments efficiently.



Why Ansible Playbooks Matter?


Ansible Playbooks are a crucial component in the realm of automation and configuration management for several reasons:

1. Simplicity and Readability:
Ansible Playbooks are written in YAML, a human-readable data format. This simplicity makes them easy to write, understand, and modify, even for those without extensive programming experience. The clear, intuitive syntax ensures that playbooks are accessible to both beginners and experienced developers.

2. Repeatability and Reusability:
Playbooks provide a way to automate tasks consistently across different environments. Once created, a playbook can be reused across various projects and infrastructures. This reusability reduces the duplication of effort and promotes a standardized approach to configuration management.

3. Infrastructure as Code (IaC):
Ansible Playbooks enable the concept of Infrastructure as Code, where infrastructure configuration is managed in a version-controlled, systematic manner. This approach enhances collaboration, versioning, and rollback capabilities, similar to software development practices.

4. Orchestration and Parallel Execution:
Playbooks allow the orchestration of complex tasks involving multiple servers and services. Ansible can execute tasks in parallel, making it efficient for managing large-scale deployments. This parallelism accelerates the configuration process, ensuring timely updates across the infrastructure.

5. Flexibility and Modularity:
Playbooks are highly flexible, allowing users to define multiple plays and tasks in a structured manner. Modules, the building blocks of playbooks, cover a wide range of tasks, from package installation to cloud provisioning. Users can leverage these modules to customize playbooks based on specific requirements.

6. Integration and Extensibility:
Ansible integrates seamlessly with various platforms, cloud providers, and third-party tools. Playbooks can incorporate modules from different sources, enabling integration with diverse technologies. This extensibility ensures Ansible's relevance in heterogeneous IT environments.

7. Error Handling and Idempotency:
Playbooks provide mechanisms for error handling and idempotency. Tasks can be designed to handle errors gracefully, allowing playbooks to continue execution even when certain tasks fail. Additionally, Ansible modules are idempotent, ensuring that running a playbook multiple times results in a consistent, desired state without unintended side effects.

8. Security and Compliance:
Ansible promotes security best practices by allowing secure communication between the control node and managed hosts. Playbooks can enforce security policies and configurations, ensuring compliance with organizational standards and industry regulations.

9. Community and Documentation:
Ansible boasts a vibrant community and extensive documentation. Users can leverage community-contributed playbooks and roles, accelerating development and deployment processes. The wealth of resources available online makes it easier to troubleshoot issues and learn advanced Ansible techniques.

In summary, Ansible Playbooks matter because they offer a powerful, user-friendly solution for automating complex IT tasks, enhancing efficiency, consistency, and collaboration in modern infrastructures. By mastering Ansible Playbooks, organizations can achieve streamlined operations, reduced errors, and increased productivity in their IT workflows.


Ansible Playbook Syntax


Ansible playbooks are written in YAML format. For those who doesn't know about it - "YAML is a human-friendly data serialization standard   for all programming languages". It's commonly abbreviated as YAML     Ain't Markup Language.

It's very human-friendly and pretty easy to write code in YAML.

An Ansible playbook consists of one or multiple plays in an ordered list. Here you can think of a play is a part of code which executes instructions to achieve the overall goal of the playbook.

Each Play runs a task and each task calls an Ansible module to execute the instructions on one or more managed target nodes..
           Please note that a YAML file always starts with 3 hyphens ---.
            Below visualization gives easy of understanding on how Playbook Interacts

![image](https://github.com/sumitawate2312/DSA_Hacktoberfest2023/assets/148269404/63470ad7-0960-4cd5-9bd9-63566836e050)

 

Write your First PlayBook


            It's time get your hands dirty     
Here I will show how we can write a very simple playbook which will go to remote hosts install the apache server packages, copy the index.html file from my controller node to the web root of remote server, allow port 80 on firewall and restart the http service.

Here is our playbook file named install_configure_apache.yml




 

COPY

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
-	
 




Let's understand this file in more details.

It starts with 3 hyphens ---

hosts section defines on which hosts this playbook needs to be run. It's a mandatory field. You can run the tasks on the multiple machines, and the host's tag can have a group of host's entry as well as here we are running against our workers group.

become when set to true or yes activates privilege escalation

This playbook has 5 different tasks to perform different operations. tasks are the lists of the actions which our playbook performs on remote hosts. Every task is calling a different module such as yum , copy , service , firewalld etc. in order to execute the sequential instructions.

Please note that in YAML indentation is very important. So please take care of that.

And here is our index.html file which we will be copying over to target server's web root location.
<img width="504" alt="image" src="https://github.com/sumitawate2312/DSA_Hacktoberfest2023/assets/148269404/abf1d80e-cbf9-4291-b13a-dbae8f443d05">


 
Playbook Syntax Check


Before execution we should always check the syntax of our yaml playbook.

Run the below command to check the syntax of our playbook.

<img width="473" alt="image" src="https://github.com/sumitawate2312/DSA_Hacktoberfest2023/assets/148269404/5c981750-1a87-4a40-9bb6-8dc6ba20931b">



ansible-playbook is the command to execute playbook files. Here we are supplying our custom inventory file with -i argument and the parameter to check the syntax is --syntax-check .



Playbook Dry-Run


This step is often overlooked, but I strongly advise you to always perform it. Conducting a dry run ensures that your playbook will accurately reflect the changes made to your systems.

Executing an ansible-playbook with the --check flag won't apply any alterations to remote systems; it's essentially a simulation. Modules that support check mode outline the modifications they would have implemented. However, be mindful that if you have conditional tasks dependent on the outcomes of previous tasks, this simulation might not provide as much utility in those specific cases.

Let's proceed to run our playbook in --check mode.

 


[ansible_user@lco-ansible-master ansible]$ ansible-playbook instal SSH password:
BECOME password[defaults to SSH password]:


PLAY [Setting up Apache webserver] *******************************


TASK [Gathering Facts] ******************************************* ok: [worker2]
ok: [worker1]


TASK [Install apache packages] *********************************** changed: [worker2]
changed: [worker1]


TASK [copy index.html to web root] ******************************* changed: [worker1]
changed: [worker2]


TASK [ensure httpd service is running] *************************** changed: [worker2]
changed: [worker1]


TASK [Open port 80 for http access] ****************************** changed: [worker2]
changed: [worker1]


TASK [Restart the firewalld service to load in the firewall change changed: [worker1]
changed: [worker2]


PLAY RECAP ******************************************************* worker1	: ok=6	changed=5	unreachable=0
worker2	: ok=6	changed=5	unreachable=0


 
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


 
changed: [worker2]
TASK [copy index.html to web root] ******************************* changed: [worker1]
changed: [worker2]


TASK [ensure httpd service is running] *************************** changed: [worker1]
changed: [worker2]


TASK [Open port 80 for http access] ****************************** changed: [worker1]
changed: [worker2]


TASK [Restart the firewalld service to load in the firewall change changed: [worker1]
changed: [worker2]


PLAY RECAP ******************************************************* worker1	: ok=6	changed=5	unreachable=0
worker2	: ok=6	changed=5	unreachable=0





If you observe it runs all the tasks in a sequential manner. It has run successfully.
Now let's verify the changes by hitting the worker machines on port 80.

<img width="456" alt="image" src="https://github.com/sumitawate2312/DSA_Hacktoberfest2023/assets/148269404/c4f44339-9f41-42df-95dd-e811973d437b">

 
     Verify the changes from Web Browser:
 ![image](https://github.com/sumitawate2312/DSA_Hacktoberfest2023/assets/148269404/1e979d17-0b27-40ec-a8eb-c59901e90a83)



The presented results affirm that our playbook has performed precisely as anticipated.

Achieving Idempotency

When a playbook is executed multiple times, it ensures no further changes are made thereafter. Most Ansible modules validate if the desired final state has been achieved. If it has, they exit without making any alterations. This characteristic, known as idempotency, ensures that repetitive tasks do not alter the system's ultimate state. Modules exhibiting this behavior are termed idempotent.

From an end-user perspective, the objective is consistency: regardless of how many times the playbook is executed, the outcome should remain unaltered. It is important to note that not all playbooks and modules exhibit this behavior uniformly. Hence, it is highly recommended to rigorously test playbooks in a controlled test environment before deploying them in a production setting.

To confirm this behaviour, let's rerun our playbook once more and observe the output.
 

[ansible_user@lco-ansible-master ansible]$ ansible-playbook install SSH password:
BECOME password[defaults to SSH password]:
[WARNING]: Found both group and host with the same name: master

                            PLAY [Setting up Apache webserver] *******************************

                           TASK [Gathering Facts] *******************************************
                            ok: [worker1]
ok: [worker2]

                           TASK [Install Apache packages] ***********************************
                           ok:  [worker1]
ok: [worker2]


TASK [copy index.html to web root] ******************************* 
ok: [worker1]
ok: [worker2]


TASK [ensure httpd service is running] ***************************
  ok: [worker1]
ok: [worker2]


TASK [Open port 80 for http access] ******************************
 ok: [worker2]
ok: [worker1]

                         TASK [Restart the firewall service to load in the firewall change changed: [worker2]
changed: [worker1]


PLAY RECAP *******************************************************
                           worker1	: ok=6	changed=1	unreachable=0
worker2	: ok=6	changed=1	unreachable=0


 




As evident, the first four tasks remained unchanged as there were no alterations needed; the target nodes had already attained the desired state. The service restart, however, was executed as planned.

That concludes our discussion for now.

I trust that this article has enhanced your understanding of Playbooks.

If you found this article helpful, stay tuned for more insightful content. Thank you, and happy learning!






