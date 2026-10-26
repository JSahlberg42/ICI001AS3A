# h1 Hei Ansiblen maailma

## x

### SSH and keys

- The most common key types are RSA and Ed25519
    - Ed25519 (elliptic curve) takes dominance from RSA RSN
    - Key is two parts:
        - private: never transfer from computer
        - public key: put that to newspaper if possible, nothing secret
- without keys, you need password for login
- usually BCP is not allowing login with password
- so use keys
- one key per purpose (or machine) - follow guidelines

### Hello ansible

- setup passwordless login first
- install ansible
- test ansible is working and create inventory
- if Python version nagging is disturbing, disable nagging or install propiate Python version in virtual env and continue living
- also remember configure default inventory to save keystrokes
- site file defines roles for slaves
- so under role-specific dir, tasks dir defines tasks
- YAML is strict about indent!
- When playing with user logins, sudo is needed.
    - disable passwd ask
    - give root passwd using --ask-become-pass (or -K)

### lab env

One master (debiili) and 5 slaves, named as slave0[1-5]
An another master is my local machine, can be used too

>UTM instance `slave00` is a master instance, use it only for cloning new instances!

## a-c

Passwordless login is working and Ansible is also working without nagging:
```
jude@debiili:~/src/ICI001AS3A/ansible$ ansible all -a 'uptime'
slave04 | CHANGED | rc=0 >>
 17:21:07 up 31 min,  1 user,  load average: 0.00, 0.01, 0.00
slave05 | CHANGED | rc=0 >>
 17:21:07 up 31 min,  1 user,  load average: 0.01, 0.01, 0.00
slave03 | CHANGED | rc=0 >>
 17:21:07 up 31 min,  1 user,  load average: 0.00, 0.02, 0.00
slave01 | CHANGED | rc=0 >>
 17:21:07 up 31 min,  2 users,  load average: 0.02, 0.03, 0.00
slave02 | CHANGED | rc=0 >>
 17:21:07 up 31 min,  1 user,  load average: 0.00, 0.00, 0.00
jude@debiili:~/src/ICI001AS3A/ansible$
```

Hello-file is written:

```
jude@debiili:~/src/ICI001AS3A/ansible$ ansible-playbook -K site.yml
BECOME password:

PLAY [all] *************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************
ok: [slave02]
ok: [slave05]
ok: [slave01]
ok: [slave04]
ok: [slave03]

TASK [hello : copy] ****************************************************************************************************
ok: [slave03]
ok: [slave04]
ok: [slave02]
ok: [slave05]
ok: [slave01]

TASK [basic_tools : Install basic system utilities] ********************************************************************
changed: [slave01]
changed: [slave03]
changed: [slave05]
changed: [slave02]
changed: [slave04]

PLAY RECAP *************************************************************************************************************
slave01                    : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
slave02                    : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
slave03                    : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
slave04                    : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
slave05                    : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

jude@debiili:~/src/ICI001AS3A/ansible$ cat site.yml
- hosts: all
  roles:
    - hello
    - basic_tools
jude@debiili:~/src/ICI001AS3A/ansible$ cat roles/hello/tasks/main.yml
- copy:
    dest: /tmp/hello-ansible
    content: "Trololoo!\n"
jude@debiili:~/src/ICI001AS3A/ansible$
```

Hello-file read:
```
jude@debiili:~/src/ICI001AS3A/ansible$ ansible all -a 'cat /tmp/hello-ansible'
slave05 | CHANGED | rc=0 >>
Trololoo!
slave01 | CHANGED | rc=0 >>
Trololoo!
slave02 | CHANGED | rc=0 >>
Trololoo!
slave03 | CHANGED | rc=0 >>
Trololoo!
slave04 | CHANGED | rc=0 >>
Trololoo!
jude@debiili:~/src/ICI001AS3A/ansible$
```

## d

`basic_tools` role's main task is installing `tree` and `traceroute` to target systems.

```
jude@debiili:~/src/ICI001AS3A/ansible$ ansible-playbook -K site.yml
BECOME password:

PLAY [all] *************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************
ok: [slave02]
ok: [slave05]
ok: [slave01]
ok: [slave04]
ok: [slave03]

TASK [hello : copy] ****************************************************************************************************
ok: [slave03]
ok: [slave04]
ok: [slave02]
ok: [slave05]
ok: [slave01]

TASK [basic_tools : Install basic system utilities] ********************************************************************
changed: [slave01]
changed: [slave03]
changed: [slave05]
changed: [slave02]
changed: [slave04]

PLAY RECAP *************************************************************************************************************
slave01                    : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
slave02                    : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
slave03                    : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
slave04                    : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
slave05                    : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

jude@debiili:~/src/ICI001AS3A/ansible$ cat site.yml
- hosts: all
  roles:
    - hello
    - basic_tools
jude@debiili:~/src/ICI001AS3A/ansible$ cat roles/basic_tools/tasks/main.yml
---
- name: Install basic system utilities
  become: true
  ansible.builtin.apt:
    name:
      - tree
      - traceroute
    state: present
    update_cache: true
jude@debiili:~/src/ICI001AS3A/ansible$
```