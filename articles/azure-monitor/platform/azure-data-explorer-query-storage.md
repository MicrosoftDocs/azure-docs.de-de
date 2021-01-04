---
title: Abfragen exportierter Daten aus Azure Monitor mit Azure Data Explorer (Vorschau)
description: Verwenden Sie Azure Data Explorer, um Daten abzufragen, die aus Ihrem Log Analytics-Arbeitsbereich in ein Azure Storage-Konto exportiert wurden.
ms.subservice: logs
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 2735aec0f95f5e282bb1dffba6e4f42f966cf117
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186540"
---
# <a name="query-exported-data-from-azure-monitor-using-azure-data-explorer-preview"></a>Abfragen exportierter Daten aus Azure Monitor mit Azure Data Explorer (Vorschau)
Das Exportieren von Daten aus Azure Monitor in ein Azure Storage-Konto ermöglicht eine kostengünstige Aufbewahrung und bietet die Möglichkeit, Protokolle verschiedenen Regionen neu zuzuordnen. Verwenden Sie Azure Data Explorer, um Daten abzufragen, die aus Ihrem Log Analytics-Arbeitsbereich exportiert wurden. Nach der Konfiguration stehen unterstützte Tabellen, die von Ihren Arbeitsbereichen an ein Azure Storage-Konto gesendet werden, als Datenquelle für Azure Data Explorer bereit.

Der Prozessablauf sieht folgendermaßen aus: 

1.  Exportieren von Daten aus dem Log Analytics-Arbeitsbereich in ein Azure Storage-Konto
2.  Erstellen einer externen Tabelle in Ihrem Azure Data Explorer-Cluster und Zuordnen der Datentypen
3.  Abfragen von Daten aus Azure Data Explorer

:::image type="content" source="media/azure-data-explorer-query-storage/exported-data-query.png" alt-text="Ablauf zur Abfrage exportierter Daten mit Azure Data Explorer":::



## <a name="send-data-to-azure-storage"></a>Senden von Daten an Azure Storage
Azure Monitor-Protokolle können mithilfe einer der folgenden Optionen in ein Azure Storage-Konto exportiert werden.

- Wenn Sie alle Daten aus dem Log Analytics-Arbeitsbereich in ein Azure Storage-Konto oder einen Event Hub exportieren möchten, verwenden Sie das Datenexportfeature von Azure Monitor-Protokollen im Log Analytics-Arbeitsbereich. Informationen hierzu finden Sie unter [Datenexport im Log Analytics-Arbeitsbereich in Azure Monitor (Vorschau)](logs-data-export.md).
- Geplanter Export aus einer Protokollabfrage mithilfe einer Logik-App. Dies ähnelt dem Datenexportfeature, ermöglicht Ihnen aber das Senden von gefilterten oder aggregierten Daten an Azure Storage. Diese Methode unterliegt jedoch [Beschränkungen für Protokollabfragen](../service-limits.md#log-analytics-workspaces). Informationen dazu finden Sie unter [Archivieren von Daten aus dem Log Analytics-Arbeitsbereich in Azure Storage mithilfe von Logic Apps](logs-export-logic-app.md).
- Einmaliger Export mit einer Logik-App. Informationen hierzu finden Sie unter [Connector für Azure Monitor-Protokolle für Azure Logic Apps und Power Automate](logicapp-flow-connector.md).
- Einmaliger Export auf lokalen Computer mit einem PowerShell-Skript. Informationen hierzu finden Sie unter [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).

> [!TIP]
> Sie können einen vorhandenen Azure Data Explorer-Cluster verwenden oder einen neuen dedizierten Cluster mit den erforderlichen Konfigurationen erstellen.

## <a name="create-an-external-table-located-in-azure-blob-storage"></a>Erstellen einer externen Tabelle in Azure Blob Storage
Verwenden Sie [externe Tabellen](/azure/data-explorer/kusto/query/schema-entities/externaltables), um Azure Data Explorer mit einem Azure Storage-Konto zu verknüpfen. Eine externe Tabelle ist eine Kusto-Schemaentität, die auf Daten verweist, die außerhalb einer Kusto-Datenbank gespeichert sind. Wie Tabellen hat auch eine externe Tabelle ein genau definiertes Schema. Im Gegensatz zu Tabellen werden die Daten jedoch außerhalb eines Kusto-Clusters gespeichert und verwaltet. Die exportierten Daten aus dem vorherigen Abschnitt werden in JSON-Zeilen gespeichert.

Um einen Verweis zu erstellen, benötigen Sie das Schema der exportierten Tabelle. Verwenden Sie den [getschema](/azure/data-explorer/kusto/query/getschemaoperator)-Operator von Log Analytics, um diese Informationen abzurufen, zu denen auch die Spalten der Tabelle und deren Datentypen gehören.

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-map-schema.jpg" alt-text="Log Analytics-Tabellenschema":::

Sie können nun die Ausgabe verwenden, um die Kusto-Abfrage zum Erstellen der externen Tabelle zu erzeugen.
Folgen Sie den Anweisungen unter [Erstellen und Ändern externer Tabellen in Azure Storage oder Azure Data Lake](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake), erstellen Sie eine externe Tabelle in einem JSON-Format, und führen Sie dann die Abfrage aus Ihrer Azure Data Explorer-Datenbank aus.

>[!NOTE]
>Die Erstellung der externen Tabelle basiert auf zwei Prozessen. Im ersten Prozess wird die externe Tabelle und im zweiten die Zuordnung erstellt.

Mit dem folgenden PowerShell-Skript werden die [create](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake#create-external-table-mapping)-Befehle für die Tabelle und die Zuordnung erstellt.

```powershell
PARAM(
    $resourcegroupname, #The name of the Azure resource group
    $TableName, # The Log Analytics table you wish to convert to external table
    $MapName, # The name of the map
    $subscriptionId, #The ID of the subscription
    $WorkspaceId, # The Log Analytics WorkspaceId
    $WorkspaceName, # The Log Analytics workspace name
    $BlobURL, # The Blob URL where the data is saved
    $ContainerAccessKey, # The blob container Access Key (Option to add a SAS url)
    $ExternalTableName = $null # The External Table name, null to use the same name
)

if($null -eq $ExternalTableName) {
    $ExternalTableName = $TableName
}

$query = $TableName + ' | getschema | project ColumnName, DataType'

$output = (Invoke-AzOperationalInsightsQuery -WorkspaceId $WorkspaceId -Query $query).Results

$FirstCommand = @()
$SecondCommand = @()

foreach ($record in $output) {
    if ($record.DataType -eq 'System.DateTime') {
        $dataType = 'datetime'
    } else {
        $dataType = 'string'
    }
    $FirstCommand += $record.ColumnName + ":" + "$dataType" + ","
    $SecondCommand += "{`"column`":" + "`"" + $record.ColumnName + "`"," + "`"datatype`":`"$dataType`",`"path`":`"$." + $record.ColumnName + "`"},"
}
$schema = ($FirstCommand -join '') -replace ',$'
$mapping = ($SecondCommand -join '') -replace ',$'

$CreateExternal = @'
.create external table {0} ({1})
kind=blob
partition by (TimeGeneratedPartition:datetime = bin(TimeGenerated, 1min))
pathformat = (datetime_pattern("'y='yyyy'/m='MM'/d='dd'/h='HH'/m='mm", TimeGeneratedPartition))
dataformat=multijson
(
   h@'{2}/WorkspaceResourceId=/subscriptions/{4}/resourcegroups/{6}/providers/microsoft.operationalinsights/workspaces/{5};{3}'
)
with
(
   docstring = "Docs",
   folder = "ExternalTables"
)
'@ -f $TableName, $schema, $BlobURL, $ContainerAccessKey, $subscriptionId, $WorkspaceName, $resourcegroupname,$WorkspaceId

$createMapping = @'
.create external table {0} json mapping "{1}" '[{2}]'
'@ -f $ExternalTableName, $MapName, $mapping

Write-Host -ForegroundColor Red 'Copy and run the following commands (one by one), on your Azure Data Explorer cluster query window to create the external table and mappings:'
write-host -ForegroundColor Green $CreateExternal
Write-Host -ForegroundColor Green $createMapping
```

Die folgende Abbildung zeigt ein Beispiel der Ausgabe.

:::image type="content" source="media/azure-data-explorer-query-storage/external-table-create-command-output.png" alt-text="Ausgabe des create-Befehls für eine externe Tabelle":::

[![Beispielausgabe](media/azure-data-explorer-query-storage/external-table-create-command-output.png)](media/azure-data-explorer-query-storage/external-table-create-command-output.png#lightbox)

>[!TIP]
>* Sie können die Ausgabe des Skripts kopieren, einfügen und dann in Ihrem Azure Data Explorer-Clienttool ausführen, um die Tabelle und die Zuordnung zu erstellen.
>* Wenn Sie alle Daten im Container verwenden möchten, passen Sie das Skript an, und ändern Sie die URL in „https://your.blob.core.windows.net/containername;SecKey“.

## <a name="query-the-exported-data-from-azure-data-explorer"></a>Abfragen der exportierten Daten aus Azure Data Explorer 

Nach dem Konfigurieren der Zuordnung können Sie die exportierten Daten aus Azure Data Explorer abfragen. Für die Abfrage ist die Funktion [external_table](/azure/data-explorer/kusto/query/externaltablefunction) wie im folgenden Beispiel erforderlich.

```kusto
external_table("HBTest","map") | take 10000
```

[![Abfrage exportierter Log Analytics-Daten](media/azure-data-explorer-query-storage/external-table-query.png)](media/azure-data-explorer-query-storage/external-table-query.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Schreiben von Abfragen in Azure Data Explorer](/azure/data-explorer/write-queries).