+++
+++

{{% section %}}

{{< slide template="subtitle" >}}
## Hands-on
---

## Ejecución de playbooks en el laboratorio

> export ANSIBLE_HOST_KEY_CHECKING=False && **ansible-playbook -i inventory.cfg playbooks/nombre_playbook.yml**

---


## Recogemos datos (1)

> **play_recoger_datos.yml**

```yaml
---
- name: PLAY 1 - Recoger datos basicos de los routers
  hosts: routers
  connection: local
  gather_facts: no
  tasks:
    - name: Recoger datos de cada router
      vyos_facts:
        provider: "{{ base_provider }}"
    - name: Visualizar el tipo de equipo remoto
      debug:
        var: ansible_net_version
... # Continua en la siguiente slide
```
---

## Recogemos datos (2)

```yaml
... # Continua del anterior
- name: PLAY 2 - Recoger datos basicos de los servidores
  hosts: servers
  tasks:
    - name: Visualizar el sistema operativo
      debug:
        var: ansible_distribution
    - name: Visualizar la version del SO
      debug:
        var: ansible_distribution_version  
```
---

## Trucos

> Ejecutando en modo "Verbose" con -v nos mostrará toda la salida (equivale a un "debug" para toda la respuesta)

> **ansible-docs** es vuestro amigo para entender los diferentes módulos

---

## Validemos que no existe conectividad

Los dos Servidores pertenecen a redes diferentes y el Router todavía no está configurado.

> **play_validar_conectividad.yml** (-v para ver más detalle)

```yaml
---
- name: PLAY 1 - Validar que los servidores pueden comunicarse via ICMP
  hosts: servers
  tasks:
    - name: ICMP hacia el SERVER01
      command: ping -c 2 "{{ server01_ip }}"
      ignore_errors: yes
    - name: ICMP hacia el SERVER02
      command: ping -c 2 "{{ server02_ip }}"
      ignore_errors: yes    
```
---

## ¿de dónde salen las variables?

1. En el fichero de Inventory
2. -e / --extra-vars "var1=1 var2=2"
3. En el playbook (facts)
4. En el directorio **group_vars**
5. En el directorio **host_vars**

---

## Configuramos el router
---

## Variables y Templates

En **host_vars**/**router.yml**
```yaml
---
interfaces:
  - name: "eth2"
    ip: "10.10.0.1"
    mask: "24"
  - name: "eth3"
    ip: "10.20.0.1"
    mask: "24"
```
En playbooks/**templates**/interfaces/vyos.j2
```jinja
{% for interface in interfaces %}
set interfaces ethernet {{ interface.name }} address {{ interface.ip }}/{{ interface.mask }}
{% endfor %}
```
---

## Generamos el template de configuración y aplicamos

> **play_configurar_router.yml**

```yaml
---
- name: PLAY 1 - Aplicar configuracion sobre las interfaces
  hosts: router
  connection: local
  gather_facts: no
  tasks:
  - name: Generar fichero de configuracion
    template:
      src: "./interfaces/{{ os }}.j2"
      dest: "./configs/{{ inventory_hostname }}.cfg"  
  - name: Cargar configuracion de vyos
    vyos_config:
      src: "./configs/{{ inventory_hostname }}.cfg"
      provider: "{{ base_provider }}"

```
---

## Ya tenemos el router a punto!

```
 vagrant ssh router
Linux vyos 3.13.11-1-amd64-vyos #1 SMP Wed Aug 12 02:08:05 UTC 2015 x86_64
Welcome to VyOS.
This system is open-source software. The exact distribution terms for
each module comprising the full system are described in the individual
files in /usr/share/doc/*/copyright.
Last login: Sun May 27 15:18:46 2018 from 192.168.0.200

vagrant@router:~$ sh interfaces
Codes: S - State, L - Link, u - Up, D - Down, A - Admin Down
Interface        IP Address                        S/L  Description
---------        ----------                        ---  -----------
eth0             10.0.2.15/24                      u/u
eth1             192.168.0.3/24                    u/u
eth2             10.10.0.1/24                      u/u
eth3             10.20.0.1/24                      u/u
lo               127.0.0.1/8                       u/u
                 ::1/128
```
---

## Y de paso, un backup de configuración

```
cat lab3/playbooks/configs/router.cfg

set interfaces ethernet eth2 address 10.10.0.1/24
set interfaces ethernet eth3 address 10.20.0.1/24
```
---

## Probamos la connectividad

```
vagrant@router:~$ ping 10.10.0.100
PING 10.10.0.100 (10.10.0.100) 56(84) bytes of data.
64 bytes from 10.10.0.100: icmp_req=1 ttl=64 time=0.798 ms
^C
--- 10.10.0.100 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.798/0.798/0.798/0.000 ms

vagrant@router:~$ ping 10.20.0.101
PING 10.20.0.101 (10.20.0.101) 56(84) bytes of data.
64 bytes from 10.20.0.101: icmp_req=1 ttl=64 time=1.04 ms
^C
--- 10.20.0.101 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 0.304/0.672/1.041/0.369 ms
```
---

## Volvemos a validar la connectividad desde los servidores... 

> **No funciona :(**

---

## Configuramos los servidores
---

## Añadimos las rutas IP en los servidores (1)

En **host_vars/server01.yml** y **host_vars/server02.yml**

```yaml
---
routes:
  - destination: "10.20.0.0/24"
    next_hop: "10.10.0.1"
---
routes:
  - destination: "10.10.0.0/24"
    next_hop: "10.20.0.1"
```
---


## Añadimos las rutas IP en los servidores (2)

> **play_server_iproute.yml**

```yaml
---    
- name: PLAY 1 - Configurar las rutas en los servidores comunicarse via ICMP
  hosts: servers
  tasks:
    - name: Añadimos rutas IP
      command: ip route add {{ item.destination }} via {{ item.next_hop }}
      with_items: "{{ routes }}"
      become: yes
      become_user: root
```
---

## Ahora sí!!
{{% /section %}}
