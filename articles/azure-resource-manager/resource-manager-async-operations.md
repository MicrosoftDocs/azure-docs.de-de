---
title: Status asynchroner Vorgänge – Azure Resource Manager
description: Beschreibt, wie asynchrone Vorgänge in Azure nachverfolgt werden. Es werden die Werte gezeigt, die Sie verwenden, um den Status eines Vorgangs mit langer Ausführungsdauer abzurufen.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 1b05ed50f08ddbf2eb5da8e08f5bf623596e1f9b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61061372"
---
# <a name="track-asynchronous-azure-operations"></a>Nachverfolgen asynchroner Vorgänge in Azure
Einige Azure-REST-Vorgänge werden asynchron ausgeführt, da sie nicht schnell abgeschlossen werden können. In diesem Artikel wird beschrieben, wie der Status asynchroner Vorgänge mithilfe von Werten nachverfolgt wird, die in der Antwort zurückgegeben werden.  

## <a name="status-codes-for-asynchronous-operations"></a>Statuscodes für asynchrone Vorgänge
Ein asynchroner Vorgang gibt anfänglich einen dieser HTTP-Statuscodes zurück:

* 201 (Erstellt)
* 202 (Akzeptiert) 

Wenn der Vorgang erfolgreich abgeschlossen wurde, wird einer dieser Statuscodes zurückgegeben:

* 200 (OK)
* 204 (Kein Inhalt) 

In der [REST-API-Dokumentation](/rest/api/) finden Sie Informationen zu den Antworten für den Vorgang, den Sie ausführen.

## <a name="monitor-status-of-operation"></a>Überwachen des Status eines Vorgangs
Die asynchronen REST-Vorgänge geben Headerwerte zurück, mit deren Hilfe Sie den Status des jeweiligen Vorgangs bestimmen. Es gibt potenziell drei zu untersuchende Headerwerte:

* `Azure-AsyncOperation`: URL zur Überprüfung des aktuellen Status des Vorgangs. Wenn der Vorgang diesen Wert zurückgibt, verwenden Sie ihn stets (anstelle von „Location“), um den Status des Vorgangs nachzuverfolgen.
* `Location`: URL zum Bestimmen, wann ein Vorgang abgeschlossen wurde. Verwenden Sie diesen Wert nur, wenn „Azure-AsyncOperation“ nicht zurückgegeben wird.
* `Retry-After`: Abzuwartende Anzahl von Sekunden vor dem Überprüfen des Status des asynchronen Vorgangs.

Nicht jeder asynchrone Vorgang gibt jedoch alle diese Werte zurück. Sie müssen beispielsweise den Headerwert „Azure-AsyncOperation“ für einen Vorgang und den Headerwert „Location“ für einen anderen Vorgang auswerten. 

Die Headerwerte werden wie beliebige andere Headerwerte für eine Anforderung abgerufen. Beispiel: In C# rufen Sie den Headerwert aus einem `HttpWebResponse`-Objekt mit dem Namen `response` mit dem folgenden Code ab:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Anforderung „Azure-AsyncOperation“ und Antwort

Um den Status des asynchronen Vorgangs zu erhalten, senden Sie eine GET-Anforderung an die URL im Headerwert „Azure-AsyncOperation“.

Der Text der Antwort aus diesem Vorgang enthält Informationen zum Vorgang. Das folgende Beispiel zeigt die möglichen Werte, die vom Vorgang zurückgegeben werden:

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}", 
    "status" : "Succeeded | Failed | Canceled | {resource provider values}", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : { 
        "code": "{error code}",  
        "message": "{error description}" 
    }
}
```

Nur `status` wird für alle Antworten zurückgegeben. Das Fehlerobjekt wird zurückgegeben, wenn der Status „Failed“ oder „Canceled“ ist. Alle anderen Werte sind optional, weshalb die Antwort, die Sie erhalten, sich vom Beispiel unterscheiden kann.

## <a name="provisioningstate-values"></a>„provisioningState“-Werte

Vorgänge zum Erstellen, Aktualisieren oder Löschen (PUT, PATCH, DELETE) einer Ressource geben in der Regel einen `provisioningState`-Wert zurück. Wenn ein Vorgang abgeschlossen wurde, wird einer der folgenden drei Werte zurückgegeben: 

* Succeeded
* Failed
* Canceled

Alle anderen Werte bedeuten, dass der Vorgang noch ausgeführt wird. Der Ressourcenanbieter kann einen benutzerdefinierten Wert zurückgeben, der seinen Status angibt. Sie erhalten möglicherweise **Accepted**, wenn die Anforderung empfangen wurde und ausgeführt wird.

## <a name="example-requests-and-responses"></a>Beispielanforderungen und -antworten

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Starten des virtuellen Computers (202 bei „Azure-AsyncOperation“)
In diesem Beispiel wird gezeigt, wie der Status des Vorgangs **Starten** für virtuelle Computer bestimmt wird. Die ursprüngliche Anforderung hat das folgende Format:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Sie gibt den Statuscode 202 zurück. Unter den Headerwerten finden Sie:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Um den Status des asynchronen Vorgangs zu überprüfen, senden Sie eine weitere Anforderung an diese URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Der Antworttext enthält den Status des Vorgangs:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Bereitstellen von Ressourcen (201 bei „Azure-AsyncOperation“)

In diesem Beispiel wird gezeigt, wie der Status des Vorgangs **Bereitstellung** für die Bereitstellung von Ressourcen in Azure bestimmt wird. Die ursprüngliche Anforderung hat das folgende Format:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Sie gibt den Statuscode 201 zurück. Der Antworttext enthält Folgendes:

```json
"provisioningState":"Accepted",
```

Unter den Headerwerten finden Sie:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Um den Status des asynchronen Vorgangs zu überprüfen, senden Sie eine weitere Anforderung an diese URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Der Antworttext enthält den Status des Vorgangs:

```json
{"status":"Running"}
```

Wenn die Bereitstellung abgeschlossen ist, enthält die Antwort Folgendes:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Erstellen eines Speicherkontos (202 bei „Location“ und „Retry-After“)

In diesem Beispiel wird gezeigt, wie der Status des Vorgangs **Erstellen** für Speicherkonten bestimmt wird. Die ursprüngliche Anforderung hat das folgende Format:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

Und der Anforderungstext enthält Eigenschaften für das Speicherkonto:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Sie gibt den Statuscode 202 zurück. In den Headerwerten finden Sie die folgenden beiden Werte:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Überprüfen Sie nach Abwarten der in „Retry-After“ angegebenen Anzahl von Sekunden den Status des asynchronen Vorgangs, indem Sie eine weitere Anforderung an diese URL senden.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Wenn die Anforderung noch ausgeführt wird, erhalten Sie den Statuscode 202. Wenn die Anforderung abgeschlossen wurde, erhalten Sie den Statuscode 200, wobei der Text der Anforderung die Eigenschaften des erstellten Speicherkontos enthält.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu den einzelnen REST-Vorgängen finden Sie in der [REST-API-Dokumentation](/rest/api/).
* Informationen zum Bereitstellen von Vorlagen mit der Ressourcen-Manager-REST-API finden Sie unter [Bereitstellen von Ressourcen mit Ressourcen-Manager-Vorlagen und der Ressourcen-Manager-REST-API](resource-group-template-deploy-rest.md).