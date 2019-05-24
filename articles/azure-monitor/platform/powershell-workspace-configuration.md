---
title: Verwenden von PowerShell zum Erstellen und Konfigurieren eines Log Analytics-Arbeitsbereichs | Microsoft-Dokumentation
description: In Log Analytics-Arbeitsbereichen in Azure Monitor werden Daten von Servern in Ihrer lokalen oder Cloudinfrastruktur gespeichert. Sie können Computerdaten aus dem Azure-Speicher sammeln, wenn sie von Azure-Diagnose generiert werden.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.devlang: powershell
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.openlocfilehash: 36cb2462a47f9d175ca25bbbde46a14009637db0
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65907886"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Verwalten von Log Analytics-Arbeitsbereichen in Azure Monitor mithilfe von PowerShell

Mit den [PowerShell-Cmdlets für Log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) können Sie verschiedene Funktionen für einen Log Analytics-Arbeitsbereich in Azure Monitor über eine Befehlszeile oder in einem Skript ausführen.  Beispiele für die Aufgaben, die Sie mit PowerShell ausführen können:

* Erstellen eines Arbeitsbereichs
* Hinzufügen oder Entfernen einer Lösung
* Importieren und Exportieren von gespeicherten Suchvorgängen
* Erstellen einer Computergruppe
* Aktivieren der Sammlung von IIS-Protokollen auf Computern mit installiertem Windows-Agent
* Sammeln von Leistungsindikatoren von Windows- und Linux-Computern
* Sammeln von Ereignissen aus Syslog auf Linux-Computern
* Sammeln von Ereignissen aus Windows-Ereignisprotokollen
* Sammeln von benutzerdefinierten Ereignisprotokollen
* Hinzufügen des Log Analytics-Agents auf virtuellen Azure-Computern
* Konfiguration von Log Analytics zum Indizieren der Daten, die mit der Azure-Diagnose gesammelt werden

Dieser Artikel enthält zwei Codebeispiele, die einige der Funktionen veranschaulicht, die Sie mit PowerShell ausführen können.  In der [Referenz zu den PowerShell-Cmdlets für Log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) finden Sie noch weitere Funktionen.

> [!NOTE]
> Log Analytics wurde früher als Operational Insights bezeichnet, daher wird dieser Name in den Cmdlets verwendet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen
In diesen Beispielen wird Version 1.0.0 oder höher des Az.OperationalInsights-Moduls verwendet.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Erstellen und Konfigurieren eines Log Analytics-Arbeitsbereichs
Das folgende Beispielskript veranschaulicht Folgendes:

1. Erstellen eines Arbeitsbereichs
2. Auflisten der verfügbaren Lösungen
3. Hinzufügen von Lösungen zum Arbeitsbereich
4. Importieren gespeicherter Suchvorgänge
5. Exportieren gespeicherter Suchvorgänge
6. Erstellen einer Computergruppe
7. Aktivieren der Sammlung von IIS-Protokollen auf Computern mit installiertem Windows-Agent
8. Sammeln von Leistungsindikatoren logischer Datenträger von Linux-Computern (Prozentsatz verwendeter I-Knoten, MB frei, Prozentsatz des verwendeten Speicherplatzes, Übertragungen/s, Lesevorgänge/s, Schreibvorgänge/s)
9. Sammeln von Syslog-Ereignissen auf Linux-Computern
10. Sammeln von Fehler- und Warnereignissen aus dem Anwendungsereignisprotokoll von Windows-Computern
11. Erfassen des Leistungsindikators „Verfügbarer Arbeitsspeicher in MB“ von Windows-Computern
12. Sammeln eines benutzerdefinierten Protokolls

```powershell

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```
Im obigen Beispiel wurde regexDelimiter als „\\n“ für den Zeilenumbruch definiert. Das Protokolltrennzeichen kann auch ein Zeitstempel sein.  Diese Formate werden unterstützt:

| Format | Das RegEx-Format von JSON verwendet zwei \\ für jedes „\“ in einem standardmäßigen RegEx, also muss \\ beim Testen in einer RegEx-App zu „\“ reduziert werden. | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\\\d{2})\|(\\\\d{4}))-([0-1]\\\\d)-(([0-3]\\\\d)\|(\\\\d))\\\\s((\\\\d)\|([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\\\d)\|[0-9])/(([0-3]\\\\d)\|(\\\\d))/((\\\\d{2})\|(\\\\d{4}))\\\\s((\\\\d)\|([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\\\s(AM\|PM\|am\|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `((([0-3]\\\\d)\` | `(\\\\d))/(Jan\|Feb\|Mar\|May\|Apr\|Jul\|Jun\|Aug\|Oct\|Sep\|Nov\|Dec\|jan\|feb\|mar\|may\|apr\|jul\|jun\|aug\|oct\|sep\|nov\|dec)/((\\\\d{2})\|(\\\\d{4}))\\\\s((\\\\d)\` | `([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?\|Feb(?:ruary)?\|Mar(?:ch)?\|Apr(?:il)?\|May\|Jun(?:e)?\|Jul(?:y)?\|Aug(?:ust)?\|Sep(?:tember)?\|Sept\|Oct(?:ober)?\|Nov(?:ember)?\|Dec(?:ember)?)).*?((?:(?:[0-2]?\\\\d{1})\|(?:[3][01]{1})))(?![\\\\d]).*?((?:(?:[1]{1}\\\\d{1}\\\\d{1}\\\\d{1})\|(?:[2]{1}\\\\d{3})))(?![\\\\d]).*?((?:(?:[0-1][0-9])\|(?:[2][0-3])\|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\\\s?(?:am\|AM\|pm\|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]\|[1][0-2])([0-2][0-9]\|[3][0-1])\\\\s\\\\s?([0-1]?[0-9]\|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]\|[3][0-1])([0][1-9]\|[1][0-2])[0-9]{2}\\\\s\\\\s?([0-1]?[0-9]\|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan\|Feb\|Mar\|Apr\|May\|Jun\|Jul\|Aug\|Sep\|Oct\|Nov\|Dec)\\\\s\\\\s?([0]?[1-9]\|[1-2][0-9]\|[3][0-1])\\\\s([0-1]?[0-9]\|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> zwei Leerzeichen nach MMM | `(Jan\|Feb\|Mar\|Apr\|May\|Jun\|Jul\|Aug\|Sep\|Oct\|Nov\|Dec)\\\\s\\\\s([0]?[1-9]\|[1-2][0-9]\|[3][0-1])\\\\s([0][0-9]\|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan\|Feb\|Mar\|Apr\|May\|Jun\|Jul\|Aug\|Sep\|Oct\|Nov\|Dec)\\\\s([0]?[1-9]\|[1-2][0-9]\|[3][0-1])\\\\s([0][0-9]\|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> dabei ist „+“ ein „+“ oder ein „-“ <br> dabei ist „zzzz“ der Zeitversatz | `(([0-2][1-9]\|[3][0-1])\\\\/(Jan\|Feb\|Mar\|Apr\|May\|Jun\|Jul\|Aug\|Sep\|Oct\|Nov\|Dec)\\\\/((19\|20)[0-9][0-9]):([0][0-9]\|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\\\s[\\\\+\|\\\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> Das „T“ steht für den Buchstaben „T“ | `((\\\\d{2})\|(\\\\d{4}))-([0-1]\\\\d)-(([0-3]\\\\d)\|(\\\\d))T((\\\\d)\|([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Konfigurieren von Log Analytics zum Senden der Azure-Diagnose
Für die Überwachung von Azure-Ressourcen ohne Agents müssen die Ressourcen Azure-Diagnose aktiviert haben und zum Schreiben in einen Log Analytics-Arbeitsbereich konfiguriert worden sein. Bei diesem Ansatz werden Daten direkt an den Arbeitsbereich gesendet, und Daten müssen nicht in ein Speicherkonto geschrieben werden. Unterstützte Ressourcen:

| Ressourcentyp | Protokolle | Metriken |
| --- | --- | --- |
| Anwendungsgateways    | Ja | Ja |
| Automation-Konten     | Ja | |
| Batch-Konten          | Ja | Ja |
| Data Lake Analytics     | Ja | |
| Data Lake Store         | Ja | |
| Elastischer SQL-Pool        |     | Ja |
| Event Hub-Namespace     |     | Ja |
| IoT Hubs                |     | Ja |
| Key Vault               | Ja | |
| Load Balancer          | Ja | |
| Logic Apps              | Ja | Ja |
| Netzwerksicherheitsgruppen | Ja | |
| Azure Cache for Redis             |     | Ja |
| Suchdienste         | Ja | Ja |
| Service Bus-Namespace   |     | Ja |
| SQL (v12)               |     | Ja |
| Websites               |     | Ja |
| Webserverfarmen        |     | Ja |

Ausführliche Informationen zu den verfügbaren Metriken finden Sie unter [Supported metrics with Azure Monitor](../../azure-monitor/platform/metrics-supported.md) (Von Azure Monitor unterstützte Metriken).

Ausführliche Informationen zu den verfügbaren Protokollen finden Sie unter [Supported services and schema for Diagnostic Logs](../../azure-monitor/platform/diagnostic-logs-schema.md) (Unterstützte Dienste und Schema für Diagnoseprotokolle).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Sie können mit dem vorherigen Cmdlet auch Protokolle von Ressourcen in verschiedenen Abonnements sammeln. Das Cmdlet kann abonnementübergreifend verwendet werden, da Sie die ID der Ressource, die Protokolle erstellt, und des Arbeitsbereichs angeben, an den die Protokolle gesendet werden.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Konfigurieren eines Log Analytics-Arbeitsbereichs zum Erfassen der Azure-Diagnose aus dem Speicher
Damit Sie Protokolldaten in einer ausgeführten Instanz eines klassischen Clouddiensts oder eines Service Fabric-Clusters erfassen können, müssen Sie die Daten zunächst in den Azure-Speicher schreiben. Dann wird ein Log Analytics-Arbeitsbereich konfiguriert, um die Protokolle aus dem Speicherkonto zu sammeln. Unterstützte Ressourcen:

* Azure Cloud Services (Web- und Workerrollen)
* Service Fabric-Cluster

Das folgende Beispiel veranschaulicht die Vorgehensweise:

1. Auflisten der vorhandenen Speicherkonten und Speicherorte, aus denen der Arbeitsbereich Daten indiziert
2. Erstellen einer Konfiguration zum Lesen aus einem Speicherkonto
3. Aktualisieren der neu erstellten Konfiguration zum Indizieren von Daten von zusätzlichen Speicherorten
4. Löschen der neu erstellten Konfiguration

```powershell
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable"
$workspace = (Get-AzOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want the workspace to index
$storageId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight"

```

Sie können mit dem vorherigen Skript auch Protokolle aus Speicherkonten in verschiedenen Abonnements sammeln. Das Skript kann abonnementübergreifend verwendet werden, da Sie die Speicherkontoressourcen-ID und einen entsprechenden Zugriffsschlüssel bereitstellen. Wenn Sie den Zugriffsschlüssel ändern, müssen Sie die Speicherdetails aktualisieren, sodass sie den neuen Schlüssel enthalten.


## <a name="next-steps"></a>Nächste Schritte
* [PowerShell-Cmdlets für Log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) .

