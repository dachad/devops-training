+++
+++

{{% section %}}

## Plataformas de Infraestructura Dinámica

---

## ¿Qué es una Plataforma de Infraestructura Dinámica?

> **Es una combinación software y hardware aplicada a datacenters,
que permite ofrecer recursos que pueden ser provisionados y gestionados de forma programática**
 
El avance de la Infraestructura Dinámica ha permitido consolidar lo que hoy conocemos como
**Cloud** (tras el intento del Grid Computing)

> No importa la tecnología base (cloud, virtualización o bare-metal), lo importante es que existan 
**mecanismos, herramientas y un nivel de automatización** que permita **su gestión dinámica**. 
Eso sí, **ciertas tecnologías ayudan** a implementar estas aproximaciones.

---

## Características del Cloud (NIST)

* **On-demand self-service** (provisión)
* **Broad network access** (disponible vía red con mecanismos estándares)
* **Resource Pooling** (multitenancy/multicliente)
* **Rapid elasticity** (añadir / quitar elementos en minutos / segundos)
* **Measured service** (control sobre el uso de recursos)

<!-- http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-145.pdf -->

---

## Características (II)

* **Programable**: La interacción con scripts y aplicaciones debe ser directa
    - API (Application Programming Interface)
    - REST API y SDK
* **On-demand**: La creación o destrucción debe ser inmediata y sin interacciones de terceros
    - El pago por servicio debe ser por uso, pago por horas/minutos/segundos o capacidad consumida
* **Self-service**: Los usuarios deben ser capaces de adaptar los recursos a sus requisitos
    - El departamento de Infraestructura no crea infraestructura, sino herramientas y servicios 
    para que cada equipo de desarrollo pueda generar, e incluso mantener, la infraestructura que necesita
    
---

## ¿Qué recursos proporcionan?
 
<div id="left">

Aunque muchas plataformas ofrecen una larga lista de servicios, todos combinan los siguientes tres:
<br>
<ul>
  <li>Computación</li>
  <li>Storage</li>
  <li>Networking</li>
</ul>

</div>
<div id="right">
<!-- https://docs.google.com/drawings/d/11AWajqyKC8Q3UswXEf5K2ISkyAIjI41cJ5z0jq17Uls/edit -->
<img src="https://docs.google.com/drawings/d/11AWajqyKC8Q3UswXEf5K2ISkyAIjI41cJ5z0jq17Uls/pub?w=582&h=582" style="width:100%;">

</div>


---

## Tipos de Cloud según "ownership"

* **Cloud Público**
    - Servicio estándar para múltiples clientes
    - Cada cliente paga sólo por lo que usa
* **Cloud Privado**
    - Servicio privada para múltiples consumidores de una misma organización 
    - La organización paga por el total de recursos desplegados
* **Cloud Híbrido**
    - Simplemente es que parte de la infraestructura corra en un cloud privado y otra en cloud público
    - Razones como regulación/seguridad, capacidad o amortización
* **Cloud de Comunidad**
    - Infraestructura compartida por diversas organizaciones
    - Puede ser interno o externo
    
---

## Ejemplos

| Tipo | Proveedores o Productos |
| --- | --------- |
| Public IaaS cloud | AWS, Azure, Digital Ocean, GCE, Rackspace Cloud |
| Private IaaS cloud | CloudStack, OpenStack, VMware vCloud |
| Bare-metal cloud | Cobbler, FAI, Foreman |

---


## Tipos de Cloud según modelo de servicio (NIST)

* **Software as a Service (SaaS)**
    - Una aplicación que se comparte entre diferentes usuarios
    - Ejemplos: Google Docs, Outlook365
* **Platform as a Service (PaaS)**
    - Una plataforma ya preparada para ser usada junto a las aplicaciones
    - Ejemplos: Heroku, Amazon DynamoDB, Google App Engine
* **Infrastructure as a Service (IaaS)**
    - Infraestructura hardware compartida para crear encima infraestructura para los servicios
    - Ejemplos: AWS EC2, GCE

---

## Diferentes definiciones del Cloud


<img src="https://get.cloudbolt.io/hs-fs/hubfs/blog-images/nist-cloud.gif?t=1493733144491&width=1240&height=730&name=nist-cloud.gif" style="width:130%;">

---

## La gran pregunta: ¿público o privado?

Aspectos a tener en cuenta:

* Seguridad y protección de datos
* Requisitos legales según la localización del hosting
* Capacidad variable
* Coste total de crear tu propio Cloud vs economía de escala
* Elemento diferenciador, cercanía al negocio o commodity
* Presencia de elementos legacy o soluciones físicas específicas

{{% /section %}}
