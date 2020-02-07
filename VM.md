# Create VM

Create resource group
 
 ```shell script
az group create --name myResourceGroup --location eastus
```

Create VM

```
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword
```

# Enable encryption

Create Key Vault

```shell script
az keyvault create 
--name "myKV" 
--resource-group "myResourceGroup" 
--location eastus 
--enabled-for-disk-encryption
```

Create Key Vault Key

```shell script
az keyvault key create
--vault-name "myKv"
--name myKey
--protection software
```

Create VM

```shell script
az group create --name "myResourceGroup" --location "eastus"

az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

Encrypt VM

```shell script
az vm encryption enable 
--resource-group MyResourceGroup 
--name MyVM 
--disk-encryption-keyvault myKV
--key-encryption-key myKey
--volume-type all
```

