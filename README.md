# azkaban-kubernetes-osx
Run Azkaban Scheduler on Kubernetes for OSX

More about Azkaban Scheduler: https://azkaban.github.io/ (frequently used together with Hadoop)

# 1. Prerequisites: 
1. Install Docker for Mac (new version that has Kubernetes) - https://www.docker.com/docker-mac
2. Enable Kubernetes in Docker UI

![Kubernetes Preferences in Docker for MAC](dockermackubernetespreferences.png)

3. Set kubernetes context (in Terminal)
```
kubectl config use-context docker-for-desktop  
```

# 2. Install Azkaban scheduler (from Terminal)
```
$ docker stack deploy azkaban -c azkaban/docker-compose.yml
```

## 2.1. Should expect something like this (might take a few minutes < 5)
```
Stack azkaban was created
Waiting for the stack to be stable and running...
 - Service azkweb has one container running
 - Service mysql has one container running
 - Service azkexec has one container running
Stack azkaban is stable and running
```

# 3. Open browser on http://localhost:8081
Use username and password: azkaban

## 3.1 Should get something like this in the browser

![Azkaban Scheduler on Mac in Browser](azkabanscheduler.png)

# 4. See Kubernetes status related to Azkaban (your output might be different)

```
$ kubectl get stacks
NAME      AGE
azkaban   1m
```

```
$ kubectl get services
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)           AGE
azkexec      LoadBalancer   10.104.255.224   <pending>     12321:31580/TCP   1m
azkweb       LoadBalancer   10.103.164.110   <pending>     8081:32019/TCP    1m
kubernetes   ClusterIP      10.96.0.1        <none>        443/TCP           1d
mysql        LoadBalancer   10.97.203.183    <pending>     3306:30034/TCP    1m
```

```
$ kubectl get pods
NAME                       READY     STATUS    RESTARTS   AGE
azkexec-54b45859c4-k8cwd   1/1       Running   0          1m
azkweb-6fdb78f977-l467h    1/1       Running   0          1m
mysql-67445f897f-w4zb8     1/1       Running   0          1m
```

# 5. Login to a kubernetes pod - mysql
(see mysql name from kubectl get pods above, and use 'password' as mysql password, ref docker-compose.yml file)
```
$ kubectl exec -it mysql-67445f897f-w4zb8 -- /bin/bash
root@mysql-67445f897f-w4zb8:/#
root@mysql-67445f897f-w4zb8:/# mysql -p
Enter password:  
elcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 23
Server version: 5.7.20 MySQL Community Server (GPL)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases
+--------------------+
| Database           |
+--------------------+
| information_schema |
| azkaban            |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.01 sec)

mysql> use azkaban;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
```

# 6. Complementary Kubernetes Tools - Kubeapps / Helm - for installing apps

## 6.1 Install kubeapps
```
$ sudo curl -L https://github.com/kubeapps/installer/releases/download/v0.2.0/kubeapps-darwin-amd64 -o /usr/local/bin/kubeapps && sudo chmod +x /usr/local/bin/kubeapps
```

## 6.2 Start kubeapps 
```
$ kubeapps up
kubeapps up
INFO[0000] Updating namespaces kubeapps                 
INFO[0000]  Creating non-existent namespaces kubeapps   
INFO[0000] Updating namespaces kubeless                 
INFO[0000]  Creating non-existent namespaces kubeless   
INFO[0000] Updating configmaps kubeapps.tcp-services    
... # LOT OF OUTPUT OMITTED
INFO[0008]  Creating non-existent secrets kubeapps.mongodb 
INFO[0008] Updating statefulsets kubeless.zoo           
INFO[0008]  Creating non-existent statefulsets kubeless.zoo 
INFO[0008] Updating statefulsets kubeless.kafka         
INFO[0008]  Creating non-existent statefulsets kubeless.kafka 
```
## 6.3 Start kubeapps dashboard
```
$ kubeapps dashboard
Forwarding from 127.0.0.1:52680 -> 80
```

## 6.4 Open kubeapps ui in browser - http://localhost:52680/kubeless/
![Kubeapps in Browser](kubeapps.png)

# Appendix - Learn more about Kubernetes (for OSX on Mac)

1. https://blog.alexellis.io/docker-for-mac-with-kubernetes/
2. https://kubernetes.io/docs/tools/kompose/user-guide/


