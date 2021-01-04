---
title: Azure-Dateifreigabe – Dateien wurden nicht aus Azure-Dateifreigabe gelöscht
description: Hier wird beschrieben, wie Sie den Fehler beim Löschen von Dateien aus der Azure-Dateifreigabe identifizieren und beheben.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 3d4f10745d90ccd83e7251af40d3e92a230f2fcd
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629681"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure-Dateifreigabe – Dateien wurden nicht aus Azure-Dateifreigabe gelöscht

Der Fehler beim Löschen von Dateien aus einer Azure-Dateifreigabe kann verschiedene Symptome haben:

**Symptom 1:**

Fehler beim Löschen einer Datei in der Azure-Dateifreigabe aufgrund eines der beiden folgenden Probleme:

* Für den Löschvorgang markierte Datei
* Angegebene Ressource wird möglicherweise von einem SMB-Client verwendet

**Symptom 2:**

Unzureichendes Kontingent für die Verarbeitung dieses Befehls

## <a name="cause"></a>Ursache

Der Fehler 1816 tritt auf, wenn Sie die Obergrenze der parallel offenen Handles erreichen, die für eine Datei auf dem Computer zulässig sind, auf dem die Dateifreigabe bereitgestellt wird. Weitere Informationen finden Sie unter [Checkliste zur Leistung und Skalierbarkeit von Azure Storage](../blobs/storage-performance-checklist.md).

## <a name="resolution"></a>Lösung

Reduzieren Sie die Anzahl von gleichzeitig geöffneten Handles, indem Sie einige Handles schließen.

## <a name="prerequisite"></a>Voraussetzung

### <a name="install-the-latest-azure-powershell-module"></a>Installieren des aktuellen Azure PowerShell-Moduls

* [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Stellen Sie eine Verbindung mit Azure her:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Wählen Sie das Abonnement für das Zielspeicherkonto aus:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Erstellen Sie den Kontext für das Zielspeicherkonto:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Rufen Sie die derzeit geöffneten Handles der Dateifreigabe ab:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Beispielergebnis:

|HandleId|`Path`|ClientIp|ClientPort|OpenTime|LastReconnectTime|FileId|ParentId|SessionID|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Neuer Ordner/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Schließen Sie ein geöffnetes Handle:

Verwenden Sie den folgenden Befehl, um ein geöffnetes Handle zu schließen:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Nächste Schritte

* [Behandeln von Azure Files-Problemen unter Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Behandeln von Azure Files-Problemen unter Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Problembehandlung für die Azure-Dateisynchronisierung](storage-sync-files-troubleshoot.md)