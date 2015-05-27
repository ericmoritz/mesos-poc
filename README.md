# mesos-poc
POC for a mesos/docker cluster

# Usage

Start the zookeeper cluster:

```
vagrant up zk-1 zk-2 zk-3
```

Visit http://zk-1.vagrant:8080/exhibitor/v1/ui/index.html and wait until the essemble stabilize

Start the mesos cluster:

```
vagrant up mesos-master-1 mesos-slave-1
```
