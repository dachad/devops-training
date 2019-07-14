+++
+++

{{% section %}}

{{< slide template="subtitle" >}}

## Extendamos el laboratorio

Objectivo: Instalar un servidor HTTP mediante el uso de roles
---

## Roles

```
.
|-- apache
|   |-- files
|   |   |-- httpd.conf
|   |   |-- index.html
|   |   `-- ports.conf
|   |-- handlers
|   |   `-- main.yml
|   `-- tasks
|       |-- configure.yml
|       |-- install.yml
|       |-- main.yml
|       `-- service.yml
`-- common
    |-- defaults
    |   `-- main.yml
    |-- handlers
    |   `-- main.yml
    |-- tasks
    |   `-- main.yml
    `-- templates
        `-- default.j2
```
---

## Playbook

> **play_role_server.yml**

```yaml
---    
- name: PLAY 1 - Configurar el server01 como servidor web
  hosts: server01
  roles:
    - common
    - apache
```

Cada role incluye un conjunto de tareas con su configuración específica que puede combinarse

* **common**: Configuración base para todos los servidores
* **apache**: Configuración específica para los servidores web
---

## Validamos desde el server02

```
% vagrant ssh server02
% curl http://10.10.0.100:8010
Página HTMl para el curso de DevOps
```

Podemos comprobar que hemos utilizado la página web del curso y que se está escuchando en el puerto 8010
{{% /section %}}
