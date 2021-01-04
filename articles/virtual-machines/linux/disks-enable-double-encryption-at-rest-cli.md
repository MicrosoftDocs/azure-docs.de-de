---
title: Aktivieren der doppelten Verschlüsselung für ruhende Daten – Azure CLI – verwaltete Datenträger
description: Hier erfahren Sie, wie Sie die doppelte Verschlüsselung für ruhende Daten auf Ihren verwalteten Datenträgern mithilfe der Azure CLI aktivieren.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 55a49c5f8d066cd6893c46ef9942462bb7911307
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360614"
---
# <a name="use-the-azure-cli-to-enable-double-encryption-at-rest-for-managed-disks"></a>Verwenden der Azure CLI zum Aktivieren der doppelten Verschlüsselung von ruhenden Daten auf verwalteten Datenträgern

Azure Disk Storage unterstützt die doppelte Verschlüsselung von ruhenden Daten auf verwalteten Datenträgern. Informationen zum Konzept der doppelten Verschlüsselung ruhender Daten sowie weitere Typen der Verschlüsselung von Daten auf verwalteten Datenträgern finden Sie im Abschnitt [Doppelte Verschlüsselung ruhender Daten](../disk-encryption.md#double-encryption-at-rest) des Artikels zur Datenträgerverschlüsselung.

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie die neueste Version der [Azure CLI](/cli/azure/install-az-cli2), und melden Sie sich mit [az login](/cli/azure/reference-index) bei einem Azure-Konto an.

## <a name="getting-started"></a>Erste Schritte

1. Erstellen Sie eine Azure Key Vault-Instanz und den Verschlüsselungsschlüssel.

    Beim Erstellen der Key Vault-Instanz müssen Sie vorläufiges Löschen und den Schutz vor endgültigem Löschen aktivieren. Durch vorläufiges Löschen wird sichergestellt, dass der Schlüsseltresor einen gelöschten Schlüssel für einen bestimmten Aufbewahrungszeitraum (standardmäßig 90 Tage) speichert. Der Schutz vor endgültigem Löschen stellt sicher, dass ein gelöschter Schlüssel erst nach Ablauf der Aufbewahrungsdauer dauerhaft gelöscht werden kann. Diese Einstellungen schützen Sie vor dem Verlust von Daten durch versehentliches Löschen. Diese Einstellungen sind obligatorisch, wenn ein Schlüsseltresor für die Verschlüsselung verwalteter Datenträger verwendet wird.

    
    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    Erstellen Sie eine DiskEncryptionSet-Klasse. Legen Sie encryptionType dabei auf EncryptionAtRestWithPlatformAndCustomerKeys fest. Verwenden Sie die API-Version **2020-05-01** in der Azure Resource Manager-Vorlage (ARM). 
    
        ```azurecli
        az group deployment create -g $rgName \
       --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" \
        --parameters "diskEncryptionSetName=$diskEncryptionSetName" "encryptionType=EncryptionAtRestWithPlatformAndCustomerKeys" "keyVaultId=$keyVaultId" "keyVaultKeyUrl=$keyVaultKeyUrl" "region=$location"
        ```

1.    Gewähren Sie der DiskEncryptionSet-Ressource Zugriff auf den Schlüsseltresor. 

        > [!NOTE]
        > Es kann einige Minuten dauern, bis Azure die Identität des Datenträgerverschlüsselungssatzes in Azure Active Directory erstellt hat. Wenn Sie bei der Ausführung des folgenden Befehls einen ähnlichen Fehler wie „Active Directory-Objekt kann nicht gefunden werden“ erhalten, warten Sie einige Minuten, und versuchen Sie es erneut.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie diese Ressourcen erstellt und konfiguriert haben, können Sie diese zum Sichern Ihrer verwalteten Datenträger verwenden. Unter den folgenden Links finden Sie Beispielskripts mit jeweils einem entsprechenden Szenario, die Sie zum Schützen Ihrer verwalteten Datenträger verwenden können.

- [Beispiele für Azure Resource Manager-Vorlagen](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Aktivieren kundenseitig verwalteter Schlüssel mit serverseitiger Verschlüsselung – Beispiele](disks-enable-customer-managed-keys-cli.md#examples)
