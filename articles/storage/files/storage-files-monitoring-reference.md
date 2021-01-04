---
title: Überwachen von Daten in Azure Files – Referenz | Microsoft-Dokumentation
description: Protokoll- und Metrikenreferenz für die Überwachung von Daten in Azure Files
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 3a3f4faf8278553153ae2186ea74d20702c603b3
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780925"
---
# <a name="azure-files-monitoring-data-reference"></a>Überwachen von Daten in Azure Files – Referenz

Ausführliche Informationen über das Erfassen und Analysieren von Überwachungsdaten für Azure Files finden Sie unter [Überwachen von Azure Files](storage-files-monitoring.md).

## <a name="metrics"></a>Metriken

In den nachstehenden Tabellen sind die für Azure Files erfassten Plattformmetriken aufgeführt. 

### <a name="capacity-metrics"></a>Kapazitätsmetriken

Kapazitätsmetriken werden täglich aktualisiert (bis zu 24 Stunden). Das Aggregationsintervall definiert das Zeitintervall, für das Metrikwerte dargestellt werden. Das unterstützte Aggregationsintervall für alle Kapazitätsmetriken beträgt eine Stunde (PT1H).

Azure Files stellt in Azure Monitor die folgenden Kapazitätsmetriken bereit.

#### <a name="account-level"></a>Kontoebene

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="azure-files"></a>Azure Files

Diese Tabelle enthält [Azure Files-Metriken](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices).

| Metrik | BESCHREIBUNG |
| ------------------- | ----------------- |
| FileCapacity | Der vom Speicherkonto beanspruchte File-Speicher. <br/><br/> Einheit: Byte <br/> Aggregationstyp: Average <br/> Beispielwert: 1024 |
| FileCount   | Die Anzahl von Dateien im Speicherkonto. <br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Average <br/> Beispielwert: 1024 |
| FileShareCapacityQuota | Die Obergrenze für die Speichermenge zur Verwendung durch den Azure Files-Dienst, angegeben in Byte. <br/><br/> Einheit: Byte <br/> Aggregationstyp: Average <br/> Beispielwert: 1024|
| FileShareCount | Die Anzahl von Dateifreigaben im Speicherkonto. <br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Average <br/> Beispielwert: 1024 |
| FileShareProvisionedIOPS | Die Anzahl der bereitgestellten IOPS in einer Dateifreigabe. Diese Metrik gilt nur für den Speicher für Premiumdateien. <br/><br/> Einheit: Bytes <br/> Aggregationstyp: Average |
| FileShareSnapshotCount | Hiermit wird die Anzahl der in der Freigabe im Azure Files-Dienst des Speicherkontos enthaltenen Momentaufnahmen angegeben. <br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Average | 
|FileShareSnapshotSize|Hiermit wird die durch die Momentaufnahmen im Azure Files-Dienst des Speicherkontos genutzte Speichermenge angegeben. <br/><br/> Einheit: Byte <br/> Aggregationstyp: Average|

### <a name="transaction-metrics"></a>Transaktionsmetriken

Transaktionsmetriken werden für jede Anforderung an ein Speicherkonto aus Azure Storage an Azure Monitor ausgegeben. Falls in Ihrem Speicherkonto keine Aktivität stattfindet, gibt es in diesem Zeitraum keine Daten zu Transaktionsmetriken. Alle Transaktionsmetriken stehen sowohl auf Ebene des Kontos als auch auf der des Azure Files-Diensts zur Verfügung. Das Aggregationsintervall definiert das Zeitintervall für die dargestellten Metrikwerte. Für alle Transaktionsmetriken werden die Aggregationsintervalle PT1H und PT1M unterstützt.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Metrikdimensionen

Azure Files unterstützt die folgenden Dimensionen für Metriken in Azure Monitor.

> [!NOTE] 
> Die Dateifreigabedimension ist für Standard-Dateifreigaben nicht verfügbar, sondern nur für Premium-Dateifreigaben. Bei der Verwendung von Standard-Dateifreigaben beziehen sich die bereitgestellten Metriken auf alle Dateifreigaben im Speicherkonto. Erstellen Sie eine Dateifreigabe pro Speicherkonto, um bei Standard-Dateifreigaben Metriken für einzelne Dateifreigaben zu erhalten.

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

- Eine Beschreibung der Überwachung von Azure Files finden Sie unter [Überwachen von Azure Files](storage-files-monitoring-reference.md).
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md).