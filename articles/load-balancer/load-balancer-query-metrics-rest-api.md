---
title: Abrufen von Metriken über die REST-API
titleSuffix: Azure Load Balancer
description: In diesem Artikel erfahren Sie die ersten Schritte zur Verwendung der Azure REST-APIs, um Metriken zur Integrität und Nutzung für Azure Load Balancer zu erfassen.
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: how-to
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: fbf21ba359ae7914acd2d812ec9e12f9f3ee557a
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511942"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>Abrufen von Metriken zur Load Balancer-Nutzung über die REST-API

Erfassen Sie die Anzahl der Bytes, die von einem [Load Balancer Standard](./load-balancer-overview.md) für ein Zeitintervall mithilfe der [Azure REST-API](/rest/api/azure/) verarbeitet werden.

Eine vollständige Dokumentation und weitere Beispiele für die REST-API sind in der [Azure Monitor-REST-Referenz](/rest/api/monitor) verfügbar. 

## <a name="build-the-request"></a>Erstellen der Anforderung

Verwenden Sie die folgende GET-Anforderung zum Sammeln der [ByteCount-Metrik](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics) von einem Load Balancer Standard. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Anforderungsheader

Die folgenden Header sind erforderlich: 

|Anforderungsheader|BESCHREIBUNG|  
|--------------------|-----------------|  
|*Content-Type:*|Erforderlich. Legen Sie diese Option auf `application/json` fest.|  
|*Authorization:*|Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-[Zugriffstoken](/rest/api/azure/#authorization-code-grant-interactive-clients) fest. |  

### <a name="uri-parameters"></a>URI-Parameter

| Name | BESCHREIBUNG |
| :--- | :---------- |
| subscriptionId | Abonnement-ID für ein Azure-Abonnement. Wenn Sie über mehrere Abonnements verfügen, lesen Sie [Verwenden mehrerer Abonnements](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest): |
| resourceGroupName | Der Name der Ressourcengruppe, die die Ressource enthält. Dieser Wert kann über die Azure Resource Manager-API, Befehlszeilenschnittstelle oder das Portal abgerufen werden. |
| loadBalancerName | Der Azure Load Balancer-Name. |
| Metriknamen | Durch Trennzeichen getrennte Liste der gültigen [Load Balancer-Metriken](./load-balancer-standard-diagnostics.md). |
| api-version | API-Version, die für die Anforderung verwendet werden soll.<br /><br /> In diesem Dokument wird API-Version `2018-01-01` behandelt, die in der obigen URL enthalten ist.  |
| Zeitraum | Die Zeitspanne der Abfrage. Es handelt sich um eine Zeichenfolge mit dem folgenden Format `startDateTime_ISO/endDateTime_ISO`. Dieser optionale Parameter ist im Beispiel zum Zurückgeben eines gesamten Tages an Daten festgelegt. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Anforderungstext

Für diesen Vorgang ist kein Anforderungskörper erforderlich.

## <a name="handle-the-response"></a>Verarbeiten der Antwort

Wenn die Liste der Metrikwerte erfolgreich zurückgegeben wird, wird der Statuscode 200 zurückgegeben. Eine vollständige Liste der Fehlercodes finden Sie in der [Referenzdokumentation](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Beispielantwort 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```