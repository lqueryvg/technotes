```
az account show  --expanded-view
az group create --name "rg1" --location "ukwest"
az group show -n buxton_rg1

cd ~/acs-engine/
acs-engine examples/kubernetes.json

outd=~/acs-engine/_output/Kubernetes-34299887  # example
export KUBECONFIG=$outd/kubeconfig/kubeconfig.ukwest.json

az group create -n buxton_rg1 --location ukwest
az group deployment create \
    --name "dep1" \
    --resource-group "buxton_rg1" \
    --template-file "${outd}/azuredeploy.json" \
    --parameters "@${outd}/azuredeploy.parameters.json"
master=buxtontest.ukwest.cloudapp.azure.com

az component update   # update az CLI

# TODO replace this with ssh-agent approach

id=id_rsa   # danger!
scp -p ~/.ssh/${id} azureuser@${master}:.ssh
kc=/tmp/${master}.kubeconfig
scp ${master}:.kube/config $kc
export KUBECONFIG=$kc
ssh azureuser@${master}
kubectl describe  nodes | grep Addresses | awk '{print $2}'
ssh {node_ip}

kubectl proxy   # could be 10 mins 

# a test pod

kubectl run nginx --image nginx
ip=$(kubectl get pods -o yaml | grep podIP | awk '{print $2}')
curl $ip

kubectl expose deployment nginx --port=80
internal_ip=$(kubectl get service | grep ^nginx | awk '{print $2}')
curl $internal_ip

kubectl edit svc/nginx
change
    "type: ClusterIP"
  to
    "type: LoadBalancer"
kubectl get svc   -w     # wait a couple of mins for <pending> to change to an address below...



az vm list  -o jsonc            # colour json output
az vm list  | jq -C .[].name

az vm list | jq -C '.[] | {name,resourceGroup}' | less

rg=buxton_rg1
az vm list-ip-addresses --ids \
  $(az vm list -g $rg --query "[].id" -o tsv)

az network nsg list --output table

az keyvault list
az keyvault secret list --vault-name ${vault_name} -o tsv
az keyvault secret show --vault-name ${vault_name} --name ${key} --query value -o tsv
az keyvault secret list --vault-name ${vault_name} -o tsv --query '[].id'  # list names

az login --service-principal -u $service_principal_name -p $service_principal_password --tenant $tenant

az network public-ip list -o table --query '[].[location,name,publicIpAllocationMethod]' 
az  network  public-ip list -o jsonc --query '[].ipAddress'


az storage  container list --account-name {strg-account} |
  jq '.[] | .name,  .properties.lastModified'

rg={rg}
account={account_name}
container={container-name}
query_string=$(az storage account show-connection-string \
  --resource-group $rg --name $account --query connectionString)

az storage blob  list --container-name $container --connection-string $query_string | jq '.[].name'
az storage blob copy start --account-name $account \
  --source-container $container \
  --destination-container $container \
  --source-blob template/10GB_Disk.vhd \
  --destination-blob  fred

az storage blob delete --connection-string $query_string \
  --container-name $container --name fred

az storage blob  list --container-name $container \
  --connection-string $query_string \
  --query "[?name=='fred'].properties.contentLength"

#  --source-uri https://{blah}.blob.core.windows.net/vhds/template/10GB_Disk.vhd  \

# view various limits and how much being used (e.g. cpus)
az vm list-usage -l west-europe
```

# RBAC (Roles)
```
RG - Role - {Users,Groups,Apps}
     |
     \> {Contributor,Owner,Reader,Custom}
```
## Custom Roles
- roles are assigned actions which define what the role allows
- AssignableScopes defines what objects (specific sub, rg or resource) the role is assignable to
- Owner of a scope can create roles for assignment in that scope

# VSTS

### VSTS CLI

- Setup PAT in VSTS for your personal account here...
https://docs.microsoft.com/en-gb/vsts/accounts/use-personal-access-tokens-to-authenticate?view=vsts

```
export VSTS_CLI_PAT={your-token}
vsts login --token $VSTS_CLI_PAT
vsts configure --defaults instance=https://{name}.visualstudio.com
vsts project list
vsts configure --defaults project="Your Project"
vsts build definition show -o json
vsts build definition show --id 82 -o json | jq '.variables |with_entries(.value |= .value)'
```

### Tasks

Clone another repo:

  - script: |
      echo current directory = `pwd`
      git clone -b some-branch https://$SYSTEM_ACCESSTOKEN@some-project.visualstudio.com/proj-name/_git/repo-name
    displayName: clone another repo


