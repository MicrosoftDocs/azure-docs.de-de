---
title: Abrufen von Integritätsereignissen für Azure-Ressourcen mithilfe der REST-API | Microsoft-Dokumentation
description: Verwenden Sie die Azure-REST-APIs, um die Integritätsereignisse für Ihre Azure-Ressourcen abzurufen.
author: stephbaron
ms.author: stbaron
ms.service: service-health
ms.custom: REST
ms.topic: article
ms.date: 06/06/2017
ms.openlocfilehash: 6d83aed6910127ceb34b9a694f48ca9c19ab6d18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60790911"
---
# <a name="get-resource-health-using-the-rest-api"></a>Abrufen der Ressourcenintegrität mithilfe der REST-API 

In diesem Artikel wird anhand von Beispielen gezeigt, wie Sie mithilfe der [Azure-REST-API](/rest/api/azure/) eine Liste von Integritätsereignissen für die Azure-Ressourcen in Ihrem Abonnement abrufen.

Eine vollständige Dokumentation und weitere Beispiele für die REST-API sind in der [Azure Monitor-REST-Referenz](/rest/api/monitor) verfügbar. 

## <a name="build-the-request"></a>Erstellen der Anforderung

Verwenden Sie die folgende `GET`-HTTP-Abfrage, um die Integritätsereignisse für Ihr Abonnement für den Zeitraum zwischen dem `2018-05-16` und dem `2018-06-20` aufzulisten.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Anforderungsheader

Die folgenden Header sind erforderlich: 

|Anforderungsheader|BESCHREIBUNG|  
|--------------------|-----------------|  
|*Content-Type:*|Erforderlich. Legen Sie diese Option auf `application/json` fest.|  
|*Authorization:*|Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer` [Zugriffstoken](/rest/api/azure/#authorization-code-grant-interactive-clients) fest. |  

### <a name="uri-parameters"></a>URI-Parameter

| NAME | BESCHREIBUNG |
| :--- | :---------- |
| subscriptionId | Abonnement-ID für ein Azure-Abonnement. Wenn Sie über mehrere Abonnements verfügen, lesen Sie [Verwenden mehrerer Abonnements](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest): |
| api-version | API-Version, die für die Anforderung verwendet werden soll.<br /><br /> In diesem Dokument wird API-Version `2015-04-01` behandelt, die in der obigen URL enthalten ist.  |
| $filter | Die Filteroption, um die Anzahl der zurückgegebenen Ergebnisse zu verringern. Die zulässigen Muster für diesen Parameter sind [in der Referenz für den Vorgang der Aktivitätsprotokolle](/rest/api/monitor/activitylogs/list#uri-parameters) verfügbar. Das gezeigte Beispiel erfasst alle Ereignisse in einem Zeitraum zwischen den Daten „2018-05-16“ und „2018-06-20“. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Anforderungstext

Für diesen Vorgang ist kein Anforderungskörper erforderlich.

## <a name="handle-the-response"></a>Verarbeiten der Antwort

Der Statuscode 200 wird mit einer Liste von Integritätsereigniswerten zurückgegeben, die dem Filterparameter entsprechen, zusammen mit einer URI `nextlink`, um die nächste Seite der Ergebnisse abzurufen.

## <a name="example-response"></a>Beispielantwort 

```json
{
  "value": [
    {
      "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
      "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
      },
      "id": "/subscriptions/{subscription-id}/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
      "resourceGroupName": "MSSupportGroup",
      "resourceProviderName": {
        "value": "microsoft.support",
        "localizedValue": "microsoft.support"
      },
      "operationName": {
        "value": "microsoft.support/supporttickets/write",
        "localizedValue": "microsoft.support/supporttickets/write"
      },
      "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
      },
      "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
      "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
      "level": "Informational"
    }
  ],
  "nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```
