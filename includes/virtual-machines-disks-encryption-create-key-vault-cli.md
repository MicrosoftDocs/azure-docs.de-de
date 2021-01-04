---
title: include file
description: include file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/15/2020
ms.author: rogarana
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 5c17c1b7fed50c311d356aadcb7ca2837cc20abd
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92741736"
---
1. Achten Sie darauf, dass Sie die neueste Version der [Azure CLI](/cli/azure/install-az-cli2) installiert haben und mit [az login](/cli/azure/reference-index) bei einem Azure-Konto angemeldet sind.

1. Erstellen Sie eine Azure Key Vault-Instanz und den Verschlüsselungsschlüssel.

    Beim Erstellen der Key Vault-Instanz müssen Sie vorläufiges Löschen und den Schutz vor endgültigem Löschen aktivieren. Durch vorläufiges Löschen wird sichergestellt, dass der Schlüsseltresor einen gelöschten Schlüssel für einen bestimmten Aufbewahrungszeitraum (standardmäßig 90 Tage) speichert. Der Schutz vor endgültigem Löschen stellt sicher, dass ein gelöschter Schlüssel erst nach Ablauf der Aufbewahrungsdauer dauerhaft gelöscht werden kann. Diese Einstellungen schützen Sie vor dem Verlust von Daten durch versehentliches Löschen. Diese Einstellungen sind obligatorisch, wenn ein Schlüsseltresor für die Verschlüsselung verwalteter Datenträger verwendet wird.

    > [!IMPORTANT]
    > Verwenden Sie keine gemischte Groß-/Kleinschreibung für die Region, da ansonsten Probleme beim Zuweisen zusätzlicher Datenträger zur Ressource im Azure-Portal auftreten.

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

1.    Erstellen Sie eine DiskEncryptionSet-Instanz. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    Gewähren Sie der DiskEncryptionSet-Ressource Zugriff auf den Schlüsseltresor. 

        > [!NOTE]
        > Es kann einige Minuten dauern, bis Azure die Identität des Datenträgerverschlüsselungssatzes in Azure Active Directory erstellt hat. Wenn Sie bei der Ausführung des folgenden Befehls einen ähnlichen Fehler wie „Active Directory-Objekt kann nicht gefunden werden“ erhalten, warten Sie einige Minuten, und versuchen Sie es erneut.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```
