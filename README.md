# ICI001AS3A

[Palvelinten hallinta](https://opinto-opas.haaga-helia.fi/course/ICI001AS3A) at [Haaga-Helia](https://haaga-helia.fi)

[Course homepage](https://terokarvinen.com/palvelinten-hallinta/) (Tero Karvinen 2026)

## Lab environment

Five slave machines, called slave01...slave05. IPs described in `hosts.ini`

### Grouping

In Ansible usually you group your servers by role. In this course I have two groups. 

Note: `all` points to all configured inventory hosts. 

#### web_wervers

Machines slave01, slave03 and slave05 are in web server role (odd numbers).

#### db_servers

Machines slave02 and slave04 are in DB server role (even numbers).
