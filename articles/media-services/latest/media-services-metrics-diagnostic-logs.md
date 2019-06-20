---
title: Überwachen von Media Services-Metriken und -Diagnoseprotokollen über Azure Monitor | Microsoft-Dokumentation
description: Dieser Artikel bietet eine Übersicht darüber, wie Sie Metriken und Diagnoseprotokolle von Media Services über Azure Monitor überwachen können.
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
ms.date: 03/05/2019
ms.author: juliako
ms.openlocfilehash: bbf43ecb07947fad8cc1ee064d2038e4a21d4444
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964769"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Überwachen von Media Services-Metriken und -Diagnoseprotokollen

[Azure Monitor](../../azure-monitor/overview.md) ermöglicht Ihnen die Überwachung von Metriken und Diagnoseprotokollen, die Ihnen zu verstehen helfen, wie sich Ihre Anwendungen verhalten. Alle von Azure Monitor gesammelten Daten gehören einem von zwei Grundtypen an: Metriken und Protokollen. Sie können Media Services-Diagnoseprotokolle überwachen sowie Warnungen und Benachrichtigungen für die gesammelten Metriken und Protokolle erstellen. Sie können die Metrikdaten mit dem [Metrik-Explorer](../../azure-monitor/platform/metrics-getting-started.md) visualisieren und analysieren. Sie können Protokolle an [Azure Storage](https://azure.microsoft.com/services/storage/) senden, sie an [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) streamen und in [Log Analytics](https://azure.microsoft.com/services/log-analytics/) exportieren oder Dienste von Drittanbietern nutzen.

Eine ausführliche Übersicht finden Sie unter [Azure Monitor-Metrik](../../azure-monitor/platform/data-platform.md) und [Azure Monitor-Diagnoseprotokolle](../../azure-monitor/platform/diagnostic-logs-overview.md).

In diesem Thema werden die derzeit verfügbaren [Media Services-Metriken](#media-services-metrics) und [Media Services-Diagnoseprotokolle](#media-services-diagnostic-logs) behandelt.

## <a name="media-services-metrics"></a>Media Services-Metriken

Metriken werden in regelmäßigen Abständen gesammelt, unabhängig davon, ob sich der Wert ändert oder nicht. Sie sind für Warnmeldungen gut geeignet, da sie häufig abgetastet werden können und eine Warnung mit relativ einfacher Logik schnell ausgelöst werden kann.

Derzeit werden die folgenden Metriken der Media Services-[Streamingendpunkte](https://docs.microsoft.com/rest/api/media/streamingendpoints) von Azure ausgegeben:

|Metrik|`Display name`|BESCHREIBUNG|
|---|---|---|
|Requests|Requests|Liefert Details zur Gesamtzahl der Anforderungen, die durch Streamingendpunkte verarbeitet werden.|
|Ausgehende Daten|Ausgehende Daten|Gesamtzahl der ausgehenden Bytes. Beispiel: Vom Streamingendpunkt gestreamte Bytes.|
|SuccessE2ELatency|End-to-End-Wartezeit bei Erfolg| Bietet Informationen über die End-to-End-Wartezeit erfolgreicher Anforderungen.|

Sie würden z. B. den folgenden `az monitor metrics` CLI-Befehl ausführen, um Metriken „ausgehender Daten“ abzurufen:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Informationen zum Erstellen von Metrikwarnungen finden Sie unter [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="media-services-diagnostic-logs"></a>Media Services-Diagnoseprotokolle

Derzeit können Sie die folgenden Diagnoseprotokolle abrufen:

|NAME|BESCHREIBUNG|
|---|---|
|Dienstanforderung zur Schlüsselübermittlung|Protokolle, die die wichtigsten Informationen zur Dienstanforderung für die Schlüsselübermittlung anzeigen. Weitere Informationen finden Sie unter [Schemas](media-services-diagnostic-logs-schema.md).|

Sie würden den folgenden `az monitor diagnostic-settings` CLI-Befehl ausführen, um das Speichern von Diagnoseprotokollen in einem Speicherkonto zu aktivieren: 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Beispiel:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforamsv3  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsv3ResourceGroup/providers/Microsoft.Media/mediaservices/amsv3account" \
    --resource-group "amsv3ResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="next-steps"></a>Nächste Schritte 

[Erfassen und Nutzen von Protokolldaten aus Ihren Azure-Ressourcen](../../azure-monitor/platform/diagnostic-logs-overview.md)
