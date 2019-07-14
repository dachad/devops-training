+++
+++

{{% section %}}

{{< slide template="subtitle" >}}

## Segunda Parte: Testing en local

---

## Unit Testing

<!--
https://damyanon.net/flask-series-testing/
-->
Primero actualizamos el sistema e instalemos el gestor de entornos de Python:
```bash
sudo apt-get update
sudo apt-get install python-virtualenv
```

Analicemos y ejecutemos el fichero `scripts/test_unit.sh`:
```bash
#!/bin/bash
set -ex

virtualenv .venv
. .venv/bin/activate
pip install -r app/requirements.txt
coverage run app/test_app.py
coverage report app/app.py
```

En caso de errores como:
```bash
-bash: ./scripts/test_unit.sh: /bin/bash^M: bad interpreter: No such file or directory
```

Convertir saltos de líneas a formato Unix:
```bash
sudo apt-get install dos2unix; dos2unix scripts/*
```

---

## Functional Testing

Analicemos y ejecutemos el fichero `scripts/test_docker.sh`

```bash
#!/bin/bash

set -ex

docker build -t devops:test app/.

cid=$(docker run -d -p 6789:5000 devops:test)

until test=$(curl localhost:6789 | grep 'Index!') &> /dev/null
do
    sleep 0.1
done

# Clean Docker
docker rm -fv "$cid" &> /dev/null

if [[ "$test" == "Index!" ]]; then
    echo "Test Passes! Smile :)"
else
    echo "Test Fails, fix it!"
    exit 1
fi
```

---

## Do it yourself!

* Modifiquemos el código y veamos como afecta a nuestro testing
* Ampliamos el test coverage al **100%**

<img src="../images/keep-calm-and-do-it-yourself-13.png" style="width:30%;">


{{% /section %}}
