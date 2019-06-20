---
title: Behandeln von Drosselungsfehlern in Azure | Microsoft-Dokumentation
description: Drosselungsfehler, Wiederholungsversuche und Backoffs in Azure Compute.
services: virtual-machines
documentationcenter: ''
author: changov
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: vashan, rajraj, changov
ms.openlocfilehash: efa10f5beae64105857b00b186683d491edb00f5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233767"
---
# <a name="troubleshooting-api-throttling-errors"></a>Behandeln von API-Drosselungsfehlern 

Azure Compute-Anforderungen können in einem Abonnement und auf Basis der jeweiligen Region gedrosselt werden, um die Gesamtleistung des Diensts zu unterstützen. Wir stellen sicher, dass die Aufrufe an den Azure Compute-Ressourcenanbieter (Compute Resource Provider, CRP), der Ressourcen unter dem Namespace „Microsoft.Compute“ verwaltet, die maximal zulässige API-Anforderungsrate nicht überschreiten. Dieses Dokument beschreibt die API-Drosselung, Details zur Behandlung von Drosselungsproblemen sowie bewährte Methoden zur Vermeidung von Drosselungen.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Vergleich der Drosselung durch Azure Resource Manager gegenüber Ressourcenanbietern  

Als „Eingangstür“ zu Azure übernimmt Azure Resource Manager die Authentifizierung und Validierung erster Ordnung sowie die Drosselung aller eingehenden API-Anforderungen. Grenzwerte für Azure Resource Manager-Aufrufraten und HTTP-Kopfzeilen verwandter Diagnoseantworten werden [hier](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-request-limits) beschrieben.
 
Wenn ein Azure-API-Client einen Drosselungsfehler erhält, ist der HTTP-Status „429: Zu viele Anforderungen“. Um herauszufinden, ob die Anforderungsdrosselung durch Azure Resource Manager oder einen zugrunde liegenden Ressourcenanbieter wie CRP erfolgt, untersuchen Sie die `x-ms-ratelimit-remaining-subscription-reads` auf GET-Anforderungen und die `x-ms-ratelimit-remaining-subscription-writes`-Antwortkopfzeilen auf Nicht-GET-Anforderungen. Wenn sich die Anzahl verbleibender Aufrufe 0 nähert, wurde das von Azure Resource Manager definierte allgemeine Aufruflimit des Abonnements erreicht. Die Aktivitäten von allen Abonnementclients werden zusammen gezählt. Andernfalls erfolgt die Drosselung durch den Zielressourcenanbieter (dem im Segment „`/providers/<RP>`“ der Anforderungs-URL adressierten). 

## <a name="call-rate-informational-response-headers"></a>Aufrufrate für Informationsantwortkopfzeilen 

| Header                            | Wertformat                           | Beispiel                               | BESCHREIBUNG                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-remaining-resource |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | Verbleibende Anzahl der API-Aufrufe für die Drosselungsrichtlinie, die den Ressourcenbucket oder die Vorgangsgruppe, einschließlich des Ziels dieser Anforderung, abdeckt                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | Die Anzahl der Aufrufe, die für diese HTTP-Anforderung auf den Grenzwert der anwendbaren Richtlinie „angerechnet“ wird. Dies ist meistens 1. Batchanforderungen wie zum Skalieren einer VM-Skalierungsgruppe können mehrfach angerechnet werden. |


Beachten Sie, dass eine API-Anforderung mehreren Drosselungsrichtlinien unterliegen kann. Für jede Richtlinie gibt es dann eine gesonderte `x-ms-ratelimit-remaining-resource`-Kopfzeile. 

Hier sehen Sie eine Beispielantwort auf die Anforderung zum Löschen der Skalierungsgruppe eines virtuellen Computers.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Drosselungsfehlerdetails

Der HTTP-Statuscode 429 wird häufig verwendet, um eine Anforderung abzulehnen, weil ein Aufrufratenlimit erreicht wurde. Eine typische Drosselungsfehlerantwort des Compute-Ressourcenanbieters sieht wie im folgenden Beispiel aus (nur die relevanten Kopfzeilen werden angezeigt):

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

Die Richtlinie mit der Anzahl verbleibender Aufrufe von 0 ist diejenige, wegen der der Drosselungsfehler zurückgegeben wird. In diesem Fall ist dies `HighCostGet30Min`. Das generelle Format des Antworttexts ist das allgemeine Fehlerformat der Azure Resource Manager-API (konform mit OData). Der Hauptfehlercode `OperationNotAllowed` ist der, den der Compute-Ressourcenanbieter verwendet, um Drosselungsfehler zu melden (neben anderen Typen von Clientfehlern). Die Eigenschaft `message` der internen Fehler enthält eine serialisierte JSON-Struktur mit den Details der Drosselungsverletzung.

Wie oben gezeigt, enthält jeder Drosselungsfehler die `Retry-After`-Kopfzeile, die die Mindestanzahl von Sekunden angibt, die der Client warten sollte, bevor ein Wiederholungsversuch der Anforderung gestartet wird. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>Analysetool für API-Aufrufrate und Drosselungsfehler
Für die API des Computeressourcenanbieters steht die Vorschauversion eines Features zur Problembehandlung zur Verfügung. Diese PowerShell-Cmdlets liefern Statistiken über die API-Anforderungsrate pro Zeitintervall pro Vorgang und die Drosselungsverletzungen pro Vorgangsgruppe (Richtlinie):
-   [Export-AzLogAnalyticRequestRateByInterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-AzLogAnalyticThrottledRequest](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

Die Statistiken über die API-Aufrufe ermöglichen umfassende Einblicke in das Verhalten der Clients eines Abonnements sowie die einfache Identifizierung von Aufrufmustern, die zu einer Drosselung führen.

Eine Einschränkung des Analysetools besteht vorerst darin, dass Anforderungen für Ressourcentypen von Datenträgern und Momentaufnahmen (zur Unterstützung von verwalteten Datenträgern) nicht einbezogen werden. Da die Daten von der Telemetrie des Computeressourcenanbieters erfasst werden, können zudem keine Drosselungsfehler in ARM identifiziert werden. Diese können jedoch wie oben erörtert basierend auf den eindeutigen ARM-Antwortkopfzeilen einfach identifiziert werden.

Die PowerShell-Cmdlets verwenden eine Dienst-REST-API, die auf einfache Weise direkt von Clients aufgerufen werden kann (wenn auch derzeit noch ohne formale Unterstützung). Um das Format der HTTP-Anforderungen anzuzeigen, führen Sie die Cmdlets mit dem Schalter „-Debug“ aus, oder überwachen Sie ihre Ausführung mit Fiddler.


## <a name="best-practices"></a>Bewährte Methoden 

- Führen Sie bei Azure-Dienst-API-Fehlern nicht vorbehaltlos und/oder sofort Wiederholungsversuche durch. Bei Clientcode kommt es häufig vor, dass er in eine schnelle Wiederholungsschleife gerät, wenn ein Fehler auftritt, bei dem keine Wiederholungsversuche möglich sind. Wiederholungsversuche erschöpfen letztendlich das zulässige Aufruflimit für die Zielgruppe des Vorgangs und beeinträchtigen andere Clients des Abonnements. 
- Erwägen Sie bei API-Automatisierungsfällen mit hohem Volumen die Implementierung einer proaktiven, clientseitigen Selbstdrosselung, wenn die Anzahl verfügbarer Aufrufe für eine Vorgangszielgruppe unter einen niedrigen Schwellenwert fällt. 
- Beachten Sie beim Nachverfolgen asynchroner Vorgänge die Hinweise in „Retry-After“-Kopfzeilen. 
- Wenn der Clientcode Informationen zu einem bestimmten virtuellen Computer benötigt, fragen Sie diesen VM direkt ab, statt alle VMs in der entsprechenden Ressourcengruppe oder dem gesamten Abonnement aufzulisten und dann den erforderlichen VM auf der Clientseite auszuwählen. 
- Wenn der Clientcode VMs, Datenträger und Momentaufnahmen von einem bestimmten Azure-Ort benötigt, verwenden Sie die ortsbasierte Form der Abfrage, statt alle Abonnement-VMs abzufragen und diese dann nach dem Ort auf der Clientseite zu filtern: `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30`-Abfrage an regionale Compute-Ressourcenanbieter-Endpunkte. 
-   Vor allem beim Erstellen oder Aktualisieren von API-Ressourcen sowie von virtuellen Computern und VM-Skalierungsgruppen ist es wesentlich effizienter, den zurückgegebenen asynchronen Vorgang bis zu dessen Abschluss nachzuverfolgen, als die Ressourcen-URL selbst abzurufen (basierend auf dem `provisioningState`).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Wiederholungsanleitung für andere Dienste in Azure finden Sie unter [Wiederholungsanleitung für bestimmte Dienste](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific).
