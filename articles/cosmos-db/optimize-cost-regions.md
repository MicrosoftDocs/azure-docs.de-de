---
title: Optimieren der Kosten von Azure Cosmos DB bei Bereitstellungen in mehreren Regionen
description: In diesem Artikel wird erläutert, wie Sie die Kosten von Azure Cosmos DB bei Bereitstellungen in mehreren Regionen verwalten.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: a559a51feafa310a4645282dc6368f520fc6b972
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459625"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optimieren der Kosten bei mehreren Regionen in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Sie können jederzeit Regionen zu Ihrem Azure Cosmos-Konto hinzufügen oder Regionen aus diesem entfernen. Der Durchsatz, den Sie für verschiedene Azure Cosmos-Datenbanken und -Container konfigurieren, wird in jeder Region, die Ihrem Konto zugeordnet ist, reserviert. Wenn der pro Stunde bereitgestellte Durchsatz (d. h. die Summe der RUs/Sek., die für alle Datenbanken und Container Ihres Azure Cosmos-Kontos konfiguriert wurden) `T` beträgt und sich die Anzahl der Ihrem Datenbankkonto zugeordneten Azure-Regionen auf `N` beläuft, wird der insgesamt für Ihr Cosmos-Konto bereitgestellte Durchsatz für jede Stunde wie folgt berechnet: `T x N RU/s`.

Der bereitgestellte Durchsatz bei nur einer Region mit Schreibvorgängen kostet 0,008 US-Dollar/Stunde pro 100 RUs/Sek., und der bereitgestellte Durchsatz bei mehreren Regionen mit Schreibvorgängen kostet 0,016 US-Dollar/Stunde pro 100 RUs/Sek. Weitere Informationen zu den Kosten von Azure Cosmos DB finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="costs-for-multiple-write-regions"></a>Kosten für mehrere Regionen mit Schreibvorgängen

In einem System mit Schreibvorgängen in mehreren Regionen nehmen die tatsächlich verfügbaren Anforderungseinheiten (RUs) für Schreibvorgänge um das `N`-Fache zu, wobei `N` für die Anzahl der Schreibregionen steht. Im Gegensatz zu Schreibvorgängen in einer einzelnen Region kann hier jede Region Schreibanforderungen verarbeiten und unterstützt die Konfliktlösung. Unter Gesichtspunkten der Kostenplanung müssen Sie, um weltweite Schreibvorgänge für `M` RUs/Sek. auszuführen, M `RUs` auf Container- oder Datenbankebene bereitstellen. Anschließend können Sie beliebig viele Regionen hinzufügen und diese für Schreibvorgänge verwenden, um weltweite Schreibvorgänge für `M` RUs auszuführen.

### <a name="example"></a>Beispiel

Sie verfügen über einen Container in der Region „USA, Westen“, der für Schreibvorgänge in einer einzelnen Region konfiguriert und mit einem Durchsatz von 10.000 RUs/Sek. bereitgestellt wurde, und speichern in diesem Monat 0,5 TB Daten. Nehmen wir einmal an, Sie fügen „USA, Osten“ als zweite Region mit denselben Speicher- und Durchsatzwerten hinzu und möchten von Ihrer App aus in beiden Regionen in die Container schreiben. Ihre neue monatliche Gesamtrechnung (basierend auf 730 Stunden im Monat) sieht dann wie folgt aus:

|**Element**|**Nutzung (monatlich)**|**Rate**|**Monatliche Kosten**|
|----|----|----|----|
|Durchsatzabrechnung für Container in „USA, Westen“ (Schreibvorgänge in einer einzelnen Region) |10.000 RUs/Sek. × 730 Stunden |0,008 USD pro 100 RUs/Sek. pro Stunde |584 US-Dollar |
|Durchsatzabrechnung für Container in zwei Regionen: „USA, Westen“ und „USA, Osten“ (Schreibvorgänge in mehreren Regionen) |2 × 10.000 RUs/Sek. × 730 Stunden |0,016 US-Dollar pro 100 RUs/Sek. pro Stunde |2\.336 US-Dollar |
|Speicherabrechnung für Container in „USA, Westen“ |0,5 TB (oder 512 GB) |0,25 US-Dollar/GB |128 US-Dollar |
|Speicherabrechnung für Container in zwei Regionen: „USA, Westen“ und „USA, Osten“ |2 × 0,5 TB (oder 1.024 GB) |0,25 US-Dollar/GB |256 US-Dollar |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Optimieren der Durchsatznutzung nach Region

Bei einer ineffizienten Auslastung – beispielsweise durch mindestens eine nicht ausgelastete Leseregion – können Sie Maßnahmen ergreifen, um die RUs in Leseregionen maximal zu nutzen: Sie können Änderungsfeeds von der Leseregion nutzen oder im Fall einer übermäßigen Auslastung die Region in eine andere sekundäre Region verschieben. Dabei müssen Sie sicherstellen, dass Sie zuerst den bereitgestellten Durchsatz (RUs) in der Schreibregion optimieren. Schreibvorgänge kosten mehr als Lesevorgänge (außer bei sehr umfangreichen Abfragen), daher kann die Sicherstellung einer gleichmäßigen Auslastung eine große Herausforderung sein. Überwachen Sie den genutzten Durchsatz in Ihren Regionen in seiner Gesamtheit. Bei Bedarf können Sie Regionen hinzufügen oder entfernen, um den Lese- und Schreibdurchsatz zu skalieren. Dabei müssen Sie aber die Auswirkungen auf die Latenz für Apps berücksichtigen, die in derselben Region bereitgestellt wurden.

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie mithilfe der folgenden Artikel mehr über die Kostenoptimierung in Azure Cosmos DB erfahren:

* Weitere Informationen unter [Optimale Entwicklungs- und Testmöglichkeiten mit Azure Cosmos DB](optimize-dev-test.md)
* Weitere Informationen unter [Erläuterungen zu Ihrer Azure Cosmos DB-Rechnung](understand-your-bill.md)
* Weitere Informationen unter [Optimieren der Durchsatzkosten](optimize-cost-throughput.md)
* Weitere Informationen unter [Optimieren der Speicherkosten](optimize-cost-storage.md)
* Weitere Informationen unter [Optimieren der Kosten für Lese- und Schreibvorgänge](optimize-cost-reads-writes.md)
* Weitere Informationen unter [Optimieren der Kosten von Abfragen](./optimize-cost-reads-writes.md)
