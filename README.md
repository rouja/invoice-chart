# This charts installs the Invoice Ninja system on kubernetes

## Prerequisites

- a working HTTP/HTTPS ingress controller.
- cert-manager installed and configured (including a working cert issuer).
- a working storage class

## Installation

```console
git clone git@github.com:rouja/invoice-chart.git
cp invoice-chart/values.yaml cutomized-values.yaml
```

Adapt the content of  **cutomized-values.yaml** to your environment.

To generate the APP_KEY, you can do:

```console
cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 32 | head -n 1
```

Deploy the chart :

```console
kubectl create ns invoice-ninja
helm upgrade -n invoice-ninja -f cutomized-values.yaml --install invoice invoice-chart/
```

Wait a minute and check that mysql is ready :

```console
kubectl -n invoice-ninja get ingress,po
NAME                                             HOSTS                     ADDRESS   PORTS     AGE
ingress.extensions/invoice-invoice-ninja-nginx   invoice.example.com             80, 443   17s

NAME                                                 READY   STATUS    RESTARTS   AGE
pod/invoice-invoice-ninja-invoice-6b8dcf5654-75rkz   1/1     Running   0          16s
pod/invoice-invoice-ninja-mysql-76fc8c97f4-9cvn2     1/1     Running   0          16s
pod/invoice-invoice-ninja-nginx-8558b65c8d-q678z     1/1     Running   0          16s

kubectl -n invoice-ninja logs invoice-invoice-ninja-mysql-76fc8c97f4-9cvn2
...
2020-02-24T14:35:49.979820Z 0 [Note] Event Scheduler: Loaded 0 events
2020-02-24T14:35:49.980368Z 0 [Note] mysqld: ready for connections.
Version: '5.7.29'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
```

If you see **mysqld: ready for connections.**, everything should be ok. You can access your ingress with a browser and finish the installation.
