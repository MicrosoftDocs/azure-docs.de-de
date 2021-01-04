---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 2474b8920c5387c7896b413f229c2f5b06cdafb1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011182"
---
| Eigenschaft | BESCHREIBUNG |
|:--- |:---|
|**time** | Hierbei handelt es sich um die Uhrzeit gemäß koordinierter Weltzeit (UTC), zu der die Anforderung von Storage empfangen wurde. Beispiel: `2018/11/08 21:09:36.6900118`.|
|**Ressourcen-ID** | Hierbei handelt es sich um die Ressourcen-ID des Speicherkontos. Beispiel: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**category** | Hierbei handelt es sich um die Kategorie des angeforderten Vorgangs. Beispiel: `StorageRead`, `StorageWrite` oder `StorageDelete`.|
|**operationName** | Hierbei handelt es sich um den Typ des ausgeführten REST-Vorgangs. <br> Eine vollständige Liste der Vorgänge finden Sie im Thema [Mit Storage Analytics protokollierte Vorgänge und Statusmeldungen](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | Hierbei handelt es sich um die Speicherdienstversion, die beim Erstellen der Anforderung angegeben wurde. Diese entspricht dem Wert des **x-ms-version**-Headers. Beispiel: `2017-04-17`.|
|**schemaVersion** | Die Schemaversion des Protokolls Beispiel: `1.0`.|
|**statusCode** | Hierbei handelt es sich um den HTTP-Statuscode für die Anforderung. Wenn die Anforderung unterbrochen wurde, ist dieser Wert möglicherweise auf `Unknown` festgelegt. <br> Beispiel: `206` |
|**statusText** | Hierbei handelt es sich um den Status des angeforderten Vorgangs.  Eine vollständige Liste der Statusmeldungen finden Sie im Thema [Mit Storage Analytics protokollierte Vorgänge und Statusmeldungen](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). In Version 2017-04-17 und höher wird die Statusmeldung `ClientOtherError` nicht verwendet. Stattdessen enthält dieses Feld einen Fehlercode. Beispiel: `SASSuccess`  |
|**durationMs** | Hierbei handelt es sich um die zum Ausführen des angeforderten Vorgangs benötigte Gesamtzeit in Millisekunden. Dieser Wert schließt die Zeit mit ein, die für das Lesen der eingehenden Anforderung und das Senden der Antwort an den Anforderer benötigt wurde. Beispiel: `12`.|
|**callerIpAddress** | Hierbei handelt es sich um die IP-Adresse des Anforderers einschließlich der Portnummer. Beispiel: `192.100.0.102:4362`. |
|**correlationId** | Hierbei handelt es sich um die ID, die zum ressourcenübergreifenden Korrelieren von Protokollen verwendet wird. Beispiel: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**location** | Hierbei handelt es sich um den Speicherort des Speicherkontos. Beispiel: `North Europe`. |
|**protocol**|Hierbei handelt es sich um das für den Vorgang verwendete Protokoll, z. B. `HTTP`, `HTTPS`, `SMB` oder `NFS`.|
| **uri** | Hierbei handelt es sich um den angeforderten Uniform Resource Identifier. |