+++
+++

{{% section %}}

{{< slide template="subtitle" >}}

## Segunda parte: Definición de IaC y despliegue con aplicación

---

## Descripción de la imagen Docker con la aplicación

Una vez tenemos el entorno preparado, conectados a la máquina con vagrant, el primer objetivo será crear una imagen Docker que contenga nuestra aplicación, tanto para desarrollo como producción.

Dentro de **app** (en la VM gestionada por vagrant, paths relativos respecto a **/vagrant/lab1**), disponemos de un **Dockerfile** que describe los pasos necesarios para crear la imagen del contenedor:
```
$ cat Dockerfile
FROM gliderlabs/alpine:3.6
MAINTAINER DevOps Training BCN "devops-training-bcn@googlegroups.com"
RUN apk add --no-cache python && \
    python -m ensurepip && \
    rm -r /usr/lib/python*/ensurepip && \
    pip install --upgrade pip setuptools && \
    rm -r /root/.cache
COPY . /app
WORKDIR /app
RUN pip install -r requirements.txt
ENTRYPOINT ["python"]
CMD ["app.py"]
```

[Cheat Sheet Dockerfile](https://github.com/wsargent/docker-cheat-sheet#dockerfile)

---


## ¡Nuestra aplicación!

En nuestro caso, la aplicación es un simple servidor web desarrollado con el framework Flask sobre Python. Se puede ver en **lab1/app/app.py**:
```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def index():
    return "Index!"

@app.route("/hello")
def hello():
    return "Hello World!"

def main():
    # listening in port 5000
    app.run(threaded=True, debug=True, host='0.0.0.0')

if __name__ == '__main__':
    main()
```

---


## Creación una imagen de Docker con nuestra aplicación

Crearemos el container a partir del Dockerfile (podéis personalizarlo con vuestros datos). Siéntete libre de bautizar también a tu imagen ;) 

Este proceso tarda unos minutos pues debe descargar la imagen inicial definida en **FROM** 
```
$ docker build -t <docker_image_name>:<tag> .
```
Una vez creada la imagen del contenedor, podemos verla disponible en:
```
$ docker images
REPOSITORY              TAG     IMAGE ID        CREATED         SIZE
<docker_image_name>     <tag>   d1667d4db76a    2 minutes ago   400.3 MB
```

---

## Arrancamos nuestra aplicación en local

A partir de la imagen creada, pasaremos a arrancarla en local para validar que el contenedor se ha creado correctamente y la aplicación funciona como deseamos:
```
$ docker run -d -p <exposed_port>:5000 <docker_image_name>:<tag>
```
El *exposed-port* nos permite mapear externamente un puerto diferente al que nuestra aplicación sirve por defecto (5000).

Revisemos que el container está funcionando (puede haber otro en marcha según el entrono de trabajo):
```
$ docker ps
CONTAINER ID        IMAGE                       COMMAND                  CREATED              STATUS              PORTS                    NAMES
349b69cbe16b        <docker_image_name>:<tag>   "python app.py"          About a minute ago   Up About a minute   0.0.0.0:4444->5000/tcp   grave_kare
```
Notar que docker nos da un nombre e ID aleatorios, y que nos indica cuál es el COMMAND que esta ejecutando, **python app.py**

---

## Validación del funcionamiento de nuestra aplicación en local

Ahora verifiquemos (manualmente) en local que la dupla aplicación/contenedor funciona:
```
$ curl localhost:<exposed-port>
Index!
$ curl localhost:<exposed-port>/hello
Hello World!
```

---

## Publicación de nuestro contenedor

El entorno de trabajo en local es útil para el desarrollo, pero para usar nuestra aplicación la debemos poder desplegar en producción.

Para eso, utilizaremos Docker HUB como repositorio (¡Primero debemos crear una cuenta personal! No es necesario crear un repositorio desde el interfaz web):
```
$ docker login --username=<yourhubusername> --password=<yourpassord>
```

Crearemos un segundo nombre (tag) de la imagen, esta vez, alineada con la estructura de Docker Hub:
```
$ docker tag <original_docker_image_new>:<original_tag> <yourhubusername>/<docker_image_name_new>:<tag_new>
```

Subimos la imagen a Docker Hub (puede tardar un tiempo pues la imagen se sube a Internet):
```
$ docker push <yourhubusername>/<docker_image_name_new>:<tag_new>
```

---

## Preparación del entorno de Producción

En nuestro caso utilizaremos un entorno de infraestructura dinámica, Amazon Web Services

Para comenzar deberemos disponer de una cuenta:
[https://portal.aws.amazon.com/gp/aws/developer/registration/index.html](https://portal.aws.amazon.com/gp/aws/developer/registration/index.html)

---

## Creación del stack en AWS

En AWS, una de las opciones para crear un entrono de infraestructura dinámica es usar el servico Cloudformation, que mediante unos datos en estructura JSON o YAML, permite crear múltiples recursos propios de AWS

En nuestro caso, para crear esta template, que subiremos a AWS, usaremos un script en python ubicado en **lab1/cloudformation/stack_asg.py**

[Lab 1 Cloudformation stack](https://bitbucket.org/dcaba/devops-training/src/d17b02b3b89db3bb716a79d5fd037988553c9888/lab1/cloudformation/stack_asg.py?at=master&fileviewer=file-view-default)

Si analizamos el script veremos que el stack crea múltiples recursos a partir de unos parámetros de entrada (por ejemplo, la imagen docker a utilizar), entre ellos un Autoscaling Group (ASG; una entidad que según unos parámetros irá añadiendo más o menos capacidad de computación) y un balanceador de carga asociado a este ASG

---

## Preparación del entorno Python 

En esta práctica generaremos el fichero de Cloudformation desde código, usando python/troposphere. Por lo tanto, y dentro de la máquina virtual arrancada con vagrant:

* Instalaremos Python2 y su gestor de paquetes (pip):
```
$ sudo apt-get update
$ sudo apt-get install python-pip
```

* Instalaremos la librería necesaria para crear el template de CloudFormation, **troposphere** (ver imports en el código para confirmar las dependencias), mas actualizaremos una dependencia de ésta:
```
$ sudo pip install --upgrade setuptools
$ sudo pip install troposphere
```

---

## Ejecución el script de creación

La ejecución del script sólo crea la template, que deberemos guardar en un fichero JSON para usar en la consola de Cloudformation/AWS:
```
$ cd /vagrant/lab1/cloudformation/
$ python stack_asg.py > stack.json
```

Este fichero estará disponible en nuestro directorio del curso pues el directorio "/vagrant" está mapeado fuera de la máquina virtual en "/devops-training":
```
config.vm.synced_folder "../../", "/vagrant", disabled: false
```

---

En caso de problemas con la sincronización de directorios podemos acceder desde nuestra máquina, sacamos el fichero fuera del entorno Vagrant (se puede usar cualquier cliente sftp), bien usando una key de autenticación (sin password):
```
$ scp -P 2222 -i ~/.vagrant.d/insecure_private_key vagrant@127.0.0.1:/vagrant/lab1/cloudformation/stack.json .
```

O autenticación via password:
```
$ scp -P 2222 vagrant@127.0.0.1:/vagrant/lab1/cloudformation/stack.json .
```

El password por defecto en esta máquina de Vagrant es **vagrant**

---

## Entrando a la consola de AWS

Aunque en los entornos habituales esta ejecución también se realiza vía código, en este caso utilizaremos la consola AWS. Atención a la esquina superior derecha, donde asignamos la región de trabajo para cada servicio AWS.

Como requisito para arrancar máquinas en EC2, hemos de crear un par de llaves, que se configurarán en las máquinas de Amazon y nos permitirían un acceso directo a la consola de éstas. Usa la sección **Key Pairs** en EC2 (la clave privada se descarga automáticamente).

---

## AWS Console

<img src="../images/awsconsole.png" style="width:65%;">

---

## Creación y ejecución del Cloudformation

Ya con el par de claves disponible, dentro de la sección CloudFormation (upload template), subimos el fichero especificando:

* **docker-image** (imagen docker que habéis subido a Docker Hub)
* **instance-type** (dejar valor por defecto, pues no tiene coste)
* **keyname** (deberemos haberla creada a priori)
* **webport** (puerto que expondrá el contenedor, que puede ser también el 5000)
* **scale-capacity** (número de instancias a desplegar)

Podemos aceptar el resto de valores por defecto. 
Revisemos la herramienta de previsión de costes, y el coste del ejemplo, que nos propone Cloudformation (recordad filtrar por la región en la que estemos desplegando). 
A partir de este punto, Cloudformation empieza a desplegar todos los recursos necesarios para hacer funcionar el entorno de producción.

---

## Validación del escenario en producción (i)

* Analicemos la información de nuestro stack creado por Cloudformation:
    * Events (deberemos poder seguir el proceso de despliegue del stack)
    * Outputs
    * Resources
    * Parameters
* Validemos en el servicio EC2 que se han creado:
    * 1 Autoscaling group (ASG)
    * 2 instancias EC2 debido a la política del ASG
    * 1 ELB ligado al ASG

---

## Validación del escenario en producción (ii)

* Dentro del ELB, podemos ver las instancias asociadas y si tiene el servicio "InService" (las máquinas tardan un par de minutos en arrancar). Esto ser valida según el Health Check definido. Y este ELB expone al exterior el puerto estándar 80.
* Accedamos a la url del ELB  puerto 80 para validar que funciona
* o a las instancias, en los puertos de servicio correspondientes
* Juguemos con el ASG, eliminando alguna instancia, para ver como ser recrean
* Validemos el ELB y su healthcheck

{{% /section %}}
