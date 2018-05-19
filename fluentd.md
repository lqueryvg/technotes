
```
<source>

  @type http
  port 8888
  bind 0.0.0.0
  body_size_limit 32m
  keepalive_timeout 10s
</source>
```

fluentd -c fl.conf
curl -i -X POST -d 'json={"action":"logout","user":2}' http://localhost:8888/kube.test
