---
title: Lesen von NSG-Datenflussprotokollen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Azure PowerShell Netzwerksicherheitsgruppen-Datenflussprotokolle analysieren, die stündlich erstellt und alle paar Minuten in Azure Network Watcher aktualisiert werden.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: damendo
ms.openlocfilehash: 1f9b9e91cda93a986fdaaf0f53d8987544e783a2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966461"
---
# <a name="read-nsg-flow-logs"></a>Lesen von NSG-Datenflussprotokollen

Hier erfahren Sie, wie NSG-Datenflussprotokolle mit PowerShell gelesen werden.

NSG-Datenflussprotokolle werden in einem Speicherkonto in [Blockblobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) gespeichert. Blockblobs setzen sich aus kleineren Blöcken zusammen. Jedes Protokoll stellt einen separaten Blockblob dar, der einmal pro Stunde generiert wird. Neue Protokolle werden stündlich generiert. Die Protokolle werden anhand der neuesten Daten alle paar Minuten mit neuen Einträgen aktualisiert. In diesem Artikel erfahren Sie, wie Sie Teile der Datenflussprotokolle lesen können.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Szenario

Im folgenden Beispiel liegt ein Beispiel-Datenflussprotokoll vor, das in einem Speicherkonto gespeichert ist. Sie erfahren, wie Sie selektiv die aktuellen Ereignisse in NSG-Datenflussprotokollen lesen. In diesem Artikel verwenden Sie PowerShell. Die erörterten Konzepte sind jedoch nicht auf diese Programmiersprache beschränkt und gelten für alle Sprachen, die von den Azure Storage-APIs unterstützt werden.

## <a name="setup"></a>Einrichten

Bevor Sie beginnen, müssen Sie die NSG-Datenflussprotokollierung für mindestens eine Netzwerksicherheitsgruppe Ihres Kontos aktiviert haben. Eine Anleitung zum Aktivieren von Netzwerksicherheits-Flowprotokollen finden Sie in folgendem Artikel: [Einführung in die Datenflussprotokollierung für Netzwerksicherheitsgruppen](network-watcher-nsg-flow-logging-overview.md).

## <a name="retrieve-the-block-list"></a>Abrufen der Liste der Blöcke

Mit dem folgenden PowerShell-Skript werden die erforderlichen Variablen zum Abfragen des Blobs mit dem NSG-Datenflussprotokoll eingerichtet, und die Blöcke im Blockblob [CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob) werden aufgelistet. Aktualisieren Sie das Skript so, dass es gültige Werte für Ihre Umgebung enthält.

```powershell
function Get-NSGFlowLogCloudBlockBlob {
    [CmdletBinding()]
    param (
        [string] [Parameter(Mandatory=$true)] $subscriptionId,
        [string] [Parameter(Mandatory=$true)] $NSGResourceGroupName,
        [string] [Parameter(Mandatory=$true)] $NSGName,
        [string] [Parameter(Mandatory=$true)] $storageAccountName,
        [string] [Parameter(Mandatory=$true)] $storageAccountResourceGroup,
        [string] [Parameter(Mandatory=$true)] $macAddress,
        [datetime] [Parameter(Mandatory=$true)] $logTime
    )

    process {
        # Retrieve the primary storage account key to access the NSG logs
        $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccountResourceGroup -Name $storageAccountName).Value[0]

        # Setup a new storage context to be used to query the logs
        $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

        # Container name used by NSG flow logs
        $ContainerName = "insights-logs-networksecuritygroupflowevent"

        # Name of the blob that contains the NSG flow log
        $BlobName = "resourceId=/SUBSCRIPTIONS/${subscriptionId}/RESOURCEGROUPS/${NSGResourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/${NSGName}/y=$($logTime.Year)/m=$(($logTime).ToString("MM"))/d=$(($logTime).ToString("dd"))/h=$(($logTime).ToString("HH"))/m=00/macAddress=$($macAddress)/PT1H.json"

        # Gets the storage blog
        $Blob = Get-AzStorageBlob -Context $ctx -Container $ContainerName -Blob $BlobName

        # Gets the block blog of type 'Microsoft.Azure.Storage.Blob.CloudBlob' from the storage blob
        $CloudBlockBlob = [Microsoft.Azure.Storage.Blob.CloudBlockBlob] $Blob.ICloudBlob

        #Return the Cloud Block Blob
        $CloudBlockBlob
    }
}

function Get-NSGFlowLogBlockList  {
    [CmdletBinding()]
    param (
        [Microsoft.Azure.Storage.Blob.CloudBlockBlob] [Parameter(Mandatory=$true)] $CloudBlockBlob
    )
    process {
        # Stores the block list in a variable from the block blob.
        $blockList = $CloudBlockBlob.DownloadBlockListAsync()

        # Return the Block List
        $blockList
    }
}


$CloudBlockBlob = Get-NSGFlowLogCloudBlockBlob -subscriptionId "yourSubscriptionId" -NSGResourceGroupName "FLOWLOGSVALIDATIONWESTCENTRALUS" -NSGName "V2VALIDATIONVM-NSG" -storageAccountName "yourStorageAccountName" -storageAccountResourceGroup "ml-rg" -macAddress "000D3AF87856" -logTime "11/11/2018 03:00" 

$blockList = Get-NSGFlowLogBlockList -CloudBlockBlob $CloudBlockBlob
```

Die `$blockList`-Variable gibt eine Liste der Blöcke im Blob zurück. Jeder Blockblob enthält mindestens zwei Blöcke.  Der erste Block weist eine Länge von `12` Byte auf; dieser Block enthält die öffnenden Klammern des json-Protokolls. Der andere Block enthält die schließenden Klammern und hat eine Länge von `2` Byte.  Wie Sie sehen, enthält das folgende Beispielprotokoll sieben Einträge, von denen jeder einen Einzeleintrag darstellt. Alle neuen Einträge im Protokoll werden am Ende unmittelbar vor dem letzten Block hinzugefügt.

```
Name                                         Length Committed
----                                         ------ ---------
ZDk5MTk5N2FkNGE0MmY5MTk5ZWViYjA0YmZhODRhYzY=     12      True
NzQxNDA5MTRhNDUzMGI2M2Y1MDMyOWZlN2QwNDZiYzQ=   2685      True
ODdjM2UyMWY3NzFhZTU3MmVlMmU5MDNlOWEwNWE3YWY=   2586      True
ZDU2MjA3OGQ2ZDU3MjczMWQ4MTRmYWNhYjAzOGJkMTg=   2688      True
ZmM3ZWJjMGQ0ZDA1ODJlOWMyODhlOWE3MDI1MGJhMTc=   2775      True
ZGVkYTc4MzQzNjEyMzlmZWE5MmRiNjc1OWE5OTc0OTQ=   2676      True
ZmY2MjUzYTIwYWIyOGU1OTA2ZDY1OWYzNmY2NmU4ZTY=   2777      True
Mzk1YzQwM2U0ZWY1ZDRhOWFlMTNhYjQ3OGVhYmUzNjk=   2675      True
ZjAyZTliYWE3OTI1YWZmYjFmMWI0MjJhNzMxZTI4MDM=      2      True
```

## <a name="read-the-block-blob"></a>Lesen des Blockblobs

Nun müssen Sie die `$blocklist`-Variable lesen, um die Daten abzurufen. In diesem Beispiel wird die Liste der Blöcke durchlaufen, die Bytes werden aus den einzelnen Blöcken gelesen und in einem Array gespeichert. Rufen Sie die Daten mit der [DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadrangetobytearray)-Methode ab.

```powershell
function Get-NSGFlowLogReadBlock  {
    [CmdletBinding()]
    param (
        [System.Array] [Parameter(Mandatory=$true)] $blockList,
        [Microsoft.Azure.Storage.Blob.CloudBlockBlob] [Parameter(Mandatory=$true)] $CloudBlockBlob

    )
    # Set the size of the byte array to the largest block
    $maxvalue = ($blocklist | measure Length -Maximum).Maximum

    # Create an array to store values in
    $valuearray = @()

    # Define the starting index to track the current block being read
    $index = 0

    # Loop through each block in the block list
    for($i=0; $i -lt $blocklist.count; $i++)
    {
        # Create a byte array object to story the bytes from the block
        $downloadArray = New-Object -TypeName byte[] -ArgumentList $maxvalue

        # Download the data into the ByteArray, starting with the current index, for the number of bytes in the current block. Index is increased by 3 when reading to remove preceding comma.
        $CloudBlockBlob.DownloadRangeToByteArray($downloadArray,0,$index, $($blockList[$i].Length)) | Out-Null

        # Increment the index by adding the current block length to the previous index
        $index = $index + $blockList[$i].Length

        # Retrieve the string from the byte array

        $value = [System.Text.Encoding]::ASCII.GetString($downloadArray)

        # Add the log entry to the value array
        $valuearray += $value
    }
    #Return the Array
    $valuearray
}
$valuearray = Get-NSGFlowLogReadBlock -blockList $blockList -CloudBlockBlob $CloudBlockBlob
```

Nun enthält das `$valuearray`-Array den Zeichenfolgenwert jedes Blocks. Um den Eintrag zu überprüfen, rufen Sie den zweitletzten Wert aus dem Array ab. Führen Sie dazu `$valuearray[$valuearray.Length-2]` aus. Der letzte Wert ist die schließende Klammer und daher von Ihnen nicht gewünscht.

Die Ergebnisse dieses Werts werden im folgenden Beispiel gezeigt:

```json
        {
             "time": "2017-06-16T20:59:43.7340000Z",
             "systemId": "5f4d02d3-a7d0-4ed4-9ce8-c0ae9377951c",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/CONTOSORG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/CONTOSONSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_AllowInternetOutBound","flows":[{"mac":"000D3A18077E","flowTuples":["1497646722,10.0.0.4,168.62.32.14,44904,443,T,O,A","1497646722,10.0.0.4,52.240.48.24,45218,443,T,O,A","1497646725,10.
0.0.4,168.62.32.14,44910,443,T,O,A","1497646725,10.0.0.4,52.240.48.24,45224,443,T,O,A","1497646728,10.0.0.4,168.62.32.14,44916,443,T,O,A","1497646728,10.0.0.4,52.240.48.24,45230,443,T,O,A","1497646732,10.0.0.4,168.62.32.14,44922,443,T,O,A","14976
46732,10.0.0.4,52.240.48.24,45236,443,T,O,A","1497646735,10.0.0.4,168.62.32.14,44928,443,T,O,A","1497646735,10.0.0.4,52.240.48.24,45242,443,T,O,A","1497646738,10.0.0.4,168.62.32.14,44934,443,T,O,A","1497646738,10.0.0.4,52.240.48.24,45248,443,T,O,
A","1497646742,10.0.0.4,168.62.32.14,44942,443,T,O,A","1497646742,10.0.0.4,52.240.48.24,45256,443,T,O,A","1497646745,10.0.0.4,168.62.32.14,44948,443,T,O,A","1497646745,10.0.0.4,52.240.48.24,45262,443,T,O,A","1497646749,10.0.0.4,168.62.32.14,44954
,443,T,O,A","1497646749,10.0.0.4,52.240.48.24,45268,443,T,O,A","1497646753,10.0.0.4,168.62.32.14,44960,443,T,O,A","1497646753,10.0.0.4,52.240.48.24,45274,443,T,O,A","1497646756,10.0.0.4,168.62.32.14,44966,443,T,O,A","1497646756,10.0.0.4,52.240.48
.24,45280,443,T,O,A","1497646759,10.0.0.4,168.62.32.14,44972,443,T,O,A","1497646759,10.0.0.4,52.240.48.24,45286,443,T,O,A","1497646763,10.0.0.4,168.62.32.14,44978,443,T,O,A","1497646763,10.0.0.4,52.240.48.24,45292,443,T,O,A","1497646766,10.0.0.4,
168.62.32.14,44984,443,T,O,A","1497646766,10.0.0.4,52.240.48.24,45298,443,T,O,A","1497646769,10.0.0.4,168.62.32.14,44990,443,T,O,A","1497646769,10.0.0.4,52.240.48.24,45304,443,T,O,A","1497646773,10.0.0.4,168.62.32.14,44996,443,T,O,A","1497646773,
10.0.0.4,52.240.48.24,45310,443,T,O,A","1497646776,10.0.0.4,168.62.32.14,45002,443,T,O,A","1497646776,10.0.0.4,52.240.48.24,45316,443,T,O,A","1497646779,10.0.0.4,168.62.32.14,45008,443,T,O,A","1497646779,10.0.0.4,52.240.48.24,45322,443,T,O,A"]}]}
,{"rule":"DefaultRule_DenyAllInBound","flows":[]},{"rule":"UserRule_ssh-rule","flows":[]},{"rule":"UserRule_web-rule","flows":[{"mac":"000D3A18077E","flowTuples":["1497646738,13.82.225.93,10.0.0.4,1180,80,T,I,A","1497646750,13.82.225.93,10.0.0.4,
1184,80,T,I,A","1497646768,13.82.225.93,10.0.0.4,1181,80,T,I,A","1497646780,13.82.225.93,10.0.0.4,1336,80,T,I,A"]}]}]}
        }
```

Anhand dieses Szenarios wird beispielhaft veranschaulicht, wie Einträge in NSG-Datenflussprotokollen gelesen werden, ohne dass das gesamte Protokoll analysiert werden muss. Sie können neue Einträge im Protokoll lesen, da diese unter Verwendung der Block-ID geschrieben wurden. Sie können dazu auch die Länge der im Blockblob gespeicherten Blöcke verfolgen. Dadurch können Sie ausschließlich die neuen Einträge lesen.

## <a name="next-steps"></a>Nächste Schritte


Unter [Verwenden von Elastic Stack](network-watcher-visualize-nsg-flow-logs-open-source-tools.md), [Verwenden von Grafana](network-watcher-nsg-grafana.md) und [Verwenden von Graylog](network-watcher-analyze-nsg-flow-logs-graylog.md) erfahren Sie mehr zu den Möglichkeiten zum Anzeigen von NSG-Flussprotokollen. Ein Open-Source-Ansatz für Azure Functions, um die Blobs direkt zu nutzen und an verschiedene Consumer von Protokollanalysen zu senden, finden Sie hier: [AzureNetworkWatcherNSGFlowLogsConnector](https://github.com/Microsoft/AzureNetworkWatcherNSGFlowLogsConnector).

Mit [Azure Traffic Analytics](./traffic-analytics.md) können Sie Einblicke in Ihren Datenverkehrsfluss erhalten. Traffic Analytics verwendet [Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md), um den Datenverkehrsfluss abzufragen.

Weitere Informationen zu Speicherblobs erhalten Sie im Artikel: [Azure Blob Storage-Bindungen für Azure Functions](../azure-functions/functions-bindings-storage-blob.md).