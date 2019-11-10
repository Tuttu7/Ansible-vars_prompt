#### When running a playbook, you may wish to prompt the user for certain input, and can do so with the ‘vars_prompt’ section. The following playbook includes the vars_prompt and leverage it by adding a user across multiple linux servers.
```
---
- hosts: all
  gather_facts: false
  become: true
  vars_prompt:
    - name: first_lastname
      private: no
      prompt: "Enter the users first and last name"
    
    - name: username
      private: no
      prompt: "Enter the username of the user"
      
    - name: password
      private: yes
      prompt: "Enter the password"
      encrypt: "sha512_crypt"
      confirm: yes
      salt_size: 7
    
  tasks:
  
    - name: Adding user "{{ first_lastname }}"
      user:
        name: "{{ username }}"
        comment: "{{ first_lastname }}"
        shell: /bin/bash
        password: "{{ password }}"
```

#### Output :
```
[root@ip-172-31-12-96 ec2-user]# ansible-playbook add_linux_user.yml 
Enter the users first and last name: tuttu
Enter the username of the user: jolly
Enter the password: 
confirm Enter the password: 

PLAY [all] ***************************************************************************************************************************

TASK [Adding user "tuttu"] ***********************************************************************************************************
 [WARNING]: Platform linux on host 172.31.13.165 is using the discovered Python interpreter at /usr/bin/python, but future
installation of another Python interpreter could change this. See
https://docs.ansible.com/ansible/2.8/reference_appendices/interpreter_discovery.html for more information.

changed: [172.31.13.165]

PLAY RECAP ***************************************************************************************************************************
172.31.13.165              : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
#### Checking in the client machine we can see that the user is created there 
```
[root@ip-172-31-13-165 ~]# grep jolly /etc/passwd
jolly:x:1001:1001:tuttu:/home/jolly:/bin/bash
[root@ip-172-31-13-165 ~]# id jolly
uid=1001(jolly) gid=1001(jolly) groups=1001(jolly)
```

