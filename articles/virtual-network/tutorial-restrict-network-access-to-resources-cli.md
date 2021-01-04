---
title: Einschränken des Netzwerkzugriffs auf PaaS-Ressourcen – Azure CLI
description: In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure CLI den Netzwerkzugriff auf Azure-Ressourcen wie Azure Storage und Azure SQL-Datenbank mit VNET-Dienstendpunkten einschränken können.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1a1eab081a7c1e83a57ef4735c6eb5248d92defc
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94734105"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Einschränken des Netzwerkzugriffs auf PaaS-Ressourcen mit VNET-Dienstendpunkten mithilfe der Azure CLI

VNET-Dienstendpunkte ermöglichen es Ihnen, den Netzwerkzugriff auf einige Azure-Dienstressourcen auf ein Subnetz eines virtuellen Netzwerks einzuschränken. Sie können auch den Internetzugriff auf die Ressourcen entfernen. Dienstendpunkte ermöglichen eine direkte Verbindung zwischen Ihrem virtuellen Netzwerk und unterstützten Azure-Diensten, sodass Sie mithilfe des privaten Adressraums Ihres virtuellen Netzwerks auf die Azure-Dienste zugreifen können. Datenverkehr, der über Dienstendpunkte für Azure-Ressourcen bestimmt ist, verbleibt immer im Microsoft Azure-Backbonenetzwerk. In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Erstellen eines virtuellen Netzwerks mit einem Subnetz
* Hinzufügen eines Subnetzes und Aktivieren eines Dienstendpunkts
* Erstellen einer Azure-Ressource und Zulassen des Netzwerkzugriffs darauf ausschließlich aus einem Subnetz
* Bereitstellen eines virtuellen Computers für jedes Subnetz
* Bestätigen des Zugriffs auf eine Ressource aus einem Subnetz
* Bestätigen, dass der Zugriff auf eine Ressource aus einem Subnetz und dem Internet verweigert wird

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Vor der Erstellung eines virtuellen Netzwerks müssen Sie eine Ressourcengruppe für das virtuelle Netzwerk und alle anderen in diesem Artikel erstellten Ressourcen erstellen. Erstellen Sie mit [az group create](/cli/azure/group) eine Ressourcengruppe. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet) ein virtuelles Netzwerk mit einem Subnetz.

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Aktivieren eines Dienstendpunkts 

Sie können Dienstendpunkte nur für Dienste aktivieren, die Dienstendpunkte unterstützen. Zeigen Sie dienstendpunktfähige Dienste, die an einen Azure-Standort verfügbar sind, mit [az network vnet list-endpoint-services](/cli/azure/network/vnet) an. Im folgenden Beispiel wird eine Liste der dienstendpunktfähigen Dienste zurückgegeben, die in der Region *eastus* verfügbar sind. Die Liste der zurückgegebenen Dienste wird im Lauf der Zeit länger werden, da immer mehr Azure-Dienste für Dienstendpunkte aktiviert werden.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Erstellen Sie mit [az network vnet subnet create](/cli/azure/network/vnet/subnet) ein zusätzliches Subnetz im virtuellen Netzwerk. In diesem Beispiel wird ein Dienstendpunkt für *Microsoft.Storage* für das Subnetz erstellt: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Einschränken des Netzwerkzugriffs für ein Subnetz

Erstellen Sie mit [az network nsg create](/cli/azure/network/nsg) eine Netzwerksicherheitsgruppe. Im folgenden Beispiel wird eine Netzwerksicherheitsgruppe namens *myNsgPrivate* erstellt.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Ordnen Sie die Netzwerksicherheitsgruppe mit [az network vnet subnet update](/cli/azure/network/vnet/subnet) dem Subnetz *Private* zu. Im folgenden Beispiel wird die Netzwerksicherheitsgruppe *myNsgPrivate* dem Subnetz *Private* hinzugefügt:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Erstellen Sie Sicherheitsregeln mit [az network nsg rule create](/cli/azure/network/nsg/rule). Die folgende Regel erlaubt den ausgehenden Zugriff auf die öffentlichen IP-Adressen, die dem Azure Storage-Dienst zugewiesen sind: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

Jede Netzwerksicherheitsgruppe enthält mehrere [Standardsicherheitsregeln](security-overview.md#default-security-rules). Die folgende Regel überschreibt eine Standardsicherheitsregel, die ausgehenden Zugriff auf alle öffentlichen IP-Adressen zulässt. Die `destination-address-prefix "Internet"`-Option verweigert den ausgehenden Zugriff auf alle öffentlichen IP-Adressen. Die vorherige Regel überschreibt diese Regel. Dies ist auf die höhere Priorität zurückzuführen, die den Zugriff auf die öffentlichen IP-Adressen von Azure Storage zulässt.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

Die folgende Regel lässt eingehenden SSH-Datenverkehr an das Subnetz von überall aus zu. Die Regel setzt eine Standardsicherheitsregel außer Kraft, die jeglichen eingehenden Verkehr aus dem Internet abweist. SSH in das Subnetz ist zulässig, sodass die Konnektivität in einem späteren Schritt getestet werden kann.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-a-resource"></a>Einschränken des Netzwerkzugriffs auf eine Ressource

Die Schritte, die erforderlich sind, um den Netzwerkzugriff auf Ressourcen einzuschränken, die durch Azure-Dienste erstellt und für Dienstendpunkte aktiviert wurden, sind je nach Dienst unterschiedlich. Informationen zu den Schritten für einzelne Dienste finden Sie in der Dokumentation des jeweiligen Diensts. Der Rest dieses Artikels enthält als Beispiel Schritte zum Einschränken des Netzwerkzugriffs für ein Azure Storage-Konto.

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

Erstellen Sie ein Azure-Speicherkonto mithilfe von [az storage account create](/cli/azure/storage/account). Ersetzen Sie `<replace-with-your-unique-storage-account-name>` durch einen Namen, der an allen Azure-Standorten eindeutig, zwischen 3 und 24 Zeichen lang ist und nur aus Ziffern und Kleinbuchstaben besteht.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Nachdem das Speicherkonto erstellt wurde, rufen Sie die Verbindungszeichenfolge für das Speicherkonto mit [az storage account show-connection-string](/cli/azure/storage/account) in eine Variable ab. Die Verbindungszeichenfolge wird verwendet, um in einem späteren Schritt eine Dateifreigabe zu erstellen.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Zeigen Sie den Inhalt der Variablen an, und notieren Sie sich den Wert für **AccountKey**, der in der Ausgabe zurückgegeben wird, weil dieser in einem späteren Schritt verwendet wird.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Erstellen einer Dateifreigabe im Speicherkonto

Erstellen Sie mit [az storage share create](/cli/azure/storage/share) eine Dateifreigabe im Speicherkonto. In einem späteren Schritt wird diese Dateifreigabe bereitgestellt, um den Netzwerkzugriff darauf zu bestätigen.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Verweigern des gesamten Netzwerkzugriffs auf ein Speicherkonto

Standardmäßig akzeptieren Speicherkonten Netzwerkverbindungen von Clients in allen Netzwerken. Um den Zugriff auf ausgewählte Netzwerke einzuschränken, ändern Sie die Standardaktion mit [az storage account update](/cli/azure/storage/account) in *Deny* (Verweigern). Nachdem der Netzwerkzugriff verweigert wurde, kann auf das Speicherkonto aus keinem Netzwerk mehr zugegriffen werden.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Aktivieren des Netzwerkzugriffs aus einem Subnetz

Erlauben Sie den Netzwerkzugriff auf das Speicherkonto aus dem Subnetz *Private* mit [az storage account network-rule add](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Zum Testen des Netzwerkzugriffs auf ein Speicherkonto stellen Sie einen virtuellen Computer für jedes Subnetz bereit.

### <a name="create-the-first-virtual-machine"></a>Erstellen des ersten virtuellen Computers

Erstellen Sie mit *az vm create* eine VM im Subnetz [Public](/cli/azure/vm). Wenn SSH-Schlüssel nicht bereits an einem Standardschlüsselspeicherort vorhanden sind, werden sie durch den Befehl erstellt. Um einen bestimmten Satz von Schlüsseln zu verwenden, nutzen Sie die Option `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

Die Erstellung des virtuellen Computers dauert einige Minuten. Nach dem Erstellen der VM zeigt die Azure CLI ähnliche Informationen wie im folgenden Beispiel an: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Notieren Sie die **publicIpAddress** in der zurückgegebenen Ausgabe. Über diese Adresse wird in einem späteren Schritt über das Internet auf den virtuellen Computer zugegriffen.

### <a name="create-the-second-virtual-machine"></a>Erstellen des zweiten virtuellen Computers

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

Die Erstellung des virtuellen Computers dauert einige Minuten. Nachdem er erstellt wurde, notieren Sie die **publicIpAddress** in der zurückgegebenen Ausgabe. Über diese Adresse wird in einem späteren Schritt über das Internet auf den virtuellen Computer zugegriffen.

## <a name="confirm-access-to-storage-account"></a>Bestätigen des Zugriffs auf das Speicherkonto

Stellen Sie mit SSH eine Verbindung mit dem virtuellen Computer *MyVmPrivate* her. Ersetzen Sie *\<publicIpAddress>* durch die öffentliche IP-Adresse Ihres virtuellen Computers *MyVmPrivate*.

```bash 
ssh <publicIpAddress>
```

Erstellen Sie einen Ordner für einen Bereitstellungspunkt:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Stellen Sie die Azure-Dateifreigabe für das Verzeichnis bereit, das Sie erstellt haben. Ersetzen Sie vor dem Ausführen des folgenden Befehls `<storage-account-name>` durch den Kontonamen und `<storage-account-key>` durch den Schlüssel, den Sie unter [Erstellen eines Speicherkontos](#create-a-storage-account) abgerufen haben.

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Die Eingabeaufforderung `user@myVmPrivate:~$` wird angezeigt. Die Azure-Dateifreigabe wurde erfolgreich für */mnt/MyAzureFileShare* bereitgestellt.

Bestätigen Sie, dass der virtuelle Computer über keine ausgehende Verbindung mit einer beliebigen anderen öffentlichen IP-Adresse verfügt:

```bash
ping bing.com -c 4
```

Sie erhalten keine Antworten, da die dem Subnetz *Private* zugeordnete Netzwerksicherheitsgruppe keinen ausgehenden Zugriff auf andere öffentliche IP-Adressen als auf die dem Azure Storage-Dienst zugewiesenen Adressen erlaubt.

Beenden Sie die SSH-Sitzung mit dem virtuellen Computer *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Bestätigen, dass der Zugriff auf das Speicherkonto verweigert wird

Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung mit dem virtuellen Computer *myVmPrivate*. Ersetzen Sie `<publicIpAddress>` durch die öffentliche IP-Adresse Ihres virtuellen Computers *MyVmPublic*: 

```bash 
ssh <publicIpAddress>
```

Erstellen Sie ein Verzeichnis für einen Bereitstellungspunkt:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Versuchen Sie, die Azure-Dateifreigabe für das Verzeichnis bereitzustellen, das Sie erstellt haben. In diesem Artikel wird davon ausgegangen, dass Sie die neueste Version von Ubuntu bereitgestellt haben. Bei Verwendung von früheren Versionen von Ubuntu finden Sie unter [Bereitstellen unter Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zusätzliche Anweisungen zum Bereitstellen von Dateifreigaben. Ersetzen Sie vor dem Ausführen des folgenden Befehls `<storage-account-name>` durch den Kontonamen und `<storage-account-key>` durch den Schlüssel, den Sie unter [Erstellen eines Speicherkontos](#create-a-storage-account) abgerufen haben:

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Der Zugriff wird verweigert, und Sie erhalten einen `mount error(13): Permission denied`-Fehler, da der virtuelle Computer *MyVmPublic* im Subnetz *Public* bereitgestellt wird. Für das Subnetz *Public* ist kein Dienstendpunkt für Azure Storage aktiviert, und das Speicherkonto erlaubt nur den Netzwerkzugriff aus dem Subnetz *Private*, nicht aus dem Subnetz *Public*.

Beenden Sie die SSH-Sitzung mit dem virtuellen Computer *myVmPublic*.

Versuchen Sie von Ihrem Computer aus, die Freigaben in Ihrem Speicherkonto mit [az storage share list](/cli/azure/storage/share?view=azure-cli-latest) anzuzeigen. Ersetzen Sie `<account-name>` und `<account-key>` durch den Namen des Speicherkontos und den Schlüssel aus [Erstellen eines Speicherkontos](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Der Zugriff wird verweigert, und Sie erhalten einen Fehler des Typs *Diese Anforderung ist nicht berechtigt, diesen Vorgang auszuführen*, da sich Ihr Computer nicht im Subnetz *Private* des virtuellen Netzwerks *MyVirtualNetwork* befindet.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle enthaltenen Ressourcen nicht mehr benötigt werden, können Sie sie mit [az group delete](/cli/azure) entfernen.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einen Dienstendpunkt für ein Subnetz eines virtuellen Netzwerks aktiviert. Sie haben erfahren, dass Dienstendpunkte für Ressourcen aktiviert werden können, die mit mehreren Azure-Diensten bereitgestellt werden. Sie haben ein Azure Storage-Konto erstellt und den Netzwerkzugriff auf das Speicherkonto ausschließlich auf Ressourcen im Subnetz eines virtuellen Netzwerks eingeschränkt. Weitere Informationen zu Dienstendpunkten finden Sie unter [Dienstendpunkte im virtuellen Netzwerk](virtual-network-service-endpoints-overview.md) und [Hinzufügen, Ändern oder Löschen von Subnetzen virtueller Netzwerke](virtual-network-manage-subnet.md).

Wenn Sie mehrere virtuelle Netzwerke in Ihrem Konto verwenden, können Sie zwei virtuelle Netzwerke miteinander verbinden, damit die Ressourcen in jedem virtuellen Netzwerk miteinander kommunizieren können. Informationen zur Vorgehensweise finden Sie unter [Herstellen von Verbindungen zwischen virtuellen Netzwerken](tutorial-connect-virtual-networks-cli.md).
