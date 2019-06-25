# jq

```
# Schema

echo 'def schema: path(..) | map(tostring) | join("/");' >> ~/.jq
jq schema

with_entries(.value |= .ip)  # get key and .ip attr for all objects in hash

```

    jq -C . blah.json | less -R     # pipe to less with colour
