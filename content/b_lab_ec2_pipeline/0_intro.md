+++
+++

{{% section %}}

{{< slide template="subtitle" >}}

# Práctica 1: Del código al Cloud Provider

---

## Objetivo

![](https://docs.google.com/drawings/d/128ap-RQGC6z89dZ6Jg379V2-O2uKiNvfLzlV0ZEdZR0/pub?w=960&h=720)

---


## Primera parte: Preparación del entorno virtual

|   |
| --- |
| Clonar repositorio del curso |
| Instalación VirtualBox y Vagrant |
| Arrancar el entorno virtual de prácticas |

---


## Segunda parte: Definición de IaC y despliegue con aplicación

|   |
| --- |
| Empaquetar nuestra aplicación en un Contenedor Docker |
| Subir nuestro Contenedor a un repositorio |
| Definir nuestra infraestructura en código en AWS |
| Desplegar y validar la infraestructura definida |
| Validación del escenario en producción |

---

## Tercera parte: Modificaciones extras

|   |
| --- |
| Modifiquemos nuestra IaC |
| ¡Destruyamos la infraestructura! |

---


# Práctica 2: CI/CD de infraestructura dinámica

---

## Objetivo

![](https://docs.google.com/drawings/d/1-rk-dMDChhNxcUKdNeGxTLaKcrCqSZdO48Vb_oSW-0o/pub?w=1080&h=423)

---


## Materiales
```
|-- app
|   |-- Dockerfile
|   |-- app.py
|   |-- app.pyc
|   |-- requirements.txt
|   `-- test_app.py
|-- bitbucket-pipelines.yml
|-- cloudformation
|   |-- cfn_stack_update.py
|   |-- requirements.txt
|   `-- stack_asg.py
`-- scripts
    |-- bootstrap
    |-- cf_stack.sh
    |-- cibuild
    |-- cleanup
    |-- test_docker.sh
    |-- test_unit.sh
    `-- upload_docker_hub.sh
```

---


## Primera parte: Trabajando con nuestro propio repositorio

|   |
| --- |
| Nuevo repositorio |
| Creando e instalando un par de claves |
| Inicializando el repositorio dentro de la máquina virtual |

---


## Segunda parte: Testing en local

|   |
| --- |
| Unit Testing |
| Functional Testing |
| Mejorar los tests |

---


## Tercera parte: Integración y despliegue continuo

|   |
| --- |
| Creación de una Pipeline |
| Definición de pasos y variables |
| Ejecución de la Pipeline |
| Validación de resultados |
| Añadir nuevos pasos a la Pipeline |

{{% /section %}}
