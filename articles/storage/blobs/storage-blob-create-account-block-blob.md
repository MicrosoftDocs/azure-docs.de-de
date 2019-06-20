---
title: Erstellen eines Blockblob-Speicherkontos – Azure Storage | Microsoft-Dokumentation
description: Hier wird gezeigt, wie Sie ein Azure-Blockblob-Speicherkonto mit Premium-Leistungsmerkmalen erstellen.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9d8fb8f5f470dc47088efb30b7f823a0b8c624c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65141002"
---
# <a name="create-a-block-blob-storage-account"></a>Erstellen eines Blockblob-Speicherkontos

Mit dem Blockblob-Speicherkontotyp können Sie Blockblobs mit Premium-Leistungsmerkmalen erstellen. Diese Art von Speicherkonto ist für Workloads optimiert, die hohe Transaktionsraten aufweisen oder äußerst kurze Zugriffszeiten erfordern. In diesem Artikel wird gezeigt, wie Sie ein Blockblob-Speicherkonto über das Azure-Portal, mit der Azure-Befehlszeilenschnittstelle oder in Azure PowerShell erstellen.

Weitere Informationen zu Blockblob-Speicherkonten finden Sie unter [Azure-Speicherkonto – Übersicht](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="create-account-in-the-azure-portal"></a>Erstellen eines Kontos über das Azure-Portal

Wenn Sie ein Blockblob-Speicherkonto über das Azure-Portal erstellen möchten, gehen Sie wie folgt vor:

1. Wählen Sie im Azure-Portal **Alle Dienste** > die Kategorie **Storage** > **Speicherkonten** aus.

1. Wählen Sie unter **Speicherkonten** die Option **Hinzufügen** aus.

1. Wählen Sie im Feld **Abonnement** das Abonnement aus, in dem das Speicherkonto erstellt werden soll.

1. Wählen Sie im Feld **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für die neue Ressourcengruppe ein.

1. Geben Sie im Feld **Speicherkontoname** einen Namen für das Konto ein. Beachten Sie die folgenden Richtlinien:

   - Der Name muss innerhalb von Azure eindeutig sein.
   - Der Name muss zwischen drei und 24 Zeichen lang sein.
   - Der Name darf nur Ziffern und Kleinbuchstaben enthalten.

1. Wählen Sie im Feld **Standort** einen Standort für das Speicherkonto aus, oder verwenden Sie den Standardstandort.

1. Konfigurieren Sie für die übrigen Einstellungen die folgenden Werte:

   |Feld     |Wert  |
   |---------|---------|
   |**Leistung**    |  Wählen Sie **Premium** aus.   |
   |**Kontoart**    | Wählen Sie **BlockBlobStorage** aus.      |
   |**Replikation**    |  Behalten Sie die Standardeinstellung **Lokal redundanter Speicher (LRS)** bei.      |

   ![Portal-Benutzeroberfläche zum Erstellen eines Blockblob-Speicherkontos](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Wählen Sie **Überprüfen + erstellen** aus, um die Speicherkontoeinstellungen zu überprüfen.

1. Klicken Sie auf **Erstellen**.

## <a name="create-account-using-azure-powershell"></a>Erstellen eines Kontos mithilfe von Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten („Als Administrator ausführen“).

1. Führen Sie den folgenden Befehl aus, um sicherzustellen, dass die neueste Version des `Az` PowerShell-Moduls installiert ist.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Öffnen Sie eine neue PowerShell-Konsole, und melden Sie sich mit Ihrem Azure-Konto an.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Erstellen Sie bei Bedarf eine neue Ressourcengruppe. Ersetzen Sie die Werte in Anführungszeichen, und führen Sie den folgenden Befehl aus.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Erstellen Sie das Blockblob-Speicherkonto. Ersetzen Sie die Werte in Anführungszeichen, und führen Sie den folgenden Befehl aus.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="create-account-using-azure-cli"></a>Erstellen eines Kontos mithilfe der Azure-Befehlszeilenschnittstelle

Zum Erstellen eines Blockblob-Speicherkontos mithilfe der Azure-Befehlszeilenschnittstelle müssen Sie zunächst die Azure-Befehlszeilenschnittstelle, Version 2.0.46 oder eine höhere Version, installieren. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

1. Melden Sie sich bei Ihrem Azure-Abonnement an.

   ```azurecli
   az login
   ```

1. Erstellen Sie bei Bedarf eine neue Ressourcengruppe. Ersetzen Sie die Werte in Klammern (einschließlich der Klammern), und führen Sie den folgenden Befehl aus.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Erstellen Sie das Blockblob-Speicherkonto. Ersetzen Sie die Werte in Klammern (einschließlich der Klammern), und führen Sie den folgenden Befehl aus.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Speicherkonten finden Sie unter [Azure-Speicherkonto – Übersicht](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Weitere Informationen zu Ressourcengruppen finden Sie unter [Azure Resource Manager – Übersicht](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
