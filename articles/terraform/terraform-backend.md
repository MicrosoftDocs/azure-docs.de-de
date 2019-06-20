---
title: Verwenden von Azure Storage als Terraform-Back-End
description: Eine Einführung in das Speichern des Terraform-Status in Azure Storage.
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/13/2018
ms.author: tarcher
ms.openlocfilehash: a88ad25e335026d5172c7997f62629d5ada46f6e
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693300"
---
# <a name="store-terraform-state-in-azure-storage"></a>Speichern des Terraform-Status in Azure Storage

Der Terraform-Status wird verwendet, um bereitgestellte Ressourcen auf Terraform-Konfigurationen abzustimmen. Der Status gibt an, welche Azure-Ressourcen von Terraform hinzugefügt, aktualisiert oder gelöscht werden sollen. Standardmäßig wird der Terraform-Status lokal gespeichert, wenn *Terraform apply* ausgeführt wird. Diese Konfiguration ist aus diversen Gründen nicht optimal:

- Der lokale Status funktioniert nicht in einem Team oder in einer Umgebung für die Zusammenarbeit.
- Der Terraform-Status kann sensible Informationen enthalten.
- Das lokale Speichern des Benutzerstatus erhöht das Risiko einer versehentlichen Löschung.

Terraform basiert auf dem Konzept eines Status-Back-Ends, der den Remotespeicher für den Terraform-Status darstellt. Wenn Sie ein Status-Back-End verwenden, wird die Statusdatei in einem Datenspeicher wie Azure Storage gespeichert. In diesem Dokument wird beschrieben, wie Azure Storage als Terraform-Status-Back-End konfiguriert und verwendet werden kann.

## <a name="configure-storage-account"></a>Konfigurieren des Speicherkontos

Bevor Sie Azure Storage als Back-End verwenden können, muss ein Speicherkonto erstellt werden. Das Speicherkonto kann mit dem Azure-Portal, PowerShell, der Azure CLI oder Terraform selbst erstellt werden. Verwenden Sie das folgende Beispiel, um das Speicherkonto mit der Azure CLI zu konfigurieren.

```azurecli-interactive
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Notieren Sie sich den Speicherkontonamen, den Containernamen und den Speicherzugriffsschlüssel. Diese Werte sind für die Konfiguration des Remotestatus erforderlich.

## <a name="configure-state-backend"></a>Konfigurieren eines Status-Back-Ends

Das Terraform-Status-Back-End wird bei der Ausführung von *Terraform init* konfiguriert. Um das Status-Back-End konfigurieren zu können, sind folgende Daten erforderlich.

- storage_account_name: Der Name des Azure Storage-Kontos.
- container_name: Der Name des Blobcontainers.
- key: Der Name der Statusspeicherdatei, die erstellt werden soll.
- access_key: Der Speicherzugriffsschlüssel.

Jeder dieser Werte kann in der Terraform-Konfigurationsdatei oder in der Befehlszeile angegeben werden, allerdings sollte eine Umgebungsvariable für den `access_key` verwendet werden. Durch die Verwendung einer Umgebungsvariablen wird verhindert, dass der Schlüssel auf den Datenträger geschrieben wird.

Erstellen Sie eine Umgebungsvariable namens `ARM_ACCESS_KEY` mit dem Wert des Azure Storage-Zugriffsschlüssels.

```console
export ARM_ACCESS_KEY=<storage access key>
```

Um den Zugriffsschlüssel des Azure Storage-Kontos noch stärker zu schützen, speichern Sie ihn in Azure Key Vault. Die Umgebungsvariable kann dann mit einem Befehl ähnlich wie dem Folgenden festgelegt werden. Weitere Informationen zu Azure Key Vault finden Sie in der [Dokumentation zu Azure Key Vault][azure-key-vault].

```console
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Um Terraform für die Verwendung des Back-Ends zu konfigurieren, schließen Sie eine *backend*-Konfiguration vom Typ *azurerm* in die Terraform-Konfiguration ein. Fügen Sie die Werte von *storage_account_name*, *container_name* und *key* zum Konfigurationsblock hinzu.

Das folgende Beispiel konfiguriert ein Terraform-Back-End und erstellt eine Azure-Ressourcengruppe. Ersetzen Sie die Werte durch Werte aus Ihrer Umgebung.

```json
terraform {
  backend "azurerm" {
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Initialisieren Sie nun die Konfiguration mit *Terraform init*, und führen Sie die Konfiguration mit *Terraform apply* aus. Wenn Sie fertig sind, finden Sie die Statusdatei im Azure Storage-Blob.

## <a name="state-locking"></a>Statussperre

Wenn Sie ein Azure Storage-Blob für Statusspeicher verwenden, wird der Blob vor jedem Vorgang, bei dem Zustand geschrieben wird, automatisch gesperrt. Diese Konfiguration verhindert mehrere gleichzeitige Statusvorgänge, die zu Beschädigungen führen können. Weitere Informationen finden Sie unter [State Locking][terraform-state-lock] (Sperren des Status) in der Terraform-Dokumentation.

Die Sperre kann bei der Untersuchung des Blobs über das Azure-Portal oder andere Azure-Verwaltungstools angezeigt werden.

![Azure-Blob mit Sperre](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten

Standardmäßig werden in einem Azure-Blob gespeicherte Daten verschlüsselt, bevor sie in der Speicherinfrastruktur gespeichert werden. Wenn Terraform den Status benötigt, wird dieser über das Back-End abgerufen und in Ihrem Entwicklungssystem im Arbeitsspeicher gespeichert. In dieser Konfiguration wird der Status in Azure Storage geschützt und nicht auf Ihren lokalen Datenträger geschrieben.

Weitere Informationen zur Azure Storage-Dienstverschlüsselung finden Sie unter [Azure Storage-Dienstverschlüsselung für ruhende Daten][azure-storage-encryption].

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Terraform-Back-End-Konfiguration in der [Dokumentation zum Terraform-Back-End][terraform-backend].

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
