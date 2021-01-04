---
title: 'Einschränken der Datenexfiltration in Azure Storage: Azure-Befehlszeilenschnittstelle'
description: In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle die Exfiltration von Daten virtueller Netzwerke in Azure-Speicherressourcen mit VNET-Dienstendpunkt-Richtlinien einschränken können.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: a080c3953c48227301052a0bb151c8cba96e515a
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94737335"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Verwalten der Datenexfiltration in Azure Storage-Konten mit VNET-Dienstendpunkt-Richtlinien mithilfe der Azure-Befehlszeilenschnittstelle

Mithilfe von VNET-Dienstendpunkt-Richtlinien können Sie Zugriffssteuerung auf Azure Storage-Konten innerhalb eines virtuellen Netzwerks über Dienstendpunkte anwenden. Dies ist ein wichtiger Aspekt zum Sichern von Workloads und zum Verwalten der zulässigen Speicherkonten sowie der Situationen mit zulässiger Datenexfiltration.
In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Erstellen eines virtuellen Netzwerks und Hinzufügen eines Subnetzes
* Aktivieren von Dienstendpunkten für Azure Storage
* Erstellen von zwei Azure Storage-Konten und Zulassen des Netzwerkzugriffs auf diese Konten aus dem oben erstellten Subnetz
* Erstellen einer Dienstendpunkt-Richtlinie zum Zulassen des Zugriffs nur auf eines der Speicherkonten
* Bereitstellen eines virtuellen Computers im Subnetz
* Bestätigen des Zugriffs auf das zulässige Speicherkonto aus dem Subnetz
* Bestätigen der Verweigerung des Zugriffs auf das unzulässige Speicherkonto aus dem Subnetz

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
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Aktivieren eines Dienstendpunkts 

In diesem Beispiel wird ein Dienstendpunkt für *Microsoft.Storage* für das Subnetz *Private* erstellt: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
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

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Einschränken des Netzwerkzugriffs auf Azure Storage-Konten

In diesem Abschnitt werden die Schritte zum Einschränken des Netzwerkzugriffs für ein Azure Storage-Konto aus dem angegebenen Subnetz in einem virtuellen Netzwerk über den Dienstendpunkt aufgeführt.

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

Erstellen Sie zwei Azure Storage-Konten mithilfe von [az storage account create](/cli/azure/storage/account).

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Rufen Sie nach dem Erstellen der beiden Speicherkonten die Verbindungszeichenfolge für die Speicherkonten mit [az storage account show-connection-string](/cli/azure/storage/account) in eine Variable ab. Die Verbindungszeichenfolge wird verwendet, um in einem späteren Schritt eine Dateifreigabe zu erstellen.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Zeigen Sie den Inhalt der Variablen an, und notieren Sie sich den Wert für **AccountKey**, der in der Ausgabe zurückgegeben wird, weil dieser in einem späteren Schritt verwendet wird.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Erstellen einer Dateifreigabe im Speicherkonto

Erstellen Sie mit [az storage share create](/cli/azure/storage/share) eine Dateifreigabe im Speicherkonto. In einem späteren Schritt wird diese Dateifreigabe bereitgestellt, um den Netzwerkzugriff darauf zu bestätigen.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>Verweigern des gesamten Netzwerkzugriffs auf das Speicherkonto

Standardmäßig akzeptieren Speicherkonten Netzwerkverbindungen von Clients in allen Netzwerken. Um den Zugriff auf ausgewählte Netzwerke einzuschränken, ändern Sie die Standardaktion mit [az storage account update](/cli/azure/storage/account) in *Deny* (Verweigern). Nachdem der Netzwerkzugriff verweigert wurde, kann auf das Speicherkonto aus keinem Netzwerk mehr zugegriffen werden.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>Aktivieren des Netzwerkzugriffs aus dem Subnetz des virtuellen Netzwerks

Erlauben Sie den Netzwerkzugriff auf das Speicherkonto aus dem Subnetz *Private* mit [az storage account network-rule add](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Anwenden der Richtlinie zum Zulassen des Zugriffs auf ein gültiges Speicherkonto

Azure-Dienstendpunkt-Richtlinien sind nur für Azure Storage verfügbar. Wir aktivieren daher für dieses Beispielsetup den Dienstendpunkt für *Microsoft.Storage* in diesem Subnetz.

Dienstendpunkt-Richtlinien werden auf Dienstendpunkte angewandt. Wir beginnen mit dem Erstellen einer Dienstendpunkt-Richtlinie. Anschließend erstellen wir die Richtliniendefinitionen unter dieser Richtlinie für Azure Storage-Konten, die für dieses Subnetz genehmigt werden sollen.

Erstellen einer Dienstendpunktrichtlinie

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Speichern Sie den Ressourcen-URI für das zulässige Speicherkonto in einer Variable. Bevor Sie den folgenden Befehl ausführen, ersetzen Sie *\<your-subscription-id>* durch Ihre tatsächliche Abonnement-ID.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Erstellen Sie eine Richtliniendefinition zum Zulassen des oben genannten Azure Storage-Kontos, und fügen Sie sie der Dienstendpunkt-Richtlinie hinzu.

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

Aktualisieren Sie schließlich das Subnetz des virtuellen Netzwerks, sodass es der im vorherigen Schritt erstellten Dienstendpunkt-Richtlinie zugeordnet ist.

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Überprüfen der Zugriffsbeschränkung auf Azure Storage-Konten

### <a name="create-the-virtual-machine"></a>Erstellen des virtuellen Computers

Zum Testen des Netzwerkzugriffs auf ein Speicherkonto stellen Sie eine VM im Subnetz bereit.

Erstellen Sie mit *az vm create* eine VM im Subnetz [Private](/cli/azure/vm). Wenn SSH-Schlüssel nicht bereits an einem Standardschlüsselspeicherort vorhanden sind, werden sie durch den Befehl erstellt. Um einen bestimmten Satz von Schlüsseln zu verwenden, nutzen Sie die Option `--ssh-key-value`.

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

### <a name="confirm-access-to-storage-account"></a>Bestätigen des Zugriffs auf das Speicherkonto

Stellen Sie mit SSH eine Verbindung mit dem virtuellen Computer *MyVmPrivate* her. Ersetzen Sie *\<publicIpAddress>* durch die öffentliche IP-Adresse Ihres virtuellen Computers *MyVmPrivate*.

```bash 
ssh <publicIpAddress>
```

Erstellen Sie einen Ordner für einen Bereitstellungspunkt:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Stellen Sie die Azure-Dateifreigabe für das Verzeichnis bereit, das Sie erstellt haben. Bevor Sie den unten angegebenen Befehl ausführen, ersetzen Sie *\<storage-account-key>* durch den Wert von *AccountKey* aus **$saConnectionString1**.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Die Eingabeaufforderung `user@myVmPrivate:~$` wird angezeigt. Die Azure-Dateifreigabe wurde erfolgreich für */mnt/MyAzureFileShare* bereitgestellt.

### <a name="confirm-access-is-denied-to-storage-account"></a>Bestätigen, dass der Zugriff auf das Speicherkonto verweigert wird

Erstellen Sie auf derselben VM *myVmPrivate* ein Verzeichnis für einen Bereitstellungspunkt:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Versuchen Sie, die Azure-Dateifreigabe aus dem Speicherkonto *notallowedstorageacc* in dem erstellten Verzeichnis einzubinden. In diesem Artikel wird davon ausgegangen, dass Sie die neueste Version von Ubuntu bereitgestellt haben. Bei Verwendung von früheren Versionen von Ubuntu finden Sie unter [Bereitstellen unter Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zusätzliche Anweisungen zum Bereitstellen von Dateifreigaben. 

Bevor Sie den unten angegebenen Befehl ausführen, ersetzen Sie *\<storage-account-key>* durch den Wert von *AccountKey* aus **$saConnectionString2**.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Der Zugriff wird verweigert, und Sie erhalten den Fehler `mount error(13): Permission denied`, da dieses Speicherkonto nicht in der Zulassungsliste der Dienstendpunkt-Richtlinie enthalten ist, die wir auf das Subnetz angewandt haben. 

Beenden Sie die SSH-Sitzung mit dem virtuellen Computer *myVmPublic*.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle enthaltenen Ressourcen nicht mehr benötigt werden, können Sie sie mit [az group delete](/cli/azure) entfernen.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine Dienstendpunkt-Richtlinie über den Dienstendpunkt eines virtuellen Azure-Netzwerks auf Azure Storage angewandt. Sie haben Azure Storage-Konten erstellt und den Netzwerkzugriff auf bestimmte Speicherkonten im Subnetz eines virtuellen Netzwerks eingeschränkt (und somit für andere Konten verweigert). Weitere Informationen zu Dienstendpunkt-Richtlinien finden Sie unter [Dienstendpunkt-Richtlinien: Übersicht](virtual-network-service-endpoint-policies-overview.md).
