
Because NON of the other build work properly...
Here is a clean build of mesos.
master and slave are the same program,
so it seems somewhat redundant to have more than one package.

the build includes docker package for the sake of slave commands.
Though the host docker sock must be linked in otherwise it won't work.

## Master
```
docker run -d --entrypoint=mesos-master mrbar42/mesos:0.27.2
```

## Slave
base:
```
docker run -d --entrypoint=mesos-slave mrbar42/mesos:0.27.2
```

## Real world examples:
### Master
```
docker run -d \
    --entrypoint=mesos-master \
    --net="host" \
    - "MESOS_ZK=zk://master1:2181,master2:2181,master3:2181/mesos"
    - "MESOS_PORT=5050"
    - "MESOS_QUORUM=2"
    - "MESOS_WORK_DIR=/var/lib/mesos"
    mrbar42/mesos:0.27.1
```

### Slave
```
docker run -d \
    --entrypoint=mesos-slave \
    --net="host" \
    -e "MESOS_MASTER=zk://master1:2181,master2:2181,master3:2181/mesos" \
    -e "MESOS_CONTAINERIZERS=docker,mesos" \
    -e "MESOS_EXECUTOR_REGISTRATION_TIMEOUT=5mins" \
    -e "MESOS_ISOLATON=cgroups/cpu,cgroups/mem" \
    -e "MESOS_WORK_DIR=/tmp/mesos" \
    -e "MESOS_RESOURCES=mem:2000;ports:[31000-33000]" \
    -e "MESOS_ATTRIBUTES=region:america;location:us-west;host:aws" \
    -v /tmp/mesos-slave:/tmp/mesos \
    -v /proc:/host/proc:ro \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v /cgroup:/cgroup \
    -v /sys:/sys \
    mrbar42/mesos:0.27.1
```