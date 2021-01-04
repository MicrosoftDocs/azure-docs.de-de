---
title: Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault Managed HSM (Vorschau) gespeichert sind
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie die Azure Storage-Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault Managed HSM (Vorschau) gespeichert sind, über die Azure-Befehlszeilenschnittstelle konfiguriert wird.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/21/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 2f57e801720c6b546a58b216422629d192e8d2e6
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843314"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault Managed HSM (Vorschau) gespeichert sind

Azure Storage verschlüsselt alle Daten in einem ruhenden Speicherkonto. Standardmäßig werden Daten mit von Microsoft verwalteten Schlüsseln verschlüsselt. Um zusätzliche Kontrolle über die Verschlüsselungsschlüssel zu erhalten, können Sie eigene Schlüssel verwalten. Kundenseitig verwaltete Schlüssel müssen in Azure Key Vault oder Key Vault Managed Hardware Security Model (HSM) (Vorschau) gespeichert werden. Ein Azure Key Vault Managed HSM ist ein HSM mit FIPS 140-2 Level 3-Zertifizierung.

In diesem Artikel erfahren Sie, wie die Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in einem verwalteten HSM gespeichert sind, mithilfe der Azure-Befehlszeilenschnittstelle konfiguriert werden. Informationen zum Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in einem Schlüsseltresor gespeichert sind, finden Sie unter [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault gespeichert sind](customer-managed-keys-configure-key-vault.md).

> [!NOTE]
> Azure Key Vault und Azure Key Vault Managed HSM unterstützen dieselben APIs und Verwaltungsschnittstellen für die Konfiguration.

## <a name="assign-an-identity-to-the-storage-account"></a>Zuweisen einer Identität zum Speicherkonto

Weisen Sie zunächst dem Speicherkonto eine systemseitig zugewiesene verwaltete Identität zu. Diese Identität wird verwendet, um dem Speicherkonto die Zugriffsberechtigungen für das verwaltete Hardwaresicherheitsmodul (HSM) zu gewähren. Weitere Informationen zu systemseitig zugewiesenen verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../active-directory/managed-identities-azure-resources/overview.md).

Rufen Sie [az storage account update](/cli/azure/storage/account#az-storage-account-update) auf, um eine verwaltete Identität mit der Azure CLI zuzuweisen. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>Zuweisen einer Rolle zum Speicherkonto für den Zugriff auf das verwaltete HSM

Weisen Sie als Nächstes der verwalteten Identität des Speicherkontos die Rolle **Managed HSM Crypto Service Encryption** (Kryptografiedienstverschlüsselung für verwaltete HSMs) zu, sodass das Speicherkonto über Berechtigungen für das verwaltete HSM verfügt. Microsoft empfiehlt, die Rollenzuweisung auf die Ebene des einzelnen Schlüssels festzulegen, sodass der verwalteten Identität nur die notwendigsten Berechtigungen erteilt werden.

Um die Rollenzuweisung für das Speicherkonto zu erstellen, rufen Sie [az keyvault role assignment create](/cli/azure/role/assignment#az_role_assignment_create) auf. Denken Sie daran, die Platzhalterwerte in den spitzen Klammern durch Ihre eigenen Werte zu ersetzen.
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>Konfigurieren der Verschlüsselung mit einem Schlüssel im verwalteten HSM

Konfigurieren Sie abschließend die Azure Storage-Verschlüsselung mit kundenseitig verwalteten Schlüsseln für die Verwendung eines im verwalteten HSM gespeicherten Schlüssels. Folgende Schlüsseltypen werden unterstützt: RSA-HSM-Schlüssel der Größen 2048, 3072 und 4096. Installieren Sie die Azure CLI 2.12.0 oder höher, um die Verschlüsselung für die Verwendung eines kundenseitig verwalteten Schlüssels in einem verwalteten HSM zu konfigurieren. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

Um die Schlüsselversion für einen kundenseitig verwalteten Schlüssel automatisch zu aktualisieren, lassen Sie die Schlüsselversion aus, wenn Sie die Verschlüsselung mit kundenseitig verwalteten Schlüsseln für das Speicherkonto konfigurieren. Rufen Sie [az storage account update](/cli/azure/storage/account#az_storage_account_update) wie im folgenden Beispiel gezeigt auf, um die Verschlüsselungseinstellungen des Speicherkontos zu aktualisieren. Fügen Sie den Parameter `--encryption-key-source parameter` ein, und legen Sie ihn auf `Microsoft.Keyvault` fest, um kundenseitig verwaltete Schlüssel für das Speicherkonto zu aktivieren. Denken Sie daran, die Platzhalterwerte in den spitzen Klammern durch Ihre eigenen Werte zu ersetzen.

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Um die Version für einen kundenseitig verwalteten Schlüssel manuell zu aktualisieren, beziehen Sie die Schlüsselversion ein, wenn Sie die Verschlüsselung mit kundenseitig verwalteten Schlüsseln für das Speicherkonto konfigurieren.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Wenn Sie die Schlüsselversion manuell aktualisieren, müssen Sie die Verschlüsselungseinstellungen des Speicherkontos aktualisieren, damit die neue Version verwendet wird. Rufen Sie zunächst [az keyvault show](/cli/azure/keyvault#az-keyvault-show) zum Abfragen des Schlüsseltresor-URIs und [az keyvault key list-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions) zum Abfragen der Schlüsselversion auf. Rufen Sie dann [az storage account update](/cli/azure/storage/account#az-storage-account-update) auf, um die Verschlüsselungseinstellungen des Speicherkontos zu aktualisieren, damit wie im vorherigen Beispiel gezeigt die neue Version des Schlüssels verwendet wird.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](storage-service-encryption.md)
- [Kundenseitig verwaltete Schlüssel für die Azure Storage-Verschlüsselung](customer-managed-keys-overview.md)
