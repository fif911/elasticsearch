# Understanding replication

Shard can have their replicas not to lose data
The replica shard should be stored on different from master shard nodes (cause if node with master shard fails we still will have
it's replicas)


## Creating a new index

```
PUT /pages
```

## Checking the cluster's health

```
GET /_cluster/health
```

## Listing the cluster's indices

```
GET /_cat/indices?v
```

## Listing the cluster's shards

```
GET /_cat/shards?v
```