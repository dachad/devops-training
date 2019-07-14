+++
+++

{{% section %}}

{{< slide template="subtitle" >}}

## Gestión de la configuración de servicios e Infraestructura

---

## Objetivos de la gestión automatizada

* Un nuevo servidor ha de poder ser **provisionado bajo demanda**, en pocos minutos o segundos
* Un cambio de provisión o configuración se debe aplicar a los servidores **sin interacción humana**
* Cada cambio se **aplica a todos** los servidores afectados, y también en los nuevos servidores creados
* El proceso de provisión y aplicación de cambios debe ser **repetible, consistente, auto-documentado y transparente**
    * Idealmente *idempotente*
* Debe ser sencillo y seguro hacer cambios al propio proceso
* **Tests automáticos** deben ejecutarse para cualquier nuevo cambio
* Los cambios de configuración o de proceso se deben **versionar y aplicar a diferentes entornos**
    * ¿PR como oportunidad para discusiones y validaciones del resto del equipo?

{{% /section %}}
