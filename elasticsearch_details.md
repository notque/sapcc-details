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
