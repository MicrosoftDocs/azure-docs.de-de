---
title: Verwalten von Daten in unabhängigen Azure-Clouds mithilfe von PowerShell
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie Speicher in der China-, der Government- und der Deutschland-Cloud mithilfe von Azure PowerShell verwalten.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e924a5f6c765b5b964fe3b1492393b063d9d23b4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783571"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Verwalten von Speicher in den unabhängigen Azure-Clouds mithilfe von PowerShell

Die meisten Benutzer verwenden Azure Public Cloud für die globale Bereitstellung von Azure. Es gibt allerdings auch einige unabhängige Bereitstellungen von Microsoft Azure, die beispielsweise durch bestimmte Anforderungen an Datenhoheit motiviert sind. Diese unabhängigen Bereitstellungen werden als „Umgebungen“ bezeichnet. Nachfolgend sind die derzeit verfügbaren unabhängigen Clouds aufgeführt:

* [Azure Government-Cloud](https://azure.microsoft.com/features/gov/)
* [Azure China 21Vianet-Cloud, betrieben von 21Vianet in China](http://www.windowsazure.cn/)
* [Azure Deutschland-Cloud](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Verwenden einer unabhängigen Cloud

Zur Verwendung von Azure Storage in einer der unabhängigen Clouds stellen Sie nicht mit Azure Public, sondern mit der gewünschten Cloud eine Verbindung her. So verwenden Sie anstelle von Azure Public eine der unabhängigen Clouds:

* Sie geben die *Umgebung* an, mit der eine Verbindung hergestellt werden soll.
* Sie bestimmen und verwenden die verfügbaren Regionen.
* Sie verwenden das richtige Endpunktsuffix. Dieses Suffix unterscheidet sich von Azure Public.

Für die Beispiele ist mindestens Version 0.7 des Azure PowerShell-Moduls Az erforderlich. Führen Sie in einem PowerShell-Fenster `Get-Module -ListAvailable Az` aus, um die Version zu ermitteln. Wenn nichts aufgeführt ist oder Sie ein Upgrade ausführen müssen, lesen Sie die Informationen unter [Install and configure Azure PowerShell](/powershell/azure/install-Az-ps) (Installieren und Konfigurieren von Azure PowerShell).

## <a name="log-in-to-azure"></a>Anmelden an Azure

Führen Sie das Cmdlet [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) aus, um die verfügbaren Azure-Umgebungen anzuzeigen:

```powershell
Get-AzEnvironment
```

Melden Sie sich bei dem Konto an, mit dem Sie Zugriff auf die Cloud haben, mit der Sie eine Verbindung herstellen möchten, und legen Sie die Umgebung fest. Dieses Beispiel zeigt, wie Sie sich bei einem Konto anmelden, das die Azure Government-Cloud verwendet.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Verwenden Sie zum Zugreifen auf die China-Cloud die Umgebung **AzureChinaCloud** . Verwenden Sie den Zugriff auf die Deutschland-Cloud **AzureGermanCloud** .

Falls Sie nun die Liste der Standorte benötigen, um ein Speicherkonto oder eine andere Ressource zu erstellen, können Sie mit [Get-AzLocation](/powershell/module/az.resources/get-azlocation) die für die ausgewählte Cloud verfügbaren Standorte abfragen.

```powershell
Get-AzLocation | select Location, DisplayName
```

Die folgende Tabelle enthält die für die Deutschland-Cloud zurückgegebenen Standorte:

|Standort | Anzeigename |
|----|----|
| `germanycentral` | Deutschland, Mitte|
| `germanynortheast` | Deutschland, Nordosten |


## <a name="endpoint-suffix"></a>Endpunktsuffix

Das Endpunktsuffix der einzelnen Umgebungen unterscheidet sich vom Azure Public-Endpunkt. Das Blobendpunktsuffix für Azure Public lautet **blob.core.windows.net** . Für die Government-Cloud lautet das Blobendpunktsuffix **blob.core.usgovcloudapi.net** .

### <a name="get-endpoint-using-get-azenvironment"></a>Abrufen des Endpunkts mit „Get-AzEnvironment“

Rufen Sie das Endpunktsuffix mit [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) ab. Beim Endpunkt handelt es sich um die *StorageEndpointSuffix* -Eigenschaft der Umgebung.

Die folgenden Codeausschnitte zeigen das Abrufen des Endpunktsuffix. Alle diese Befehle geben Ergebnisse wie „core.cloudapp.net“ oder „core.cloudapi.de“ zurück. Fügen Sie das Suffix an den Speicherdienst an, um auf den entsprechenden Dienst zuzugreifen. Beispiel: „queue.core.cloudapi.de“ greift auf den Warteschlangendienst in der Deutschland-Cloud zu.

Dieser Codeausschnitt ruft alle Umgebungen und ihre Endpunktsuffixe ab.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Mit diesem Befehl werden die folgenden Ergebnisse zurückgegeben:

| Name| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Um alle Eigenschaften für die angegebene Umgebung abzurufen, rufen Sie **Get-AzEnvironment** auf, und geben Sie den Cloudnamen an. Dieser Codeausschnitt gibt eine Liste mit Eigenschaften zurück. Suchen Sie in der Liste nach **StorageEndpointSuffix** . Das folgende Beispiel gilt für die Deutschland-Cloud.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

Die Ergebnisse sehen in etwa wie folgt aus:

|Eigenschaftenname|Wert|
|----|----|
| Name | `AzureGermanCloud` |
| EnableAdfsAuthentication | `False` |
| ActiveDirectoryServiceEndpointResourceI | `http://management.core.cloudapi.de/` |
| GalleryURL | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PublishSettingsFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix | `.database.cloudapi.de` |
| **StorageEndpointSuffix** | `core.cloudapi.de` |
| ... | ... |

Um nur die Eigenschaft des Speicherendpunktsuffixes abzurufen, rufen Sie die bestimmte Cloud ab, und fragen Sie nur diese eine Eigenschaft ab.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

Dieser Befehl gibt folgende Information zurück:

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>Abrufen eines Endpunkts von einem Speicherkonto

Sie können zum Abrufen der Endpunkte auch die Eigenschaften eines Speicherkontos überprüfen:

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

Für ein Speicherkonto in der Government-Cloud gibt dieser Befehl die folgende Ausgabe zurück:

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Nach dem Einrichten der Umgebung

Sie können nun PowerShell verwenden, um Ihre Speicherkonten zu verwalten und auf Blob-, Warteschlangen-, Datei- und Tabellendaten zuzugreifen. Weitere Informationen finden Sie unter [Az.Storage](/powershell/module/az.storage).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie eine neue Ressourcengruppe und ein Speicherkonto für diese Übung erstellt haben, können Sie beide Ressourcen entfernen, indem Sie die Ressourcengruppe löschen. Beim Löschen der Ressourcengruppe werden alle in der Gruppe enthaltenen Ressourcen gelöscht.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

* [Speichern von Benutzeranmeldungen zwischen PowerShell-Sitzungen](/powershell/azure/context-persistence)
* [Azure Government-Speicher](../../azure-government/compare-azure-government-global-azure.md)
* [Microsoft Azure Government-Entwicklerhandbuch](../../azure-government/documentation-government-developer-guide.md)
* [Entwicklerhinweise zu Azure China 21Vianet-Anwendungen](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Azure Deutschland-Dokumentation](../../germany/germany-welcome.md)