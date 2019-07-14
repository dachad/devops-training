+++
+++

{{% section %}}

## Desarrollo de Código y DevOps

DevOps impulsa el uso de código para configurar, desplegar, actualizar y solventar problemas que van desde la infraestructura a la Aplicación. 

> **Por ende, una gestión eficiente y eficaz del código pasa a ser un skill fundamental de los equipos y roles DevOps**

---

## Un código no gestionable ó no fiable sólo aporta más problemas al ecosistema

---

## No todo vale...

* *"Pero si es un script"*
* *"Si no forma parte del código de producción"*

> Aprovecha todas las "best practices" que han generado años de construcción de aplicaciones

---

## Ejemplo: Código duplicado y hardcoding

* Punto de partida: Aplicación que necesita un bucket de S3 por cliente

``` 
$ ls required_s3_buckets/
my_super_customer_1.py my_super_customer_2.py my_super_customer_3.py my_super_customer_4.py my_super_customer_5.py
```

```python
$ cat required_s3_buckets/my_super_customer_1.py
from troposphere import Output, Ref, Template
from troposphere.s3 import Bucket, PublicRead

t = Template()

t.add_description(
    "Bucket management for my_super_customer_1")

s3bucket = t.add_resource(Bucket("S3Bucket", AccessControl=PublicRead,))

t.add_output(Output(
    "BucketName",
    Value=Ref(s3bucket),
    Description="Name of S3 bucket for my_super_customer_1"
))

print(t.to_json())
```

---

## Necesidad

Queremos que los buckets sólo retengan la información durante 30 días:

```python
from troposphere import Output, Ref, Template
from troposphere.s3 import Bucket, PublicRead

t = Template()

t.add_description(
    "Bucket management for my_super_customer_1")

s3bucket = t.add_resource(Bucket(
    "S3Bucket", 
    AccessControl=PublicRead,
    LifecycleConfiguration=LifecycleConfiguration(Rules=[
        LifecycleRule(
            Id="S3BucketRule1",
            Status="Enabled",
            ExpirationInDays=30,
            )
        ])
    ))

t.add_output(Output(
    "BucketName",
    Value=Ref(s3bucket),
    Description="Name of S3 bucket for my_super_customer_1"
))

print(t.to_json())
```

---

## El problema 

* ¿Hemos de modificar los 5 ficheros (que podrían ser 30)?
* ¿Y también re-ejecutar / actualizar cada uno de los 30 stacks?

---

## Mejor aproximación 

```python
def create_bucket_for_customer(customername):
    t = Template()

    t.add_description(
        'Bucket management for {}'.format(customername))

    s3bucket = t.add_resource(Bucket(
        "S3Bucket", 
        AccessControl=PublicRead,
        LifecycleConfiguration=LifecycleConfiguration(Rules=[
            LifecycleRule(
                Id="S3BucketRule1",
                Status="Enabled",
                ExpirationInDays=30,
                )
            ])
        ))
    
    t.add_output(Output(
        "BucketName",
        Value=Ref(s3bucket),
        Description='Name of S3 bucket for {}'.format(customername)
    ))
    
    return t
```

---

## El "main" queda...

```python
customers = [
    "my_super_customer_1",
    "my_super_customer_2",
    "my_super_customer_3",
    "my_super_customer_4",
    "my_super_customer_5",
    ]

for customer in customers:
    print(create_bucket_for_customer(customer).to_json())

```

---

## Aún mejor aproximación

```
$ cat customer_list.csv
#customer_name,retention
my_super_customer_1,30
my_super_customer_2,30
my_super_customer_3,50
my_super_customer_4,60
my_super_customer_5,30
```

```python
customers = read_customer_list_from_csv(customer_list_file)

for customer in customers:
    print(create_bucket_for_customer(customer).to_json())

```

---

## Y aún habría mejores

* Cloudformation acepta el uso de variables
* ¿Podríamos integrar la automatización del alta de clientes en la llamada a API correspondiente?

---

## Patrones para definir la infraestructura

---

## Stack, la unidad básica

> **Stack**: conjunto de elementos de infraestructura que se definen/empaquetan/gestionan como una unidad

Un stack puede ser un único servidor, toda la infraestructura relacionada con una aplicación o hasta un CPD entero

---

## Organizando stacks

* Monolítico
* Tiers
* Stacks compartidos
* Microservicios

---

## Monolítico

<img src="https://docs.google.com/drawings/d/12OC-_Z7HzOSQ8w6xaVjEQkRDgYdb2H6dK4G35L0r624/pub?w=742&h=934" style="width:50%;">

---

## Dividir un entorno de aplicación en múltiples stacks / tiers


<img src="https://docs.google.com/drawings/d/1SZbMk-rkibmzWa0wrhQUDHtfhf2-2vX9pg2SVKb7yjs/pub?w=889&h=553" style="width:50%;">

---

## Compartir stacks entre aplicaciones

![](https://docs.google.com/drawings/d/1rS92zQzceYIWsLoR-FSYa9Qucv05MHS-kd1pyDOfxsM/pub?w=686&h=741){ width=50% }
<img src="https://docs.google.com/drawings/d/1rS92zQzceYIWsLoR-FSYa9Qucv05MHS-kd1pyDOfxsM/pub?w=686&h=741" style="width:50%;">

---

## Microservicios

<img src="https://docs.google.com/drawings/d/16q-IsZ2Sr_rm56fDHpkuq51TRT_TeXVekb3mgUzCzXU/pub?w=675&h=705" style="width:50%;">

---

## Entornos

* **Despliegues/"instancias" de un mismo stack(s) y servicio**, para propósitos como el testing
    - Por ejemplo: Desarrollo, Pre-producción y Producción
    - Misma infraestructura con diferente dimensionamiento o particularidades de configuración
    - También para sandboxing o "plataformas dedicadas" a propósitos/clientes
    
* **Mantener múltiples entornos de forma consistente** siempre ha sido un reto...
    - ...pero definir la infraestructura como código nos lo facilita

> **NADA** debe quedar fuera de la definición como código!

---

## Reusando ficheros de definición

* Para mantener la consistencia, debemos tener un **único fichero de definición de stack**
* Cada entorno se **personaliza con parámetros de entorno**
* Ejemplo:

```terraform
variable "environment" {}
resource "aws_instance" "web_server_1" {
    instance_type = "t2.micro"
    tags {
        Enviroment = "${var.environment}"
}
```
---

## Ventajas de la parametrización

* **Verificar** completamente la infraestructura antes de producción
* Facilita el **self-service**
* **Evita la duplicidad** de código
* **Mejora la adaptabilidad** del stack ante futuros cambios

---

## Como definir infraestructura como código

Esta definición se especifica en ficheros de configuración y 
diferentes herramientas se encargan de provisionar, modificar o eliminar 
los elementos en base a esa definición

---


## Evolución de la Infrastructura como código


{{% /section %}}

{{% section %}}

## The AWS journey

---

## API calls

```python
import boto3

s3 = boto3.client('s3')
bucket_name = 'my-bucket'
s3.create_bucket(bucket_name)
filename = 'file.txt'
s3.upload_file(filename, bucket_name, filename)
```

---

## Cloudformation

```json
{
    "AWSTemplateFormatVersion": "2010-09-09",
    "Resources": {
        "S3Bucket": {
            "Type": "AWS::S3::Bucket"
        },
    },
    "Outputs": {
        "S3BucketSecureURL": {
            "Value": {
                "Fn::Join": [
                    "",
                    [
                        "https://",
                        {
                            "Fn::GetAtt": [
                                "S3Bucket",
                                "DomainName"
                            ]
                        }
                    ]
                ]
            },
            "Description": "Name of S3 bucket"
        }
    }
}
```

---

## Troposphere

```python
from troposphere
import Output, Ref, Template
from troposphere.s3
import Bucket, PublicRead

t = Template()

t.add_description(
  "AWS CloudFormation Sample Template”

s3bucket = t.add_resource(Bucket("S3Bucket", AccessControl = PublicRead, ))

t.add_output(Output(
  "BucketName",
  Value = Ref(s3bucket),
  Description = "Name of S3 bucket to hold website content"
))

print(t.to_json())
```

---

## Sceptre

```bash
$ sceptre create dev/vpc.yaml

dev/vpc - Creating stack dev/vpc
VirtualPrivateCloud AWS::EC2::VPC CREATE_IN_PROGRESS
dev/vpc VirtualPrivateCloud AWS::EC2::VPC CREATE_COMPLETE
dev/vpc sceptre-demo-dev-vpc AWS::CloudFormation::Stack CREATE_COMPLETE
```

---

## AWS CDK

Amazon Cloud Development Kit

```python
from aws_cdk import (
    aws_s3 as s3,
    cdk
)

bucket = s3.Bucket(self, 
    "MyFirstBucket", 
    versioned=True,
    encryption=s3.BucketEncryption.KmsManaged,)
```

{{% fragment %}} *troposphere, but multilanguage?* {{% /fragment %}}

---

**Summarizing (some) AWS IaC options**

| Tool           | Declarative? | Deployment/Update tool? | Config-driven | Programmatic | Language  |
|----------------|:------------:|:-----------------------:|:-------------:|:------------:|:---------:|
| AWS API        |       N      |            N            |       N       |      Y       |    N/A    |
| Cloudformation |       Y      |       More or less      |       Y       |      N       | JSON/YAML |
| Troposphere    |       Y      |            N            |       N       |      Y       |   Python  |
| Sceptre        |      N/A     |            Y            |      N/A      |     N/A      |    N/A    |
| CDK            |       Y      |            Y            |       N       |      Y       |     *     |
{{% /section %}}

---


{{% section %}}

## Multicloud

---

## Terraform(-v11)

```hcl
provider "aws" {
  region = "us-west-2"
}

resource "aws_s3_bucket" "example" {
  bucket = "my-test-s3-terraform-bucket"
  acl = "private"
  versioning {
    enabled = true
  }

  tags {
    Name = "my-test-s3-terraform-bucket"
  }
}
```

---

## Terrascript (+Terraform-v12)

```python
from terrascript import Terrascript, provider
from terrascript.aws.r import aws_instance

ts = Terrascript()

# Add a provider (+= syntax)
ts += provider('aws', access_key='ACCESS_KEY_HERE',
               secret_key='SECRET_KEY_HERE', region='us-east-1')

# Add an AWS EC2 instance (add() syntax).
inst = ts.add(aws_instance('example', ami='ami-2757f631', instance_type='t2.micro'))

# Print the JSON-style configuration to stdout.
print(ts.dump())
```

---

## Pulumi

```go
package main
import (
    "github.com/pulumi/pulumi-gcp/sdk/go/gcp/storage"
    "github.com/pulumi/pulumi/sdk/go/pulumi"
)

func main() {
    pulumi.Run(func(ctx *pulumi.Context) error {
        // NewBucket already adds a random suffix
        bucket, err := storage.NewBucket(ctx, "pulumi-bucket", &storage.BucketArgs{
            Location: "EU",
            })
        if err != nil {
            return err
        }
        ctx.Export("bucketName", bucket.ID())
        ctx.Export("websiteUrl", bucket.Url())
        return nil
    })
}
```

---

**Multicloud IaC options summary**

| Tool        | Declarative? | Deployment/Update tool? | Config-driven | Programmatic |     Language    |
|-------------|:------------:|:-----------------------:|:-------------:|:------------:|:---------------:|
| Terraform   |       Y      |            Y            |       Y       |      N       | HCL, JSON(v12)  |
| Terrascript |       Y      |            N            |       N       |      Y       |      Python     |
| Pulumi      |       Y      |            Y            |       N       |      Y       |        *        |

{{% /section %}}
