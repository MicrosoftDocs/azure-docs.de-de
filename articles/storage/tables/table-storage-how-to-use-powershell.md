---
title: Ausführen von Azure Table Storage-Vorgängen mit PowerShell | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie häufige Aufgaben wie das Erstellen, Abfragen und Löschen von Daten im Azure Table Storage-Konto mithilfe von PowerShell ausführen.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9a7502d48095fe18b983c1971d5145f51e766c95
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93306908"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Ausführen von Azure Table Storage-Vorgängen mit Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure Table Storage ist ein NoSQL-Datenspeicher, den Sie zum Speichern und Abfragen umfangreicher strukturierter, nicht relationaler Datasets verwenden können. Die Hauptkomponenten des Diensts sind Tabellen, Entitäten und Eigenschaften. Eine Tabelle ist eine Sammlung von Entitäten. Eine Entität ist ein Satz von Eigenschaften. Jede Entität kann über bis zu 252 Eigenschaften verfügen, bei denen es sich um Name-Wert-Paare handelt. In diesem Artikel wird vorausgesetzt, dass Sie bereits mit den Konzepten des Azure Table Storage-Diensts vertraut sind. Ausführliche Informationen finden Sie unter [Understanding the Table Service Data Model](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) (Grundlegendes zum Tabellenspeicherdienst-Datenmodell) und [Erste Schritte mit Azure Table Storage mit .NET](../../cosmos-db/tutorial-develop-table-dotnet.md).

In dieser Anleitung werden gängige Azure Table Storage-Vorgänge behandelt. Folgendes wird vermittelt: 

> [!div class="checklist"]
> * Erstellen einer Tabelle
> * Abrufen einer Tabelle
> * Hinzufügen von Tabellenentitäten
> * Abfragen einer Tabelle
> * Löschen von Tabellenentitäten
> * Löschen einer Tabelle

In dieser Anleitung erfahren Sie, wie Sie ein neues Azure Storage-Konto in einer neuen Ressourcengruppe erstellen, um es ganz einfach entfernen zu können, wenn Sie es nicht mehr benötigen. Sie können aber auch ein bereits vorhandenes Storage-Konto verwenden, wenn Ihnen das lieber ist.

Für die Beispiele werden die Azure PowerShell-Module `Az.Storage (1.1.0 or greater)` und `Az.Resources (1.2.0 or greater)` benötigt. Führen Sie in einem PowerShell-Fenster `Get-Module -ListAvailable Az*` aus, um die Version zu ermitteln. Sollte nichts angezeigt werden oder ein Upgrade erforderlich sein, lesen Sie die Informationen unter [Install and configure Azure PowerShell](/powershell/azure/install-az-ps) (Installieren und Konfigurieren von Azure PowerShell).

> [!IMPORTANT]
> Die Verwendung dieser Azure-Funktion mit PowerShell erfordert, dass Sie das `Az`-Modul installiert haben. Die aktuelle Version von `AzTable` ist mit dem älteren AzureRM-Modul nicht kompatibel.
> Folgen Sie bei Bedarf den [neuesten Installationsanweisungen für die Installation des Az-Moduls](/powershell/azure/install-az-ps).

Nach dem Installieren oder Aktualisieren von Azure PowerShell muss das Modul **AzTable** installiert werden. Es enthält die Befehle zum Verwalten der Entitäten. Führen Sie zum Installieren des Moduls PowerShell als Administrator aus, und verwenden Sie den Befehl **Install-Module**.

> [!IMPORTANT]
> Aus Gründen der Kompatibilität von Modulnamen veröffentlichen wir dieses Modul im PowerShell-Katalog weiterhin unter seinem alten Namen `AzureRmStorageTables`. In diesem Dokument wird auf den neuen Namen nur verwiesen.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit dem Befehl `Add-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Abrufen der Standortliste

Wenn Sie sich nicht sicher sind, welche Region Sie verwenden sollen, können Sie die verfügbaren Regionen auflisten. Sobald die Liste angezeigt wird, wählen Sie die gewünschte Region aus. In diesen Beispielen wird **eastus** verwendet. Speichern Sie diesen Wert zur späteren Verwendung in der Variablen **location**.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Erstellen Sie mit dem Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe. 

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Speichern Sie den Namen der Ressourcengruppe für die zukünftige Verwendung in einer Variable. In diesem Beispiel wird eine Ressourcengruppe namens *pshtablesrg* in der Region *eastus* erstellt.

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Speicherkonto erstellen

Erstellen Sie mit dem Befehl [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) ein universelles Standardspeicherkonto mit lokal redundantem Speicher (LRS). Stellen Sie sicher, dass Sie einem eindeutigen Speicherkontonamen angeben. Als Nächstes rufen Sie den Kontext ab, der das Speicherkonto darstellt. Wenn Sie Aktionen für ein Speicherkonto ausführen, können Sie auf den Kontext verweisen, anstatt wiederholt Ihre Anmeldeinformationen angeben zu müssen.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Erstellen einer neuen Tabelle

Verwenden Sie zum Erstellen einer Tabelle das Cmdlet [New-AzStorageTable](/powershell/module/az.storage/New-AzStorageTable). In diesem Beispiel heißt die Tabelle `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Abrufen einer Liste mit Tabellen im Speicherkonto

Rufen Sie mithilfe von [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable) eine Liste mit Tabellen im Speicherkonto ab.

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Abrufen eines Verweises auf eine bestimmte Tabelle

Zum Ausführen von Vorgängen für eine Tabelle wird ein Verweis auf die gewünschte Tabelle benötigt. Verwenden Sie [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable), um einen Verweis abzurufen.

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Verweisen auf die CloudTable-Eigenschaft einer bestimmten Tabelle

> [!IMPORTANT]
> Die Verwendung von „CloudTable“ ist obligatorisch, wenn Sie mit dem PowerShell-Modul **AzureTable** arbeiten. Rufen Sie den Befehl **Get-AzStorageTable** auf, um den Verweis auf dieses Objekt abzurufen. Dieser Befehl erstellt auch die Tabelle, wenn sie nicht bereits existiert.

Um Operationen für eine Tabelle unter Verwendung von **AzTable** ausführen zu können, benötigen Sie einen Verweis auf die „CloudTable“-Eigenschaft einer bestimmten Tabelle.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Löschen einer Tabelle

Verwenden Sie zum Löschen einer Tabelle das Cmdlet [Remove-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). Dieses Cmdlet entfernt die Tabelle einschließlich der dazugehörigen Daten.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie am Anfang dieser Anleitung eine neue Ressourcengruppe und ein Speicherkonto erstellt haben, können Sie alle in dieser Übung erstellten Ressourcen entfernen, indem Sie die Ressourcengruppe löschen. Dieser Befehl löscht alle in der Gruppe enthaltenen Ressourcen sowie die Ressourcengruppe selbst.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Anleitung haben Sie unter anderem erfahren, wie Sie folgende gängige Azure Table Storage-Vorgänge mit PowerShell ausführen: 

> [!div class="checklist"]
> * Erstellen einer Tabelle
> * Abrufen einer Tabelle
> * Hinzufügen von Tabellenentitäten
> * Abfragen einer Tabelle
> * Löschen von Tabellenentitäten
> * Löschen einer Tabelle

Weitere Informationen finden Sie in den folgenden Artikeln:

* [Storage PowerShell-Cmdlets](/powershell/module/az.storage#storage)

* [Working with Azure Tables from PowerShell – AzureRmStorageTable/AzTable PS Module v2.0 (Arbeiten mit Azure-Tabellen über PowerShell – AzureRmStorageTable/AzTable PS Module v2.0)](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* Beim [Microsoft Azure Storage-Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) handelt es sich um eine kostenlose eigenständige App von Microsoft, über die Sie ganz einfach visuell mit Azure Storage-Daten arbeiten können – unter Windows, MacOS und Linux.