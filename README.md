# azurerm-terraform-blob-storage
Terraform module to set up a storage account on Azure for Azure Blob storage and Azure Files

---- Under construction -----
## Usage
```hcl-terraform
module "blob-storage"{
  source   = "sjones-sot/remote-state-storage/azurerm"
  name     = "myremotestorage"
  location = "westeurope"
}

```
Due to the nature of backend config, you will need to make note of the outputs of the module.
These can later be used as the following example illustrates:
```hcl-terraform
terraform{
  backend "azurerm" {
    storage_account_name = "demoremotestates"
    container_name       = "demo-remote-state-container"
    key                  = "mydemo/terraform.tfstate"
    resource_group_name  = "demo-remote-state-rg"
  }
}
```



## Limitations and Notes
Management locks - I'd recommend setting a delete lock on this storage account.  If you run your terraform client as an `owner` rather than `contributor` you can add something like the following to do this automatically:
```hcl-terraform
resource "azurerm_management_lock" "state_storage_delete_lock"{
  name       = "statefiles-nodelete-lock"
  scope      = "${module.remote_state_storage.storage_account_id}"
  lock_level = "CanNotDelete"
  notes      = "Some meaningful notes here"
}
```

NB: I do _not_ recommend running your terraform app as `owner`.

