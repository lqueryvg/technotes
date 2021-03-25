# Kubernetes

## Concepts

### Components (`CEASKP`)

| What                    | Where  | Notes                                                             |
| ------------------      | ------ | ----------------------------------------------------------------- |
| kube-controller-manager | master | watches for changes and manages state                             |
| etcd                    | master | key / value config store                                          |
| kube-apiserver          | master | all components communicate through here, including user (kubectl) |
| kube-scheduler          | master | decides where (which nodes) to run pods                           |
| kubelet                 | node   | (service!) register node, create pods, monitor node & pods        |
| kube-proxy              | node   | controls iptables                                                 |

#### etcd

- key / value store
- can run as a service or a POD (kubeadm)
- listens on `--adertise-client-urls` URL (port 2379)
- points to other etcd's in cluster via `--intial-cluster` comma separated list of URLs

#### kube-apiserver

- can run as a service or a POD (kubeadm)
- can curl to this instead of using kubectl
- responsible for:
  - authenticate user
  - validate request
  - retrieve data
  - update etcd (it is the only service which updates etcd)
- used by scheduler & kubelet
- points to etcd's via `--etcd-servers`

#### kube-scheduler

- can run as a service or a POD (kubeadm)
- only decides where to run a POD, doesn't start it (`kubelet` starts it)

#### kube-proxy

- can run as a service or a POD (kubeadm)
- if deployed as a Pod (via kubeadm) it runs as a daemonset

#### kube-controller-manager

- can run as a service or a POD (kubeadm)
- configures enabled controllers via `--controllers` option

##### Replication Controller / ReplicaSet
- high availability
- load balancing & scaling
- Replication Controller is old; replaced by Replica Set
- ReplicaSet adds `selector`

### YAML Structure

Always contains:

- `apiVersion`
- `kind` (supported values depends on `apiVersion`)
- `metadata`
- `spec`

#### Generators

    kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run --replicas=4 -o yaml > nginx-deployment.yaml

### Pods

- group of tightly coupled containers
- scheduling & placement unit
- bound to a node
- one pod is implemented as an "infra" container which
- containers share namespace
  - IP address
  - localhost
  - IPC
  - etc...

### Service

- managed by kube-proxy
- provides VIP
- load balance
- uses iptables to implement VIPs
- definition
  - spec
    - selector
      - key: value # specify label to select nodes to apply service to
    - ports # array of...
      - port # abstracted port that the service listens on
      - targetPort # port on the container
        - default targetPort = port
        - can be string referring to pod port name
        - port number assigned to that name can be different in each pod
          - offers flexibility
          - eg, can change port number that pods expose in the next version
- service types:
  - ClusterIP - accessible within the cluster
  - NodePort - expose port on every node which will be routed to the service
  - loadBalancer
    - equivalent to ClusterIP for pods within the cluster
    - programs (external to k8s) load balancer with entries for k8s VMs.
    - k8s service controller automates:
      - the creation of the external load balancer
      - health checks (if needed)
      - firewall rules (if needed)
      - retrieves external IP allocated by the cloud provider and populates it
        in the service object ("LoadBalancer Ingress").
    - source IP for sessions as seen in target container are _not original IP_
      of the client (in v1.5, an optional beta feature that will preserve the
      client Source IP for GCE/GKE environments. Will be phased in for other
      cloud providers)
    - loadBalancerSourceRanges can be used to restrict subnet ranges which can access
      - only on GCE & AWS

### Networking

- default Docker host-private networking:
  - creates virtual bridge "docker0", with private CIDR address block
  - attaches a veth for each container to docker0
  - each veth appears as eth0 in the corresponding container
  - eth0 in the container is given an ip address from bridge's address range
  - so:
    - containers on same node CAN talk to each other
    - containers on different nodes CANNOT talk to each other
- k8s model:
  - all containers can communicate with all other containers without NAT
  - all nodes can communicate with all containers (and vice-versa) without NAT
  - the IP that a container sees itself as is the same IP that others see it as
  - ip address is as pod level
    - docker runs a container for each pod for the network namespace
    - app containers within that pod join the namespace with --net=container:{id}

### Misc

- init containers

## Minikube

    mkdir ~/minikube; cd $_
    curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.17.1/minikube-darwin-amd64
    chmod +x minikube
    ./minikube
    # will modify $KUBECONFIG
    cp $KUBECONFIG ./minikube.kubeconfig
    alias mk='export KUBECONFIG=~/minikube/minikube.kubeconfig'

    minikube version
    minikube status
    minikube start
    minikube addons enable heapster
    minikube addons open heapster   # takes a while then opens grafana in browser

    minikube ssh      # get prompt
    sudo -i
    alias mk='export KUBECONFIG=/Users/john/minikube/minikube.kubeconfig'
    mk

## kubectl

```
source <(kubectl completion bash)
source <(kubectl completion zsh)
kubectl version
kubectl cluster-info

kubectl config view
kubectl config get-contexts               # * besides current
kubectl config use-context buxtontest
export KUBECONFIG=/tmp/config

kubectl get nodes
kubectl get nodes --help
kubectl run kubernetes-bootcamp --image=docker.io/jocatalin/kubernetes-bootcamp:v1 --port=8080
kubectl get deployments
kubectl delete deployments {deployment}


kubectl get pods
kubectl exec {pod} -- ps -ef
kubectl exec -it {pod} nslookup blah
kubectl exec {pod} -c {container} -- ps -ef     # if multiple containers
kubectl describe pods
kubectl delete pod {pod}

kubectl logs {pod}
kubectl exec {pod} bash
apt-get install procps  # e.g. if ps command not available !


kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080


kubectl label pod $POD_NAME app=v1
kubectl describe pods $POD_NAME
kubectl get pods -l app=v1
kubectl --namespace={ns} scale deployment {dep} --replicas=10   # 10 pods
kubectl --namespace=demos get svc hostnames -o yaml

# pass connections on 8020 to 80 in pod
kubectl port-forward nginx-701339712-57hd5 8020:80

# get secret for UI token
kubectl -n kube-system describe secret \
  $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```

## Dashboard

    kubectl proxy &
    browse to http://127.0.0.1:8001/ui    # or the port provided
    # or, if kubectl is running inside a VM and you want to browse from the host
    kubectl proxy --address='10.0.2.15'  # listen on the IP of the NAT interface

## API

    kubectl proxy &
    curl http://localhost:8001/api/v1/proxy/namespaces/default/pods/$POD_NAME/
      # trailing slash is important

## ssh

To ssh into a node, go via master, but need to temporarily copy private key.

    kubectl describe  nodes | grep Addresses
    scp -p ~/.ssh/id_rsa azureuser@${master}:.ssh
    ssh azureuser@${master}
    ssh {node_ip}

## Moving pod to another node

    kubectl get pods -o wide
    kubectl label nodes k8s-agentpool1-34299887-0 node_number=0
    kubectl label nodes k8s-agentpool1-34299887-1 node_number=1
    kubectl get nodes --show-labels

## Azure Persistent Volumes

### azureDisk

Add within container:

    volumeMounts:
      - name: azure
        mountPath: "/usr/share/nginx/html"

Add within spec:

    volumes:
      - name: azure
        azureDisk:
          diskName: buxton_unmanaged_disk1.vhd
          diskURI: https://buxtonblobs.blob.core.windows.net/vhds/buxton_unmanaged_disk1.vhd

The blob needs to be of type "Page blob".

Problems:

- doesn't release the disk from the node when the pod is deleted.
- nodes created via acs-engine don't support managed disks
- can't create page blobs from CLI, without creating them as part of a VM first

### azureFile

Add within container:

```yaml
volumeMounts:
  - name: azure
    mountPath: "/usr/share/nginx/html"
```

Add within spec:

```yaml
volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: "buxtonshare"
      readOnly: false
```

Next create `azure-secret.yaml` as follows:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: azure-secret
type: Opaque
data:
  azurestorageaccountname: blah1
  azurestorageaccountkey: blah2
```

Where:

    rg=buxton_storage_rg
    acc=buxtonblobs
    az storage account show  -n $acc -g $rg
    az storage account keys list -n $acc -g $rg
    echo -n ${acc} | base64    # blah1

Where `blah1` and `blah2` are obtained by running:

    echo -n {storage-account-name} | base64
    echo -n {storage-account-access-key1} | base64

Important:

1. the `-n` is important
2. YES, the access key

```
   kubectl create -f azure-secret.yaml
   kubectl get secrets
```

### mount disks

```yaml
# Mount 2 Azure blobs into a container so that you can
# manually gain a shell and (say) copy the contents
# from one to the other.
apiVersion: "extensions/v1beta1"
kind: "Deployment"
metadata:
  name: "mount-disks"
  labels:
    name: "mount-disks"
spec:
  replicas: 1
  template:
    metadata:
      name: "mount-disks"
      labels:
        name: "mount-disks"
    spec:
      containers:
        - name: "mount-disks"
          image: "some-docker-registry/some-linux-image:some-verion"
          args: ["sleep", "1d"] # sleep 1 day
          imagePullPolicy: Always
          resources:
            limits:
              cpu: 2000m
            requests:
              cpu: 100m
          volumeMounts:
            - name: "disk3"
              mountPath: "/disk3"
            - name: "disk4"
              mountPath: "/disk4"
      volumes:
        - name: "disk3"
          azureDisk:
            diskName: disk3
            diskURI: https://some-name.blob.core.windows.net/vhds/100GB_Disk.vhd
        - name: "disk4"
          azureDisk:
            diskName: disk4
            diskURI: https://some-name.blob.core.windows.net/vhds/200GB_Disk.vhd
```

## Vagrant Notes

    mkdir ~/kv; cd $_
    export NUM_NODES=2
    export KUBERNETES_PROVIDER=vagrant
    ./kubernetes/cluster/kubectl.sh

## Networking

```
                                             Endpoint
On node:

*:31196       \  (listens on node interfaces)
                -> 172.17.0.10:8080   (eth0 & port in pod)
10.0.0.215:80 /  (cluster-ip vip implemented in iptables)

% kubectl get svc jenkins2
NAME       CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
jenkins2   10.0.0.215   <.......>     80:31196/TCP   1h
           vip implemented            ^  ^
           in iptables                |  +- on "real" node interfaces
                                      |
                                      +- on cluster-ip VIP on each node

% kubectl describe svc jenkins2
...
Type:             LoadBalancer
IP:               10.0.0.215              # CLUSTER-IP (via iptables on node)
Port:             http	80/TCP            # listens on {cluster-ip} (not the node!)
NodePort:         http	31196/TCP         # listens on all interfaces on node
Endpoints:        172.17.0.10:8080        # {eth0-in-pod}:{targetPort in svc yaml}
                  ^           ^
                  |           |
                pod eth0      + - targetPort in svc yaml
                                - containerPort in pod or dep yaml
                                - Endpoint in describe
                                - use with port-forward
```

- endpoint is inside the pod (virtual eth0 device)
- cluster-ip is a vip configured on the nodes via iptables
  - nodes should be able to connect to
    {cluster-ip}:Port # 10.0.0.215:80
    {any-interface}:NodePort # or any interface
  - pod should be able to connect to:
    Endpoint

iptables rules for the above look like this:

```
-A KUBE-SERVICES -d 10.0.0.215/32 -p tcp -m comment --comment "default/jenkins2:http cluster IP"
                                         -m tcp --dport 80 -j KUBE-SVC-6G4QFESE6JTXRJ4C
-A KUBE-NODEPORTS -p tcp -m comment --comment "default/jenkins2:http" -m tcp
                  --dport 31196 -j KUBE-MARK-MASQ
-A KUBE-NODEPORTS -p tcp -m comment --comment "default/jenkins2:http" -m tcp
                  --dport 31196 -j KUBE-SVC-6G4QFESE6JTXRJ4C
  -A KUBE-SVC-6G4QFESE6JTXRJ4C -m comment --comment "default/jenkins2:http"
                               -j KUBE-SEP-XMMVQGNA6WTMYMDP
    -A KUBE-SEP-XMMVQGNA6WTMYMDP -p tcp -m comment --comment "default/jenkins2:http" -m tcp
                                 -j DNAT --to-destination 172.17.0.10:8080
    -A KUBE-SEP-XMMVQGNA6WTMYMDP -s 172.17.0.10/32 -m comment --comment "default/jenkins2:http"
                                 -j KUBE-MARK-MASQ
```

## command and args

- `command` overrides docker `ENTRYPOINT`
- `args` overrides docker `CMD`

(Counter-intuitively, `command` does *not* override `CMD` !!!)

## Container which sleeps

```yaml
command: ["bash", "-c", "sleep 7d"]
```

## Use curl api via master without proxy

Extract client-certificate-data into client-key-data

- Open kubeconfig file and put:
  - `client-certificate-data` value into `client.crt.base64`
  - `client-key-data` value into `client.key.base64`

Then...

    base64_decode -D client.crt.base64 > client.crt
    base64_decode -D client.key.base64 > client.key
    openssl pkcs12 -export -in client.crt -inkey client.key -out cert.p12

    # enter password

    curl -k -E cert.p12:password https://{master}/metrics

## Getting Stats

    curl http://127.0.0.1:8001/apis/extensions/v1beta1/namespaces/default/deployments

## Hard way

- setup TLS certs
- install haproxy / loadbalancer (with certs) pointing to controller node backends
- install etcd on controller0,1,2 nodes and configure as a cluster (point to each other's IP)
- on every controller:
  - install kube-apiserver, kube-controller-manager & kube-scheduler on controller0
  - setup /var/lib/kubernetes/token.csv
  - setup /var/lib/kubernetes/authorization-policy.jsonl
  - point kube-apiserver.service startup config at etcd controller nodes
  - set "advertise-address" in service file to this controller's IP
  - Start the kube-apiserver service
  - Create the kube-controller-manager service file
  - Replace INTERNAL_IP in the master section with the IP of this host.
  - Start the kube-controller-manager service
  - configure kube-scheduler.service
  - systemctl start kube-scheduler
- on each worker:
  - make kubernetes folder
  - Put the certificates into the kubernetes folder
  - Get the docker binary
  - Set up the docker service. We are managing docker networking with the cni plugin (coming up),
    so switch off iptables (iptables=false) and ip masquerading (ip-masq=false).
  - enable and start docker
  - Create a folder for the cni plugin
  - Download and untar the cni plugin
  - Get kubectl kube-proxy kubelet binaries
  - Create the kubelet service file.
  - Start the kubelet service
  - Create the kube-proxy service file. - systemctl start kube-proxy
- on loadbalancer...
  - Restart haproxy
- on client...
  - install xterm
  - Get the kubectl client binary
  - Copy the certificates to /root
  - Configure cluster to use the certs and point at load balancer.
    Name the cluster "kubernetes-the-hard-way"
    - kubectl config set-cluster kubernetes-the-hard-way \
      --certificate-authority=/root/ca.pem \
      --embed-certs=true --server=https://192.168.2.8:6443
  - Use the token as our credentials
    - kubectl config set-credentials admin --token chAng3m3
  - Set the default context on this cluster to user=admin
    - kubectl config set-context default-context --cluster=kubernetes-the-hard-way --user=admin
  - Use the just-created default context
    - kubectl config use-context default-context
    - kubectl get componentstatuses
    - kubectl get nodes
  - get the details of nodes' service network setup & add route
    to send to send packets to appropriate subnet in cluster. - kubectl get nodes --output=jsonpath='{range .items[*]} {.spec.podCIDR} {.spec.externalID} {"\n"}{end}'
  - Add the worker1 cidr route to this node. - ip route add 10.200.1.0/24 via 192.168.2.6
  - Add the worker2 cidr route to this node. - ip route add 10.200.2.0/24 via 192.168.2.7
- on worker1

  - Add the worker0 cidr route to this node - ip route add 10.200.0.0/24 via 192.168.2.5
  - Add the worker2 cidr route to this node - ip route add 10.200.2.0/24 via 192.168.2.7

- on worker2
  - Add the worker0 cidr route to this node - ip route add 10.200.0.0/24 via 192.168.2.5
  - Add the worker1 cidr route to this node - ip route add 10.200.1.0/24 via 192.168.2.6
- on client
  - get kubedns yaml
  - Check the service is in the kube-system namespace
  -     kubectl --namespace=kube-system get svc
  - Create the kubedns deployment.
  - As a smoke test, run the nginx image with two pods across this cluster
  - Expose the nginx service as a port on the nodes they run on.
- on loadbalancer
  - Add the haproxy frontend and backend entries to the config.
  - Restart the haproxy.
- on worker0
  - List kubernetes rules to route requests to the service layer to the underlying pods.
    - iptables --list -t nat
- on client
  - curl nginx
    - curl http://192.168.2.8:31244
- DONE !
