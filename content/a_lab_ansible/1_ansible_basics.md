+++
+++

{{% section %}}

{{< slide template="subtitle" >}}

## Arquitectura Ansible

![](https://d1jnx9ba8s6j9r.cloudfront.net/blog/wp-content/uploads/2016/11/Ansible-Architechture-What-Is-Ansible-Edureka.png){ width=60% }

* Automatización de servidores
    * Ejecución **distribuida**
    * Se **copia** via SSH el código Python y se **ejecuta** en cada equipo

* Automatización de equipos de red
    * Ejecución **centralizada**
    * Se **ejecuta** el código python en local y se accede a los equipos vi SNMP, SSH o mediante APIs
---

## Fichero de inventario

> Contiene los equipos (por IP o FQDN) que serán automatizados (y sus variables asociadas)

* **/etc/ansible/hosts**
* ANSIBLE_INVENTORY
* -i, --inventory-file

```
[routers]
router

[servers]
server01
server02

[servers:vars]
dns_server=1.1.1.1
```
---

## Ejecutando un Playbook

> **Playbook** es un fichero que continene las instrucciones de automatización

```yaml
---
    - name: PLAY 1 - Configure Interface Speed
      hosts: barcelona-dc
      connection: local
      gather_facts: no

      tasks:
        - name: TASK1 - Get interface information
          ios_command:
            commands:
                - show run | include interfaces
            provider:
                username: myusername
                password: mypassword
                host: "{{ inventory_hostname }}"
```
{{% /section %}}
