# Kubernetes Cassandra
 Deploy Apache Cassandra in Kubernetes

## Install

We utilize a ["headless" service](https://kubernetes.io/docs/concepts/services-networking/service/#headless-services) for Cassandra. This way we can provide a way for applications to access it via [KubeDNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/) and not expose it to the outside. To access it from your developer workstation you can use `kubectl exec` commands against any of the cassandra pods.  
If you do wish to connect an application to it you can use the KubeDNS value of `cassandra.default.svc.cluster.local` when configuring your application.

```shell
kubectl create -f service.yaml
kubectl create -f volumes.yaml
kubectl create -f statefulset.yaml
```

## Scaling

To increase or decrease the size of your StatefulSet you can use the scale command:

```shell
$ kubectl scale --replicas=3 statefulset/cassandra
```

## Check Cluster

You can perform a `nodetool` status to check if the other cassandra nodes have joined and formed a Cassandra cluster.

```shell
$ kubectl exec -ti cassandra-0 -- nodetool status

Datacenter: DC1
===============
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address     Load        Tokens       Owns (effective)   Host ID                                 Rack
UN  10.1.4.99   80.05 KiB   256          69.3%              528a11d5-74a4-4768-a1aa-70de5998449b    Rack1
UN  10.1.4.98   74.94 KiB   256          61.9%              510c2b45-79ba-4717-8b7b-c7bc782fe6e7    Rack1
UN  10.1.4.101  236.12 KiB  256          68.9%              677859ee-ca59-42e1-9d6d-2632dfd3c13a    Rack1
```

## Check Cassandra

You can access the cassandra container using the following command:

```shell
$ kubectl exec -it cassandra-0 cqlsh

Connected to Cassandra at 127.0.0.1:9042.
[cqlsh 5.0.1 | Cassandra 3.11.6 | CQL spec 3.4.4 | Native protocol v4]
Use HELP for help.
cqlsh>
```

Check tables :

```shell
cqlsh> describe tables


Keyspace system_traces
----------------------
events  sessions

Keyspace system_schema
----------------------
tables     triggers    views    keyspaces  dropped_columns
functions  aggregates  indexes  types      columns

Keyspace system_auth
--------------------
resource_role_permissons_index  role_permissions  role_members  roles

Keyspace system
---------------
available_ranges          peers               batchlog        transferred_ranges
batches                   compaction_history  size_estimates  hints
prepared_statements       sstable_activity    built_views
"IndexInfo"               peer_events         range_xfers
views_builds_in_progress  paxos               local

Keyspace system_distributed
---------------------------
repair_history  view_build_status  parent_repair_history
```
