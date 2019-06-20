---
title: Konfigurieren von Kunden verwalteter Schlüssel für die Azure Storage-Verschlüsselung mithilfe der Azure CLI
description: Erfahren Sie, wie Sie die Azure CLI verwenden, um von Kunden verwaltete Schlüssel für die Azure Storage-Verschlüsselung zu konfigurieren. Mit von Kunden verwalteten Schlüsseln können Sie Zugriffssteuerungen erstellen, rotieren, deaktivieren und widerrufen.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ca2cfc9369fd6fb001b2a2dc401c33e5ddfd38c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65142904"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Konfigurieren von Kunden verwalteter Schlüssel für die Azure Storage-Verschlüsselung mithilfe der Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

In diesem Artikel erfahren Sie, wie Sie einen Schlüsseltresor mit von Kunden verwalteten Schlüsseln mithilfe der Azure CLI konfigurieren.

## <a name="assign-an-identity-to-the-storage-account"></a>Zuweisen einer Identität zum Speicherkonto

Sie müssen zunächst eine systemseitig zugewiesene verwaltete Identität zum Speicherkonto hinzufügen, um von Kunden verwaltete Schlüssel für Ihr Speicherkonto zu aktivieren. Sie verwenden diese Identität, um dem Speicherkonto die Zugriffsberechtigungen für den Schlüsseltresor zu gewähren.

Rufen Sie [az storage account update](/cli/azure/storage/account#az-storage-account-update) auf, um eine verwaltete Identität mit der Azure CLI zuzuweisen. Denken Sie daran, die Platzhalterwerte in den spitzen Klammern durch Ihre eigenen Werte zu ersetzen.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Weitere Informationen über die Konfiguration systemseitig zugewiesener verwalteter Identitäten mit der Azure CLI finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe der Azure-Befehlszeilenschnittstelle](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Erstellen eines neuen Schlüsseltresors

Für den Schlüsseltresor, den Sie zum Speichern von Kunden verwalteter Schlüssel für die Azure Storage-Verschlüsselung verwenden, müssen zwei wichtige Schutzeinstellungen aktiviert sein: **Vorläufiges Löschen** und **Do Not Purge** (Nicht bereinigen). Führen Sie die folgenden Befehle aus, um mithilfe von PowerShell oder der Azure CLI einen neuen Schlüsseltresor zu erstellen, für den diese Einstellungen aktiviert sind. Denken Sie daran, die Platzhalterwerte in den spitzen Klammern durch Ihre eigenen Werte zu ersetzen. 

Rufen Sie [az keyvault create](/cli/azure/keyvault#az-keyvault-create) auf, um einen neuen Schlüsseltresor mit der Azure CLI zu erstellen. Denken Sie daran, die Platzhalterwerte in den spitzen Klammern durch Ihre eigenen Werte zu ersetzen.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurieren der Zugriffsrichtlinie für den Schlüsseltresor

Konfigurieren Sie als Nächstes die Zugriffsrichtlinie für den Schlüsseltresor, sodass das Speicherkonto über die Berechtigungen für den Zugriff auf diesen verfügt. In diesem Schritt verwenden Sie die verwaltete Identität, die Sie zuvor dem Speicherkonto zugewiesen haben.

Rufen Sie [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) auf, um die Zugriffsrichtlinie für den Schlüsseltresor festzulegen. Denken Sie daran, die Platzhalterwerte in den spitzen Klammern durch Ihre eigenen Werte zu ersetzen.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    -name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Erstellen eines neuen Schlüssels

Als Nächstes erstellen Sie einen Schlüssel im Schlüsseltresor. Rufen Sie hierzu den Befehl [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create) auf. Denken Sie daran, die Platzhalterwerte in den spitzen Klammern durch Ihre eigenen Werte zu ersetzen.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurieren der Verschlüsselung mit von Kunden verwalteten Schlüsseln

Die Azure Storage-Verschlüsselung verwendet standardmäßig von Microsoft verwaltete Schlüssel. Konfigurieren Sie Ihr Azure Storage-Konto für von Kunden verwaltete Schlüssel, und legen Sie fest, dass der Schlüssel dem Speicherkonto zugeordnet ist.

Rufen Sie [az storage account update](/cli/azure/storage/account#az-storage-account-update) auf, um die Verschlüsselungseinstellungen der Speicherkontos zu aktualisieren. In diesem Beispiel werden außerdem der Schlüsseltresor-URI und die Schlüsselversion abgefragt. Beide dieser Werte sind erforderlich, um den Schlüssel dem Speicherkonto zuzuordnen. Denken Sie daran, die Platzhalterwerte in den spitzen Klammern durch Ihre eigenen Werte zu ersetzen.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>Aktualisieren der Schlüsselversion

Wenn Sie eine neue Version eines Schlüssels erstellen, müssen Sie das Speicherkonto aktualisieren, damit dieses die neue Version nutzt. Rufen Sie zunächst [az keyvault show](/cli/azure/keyvault#az-keyvault-show) zum Abfragen des Schlüsseltresor-URIs und [az keyvault key list-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions) zum Abfragen der Schlüsselversion auf. Rufen Sie dann [az storage account update](/cli/azure/storage/account#az-storage-account-update) auf, um die Verschlüsselungseinstellungen des Speicherkontos zu aktualisieren, damit wie im vorherigen Abschnitt gezeigt die neue Version des Schlüssels verwendet wird.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](storage-service-encryption.md) 
- [What is Azure Key Vault? (Was ist Azure Key Vault?)](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)
