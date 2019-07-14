+++
+++

{{% section %}}

{{< slide template="subtitle" >}}

## Primera parte: Trabajando con nuestro propio repositorio

---

## Nuevo repositorio

* Seguimos utilizando la misma máquina virtual del **Lab1 (Vagrant)**
* Creamos un repositorio nuevo para crear una pipeline propia que utilizaremos en la segunda parte.
    * Primero, en la **interfaz web de Bitbucket** crearemos un repositorio y escogeremos la opción *I'm starting from scratch* que nos mostrará los siguientes comandos (no ejecutar aún):

```bash
git clone git@bitbucket.org:<bitbucket_user>/<repo_name>.git
cd <repo_name>
echo "# My project's README" >> README.md
git add README.md
git commit -m "Initial commit"
git push -u origin master
```

---

## Creando e instalando un par de claves

* Para proseguir, necesitaremos de nuestras **credenciales en Bitbucket**. Para eso, debemos **crear una llave SSH en nuestro perfil de bitbucket** (o reusar una).
    * Click on user avatar -> View profile -> Security -> SSH keys
* Esta llave la copiaremos dentro de Vagrant, bien directamente en la copia compartida de los materiales del curso (visible en la VM bajo `/vagrant`), o usando un cliente scp:

```
scp -P 2222 -i ~/.vagrant.d/insecure_private_key <nuestra_llave_ssh> vagrant@127.0.0.1:/vagrant/
```

Y ya dentro de Vagrant otra vez, la **activaremos**:
```
eval `ssh-agent -s`
ssh-add <nuestra_llave_ssh>
```

---

## Inicializando el repositorio dentro de la máquina virtual

Una vez activa la llave ssh, ya podremos **realizar los pasos propuestos por bitbucket para clonar (descargar) nuestro nuevo repositorio**, fuera del directorio `/vagrant` (por ejemplo, en el home directory del usuario en la VM)

Finalmente, y para inicializar el repositorio con los ficheros de la práctica 2, copiaremos éstos desde lab2/* al nuevo repositorio:

```bash
cp -r /vagrant/lab2/* .
```

{{% /section %}}
