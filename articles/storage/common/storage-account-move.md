---
title: Verschieben eines Azure Storage-Kontos in eine andere Region | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie ein Azure Storage-Konto in eine andere Region verschieben können.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: b70beb90fae794eb5512cb8b466524169c4c7b53
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/29/2020
ms.locfileid: "92792989"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Verschieben eines Azure Storage-Kontos in eine andere Region

Erstellen Sie eine Kopie Ihres Speicherkontos in einer anderen Region, um ein Speicherkonto zu verschieben. Verschieben Sie dann Ihre Daten in dieses Konto, indem Sie AzCopy oder ein anderes Tool Ihrer Wahl verwenden.

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> 
> * Exportieren einer Vorlage.
> * Ändern der Vorlage, indem Sie die Zielregion und den Namen des Speicherkontos hinzufügen.
> * Bereitstellen der Vorlage zur Erstellung des neuen Speicherkontos.
> * Konfigurieren des neuen Speicherkontos.
> * Verschieben der Daten in das neue Speicherkonto.
> * Löschen der Ressourcen in der Quellregion.

## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass die Dienste und Features, die von Ihrem Konto verwendet werden, in der Zielregion unterstützt werden.

- Stellen Sie für Previewfunktionen sicher, dass Ihr Abonnement für die Zielregion auf der Whitelist steht.

<a id="prepare"></a>

## <a name="prepare"></a>Vorbereiten

Exportieren Sie zunächst eine Resource Manager-Vorlage und ändern Sie sie anschließend. 

### <a name="export-a-template"></a>Exportieren einer Vorlage

Diese Vorlage enthält Einstellungen, die Ihr Speicherkonto beschreiben. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

So exportieren Sie eine Vorlage mithilfe des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie die Option **Alle Ressourcen** und dann Ihr Speicherkonto aus.

3. Wählen Sie **Einstellungen** > **Vorlage exportieren** aus.

4. Wählen Sie **Herunterladen** auf dem Blatt **Vorlage exportieren** aus.

5. Suchen Sie die aus dem Portal heruntergeladene ZIP-Datei, und entpacken Sie sie in einen Ordner Ihrer Wahl.

   Diese ZIP-Datei enthält die JSON-Dateien, in denen die Vorlage und die Skripts zum Bereitstellen der Vorlage enthalten sind.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

So exportieren Sie eine Vorlage mithilfe von PowerShell

1. Melden Sie sich mit dem Befehl [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement auf das Abonnement des Speicherkontos fest, das Sie verschieben möchten.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportieren Sie die Vorlage Ihres Quellspeicherkontos. Diese Befehle speichern eine JSON-Vorlage in Ihrem aktuellen Verzeichnis.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>Ändern der Vorlage 

Ändern Sie die Vorlage, indem Sie den Namen und die Region des Speicherkontos ändern.

# <a name="portal"></a>[Portal](#tab/azure-portal)

So stellen Sie die Vorlage mithilfe des Azure-Portals bereit

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**.

2. Geben Sie in **Marketplace durchsuchen** den Begriff **Vorlagenbereitstellung** ein, und drücken Sie dann die **EINGABETASTE**.

3. Wählen Sie **Vorlagenbereitstellung** aus.

    ![Azure Resource Manager-Vorlagenbibliothek](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Klicken Sie auf **Erstellen**.

5. Wählen Sie **Eigene Vorlage im Editor erstellen**.

6. Wählen Sie **Datei laden** aus, und befolgen Sie dann die Anweisungen zum Laden der Datei **template.json**, die Sie im vorherigen Abschnitt heruntergeladen haben.

7. Benennen Sie in der Datei **template.json** das Zielspeicherkonto, indem Sie den Standardwert des Speicherkontonamens festlegen. In diesem Beispiel wird der Standardwert des Speicherkontonamens auf `mytargetaccount` festgelegt.
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    Informationen zum Abrufen von Regionsstandortcodes finden Sie unter [Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/).  Der Code für eine Region ist der Regionsname ohne Leerzeichen, **USA, Mitte** = **centralus**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

So stellen Sie die Vorlage mithilfe von PowerShell bereit

1. Benennen Sie in der Datei **template.json** das Zielspeicherkonto, indem Sie den Standardwert des Speicherkontonamens festlegen. In diesem Beispiel wird der Standardwert des Speicherkontonamens auf `mytargetaccount` festgelegt.
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. Bearbeiten Sie die **location**-Eigenschaft in der Datei **template.json** in der Zielregion. Dieses Beispiel legt den Zielbereich auf `eastus` fest.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Sie können Regionscodes erhalten, indem Sie den Befehl [Get-AzLocation](/powershell/module/az.resources/get-azlocation) ausführen.

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move"></a>

## <a name="move"></a>Move

Stellen Sie die Vorlage bereit, um ein neues Speicherkonto in der Zielregion zu erstellen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Speichern Sie die Datei **template.json**.

2. Geben Sie die Eigenschaftswerte ein oder wählen Sie sie aus:

- **Abonnement**: Wählen Sie ein Azure-Abonnement aus.

- **Ressourcengruppe**: Wählen Sie **Neu erstellen** aus, und benennen Sie die Ressourcengruppe.

- **Standort**: Wählen Sie einen Azure-Standort aus.

3. Klicken Sie auf das Kontrollkästchen **Ich stimme den oben genannten Geschäftsbedingungen zu** und dann auf die Schaltfläche **Kauf auswählen**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Rufen Sie die Abonnement-ID ab, für die Sie die öffentliche Ziel-IP-Adresse mit [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) bereitstellen möchten:

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Verwenden Sie diese Befehle, um Ihre Vorlage bereitzustellen:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Konfigurieren des neuen Speicherkontos

Einige Features werden nicht in eine Vorlage exportiert, daher müssen Sie sie dem neuen Speicherkonto hinzufügen. 

In der folgenden Tabelle sind diese Features zusammen mit einer Anleitung zum Hinzufügen zu Ihrem neuen Speicherkonto aufgeführt.

| Funktion    | Anleitungen    |
|--------|-----------|
| **Richtlinien für die Lebenszyklusverwaltung** | [Verwalten des Azure Blob Storage-Lebenszyklus](../blobs/storage-lifecycle-management-concepts.md) |
| **Statische Websites** | [Host a static website in Azure Storage](../blobs/storage-blob-static-website-how-to.md) (Hosten einer statischen Website in Azure Storage) |
| **Ereignisabonnements** | [Reacting to Blob storage events (preview)](../blobs/storage-blob-event-overview.md) (Reagieren auf Blob Storage-Ereignisse (Vorschauversion)) |
| **Warnungen** | [Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen mit Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [Verwenden von Azure CDN zum Zugreifen auf Blobs mit benutzerdefinierten Domänen über HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Wenn Sie ein CDN für das Quellspeicherkonto einrichten, ändern Sie einfach den Ursprung Ihres bestehenden CDNs in den primären Blobdienstendpunkt (oder den primären statischen Webseitenendpunkt) Ihres neuen Kontos. 

### <a name="move-data-to-the-new-storage-account"></a>Verschieben der Daten in das neue Speicherkonto

AzCopy ist das bevorzugte Tool zum Verschieben Ihrer Daten. Er ist für die Leistung optimiert.  Ein Grund für die höhere Leistung besteht darin, dass Daten direkt zwischen Speicherservern kopiert werden, sodass AzCopy nicht die Netzwerkbandbreite Ihres Computers nutzt. Verwenden Sie AzCopy über die Befehlszeile oder als Teil eines benutzerdefinierten Skripts. Informationen finden Sie unter [Übertragen von Daten mit AzCopy v10](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Sie können auch Azure Data Factory verwenden, um Ihre Daten zu verschieben. Die Anwendung bietet eine intuitive Benutzeroberfläche. Informationen zur Verwendung von Azure Data Factory finden Sie unter den folgenden Links: 

  - [Kopieren von Daten nach oder aus Azure Blob Storage mit Azure Data Factory](/azure/data-factory/connector-azure-blob-storage)
  - [Kopieren von Daten nach und aus Azure Data Lake Storage Gen2 mithilfe von Azure Data Factory](/azure/data-factory/connector-azure-data-lake-storage)
  - [Kopieren von Daten aus bzw. in Azure File Storage mithilfe von Azure Data Factory](/azure/data-factory/connector-azure-file-storage)
  - [Kopieren von Daten nach und aus Azure Table Storage mit Azure Data Factory](/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Verwerfen oder Bereinigen

Wenn Sie nach der Bereitstellung von vorne beginnen möchten, können Sie das Zielspeicherkonto löschen und die in den Abschnitten [Vorbereiten](#prepare) und [Verschieben](#move) dieses Artikels beschriebenen Schritte wiederholen.

Um die Änderungen zu übernehmen und das Verschieben eines Speicherkonto abzuschließen, löschen Sie das Quellspeicherkonto.

# <a name="portal"></a>[Portal](#tab/azure-portal)

So entfernen Sie ein Speicherkonto über das Azure-Portal

1. Erweitern Sie im Azure-Portal das Menü auf der linken Seite, um das Menü mit den Diensten zu öffnen, und klicken Sie auf **Speicherkonten**, um die Liste mit Ihren Speicherkonten anzuzeigen.

2. Suchen Sie das zu löschende Zielspeicherkonto, und klicken Sie mit der rechten Maustaste rechts neben der Liste auf die Schaltfläche **Mehr** ( **...** ).

3. Wählen Sie **Löschen** aus, und bestätigen Sie den Vorgang.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie den Befehl [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount), um die Ressourcengruppe und die zugeordneten Ressourcen (einschließlich des neuen Speicherkontos) zu entfernen:

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein Azure-Speicherkonto aus einer Region in eine andere verschoben und die Quellressourcen bereinigt.  Weitere Informationen zum Verschieben von Ressourcen zwischen Regionen und zur Notfallwiederherstellung in Azure finden Sie unter:


- [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Verschieben virtueller Azure-Computer in eine andere Region](../../site-recovery/azure-to-azure-tutorial-migrate.md)