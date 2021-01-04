---
title: Erstellen eines synthetischen Partitionsschlüssels in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie synthetische Partitionsschlüssel in Azure Cosmos-Containern verwenden, um die Daten und die Arbeitsauslastung gleichmäßig auf die Partitionsschlüssel zu verteilen.
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 6b8bc44f1ba5624c37620205aaa574e618ef395f
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340650"
---
# <a name="create-a-synthetic-partition-key"></a>Erstellen eines synthetischen Partitionsschlüssels
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Es hat sich bewährt, mit einem Partitionsschlüssel mit vielen (hunderten oder tausenden) unterschiedlichen Werten zu arbeiten. Ziel ist es, Ihre Daten und die Workload gleichmäßig auf die Elemente zu verteilen, die diesen Partitionsschlüsselwerten zugeordnet sind. Wenn in den Daten keine solche Eigenschaft enthalten ist, können Sie einen *synthetischen Partitionsschlüssel* erstellen. In diesem Artikel werden einige grundlegende Verfahren zum Generieren synthetischer Partitionsschlüssel für Ihre Cosmos-Container beschrieben.

## <a name="concatenate-multiple-properties-of-an-item"></a>Verketten mehrerer Eigenschaften eines Elements

Sie können einen Partitionsschlüssel bilden, indem Sie mehrere Eigenschaftswerte zu einer einzigen künstlichen `partitionKey`-Eigenschaft verketten. Diese Schlüssel werden als synthetische Schlüssel bezeichnet. Betrachten Sie etwa das folgende Beispieldokument:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Für das vorherige Dokument besteht eine Option darin, „/deviceId“ oder „/date“ als Partitionsschlüssel festzulegen. Verwenden Sie diese Option, wenn Sie Ihren Container entweder anhand der Geräte-ID oder des Datums partitionieren möchten. Eine andere Möglichkeit ist ein Verketten dieser beiden Werte in einer synthetischen `partitionKey`-Eigenschaft, die als Partitionsschlüssel verwendet wird.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

In Echtzeitszenarien können Sie Tausende von Elementen in einer Datenbank ablegen. Anstatt den synthetischen Schlüssel manuell hinzuzufügen, definieren Sie clientseitige Logik zur Verkettung von Werten und fügen den synthetischen Schlüssel in die Elemente in Ihren Cosmos-Containern ein.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Verwenden eines Partitionsschlüssels mit einem zufälligen Suffix

Noch eine weitere Strategie für ein gleichmäßigeres Verteilen der Workload stellt das Anfügen einer Zufallszahl am Ende des Partitionsschlüsselwerts dar. Wenn Sie Elemente auf diese Weise verteilen, können Sie Schreibvorgänge über Partitionen hinweg parallel ausführen.

Ein Beispiel ist, wenn ein Partitionsschlüssel ein Datum darstellt. Sie können eine Zufallszahl von 1 bis 400 wählen und als Suffix mit dem Datum verketten. Diese Methode führt zu Partitionsschlüsselwerten wie  `2018-08-09.1`, `2018-08-09.2` usw. bis  `2018-08-09.400`. Da Sie den Partitionsschlüssel zufällig zuordnen, werden die Schreibvorgänge im Container an jedem Tag gleichmäßig auf mehrere Partitionen verteilt. Diese Methode führt zu mehr Parallelität und einem insgesamt höheren Durchsatz.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Verwenden von Partitionsschlüsseln mit vorab berechneten Suffixen 

Die Strategie mit nach dem Zufallsprinzip gewählten Suffixen kann den Schreibdurchsatz erheblich verbessern, erschwert aber das Lesen eines bestimmten Elements. Denn Sie kennen den Suffixwert nicht, der beim Schreiben des Elements verwendet wurde. Um das Lesen einzelner Elemente zu vereinfachen, verwenden Sie vorab berechnete Suffixe. Anstelle einer Zufallszahl verwenden Sie für die Verteilung der Elemente auf die Partitionen eine Zahl, die basierend auf etwas, das Sie abfragen möchten, berechnet wird.

Gehen Sie wieder vom vorherigen Beispiel aus, bei dem in einem Container das Datum als Partitionsschlüssel verwendet wird. Nehmen wir nun an, dass jedes Element das Attribut  `Vehicle-Identification-Number` (`VIN`) hat, auf das wir zugreifen möchten. Nehmen wir weiter an, dass Sie häufig Abfragen durchführen, um Elemente zusätzlich zum Datum über die `VIN` zu finden. Bevor Ihre Anwendung das Element in den Container schreibt, kann es basierend auf der VIN ein Hashsuffix berechnen und an das Partitionsschlüsseldatum anfügen. Die Berechnung kann eine gleichmäßig verteilte Zahl von 1 bis 400 erzeugen. Dieses Ergebnis ist vergleichbar mit den Ergebnissen der Zufallszahlstrategie. Der Partitionsschlüsselwert ist dann eine Verkettung aus dem Datum und dem berechneten Ergebnis.

Mit dieser Strategie werden die Schreibvorgänge gleichmäßig auf die Partitionsschlüsselwerte und damit auf die Partitionen verteilt. Sie können ein bestimmtes Element und das Datum ganz einfach lesen, da Sie den Partitionsschlüsselwert für eine bestimmte `Vehicle-Identification-Number` berechnen können. Diese Methode hat den Vorteil, dass Sie es vermeiden, einen einzelnen aktiven Partitionsschlüssel (der die gesamte Workload aufnimmt) zu erstellen. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Konzept der Partitionierung finden Sie in den folgenden Artikeln:

* Erfahren Sie mehr über [logische Partitionen](partitioning-overview.md).
* Erfahren Sie mehr über das [Bereitstellen von Durchsatz für Cosmos-Container und -Datenbanken](set-throughput.md).
* Erfahren Sie mehr über das [Bereitstellen von Durchsatz für einen Azure Cosmos-Container](how-to-provision-container-throughput.md).
* Erfahren Sie mehr über das [Bereitstellen des Durchsatzes für eine Datenbank in Azure Cosmos](how-to-provision-database-throughput.md).
