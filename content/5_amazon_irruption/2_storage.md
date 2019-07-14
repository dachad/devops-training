+++
+++

{{% section %}}
## Réplica de Datos: CAP theorem

<img src="../images/captheorem.png" style="width:100%;">

---


## NoSQL

![](http://www.kdnuggets.com/wp-content/uploads/sql-nosql-dbs.jpg)

* **Document model**: Los datos se guardan en documentos, tipo estructura JSON (e.g. CouchDB, MongoDB)
* **Graph model**: Las estructura de graphs simplifican el modelado de relaciones entre entidades (e.g. HyperGraphDB, Neo4j)
* **Key-Value & Wide-Column model**: Estructura básica, donde sólo se consulta via Keys (e.g. Cassandra, Redis, DynamoDB)

<!-- https://www.netsolutions.com/insights/5-things-you-must-consider-adopt-nosql-databases-mongodb/ -->

---

## Continuidad de datos

La persistencia de los datos es un reto particular de la infraestructura dinámica. ¿cómo podemos afrontarlo?

* **Replicar datos**, simplemente asegurar que los datos de una instancia estén replicados en otra antes de destruirla
* **Regenerar datos**, hay datos que pueden ser generados a partir del *System of record*, 
en estos casos no es necesario proteger los datos, sino ser capaz de regenerarlos
* **Delegar datos**, existen servicios de storage as a service (AWS S3, etc.) o simplemente storage externo (SAN, NFS), 
esto permite desacoplar los problemas y focalizar en cada parte
* **Back up a storage persistente**, los anteriores se refieren principalmente a *runtime data*, 
pero hay datos que deben permanecer después de la ejecución. Aunque utilicemos entornos Cloud,
 no podemos esquivar nuestras responsabilidades y tener claro físicamente donde se almacenan nuestros datos.
    * Truco: utilizar un proveedor diferente de infraestructura para ciertos datos es sencillo y proporciona un extra de seguridad

{{% /section %}}
