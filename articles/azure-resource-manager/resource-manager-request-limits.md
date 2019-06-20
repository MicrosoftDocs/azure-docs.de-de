---
title: Grenzwerte und Drosselung für Anforderungen – Azure Resource Manager
description: Beschreibt, wie eine Begrenzung von Azure Resource Manager-Anforderungen genutzt wird, wenn Abonnementgrenzwerte erreicht wurden.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: b382b9ae35d492b4c779b8f7ee360fb378d54e08
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399710"
---
# <a name="throttling-resource-manager-requests"></a>Begrenzen von Resource Manager-Anforderungen

Für jedes Azure-Abonnement und jeden Azure-Mandanten erlaubt Resource Manager pro Stunde bis zu 12.000 Leseanforderungen und bis zu 1.200 Schreibanforderungen. Diese Grenzwerte gelten für die Prinzipal-ID, von der die Anforderungen stammen, sowie für die Abonnement-ID bzw. die Mandanten-ID. Falls Ihre Anforderungen von mehreren Prinzipal-IDs stammen, liegen die Grenzwerte für das Abonnement/den Mandanten über 12.000 bzw. 1.200 Anforderungen pro Stunde.

Anforderungen werden entweder auf Ihr Abonnement oder auf Ihren Mandanten angewendet. Bei Abonnementanforderungen wird Ihre Abonnement-ID übergeben, z. B. beim Abrufen der Ressourcengruppen in Ihrem Abonnement. In Mandantenanforderungen ist Ihre Abonnement-ID nicht enthalten (etwa beim Abrufen gültiger Azure-Standorte).

Diese Grenzwerte gelten für jede Azure Resource Manager-Instanz. In jeder Azure-Region sind mehrere Instanzen vorhanden, und Azure Resource Manager wird in allen Azure-Regionen bereitgestellt.  In der Praxis sind die Grenzwerte also effektiv sehr viel höher als die aufgeführten Grenzwerte, da Benutzeranforderungen in der Regel von vielen verschiedenen Instanzen verarbeitet werden.

Wenn Ihre Anwendung oder Ihr Skript diese Grenzwerte erreicht, müssen Sie die Anforderungen begrenzen. In diesem Artikel erfahren Sie, wie Sie Ihre verbleibenden Anforderungen bestimmen können, bevor der Grenzwert erreicht wird, und wie Sie reagieren können, wenn Sie den Grenzwert erreicht haben.

Wenn Sie den Grenzwert erreichen, erhalten Sie den HTTP-Statuscode **429 Zu viele Anforderungen**.

Azure Resource Graph begrenzt die Anzahl der Anforderungen nach seinen Vorgängen. Die Schritte in diesem Artikel zum Bestimmen der verbleibenden Anforderungen und zum Reagieren bei Erreichen des Grenzwerts gelten auch für Resource Graph. Resource Graph legt jedoch einen eigenen Grenzwert und eine eigene Rücksetzrate fest. Weitere Informationen finden Sie unter [Drosselung in Azure Resource Graph](../governance/resource-graph/overview.md#throttling).

## <a name="remaining-requests"></a>Verbleibende Anforderungen
Sie können die Anzahl der verbleibenden Anforderungen durch Untersuchen der Antwortheader bestimmen. Leseanforderungen geben im Header einen Wert für die Anzahl der verbleibenden Leseanforderungen zurück. Schreibanforderungen enthalten einen Wert für die Anzahl der verbleibenden Schreibanforderungen. Die folgende Tabelle beschreibt die Antwortheader, die Sie auf diese Werte untersuchen können:

| Antwortheader | BESCHREIBUNG |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Verbleibende abonnementbezogene Lesevorgänge. Dieser Wert wird für Lesevorgänge zurückgegeben. |
| x-ms-ratelimit-remaining-subscription-writes |Verbleibende abonnementbezogene Schreibvorgänge. Dieser Wert wird für Schreibvorgänge zurückgegeben. |
| x-ms-ratelimit-remaining-tenant-reads |Verbleibende mandantenbezogene Lesevorgänge |
| x-ms-ratelimit-remaining-tenant-writes |Verbleibende mandantenbezogene Schreibvorgänge |
| x-ms-ratelimit-remaining-subscription-resource-requests |Verbleibende abonnementbezogene Ressourcenanforderungen.<br /><br />Dieser Headerwert wird nur zurückgegeben, wenn ein Dienst den standardmäßigen Grenzwert außer Kraft gesetzt hat. Resource Manager fügt diesen Wert anstelle der Lese- oder Schreibvorgänge des Abonnements hinzu. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Verbleibende abonnementbezogene Ressourcensammlungsanforderungen.<br /><br />Dieser Headerwert wird nur zurückgegeben, wenn ein Dienst den standardmäßigen Grenzwert außer Kraft gesetzt hat. Dieser Wert gibt die Anzahl der verbleibenden Sammlungsanforderungen an (Auflisten von Ressourcen). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Verbleibende mandantenbezogene Ressourcenanforderungen.<br /><br />Dieser Header wird nur für Anforderungen auf Mandantenebene hinzugefügt, und nur dann, wenn ein Dienst den standardmäßigen Grenzwert außer Kraft gesetzt hat. Resource Manager fügt diesen Wert anstelle der Lese- oder Schreibvorgänge des Mandanten hinzu. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Verbleibende mandantenbezogene Ressourcensammlungsanforderungen.<br /><br />Dieser Header wird nur für Anforderungen auf Mandantenebene hinzugefügt, und nur dann, wenn ein Dienst den standardmäßigen Grenzwert außer Kraft gesetzt hat. |

## <a name="retrieving-the-header-values"></a>Abrufen der Headerwerte
Das Abrufen dieser Headerwerte in Ihrem Code oder Skript unterscheidet sich nicht vom Abrufen anderer Headerwerte. 

Beispiel: In **C#** rufen Sie den Headerwert aus einem **HttpWebResponse**-Objekt mit dem Namen **response** mit dem folgenden Code ab:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

In **PowerShell** rufen Sie den Headerwert aus einem Invoke-WebRequest-Vorgang ab.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Ein vollständiges PowerShell-Beispiel finden Sie unter [Check ARM Limits for a Given Subscription](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI) (Überprüfen der ARM-Grenzwerte für ein bestimmtes Abonnement).

Wenn Sie zum Debuggen die übrigen Anforderungen anzeigen möchten, können Sie in Ihrem **PowerShell**-Cmdlet den Parameter **-Debug** angeben.

```powershell
Get-AzResourceGroup -Debug
```

So wird eine Vielzahl von Werten zurückgegeben, einschließlich des folgenden Antwortwerts:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Um den Schreibgrenzwert abzurufen, verwenden Sie einen Schreibvorgang: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

So wird eine Vielzahl von Werten zurückgegeben, einschließlich der folgenden Werte:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

In der **Azure-CLI** rufen Sie den Headerwert mithilfe der ausführlicheren Option ab.

```azurecli
az group list --verbose --debug
```

So wird eine Vielzahl von Werten zurückgegeben, einschließlich der folgenden Werte:

```azurecli
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Um den Schreibgrenzwert abzurufen, verwenden Sie einen Schreibvorgang: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

So wird eine Vielzahl von Werten zurückgegeben, einschließlich der folgenden Werte:

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="waiting-before-sending-next-request"></a>Warten vor dem Senden der nächsten Anforderung
Wenn der Grenzwert für Anforderungen erreicht ist, gibt Resource Manager den HTTP-Statuscode **429** und einen **Retry-After** Wert im Header zurück. Der **Retry-After**-Wert gibt die Anzahl von Sekunden an, die Ihre Anwendung warten muss (oder im Ruhezustand verbleibt), bevor die nächste Anforderung gesendet wird. Wenn Sie eine Anforderung senden, bevor der Retry-Wert verstrichen ist, wird Ihre Anforderung nicht verarbeitet, und es wird ein neuer Retry-Wert zurückgegeben.

## <a name="next-steps"></a>Nächste Schritte

* Ein vollständiges PowerShell-Beispiel finden Sie unter [Check ARM Limits for a Given Subscription](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI) (Überprüfen der ARM-Grenzwerte für ein bestimmtes Abonnement).
* Weitere Informationen zu Grenzwerten und Kontingenten finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).
* Informationen zum Arbeiten mit asynchronen REST-Anforderungen finden Sie unter [Nachverfolgen asynchroner Vorgänge in Azure](resource-manager-async-operations.md).
