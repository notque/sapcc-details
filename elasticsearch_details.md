We use rolling indexes with a date time, that are created.

index.max_result_window defaults to 10k, but won't allow pagination past 10k. Have to increase size, but also eats more heap memory.

to change
```
PUT _all/_settings?preserve_existing=true'
{
  "index.max_result_window" : "10000000"
}
```

But also they need to be set on creation, which is controlled by 

https://github.com/sapcc/helm-charts/blob/master/openstack/hermes/templates/etc/_audit.json.tpl

index.max_inner_result_window may affect aggreations in a window in the same way. have to consider.


some quick ElasticSearch debugging tips

consider ES_URI to be something like http://127.0.0.1:9200

    check cluster health

curl -skL -XGET "${ES_URI}/_cluster/health?pretty"

    check state of all shards across all nodes

curl -skL -XGET "${ES_URI}/_cat/shards"

    retry all failed routing of unassigned shards

curl -skL -XPOST "${ES_URI}/_cluster/reroute?pretty&retry_failed"

    move shards to some place else

curl -XPOST '${ES_URI}/_cluster/reroute?pretty' -H 'Content-Type: application/json' -d'
{
    "commands" : [
        {
            "move" : {
                "index" : "shard.name.xyz",
                "shard" : 0,
                "from_node" : "node1",
                "to_node" : "node2"
            }
        },
        {
          "allocate_replica" : {
                "index" : "shard.name.xyz",
                "shard" : 0,
                "node" : "node3"
          }
        }
    ]
}
'

deleting indices

    delete all

curl -X DELETE '${ES_URI}/_all'

    delete an index

curl -X DELETE '${ES_URI}/shop'
