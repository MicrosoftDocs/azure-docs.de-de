---
title: Verwenden von Terraform zum Erstellen einer vollständigen Linux-VM in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Terraform eine vollständige Linux-VM-Umgebung in Azure erstellen und verwalten.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2017
ms.author: echuvyrov
ms.openlocfilehash: 406af03ab81b279be4f0d72dcca4ec8ec0e4ee55
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304647"
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Erstellen einer vollständigen Linux-VM-Infrastruktur in Azure mit Terraform

Mit Terraform können Sie vollständige Infrastrukturbereitstellungen in Azure definieren und erstellen. Dazu lassen sich Terraform-Vorlagen in einem für Menschen lesbaren Format erstellen, die Azure-Ressourcen konsistent und reproduzierbar erstellen und konfigurieren. In diesem Artikel wird gezeigt, wie Sie eine vollständige Linux-Umgebung und die unterstützenden Ressourcen mit Terraform erstellen. Hier erfahren Sie auch, wie Sie [Terraform installieren und konfigurieren](terraform-install-configure.md).


## <a name="create-azure-connection-and-resource-group"></a>Erstellen einer Azure-Verbindung und -Ressourcengruppe

Lassen Sie uns alle Abschnitte einer Terraform-Vorlage durchgehen. Sie können auch die Vollversion der [Terraform-Vorlage](#complete-terraform-script) sehen, die Sie kopieren und einfügen können.

Der `provider`-Abschnitt weist Terraform an, einen Azure-Anbieter zu verwenden. Um die Werte für *subscription_id*, *client_id*, *client_secret* und [tenant_id](terraform-install-configure.md) abzurufen, sehen Sie im *Handbuch zum Installieren und Konfigurieren von Terraform* nach. 

> [!TIP]
> Wenn Sie Umgebungsvariablen für die Werte erstellen oder die [Azure Cloud Shell Bash-Funktionalität](/azure/cloud-shell/overview) verwenden, müssen Sie die Variablendeklarationen in diesem Abschnitt nicht einbeziehen.

```tf
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

Der folgende Abschnitt erstellt eine Ressourcengruppe namens `myResourceGroup` am Standort `eastus`:

```tf
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

In allen weiteren Abschnitten verweisen Sie mit *${azurerm_resource_group.myterraformgroup.name}* auf die Ressourcengruppe.

## <a name="create-virtual-network"></a>Virtuelles Netzwerk erstellen
Im folgenden Abschnitt wird ein virtuelles Netzwerk namens *myVnet* im Adressbereich *10.0.0.0/16* erstellt:

```tf
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags = {
        environment = "Terraform Demo"
    }
}
```

Im folgenden Abschnitt wird das Subnetz *mySubnet* im virtuellen Netzwerk *myVnet* erstellt:

```tf
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse
Für den Zugriff auf Ressourcen über das Internet erstellen Sie eine öffentliche IP-Adresse für Ihren virtuellen Computer und weisen sie zu. Im folgenden Abschnitt wird eine öffentliche IP-Adresse *myPublicIP* erstellt:

```tf
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe
Netzwerksicherheitsgruppen steuern den ein- und ausgehenden Netzwerkdatenverkehr des virtuellen Computers. Im folgenden Abschnitt wird eine Netzwerksicherheitsgruppe *myNetworkSecurityGroup* erstellt und eine Regel zum Zulassen von SSH-Datenverkehr über den TCP-Port 22 definiert:

```tf
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Erstellen virtueller Netzwerkschnittstellenkarten
Eine virtuelle Netzwerkschnittstellenkarte (NIC) verbindet Ihren virtuellen Computer mit einem angegebenen virtuellen Netzwerk, einer öffentlichen IP-Adresse und einer Netzwerksicherheitsgruppe. Im folgenden Abschnitt einer Terraform-Vorlage wird die virtuelle Netzwerkkarte *myNIC* erstellt, die mit den erstellten virtuellen Netzwerkressourcen verbunden ist:

```tf
resource "azurerm_network_interface" "myterraformnic" {
    name                = "myNIC"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    network_security_group_id = "${azurerm_network_security_group.myterraformnsg.id}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-storage-account-for-diagnostics"></a>Erstellen eines Speicherkontos für Diagnosezwecke
Um die Startdiagnose für eine VM zu speichern, benötigen Sie ein Speicherkonto. Diese Startdiagnose kann Sie bei der Problembehandlung und Überwachung des Status Ihrer VM unterstützen. Das Speicherkonto, dass Sie erstellen, dient nur der Speicherung der Startdiagnosedaten. Da jedes Speicherkonto einen eindeutigen Namen haben muss, wird im nächsten Abschnitt zufälliger Text erzeugt:

```tf
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}
```

Nun können Sie ein Speicherkonto erstellen. Im folgenden Abschnitt wird ein Speicherkonto erstellt, dessen Name auf dem im vorherigen Schritt generierten zufälligen Text basiert:

```tf
resource "azurerm_storage_account" "mystorageaccount" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    location            = "eastus"
    account_replication_type = "LRS"
    account_tier = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

Der letzte Schritt besteht im Erstellen eines virtuellen Computers und dem Verwenden der erstellten Ressourcen. Im folgenden Abschnitt wird eine VM namens *myVM* erstellt und die virtuelle NIC *myNIC* angefügt. Das neueste *Ubuntu 16.04 LTS*-Image wird verwendet, und ein Benutzer namens *azureuser* wird mit deaktivierter Kennwortauthentifizierung erstellt.

 SSH-Schlüsseldaten finden Sie im Abschnitt *ssh_keys*. Geben Sie einen gültigen öffentlichen SSH-Schlüssel im Feld *key_data* an.

```tf
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled     = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Vervollständigen des Terraform-Skripts

Um alle diese Abschnitte zusammenzuführen und mit Terraform auszuführen, erstellen Sie eine Datei namens *terraform_azure.tf*, und fügen Sie den folgenden Inhalt ein:

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn’t exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = "${azurerm_resource_group.myterraformgroup.name}"
    network_security_group_id = "${azurerm_network_security_group.myterraformnsg.id}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = "${azurerm_resource_group.myterraformgroup.name}"
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Erstellen und Bereitstellen der Infrastruktur
Wenn die Terraform-Vorlage erstellt wurde, initialisieren Sie Terraform. Dadurch wird sichergestellt, dass Terraform über alle erforderlichen Komponenten zum Erstellen der Vorlage in Azure verfügt.

```bash
terraform init
```

Lassen Sie die Vorlage als Nächstes von Terraform prüfen und validieren. Bei diesem Schritt werden die erforderlichen Ressourcen mit den von Terraform gespeicherten Zustandsinformationen verglichen. Anschließend wird die geplante Ausführung ausgegeben. Ressourcen werden *nicht* in Azure erstellt.

```bash
terraform plan
```

Nach der Ausführung des vorherigen Befehls sollte etwa der folgende Bildschirm angezeigt werden:

```bash
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn’t specify an “-out” parameter to save this plan, so when
“apply” is called, Terraform can’t guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Wenn alle Eingaben richtig und Sie für die Erstellung der Infrastruktur in Azure bereit sind, wenden Sie die Vorlage in Terraform an:

```bash
terraform apply
```

Wenn Terraform den Vorgang abschließt, wurde die VM-Infrastruktur erstellt. Sie können die öffentliche IP-Adresse Ihres virtuellen Computers mit dem Befehl [az vm show](/cli/azure/vm) abrufen:

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Anschließend können Sie eine SSH-Verbindung mit dem virtuellen Computer herstellen:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Nächste Schritte
Sie haben die grundlegende Infrastruktur in Azure mithilfe von Terraform erstellt. Komplexere Szenarios einschließlich Beispielen zur Verwendung von Lastenausgleichen und VM-Skalierungsgruppen finden Sie in den zahlreichen [Terraform-Beispielen für Azure](https://github.com/hashicorp/terraform/tree/master/examples). Eine vollständige und aktuelle Liste der unterstützten Azure-Anbieter finden Sie in der [Terraform-Dokumentation](https://www.terraform.io/docs/providers/azurerm/index.html).
