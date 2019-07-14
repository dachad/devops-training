+++
+++

{{% section %}}
## Gestión dinámica de la capacidad

¡Uno de los grandes beneficios del Cloud!

¿En base a qué decidimos aumentar/disminuir nuestra capacidad?

* Eventos preprogramados (pre-warming; efectos estacionales)
* Utilización de CPU
* Métricas de sistema
* Número de trabajos/mensajes pendientes

<div id="left">
<img src="http://a248.e.akamai.net/f/248/3214/1d/www.zones.com/images/static/ss-why-cloud-promo.jpg" style="width:80%;">
</div>

<div id="right">
<img src="http://docs.aws.amazon.com/autoscaling/latest/userguide/images/as-basic-diagram.png" style="width:100%;">
</div>

---

## Client vs Server side Service Discovery

<!-- https://www.nginx.com/blog/service-discovery-in-a-microservices-architecture/
https://www.slideshare.net/SreenivasMakam/service-discovery-using-etcd-consul-and-kubernetes -->


<div id="left">
Client-side
<img src="https://cdn-1.wp.nginx.com/wp-content/uploads/2016/04/Richardson-microservices-part4-2_client-side-pattern.png" style="width:80%;">
</div>

<div id="right">
Server-side
<img src="https://cdn-1.wp.nginx.com/wp-content/uploads/2016/04/Richardson-microservices-part4-3_server-side-pattern.png" style="width:100%;">
</div>

---

## Service Discovery

> "Distributed systems are hard"

Opciones:

* Anuncios Multicast/Broadcast
* Hardcoded IPs (individuales o rangos) + DNS lookup
* Registrarse individualmente en los servicios clientes
* Service Registry
    - Los servicios se registran via REST API
    - Los consumidores conusltan via REST API o DNS
    - Ofrece capacidad de Health Check y Balanceo de Carga
    - e.g. Consul, Eureka, ZooKeeper, etcd.
    
<!-- https://www.nginx.com/blog/service-discovery-in-a-microservices-architecture/ -->

---

## Service Discovery (ejemplo)

```json
# Registro HTTP API

{
    "name": "servicio-frontend",
    "address":  "10.0.0.12",
    "port": 8080,
    "check": {
        "http": "http://10.0.0.12:8080/health",
        "interval": "5s"
    }
}

$ curl -X PUT -d @servicio-frontend.json localhost:8500/v1/agent/service/register 

# Consulta HTTP API

$ curl -s http://192.168.99.106:8500/v1/catalog/service/servicio-frontend

[{
    "Node": "f2266cec556",
    "ServiceID": "servicio-frontend",
    "ServiceName": "servicio-frontend",
    "Address":  "10.0.0.12"
    "ServicePort": 8080,
}]
```

{{% /section %}}
