## How to add-cluster-role-to-user 'robot' 
To access some privileged api such as ```GET https://127.0.0.1:8443/api/v1/namespaces/``` our 'robot'
needs a **cluster-role**.
In order to grant this role: 
1.  Connect to the 'origin' container to issue  ```oc adm``` command

### Connect to the 'origin' container to issue  ```oc adm``` command
#### Find the container
- docker ps | grep origin
- docker inspect <CONTAINER-ID> | grep master-config

``` bash
openshift-kafka (develop)*$ docker ps | grep origin
ca6df3374b28        openshift/origin-pod:v3.6.0        "/usr/bin/pod"           3 hours ago         Up 3 hours                                                                             k8s_POD_kafka-debug-1-h1d3x_kafka-lab_5796be24-7dc8-11e7-8609-6a9bc5b63bf3_0
72f001cbb3e6        openshift/origin-pod:v3.6.0        "/usr/bin/pod"           3 hours ago         Up 3 hours                                                                             k8s_POD_kafka-debug-1-60vfq_kafka-lab_4127b1cc-7dc7-11e7-8609-6a9bc5b63bf3_0
3b893cd4d662        openshift/origin-pod:v3.6.0        "/usr/bin/pod"           3 hours ago         Up 3 hours                                                                             k8s_POD_apache-kafka-1-ln40b_kafka-lab_f4fe784d-7dc6-11e7-8609-6a9bc5b63bf3_0
213f5323c834        openshift/origin-docker-registry   "/bin/sh -c '/usr/..."   2 days ago          Up 2 days                                                                              k8s_registry_docker-registry-1-2mxr1_default_cfa6c9eb-7be7-11e7-8609-6a9bc5b63bf3_0
f6c5f3bd4b98        openshift/origin-pod:v3.6.0        "/usr/bin/pod"           2 days ago          Up 2 days                                                                              k8s_POD_docker-registry-1-2mxr1_default_cfa6c9eb-7be7-11e7-8609-6a9bc5b63bf3_0
0851cc608d64        openshift/origin-haproxy-router    "/usr/bin/openshif..."   2 days ago          Up 2 days                                                                              k8s_router_router-1-xgjfp_default_cbaa4140-7be7-11e7-8609-6a9bc5b63bf3_0
21501f5d62b2        openshift/origin-pod:v3.6.0        "/usr/bin/pod"           2 days ago          Up 2 days           0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp, 0.0.0.0:1936->1936/tcp   k8s_POD_router-1-xgjfp_default_cbaa4140-7be7-11e7-8609-6a9bc5b63bf3_0
33045b678a87        openshift/origin:v3.6.0            "/usr/bin/openshif..."   2 days ago          Up 2 days                                                                              origin
```
**<CONTAINER-ID>** may be: **33045b678a87** , get the **master-config** 

```
openshift-kafka (develop)*$ docker inspect 33045b678a87 | grep master-config
            "--master-config=/var/lib/origin/openshift.local.config/master/master-config.yaml",
                "--master-config=/var/lib/origin/openshift.local.config/master/master-config.yaml",
openshift-kafka (develop)*$ docker inspect 33045b678a87 | grep node-config
            "--node-config=/var/lib/origin/openshift.local.config/node-localhost/node-config.yaml"
                "--node-config=/var/lib/origin/openshift.local.config/node-localhost/node-config.yaml"
```


we can check the ```/var/lib/origin/openshift.local.config/master/master-config.yaml``` in the docker terminal: 
``` 
cd ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux
$ screen ./tty
/ # uname -a
Linux moby 4.9.36-moby #1 SMP Wed Jul 12 15:29:07 UTC 2017 x86_64 Linux
/ # ls -lrt /var/lib/origin/openshift.local.config/master/
total 432
-rw-------    1 root     root           451 Aug  7 07:50 serviceaccounts.public.key
-rw-------    1 root     root          1675 Aug  7 07:50 serviceaccounts.private.key
-rw-------    1 root     root          1679 Aug  7 07:50 service-signer.key
-rw-r--r--    1 root     root          1115 Aug  7 07:50 service-signer.crt
-rw-------    1 root     root          1675 Aug  7 07:50 openshift-master.key
-rw-r--r--    1 root     root          1123 Aug  7 07:50 openshift-master.crt
-rw-------    1 root     root          1675 Aug  7 07:50 master.server.key
-rw-r--r--    1 root     root          2534 Aug  7 07:50 master.server.crt
-rw-------    1 root     root          1679 Aug  7 07:50 master.proxy-client.key
-rw-r--r--    1 root     root          1086 Aug  7 07:50 master.proxy-client.crt
-rw-------    1 root     root          1679 Aug  7 07:50 master.kubelet-client.key
-rw-r--r--    1 root     root          1135 Aug  7 07:50 master.kubelet-client.crt
-rw-------    1 root     root          1675 Aug  7 07:50 master.etcd-client.key
-rw-r--r--    1 root     root          1078 Aug  7 07:50 master.etcd-client.crt
-rw-------    1 root     root          1675 Aug  7 07:50 etcd.server.key
-rw-r--r--    1 root     root          2534 Aug  7 07:50 etcd.server.crt
-rw-------    1 root     root          1675 Aug  7 07:50 ca.key
-rw-r--r--    1 root     root          1070 Aug  7 07:50 ca.crt
-rw-------    1 root     root          1679 Aug  7 07:50 admin.key
-rw-r--r--    1 root     root          1119 Aug  7 07:50 admin.crt
-rw-r--r--    1 root     root             2 Aug  7 07:50 ca.serial.txt
-rw-r--r--    1 root     root          1070 Aug  8 03:14 ca-bundle.crt
-rw-r--r--    1 root     root        316612 Aug  8 03:14 policy.json
-rw-------    1 root     root          5674 Aug  8 03:14 openshift-master.kubeconfig
-rw-r--r--    1 501      dialout       7142 Aug  8 03:14 master-config.yaml
-rw-------    1 root     root          5626 Aug  8 03:14 admin.kubeconfig
-rw-r--r--    1 501      dialout       4966 Aug  8 03:15 router.pem
```

we have to connect to the origin container to issue the ```oadm policy add-cluster-role-to-user``` command for user ```system:serviceaccount:kafka-lab:robot```

[How to get into a docker container?](https://stackoverflow.com/questions/30172605/how-to-get-into-a-docker-container)
```
$ docker exec -it 33045b678a87 bash
[root@moby origin]# oc version
oc v3.6.0+c4dd4cf
kubernetes v1.6.1+5115d708d7
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://127.0.0.1:8443
openshift v3.6.0+c4dd4cf
kubernetes v1.6.1+5115d708d7
[root@moby origin]# oadm version
oadm v3.6.0+c4dd4cf
kubernetes v1.6.1+5115d708d7

Server https://127.0.0.1:8443
openshift v3.6.0+c4dd4cf
kubernetes v1.6.1+5115d708d7
```
please note: ```system:serviceaccount:kafka-lab:robot```
```
[root@moby origin]# oadm policy add-cluster-role-to-user cluster-admin system:serviceaccount:kafka-lab:robot --config=/var/lib/origin/openshift.local.config/master/admin.kubeconfig
cluster role "cluster-admin" added: "system:serviceaccount:kafka-lab:robot"
[root@moby origin]# ls -lrt /var/lib/origin/openshift.local.config/master/admin.kubeconfig
```
To have ctrl+c stop the container you must use -it
To detach from the container you should use ```ctrl+pq```


now I can GET everithing like:
-- https://127.0.0.1:8443/api/v1/namespaces/
-- https://127.0.0.1:8443/api/v1/namespaces/kafka-lab/services


the above solution was inspired by: [Admin user does not have system:admin privileges #32](https://github.com/projectatomic/adb-utils/issues/32)
```
sudo oadm policy add-cluster-role-to-user cluster-admin admin --config=/var/lib/origin/openshift.local.config/master/admin.kubeconfig
oc adm policy add-cluster-role-to-user cluster-admin admin --config=/var/lib/origin/openshift.local.config/master/admin.kubeconfig
```


### Appendix

```
openshift-kafka (develop)*$ oc get namespaces
NAME              STATUS    AGE
default           Active    2d
kafka-lab         Active    2d
kube-public       Active    2d
kube-system       Active    2d
myproject         Active    2d
openshift         Active    2d
openshift-infra   Active    2d
openshift-kafka (develop)*$ oc whoami
admin
openshift-kafka (develop)*$ oc get projects
NAME              DISPLAY NAME   STATUS
default                          Active
kafka-lab         Kafka Lab      Active
kube-public                      Active
kube-system                      Active
myproject         My Project     Active
openshift                        Active
openshift-infra                  Active
```


<!-- 
#### TODO: descrivere: 
[Installing and Starting an All-in-One Server](https://docs.openshift.org/latest/getting_started/administrators.html)
$ sudo docker run -d --name "origin" \
        --privileged --pid=host --net=host \
        -v /:/rootfs:ro -v /var/run:/var/run:rw -v /sys:/sys -v /sys/fs/cgroup:/sys/fs/cgroup:rw \
        -v /var/lib/docker:/var/lib/docker:rw \
        -v /var/lib/origin/openshift.local.volumes:/var/lib/origin/openshift.local.volumes:rslave \ 
        openshift/origin start
-->