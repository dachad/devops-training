+++
+++

{{% section %}}

{{< slide template="subtitle" >}}

## Tercera Parte: Modificaciones extras

---

## Modifiquemos nuestra IaC

* Cambiar el puerto del ELB del puerto **80** al puerto **6789**
* Incrementar el número de servidores a 4
* Cambiar la aplicación para que acepte el path **"/devops"** y retorne el string **"16-02-2019"**
* Añadir en la respuesta de la aplicación la IP privada de la máquina que responde

<img src="../images/keep-calm-and-do-it-yourself-13.png" style="width:30%;">

---

## ¡Redespleguemos!

Para cada cambio, recuerda reflejar los cambios en código, actualizar tu stack y ver como los cambios se despliegan automáticamente

<img src="https://davidjellison.files.wordpress.com/2013/10/deploy-button.jpg" style="width:30%;">

Si no hay cambios en la infraestructura (por ejemplo, cambios en el container), CF no actualizará el Stack, en ese caso podemos simular el rollout mediante la terminación de instancias del Autoscaling Group.

---

## ¡Destruyamos la infraestructura!

Destruye tu stack de Cloudformation, probablemente no lo quieres activo toda la semana: *Delete Stack*

<img src="http://becomeatopweddingplannerblog.com/wp-content/uploads/2015/01/011215WeddingPlannersWasteMoneyV2.jpg" style="width:20%;">

{{% /section %}}
