---
title: Verschlüsseln von Datenträgern für Azure-Skalierungsgruppen per Azure CLI
description: Hier erfahren Sie, wie Sie mithilfe der Azure CLI VM-Instanzen und angefügte Datenträger in einer Windows-VM-Skalierungsgruppe verschlüsseln.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 6edfa1beb568bb05bd0f3f1ef9e7792ac3c3cbe2
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515743"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Verschlüsseln von Betriebssystem- und angefügten Datenträgern in einer VM-Skalierungsgruppe mit Azure CLI

Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In dieser Schnellstartanleitung wird veranschaulicht, wie Sie mit der Azure CLI eine VM-Skalierungsgruppe erstellen und verschlüsseln. Weitere Informationen zum Anwenden von Azure Disk Encryption auf eine VM-Skalierungsgruppe finden Sie unter [Azure Disk Encryption für VM-Skalierungsgruppen](disk-encryption-overview.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.31 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-scale-set"></a>Erstellen einer Skalierungsgruppe

Vor der Erstellung einer Skalierungsgruppe müssen Sie zunächst mit [az group create](/cli/azure/group) eine Ressourcengruppe erstellen. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Erstellen Sie dann mit [az vmss create](/cli/azure/vmss) eine VM-Skalierungsgruppe. Im folgenden Beispiel wird eine Skalierungsgruppe mit dem Namen *myScaleSet* erstellt, die automatisch aktualisiert wird, wenn Änderungen angewendet werden. Außerdem werden SSH-Schlüssel generiert, falls sie unter *~/.ssh/id_rsa* nicht vorhanden sind. Jeder VM-Instanz wird ein Datenträger mit einer Kapazität von 32 GB angefügt, und die [benutzerdefinierte Skripterweiterung](../virtual-machines/extensions/custom-script-linux.md) für Azure wird zum Vorbereiten der Datenträger mit [az vmss extension set](/cli/azure/vmss/extension) verwendet:

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Die Erstellung und Konfiguration aller Ressourcen und virtuellen Computer der Skalierungsgruppe dauert einige Minuten.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Erstellen eines für Datenträgerverschlüsselung aktivierten Azure Key Vault

In Azure Key Vault können Schlüssel, geheime Schlüssel und Kennwörter gespeichert werden, um eine sichere Implementierung in Anwendungen und Diensten zu ermöglichen. Kryptografische Schlüssel werden in Azure Key Vault mit Softwareschutz gespeichert. Alternativ können Sie Schlüssel aber auch in Hardwaresicherheitsmodulen (HSMs) mit FIPS 140-2 Level 2-Zertifizierung importieren oder generieren. Die kryptografischen Schlüssel dienen zum Verschlüsseln und Entschlüsseln virtueller Datenträger, die an Ihren virtuellen Computer angefügt sind. Diese kryptografischen Schlüssel werden allein von Ihnen kontrolliert, und Sie können deren Verwendung überwachen.

Geben Sie Ihren eigenen eindeutigen *keyvault_name* an. Erstellen Sie dann mit [az keyvault create](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) einen Schlüsseltresor im gleichen Abonnement und derselben Region wie die Skalierungsgruppe, und legen Sie die Zugriffsrichtlinie *--enabled-for-disk-encryption* fest.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Verwenden eines vorhandenen Schlüsseltresors

Dieser Schritt ist nur erforderlich, wenn Sie über einen vorhandenen Schlüsseltresor verfügen, den Sie mit der Datenträgerverschlüsselung verwenden möchten. Überspringen Sie diesen Schritt, wenn Sie im vorherigen Abschnitt einen Schlüsseltresor erstellt haben.

Geben Sie Ihren eigenen eindeutigen *keyvault_name* an. Aktualisieren Sie dann Ihren Schlüsseltresor mit [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update), und legen Sie die Zugriffsrichtlinie *--enabled-for-disk-encryption* fest.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Aktivieren der Verschlüsselung

Rufen Sie zum Verschlüsseln von VM-Instanzen in einer Skalierungsgruppe zunächst Informationen über die Key Vault-Ressourcen-ID mit [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show) ab. Die folgenden Variablen werden zum Starten des Verschlüsselungsvorgangs mit [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable) verwendet:

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Es kann etwas Zeit in Anspruch nehmen, diesen Verschlüsselungsvorgang zu starten.

Da eine in einem vorherigen Schritt erstellte Upgraderichtlinie für die Skalierungsgruppe auf *automatisch* festgelegt ist, starten die VM-Instanzen den Verschlüsselungsvorgang automatisch. Bei Skalierungsgruppen, für die die Upgraderichtlinie auf „manuell“ festgelegt ist, starten Sie die Verschlüsselungsrichtlinie auf den VM-Instanzen mit [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances).

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Aktivieren der Verschlüsselung mithilfe von KEK zum Umschließen des Schlüssels

Sie können auch einen Schlüsselverschlüsselungsschlüssel für zusätzliche Sicherheit bei der Verschlüsselung der VM-Skalierungsgruppe verwenden.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  Die Syntax für den Wert des Parameters disk-encryption-keyvault ist die vollständige Bezeichnerzeichenfolge:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> Die Syntax für den Wert des Parameters key-encryption-key ist der vollständige URI zum KEK, beispielsweise:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Überprüfen des Verschlüsselungsfortschritts

Verwenden Sie [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show) zum Überprüfen des Status der Datenträgerverschlüsselung:

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Wenn VM-Instanzen verschlüsselt sind, gibt der Statuscode wie in der folgenden Beispielausgabe *EncryptionState/encrypted* an:

```console
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Deaktivieren der Verschlüsselung

Wenn Sie die verschlüsselten Datenträger von VM-Instanzen nicht mehr verwenden möchten, können Sie die Verschlüsselung mit [az vmss encryption disable](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) wie folgt deaktivieren:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Nächste Schritte

- In diesem Artikel haben Sie Azure CLI verwendet, um eine VM-Skalierungsgruppe zu verschlüsseln. Sie können auch [Azure PowerShell](disk-encryption-powershell.md) oder [Azure Resource Manager-Vorlagen](disk-encryption-azure-resource-manager.md) verwenden.
- Wenn Sie Azure Disk Encryption anwenden möchten, nachdem eine weitere Erweiterung bereitgestellt wurde, können Sie [Erweiterungssequenzierung](virtual-machine-scale-sets-extension-sequencing.md) verwenden. 
- Ein End-to-End-Batchdateibeispiel für die Datenträgerverschlüsselung von Linux-Skalierungsgruppen finden Sie [hier](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). Dieses Beispiel erstellt eine Ressourcengruppe und eine Linux-Skalierungsgruppe, bindet einen 5-GB-Datenträger für Daten ein und verschlüsselt die VM-Skalierungsgruppe.
