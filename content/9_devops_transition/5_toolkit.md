+++
+++

{{% section %}}

{{< slide template="subtitle" >}}

##  Herramientas para IaC

<!-- iac_ecosystem.png -->
<!-- https://martinfowler.com/bliki/PhoenixServer.html -->

---

## Ecosistema para Infraestructura

| Herramienta | Phoenix (Inmutable) | Snowflake (Estado deseado) |
| ------ | ------- | ------- |
| **Deploy** | - | Cambios config (AWS Cloudformation, Terraform) - Ejecución remota (Ansible) |
| **Runtime** | - | Cambios config (Terraform) - Ejecución remota (Ansible) |

---

## Ecosistema para Sistemas

| Herramienta | Phoenix (Inmutable) | Snowflake (Estado deseado) |
| ------ | ------- | ------- |
| **Base** | Images (ISO, AMI, BOX etc.) - Tools (Packer, Vagrant, VeeWee) | - |
| **Deploy** | Orquestración (Mesos, AWS ASG, Vagrant) - PXE (Cobbler, FAI, Kickstart) | Provisión (Cloud-init, Cfn-init, KickStart) |
| **Runtime** | - | Cambios config (Chef, Puppet, CFEngine) - Ejecución remota (Ansible, Saltstack) |

---

## Ecosistema para Aplicaciones

| Herramienta | Phoenix (Inmutable) | Snowflake (Estado deseado) |
| ------ | ------- | ------- |
| **Base** | Container Images (Docker) - Tools (Packer, Dockerfile) | - |
| **Deploy** | Orquestración (Mesos, Kubernetes) - Ejecución remota (Ansible, Saltstack) | Ejecución remota (Fabric, Capristrano, Saltstack, Ansible) |
| **Runtime** | - | Cambios config (Chef, Puppet, CFEngine) - Ejecución remota (Ansible, Saltstack) |

{{% /section %}}
