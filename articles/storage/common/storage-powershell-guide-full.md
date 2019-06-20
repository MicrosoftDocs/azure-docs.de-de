---
title: Verwenden von Azure PowerShell mit Azure Storage | Microsoft Docs
description: Erfahren Sie, wie Sie Azure PowerShell-Cmdlets für Azure Storage verwenden.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 082033cebc68fc97f7cff9ce80eb02acbbf5f4b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65145880"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Verwenden von Azure PowerShell mit Azure Storage

Azure PowerShell dient zum Erstellen und Verwalten von Azure-Ressourcen über die PowerShell-Befehlszeile oder mit Skripts. Für Azure Storage werden diese Cmdlets in zwei Kategorien unterteilt: die Steuerungsebene und die Datenebene. Mithilfe der Cmdlets der Steuerungsebene wird das Speicherkonto verwaltet, um z. B. Konten zu erstellen, Eigenschaften festzulegen, Speicherkonten zu löschen, Zugriffsschlüssel zu drehen usw. Mithilfe der Cmdlets der Datenebene werden die Daten verwaltet, die *im* Speicherkonto gespeichert werden. Dazu zählen z. B. das Hochladen von Blobs, das Erstellen von Dateifreigaben und das Hinzufügen von Nachrichten zu einer Warteschlange.

Diese Anleitung behandelt allgemeine Vorgänge, über die Speicherkonten mithilfe der Cmdlets der Verwaltungsebene verwaltet werden. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Auflisten von Speicherkonten
> * Abrufen eines Verweises auf ein vorhandenes Speicherkonto
> * Speicherkonto erstellen
> * Festlegen der Eigenschaften von Speicherkonten
> * Abrufen und erneutes Generieren der Kontenschlüssel
> * Schützen des Zugriffs auf Ihr Speicherkonto
> * Aktivieren von Storage Analytics

Dieser Artikel enthält Links zu weiteren PowerShell-Artikeln für Storage. In diesen wird beispielsweise das Aktivieren und Zugreifen auf Storage Analytics, das Verwenden von Cmdlets der Datenebene und das Zugreifen auf unabhängige Azure-Clouds in China und Deutschland sowie auf die Government Cloud beschrieben.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Für diese Übung ist das Azure PowerShell-Modul Az (Version 0.7 oder höher) erforderlich. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Install and configure Azure PowerShell](/powershell/azure/install-Az-ps) (Installieren des Azure PowerShell-Moduls) Informationen dazu.

Für diese Übung können Sie die Befehle in ein normales PowerShell-Fenster eingeben oder die [Windows PowerShell ISE (Integrated Scripting Environment)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) verwenden und die Befehle in einen Editor eingeben. Dann können Sie einen oder mehrere Befehle gleichzeitig testen, während Sie die Beispiele durchlaufen. Sie können die auszuführenden Zeilen hervorheben und auf „Auswahl ausführen“ klicken, um diese Befehle einfach auszuführen.

Weitere Informationen zu Speicherkonten finden Sie unter [Einführung in Storage](storage-introduction.md) und [Informationen zu Azure-Speicherkonten](storage-create-storage-account.md).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Auflisten der Speicherkonten im Abonnement

Führen Sie das Cmdlet [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) aus, um die Liste der Speicherkonten im aktuellen Abonnement abzurufen.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Abrufen eines Verweises auf ein Speicherkonto

Als Nächstes benötigen Sie einen Verweis auf ein Speicherkonto. Sie können ein neues Speicherkonto erstellen oder einen Verweis auf ein vorhandenes Speicherkonto abrufen. Im folgenden Abschnitt werden beide Methoden veranschaulicht.

### <a name="use-an-existing-storage-account"></a>Verwenden eines vorhandenen Speicherkontos

Damit Sie ein vorhandenes Speicherkonto abrufen können, benötigen Sie den Namen der Ressourcengruppe und den Namen des Speicherkontos. Legen Sie die Variablen für diese beiden Felder fest, und verwenden Sie dann das Cmdlet [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount).

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

Jetzt verfügen Sie über „$storageAccount“, das auf ein vorhandenes Speicherkonto verweist.

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

Das folgende Skript veranschaulicht die Erstellung eines allgemeinen Speicherkontos mithilfe von [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Nachdem Sie das Konto erstellt haben, rufen Sie dessen Kontext ab, der in nachfolgenden Befehlen verwendet werden kann, anstatt die Authentifizierung bei jedem Aufruf anzugeben.

```powershell
# Get list of locations and select one.
Get-AzLocation | select Location
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzResourceGroup -Name $resourceGroup -Location $location

# Set the name of the storage account and the SKU name.
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context.
$ctx = $storageAccount.Context
```

Das Skript verwendet die folgenden PowerShell-Cmdlets:

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation): Ruft eine Liste der gültigen Speicherorte ab. Im Beispiel wird `eastus` für den Speicherort verwendet.

*   [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup): Erstellt eine neue Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Ihre Azure-Ressourcen bereitgestellt und verwaltet werden. In diesem Beispiel erhält sie die Bezeichnung `teststoragerg`.

*   [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount): Erstellt das Speicherkonto. Im Beispiel wird `testpshstorage` verwendet.

Der SKU-Name gibt den Typ der Replikation für das Speicherkonto an, z. B. LRS (lokal redundanter Speicher). Weitere Informationen zur Replikation finden Sie unter [Azure Storage-Replikation](storage-redundancy.md).

> [!IMPORTANT]
> Der Name für das Speicherkonto muss in Azure eindeutig sein und Kleinschreibung verwenden. Informationen zu Namenskonventionen und Einschränkungen finden Sie unter [Benennen von Containern, Blobs und Metadaten und Verweisen auf diese](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
>

Sie verfügen jetzt über ein neues Speicherkonto und über einen Verweis auf dieses Konto.

## <a name="manage-the-storage-account"></a>Verwalten des Speicherkontos

Nachdem Sie über einen Verweis auf ein neues oder ein vorhandenes Speicherkonto verfügen, werden im folgenden Abschnitt einige der Befehle veranschaulicht, mit denen Sie das Speicherkonto verwalten können.

### <a name="storage-account-properties"></a>Eigenschaften von Speicherkonten

Verwenden Sie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount), um die Einstellungen für ein Speicherkonto zu ändern. Während Sie den Speicherort eines Speicherkontos oder die Ressourcengruppe ändern, in der sich das Speicherkonto befindet, können Sie auch viele andere Eigenschaften ändern. Nachfolgend sind einige der Eigenschaften aufgeführt, die Sie mithilfe von PowerShell ändern können.

* Die **benutzerdefinierte Domäne**, die dem Speicherkonto zugeordnet ist.

* Die **Tags**, die dem Speicherkonto zugeordnet sind. Tags werden häufig zu Abrechnungszwecken zum Kategorisieren von Ressourcen verwendet.

* Die **SKU** ist eine Replikationseinstellung für das Speicherkonto, z. B. LRS für „lokal redundanter Speicher“. Sie können z. B. von „Standard\_LRS“ zu „Standard\_GRS“ oder „Standard\_RAGRS“ wechseln. Beachten Sie, dass Sie „Standard\_ZRS“ oder „Premium\_LRS“ nicht in eine andere SKU ändern können (oder andere SKUs in diese beiden SKUs).

* Die **Zugriffsebene** für Blob-Speicherkonten. Der Wert für die Zugriffsebene wird auf **heiß** oder **kalt** festgelegt. Dadurch können Sie Ihre Kosten minimieren, indem Sie die Zugriffsebene auf die Art und Weise ausrichten, wie Sie das Speicherkonto verwenden. Weitere Informationen finden Sie unter [Speicherebenen „Heiß“ (Hot), „Kalt“ (Cool) und „Archiv“](../blobs/storage-blob-storage-tiers.md).

* Gestatten Sie nur HTTPS-Datenverkehr.

### <a name="manage-the-access-keys"></a>Verwalten der Zugriffsschlüssel

Ein Azure-Speicherkonto verfügt über zwei Speicherschlüssel. Verwenden Sie [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey), um die Schlüssel abzurufen. In diesem Beispiel wird der erste Schlüssel abgerufen. Verwenden Sie `Value[1]` anstelle von `Value[0]`, um den anderen Schlüssel abzurufen.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Verwenden Sie [New-AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey), um den Schlüssel erneut zu generieren.

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

Verwenden Sie `key2` anstelle von `key1` als Schlüsselnamen, um den anderen Schlüssel erneut zu generieren.

Generieren Sie einen Ihrer Schlüssel neu, und rufen Sie ihn dann erneut ab, um den neuen Wert anzuzeigen.

> [!NOTE]
> Sie sollten vor der erneuten Generierung des Schlüssels für ein Produktionsspeicherkonto sorgfältig planen. Wenn Sie einen oder beide Schlüssel erneut generieren, wird der Zugriff für jede Anwendung außer Kraft gesetzt, die den neu generierten Schlüssel verwendet. Weitere Informationen finden Sie unter [Zugriffsschlüssel](storage-account-manage.md#access-keys).


### <a name="delete-a-storage-account"></a>Löschen von Speicherkonten

Verwenden Sie zum Löschen eines Speicherkontos das Cmdlet [Remove-AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount).

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Wenn Sie ein Speicherkonto löschen, werden alle im Konto gespeicherten Assets ebenfalls gelöscht. Wenn Sie ein Konto versehentlich löschen, rufen Sie sofort den Support an und öffnen ein Ticket, um das Speicherkonto wiederherzustellen. Die Wiederherstellung Ihrer Daten wird nicht garantiert, kann aber in einigen Fällen funktionieren. Erstellen Sie kein neues Speicherkonto mit demselben Namen wie für das alte Konto, bis das Supportticket aufgelöst wurde.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Schützen Ihres Speicherkontos mithilfe von VNETs und Firewalls

Standardmäßig können alle Netzwerke mit Zugriff auf das Internet auf sämtliche Speicherkonten zugreifen. Sie können jedoch Netzwerkregeln konfigurieren, um nur Anwendungen aus bestimmten virtuellen Netzwerken den Zugriff auf ein Speicherkonto zu ermöglichen. Weitere Informationen finden Sie unter [Konfigurieren von Firewalls und virtuellen Netzwerken in Azure Storage](storage-network-security.md).

In diesem Artikel wird das Verwalten dieser Einstellungen mithilfe der folgenden PowerShell-Cmdlets veranschaulicht:
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Remove-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>Verwenden von Storage Analytics  

[Azure Storage Analytics](storage-analytics.md) besteht aus [Storage Analytics-Metriken](/rest/api/storageservices/about-storage-analytics-metrics) und [Storage Analytics-Protokollierung](/rest/api/storageservices/about-storage-analytics-logging).

**Storage Analytics-Metriken** werden verwendet, um Metriken für Ihre Azure-Speicherkonten zu erfassen, mit denen Sie die Integrität eines Speicherkontos überwachen können. Metriken können für Blobs, Dateien, Tabellen und Warteschlangen aktiviert werden.

Die **Storage Analytics-Protokollierung** erfolgt serverseitig, und sie ermöglicht es Ihnen, Details für erfolgreiche und fehlerhafte Anforderungen in Ihrem Speicherkonto aufzuzeichnen. Anhand dieser Protokolle können Sie Details zu Lese-, Schreib- und Löschvorgängen für Ihre Tabellen, Warteschlangen und Blobs sowie die Gründe für fehlerhafte Anforderungen anzeigen. Die Protokollierung ist für Azure Files nicht verfügbar.

Sie können die Überwachung mithilfe des [Azure-Portals](https://portal.azure.com), über PowerShell oder programmgesteuert mithilfe der Speicherclientbibliothek aktivieren.

> [!NOTE]
> Sie können die Minutenanalyse mit PowerShell aktivieren. Diese Funktion ist im Portal nicht verfügbar.
>

* Informationen zum Aktivieren und Anzeigen von Speichermetrikdaten mithilfe von PowerShell finden Sie unter [Metriken der Speicheranalyse](storage-analytics-metrics.md).

* Weitere Informationen zum Aktivieren und Abrufen von Speicherprotokolldaten mithilfe von PowerShell finden Sie unter [Aktivieren der Speicherprotokollierung mithilfe von PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) und [Suchen nach den Protokolldaten der Speicherprotokollierung](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data).

* Ausführliche Informationen zur Verwendung von Speichermetriken und der Speicherprotokollierung zum Beheben von Speicherproblemen finden Sie unter [Microsoft Azure Storage: Überwachung, Diagnose und Problembehandlung](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Verwalten der Daten im Speicherkonto

Nachdem Sie nun erfahren haben, wie Sie Ihr Speicherkonto mit PowerShell verwalten, können Sie in den folgenden Artikeln nachlesen, wie Sie auf Datenobjekte im Speicherkonto zugreifen.

* [Verwalten von Blobs mit PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Verwalten von Dateien mit PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Verwalten von Warteschlangen mit PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Perform Azure Table storage operations with Azure PowerShell](../../storage/tables/table-storage-how-to-use-powershell.md) (Ausführen von Azure Table Storage-Vorgängen mit PowerShell)

Die Azure Cosmos DB-Tabellen-API bietet Premiumfeatures für den Tabellenspeicher wie sofort einsatzbereite globale Verteilung, Lese- und Schreibvorgänge mit geringer Wartezeit, automatische sekundäre Indizierung und dedizierten Durchsatz.

* Weitere Informationen finden Sie unter [Einführung in die Table-API von Azure Cosmos DB](../../cosmos-db/table-introduction.md).

## <a name="independent-cloud-deployments-of-azure"></a>Unabhängige Cloudbereitstellungen von Azure

Die meisten Benutzer verwenden Azure Public Cloud für die globale Bereitstellung von Azure. Es gibt allerdings auch einige unabhängige Bereitstellungen von Microsoft Azure, die beispielsweise durch bestimmte Anforderungen an Datenhoheit motiviert sind. Diese unabhängige Bereitstellungen werden als „Umgebungen“ bezeichnet. Folgende Umgebungen stehen zur Verfügung:

* [Azure Government-Cloud](https://azure.microsoft.com/features/gov/)
* [Azure China 21Vianet-Cloud, betrieben von 21Vianet in China](http://www.windowsazure.cn/)
* [Azure Deutschland-Cloud](../../germany/germany-welcome.md)

Informationen zum Zugriff auf diese Clouds und deren Speicher mit PowerShell finden Sie unter [Managing Storage in the Azure independent clouds using PowerShell (Verwalten von Speicher in unabhängigen Azure-Clouds mit PowerShell)](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie eine neue Ressourcengruppe und ein Speicherkonto für diese Übung erstellt haben, können Sie alle erstellten Ressourcen entfernen, indem Sie die Ressourcengruppe löschen. Dabei werden auch alle in der Gruppe enthaltenen Ressourcen gelöscht. In diesem Fall werden das erstellte Speicherkonto und die Ressourcengruppe selbst entfernt.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Nächste Schritte

Diese Anleitung behandelt allgemeine Vorgänge, über die Speicherkonten mithilfe der Cmdlets der Verwaltungsebene verwaltet werden. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Auflisten von Speicherkonten
> * Abrufen eines Verweises auf ein vorhandenes Speicherkonto
> * Speicherkonto erstellen
> * Festlegen der Eigenschaften von Speicherkonten
> * Abrufen und erneutes Generieren der Kontenschlüssel
> * Schützen des Zugriffs auf Ihr Speicherkonto
> * Aktivieren von Storage Analytics

In diesem Artikel wurden außerdem Links zu weiteren Artikeln bereitgestellt. In diesen wird beispielsweise das Verwalten von Datenobjekten, das Aktivieren von Storage Analytics und das Zugreifen auf unabhängige Azure-Clouds wie die chinesische und deutsche Azure-Cloud sowie auf die Government Cloud beschrieben. Die folgenden Artikel und Ressourcen dienen als Referenz:

* [PowerShell-Cmdlets der Azure Storage-Steuerungsebene](/powershell/module/az.storage/)
* [PowerShell-Cmdlets der Azure Storage-Datenebene](/powershell/module/azure.storage/)
* [Windows PowerShell-Referenz](https://msdn.microsoft.com/library/ms714469.aspx)
