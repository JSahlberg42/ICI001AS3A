# h3 Demoni

## x

### Karvinen 2026: Apache installed with Ansible - quick notes
    
Article describes how to install package using `apt` tool, manipulation of configuration files and service state. (Karvinen T, 2026)

### Ansible Community Documentation: Handlers: running operations on change

Handlers are handy when there's no need run command or do something everytime when playbook is ran. Article describes how to use handlers with tasks. Like, if task has been changed the state, then the task notifies handler to do something.

### 'ansible-doc service'

The module is used to manipulate services running on target system. Target systems may have, for example, systemd or SysV style applications which orchestrate services. 

Option `enabled` means the service should be started after system boots up. Option `name` is required. With `state` it is possible to maintain idempotency of the service. `EXAMPLES` section shows easy copy-and-paste snippets to use in own tasks or handlers. (Ansible Community Documentation, 2026)

## a) Apassi

The objective was install Apache 2 package manually. At first, I checked proper package name using command `apt show <package>`:
```bash
jude@debiili:~/src/ICI001AS3A/ansible$ apt show apache2
Package: apache2
Version: 2.4.69-1~deb13u1
Priority: optional
Section: httpd
Maintainer: Debian Apache Maintainers <debian-apache@lists.debian.org>
Installed-Size: 596 kB
Provides: httpd, httpd-cgi
Pre-Depends: init-system-helpers (>= 1.54~)
Depends: apache2-bin (= 2.4.69-1~deb13u1), apache2-data (= 2.4.69-1~deb13u1), apache2-utils (= 2.4.69-1~deb13u1), media>
Recommends: ssl-cert
Suggests: apache2-doc, apache2-suexec-pristine | apache2-suexec-custom, ufw, www-browser
Homepage: https://httpd.apache.org/
Download-Size: 230 kB
APT-Sources: http://security.debian.org/debian-security trixie-security/main arm64 Packages
Description: Apache HTTP Server
 The Apache HTTP Server Project's goal is to build a secure, efficient and
 extensible HTTP server as standards-compliant open source software. The
 result has long been the number one web server on the Internet.
 .
 Installing this package results in a full installation, including the
 configuration files, init scripts and support scripts.

Notice: There is 1 additional record. Please use the '-a' switch to see it
jude@debiili:~/src/ICI001AS3A/ansible$
```

Looked good. Let's install it by using `apt install <package>` with sudo powers:
```bash
jude@debiili:~/src/ICI001AS3A/ansible$ sudo apt install apache2
The following packages were automatically installed and are no longer required:
  libnss3-tools  linux-image-6.12.101+deb13-arm64
Use 'sudo apt autoremove' to remove them.

Installing:
  apache2

Suggested packages:
  apache2-doc  apache2-suexec-pristine  | apache2-suexec-custom

Summary:
  Upgrading: 0, Installing: 1, Removing: 0, Not Upgrading: 22
  Download size: 230 kB
  Space needed: 596 kB / 30.8 GB available

Get:1 http://security.debian.org/debian-security trixie-security/main arm64 apache2 arm64 2.4.69-1~deb13u1 [230 kB]
Fetched 230 kB in 0s (1,173 kB/s)
Selecting previously unselected package apache2.
(Reading database ... 149530 files and directories currently installed.)
Preparing to unpack .../apache2_2.4.69-1~deb13u1_arm64.deb ...
Unpacking apache2 (2.4.69-1~deb13u1) ...
Setting up apache2 (2.4.69-1~deb13u1) ...
apache-htcacheclean.service is a disabled or a static unit not running, not starting it.
Processing triggers for man-db (2.13.1-1) ...
Processing triggers for ufw (0.36.2-9) ...
jude@debiili:~/src/ICI001AS3A/ansible$
```

No issues seen during installation. Next I checked is apache2 service running and is it enabled to start automagically:

```bash
jude@debiili:~/src/ICI001AS3A/ansible$ systemctl status apache2
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/apache2.service; enabled; preset: enabled)
     Active: active (running) since Fri 2026-11-06 18:42:29 EET; 41s ago
 Invocation: a1249cd515c4497faed97bd5d642d4b4
       Docs: https://httpd.apache.org/docs/2.4/
   Main PID: 5369 (apache2)
      Tasks: 55 (limit: 4562)
     Memory: 4.8M (peak: 5.3M)
        CPU: 21ms
     CGroup: /system.slice/apache2.service
             ├─5369 /usr/sbin/apache2 -k start
             ├─5370 /usr/sbin/apache2 -k start
             └─5371 /usr/sbin/apache2 -k start
jude@debiili:~/src/ICI001AS3A/ansible$
```

We can see its `enabled` and its running without issues. Good. Let's test does httpd service answering requests in TCP port 80 using `curl`command:

```bash
jude@debiili:~/src/ICI001AS3A/ansible$ curl -v http://localhost
* Host localhost:80 was resolved.
* IPv6: ::1
* IPv4: 127.0.0.1
*   Trying [::1]:80...
* Connected to localhost (::1) port 80
* using HTTP/1.x
> GET / HTTP/1.1
> Host: localhost
> User-Agent: curl/8.14.1
> Accept: */*
>
* Request completely sent off
< HTTP/1.1 200 OK
< Date: Fri, 06 Nov 2026 16:46:00 GMT
< Server: Apache/2.4.69 (Debian)
< Last-Modified: Tue, 01 Sep 2026 18:47:31 GMT
< ETag: "2e-65a7059ef0370"
< Accept-Ranges: bytes
< Content-Length: 46
< Content-Type: text/html
<
this is the default page of my new web server
* Connection #0 to host localhost left intact
jude@debiili:~/src/ICI001AS3A/ansible$
```

When using `-v` option with `curl` it provides more detailed output. In the output we can see server is Apache/2, which we were expecting too. Next task was to modify web server's front page so all users can edit it. 

I knew from the past that Apache's default home page location is `/var/www/html`. So I checked permissions of it by using `ls` command with `-la` parameters to get long listing of all files:

```bash
jude@debiili:~/src/ICI001AS3A/ansible$ ls -la /var/www/html
total 12
drwxr-xr-x 2 root root 4096 Sep  1 21:38 .
drwxr-xr-x 3 root root 4096 Sep  1 21:38 ..
-rw-r--r-- 1 root root   46 Sep  1 21:47 index.html
jude@debiili:~/src/ICI001AS3A/ansible$
```

From the output I saw the directory permissions for others, besides owners or group members, are just read. I was a lazy and bad administrator, so I changed permissions to allow write operations for others too using `chmod` command powered by sudo:

```bash
jude@debiili:~/src/ICI001AS3A/ansible$ sudo chmod 777 /var/www/html
jude@debiili:~/src/ICI001AS3A/ansible$ sudo chmod 666 /var/www/html/index.html
jude@debiili:~/src/ICI001AS3A/ansible$ ls -la /var/www/html
total 12
drwxrwxrwx 2 root root 4096 Sep  1 21:38 .
drwxr-xr-x 3 root root 4096 Sep  1 21:38 ..
-rw-rw-rw- 1 root root   46 Sep  1 21:47 index.html
jude@debiili:~/src/ICI001AS3A/ansible$
```

From the output I saw the solution is working, but I should rot in hell. 

## b) Moottorix

The objective was install nginx httpd. At first I removed installed apache2:

```bash
jude@debiili:~/src/ICI001AS3A/ansible$ sudo apt purge apache2 ; sudo apt install nginx
The following packages were automatically installed and are no longer required:
  apache2-bin   apache2-utils  libaprutil1-dbd-sqlite3  libaprutil1t64  linux-image-6.12.101+deb13-arm64
  apache2-data  libapr1t64     libaprutil1-ldap         libnss3-tools
Use 'sudo apt autoremove' to remove them.

REMOVING:
  apache2*

Summary:
  Upgrading: 0, Installing: 0, Removing: 1, Not Upgrading: 22
  Freed space: 596 kB

Continue? [Y/n]
(Reading database ... 149582 files and directories currently installed.)
Removing apache2 (2.4.69-1~deb13u1) ...
Processing triggers for man-db (2.13.1-1) ...
Processing triggers for ufw (0.36.2-9) ...
(Reading database ... 149530 files and directories currently installed.)
Purging configuration files for apache2 (2.4.69-1~deb13u1) ...
dpkg: warning: while removing apache2, directory '/var/www/html' not empty so not removed
dpkg: warning: while removing apache2, directory '/etc/apache2/sites-available' not empty so not removed
Processing triggers for ufw (0.36.2-9) ...
The following packages were automatically installed and are no longer required:
  apache2-bin   apache2-utils  libaprutil1-dbd-sqlite3  libaprutil1t64  linux-image-6.12.101+deb13-arm64
  apache2-data  libapr1t64     libaprutil1-ldap         libnss3-tools
Use 'sudo apt autoremove' to remove them.

Installing:
  nginx

Installing dependencies:
  nginx-common

Suggested packages:
  fcgiwrap  nginx-doc

Summary:
  Upgrading: 0, Installing: 2, Removing: 0, Not Upgrading: 22
  Download size: 685 kB
  Space needed: 1,955 kB / 30.8 GB available

Continue? [Y/n]
Get:1 http://security.debian.org/debian-security trixie-security/main arm64 nginx-common all 1.26.3-3+deb13u10 [112 kB]
Get:2 http://security.debian.org/debian-security trixie-security/main arm64 nginx arm64 1.26.3-3+deb13u10 [573 kB]
Fetched 685 kB in 0s (2,123 kB/s)
Preconfiguring packages ...
Selecting previously unselected package nginx-common.
(Reading database ... 149363 files and directories currently installed.)
Preparing to unpack .../nginx-common_1.26.3-3+deb13u10_all.deb ...
Unpacking nginx-common (1.26.3-3+deb13u10) ...
Selecting previously unselected package nginx.
Preparing to unpack .../nginx_1.26.3-3+deb13u10_arm64.deb ...
Unpacking nginx (1.26.3-3+deb13u10) ...
Setting up nginx-common (1.26.3-3+deb13u10) ...
Created symlink '/etc/systemd/system/multi-user.target.wants/nginx.service' → '/usr/lib/systemd/system/nginx.service'.
Setting up nginx (1.26.3-3+deb13u10) ...
Upgrading binary: nginx.
Processing triggers for man-db (2.13.1-1) ...
Processing triggers for ufw (0.36.2-9) ...
jude@debiili:~/src/ICI001AS3A/ansible$
```

Then I checked status of nginx service:

```bash
jude@debiili:~/src/ICI001AS3A/ansible$ systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; preset: enabled)
     Active: active (running) since Fri 2026-11-06 18:57:09 EET; 30s ago
 Invocation: 63176c5499a94368ac0833c90a651c92
       Docs: man:nginx(8)
    Process: 6096 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
    Process: 6097 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
   Main PID: 6127 (nginx)
      Tasks: 3 (limit: 4562)
     Memory: 2.9M (peak: 6.8M)
        CPU: 29ms
     CGroup: /system.slice/nginx.service
             ├─6127 "nginx: master process /usr/sbin/nginx -g daemon on; master_process on;"
             ├─6128 "nginx: worker process"
             └─6129 "nginx: worker process"
jude@debiili:~/src/ICI001AS3A/ansible$
```

Looked good, service is running and it's enabled to start in boot time. I knew default configuration of nginx is using the same directory as web root than Apache. So I just checked did `apt`changed permissions of web root (it should not):

```bash
jude@debiili:~/src/ICI001AS3A/ansible$ ls -l /var/www/html
total 8
-rw-rw-rw- 1 root root  46 Sep  1 21:47 index.html
-rw-r--r-- 1 root root 615 Nov  6 18:57 index.nginx-debian.html
jude@debiili:~/src/ICI001AS3A/ansible$
```

Nice, nginx default home page did not overwrite existing `index.html`; instead, package default file was renamed slightly. Objective achieved.

## c) Automoottorix

Objective is to install nginx in Ansible way. For that I created the `web_server_stuff` role:

```bash
(.venv) ➜  ansible git:(main) ✗ tree ./roles/web_server_stuff
./roles/web_server_stuff
├── files
│   └── index.html
├── handlers
│   └── main.yml
└── tasks
    └── main.yml

4 directories, 3 files
(.venv) ➜  ansible git:(main) ✗
```

Tasks file contains installation, ensuring service is enabled and home page permissions changes:

```yaml
---
- name: Install nginx
  become: true
  ansible.builtin.apt:
    name:
      - nginx
    state: present
    update_cache: true

- name: enable nginx service
  become: true
  ansible.builtin.service:
    name: nginx
    state: started
    enabled: true

- name: replace default web page and set permissions to world-writable
  become: true
  ansible.builtin.copy:
    src: "index.html"
    dest: "/var/www/html/index.html"
    mode: '0666'
  notify: restart nginx service

- name: replace default web root directory permission to world-writable
  become: true
  ansible.builtin.file:
    path: "/var/www/html"
    mode: '0777'
  notify: restart nginx service
```

I placed default home page file in `./roles/web_server_stuff/files` as source. I also used `notify` to inform handlers if something was changed. Handlers entry point file contains only one handler:

```yaml
- name: restart nginx service
  become: true
  ansible.builtin.service:
    name: nginx
    state: restarted
```

Then I ran the playbook. I limited scope to `web_servers` using the `-l` option:

```bash
(.venv) ➜  ansible git:(main) ✗ ansible-playbook site.yml -l web_servers

PLAY [all] *************************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave01]
ok: [slave05]
ok: [slave03]

TASK [hello : copy dest=/tmp/hello-ansible, content=Trololoo!
] *********************************************************
ok: [slave01]
ok: [slave03]
ok: [slave05]

TASK [users : Create group sudoless name=sudoless, state=present] ******************************************************
ok: [slave01]
ok: [slave03]
ok: [slave05]

TASK [users : Create user antero name=antero, state=present, groups=sudoless, append=True, comment=Antero will be our future superhero] ***
ok: [slave01]
ok: [slave05]
ok: [slave03]

TASK [users : SSH key for antero user=antero, state=present, key=ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDH8GcP4UEnd2gvNqXJcuX+h6uMJMaBwIrmpOmgr4M9T jude@debiili] ***
ok: [slave03]
ok: [slave01]
ok: [slave05]

TASK [basic_tools : Install basic system utilities name=['tree', 'traceroute', 'htop', 'curl', 'finger'], state=present, update_cache=True] ***
ok: [slave03]
ok: [slave05]
ok: [slave01]

TASK [host_config : Update /etc/hosts from template src=hosts.j2, dest=/etc/hosts, owner=root, group=root, mode=0644] ***
ok: [slave03]
ok: [slave01]
ok: [slave05]

TASK [host_config : Add sudoless definition to sudoers dest=/etc/sudoers.d/sudoless, content=%sudoless ALL=(ALL) NOPASSWD: ALL, validate=visudo -cf %s] ***
ok: [slave03]
ok: [slave01]
ok: [slave05]

TASK [host_config : Create REMINDER.txt file in /tmp content=Rule one: don't screw up
Rule two: always check twice
Rule three: never forget rule one
, dest=/tmp/REMINDER.txt] ***
ok: [slave01]
ok: [slave03]
ok: [slave05]

TASK [host_config : modify REMINDER.txt file permissions and ownership path=/tmp/REMINDER.txt, owner=root, group=sudoless, mode=0666] ***
ok: [slave03]
ok: [slave01]
ok: [slave05]

PLAY [web_servers] *****************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave01]
ok: [slave03]
ok: [slave05]

TASK [web_server_stuff : Install nginx name=['nginx'], state=present, update_cache=True] *******************************
changed: [slave01]
changed: [slave03]
changed: [slave05]

TASK [web_server_stuff : enable nginx service name=nginx, state=started, enabled=True] *********************************
ok: [slave03]
ok: [slave01]
ok: [slave05]

TASK [web_server_stuff : replace default web page and set permissions to world-writable src=index.html, dest=/var/www/html/index.html, mode=0666] ***
ok: [slave03]
ok: [slave01]
ok: [slave05]

TASK [web_server_stuff : replace default web root directory permission to world-writable path=/var/www/html, mode=0777] ***
changed: [slave05]
changed: [slave01]
changed: [slave03]

RUNNING HANDLER [web_server_stuff : restart nginx service name=nginx, state=restarted] *********************************
changed: [slave05]
changed: [slave01]
changed: [slave03]

PLAY [db_servers] ******************************************************************************************************
skipping: no hosts matched

PLAY RECAP *************************************************************************************************************
slave01                    : ok=16   changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
slave03                    : ok=16   changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
slave05                    : ok=16   changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

(.venv) ➜  ansible git:(main) ✗
```

From output I saw changes in Install nginx and permissions sections. That was expected outcome. Then I re-ran the same and expectation is nothing will be changed:

```bash
(.venv) ➜  ansible git:(main) ✗ ansible-playbook site.yml -l web_servers

PLAY [all] *************************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave03]
ok: [slave01]
ok: [slave05]

TASK [hello : copy dest=/tmp/hello-ansible, content=Trololoo!
] *********************************************************
ok: [slave03]
ok: [slave05]
ok: [slave01]

TASK [users : Create group sudoless name=sudoless, state=present] ******************************************************
ok: [slave01]
ok: [slave05]
ok: [slave03]

TASK [users : Create user antero name=antero, state=present, groups=sudoless, append=True, comment=Antero will be our future superhero] ***
ok: [slave01]
ok: [slave05]
ok: [slave03]

TASK [users : SSH key for antero user=antero, state=present, key=ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDH8GcP4UEnd2gvNqXJcuX+h6uMJMaBwIrmpOmgr4M9T jude@debiili] ***
ok: [slave03]
ok: [slave05]
ok: [slave01]

TASK [basic_tools : Install basic system utilities name=['tree', 'traceroute', 'htop', 'curl', 'finger'], state=present, update_cache=True] ***
ok: [slave01]
ok: [slave03]
ok: [slave05]

TASK [host_config : Update /etc/hosts from template src=hosts.j2, dest=/etc/hosts, owner=root, group=root, mode=0644] ***
ok: [slave01]
ok: [slave03]
ok: [slave05]

TASK [host_config : Add sudoless definition to sudoers dest=/etc/sudoers.d/sudoless, content=%sudoless ALL=(ALL) NOPASSWD: ALL, validate=visudo -cf %s] ***
ok: [slave01]
ok: [slave03]
ok: [slave05]

TASK [host_config : Create REMINDER.txt file in /tmp content=Rule one: don't screw up
Rule two: always check twice
Rule three: never forget rule one
, dest=/tmp/REMINDER.txt] ***
ok: [slave01]
ok: [slave03]
ok: [slave05]

TASK [host_config : modify REMINDER.txt file permissions and ownership path=/tmp/REMINDER.txt, owner=root, group=sudoless, mode=0666] ***
ok: [slave01]
ok: [slave03]
ok: [slave05]

PLAY [web_servers] *****************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave05]
ok: [slave01]
ok: [slave03]

TASK [web_server_stuff : Install nginx name=['nginx'], state=present, update_cache=True] *******************************
ok: [slave03]
ok: [slave05]
ok: [slave01]

TASK [web_server_stuff : enable nginx service name=nginx, state=started, enabled=True] *********************************
ok: [slave01]
ok: [slave03]
ok: [slave05]

TASK [web_server_stuff : replace default web page and set permissions to world-writable src=index.html, dest=/var/www/html/index.html, mode=0666] ***
ok: [slave03]
ok: [slave01]
ok: [slave05]

TASK [web_server_stuff : replace default web root directory permission to world-writable path=/var/www/html, mode=0777] ***
ok: [slave01]
ok: [slave05]
ok: [slave03]

PLAY [db_servers] ******************************************************************************************************
skipping: no hosts matched

PLAY RECAP *************************************************************************************************************
slave01                    : ok=15   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
slave03                    : ok=15   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
slave05                    : ok=15   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

(.venv) ➜  ansible git:(main) ✗
```

From recap I saw nothing was changed. Objective achieved.



## Sources
Karvinen T, 2026. [Apache installed with Ansible - quick notes](https://terokarvinen.com/apache-ansible/). Read 5.11.2026.

Karvinen T, 2026. Sudo without password. https://terokarvinen.com/passwordless-sudo/. Read 5.11.2026.

Ansible Community Documentation, 2026:  [Handlers: running operations on change](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_handlers.html). Read 5.11.2026

Ansible-doc: copy|apt|file|user|authorized_key|template

## AI Spell Checking

This document was processed for spell checking and grammar corrections using AI assistance on 2026-11-12. The spell checking was performed using:

**Language Model:** Claude (Anthropic), specifically Claude Sonnet 5.5 or later

**Harness:** Copilot SDK in VS Code - An AI-powered code assistant that provides intelligent code generation, review, and editing capabilities within the VS Code editor environment.

**Prompts Used:**
1. Initial prompt: "Do spell checking." - This directed the AI to perform comprehensive spell checking on the entire document.
2. Follow-up prompt: "Add mention to end of file using AI for spell checking. Add details too, like language model, harness and prompts used." - This prompted the documentation of the spell-checking process itself.

**Corrections Applied:** The AI identified and corrected 12 distinct spelling, grammar, and word-usage errors including typos ("hot" → "how"), grammar issues ("what we was" → "which we were"), formatting improvements ("copy'n'paste" → "copy-and-paste"), and stylistic enhancements for professional clarity.
