# Prerequires

Azure account
Terraform
Postman
Selenium
JMeter

Add [Azure Pipelines Terraform Tasks
](https://marketplace.visualstudio.com/items?itemName=charleszipp.azure-pipelines-tasks-terraform) to your Azure DevOps organisation.

# Optional

SSH private key is created and stored at: `~/.ssh/id_rsa`.

# Configure the Service Principal

Create the Service Principal which will have permissions to manage resources. Check the [Terraform documentation](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/guides/service_principal_client_secret).

```
$ az ad sp create-for-rbac --role="Contributor"
```

This will output five values:

```
{
  "appId": "00000000-0000-0000-0000-000000000000",
  "displayName": "azure-cli-2017-06-05-10-41-15",
  "name": "http://azure-cli-2017-06-05-10-41-15",
  "password": "0000-0000-0000-0000-000000000000",
  "tenant": "00000000-0000-0000-0000-000000000000"
}
```

Replace the Terraform variables in `terraform.tfvars` like so:

`appId` is the `client_id` defined above.
`password` is the `client_secret` defined above.
`tenant` is the `tenant_id` defined above.
`subscription_id` can be obtained with the command:

```
$ az account list
```

# Terraform

Configure a storage account and a state backend. Refer to the [Microsoft documentation](https://docs.microsoft.com/en-us/azure/developer/terraform/store-state-in-azure-storage) for more details.
Change the value of the variables in `az_conf_remote_storage.sh` if you would like

```
$ az login
$ ./az_conf_remote_storage.sh
$ AZ_ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query '[0].value' -o tsv)
$ export ARM_ACCESS_KEY=$AZ_ACCOUNT_KEY
$ terraform init
$ terraform apply
```

# Azure DevOps

Import `azure-pipelines.yaml` and `StarterAPIs.json`
From the `azure-pipelines.yaml`file, create a pipeline. Refer to [Microsoft documentation](https://docs.microsoft.com/en-us/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=java%2Cyaml%2Cbrowser%2Ctfs-2018-2).

If you run the pipeline for the first time, you should have a message prompt asking access to the 'TEST' environment, if you answer 'yes', it will create it for you. Otherwise follow [Microsoft documentation](https://docs.microsoft.com/en-us/azure/devops/pipelines/ecosystems/deploy-linux-vm?view=azure-devops&tabs=java) about environments.

On the 'Environment' tab of Azure DevOps, click on the 'TEST' environment and add a resource: add a Linux virtual machine. The window will ask you to execute a command line inside the virtual machine already created by Terraform in order to link it with the pipeline. In order to do it, you need to SSH the said virtual machine either with your SSH keys or by password as configured by Terraform.

