## Install Checkov
```bash
sudo apt update
```
## (DON'T USE BELOW CODE)
```bash
sudo apt install python3-pip -y
pip3 install checkov
```
The above command is an error in an externally managed environment because Ubuntu (Python 3.12) now protects the system Python environment using PEP 668. It prevents pip from installing packages globally to avoid breaking OS packages. This is normal on newer Ubuntu versions, such as the one on your Azure Virtual Machines. The correct way is to install Checkov inside a Python virtual environment.

## Solution (Recommended)
### Install venv package
```bash
sudo apt install python3-venv -y
```
### Create Virtual Environment
```bash
python3 -m venv checkov-env
```
This creates a folder: checkov-env
### Activate Virtual Environment
```bash
source checkov-env/bin/activate
```
Now your terminal will look like: (checkov-env) azureuser@VM_name:~$
### Install Checkov
```bash
pip install checkov
checkov --version
```

## Create Terraform Project
### Create folder and Terraform file
```bash
mkdir azure-iac-security
cd azure-iac-security
```
```bash
nano main.tf
```

### code - 
```bash
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "iac-security-rg"
  location = "East US"
}

resource "azurerm_storage_account" "example" {
  name                     = "insecurestorage12345"
  resource_group_name      = azurerm_resource_group.example.name
  location                 = azurerm_resource_group.example.location
  account_tier             = "Standard"
  account_replication_type = "LRS"

  allow_nested_items_to_be_public = true
}
```

## Run Checkov Scan
```bash
checkov -d .
```
### You will se some failed checks
- FAILED CKV_AZURE_35 Storage account public access allowed
- FAILED CKV_AZURE_44 HTTPS traffic not enforced

## Fix Terraform Security Issues
```bash
nano main.tf
```
### Fixed code -
```bash
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "iac-security-rg"
  location = "East US"
}

resource "azurerm_storage_account" "example" {
  name                     = "securestorage12345"
  resource_group_name      = azurerm_resource_group.example.name
  location                 = azurerm_resource_group.example.location
  account_tier             = "Standard"
  account_replication_type = "LRS"

  allow_nested_items_to_be_public = false
  min_tls_version                 = "TLS1_2"
  enable_https_traffic_only       = true
}
```
## Run Scan Again
```bash
checkov -d .
```
## PASSED checks
