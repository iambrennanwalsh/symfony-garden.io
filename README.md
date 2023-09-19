# Symfony 6 & garden.io.

This demonstrates a Symfony app running on garden.io.

If you dont have a local kubernetes cluster installed, [download and install Docker Desktop](https://www.docker.com/) first.

## Setup

Start by creating a namespace for your project and secrets to live in.

```sh
kubectl create namespace symfony-garden
```

Next define a secret called `postgres-password` which will contain your database password.

```sh
kubectl --namespace=symfony-garden create secret generic postgres-password --from-literal=root=superdupersecret
```

Now run the deploy command.

```sh
garden deploy --dev
```

Finnaly be sure to add `127.0.0.1 symfony.local` to your `/etc/hosts` file.
