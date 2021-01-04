---
title: 'Azure Maps: API für zeitintensive Vorgänge'
description: Hier erfahren Sie mehr über die zeitintensive asynchrone Hintergrundverarbeitung in Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: f5fb7c8059c8b98e8ec514a4159e96f48db7b1ad
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906198"
---
# <a name="creator-preview-long-running-operation-api"></a>Creator-API für zeitintensive Vorgänge (Vorschau)

> [!IMPORTANT]
> Azure Maps Creator-Dienste befinden sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Einige APIs in Azure Maps verwenden ein [asynchrones Anforderung-Antwort-Muster](/azure/architecture/patterns/async-request-reply). Dieses Muster ermöglicht Azure Maps die Bereitstellung hochverfügbarer und reaktionsfähiger Dienste. In diesem Artikel wird die für Azure Maps spezifische Implementierung der zeitintensiven asynchronen Hintergrundverarbeitung erläutert.

## <a name="submitting-a-request"></a>Übermitteln einer Anforderung

Eine Clientanwendung startet einen zeitintensiven Vorgang durch den synchronen Aufruf einer HTTP-API. Dieser Befehl erfolgt in der Regel in Form einer HTTP POST-Anforderung. Bei erfolgreicher Erstellung einer asynchronen Workload gibt die API den HTTP-Statuscode `202` zurück, der angibt, dass die Anforderung akzeptiert wurde. Die Antwort enthält einen `Location`-Header, der auf einen Endpunkt verweist, den der Client abfragen kann, um den Status des zeitintensiven Vorgangs zu überprüfen.

### <a name="example-of-a-success-response"></a>Beispiel für eine erfolgreiche Antwort

```HTTP
Status: 202 Accepted
Location: https://atlas.microsoft.com/service/operations/{operationId}

```

Wenn die Überprüfung für den Aufruf nicht erfolgreich war, gibt die API stattdessen die HTTP-Antwort `400` für eine ungültige Anforderung zurück. Der Antworttext liefert dem Client weitere Informationen darüber, warum die Anforderung ungültig war.

### <a name="monitoring-the-operation-status"></a>Überwachen des Vorgangsstatus

Der in den akzeptierten Antwortheadern angegebene Standortendpunkt kann abgerufen werden, um den Status des zeitintensiven Vorgangs überprüfen. Der Antworttext der Vorgangsstatusanforderung enthält immer die Eigenschaften `status` und `createdDateTime`. Die Eigenschaft `status` zeigt den aktuellen Status eines zeitintensiven Vorgangs an. Mögliche Werte sind `"NotStarted"`, `"Running"`, `"Succeeded"` und `"Failed"`. Die Eigenschaft `createdDateTime` gibt die Zeit an, zu der die anfängliche Anforderung gesendet wurde, um den zeitintensiven Vorgang zu starten. Wenn der Zustand `"NotStarted"` oder `"Running"` lautet, enthält die Antwort auch einen Header vom Typ `Retry-After`. Mithilfe des Headers vom Typ `Retry-After` (gemessen in Sekunden) kann ermittelt werden, wann der nächste Abfrageaufruf für die Vorgangsstatus-API erfolgen soll.

### <a name="example-of-running-a-status-response"></a>Beispiel für das Ausführen einer Statusantwort

```HTTP
Status: 200 OK
Retry-After: 30
{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Running"
}
```

## <a name="handling-operation-completion"></a>Behandeln des Vorgangsabschlusses

Nach Abschluss des zeitintensiven Vorgangs lautet der Status der Antwort entweder `"Succeeded"` oder `"Failed"`. Wenn eine neue Ressource auf der Grundlage eines zeitintensiven Vorgangs erstellt wurde, gibt die Erfolgsantwort den HTTP-Statuscode `201 Created` zurück. Die Antwort enthält außerdem einen `Location`-Header, der auf Metadaten zur Ressource verweist. Wenn keine neue Ressource erstellt wurde, gibt die Erfolgsantwort den HTTP-Statuscode `200 OK` zurück. Bei einem Fehler lautet der Antwortstatuscode ebenfalls `200 OK`. Die Antwort enthält jedoch die Eigenschaft `error` im Textkörper. Die Fehlerdaten entsprechen der OData-Fehlerspezifikation.

### <a name="example-of-success-response"></a>Beispiel für eine Erfolgsantwort

```HTTP
Status: 201 Created
Location: "https://atlas.microsoft.com/tileset/{tileset-id}"
 {
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/tileset/{tileset-id}"
}
```

### <a name="example-of-failure-response"></a>Beispiel für eine Fehlerantwort

```HTTP
Status: 200 OK

{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Failed",
    "error": {
        "code": "InvalidFeature",
        "message": "The provided feature is invalid.",
        "details": {
            "code": "NoGeometry",
            "message": "No geometry was provided with the feature."
        }
    }
}
```