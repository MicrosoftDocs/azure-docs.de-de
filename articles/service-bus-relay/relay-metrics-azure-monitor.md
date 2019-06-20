---
title: Azure Relay-Metriken in Azure Monitor (Vorschauversion) | Microsoft-Dokumentation
description: Überwachen von Azure Relay mit Azure Monitor
services: service-bus-relay
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: spelluru
ms.openlocfilehash: bd62624406adb006fdcd7d59f72db3fb5e1848a0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60421799"
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Azure Relay-Metriken in Azure Monitor (Vorschauversion)
Azure Relay-Metriken informieren Sie über den Status der Ressourcen in Ihrem Azure-Abonnement. Mithilfe eines umfangreichen Satzes von Metrikdaten können Sie die allgemeine Integrität Ihrer Relay-Ressourcen nicht nur auf Namespaceebene, sondern auch auf Entitätsebene bewerten. Diese Statistiken können wichtig sein, da sie Sie beim Überwachen des Zustands von Azure Relay unterstützen. Metriken können auch beim Beheben der Ursachen von Problemen helfen, ohne dass Sie sich an den Azure-Support wenden müssen.

Azure Monitor bietet einheitliche Benutzeroberflächen für die übergreifende Überwachung verschiedener Azure-Dienste. Weitere Informationen finden Sie unter [Überwachung in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) und im Beispiel zum [Abrufen von Azure Monitor-Metriken mit .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) auf GitHub.

> [!IMPORTANT]
> Dieser Artikel behandelt nur die Azure Relay-Funktion „Hybrid Connections“, nicht die WCF Relay-Funktion. 

## <a name="access-metrics"></a>Zugreifen auf Metriken

Azure Monitor bietet mehrere Möglichkeiten für den Metrikzugriff. Sie können über das [Azure-Portal](https://portal.azure.com), über die Azure Monitor-APIs (REST und .NET) sowie über Analyselösungen wie die Operation Management Suite und Event Hubs auf Metriken zugreifen. Weitere Informationen finden Sie unter [Von Azure Monitor gesammelte Überwachungsdaten](../azure-monitor/platform/data-platform.md).

Metriken sind standardmäßig aktiviert, und es stehen Daten für die letzten 30 Tage zur Verfügung. Zur längeren Aufbewahrung können Sie Metrikdaten in einem Azure-Speicherkonto archivieren. Dies wird in den [Diagnoseeinstellungen](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) in Azure Monitor konfiguriert.

## <a name="access-metrics-in-the-portal"></a>Zugreifen auf Metriken über das Portal

Im [Azure-Portal](https://portal.azure.com) können Sie Metriken im Zeitverlauf überwachen. Das folgende Beispiel zeigt, wie Sie die erfolgreichen und die eingehenden Anforderungen auf Kontoebene anzeigen:

![][1]

Sie können auch direkt über den Namespace auf die Metriken zugreifen. Dazu wählen Sie den Namespace aus und klicken dann auf **Metriken (Vorschau)** . 

Bei Metriken mit Dimensionsunterstützung müssen Sie den gewünschten Dimensionswert als Filter verwenden.

## <a name="billing"></a>Abrechnung

Die Verwendung von Metriken in Azure Monitor ist in der Vorschauversion kostenlos. Wenn Sie allerdings zusätzliche Lösungen zur Metrikdatenerfassung verwenden, fallen unter Umständen Kosten für diese Lösungen an. So werden etwa Gebühren für Azure Storage berechnet, wenn Sie Metrikdaten in einem Azure-Speicherkonto archivieren. Kosten fallen auch für Azure Monitor-Protokolle an, wenn Sie Metrikdaten für eine ausführlichere Analyse an Azure Monitor-Protokolle streamen.

Die folgenden Metriken bieten Ihnen eine Übersicht über die Integrität eines Diensts. 

> [!NOTE]
> Einige Metriken werden eingestellt, da sie unter einem anderen Namen verwendet werden. Deshalb müssen Sie möglicherweise Ihre Verweise aktualisieren. Metriken, die mit dem Schlüsselwort „veraltet“ markiert sind, werden zukünftig nicht mehr unterstützt.

Alle Metrikwerte werden minütlich an Azure Monitor gesendet. Das Aggregationsintervall definiert das Zeitintervall, für das Metrikwerte dargestellt werden. Die unterstützten Zeitintervalle für alle Azure Relay-Metriken beträgt 1 Minute.

## <a name="connection-metrics"></a>Verbindungsmetriken

| Metrikname | BESCHREIBUNG |
| ------------------- | ----------------- |
| ListenerConnections-Success (Vorschau) | Die Anzahl der erfolgreichen Listenerverbindungen mit Azure Relay in einem bestimmten Zeitraum <br/><br/> Einheit: Count <br/> Aggregationstyp: Gesamt <br/> Dimension: EntityName|
|ListenerConnections-ClientError (Vorschau)|Die Anzahl der Clientfehler bei Listenerverbindungen in einem bestimmten Zeitraum<br/><br/> Einheit: Count <br/> Aggregationstyp: Gesamt <br/> Dimension: EntityName|
|ListenerConnections-ServerError (Vorschau)|Die Anzahl der Serverfehler bei Listenerverbindungen in einem bestimmten Zeitraum<br/><br/> Einheit: Count <br/> Aggregationstyp: Gesamt <br/> Dimension: EntityName|
|SenderConnections-Success (Vorschau)|Die Anzahl der erfolgreichen Absenderverbindungen in einem bestimmten Zeitraum<br/><br/> Einheit: Count <br/> Aggregationstyp: Gesamt <br/> Dimension: EntityName|
|SenderConnections-ClientError (Vorschau)|Die Anzahl der Clientfehler bei Absenderverbindungen in einem bestimmten Zeitraum<br/><br/> Einheit: Count <br/> Aggregationstyp: Gesamt <br/> Dimension: EntityName|
|SenderConnections-ServerError (Vorschau)|Die Anzahl der Serverfehler bei Absenderverbindungen in einem bestimmten Zeitraum<br/><br/> Einheit: Count <br/> Aggregationstyp: Gesamt <br/> Dimension: EntityName|
|ListenerConnections-TotalRequests (Vorschau)|Die Gesamtanzahl der Listenerverbindungen in einem bestimmten Zeitraum<br/><br/> Einheit: Count <br/> Aggregationstyp: Gesamt <br/> Dimension: EntityName|
|SenderConnections-TotalRequests (Vorschau)|Die Verbindungsanforderungen vom Absender in einem bestimmten Zeitraum<br/><br/> Einheit: Count <br/> Aggregationstyp: Gesamt <br/> Dimension: EntityName|
|ActiveConnections (Vorschau)|Die Anzahl der aktiven Verbindungen in einem bestimmten Zeitraum<br/><br/> Einheit: Count <br/> Aggregationstyp: Gesamt <br/> Dimension: EntityName|
|ActiveListeners (Vorschau)|Die Anzahl der aktiven Listener in einem bestimmten Zeitraum<br/><br/> Einheit: Count <br/> Aggregationstyp: Gesamt <br/> Dimension: EntityName|
|ListenerDisconnects (Vorschau)|Die Anzahl der getrennten Listener in einem bestimmten Zeitraum<br/><br/> Einheit: Byte <br/> Aggregationstyp: Gesamt <br/> Dimension: EntityName|
|SenderDisconnects (Vorschau)|Die Anzahl der getrennten Absender in einem bestimmten Zeitraum<br/><br/> Einheit: Byte <br/> Aggregationstyp: Gesamt <br/> Dimension: EntityName|

## <a name="memory-usage-metrics"></a>Speichernutzungsmetriken

| Metrikname | BESCHREIBUNG |
| ------------------- | ----------------- |
|BytesTransferred (Vorschau)|Die Anzahl der in einem bestimmten Zeitraum übertragenen Bytes<br/><br/> Einheit: Byte <br/> Aggregationstyp: Gesamt <br/> Dimension: EntityName|

## <a name="metrics-dimensions"></a>Metrikdimensionen

Azure Relay unterstützt folgende Dimensionen für Metriken in Azure Monitor. Das Hinzufügen von Dimensionen zu Ihren Metriken ist optional. Wenn Sie keine Dimensionen hinzufügen, werden Metriken auf Namespaceebene angegeben. 

|Dimensionsname|BESCHREIBUNG|
| ------------------- | ----------------- |
|EntityName| Azure Relay unterstützt Messagingentitäten unter dem Namespace.|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Übersicht über die Azure-Überwachung](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




