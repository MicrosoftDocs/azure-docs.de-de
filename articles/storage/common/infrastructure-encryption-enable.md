---
title: Erstellen eines Speicherkontos mit aktivierter Infrastrukturverschlüsselung für die doppelte Datenverschlüsselung
titleSuffix: Azure Storage
description: Kunden, die besonders sicher sein müssen, dass Ihre Daten sicher sind, können auch 256-Bit-AES-Verschlüsselung auf Azure Storage-Infrastrukturebene aktivieren. Wenn die Infrastrukturverschlüsselung aktiviert ist, werden Daten in einem Speicherkonto zweimal mit zwei unterschiedlichen Verschlüsselungsalgorithmen und zwei verschiedenen Schlüsseln verschlüsselt.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 612ba18ba71a22ad6c346b26008e688195c1d1e4
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746582"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Erstellen eines Speicherkontos mit aktivierter Infrastrukturverschlüsselung für die doppelte Datenverschlüsselung

Alle Daten werden in Azure Storage automatisch in einem Speicherkonto auf dem Servicelevel mit der 256-Bit-AES-Verschlüsselung verschlüsselt, einer der stärksten verfügbaren Blockchiffren, und konform mit dem FIPS 140-2-Standard. Kunden, die besonders sicher sein müssen, dass Ihre Daten sicher sind, können auch 256-Bit-AES-Verschlüsselung auf Azure Storage-Infrastrukturebene aktivieren. Wenn die Infrastrukturverschlüsselung aktiviert ist, werden Daten in einem Speicherkonto zweimal &mdash; einmal auf dem Servicelevel und einmal auf der Infrastrukturebene &mdash; mit zwei unterschiedlichen Verschlüsselungsalgorithmen und zwei verschiedenen Schlüsseln verschlüsselt. Die doppelte Verschlüsselung von Azure Storage-Daten schützt vor dem Szenario, dass einer der Verschlüsselungsalgorithmen oder Schlüssel kompromittiert wurde. In diesem Szenario werden die Daten weiterhin durch die zusätzliche Verschlüsselungsebene geschützt.

Die Verschlüsselung auf dem Servicelevel unterstützt die Verwendung von Schlüsseln, die von Microsoft oder vom Kunden verwaltet werden, mit Azure Key Vault oder Azure Key Vault Managed HSM (Hardware Security Module, Vorschau). Die Verschlüsselung auf Infrastrukturebene basiert auf von Microsoft verwalteten Schlüsseln und verwendet immer einen separaten Schlüssel. Weitere Informationen zur Schlüsselverwaltung mit Azure Storage-Verschlüsselung finden Sie unter [Informationen zur Verwaltung von Verschlüsselungsschlüsseln](storage-service-encryption.md#about-encryption-key-management).

Um Ihre Daten doppelt zu verschlüsseln, müssen Sie zunächst ein Speicherkonto erstellen, das für die Infrastrukturverschlüsselung konfiguriert ist. In diesem Artikel wird beschrieben, wie Sie ein Speicherkonto erstellen, das die Infrastrukturverschlüsselung ermöglicht.

## <a name="register-to-use-infrastructure-encryption"></a>Registrieren zur Verwendung der Infrastrukturverschlüsselung

Um ein Speicherkonto zu erstellen, für das die Infrastrukturverschlüsselung aktiviert ist, müssen Sie sich zunächst für die Verwendung dieses Features in Azure mit PowerShell oder der Azure-Befehlszeilenschnittstelle registrieren.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

–

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Rufen Sie für die Registrierung mit PowerShell den Befehl [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) auf.

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Um den Status Ihrer Registrierung mit PowerShell zu überprüfen, rufen Sie den Befehl [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) auf.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Nachdem Ihre Registrierung genehmigt wurde, müssen Sie den Azure Storage-Ressourcenanbieter erneut registrieren. Um den Ressourcenanbieter über PowerShell erneut zu registrieren, rufen Sie den Befehl [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) auf.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Zum Registrieren bei Azure CLI rufen Sie den Befehl „[az feature register](/cli/azure/feature#az-feature-register)“ auf.

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Um den Status Ihrer Registrierung über die Azure-Befehlszeilenschnittstelle zu überprüfen, rufen Sie den Befehl [az feature](/cli/azure/feature#az-feature-show) auf.

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Nachdem Ihre Registrierung genehmigt wurde, müssen Sie den Azure Storage-Ressourcenanbieter erneut registrieren. Um den Ressourcenanbieter über die Azure-Befehlszeilenschnittstelle erneut zu registrieren, rufen Sie den Befehl [az provider register](/cli/azure/provider#az-provider-register) auf.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Vorlage](#tab/template)

–

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Erstellen eines Kontos mit aktivierter Infrastrukturverschlüsselung

Sie müssen ein Speicherkonto konfigurieren, um die Infrastrukturverschlüsselung zu dem Zeitpunkt zu verwenden, zu dem Sie das Konto erstellen. Das Speicherkonto muss vom Typ „Allgemein v2 (GPv2)“ sein.

Die Infrastrukturverschlüsselung kann nicht aktiviert oder deaktiviert werden, nachdem das Konto erstellt wurde.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um mit PowerShell ein Speicherkonto mit aktivierter Infrastrukturverschlüsselung zu erstellen:

1. Navigieren Sie im Azure-Portal zur Seite **Speicherkonten** .
1. Wählen Sie die Schaltfläche **Hinzufügen** aus, um ein neues Speicherkonto vom Typ „Universell v2“ hinzuzufügen.
1. Suchen Sie auf der Registerkarte **Erweitert** nach dem Verschlüsselungstyp **Infrastruktur** , und wählen Sie **Aktiviert** aus.
1. Wählen Sie **Überprüfen + erstellen** aus, um das Erstellen des Speicherkontos abzuschließen.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="Screenshot der Aktivierung der Infrastrukturverschlüsselung beim Erstellen eines Kontos":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie über PowerShell ein Speicherkonto mit aktivierter Infrastrukturverschlüsselung erstellen möchten, muss das [Az.Storage-PowerShell-Modul](https://www.powershellgallery.com/packages/Az.Storage) Version 2.2.0 oder höher installiert sein. Weitere Informationen finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/install-az-ps).

Erstellen Sie als Nächstes ein Speicherkonto vom Typ „Universell v2 (GPv2)“, indem Sie den Befehl [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) aufrufen. Beziehen Sie die `-RequireInfrastructureEncryption`-Option zum Aktivieren der Infrastrukturverschlüsselung ein.

Das folgende Beispiel zeigt, wie Sie ein Speicherkonto vom Typ „Universell v2“ erstellen, das für georedundanten Speicher mit Lesezugriff (Read-Access Geo-Redundant Storage, RA-GRS) konfiguriert ist und für das die Infrastrukturverschlüsselung zum doppelten Verschlüsseln von Daten aktiviert ist. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Wenn Sie mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) ein Speicherkonto erstellen möchten, für das die Infrastrukturverschlüsselung aktiviert ist, müssen Sie Azure CLI, Version 2.8.0 oder höher, installiert haben. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

Erstellen Sie als Nächstes ein Speicherkonto vom Typ „Allgemein v2 (GPv2)“, indem Sie den Befehl [az storage account create](/cli/azure/storage/account#az-storage-account-create) mit der `--require-infrastructure-encryption option` aufrufen, um die Infrastrukturverschlüsselung zu aktivieren.

Das folgende Beispiel zeigt, wie Sie ein Speicherkonto vom Typ „Universell v2“ erstellen, das für georedundanten Speicher mit Lesezugriff (Read-Access Geo-Redundant Storage, RA-GRS) konfiguriert ist und für das die Infrastrukturverschlüsselung zum doppelten Verschlüsseln von Daten aktiviert ist. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[Vorlage](#tab/template)

Im folgenden JSON-Beispiel wird ein Speicherkonto vom Typ „Universell v2“ erstellt, das für georedundanten Speicher mit Lesezugriff (Read-Access Geo-Redundant Storage, RA-GRS) konfiguriert ist und für das die Infrastrukturverschlüsselung zum doppelten Verschlüsseln von Daten aktiviert ist. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>Überprüfen, ob die Infrastrukturverschlüsselung aktiviert ist

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Führen Sie die folgenden Schritte im Azure-Portal aus, um zu überprüfen, ob die Infrastrukturverschlüsselung für ein Speicherkonto aktiviert ist:

1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Wählen Sie unter **Einstellungen** die Option **Verschlüsselung** aus.

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="Screenshot der Aktivierung der Infrastrukturverschlüsselung beim Erstellen eines Kontos":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um mit PowerShell zu überprüfen, ob die Infrastrukturverschlüsselung für ein Speicherkonto aktiviert ist, rufen Sie den Befehl [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) auf. Dieser Befehl gibt einen Satz von Speicherkontoeigenschaften und deren Werte zurück. Rufen Sie das Feld `RequireInfrastructureEncryption` innerhalb der Eigenschaft `Encryption` ab, und überprüfen Sie, ob es auf `True` festgelegt ist.

Im folgenden Beispiel wird der Wert der `RequireInfrastructureEncryption`-Eigenschaft abgerufen. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um mit der Azure-Befehlszeilenschnittstelle zu überprüfen, ob die Infrastrukturverschlüsselung für ein Speicherkonto aktiviert ist, rufen Sie den Befehl [az storage account show](/cli/azure/storage/account#az-storage-account-show) auf. Dieser Befehl gibt einen Satz von Speicherkontoeigenschaften und deren Werte zurück. Überprüfen Sie, ob das Feld `requireInfrastructureEncryption` innerhalb der Eigenschaft `encryption` auf `true` festgelegt ist.

Im folgenden Beispiel wird der Wert der `requireInfrastructureEncryption`-Eigenschaft abgerufen. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Vorlage](#tab/template)

–

---

## <a name="next-steps"></a>Nächste Schritte

- [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](storage-service-encryption.md)
- [Kundenseitig verwaltete Schlüssel für die Azure Storage-Verschlüsselung](customer-managed-keys-overview.md)
