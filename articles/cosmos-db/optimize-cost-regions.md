---
title: Optimieren der Kosten von Azure Cosmos DB bei Bereitstellungen in mehreren Regionen
description: In diesem Artikel wird erläutert, wie Sie die Kosten von Azure Cosmos DB bei Bereitstellungen in mehreren Regionen verwalten.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 478714f48782adb138f1ed803d53c81ec48f2efd
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967293"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optimieren der Kosten bei mehreren Regionen in Azure Cosmos DB

Sie können jederzeit Regionen zu Ihrem Azure Cosmos-Konto hinzufügen oder Regionen aus diesem entfernen. Der Durchsatz, den Sie für verschiedene Azure Cosmos-Datenbanken und -Container konfigurieren, wird in jeder Region, die Ihrem Konto zugeordnet ist, reserviert. Wenn der pro Stunde bereitgestellte Durchsatz (d. h. die Summe der RUs/Sek., die für alle Datenbanken und Container Ihres Azure Cosmos-Kontos konfiguriert wurden) `T` beträgt und sich die Anzahl der Azure-Regionen in Ihrem Datenbankkonto auf `N` beläuft, wird der insgesamt für Ihr Cosmos-Konto bereitgestellte Durchsatz für jede Stunde wie folgt berechnet:

1. `T x N RU/s` wenn Ihr Azure Cosmos-Konto mit nur einer Schreibregion konfiguriert ist 

1. `T x (N+1) RU/s` wenn alle Regionen Ihres Azure Cosmos-Konto für die Verarbeitung von Schreibanforderungen ausgelegt sind 

Der bereitgestellte Durchsatz bei nur einer Region mit Schreibvorgängen kostet 0,008 US-Dollar/Stunde pro 100 RUs/Sek., und der bereitgestellte Durchsatz bei mehreren Regionen mit Schreibvorgängen kostet 0,016 US-Dollar/Stunde pro 100 RUs/Sek. Weitere Informationen zu den Kosten von Azure Cosmos DB finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="costs-for-multiple-write-regions"></a>Kosten für mehrere Regionen mit Schreibvorgängen

In einem Multimastersystem nehmen die netto verfügbaren Anforderungseinheiten (RUs) für Schreibvorgänge um das `N`-fache zu, wobei `N` für die Anzahl der Regionen mit Schreibvorgängen steht. Im Gegensatz zu einer einzelnen Region mit Schreibvorgängen kann hier jede Region Schreibanforderungen verarbeiten und sollte eine Konfliktlösung unterstützen. Die Workloadmenge für die Schreiber hat sich erhöht. Unter Gesichtspunkten der Kostenplanung müssen Sie, um weltweite Schreibvorgänge für `M` RUs/Sek. auszuführen, M `RUs` auf Container- oder Datenbankebene bereitstellen. Anschließend können Sie beliebig viele Regionen hinzufügen und diese für Schreibvorgänge verwenden, um weltweite Schreibvorgänge für `M` RUs auszuführen. 

### <a name="example"></a>Beispiel

Angenommen, Sie haben einen Container in der Region „USA, Westen“ mit einem Durchsatz von 10.000 RUs/Sek. bereitgestellt und speichern in diesem Monat 1 TB Daten. Weiterhin angenommen, dass Sie die drei Regionen „USA, Osten“, „Europa, Norden“ und „Asien, Osten“ hinzufügen, wobei für jede die gleiche Speicherkapazität und der gleiche Durchsatz bereitgestellt werden. Außerdem möchten Sie Schreibvorgänge für Container in allen vier Regionen von Ihrer global verteilten App ausführen. Ihre monatliche Gesamtrechnung (basierend auf einem Monat von 31 Tagen) sieht dann wie folgt aus:

|**Element**|**Nutzung (monatlich)**|**Rate**|**Monatliche Kosten**|
|----|----|----|----|
|Durchsatzabrechnung für Container in „USA, Westen“ (Schreibvorgänge in mehreren Regionen) |10.000 RUs/Sek. x 24 x 31 |0,016 US-Dollar pro 100 RUs/Sek. pro Stunde |1.190,40 US-Dollar |
|Durchsatzabrechnung für 3 zusätzliche Regionen „USA, Osten“, „Europa, Norden“ und „Asien, Osten“ (Schreibvorgänge in mehreren Regionen) |(3+1) x 10.000 RUs/Sek. x 24 x 31 |0,016 US-Dollar pro 100 RUs/Sek. pro Stunde |4.761,60 US-Dollar |
|Speicherabrechnung für Container in „USA, Westen“ |100 GB |0,25 US-Dollar/GB |25 USD |
|Speicherabrechnung für 3 zusätzliche Regionen „USA, Osten“, „Europa, Norden“ und „Asien, Osten“ |3 x 100 GB |0,25 US-Dollar/GB |75 US-Dollar |
|**Gesamt**|||**6.052 US-Dollar** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Optimieren der Durchsatznutzung nach Region

Wenn Sie eine ineffiziente Nutzung feststellen und beispielsweise eine über- und eine unterbelastete Region haben, können Sie mit den folgenden Schritten die Durchsatznutzung optimieren:  

1. Stellen Sie sicher, dass Sie den bereitgestellten Durchsatz (RUs/Sek.) zuerst in der Region mit Schreibvorgängen optimieren und dann mithilfe des Änderungsfeeds von der Region mit Schreibvorgängen usw. die RUs in den Leseregionen maximal nutzen. 

2. Die Lese- und Schreibvorgänge in mehreren Regionen mit Schreibvorgängen können über alle mit dem Azure Cosmos-Konto verknüpften Regionen skaliert werden. 

3. Überwachen Sie die Aktivitäten in Ihren Regionen. Bei Bedarf können Sie Regionen hinzufügen und entfernen, um Ihren Lese- und Schreibdurchsatz zu skalieren.

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie mithilfe der folgenden Artikel mehr über die Kostenoptimierung in Azure Cosmos DB erfahren:

* Weitere Informationen unter [Optimale Entwicklungs- und Testmöglichkeiten mit Azure Cosmos DB](optimize-dev-test.md)
* Weitere Informationen unter [Erläuterungen zu Ihrer Azure Cosmos DB-Rechnung](understand-your-bill.md)
* Weitere Informationen unter [Optimieren der Durchsatzkosten](optimize-cost-throughput.md)
* Weitere Informationen unter [Optimieren der Speicherkosten](optimize-cost-storage.md)
* Weitere Informationen unter [Optimieren der Kosten für Lese- und Schreibvorgänge](optimize-cost-reads-writes.md)
* Weitere Informationen unter [Optimieren der Kosten von Abfragen](optimize-cost-queries.md)

