+++
+++

{{% section %}}

{{< slide template="subtitle" >}}

## Imperativo vs Declarativo


<img src="../images/imperative-vs-declarative-xkcd.png" style="width:120%;">


<!-- ## Arquitectura Agent vs Agent-less -->

<!-- https://www.slideshare.net/MartinEtmajer/automated-deployments-with-ansible -->

---

## Herramientas para gestión de configuración en servidores
 
| Herramienta | Método | Aproximación |
| ------ | ------- | ------- |
| CFEngine | Pull | Declarativo |
| Chef | Pull | Imperativo |
| Puppet | Pull | Declarativo |
| SaltStack | Push | Declarativo e Imperativo |
| Ansible | Push | Declarativo e Imperativo |

---

## Popularidad

<img src="../images/googletrends.png" style="width:80%;">

---

## Ejemplo completo de Infraestructura Inmutable

> Objetivo: queremos desplegar contenedores en un cluster dinámico sobre un servicio de Cloud público

<!-- Cuidado; la separación entre infra, sistemas, y aplicaciones, aunque entiendo que es lógica, va en contra de la filosofía del curso -->

* Infraestructura: 
    * AWS Cloudformation para definir y generar la infraestructura en el Cloud
* Sistemas: 
    * Un proceso de Pipeline que crea mediante Packer las AMIs del Cluster
    * AWS ASG para adaptarnos a cambios de carga
    * No utilizaremos cambios de configuración pues nos basamos en infraestructura inmutable
* Aplicaciones:
    * La aplicaciones a desplegar vendrán creadas como contenedores Docker por la Pipeline de aplicación
    * Para correr los contenedores tendremos implementados un sistema de orquestración de cluster como Kubernetes
    * Finalmente, cualquier cambio en la aplicación, siguiendo el principio de infraestructura inmutable, genera un nuevo contenedor a desplegar

---

## Ansible

---
## Ejemplo con Ansible (1)

```
|-- your_folder
|   |-- site.yml                # Master Playbook
|   |-- roles
|   |   |-- router
|   |   |   |-- vars
|   |   |   |   |-- main.yml
|   |   |   |-- templates
|   |   |   |   |-- router.j2
|   |   |   |-- tasks
|   |   |   |   |-- main.yml
```


```yaml
$ cat site.yml
---
- name: Generate router configuration files
  hosts: localhost

  roles:
    - router
```
---
## Ejemplo con Ansible (2)

```yaml
$ cat roles/router/tasks/main.yml
---
- name: Generate configuration files
  template: src=router.j2 dest=/home/gituser/ANSIBLE/CFGS/{{item.hostname}}.txt
  with_items: "{{ test_routers }}"
```

```yaml
$ cat roles/router/vars/main.yml
---
test_routers:
   - { hostname: twb-sf-rtr1 }
   - { hostname: twb-sf-rtr2 }
   - { hostname: twb-la-rtr1 }
   - { hostname: twb-la-rtr2 }
   - { hostname: twb-den-rtr1 }
```

```bash
$ cat roles/router/templates/router.js
no service pad
service tcp-keepalives-in
service tcp-keepalives-out
service timestamps debug datetime msec localtime show-timezone
service timestamps log datetime msec localtime show-timezone
service password-encryption
!
hostname {{item.hostname}}
!
```
---
## Ejemplo con Ansible (3)

```ansible
$ ansible-playbook site.yml

PLAY [Generate router configuration files] ************************************

GATHERING FACTS ***************************************************************
ok: [localhost]

TASK: [router | Generate configuration files] *********************************
changed: [localhost] => (item={'hostname': 'twb-sf-rtr1'})
changed: [localhost] => (item={'hostname': 'twb-sf-rtr2'})

PLAY RECAP ********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0 
``` 

```bash
$ cat /home/gituser/ANSIBLE/CFGS/twb-sf-rtr1.txt
no service pad
service tcp-keepalives-in
service tcp-keepalives-out
service timestamps debug datetime msec localtime show-timezone
service timestamps log datetime msec localtime show-timezone
service password-encryption
!
hostname twb-sf-rtr1
```
---
## Ejemplo con Ansible (4)
* El ejemplo anterior es un uso particular de Ansible dado que el host que interpreta las recetas 
(y potencialmente puede propagar cambios a terceros) es el mismo contra el que se aplica la lógica, "localhost"
    * Habitualmente en el campo **hosts** de cada parte del **playbook** definimos los hosts donde aplicar los roles (en lugar de usar localhost)
* El grupo referenciado se define en el fichero **hosts** de Ansible

```
[lb]
foo.example.com     ansible_connection=ssh    ansible_user=username
bar.example.com     ansible_connection=ssh    ansible_user=username

[www]
www[01:50].example.com:5999
```

<img src="https://www.packtpub.com/sites/default/files/Article-Images/B03800_01_02.png" style="width:50%;">

---
## Infraestructura Inmutable con herramienta de gestión de despliegues
---
## Packer y Puppet  (1)

Packer permite crear imágenes idénticas para múltiples plataformas desde un único fichero de configuración

El template (Json):
```json
{
  "variables": {
    "aws_access_key": "",
    "aws_secret_key": ""
  },
  "builders": [{
    "type": "amazon-ebs",
    "access_key": "{{user `aws_access_key`}}",
    "secret_key": "{{user `aws_secret_key`}}",
    "region": "us-east-1",
    "source_ami": "ami-fce3c696",
    "instance_type": "t2.micro",
    "ssh_username": "ubuntu",
    "ami_name": "packer-example {{timestamp}}"
  }]
}
```
---
## Packer y Puppet  (2)

```bash
$ packer build -var 'aws_access_key=YOUR ACCESS KEY' \
               -var 'aws_secret_key=YOUR SECRET KEY' \
               example.json
               
==> amazon-ebs: amazon-ebs output will be in this color.
==> amazon-ebs: Creating temporary keypair for this instance...
==> amazon-ebs: Creating temporary security group for this instance...
==> amazon-ebs: Authorizing SSH access on the temporary security group...
==> amazon-ebs: Launching a source AWS instance...
==> amazon-ebs: Waiting for instance to become ready...
==> amazon-ebs: Connecting to the instance via SSH...
==> amazon-ebs: Stopping the source instance...
==> amazon-ebs: Waiting for the instance to stop...
==> amazon-ebs: Creating the AMI: packer-example 1371856345
==> amazon-ebs: AMI: ami-19601070
==> amazon-ebs: Waiting for AMI to become ready...
==> amazon-ebs: Terminating the source AWS instance...
==> amazon-ebs: Deleting temporary security group...
==> amazon-ebs: Deleting temporary keypair...
==> amazon-ebs: Build finished.
==> Builds finished. The artifacts of successful builds are:
--> amazon-ebs: AMIs were created:

us-east-1: ami-19601070
```
---
## Packer y Puppet  (3)

```json
{
    "variables": ...,
    "builders": [{
        "type": "amazon-ebs",
        "access_key": "{{ user `aws_access_key`}}",
        ...,
        "region": "eu-west-1",
        "source_ami": "ami-abcdef12",
        "ami_name": "the_name_of_the_new_ami",
        "iam_instance_profile": "puppet-provisioner",   #secret provisioning
    }],
    "provisioners": [
        {
            "type": "puppet-masterless",
            "hiera_config_path": "../puppet/hiera.yaml",
            "manifest_file": "../puppet/manifest/default.pp",
            "module_paths": [ "../puppet/modules" ]
        },
        ...,
        {
            "type": "shell",
            "script": "../common/install-hiera-key.sh"
        },
    ]
}       
```
 
{{% /section %}}
