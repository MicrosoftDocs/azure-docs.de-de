---
title: Azure DNS-Metriken und -Warnungen | Microsoft Docs
description: Informieren Sie sich über Azure DNS-Metriken und -Warnungen.
services: dns
documentationcenter: na
author: vhorne
manager: jennoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: victorh
ms.openlocfilehash: a9d8bc172eb5f5e0e119a0bde56fb167f7a0c2b2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64699152"
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS-Metriken und -Warnungen
Azure DNS ist ein Hostingdienst für DNS-Domänen, der die Namensauflösung mithilfe der Microsoft Azure-Infrastruktur ausführt. Dieser Artikel beschreibt die Metriken und Warnungen für den Azure DNS-Dienst.

## <a name="azure-dns-metrics"></a>Azure DNS-Metriken

Azure DNS stellt Metriken bereit, mit denen Kunden spezifische Aspekte ihrer DNS-Zonen überwachen können, die im Dienst gehostet werden. Darüber hinaus können Sie mit Azure DNS-Metriken Warnungen basierend auf Bedingungen, die von Interesse sind, konfigurieren und empfangen. Die Metriken werden über den [Azure Monitor-Dienst](../azure-monitor/index.yml) bereitgestellt. Azure DNS stellt die folgenden Metriken über Azure Monitor für Ihre DNS-Zonen bereit:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

Sie können auch die [Definition dieser Metriken](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) auf der Dokumentationsseite von Azure Monitor anzeigen.
>[!NOTE]
> Zurzeit sind diese Metriken nur für öffentliche DNS-Zonen verfügbar, die in Azure DNS gehostet werden. Wenn Sie private Zonen verwenden, die in Azure DNS gehostet werden, stellen diese Metriken keine Daten für diese Zonen bereit. Die Metriken und das Warnungsfeature werden darüber hinaus nur in der Azure Public Cloud unterstützt. Unterstützung für unabhängige Clouds wird zu einem späteren Zeitpunkt bereitgestellt. 

Das detaillierteste Element, für das Metriken angezeigt werden können, ist eine DNS-Zone. Derzeit können keine Metriken für einzelne Ressourcendatensätze innerhalb einer Zone angezeigt werden.

### <a name="query-volume"></a>Abfragevolumen

Die Metrik *Abfragevolumen* in Azure DNS zeigt das Volumen der DNS-Abfragen (Abfragedatenverkehr) an, das von Azure DNS für Ihre DNS-Zone empfangen wird. Die Maßeinheit ist „Anzahl“ und die Aggregation ist der Summe aller Abfragen, die über einen Zeitraum empfangen wurden. 

Wählen Sie zum Anzeigen dieser Metrik „Metriken (Vorschau)“ in der Explorer-Benutzeroberfläche auf der Registerkarte „Überwachen“ im Azure-Portal aus. Wählen Sie Ihre DNS-Zone aus der Dropdownliste „Ressource“ aus, wählen Sie die Metrik „Abfragevolumen“ aus, und wählen Sie dann „Summe“ als „Aggregation“ aus. Der Screenshot unten zeigt ein Beispiel.  Weitere Informationen zur Benutzeroberfläche des Metrik-Explorers und zur Diagrammerstellung finden Sie unter [Metrik-Explorer von Azure Monitor](../azure-monitor/platform/metrics-charts.md).

![Abfragevolumen](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Abbildung: Metriken zum Azure DNS-Abfragevolumen*

### <a name="record-set-count"></a>Anzahl von Datensatzgruppen
Die Metrik *Anzahl der Recordsets* zeigt die Anzahl der Recordsets in Azure DNS für Ihre DNS-Zone. Alle Recordsets, die in der Zone definiert sind, werden dabei gezählt. Die Maßeinheit ist „Anzahl“, und die Aggregation ist das Maximum von allen Recordsets. Wählen Sie zum Anzeigen dieser Metrik **Metriken (Vorschau)** in der Explorer-Benutzeroberfläche auf der Registerkarte **Überwachen** im Azure-Portal aus. Wählen Sie Ihre DNS-Zone aus der Dropdownliste **Ressource** aus, wählen Sie die Metrik **Anzahl der Recordsets** aus, und wählen Sie dann **Max** als **Aggregation** aus. Weitere Informationen zur Benutzeroberfläche des Metrik-Explorers und zur Diagrammerstellung finden Sie unter [Metrik-Explorer von Azure Monitor](../azure-monitor/platform/metrics-charts.md). 

![Anzahl von Datensatzgruppen](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Abbildung: Metriken zur Anzahl von Azure DNS-Ressourceneintragssätzen*


### <a name="record-set-capacity-utilization"></a>Kapazitätsauslastung von Datensatzgruppen
Die Metrik *Kapazitätsauslastung der Recordsets* in Azure DNS zeigt den Prozentsatz der Auslastung der Recordsetkapazität für eine DNS-Zone an. Für jede DNS-Zone in Azure DNS gilt ein Recordsetgrenzwert, der die maximale Anzahl von Recordsets definiert, die für die Zone zulässig sind (siehe [DNS-Grenzwerte](dns-zones-records.md#limits)). Daher zeigt diese Metrik, wie weit Sie noch von diesem Recordsetgrenzwert entfernt sind. Wenn Sie beispielsweise 500 Recordsets für Ihre DNS-Zone konfiguriert haben und die Zone den Standardgrenzwert für Recordsets von 5.000 aufweist, zeigt die Metrik RecordSetCapacityUtilization den Wert 10 % an (der durch Division von 500 durch 5.000 ermittelt wird). Die Maßeinheit ist **Prozentsatz**, und der Typ der **Aggregation** ist **Maximum**. Wählen Sie zum Anzeigen dieser Metrik „Metriken (Vorschau)“ in der Explorer-Benutzeroberfläche auf der Registerkarte „Überwachen“ im Azure-Portal aus. Wählen Sie Ihre DNS-Zone aus der Dropdownliste „Ressource“ aus, wählen Sie die Metrik „Kapazitätsauslastung der Recordsets“ aus, und wählen Sie dann „Max“ als „Aggregation“ aus. Der Screenshot unten zeigt ein Beispiel. Weitere Informationen zur Benutzeroberfläche des Metrik-Explorers und zur Diagrammerstellung finden Sie unter [Metrik-Explorer von Azure Monitor](../azure-monitor/platform/metrics-charts.md). 

![Anzahl von Datensatzgruppen](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Abbildung: Metriken zur Kapazitätsauslastung der Azure DNS-Ressourceneintragssätze*

## <a name="alerts-in-azure-dns"></a>Warnungen in Azure DNS
Azure Monitor bietet die Möglichkeit, Warnungen für verfügbare Metrikwerte auszugeben. Die DNS-Metriken sind in der neuen Benutzeroberfläche für die Warnungskonfiguration verfügbar. Wie in der [Dokumentation zu Warnungen in Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) detailliert beschrieben wird, können Sie die DNS-Zone als Ressource auswählen, den Signaltyp „Metrik“ auswählen und dann die Warnungslogik und andere Parameter wie **Zeitraum** und **Häufigkeit** konfigurieren. Sie können außerdem eine [Aktionsgruppe](../azure-monitor/platform/action-groups.md) für den Fall definieren, dass die Warnungsbedingung erfüllt ist. Die Warnung wird dann über die ausgewählten Aktionen übermittelt. Weitere Informationen zum Konfigurieren von Warnungen für Azure Monitor-Metriken finden Sie unter [Erstellen, Anzeigen und Verwalten von Warnungen mithilfe von Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Nächste Schritte
- [Weitere Informationen zu Azure DNS](dns-overview.md).
