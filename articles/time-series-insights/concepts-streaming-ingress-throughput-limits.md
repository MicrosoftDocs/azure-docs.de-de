---
title: 'Durchsatzeinschränkungen bei der Erfassung von Datenströmen: Azure Time Series Insights Gen2 | Microsoft-Dokumentation'
description: Erfahren Sie mehr über Grenzwerte beim Eingangsdurchsatz in Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/26/2020
ms.custom: seodec18
ms.openlocfilehash: c8be18049e6ae74a198f5885a46b70df581e0cd7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187458"
---
# <a name="streaming-ingestion-throughput-limits"></a>Durchsatzlimits für Datenstromerfassung

Im Folgenden werden Einschränkungen bei eingehenden Datenströmen in Azure Time Series Insights Gen2 beschrieben.

> [!TIP]
> Eine umfassende Liste aller Grenzwerte finden Sie unter [Planen Ihrer Azure Time Series Insights Gen2-Umgebung](./how-to-plan-your-environment.md#review-azure-time-series-insights-gen2-limits).

## <a name="per-environment-limitations"></a>Einschränkungen pro Umgebung

Im Allgemeinen werden Eingangsraten als Faktor der Anzahl von Geräten in Ihrer Organisation, der Häufigkeit der Ereignisausgabe und der Größe der einzelnen Ereignisse angesehen.

* **Anzahl der Geräte** × **Häufigkeit der Ereignisausgabe** × **Größe jedes Ereignisses**.

Standardmäßig kann Azure Time Series Insights Gen2 eingehende Daten mit einer Rate von **bis zu 1 MB pro Sekunde (MBit/s) pro Azure Time Series Insights Gen2-Umgebung** erfassen. Es gelten zusätzliche Einschränkungen [pro Hubpartition](./concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits).

> [!TIP]
>
> * Die Umgebungsunterstützung für Erfassungsgeschwindigkeiten von bis zu 8 MBit/s kann auf Anfrage bereitgestellt werden.
> * Falls Sie einen höheren Durchsatz benötigen, können Sie sich an uns wenden, indem Sie über das Azure-Portal ein Supportticket senden.

* **Beispiel 1:**

    Contoso Shipping verfügt über 100.000 Geräte, die dreimal pro Minute ein Ereignis ausgeben. Die Größe eines Ereignisses beträgt 200 Byte. Als Azure Time Series Insights Gen2-Ereignisquelle wird eine IoT Hub-Instanz mit vier Partitionen genutzt.

  * Die Erfassungsrate für die Azure Time Series Insights Gen2-Umgebung lautet wie folgt: **100.000 Geräte · 200 Byte/Ereignis · (3/60 Ereignis/s) = 1 MBit/s**.
    * Unter der Annahme ausgeglichener Partitionen wäre die Erfassungsrate pro Partition 0,25 MBit/s.
    * Die Erfassungsrate von Contoso Shipping läge damit innerhalb der Skalierungseinschränkungen.

* **Beispiel 2:**

    Contoso Fleet Analytics verfügt über 40.000 Geräte, die jede Sekunde ein Ereignis ausgeben. Als Azure Time Series Insights Gen2-Ereignisquelle wird eine Event Hub-Instanz mit zwei Partitionen genutzt. Die Größe eines Ereignisses beträgt 200 Byte.

  * Die Erfassungsrate der Umgebung wäre wie folgt: **40.000 Geräte × 200 Byte/Ereignis × 1 Ereignis/s = 8 MBit/s**.
    * Unter der Annahme ausgeglichener Partitionen wäre die Rate pro Partition 4 MBit/s.
    * Die Erfassungsrate von Contoso Fleet Analytics liegt über den Umgebungs- und Partitionsgrenzwerten. Eine Anforderung an Azure Time Series Insights Gen2 kann über das Azure-Portal übermittelt werden, um die Erfassungsrate für die Umgebung zu erhöhen. Außerdem kann ein Event Hub mit einer größeren Anzahl von Partitionen erstellt werden, um die Grenzwerte einzuhalten.

## <a name="hub-partitions-and-per-partition-limits"></a>Hub-Partitionen und Limits pro Partition

Bei der Planung Ihrer Azure Time Series Insights Gen2-Umgebung ist es wichtig, die Konfiguration der Ereignisquellen zu berücksichtigen, die Sie mit Azure Time Series Insights Gen2 verbinden möchten. Sowohl Azure IoT Hub als auch Event Hubs verwenden Partitionen, um die horizontale Skalierung für die Ereignisverarbeitung zu ermöglichen.

Eine *Partition* ist eine geordnete Sequenz von Ereignissen, die in einem Event Hub besteht. Die Partitionsanzahl wird beim Erstellen des Hubs festgelegt und kann nicht geändert werden.

Informationen zu den bewährten Methoden für die Event Hubs-Partitionierung finden Sie unter [Wie viele Partitionen benötige ich?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need).

> [!NOTE]
> Für die meisten IoT Hubs, die mit Azure Time Series Insights Gen2 genutzt werden, sind nur vier Partitionen erforderlich.

Unabhängig davon, ob Sie einen neuen Hub für Ihre Azure Time Series Insights Gen2-Umgebung erstellen oder einen vorhandenen Hub verwenden, müssen Sie die Erfassungsrate pro Partition berechnen, um festzustellen, ob diese innerhalb der Grenzwerte liegt.

Für Azure Time Series Insights Gen2 gilt derzeit ein allgemeiner **Grenzwert von 0,5 MBit/s pro Partition**.

### <a name="iot-hub-specific-considerations"></a>IoT Hub-spezifische Überlegungen

Wenn ein Gerät in IoT Hub erstellt wird, wird es einer Partition dauerhaft zugewiesen. Auf diese Weise kann von IoT Hub die Ereignisreihenfolge sichergestellt werden (da sich die Zuweisung niemals ändert).

Eine feste Partitionszuweisung wirkt sich auch auf Azure Time Series Insights Gen2-Instanzen aus, die von IoT Hub nachfolgend gesendete Daten erfassen. Wenn Nachrichten von mehreren Geräten mit derselben Gatewaygeräte-ID an den Hub weitergeleitet werden, kommen sie ggf. gleichzeitig in derselben Partition an, und dies kann unter Umständen dazu führen, dass die Skalierungslimits pro Partition überschritten werden.

**Auswirkung**:

* Wenn es für eine einzelne Partition zu einer dauerhaften Erfassungsrate oberhalb des Grenzwerts kommt, synchronisiert Azure Time Series Insights Gen2 möglicherweise nicht alle Gerätetelemetriedaten, bevor der Zeitraum für die IoT Hub-Datenaufbewahrung überschritten wird. Aus diesem Grund können gesendete Daten verloren gehen, falls die Erfassungslimits dauerhaft überschritten werden.

Wir empfehlen Ihnen die folgenden bewährten Methoden, um dieses Problem zu umgehen:

* Berechnen Sie Ihre Erfassungsraten pro Umgebung und pro Partition, bevor Sie Ihre Lösung bereitstellen.
* Stellen Sie sicher, dass für Ihre IoT Hub-Geräte ein maximaler Lastenausgleich erfolgt.

> [!IMPORTANT]
> In Umgebungen, die IoT Hub als Ereignisquelle verwenden, berechnen Sie die Erfassungsrate anhand der Anzahl der verwendeten Hubgeräte, um sicherzugehen, dass die Rate unter 0,5 MBit/s pro Partitionseinschränkung fällt.
>
> * Auch wenn mehrere Ereignisse gleichzeitig eintreffen, wird der Grenzwert nicht überschritten.

  ![IoT Hub-Partitionsdiagramm](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Unter den folgenden Ressourcen finden Sie weitere Informationen zur Optimierung des Hub-Durchsatzes und der zugehörigen Partitionen:

* [Skalieren von Azure IoT Hub](../iot-hub/iot-hub-scaling.md)
* [Skalierbarkeit – Azure Event Hubs](../event-hubs/event-hubs-scalability.md#throughput-units)
* [Event Hub-Partitionen](../event-hubs/event-hubs-features.md#partitions)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur [Datenspeicherung](./concepts-storage.md).