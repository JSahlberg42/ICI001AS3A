# h4 Pizza Fantasia

## x)

- Ominaisuuksien määrä:
    - DSL tarkoittaa: domain specific language.
    - Salt: 510 state functions
    - Puppet: 113 functions out of the box
    - **Oma huomio**: kumpikin sisältää paljon pillejä ja kelloja, joita kuitenkin käytetään suhteellisen vähän.
- Eniten käytetyt komennot:
    - Kaksi otantaa (yli 10 %):
        - United States Government Configuration Baseline
            - augeas (22,5 %)   (**oma huomio**: käytetään tiedostojen manipulointiin)
            - file (16,7 %)
            - service (14,7 %)
            - exec (14,7 %)
            - package (12,7 %)
        - Mozilla Release Engineering Puppet Manifests
            - case (20 %)
            - file (19 %)
            - package (10 %)
- Tärkeimmät rakennuspalikat:
    - Daemon setup (package, file, service)
    - App setup (package, file)
    - User management (user, group)
    - File manipulation (file, directory, symlink)
    - **Oma huomio**: Ei mitään lisämoduleja? Onko rajattu tutkimuksen ulkopuolella vai eikö oikeasti ole käyttöä?

## a) Räpylä

Pitkällisen harkinnan jälkeen valitsin demoniksi `finger`in. Kyseessä on RFC 1196:n mukainen tapa kysellä ihmismäisen tulosteen mukaisia tietoja koneen käyttäjästä (RFC 1196:1990).

Tarkistin komennolla `apt search fingerd`, onko Debianin APT-paketinhallinnassa kyseistä pakettia:

    jude@debiili:~$ apt search fingerd
    cfingerd/stable 1.4.3-8 arm64
    configurable finger daemon

    efingerd/stable 1.6.7-1+b2 arm64
    Another finger daemon for unix capable of fine-tuning your output.

    fingerd/stable 0.17-17+b1 arm64
    remote user information server

    jude@debiili:~$

Näytti olevan. Seuraavaksi tutkin tarkemmin pakettia `fingerd` komennolla `apt show fingerd`:

    jude@debiili:~$ apt show fingerd
    Package: fingerd
    Version: 0.17-17+b1
    Priority: optional
    Section: net
    Source: bsd-finger (0.17-17)
    Maintainer: Josue Ortega <josue@debian.org>
    Installed-Size: 94.2 kB
    Depends: update-inetd, netbase, libc6 (>= 2.38)
    Suggests: finger
    Replaces: netstd
    Homepage: http://ftp.linux.org.uk/pub/linux/Networking/netkit/
    Tag: interface::daemon, network::server, protocol::finger, role::program,
    use::searching, works-with::people
    Download-Size: 12.5 kB
    APT-Sources: http://deb.debian.org/debian trixie/main arm64 Packages
    Description: remote user information server
    Fingerd is a simple daemon based on RFC1196 that provides an interface to the
    "finger" program at most network sites.  The program is supposed to return a
    friendly, human-oriented status report on either the system at the moment or a
    particular person in depth.

    jude@debiili:~$

Huomasin `Suggest` kohdan ehdottavan myös asiakasohjelmaa `finger`. Asensin komennolla `sudo apt install -y fingerd finger` ja root-oikeuksin kummatkin paketit. Huomiona lisäparametri `-y`, joka vastaa `yes` mahdollisiin kysymyksiin:

```console
jude@debiili:~$ sudo apt install -y fingerd finger
[sudo] password for jude:
The following packages were automatically installed and are no longer required:
  apache2-bin   apache2-utils  libaprutil1-dbd-sqlite3  libaprutil1t64  linux-image-6.12.101+deb13-arm64
  apache2-data  libapr1t64     libaprutil1-ldap         libnss3-tools
Use 'sudo apt autoremove' to remove them.

Installing:
  finger  fingerd

Summary:
  Upgrading: 0, Installing: 2, Removing: 0, Not Upgrading: 22
  Download size: 30.9 kB
  Space needed: 184 kB / 30.7 GB available

Get:1 http://deb.debian.org/debian trixie/main arm64 finger arm64 0.17-17+b1 [18.4 kB]
Get:2 http://deb.debian.org/debian trixie/main arm64 fingerd arm64 0.17-17+b1 [12.5 kB]
Fetched 30.9 kB in 0s (87.3 kB/s)
Selecting previously unselected package finger.
(Reading database ... 151904 files and directories currently installed.)
Preparing to unpack .../finger_0.17-17+b1_arm64.deb ...
Unpacking finger (0.17-17+b1) ...
Selecting previously unselected package fingerd.
Preparing to unpack .../fingerd_0.17-17+b1_arm64.deb ...
Unpacking fingerd (0.17-17+b1) ...
Setting up finger (0.17-17+b1) ...
Setting up fingerd (0.17-17+b1) ...
grep: /etc/inetd.conf: No such file or directory
update-inetd: warning: cannot add service, /etc/inetd.conf does not exist
Processing triggers for man-db (2.13.1-1) ...
jude@debiili:~$
```

Asennus onnistui. Huomio kiinnittyi `apt`-mainintaan paketeista, joita ei enää tarvita, mutta ne ovat edelleen asennettuna. Päätin poistaa ne ehdotetulla komennolla `sudo apt autoremove`:

```console
jude@debiili:~$ sudo apt autoremove
REMOVING:
  apache2-bin   apache2-utils  libaprutil1-dbd-sqlite3  libaprutil1t64  linux-image-6.12.101+deb13-arm64
  apache2-data  libapr1t64     libaprutil1-ldap         libnss3-tools

Summary:
  Upgrading: 0, Installing: 0, Removing: 9, Not Upgrading: 22
  Freed space: 197 MB

Continue? [Y/n]
(Reading database ... 151921 files and directories currently installed.)
Removing apache2-bin (2.4.69-1~deb13u1) ...
Removing apache2-data (2.4.69-1~deb13u1) ...
Removing apache2-utils (2.4.69-1~deb13u1) ...
Removing libaprutil1-ldap:arm64 (1.6.3-3+deb13u1) ...
Removing libaprutil1-dbd-sqlite3:arm64 (1.6.3-3+deb13u1) ...
Removing libaprutil1t64:arm64 (1.6.3-3+deb13u1) ...
Removing libapr1t64:arm64 (1.7.5-1) ...
Removing libnss3-tools (2:3.110-1+deb13u5) ...
Removing linux-image-6.12.101+deb13-arm64 (6.12.101-1) ...
/etc/kernel/postrm.d/initramfs-tools:
update-initramfs: Deleting /boot/initrd.img-6.12.101+deb13-arm64
/etc/kernel/postrm.d/zz-update-grub:
Generating grub configuration file ...
Found background image: /usr/share/images/desktop-base/desktop-grub.png
Found linux image: /boot/vmlinuz-6.12.113+deb13-arm64
Found initrd image: /boot/initrd.img-6.12.113+deb13-arm64
Found linux image: /boot/vmlinuz-6.12.107+deb13-arm64
Found initrd image: /boot/initrd.img-6.12.107+deb13-arm64
Warning: os-prober will not be executed to detect other bootable partitions.
Systems on them will not be added to the GRUB boot configuration.
Check GRUB_DISABLE_OS_PROBER documentation entry.
Adding boot menu entry for UEFI Firmware Settings ...
done
Processing triggers for man-db (2.13.1-1) ...
Processing triggers for libc-bin (2.41-12+deb13u4) ...
jude@debiili:~$
```

Kun kokeilin toimivuutta komennolla `finger jude@localhost`, sain odottamattoman virheen: `finger: connect: Connection refused`. Aloin tutkimaan asiaa ja hoksasin, että `fingerd` tarvitsee `inetd`-superpalvelimen toimiakseen. Debianin pakettiluettelossa on `inetutils-inetd`, joten asensin sen lisäksi. Seuraavaksi tutkin `systemctl`-komennolla, mitä `inetd`-palvelulle kuului:

    jude@debiili:~$ systemctl status inetd
    ○ inetutils-inetd.service - GNU Network Utilities internet superserver
        Loaded: loaded (/usr/lib/systemd/system/inetutils-inetd.service; enabled; preset: enabled)
        Active: inactive (dead) (Result: exec-condition) since Thu 2026-11-12 21:12:14 EET; 1min 6s ago
    Invocation: 77288158df0649fdb24914f5c73766d2
    Condition: start condition unmet at Thu 2026-11-12 21:12:14 EET; 1min 6s ago
        Docs: man:inetutils-inetd(8)
                https://www.gnu.org/software/inetutils/manual/
        Process: 4941 ExecCondition=grep -qr ^[0-9A-Za-z/] /etc/inetd.conf /etc/inetd.d/ (code=exited, status=1/FAILURE)
    Mem peak: 1.7M
            CPU: 3ms
    jude@debiili:~$

Ei kuulunut hyvää. Inetd konfiguraatiossa ei ollut fingerd-määritystä. Sen luominen oli yksinkertaista. Hakemistoon `/etc/inetd.d/` lisäsin tiedoston `fingerd`, jonka sisältö on `finger stream tcp nowait nobody /usr/sbin/in.fingerd in.fingerd`. Tämä selvisi tutkimalla `inetd` man-sivustoa, joka kertoi formaation olevan:

```console
       Upon  execution, inetd reads its configuration information from a configuration file on the command line, by de‐
       fault, /etc/inetd.conf and /etc/inetd.d.  If the configuration pathname is a directory, all the files in the di‐
       rectory are read like a configuration file. All of the configuration files are read and merged.  There  must  be
       an  entry  for  each field in the configuration file, with entries for each field separated by a tab or a space.
       Comments are denoted by a "#" at the beginning of a line.  The fields of the configuration file are as follows:

             service name
             socket type
             protocol
             wait/nowait[.max]
             user[:group]
             server program
             server program arguments
```

Tämän jälkeen uudelleenkäynnistin `inetd`n komennolla `sudo systemctl restart inetd`. Kokeilin toimivuutta uudelleen:

    jude@debiili:~$ finger jude@localhost
    Login: jude           			Name: jude
    Directory: /home/jude               	Shell: /bin/bash
    Last login Thu Nov 12 17:59 (EET) on pts/0 from 192.168.64.1
    No mail.
    No Plan.
    jude@debiili:~$

Nyt toimi. Kun kokeilin etänä toimivuutta, ei se toiminut. Tajusin että palvelimen palomuuri estää liikennöinnin. Porasin palomuuriin reiän komennolla `sudo ufw allow 79/tcp`. Tämän jälkeen finger-kysely toimi myös palvelimen ulkopuolelta.


## b) Automaatti

Kävin läpi edellisen luvun pääkohdat ja lähdin luomaan uuden roolin `finger` ja siihen liittyvät tiedostot:

    ➜  ansible git:(2026-11-12) ✗ tree roles/finger
    roles/finger
    ├── files
    │   └── fingerd
    ├── handlers
    │   └── main.yml
    └── tasks
        └── main.yml

    4 directories, 3 files
    ➜  ansible git:(2026-11-12) ✗

Tiedosto `tasks/main.yml` sisältää:

    ➜  ansible git:(2026-11-12) ✗ cat roles/finger/tasks/main.yml
    ---
    - name: Install fingerd and inetutils-inetd
    become: true
    ansible.builtin.apt:
        name:
        - fingerd
        - inetutils-inetd
        state: present
        update_cache: true

    - name: install fingerd file in inetd config
    become: true
    ansible.builtin.copy:
        src: "fingerd"
        dest: "/etc/inetd.d/fingerd"
        mode: '0644'
    notify: restart inetd service

    - name: enable inetutils-inetd service
    become: true
    ansible.builtin.service:
        name: inetutils-inetd
        state: started
        enabled: true
    ➜  ansible git:(2026-11-12) ✗

Tavoitteena on järjestelmän sisältävän kaksi pakettia: `fingerd` ja `inetutils-inetd`, konfiguraatiotiedosto on kopioitu paikkaan `/etc/inetd.d/fingerd` (sisältö sama kuin aiemmassa luvussa laadittu), sekä `inetutils-inetd` demoni on ajossa sekä käynnistetään automaattisesti järjestelmän uudelleenkäynnistyksen yhteydessä.

Koska edellä notifioidaan `restart inetd service`-handleria, tarvitaan sen määritys tiedostoon `handlers/main.yml`:

    ➜  ansible git:(2026-11-12) ✗ cat roles/finger/handlers/main.yml

    - name: restart inetd service
    become: true
    ansible.builtin.service:
        name: inetutils-inetd
        state: restarted
    ➜  ansible git:(2026-11-12) ✗

Huomioitavaa on, että `inetd` käynnistetään vain jos fingerd-konfiguraatiotiedostoon tehdään muutoksia. Jos käytössä olisi muita demoneja, jotka ovat riippuvaisia `inetd`-superpalvelusta, silloin niille tulee tehdä vastaava notifiointi-menettely.

Vielä piti muistaa lisätä `site.yml`-tiedostoon `finger`-rooli. Minulla on labrassa useampi slave-palvelin; valitsin koneen `slave02` tähän. Lopulta `site.yml` näytti tältä tässä vaiheessa:

    ➜  ansible git:(2026-11-12) ✗ cat site.yml
    - hosts: all
    become: true
    roles:
        - hello
        - users
        - basic_tools
        - host_config

    - hosts: web_servers
    roles:
        - web_server_stuff

    - hosts: db_servers
    roles:
        - db_server_stuff

    - hosts: slave02
    roles:
        - finger
    ➜  ansible git:(2026-11-12) ✗


Testasin toimivuutta:

```
(.venv) ➜  ansible git:(2026-11-12) ✗ ansible-playbook -l slave02 site.yml

PLAY [all] *************************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [hello : copy dest=/tmp/hello-ansible, content=Trololoo!
] *********************************************************
changed: [slave02]

TASK [users : Create group sudoless name=sudoless, state=present] ******************************************************
ok: [slave02]

TASK [users : Create user antero name=antero, state=present, groups=sudoless, append=True, comment=Antero will be our future superhero] ***
ok: [slave02]

TASK [users : SSH key for antero user=antero, state=present, key=ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDH8GcP4UEnd2gvNqXJcuX+h6uMJMaBwIrmpOmgr4M9T jude@debiili] ***
ok: [slave02]

TASK [basic_tools : Install basic system utilities name=['tree', 'traceroute', 'htop', 'curl', 'finger'], state=present, update_cache=True] ***
ok: [slave02]

TASK [host_config : Update /etc/hosts from template src=hosts.j2, dest=/etc/hosts, owner=root, group=root, mode=0644] ***
ok: [slave02]

TASK [host_config : Add sudoless definition to sudoers dest=/etc/sudoers.d/sudoless, content=%sudoless ALL=(ALL) NOPASSWD: ALL, validate=visudo -cf %s] ***
ok: [slave02]

TASK [host_config : Create REMINDER.txt file in /tmp content=Rule one: don't screw up
Rule two: always check twice
Rule three: never forget rule one
, dest=/tmp/REMINDER.txt] ***
changed: [slave02]

TASK [host_config : modify REMINDER.txt file permissions and ownership path=/tmp/REMINDER.txt, owner=root, group=sudoless, mode=0666] ***
changed: [slave02]

PLAY [web_servers] *****************************************************************************************************
skipping: no hosts matched

PLAY [db_servers] ******************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [db_server_stuff : Install db server stuff name=['postgresql-17'], state=present, update_cache=True] **************
ok: [slave02]

PLAY [slave02] *********************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [finger : Install fingerd and inetutils-inetd name=['fingerd', 'inetutils-inetd'], state=present, update_cache=True] ***
changed: [slave02]

TASK [finger : install fingerd file in inetd config src=fingerd, dest=/etc/inetd.d/fingerd, mode=0644] *****************
changed: [slave02]

TASK [finger : enable inetutils-inetd service name=inetutils-inetd, state=started, enabled=True] ***********************
ok: [slave02]

RUNNING HANDLER [finger : restart inetd service name=inetutils-inetd, state=restarted] *********************************
changed: [slave02]

PLAY RECAP *************************************************************************************************************
slave02                    : ok=17   changed=6    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

(.venv) ➜  ansible git:(2026-11-12) ✗
```

Tulosteen lopussa oli muuttuneita asioita 6 kpl. Tulostetta tarkemmin tutkittaessa 3 kpl liittyi finger-asioihin ja 3 kpl liittyi `/tmp`-hakemiston tiedostoihin aiemmista harjoituksista. Kohdekone oli uudelleenkäynnistetty välissä, joten hakemisto oli tyhjentynyt uudelleenkäynnistyksen yhteydessä. Seuraavaksi kokeilin ajaa pelikirjan uudelleen, odotuksena 0 muutosta:

```
(.venv) ➜  ansible git:(2026-11-12) ✗ ansible-playbook -l slave02 site.yml

PLAY [all] *************************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [hello : copy dest=/tmp/hello-ansible, content=Trololoo!
] *********************************************************
ok: [slave02]

TASK [users : Create group sudoless name=sudoless, state=present] ******************************************************
ok: [slave02]

TASK [users : Create user antero name=antero, state=present, groups=sudoless, append=True, comment=Antero will be our future superhero] ***
ok: [slave02]

TASK [users : SSH key for antero user=antero, state=present, key=ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDH8GcP4UEnd2gvNqXJcuX+h6uMJMaBwIrmpOmgr4M9T jude@debiili] ***
ok: [slave02]

TASK [basic_tools : Install basic system utilities name=['tree', 'traceroute', 'htop', 'curl', 'finger'], state=present, update_cache=True] ***
ok: [slave02]

TASK [host_config : Update /etc/hosts from template src=hosts.j2, dest=/etc/hosts, owner=root, group=root, mode=0644] ***
ok: [slave02]

TASK [host_config : Add sudoless definition to sudoers dest=/etc/sudoers.d/sudoless, content=%sudoless ALL=(ALL) NOPASSWD: ALL, validate=visudo -cf %s] ***
ok: [slave02]

TASK [host_config : Create REMINDER.txt file in /tmp content=Rule one: don't screw up
Rule two: always check twice
Rule three: never forget rule one
, dest=/tmp/REMINDER.txt] ***
ok: [slave02]

TASK [host_config : modify REMINDER.txt file permissions and ownership path=/tmp/REMINDER.txt, owner=root, group=sudoless, mode=0666] ***
ok: [slave02]

PLAY [web_servers] *****************************************************************************************************
skipping: no hosts matched

PLAY [db_servers] ******************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [db_server_stuff : Install db server stuff name=['postgresql-17'], state=present, update_cache=True] **************
ok: [slave02]

PLAY [slave02] *********************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [finger : Install fingerd and inetutils-inetd name=['fingerd', 'inetutils-inetd'], state=present, update_cache=True] ***
ok: [slave02]

TASK [finger : install fingerd file in inetd config src=fingerd, dest=/etc/inetd.d/fingerd, mode=0644] *****************
ok: [slave02]

TASK [finger : enable inetutils-inetd service name=inetutils-inetd, state=started, enabled=True] ***********************
ok: [slave02]

PLAY RECAP *************************************************************************************************************
slave02                    : ok=16   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

(.venv) ➜  ansible git:(2026-11-12) ✗
```

Lopputulos oli odotusten mukainen. Vielä viimeinen kokeilu, toimii finger etäkoneelta:

    jude@debiili:~/src/ICI001AS3A/ansible$ finger @192.168.64.10
    No one logged on.
    jude@debiili:~/src/ICI001AS3A/ansible$

Testi oli odotusten mukainen. Etäkoneella ei sisäänkirjautuneita käyttäjiä. Kokeillaan vielä kysyä käyttäjän `antero` tietoja:

```
jude@debiili:~/src/ICI001AS3A/ansible$ finger antero@192.168.64.10
Login: antero         			Name: Antero will be our future superhero
Directory: /home/antero             	Shell: /bin/sh
Last login Thu Nov 12 21:55 (EET) on pts/0 from 192.168.64.1
No mail.
No Plan.
jude@debiili:~/src/ICI001AS3A/ansible$
```

Tämä toimi myös odotusten mukaisesti.

## c) Asetus

Muutetaan tiedostoa `roles/finger/files/fingerd` ja lisätään kommentti, joka kertoo tiedoston olevan Ansiblella hallittava:

```
(.venv) ➜  ansible git:(2026-11-12) ✗ cat roles/finger/files/fingerd
# This file is under Ansible control. Do not edit manually. You have been warned.
finger stream tcp nowait nobody /usr/sbin/in.fingerd in.fingerd
(.venv) ➜  ansible git:(2026-11-12) ✗
```

Ja ajetaan pelikirja uudelleen:

```
(.venv) ➜  ansible git:(2026-11-12) ✗ ansible-playbook -l slave02 site.yml

PLAY [all] *************************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [hello : copy dest=/tmp/hello-ansible, content=Trololoo!
] *********************************************************
ok: [slave02]

TASK [users : Create group sudoless name=sudoless, state=present] ******************************************************
ok: [slave02]

TASK [users : Create user antero name=antero, state=present, groups=sudoless, append=True, comment=Antero will be our future superhero] ***
ok: [slave02]

TASK [users : SSH key for antero user=antero, state=present, key=ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDH8GcP4UEnd2gvNqXJcuX+h6uMJMaBwIrmpOmgr4M9T jude@debiili] ***
ok: [slave02]

TASK [basic_tools : Install basic system utilities name=['tree', 'traceroute', 'htop', 'curl', 'finger'], state=present, update_cache=True] ***
ok: [slave02]

TASK [host_config : Update /etc/hosts from template src=hosts.j2, dest=/etc/hosts, owner=root, group=root, mode=0644] ***
ok: [slave02]

TASK [host_config : Add sudoless definition to sudoers dest=/etc/sudoers.d/sudoless, content=%sudoless ALL=(ALL) NOPASSWD: ALL, validate=visudo -cf %s] ***
ok: [slave02]

TASK [host_config : Create REMINDER.txt file in /tmp content=Rule one: don't screw up
Rule two: always check twice
Rule three: never forget rule one
, dest=/tmp/REMINDER.txt] ***
ok: [slave02]

TASK [host_config : modify REMINDER.txt file permissions and ownership path=/tmp/REMINDER.txt, owner=root, group=sudoless, mode=0666] ***
ok: [slave02]

PLAY [web_servers] *****************************************************************************************************
skipping: no hosts matched

PLAY [db_servers] ******************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [db_server_stuff : Install db server stuff name=['postgresql-17'], state=present, update_cache=True] **************
ok: [slave02]

PLAY [slave02] *********************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [finger : Install fingerd and inetutils-inetd name=['fingerd', 'inetutils-inetd'], state=present, update_cache=True] ***
ok: [slave02]

TASK [finger : install fingerd file in inetd config src=fingerd, dest=/etc/inetd.d/fingerd, mode=0644] *****************
changed: [slave02]

TASK [finger : enable inetutils-inetd service name=inetutils-inetd, state=started, enabled=True] ***********************
ok: [slave02]

RUNNING HANDLER [finger : restart inetd service name=inetutils-inetd, state=restarted] *********************************
changed: [slave02]

PLAY RECAP *************************************************************************************************************
slave02                    : ok=17   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

(.venv) ➜  ansible git:(2026-11-12) ✗
```

Kaksi muutosta, jotka ovat odotusten mukaisia. `inetd`-määritystiedosto muuttui, joten `inetd` käynnistettiin uudelleen. Tarkistetaan muutos vielä käsin:

```
jude@debiili:~/src/ICI001AS3A/ansible$ ssh 192.168.64.10 "cat /etc/inetd.d/fingerd"
# This file is under Ansible control. Do not edit manually. You have been warned.
finger stream tcp nowait nobody /usr/sbin/in.fingerd in.fingerd
jude@debiili:~/src/ICI001AS3A/ansible$ ssh 192.168.64.10 "ls -l /etc/inetd.d/fingerd"
-rw-r--r-- 1 root root 146 Nov 12 22:01 /etc/inetd.d/fingerd
jude@debiili:~/src/ICI001AS3A/ansible$
```

Tiedoston sisältö vastaa master-koneen tiedostoa ja aikaleima vastaa pelikirjan suoritusaikaa.

## d) Paikka remonttiin

Poistetaan käsin `inetutils-inetd` paketti käyttäen `apt purge`-komentoa ja varmistetaan ettei demoni ole ajossa:

```
jude@slave02:~$ sudo apt purge -y inetutils-inetd
[sudo] password for jude:
The following package was automatically installed and is no longer required:
  tcpd
Use 'sudo apt autoremove' to remove it.

REMOVING:
  inetutils-inetd*

Summary:
  Upgrading: 0, Installing: 0, Removing: 1, Not Upgrading: 8
  Freed space: 218 kB

(Reading database ... 39966 files and directories currently installed.)
Removing inetutils-inetd (2:2.6-3+deb13u3) ...
Processing triggers for man-db (2.13.1-1) ...
(Reading database ... 39956 files and directories currently installed.)
Purging configuration files for inetutils-inetd (2:2.6-3+deb13u3) ...
dpkg: warning: while removing inetutils-inetd, directory '/etc/inetd.d' not empty so not removed
jude@slave02:~$ systemctl status inetutils-inetd
Unit inetutils-inetd.service could not be found.
jude@slave02:~$
```

Seuraavaksi varmistus etäkoneelta, ettei palvelu toimi:

```
jude@debiili:~/src/ICI001AS3A/ansible$ finger antero@192.168.64.10
finger: connect: Connection refused
jude@debiili:~/src/ICI001AS3A/ansible$
```

Särki on. Aloitetaan korjaus ajamalla pelikirja:

```
(.venv) ➜  ansible git:(2026-11-12) ✗ ansible-playbook -l slave02 site.yml

PLAY [all] *************************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [hello : copy dest=/tmp/hello-ansible, content=Trololoo!
] *********************************************************
ok: [slave02]

TASK [users : Create group sudoless name=sudoless, state=present] ******************************************************
ok: [slave02]

TASK [users : Create user antero name=antero, state=present, groups=sudoless, append=True, comment=Antero will be our future superhero] ***
ok: [slave02]

TASK [users : SSH key for antero user=antero, state=present, key=ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDH8GcP4UEnd2gvNqXJcuX+h6uMJMaBwIrmpOmgr4M9T jude@debiili] ***
ok: [slave02]

TASK [basic_tools : Install basic system utilities name=['tree', 'traceroute', 'htop', 'curl', 'finger'], state=present, update_cache=True] ***
ok: [slave02]

TASK [host_config : Update /etc/hosts from template src=hosts.j2, dest=/etc/hosts, owner=root, group=root, mode=0644] ***
ok: [slave02]

TASK [host_config : Add sudoless definition to sudoers dest=/etc/sudoers.d/sudoless, content=%sudoless ALL=(ALL) NOPASSWD: ALL, validate=visudo -cf %s] ***
ok: [slave02]

TASK [host_config : Create REMINDER.txt file in /tmp content=Rule one: don't screw up
Rule two: always check twice
Rule three: never forget rule one
, dest=/tmp/REMINDER.txt] ***
ok: [slave02]

TASK [host_config : modify REMINDER.txt file permissions and ownership path=/tmp/REMINDER.txt, owner=root, group=sudoless, mode=0666] ***
ok: [slave02]

PLAY [web_servers] *****************************************************************************************************
skipping: no hosts matched

PLAY [db_servers] ******************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [db_server_stuff : Install db server stuff name=['postgresql-17'], state=present, update_cache=True] **************
ok: [slave02]

PLAY [slave02] *********************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [finger : Install fingerd and inetutils-inetd name=['fingerd', 'inetutils-inetd'], state=present, update_cache=True] ***
changed: [slave02]

TASK [finger : install fingerd file in inetd config src=fingerd, dest=/etc/inetd.d/fingerd, mode=0644] *****************
ok: [slave02]

TASK [finger : enable inetutils-inetd service name=inetutils-inetd, state=started, enabled=True] ***********************
ok: [slave02]

PLAY RECAP *************************************************************************************************************
slave02                    : ok=16   changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

(.venv) ➜  ansible git:(2026-11-12) ✗
```

Yksi muutos, eli `inetutils-inetd` paketti asentui. Testataan palvelun toimivuus etäkoneelta sormeilemalla anteroa:

```
jude@debiili:~/src/ICI001AS3A/ansible$ finger antero@192.168.64.10
Login: antero         			Name: Antero will be our future superhero
Directory: /home/antero             	Shell: /bin/sh
Last login Thu Nov 12 22:08 (EET) on pts/0 from 192.168.64.1
No mail.
No Plan.
jude@debiili:~/src/ICI001AS3A/ansible$
```

Finger-palvelu toimii jälleen.

## e) Idempotentti

Edellisen kohdan jälkeen pelikirjan uudelleenajaminen ei pitäisi muuttaa mitään:

```
(.venv) ➜  ansible git:(2026-11-12) ✗ ansible-playbook -l slave02 site.yml

PLAY [all] *************************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [hello : copy dest=/tmp/hello-ansible, content=Trololoo!
] *********************************************************
ok: [slave02]

TASK [users : Create group sudoless name=sudoless, state=present] ******************************************************
ok: [slave02]

TASK [users : Create user antero name=antero, state=present, groups=sudoless, append=True, comment=Antero will be our future superhero] ***
ok: [slave02]

TASK [users : SSH key for antero user=antero, state=present, key=ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDH8GcP4UEnd2gvNqXJcuX+h6uMJMaBwIrmpOmgr4M9T jude@debiili] ***
ok: [slave02]

TASK [basic_tools : Install basic system utilities name=['tree', 'traceroute', 'htop', 'curl', 'finger'], state=present, update_cache=True] ***
ok: [slave02]

TASK [host_config : Update /etc/hosts from template src=hosts.j2, dest=/etc/hosts, owner=root, group=root, mode=0644] ***
ok: [slave02]

TASK [host_config : Add sudoless definition to sudoers dest=/etc/sudoers.d/sudoless, content=%sudoless ALL=(ALL) NOPASSWD: ALL, validate=visudo -cf %s] ***
ok: [slave02]

TASK [host_config : Create REMINDER.txt file in /tmp content=Rule one: don't screw up
Rule two: always check twice
Rule three: never forget rule one
, dest=/tmp/REMINDER.txt] ***
ok: [slave02]

TASK [host_config : modify REMINDER.txt file permissions and ownership path=/tmp/REMINDER.txt, owner=root, group=sudoless, mode=0666] ***
ok: [slave02]

PLAY [web_servers] *****************************************************************************************************
skipping: no hosts matched

PLAY [db_servers] ******************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [db_server_stuff : Install db server stuff name=['postgresql-17'], state=present, update_cache=True] **************
ok: [slave02]

PLAY [slave02] *********************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [finger : Install fingerd and inetutils-inetd name=['fingerd', 'inetutils-inetd'], state=present, update_cache=True] ***
ok: [slave02]

TASK [finger : install fingerd file in inetd config src=fingerd, dest=/etc/inetd.d/fingerd, mode=0644] *****************
ok: [slave02]

TASK [finger : enable inetutils-inetd service name=inetutils-inetd, state=started, enabled=True] ***********************
ok: [slave02]

PLAY RECAP *************************************************************************************************************
slave02                    : ok=16   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

(.venv) ➜  ansible git:(2026-11-12) ✗
```

Mikään ei muuttunut. Aiemmin rikottiin orjakoneen tila poistamalla paketti. Kokeillaan vielä pysäyttämällä palvelu ja konfiguroimalla käsin sen tilaksi `disabled` ja varmistetaan ettei palvelu toimi:

```
jude@slave02:~$ sudo systemctl disable --now inetutils-inetd
[sudo] password for jude:
Synchronizing state of inetutils-inetd.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install disable inetutils-inetd
Removed '/etc/systemd/system/inetd.service'.
Removed '/etc/systemd/system/multi-user.target.wants/inetutils-inetd.service'.
jude@slave02:~$
logout
Connection to 192.168.64.10 closed.
jude@debiili:~/src/ICI001AS3A/ansible$ finger antero@192.168.64.10
finger: connect: Connection refused
jude@debiili:~/src/ICI001AS3A/ansible$
```

Tämän jälkeen ajetaan pelikirja. Odotuksena on korjaavat toimenpiteet:

```
(.venv) ➜  ansible git:(2026-11-12) ✗ ansible-playbook -l slave02 site.yml

PLAY [all] *************************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [hello : copy dest=/tmp/hello-ansible, content=Trololoo!
] *********************************************************
ok: [slave02]

TASK [users : Create group sudoless name=sudoless, state=present] ******************************************************
ok: [slave02]

TASK [users : Create user antero name=antero, state=present, groups=sudoless, append=True, comment=Antero will be our future superhero] ***
ok: [slave02]

TASK [users : SSH key for antero user=antero, state=present, key=ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDH8GcP4UEnd2gvNqXJcuX+h6uMJMaBwIrmpOmgr4M9T jude@debiili] ***
ok: [slave02]

TASK [basic_tools : Install basic system utilities name=['tree', 'traceroute', 'htop', 'curl', 'finger'], state=present, update_cache=True] ***
ok: [slave02]

TASK [host_config : Update /etc/hosts from template src=hosts.j2, dest=/etc/hosts, owner=root, group=root, mode=0644] ***
ok: [slave02]

TASK [host_config : Add sudoless definition to sudoers dest=/etc/sudoers.d/sudoless, content=%sudoless ALL=(ALL) NOPASSWD: ALL, validate=visudo -cf %s] ***
ok: [slave02]

TASK [host_config : Create REMINDER.txt file in /tmp content=Rule one: don't screw up
Rule two: always check twice
Rule three: never forget rule one
, dest=/tmp/REMINDER.txt] ***
ok: [slave02]

TASK [host_config : modify REMINDER.txt file permissions and ownership path=/tmp/REMINDER.txt, owner=root, group=sudoless, mode=0666] ***
ok: [slave02]

PLAY [web_servers] *****************************************************************************************************
skipping: no hosts matched

PLAY [db_servers] ******************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [db_server_stuff : Install db server stuff name=['postgresql-17'], state=present, update_cache=True] **************
ok: [slave02]

PLAY [slave02] *********************************************************************************************************

TASK [Gathering Facts ] ************************************************************************************************
ok: [slave02]

TASK [finger : Install fingerd and inetutils-inetd name=['fingerd', 'inetutils-inetd'], state=present, update_cache=True] ***
ok: [slave02]

TASK [finger : install fingerd file in inetd config src=fingerd, dest=/etc/inetd.d/fingerd, mode=0644] *****************
ok: [slave02]

TASK [finger : enable inetutils-inetd service name=inetutils-inetd, state=started, enabled=True] ***********************
changed: [slave02]

PLAY RECAP *************************************************************************************************************
slave02                    : ok=16   changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

(.venv) ➜  ansible git:(2026-11-12) ✗
```

Yksi muutos havaittu ja se liittyy edellä mainittuun `inetutils-inetd`-palveluun. Katson idempotenssin tulleen osoitetuksi.


## Lähteet

Karvinen 2023: [Configuration Management of Distributed Systems over Unreliable and Hostile Networks](https://westminsterresearch.westminster.ac.uk/item/w7vvz/configuration-management-of-distributed-systems-over-unreliable-and-hostile-networks). Luettu 12.11.2026.

[RFC 1196](https://www.rfc-editor.org/info/rfc1196/):1990. Finger User Information Protocol. D.P. Zimmerman.

## Tekoälyn käyttö

- **Mallit:** Claude Haiku 4.5 (aiempi tarkistus) ja GPT-6 Luna (oikoluku ja Markdown-tarkistus).
- **Harness:** Copilot SDK in VS Code.
- **Käytetyt promptit:**
  - “Check my homework #h4_pizza_fantasia.md. Instructions are here: https://terokarvinen.com/palvelinten-hallinta/#h4-pizza-fantasia - nore it's only h4.”
  - “Check spelling of my homework. Also check and improve markdown syntax. Do not modify content otherwise. Add section to my homework where describe use of AI (Model, harness, prompts used).”
