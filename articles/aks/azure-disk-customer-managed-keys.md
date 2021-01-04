---
title: Verwenden eines kundenseitig verwalteten Schlüssels zum Verschlüsseln von Azure-Datenträgern in Azure Kubernetes Service (AKS)
description: Bring Your Own Key(BYOK) zum Verschlüsseln von AKS-Datenträgern für Betriebssystem und Daten.
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: c71052f44f6912006a952bae16fd89ec1dc937db
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96607890"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Bring Your Own Key (BYOK) mit Azure-Datenträgern in Azure Kubernetes Service (AKS)

Azure Storage verschlüsselt alle Daten in einem ruhenden Speicherkonto. Standardmäßig werden Daten mit von Microsoft verwalteten Schlüsseln verschlüsselt. Für zusätzliche Kontrolle über die Verschlüsselungsschlüssel können Sie kundenseitig verwaltete Schlüssel bereitstellen, die für die ruhende Verschlüsselung der Datenträger für Betriebssystem und Daten für Ihre AKS-Cluster verwendet werden sollen. Erfahren Sie mehr über kundenseitig verwaltete Schlüssel unter [Linux][customer-managed-keys-linux] und [Windows][customer-managed-keys-windows].

## <a name="limitations"></a>Einschränkungen
* Die Unterstützung der Datenträgerverschlüsselung ist auf AKS-Cluster mit Version 1.17 und höher von Kubernetes beschränkt.
* Die Verschlüsselung von Datenträgern für das Betriebssystem und für Daten mit kundenseitig verwalteten Schlüsseln kann nur beim Erstellen eines AKS-Clusters aktiviert werden.

## <a name="prerequisites"></a>Voraussetzungen
* Sie müssen vorläufiges Löschen und den Schutz vor endgültigem Löschen für *Azure Key Vault* aktivieren, wenn Sie Key Vault zum Verschlüsseln von verwalteten Datenträgern verwenden.
* Sie benötigen mindestens Version 2.11.1 der Azure-Befehlszeilenschnittstelle.

## <a name="create-an-azure-key-vault-instance"></a>Erstellen einer Azure Key Vault-Instanz

Verwenden Sie eine Azure Key Vault-Instanz zum Speichern Ihrer Schlüssel.  Sie können optional das Azure-Portal verwenden, um [kundenseitig verwaltete Schlüsseln mit Azure Key Vault zu konfigurieren][byok-azure-portal].

Erstellen Sie eine neue *Ressourcengruppe*, erstellen Sie dann eine neue *Key Vault*-Instanz, und aktivieren Sie vorläufiges Löschen und den Schutz vor endgültigem Löschen.  Stellen Sie sicher, dass Sie dieselben Regions- und Ressourcengruppennamen für jeden Befehl verwenden.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Erstellen einer DiskEncryptionSet-Instanz

Ersetzen Sie *myKeyVaultName* durch den Namen Ihres Schlüsseltresors.  Sie benötigen außerdem einen in Azure Key Vault gespeicherten *Schlüssel*, um die folgenden Schritte auszuführen.  Speichern Sie entweder Ihren vorhandenen Schlüssel in dem Key Vault, den Sie in den vorherigen Schritten erstellt haben, oder [generieren Sie einen neuen Schlüssel][key-vault-generate], und ersetzen Sie unten *myKeyName* durch den Namen Ihres Schlüssels.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query "[id]" -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query "[key.kid]" -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Gewähren des DiskEncryptionSet-Zugriffs auf den Schlüsseltresor

Verwenden Sie DiskEncryptionSet und Ressourcengruppen, die Sie in den vorherigen Schritten erstellt haben, und gewähren Sie der DiskEncryptionSet-Ressource Zugriff auf den Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query "[identity.principalId]" -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Erstellen eines neuen AKS-Clusters und Verschlüsseln des Betriebssystemdatenträgers

Erstellen Sie eine **neue Ressourcengruppe** und einen neuen AKS-Cluster, und verwenden Sie dann Ihren Schlüssel zum Verschlüsseln des Betriebssystemdatenträgers. Kundenseitig verwaltete Schlüssel werden nur in höheren Kubernetes-Versionen als 1.17 unterstützt. 

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie eine neue Ressourcengruppe für Ihren AKS-Cluster erstellen.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query "[id]" -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

Wenn dem oben erstellten Cluster neue Knotenpools hinzugefügt werden, wird der während der Erstellung bereitgestellte kundenseitig verwaltete Schlüssel zum Verschlüsseln des Betriebssystemdatenträgers verwendet.

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>Verschlüsseln Ihres AKS-Clusterdatenträgers für Daten (optional)
Der Verschlüsselungsschlüssel für den Betriebssystem-Datenträger wird zum Verschlüsseln des Datenträgers verwendet, wenn der Schlüssel nicht von v1.17.2 für den Datenträger bereitgestellt wird. Sie können auch AKS-Datenträger mit ihren anderen Schlüsseln verschlüsseln.

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie über die richtigen AKS-Anmeldeinformationen verfügen. Der Dienstprinzipal benötigt „Mitwirkender“-Zugriff auf die Ressourcengruppe, in der Ihr DiskEncryptionSet bereitgestellt ist. Andernfalls erhalten Sie eine Fehlermeldung, die darauf hinweist, dass der Dienstprinzipal keine Berechtigungen besitzt.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Erstellen Sie eine Datei namens **byok-azure-disk.yaml**, die folgende Informationen enthält.  Ersetzen Sie „myAzureSubscriptionId“, „myResourceGroup“ und „myDiskEncrptionSetName“ durch Ihre Werte, und wenden Sie die yaml-Datei an.  Stellen Sie sicher, dass Sie die Ressourcengruppe verwenden, in der Ihr DiskEncryptionSet bereitgestellt ist.  Bei Verwendung von Azure Cloud Shell kann diese Datei mit vi oder nano erstellt werden – genau wie bei der Verwendung eines virtuellen oder physischen Systems:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Führen Sie als Nächstes diese Bereitstellung in Ihrem AKS-Cluster aus:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie [Bewährte Methoden für AKS-Clustersicherheit][best-practices-security].

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/windows/disk-encryption.md#supported-regions
