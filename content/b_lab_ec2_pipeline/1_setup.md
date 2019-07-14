+++
+++

{{% section %}}

{{< slide template="subtitle" >}}

## Primera parte: Preparando el entorno

---


## Descargando las fuentes

* El primer paso será clonar/descargar el repositorio del curso desde https://bitbucket.org/dcaba/devops-training/

```bash
git clone https://bitbucket.org/dcaba/devops-training.git
```

* Para facilitar el desarrollo de la práctica utilizaremos un entorno virtual Linux con soporte para Python y Docker (usaremos el contenido del directorio lab1/vagrant)
    * Instrucciones detalladas más adelante

---


## Materiales
```
|-- lab1
|   |-- app
|   |   |-- app.py
|   |   |-- Dockerfile
|   |   |-- requirements.txt
|   |-- cloudformation
|   |   |-- stack_asg.py
|   |-- vagrant
|   |   |-- Vagrantfile
```

---

## Instalación entorno de virtualización VirtualBox

> Si disponías de instalaciones previas (desactualizadas / no operativas), probablemente debas limpiar tu sistema

VirtualBox: [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)

<img src="https://upload.wikimedia.org/wikipedia/commons/d/d5/Virtualbox_logo.png" style="width:40%;">

---

## Instalación Vagrant para provisionar la VM

> Si dispones de una versión antigua de Vagrant, probablemente debas actualizarla para ir alineado con VirtualBox

Vagrant: [https://www.vagrantup.com/downloads.html](https://www.vagrantup.com/downloads.html)

<img src="https://upload.wikimedia.org/wikipedia/commons/8/87/Vagrant.png" style="width:30%;">

---

## Notas para la siguiente sección

> En caso de fallo inicializando Vagrant, asegúrate que el soporte de VT-x se encuentra habilitado en la BIOS.

> En entornos MS Windows, asegúrate que Windows Hyper-V está desactivado en "Windows Features on or off"

> Para evitar problemas con la importación de ficheros, puedes volver a clonar el repositorio dentro de la máquina Vagrant y no depender de los ficheros externos

---

## Arranque de la VM con Vagrant

Inicializar una máquina vacía, que descargará la imagen que después usaremos:
```bash
vagrant init williamyeh/ubuntu-trusty64-docker
vagrant up; vagrant halt; vagrant destroy
```

En **lab1/vagrant**, donde tenemos definido el **Vagrantfile**, arrancaremos la máquina virtual esta vez con la configuración que esperamos:
``` bash
$ cd lab1/vagrant

$ vagrant up --provider virtualbox

$ vagrant status
```

---

## Trabajando en la VM

Accederemos a la máquina virtual creada, donde desarrollaremos la práctica:
``` bash
$ vagrant ssh default
```

Y dentro de ésta, nos situaremos en el directorio de trabajo:
```bash
vagrant@devops-host ~ $ cd /vagrant/
vagrant@devops-host /vagrant $ ls
bitbucket-pipelines.yml  lab1  lab2  lab3  README.md  slides
vagrant@devops-host /vagrant $ cd lab1
vagrant@devops-host /vagrant/lab1 $
```

{{% /section %}}
