---
title: Erstellen eines Blockblob-Speicherkontos – Azure Storage | Microsoft-Dokumentation
description: Hier wird gezeigt, wie Sie ein Azure-BlockBlobStorage-Konto mit Premium-Leistungsmerkmalen erstellen.
author: tamram
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/30/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 9350f9aeff90b75a4e1362f6fa2fa1b0d07f20cf
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95997085"
---
# <a name="create-a-blockblobstorage-account"></a>Erstellen eines BlockBlobStorage-Kontos

Mit der Kontoart BlockBlobStorage können Sie Blockblobs mit Premium-Leistungsmerkmalen erstellen. Diese Art von Speicherkonto ist für Workloads optimiert, die hohe Transaktionsraten aufweisen oder äußerst kurze Zugriffszeiten erfordern. In diesem Artikel wird gezeigt, wie Sie ein BlockBlobStorage-Konto über das Azure-Portal, mit der Azure CLI oder in Azure PowerShell erstellen.

Weitere Informationen zu BlockBlobStorage-Konten finden Sie unter [Azure-Speicherkonto – Übersicht](../common/storage-account-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Keine.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Für diese Anleitung wird Az-Version 1.2.0 oder höher des Azure PowerShell-Moduls benötigt. Führen Sie `Get-Module -ListAvailable Az` aus, um Ihre aktuelle Version zu ermitteln. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Install and configure Azure PowerShell](/powershell/azure/install-Az-ps) (Installieren des Azure PowerShell-Moduls) Informationen dazu.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sie können sich bei Azure anmelden und Azure-CLI-Befehle ausführen. Dazu haben Sie zwei Möglichkeiten:

- Sie können CLI-Befehle innerhalb des Azure-Portals in Azure Cloud Shell ausführen.
- Sie können die Befehlszeilenschnittstelle installieren und CLI-Befehle lokal ausführen.

### <a name="use-azure-cloud-shell"></a>Verwenden von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose Bash-Shell, die Sie direkt im Azure-Portal ausführen können. Die Azure-Befehlszeilenschnittstelle ist vorinstalliert und für die Verwendung mit Ihrem Konto konfiguriert. Klicken Sie im Azure-Portal im Menü im rechten oberen Bereich auf die Schaltfläche **Cloud Shell**:

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Die Schaltfläche öffnet eine interaktive Shell, mit der Sie die in diesem Artikel beschriebenen Schritte ausführen können:

[![Screenshot des Fensters „Cloud Shell“ im Portal](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Lokales Installieren der Befehlszeilenschnittstelle

Sie können die Azure-Befehlszeilenschnittstelle auch lokal installieren und verwenden. Für diese Anleitung müssen Sie mindestens Version 2.0.46 oder höher der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Authentifizierungsanweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Melden Sie sich zum Starten von Azure Cloud Shell beim [Azure-Portal](https://portal.azure.com) an.

Wenn Sie sich bei Ihrer lokalen Installation der Befehlszeilenschnittstelle anmelden möchten, führen Sie den Befehl [az login](/cli/azure/reference-index#az-login) aus:

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Erstellen eines BlockBlobStorage-Kontos

## <a name="portal"></a>[Portal](#tab/azure-portal)
Wenn Sie ein BlockBlobStorage-Konto über das Azure-Portal erstellen möchten, gehen Sie wie folgt vor:

1. Wählen Sie im Azure-Portal **Alle Dienste** > die Kategorie **Storage** > **Speicherkonten** aus.

2. Wählen Sie unter **Speicherkonten** die Option **Hinzufügen** aus.

3. Wählen Sie im Feld **Abonnement** das Abonnement aus, in dem das Speicherkonto erstellt werden soll.

4. Wählen Sie im Feld **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für die neue Ressourcengruppe ein.

5. Geben Sie im Feld **Speicherkontoname** einen Namen für das Konto ein. Beachten Sie die folgenden Richtlinien:

   - Der Name muss innerhalb von Azure eindeutig sein.
   - Der Name muss zwischen drei und 24 Zeichen lang sein.
   - Der Name darf nur Ziffern und Kleinbuchstaben enthalten.

6. Wählen Sie im Feld **Standort** einen Standort für das Speicherkonto aus, oder verwenden Sie den Standardstandort.

7. Konfigurieren Sie für die übrigen Einstellungen die folgenden Werte:

   |Feld     |Wert  |
   |---------|---------|
   |**Leistung**    |  Wählen Sie **Premium** aus.   |
   |**Kontoart**    | Wählen Sie **BlockBlobStorage** aus.      |
   |**Replikation**    |  Behalten Sie die Standardeinstellung **Lokal redundanter Speicher (LRS)** bei.      |

   ![Portal-Benutzeroberfläche zum Erstellen eines Blockblob-Speicherkontos](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

8. Wählen Sie die Registerkarte **Erweitert** aus.

9. Wenn Sie Ihr Speicherkonto für Datenanalyse optimieren möchten, legen Sie **Hierarchischer Namespace** auf **Aktiviert** fest. Andernfalls belassen Sie den Standardwert für diese Option. Wenn Sie diese Einstellung mit Ihrem BlockBlobStorage-Konto aktivieren, erhalten Sie den [Premium-Tarif für Data Lake Storage](premium-tier-for-data-lake-storage.md).  Weitere Informationen zu Data Lake Storage finden Sie in der [Einführung in Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

8. Wählen Sie **Überprüfen + erstellen** aus, um die Speicherkontoeinstellungen zu überprüfen.

9. Klicken Sie auf **Erstellen**.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten („Als Administrator ausführen“).

2. Führen Sie den folgenden Befehl aus, um sicherzustellen, dass die neueste Version des `Az` PowerShell-Moduls installiert ist.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

3. Öffnen Sie eine neue PowerShell-Konsole, und melden Sie sich mit Ihrem Azure-Konto an.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

4. Erstellen Sie bei Bedarf eine neue Ressourcengruppe. Ersetzen Sie die Werte in Anführungszeichen, und führen Sie den folgenden Befehl aus.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

5. Erstellen Sie das BlockBlobStorage-Konto. Ersetzen Sie die Werte in Anführungszeichen, und führen Sie den folgenden Befehl aus.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```
   Wenn Sie Ihr Speicherkonto für Datenanalyse optimieren möchten, fügen Sie dem Befehl `-EnableHierarchicalNamespace $True` hinzu. Wenn Sie diese Einstellung mit Ihrem BlockBlobStorage-Konto aktivieren, erhalten Sie den [Premium-Tarif für Data Lake Storage](premium-tier-for-data-lake-storage.md).  Weitere Informationen zu Data Lake Storage finden Sie in der [Einführung in Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

## <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Zum Erstellen eines Blockblob-Speicherkontos mithilfe der Azure-Befehlszeilenschnittstelle müssen Sie zunächst die Azure-Befehlszeilenschnittstelle, Version 2.0.46 oder eine höhere Version, installieren. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

1. Melden Sie sich bei Ihrem Azure-Abonnement an.

   ```azurecli
   az login
   ```

2. Erstellen Sie bei Bedarf eine neue Ressourcengruppe. Ersetzen Sie die Werte in Klammern (einschließlich der Klammern), und führen Sie den folgenden Befehl aus.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

3. Erstellen Sie das BlockBlobStorage-Konto. Ersetzen Sie die Werte in Klammern (einschließlich der Klammern), und führen Sie den folgenden Befehl aus.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

   Wenn Sie Ihr Speicherkonto für Datenanalyse optimieren möchten, fügen Sie dem Befehl `--hierarchical-namespace true` hinzu. Wenn Sie diese Einstellung mit Ihrem BlockBlobStorage-Konto aktivieren, erhalten Sie den [Premium-Tarif für Data Lake Storage](premium-tier-for-data-lake-storage.md).  Weitere Informationen zu Data Lake Storage finden Sie in der [Einführung in Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

---

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Speicherkonten finden Sie unter [Azure-Speicherkonto – Übersicht](../common/storage-account-overview.md).

- Weitere Informationen zu Ressourcengruppen finden Sie unter [Azure Resource Manager – Übersicht](../../azure-resource-manager/management/overview.md).