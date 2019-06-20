---
title: Schemas für Diagnoseprotokolle von Azure Media Services – Azure
description: In diesem Artikel werden die Schemas für Diagnoseprotokolle von Azure Media Services vorgestellt.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 394370738bc7996a221300540e68404986d91310
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60322249"
---
# <a name="diagnostic-logs-schemas"></a>Schemas für Diagnoseprotokolle

[Azure Monitor](../../azure-monitor/overview.md) ermöglicht Ihnen die Überwachung von Metriken und Diagnoseprotokollen, die Ihnen zu verstehen helfen, wie Ihre Anwendungen ausgeführt werden. Sie können Media Services-Diagnoseprotokolle überwachen sowie Warnungen und Benachrichtigungen für die gesammelten Metriken und Protokolle erstellen. Sie können Protokolle an [Azure Storage](https://azure.microsoft.com/services/storage/) senden, sie an [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) streamen und in [Log Analytics](https://azure.microsoft.com/services/log-analytics/) exportieren oder Dienste von Drittanbietern nutzen.

Ausführliche Informationen dazu finden Sie unter [Azure Monitor-Metrik](../../azure-monitor/platform/data-platform.md) und [Azure Monitor-Diagnoseprotokolle](../../azure-monitor/platform/diagnostic-logs-overview.md).

In diesem Artikel werden Schemas für Diagnoseprotokolle von Media Services beschrieben.

## <a name="top-level-diagnostic-logs-schema"></a>Schema der obersten Ebene für Diagnoseprotokolle

Eine detaillierte Beschreibung des Schemas der obersten Ebene für Diagnoseprotokolle finden Sie unter [Unterstützte Dienste, Schemas und Kategorien für Azure-Diagnoseprotokolle](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Protokollschema für Schlüsselübermittlung

### <a name="properties"></a>Eigenschaften

Diese Eigenschaften sind spezifisch für das Protokollschema für Schlüsselübermittlung.

|NAME|BESCHREIBUNG|
|---|---|
|keyId|Die ID des angeforderten Schlüssels.|
|keyType|Es kann sich um einen der folgenden Werte handeln: „Clear“ (keine Verschlüsselung), „FairPlay“, „PlayReady“ oder „Widevine“.|
|policyName|Der Azure Resource Manager-Name der Richtlinie.|
|tokenType|Der Tokentyp.|
|statusMessage|Die Statusmeldung.|

### <a name="examples"></a>Beispiele

Eigenschaften des Schemas für Schlüsselübermittlungsanforderungen.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="next-steps"></a>Nächste Schritte

[Überwachen von Media Services-Metriken und -Diagnoseprotokollen](media-services-metrics-diagnostic-logs.md)
