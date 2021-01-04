---
title: Abrufen von Azure-VM-Nutzungsdaten mithilfe der REST-API
description: Verwenden Sie die Azure-REST-APIs zum Sammeln von Nutzungsmetriken für einen virtuellen Computer.
author: rloutlaw
ms.service: virtual-machines
ms.subservice: monitoring
ms.custom: REST
ms.topic: how-to
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 0a448d4b4538d62c1631cf16d88ec0a7e7f2a399
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508032"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Abrufen von VM-Nutzungsmetriken mithilfe der REST-API

In diesem Beispiel wird gezeigt, wie Sie die CPU-Nutzung für einen virtuellen Linux-Computer mithilfe der [Azure-REST-API](/rest/api/azure/) abrufen.

Eine vollständige Dokumentation und weitere Beispiele für die REST-API sind in der [Azure Monitor-REST-Referenz](/rest/api/monitor) verfügbar. 

## <a name="build-the-request"></a>Erstellen der Anforderung

Verwenden Sie die folgende GET-Anforderung zum Sammeln der [prozentualen CPU-Metrik](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) von einem virtuellen Computer.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
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
| resourceGroupName | Name der Azure-Ressourcengruppe, die der Ressource zugeordnet ist. Dieser Wert kann über die Azure Resource Manager-API, die Befehlszeilenschnittstelle oder das Portal abgerufen werden. |
| vmname | Name des virtuellen Azure-Computers. |
| metricnames | Durch Trennzeichen getrennte Liste der gültigen [Load Balancer-Metriken](../../load-balancer/load-balancer-standard-diagnostics.md). |
| api-version | API-Version, die für die Anforderung verwendet werden soll.<br /><br /> In diesem Dokument wird API-Version `2018-01-01` behandelt, die in der obigen URL enthalten ist.  |
| Zeitraum | Zeichenfolge im Format `startDateTime_ISO/endDateTime_ISO`, die den Zeitraum der zurückgegebenen Metriken definiert. Dieser optionale Parameter ist im Beispiel zum Zurückgeben eines gesamten Tages an Daten festgelegt. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Anforderungstext

Für diesen Vorgang ist kein Anforderungskörper erforderlich.

## <a name="handle-the-response"></a>Verarbeiten der Antwort

Wenn die Liste der Metrikwerte erfolgreich zurückgegeben wird, wird der Statuscode 200 zurückgegeben. Eine vollständige Liste der Fehlercodes finden Sie in der [Referenzdokumentation](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Beispielantwort 

```json
{
    "cost": 0,
    "timespan": "2018-06-08T23:48:10Z/2018-06-09T00:48:10Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "Percentage CPU",
                "localizedValue": "Percentage CPU"
            },
            "unit": "Percent",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-08T23:48:00Z",
                            "average": 0.44
                        },
                        {
                            "timeStamp": "2018-06-08T23:49:00Z",
                            "average": 0.31
                        },
                        {
                            "timeStamp": "2018-06-08T23:50:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:51:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:52:00Z",
                            "average": 0.285
                        } ]
                } ]
        } ]
}
```
