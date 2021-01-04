---
title: Überwachungsdatenreferenz zu Azure-Tabellenspeicher | Microsoft-Dokumentation
description: Protokoll- und Metrikenreferenz für Überwachungsdaten aus Azure-Tabellenspeicher.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: b9920956a8983a1c71c1f91cd1c19a6e84be6aee
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780296"
---
# <a name="azure-table-storage-monitoring-data-reference"></a>Überwachungsdatenreferenz zu Azure-Tabellenspeicher

Ausführliche Informationen über das Erfassen und Analysieren von Überwachungsdaten für Azure Storage finden Sie unter [Überwachen von Azure Storage](monitor-table-storage.md).

## <a name="metrics"></a>Metriken

In den nachstehenden Tabellen sind die für Azure Storage erfassten Plattformmetriken aufgelistet. 

### <a name="capacity-metrics"></a>Kapazitätsmetriken

Kapazitätsmetrikwerte werden stündlich an Azure Monitor gesendet. Die Werte werden täglich aktualisiert. Das Aggregationsintervall definiert das Zeitintervall, für das Metrikwerte dargestellt werden. Das unterstützte Aggregationsintervall für alle Kapazitätsmetriken beträgt eine Stunde (PT1H).

Azure Storage stellt in Azure Monitor folgende Kapazitätsmetriken bereit:

#### <a name="account-level"></a>Kontoebene

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="table-storage"></a>Table Storage

Diese Tabelle enthält [Table Storage-Metriken](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices).

| Metrik | BESCHREIBUNG |
| ------------------- | ----------------- |
| TableCapacity | Der vom Speicherkonto beanspruchte Table-Speicher. <br/><br/> Einheit: Byte <br/> Aggregationstyp: Average <br/> Beispielwert: 1024 |
| TableCount   | Die Anzahl von Tabellen im Speicherkonto. <br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Average <br/> Beispielwert: 1024 |
| TableEntityCount | Die Anzahl von Tabellenentitäten im Speicherkonto. <br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Average <br/> Beispielwert: 1024 |

### <a name="transaction-metrics"></a>Transaktionsmetriken

Transaktionsmetriken werden für jede Anforderung an ein Speicherkonto aus Azure Storage an Azure Monitor ausgegeben. Falls in Ihrem Speicherkonto keine Aktivität stattfindet, gibt es in diesem Zeitraum keine Daten zu Transaktionsmetriken. Alle Transaktionsmetriken stehen sowohl auf der Kontoebene als auch auf dem Servicelevel von Tabellenspeicher zur Verfügung. Das Aggregationsintervall definiert das Zeitintervall für die dargestellten Metrikwerte. Für alle Transaktionsmetriken werden die Aggregationsintervalle PT1H und PT1M unterstützt.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Metrikdimensionen

Azure Storage unterstützt folgende Dimensionen für Metriken in Azure Monitor:

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Ressourcenprotokolle (Vorschau)

> [!NOTE]
> Azure Storage-Protokolle in Azure Monitor befinden sich in der öffentlichen Vorschauphase und stehen in allen öffentlichen Cloudregionen für Vorschautests zur Verfügung. Diese Vorschauversion ermöglicht Protokolle für Blobs (einschließlich Azure Data Lake Storage Gen2), Dateien, Warteschlangen, Tabellen, Universell V1-Premium-Speicherkonten und Universell V2-Speicherkonten. Klassische Speicherkonten werden nicht unterstützt.

In der nachstehenden Tabelle sind die Eigenschaften für Azure Storage-Ressourcenprotokolle aufgelistet, wenn diese in Azure Monitor-Protokollen oder Azure Storage gesammelt werden. Diese Eigenschaften beschreiben den Vorgang, den Dienst und den Autorisierungstyp, der zum Ausführen des Vorgangs verwendet wurde.

### <a name="fields-that-describe-the-operation"></a>Den Vorgang beschreibende Felder

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Felder, die beschreiben, wie der Vorgang authentifiziert wurde

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Den Dienst beschreibende Felder

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Siehe auch

- Eine Beschreibung der Überwachung von Azure Storage finden Sie unter [Überwachen von Azure-Tabellenspeicher](monitor-table-storage.md).
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md).