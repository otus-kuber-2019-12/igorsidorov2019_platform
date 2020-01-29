# igorsidorov2019_platform
igorsidorov2019 Platform repository

## kubernetes-intro

* create the Dockerfile and exec local tests;
* build and push docker image to the Docker Hub;
* create Kubernetes manifest for my web app;
* pull HipsterShop repo and create docker image from current Dockerfile (frontend);
* push new image to Docker Hub;
* create Kubernetes manifest for frontend app and exec it;
* fix error of frontend app (add envs).

## kubernetes-controllers

* study info about replicaset and create some replicasets;
* study info about deployments and create some deployments;
* study info about deamonsets and create some deamonsets.

## kubernetes-security

* study info about accounting, roles, roles binding.

## kubernetes-networks

* learn info about network infrastructure

## kubernetes-volume

* learn info about volumes

## kubernetes-template

* learn info about templating

## kubernetes-operator

[13:39:06] jumper@nobody ~/src/igorsidorov2019_platform/kubernetes-operators $ kubectl get jobs
NAME                         COMPLETIONS   DURATION   AGE
backup-mysql-instance-job    1/1           2s         19m
restore-mysql-instance-job   0/1           47m        47m

[13:38:35] jumper@nobody ~/src/igorsidorov2019_platform/kubernetes-operators $ kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "INSERT INTO test ( id, name ) VALUES ( null, 'some data-1' );" otus-database
mysql: [Warning] Using a password on the command line interface can be insecure.

[13:38:56] jumper@nobody ~/src/igorsidorov2019_platform/kubernetes-operators $ kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "select * from test;" otus-database
mysql: [Warning] Using a password on the command line interface can be insecure.
+----+-------------+
| id | name        |
+----+-------------+
|  1 | some data-2 |
|  2 | some data-1 |
+----+-------------+

