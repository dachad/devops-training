+++
+++

{{% section %}}

{{< slide template="subtitle" >}}

## Tercera parte: Integración y despliegue continuo 

---

## Continuous Deployment

Paso a paso:

* Creamos una Pipeline en Bitbucket donde definimos los pasos
* Añadimos los test previos al despliegue
* Creamos el paquete de software (container) y lo subimos a un repositorio
* Creamos/actualizamos el despliegue de infraestructura que utilice esta nueva versión

<!--
https://bitbucket.org/awslabs/aws-cloudformation-bitbucket-pipelines-python
-->

---

## Creamos una Pipeline en BitBucket
<!--
https://docs.docker.com/docker-hub/bitbucket/
-->
* En la página web de BitBucket vamos a "Pipelines" y seleccionamos "Enabled Pipelines"
* En el template de la Pipeline pegamos el contenido de lab2/bitbucket-pipelines.yml y hacemos **commit**
* Validemos que la Pipeline falla con "No such file or directory!" (no hemos commiteado nuestro repo local!)

```yaml
image: python:2.7

# enable Docker for your repository
options:
  docker: true

pipelines:
  default:
    - step:
        caches:
          - pip
        script: # Modify the commands below to build your repository.
          - scripts/cibuild
```

---

## ¿Qué contiene CIBUILD?

```bash
#!/bin/bash

set -ex

./scripts/bootstrap

./scripts/test_unit.sh

./scripts/test_docker.sh

./scripts/upload_docker_hub.sh

./scripts/cf_stack.sh

./scripts/cleanup

```

---

## Creamos las variables de entorno en la Pipeline

* En **Setting** -> **Pipelines** -> **Environment variables** añadimos las siguientes variables:

<img src="../images/var_pipeline.png" style="width:60%;">

* Básicamente son las credenciales de Docker Hub y AWS (en la **siguiente slide**)
* Estas variables de entorno se usaran en los siguientes scripts.

---

## Tenemos las credenciales de DockerHub pero no de AWS...

* Use your AWS account email address and password to sign in to the AWS Management Console.
* In the top right of the console, choose your account name or number. Then choose My Security Credentials.
* Choose Continue to Security Credentials.
* Expand the Access Keys (Access Key ID and Secret Access Key) section.
* Choose your desired action.
* To create an access key

---

## Script de subida del contenedor

En `lab2/scripts/upload_docker_hub.sh` tenemos un script para construir y subir nuestro contenedor a Docker Hub

```bash
#!/bin/bash

set -ex

docker build -t $BITBUCKET_REPO_SLUG:latest app/.

docker login -u $DOCKERHUBUSER -p $DOCKERHUBPASS

docker tag $BITBUCKET_REPO_SLUG $DOCKERHUBUSER/$BITBUCKET_REPO_SLUG:latest

docker push $DOCKERHUBUSER/$BITBUCKET_REPO_SLUG:latest
```

---

## Script de creación del Cloudformation

En `lab2/scripts/cf_stack.sh` tenemos un script para crear el stack de Cloudformation.

Este script utiliza tres variables de entorno que debemos añadir en la Pipeline:

* STACKNAME: Es el nombre que recibirá el stack deployado en AWS
* KEYNAME: Es el nombre de la llave que hemos creado en AWS-EC2
* WEBSERVERPORT: Es el puerto en el que el container está escuchando

```bash
#!/bin/bash

set -ex

pip install -r cloudformation/requirements.txt

python cloudformation/stack_asg.py > stack_asg.json

cat stack_asg.json

python cloudformation/cfn_stack_update.py --stack-name ${STACKNAME} --docker-image ${DOCKERHUBUSER}'/'${BITBUCKET_REPO_SLUG}':latest' --template 'stack_asg.json' --keyname ${KEYNAME} --webserverport ${WEBSERVERPORT}

```

---

## Subida automática del Cloudformation

En `lab2/cloudformation/cfn_stack_update.py` tenemos un script que nos sube el Cloudformation automáticamente

```python
    client = boto3.client('cloudformation')

    list_of_stacks = client.list_stacks()

    create = True
    for stack in list_of_stacks['StackSummaries']:
        if args.stack_name in stack['StackId'] and stack['StackStatus'] != 'DELETE_COMPLETE':
            create = False

    log.info(create)
    if create:
        client.create_stack(
            StackName=args.stack_name,
            TemplateBody=open(args.template, 'r').read(),
            Capabilities=[
                'CAPABILITY_NAMED_IAM'
            ],
            Parameters=[
                {
                    'ParameterKey': 'DockerImage',
                    'ParameterValue': args.docker_image,
                },
...
```

---

## Volvemos a ejecutar la Pipeline...

Para hacerlo, podemos hacer otro commit en el repositorio con todos los datos en local

```bash
git add . 
git commit -m "message"
git push -f origin master
```

---

## Revisemos la ejecución de la Pipeline

<img src="../images/exec_pipeline.png" style="width:30%;">

...y verifiquemos nuestra infraestructura en AWS

---

## Do it yourself!

* ¿Realmente un cambio en el código se traduce en un nuevo deployment? ¿Podemos mejorarlo?
* ¿Cómo podríamos introducir un entorno de PRE?

<img src="../images/keep-calm-and-do-it-yourself-13.png" style="width:30%;">

{{% /section %}}
