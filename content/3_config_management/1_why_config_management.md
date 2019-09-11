+++
+++

{{% section %}}

{{< slide template="subtitle" >}}

## ¿Porque automatizar la gestión de configuración?

---

## Objetivos

1. Evitar **errores** en la provisión
2. Evitar el **drift** de configuración entre servidores, mantener **consistencia**
3. Facilitar compartir el **ownership** de los cambios entre todo el equipo
4. **Reusar buenas prácticas** en toda la flota de servidores
5. Permitir la **reproducción de múltiples entornos** de trabajo

---

## Requerimientos

1. Un nuevo servidor ha de poder ser **provisionado bajo demanda**, en pocos minutos o segundos
2. El proceso de provisión y aplicación de cambios debe ser **repetible, consistente, auto-documentado y transparente**
    * Idealmente *idempotente*
3. Un cambio de provisión o configuración se debe aplicar a los servidores **sin interacción humana**
4. Cada cambio se **aplica a todos** los servidores afectados, y también en los nuevos servidores creados
5. **Tests automáticos** deben ejecutarse para cualquier nuevo cambio
6. Debe ser sencillo y seguro hacer cambios al propio proceso
7. Los cambios de configuración o de proceso se deben **versionar y aplicar a diferentes entornos**
    * ¿PR como oportunidad para discusiones y validaciones del resto del equipo?

---




{{% /section %}}
