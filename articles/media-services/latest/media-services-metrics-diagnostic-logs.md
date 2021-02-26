---
title: Metriken und Diagnoseprotokolle in Azure Monitor
description: Erfahren Sie, wie Sie Metriken und Diagnoseprotokolle von Azure Media Services über Azure Monitor überwachen können.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: inhenkel
ms.openlocfilehash: 35b0d9c2937024341be6183ac1438b7c4500d6ef
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954985"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-with-azure-monitor"></a>Überwachen von Media Services-Metriken und -Diagnoseprotokollen mit Azure Monitor

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Mit [Azure Monitor](../../azure-monitor/overview.md) können Sie Metriken und Diagnoseprotokolle überwachen, die Ihnen helfen zu verstehen, wie sich Ihre Anwendungen verhalten. Alle von Azure Monitor gesammelten Daten gehören einem von zwei Grundtypen an: Metriken und Protokollen. Sie können Media Services-Diagnoseprotokolle überwachen sowie Warnungen und Benachrichtigungen für die gesammelten Metriken und Protokolle erstellen. Sie können die Metrikdaten mit dem [Metrik-Explorer](../../azure-monitor/platform/metrics-getting-started.md) visualisieren und analysieren. Sie können Protokolle an [Azure Storage](https://azure.microsoft.com/services/storage/) senden, sie an [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) streamen, in [Log Analytics](https://azure.microsoft.com/services/log-analytics/) exportieren oder Dienste von Drittanbietern nutzen.

Eine ausführliche Übersicht finden Sie unter [Azure Monitor-Metrik](../../azure-monitor/platform/data-platform.md) und [Azure Monitor-Diagnoseprotokolle](../../azure-monitor/platform/platform-logs-overview.md).

In diesem Thema werden die unterstützten [Media Services-Metriken](#media-services-metrics) und [Media Services-Diagnoseprotokolle](#media-services-diagnostic-logs) behandelt.

## <a name="media-services-metrics"></a>Media Services-Metriken

Metriken werden in regelmäßigen Abständen gesammelt, unabhängig davon, ob sich der Wert ändert oder nicht. Sie sind für Warnmeldungen gut geeignet, da sie häufig abgetastet werden können und eine Warnung mit relativ einfacher Logik schnell ausgelöst werden kann. Informationen zum Erstellen von Metrikwarnungen finden Sie unter [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Media Services unterstützt das Überwachen von Metriken für die folgenden Ressourcen:

* Konto
* Streamingendpunkt

### <a name="account"></a>Konto

Sie können die folgenden Kontometriken überwachen.

|Metrikname|`Display name`|BESCHREIBUNG|
|---|---|---|
|AssetCount|Anzahl der Medienobjekte|Die Medienobjekte in Ihrem Konto.|
|AssetQuota|Kontingent der Medienobjekte|Das Kontingent der Medienobjekte in Ihrem Konto.|
|AssetQuotaUsedPercentage|Prozentsatz des verwendeten Kontingents der Medienobjekte|Der Prozentsatz des bereits verwendeten Kontingents der Medienobjekte.|
|ContentKeyPolicyCount|Anzahl der Richtlinien für Inhaltsschlüssel|Die Richtlinien für Inhaltsschlüssel in Ihrem Konto.|
|ContentKeyPolicyQuota|Kontingent der Richtlinien für Inhaltsschlüssel|Das Kontingent der Richtlinien für Inhaltsschlüssel in Ihrem Konto.|
|ContentKeyPolicyQuotaUsedPercentage|Prozentsatz des Kontingents der Richtlinien für Inhaltsschlüssel|Der Prozentsatz des bereits verwendeten Kontingents der Richtlinien für Inhaltsschlüssel.|
|StreamingPolicyCount|Anzahl der Streamingrichtlinien|Die Streamingrichtlinien in Ihrem Konto.|
|StreamingPolicyQuota|Kontingent der Streamingrichtlinien|Das Kontingent der Streamingrichtlinien in Ihrem Konto.|
|StreamingPolicyQuotaUsedPercentage|Prozentsatz des verwendeten Kontingents der Streamingrichtlinien|Der Prozentsatz des bereits verwendeten Kontingents der Streamingrichtlinien.|

Informieren Sie sich außerdem über [Kontingente und Grenzwerte für Konten](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Streamingendpunkt

Die folgenden [Streamingendpunkte](/rest/api/media/streamingendpoints) von Media Services-Metriken werden unterstützt:

|Metrikname|`Display name`|BESCHREIBUNG|
|---|---|---|
|Requests|Requests|Gibt die Gesamtzahl der HTTP-Anforderungen an, die vom Streamingendpunkt behandelt werden.|
|Ausgehende Daten|Ausgehende Daten|Ausgehende Bytes gesamt pro Minute pro Streamingendpunkt.|
|SuccessE2ELatency|End-to-End-Wartezeit bei Erfolg|Die Zeitspanne zwischen dem Empfang der Anforderung durch den Streamingendpunkt und dem Senden des letzten Bytes der Antwort.|
|CPU-Auslastung| | CPU-Auslastung für Premium-Streamingendpunkte. Diese Daten stehen für Standard-Streamingendpunkte nicht zur Verfügung. |
|Ausgangsbandbreite | | Ausgangsbandbreite in Bits pro Sekunde.|

### <a name="metrics-are-useful"></a>Praktische Metriken

Hier sind Beispiele dafür, wie Sie durch das Überwachen von Media Services-Metriken Informationen über die Leistung Ihrer Anwendungen erhalten. Media Services-Metriken können beispielsweise für folgende Fragen genutzt werden:

* Wie überwache ich meinen Standardstreamingendpunkt, um zu wissen, wann die Grenzwerte überschritten wurden?
* Wie erkenne ich, ob ich über genügend Premium-Streamingendpunkt-Skalierungseinheiten verfüge?
* Wie kann ich eine Warnung festlegen, um zu wissen, wann die Streamingendpunkte hochskaliert werden müssen?
* Wie lege ich eine Warnung fest, um zu wissen, wann der für das Konto konfigurierte maximale ausgehende Datenverkehr erreicht wurde?
* Wie kann ich die Aufschlüsselung der fehlgeschlagenen Anforderungen und die Fehlerursachen anzeigen?
* Wie kann ich erkennen, wie viele HLS- oder DASH-Anforderungen aus dem Objekt-Manager abgerufen werden?
* Wie lege ich eine Warnung fest, um zu wissen, wann der Schwellenwert für die Anzahl fehlgeschlagener Anforderungen erreicht wurde?

Parallelität wird für die Anzahl von Streamingendpunkten relevant, die im Laufe der Zeit in einem einzelnen Konto verwendet werden. Behalten Sie die Beziehung zwischen der Anzahl paralleler Datenströme mit komplexen Veröffentlichungsparametern wie dynamischer Paketerstellung in mehrere Protokolle, mehrere DRM-Verschlüsselungen usw. im Hinterkopf. Jeder weitere veröffentlichte Livestream beansprucht zusätzliche CPU- und Ausgabebandbreite des Streamingendpunkts. Vor diesem Hintergrund empfiehlt es sich, die Auslastung des Streamingendpunkts (CPU- und Ausgangskapazität) mithilfe von Azure Monitor genau zu überwachen, um eine angemessene Skalierung sicherzustellen. Im Falle von sehr hoher Parallelität können Sie den Datenverkehr alternativ auf mehrere Streamingendpunkte aufteilen.

### <a name="example"></a>Beispiel

Siehe [Überwachen von Media Services-Metriken](media-services-metrics-howto.md)

## <a name="media-services-diagnostic-logs"></a>Media Services-Diagnoseprotokolle

Diagnoseprotokolle liefern regelmäßig umfassende Daten zum Betrieb einer Azure-Ressource. Weitere Informationen finden Sie unter [Erfassen und Nutzen von Protokolldaten aus Ihren Azure-Ressourcen](../../azure-monitor/platform/platform-logs-overview.md).

Media Services unterstützt die folgenden Diagnoseprotokolle:

* Schlüsselübermittlung

### <a name="key-delivery"></a>Schlüsselübermittlung

|Name|BESCHREIBUNG|
|---|---|
|Dienstanforderung zur Schlüsselübermittlung|Protokolle, die die wichtigsten Informationen zur Dienstanforderung für die Schlüsselübermittlung anzeigen. Weitere Informationen finden Sie unter [Schemas](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Warum sollte ich Diagnoseprotokolle verwenden?

Mit Diagnoseprotokollen für die Schlüsselübermittlung können Sie beispielsweise Folgendes untersuchen:

* Anzeigen der Anzahl bereitgestellter Lizenzen nach DRM-Typ
* Anzeigen der Anzahl bereitgestellter Lizenzen nach Richtlinie
* Anzeigen von Fehlern nach DRM- oder Richtlinientyp
* Anzeigen der Anzahl nicht autorisierter Lizenzanforderungen von Clients

### <a name="example"></a>Beispiel

Siehe [Überwachen von Media Services-Diagnoseprotokollen](media-services-diagnostic-logs-howto.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfassen und Nutzen von Protokolldaten aus Ihren Azure-Ressourcen](../../azure-monitor/platform/platform-logs-overview.md)
* [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](../../azure-monitor/platform/alerts-metric.md)
* [Überwachen von Media Services-Metriken](media-services-metrics-howto.md)
* [Überwachen von Media Services-Diagnoseprotokollen](media-services-diagnostic-logs-howto.md)
