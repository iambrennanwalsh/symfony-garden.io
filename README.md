# Symfony 6 Garden.io Example

This is a Symfony app configured to run via garden.io.

## Setup

_Note: The commands below assume you're running a local Kubernetes cluster. Please adjust the commands accordingly if you're running against a remote environment (setting the `--env` parameter and the correct `--namespace` for kubectl)._

Start by creating a namespace for the project and secrets to live in:

```sh
kubectl create namespace symfony-garden
```

Then create the secret for `postgres-password` in your Kubernetes cluster:

```sh
kubectl --namespace=symfony-garden create secret generic postgres-password --from-literal=root=superdupersecret
```

Now you can deploy the services:

```sh
garden deploy --dev
```

Finnaly be sure to add `127.0.0.1 symfony.local` to your `/etc/hosts` file.
