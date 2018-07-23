# kops

## Quick start test cluster

- no Route53 DNS required
- 1 master
- 1 node

### IAM

Follow instructions here to create IAM user/group for kops and
store id and key in ~/.aws/credentials:
https://github.com/kubernetes/kops/blob/master/docs/aws.md#setup-iam-user

### State Storage

Follow:
https://github.com/kubernetes/kops/blob/master/docs/aws.md#cluster-state-storage

### Set environment

```
export KOPS_STATE_STORE=s3://{name}-kops-state-store
export KOPS_CLUSTER_NAME=myfirstcluster.k8s.local
```

### Build Config

Build the config in the state store using default minimal config:
```
kops create cluster --zones {region}    # example region = us-west-2a
```

###  Reduce Size

```
kops edit ig nodes
# change 2 to 1
# change t2.medium to t2.small
kops edit ig master-{region}
```

###  Create
```
kops update cluster        # preview
kops update cluster --yes  # do it !
```

## Misc

```
kops edit cluster
kops edit ig nodes --name $NAME

# Once created...
kops validate cluster
```
