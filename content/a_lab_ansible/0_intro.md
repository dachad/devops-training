+++
+++

{{% section %}}

{{< slide template="subtitle" >}}

## Objetivo

* Automatizar la provisión de configuración en un entorno de producción hetereogéneo

![](https://docs.google.com/drawings/d/e/2PACX-1vRayY8-4P6lGt8PuKOs0N4kBeSiubcpWrpvzDyORn5dGAOXTUEn1-_rsQNT8z34-mfHcQy9iaa49aOi/pub?w=558&h=411)

---
## Prepararemos el escenario

En el directorio **lab3**, ejecutamos **vagrant up** para levantar el escenario del laboratorio.

Accedamos al servidor **MGMT** (**vagrant ssh mgmt**) y nos movemos al directorio /vagrant desde donde ejecutaremos Ansible para desplegar la configuración vía red de gestión fuera de banda.

Podemos validar que existe conectividad entre los equipos de la red 192.168.0.0/24

{{% /section %}}
