---
title: Anforderungseinheiten als Währung für Durchsatz und Leistung in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie die erforderlichen Anforderungseinheiten in Azure Cosmos DB angeben und einschätzen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.custom: seo-nov-2020
ms.openlocfilehash: 23401885580a3883dc49eccc97c17bbedd9080ab
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187322"
---
# <a name="request-units-in-azure-cosmos-db"></a>Anforderungseinheiten in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB unterstützt viele APIs wie z.B. SQL, MongoDB, Cassandra, Gremlin und Tabelle. Jede API verfügt über einen eigenen Satz von Datenbankvorgängen. Diese Vorgänge reichen von einfachen Lese- und Schreibvorgängen für Datenpunkte bis hin zu komplexen Abfragen. Jeder Datenbankvorgang beansprucht je nach Komplexität eine bestimmte Menge an Systemressourcen.

Die Kosten sämtlicher Datenbankvorgänge werden von Azure Cosmos DB normalisiert und in Anforderungseinheiten (Request Units, RUs) ausgedrückt. Anforderungseinheiten stellen praktisch die „Währung“ zur Abstrahierung von Systemressourcen wie CPU, IOPS und Arbeitsspeicher dar, die zum Ausführen der von Azure Cosmos DB unterstützten Datenbankvorgänge erforderlich sind.

Die Kosten für einen Punktlesevorgang (d. h. das Abrufen eines einzelnen Elements anhand seiner ID und seines Partitionsschlüsselwerts) für ein Element von 1 KB betragen 1 Anforderungseinheit (oder 1 RU). Allen anderen Datenbankvorgängen werden analog dazu ebenfalls Kosten in RUs zugewiesen. Unabhängig davon, welche API Sie für die Interaktion mit Ihrem Azure Cosmos-Container verwenden, werden die Kosten immer in RUs gemessen. Unabhängig davon, ob es sich bei dem Datenbankvorgang um einen Schreib-, Lese- oder Abfragevorgang handelt, werden die Kosten immer in RUs gemessen.

Die folgende Abbildung zeigt eine Übersicht über RUs:

:::image type="content" source="./media/request-units/request-units.png" alt-text="Von Datenbankvorgängen genutzte Anforderungseinheiten" border="false":::

Zur Verwaltung und Planung der Kapazität stellt Azure Cosmos DB sicher, dass die Anzahl von RUs für einen bestimmten Datenbankvorgang und ein bestimmtes Dataset deterministisch ist. Sie können den Antwortheader untersuchen, um die von einem Datenbankvorgang genutzte Anzahl von RUs zu ermitteln. Wenn Sie mit den [Faktoren, die sich auf die Gebühren für RUs auswirken](request-units.md#request-unit-considerations), sowie mit den Durchsatzanforderungen Ihrer Anwendung vertraut sind, können Sie Ihre Anwendung kostengünstig ausführen.

Die Art des Azure Cosmos-Kontos, das Sie verwenden, bestimmt die Art, auf die verbrauchte RUs in Rechnung gestellt werden. Es gibt drei Modi, die Sie für die Erstellung eines Kontos verwenden können:

1. **Bereitgestellter Durchsatz:** In diesem Modus wird die Anzahl von Anforderungseinheiten für Ihre Anwendung auf Sekundenbasis (in Schritten von jeweils 100 RUs pro Sekunde) bereitgestellt. Um den bereitgestellten Durchsatz für Ihre Anwendung zu skalieren, können Sie die Anzahl der RUs jederzeit in Schritten von 100 RUs erhöhen oder verringern. Sie können Ihre Änderungen entweder programmgesteuert oder über das Azure-Portal vornehmen. Die Menge der bereitgestellten RUs pro Sekunde wird auf Stundenbasis abgerechnet. Weitere Informationen finden Sie im Artikel [Bereitgestellter Durchsatz](set-throughput.md).

   Durchsatz kann in zwei Granularitäten bereitgestellt werden:

   * **Container**: Weitere Informationen finden Sie unter [Bereitstellen von Durchsatz für einen Azure Cosmos-Container](how-to-provision-container-throughput.md).
   * **Datenbanken**: Weitere Informationen finden Sie unter [Bereitstellen von Durchsatz für eine Azure Cosmos-Datenbank](how-to-provision-database-throughput.md).

2. **Serverloser Modus**: In diesem Modus müssen Sie beim Erstellen von Ressourcen in Ihrem Azure Cosmos-Konto keinen Durchsatz bereitstellen. Am Ende des Abrechnungszeitraums wird Ihnen die Anzahl der Anforderungseinheiten in Rechnung gestellt, die von den Datenbankvorgängen genutzt wurden. Weitere Informationen finden Sie im Artikel [Serverloser Durchsatz](serverless.md). 

3. **Autoskalierung:** In diesem Modus können Sie den Durchsatz (RU/s) Ihrer Datenbank oder Ihres Containers automatisch und sofort anhand der Nutzung skalieren, ohne dass sich dies auf die Verfügbarkeit, die Wartezeit, den Durchsatz oder die Leistung der Workload auswirkt. Dieser Modus eignet sich gut für unternehmenskritische Workloads, die über variable oder unvorhersehbare Datenverkehrsmuster verfügen und SLAs für hohe Leistung und Skalierung erfordern. Weitere Informationen finden Sie im Artikel [Durchsatz mit Autoskalierung](provision-throughput-autoscale.md). 

## <a name="request-unit-considerations"></a>Aspekte zu Anforderungseinheiten

Berücksichtigen Sie die folgenden Faktoren, wenn Sie die Anzahl der für Ihre Workload verbrauchen RUs schätzen:

* **Elementgröße**: Je größer ein Element, desto mehr RUs werden beim Lesen oder Schreiben des Elements genutzt.

* **Elementindizierung**: Standardmäßig wird jedes Element automatisch indiziert. Wenn Sie einige Elemente in einem Container nicht indizieren, werden weniger RUs genutzt.

* **Anzahl der Elementeigenschaften**: Bei Verwendung der standardmäßigen Indizierung für alle Eigenschaften erhöht sich die Anzahl von RUs, die beim Schreiben eines Elements genutzt werden, wenn sich die Anzahl von Elementeigenschaften erhöht.

* **Indizierte Eigenschaften**: Eine Indexrichtlinie für jeden Container gibt an, welche Eigenschaften standardmäßig indiziert werden. Zum Verringern der für Schreibvorgänge genutzten RUs begrenzen Sie die Anzahl indizierter Eigenschaften.

* **Datenkonsistenz**: Im Vergleich zu anderen, weniger strengen Konsistenzebenen nutzen die Konsistenzebenen „Stark“ und „Begrenzte Veraltung“ bei Lesevorgängen ungefähr zweimal mehr RUs.

* **Lesetypen:** Punktlesevorgänge kosten erheblich weniger RUs als Abfragen.

* **Abfragemuster**: Die Komplexität einer Abfrage wirkt sich darauf aus, wie viele RUs für einen Vorgang verbraucht werden. Faktoren, die die Kosten von Abfragevorgängen beeinflussen: 
 
  * Die Anzahl der Abfrageergebnisse
  * Die Anzahl der Prädikate
  * Die Art der Prädikate
  * Die Anzahl der benutzerdefinierten Funktionen
  * Die Größe der Quelldaten
  * Die Größe des Resultsets
  * Projektionen

  Die gleiche Abfrage mit den gleichen Daten beansprucht bei wiederholter Ausführung immer die gleiche Anzahl von RUs.

* **Skriptnutzung**: Bei gespeicherten Prozeduren und Triggern hängt die Nutzung von RUs genau wie bei Abfragen von der Komplexität der ausgeführten Vorgänge ab. Sehen Sie sich beim Entwickeln Ihrer Anwendung den [Request-Charge-Header](./optimize-cost-reads-writes.md#measuring-the-ru-charge-of-a-request) an, um zu ermitteln, wie viele RUs der jeweilige Vorgang erfordert.

## <a name="request-units-and-multiple-regions"></a>Anforderungseinheiten und mehrere Regionen

Wenn Sie *R* RUs in einem Cosmos-Container (oder einer Datenbank) bereitstellen, stellt Cosmos DB sicher, dass in *jeder* Region, die mit Ihrem Cosmos-Konto verknüpft ist, *R* RUs verfügbar sind. Sie können RUs nicht selektiv einer bestimmten Region zuweisen. Die in einem Cosmos-Container (oder einer Datenbank) bereitgestellten RUs werden in allen mit Ihrem Cosmos-Konto verbundenen Regionen bereitgestellt.

Angenommen, ein Cosmos-Container ist mit *R* RUs konfiguriert, und es gibt *N* Regionen, die dem Cosmos-Konto zugeordnet sind, dann beträgt die Gesamtzahl von im Container verfügbaren RUs = *R* x *N*.

Die Wahl des [Konsistenzmodells](consistency-levels.md) wirkt sich auch auf den Durchsatz aus. Für die eher gelockerten Konsistenzebenen (z.B. *Sitzung*, *Präfixkonsistenz* und *letztliche Konsistenz*) können Sie den etwa 2-fachen Lesedurchsatz im Vergleich zu höheren Konsistenzebenen (z.B. *begrenzte Veraltung* oder *starke Konsistenz*) erzielen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Bereitstellen von Durchsatz für Cosmos-Container und -Datenbanken](set-throughput.md).
- Weitere Informationen zum [serverlosen Modus in Azure Cosmos DB](serverless.md).
- Erfahren Sie mehr über [logische Partitionen](./partitioning-overview.md).
- Erfahren Sie mehr über das [Bereitstellen von Durchsatz für einen Azure Cosmos-Container](how-to-provision-container-throughput.md).
- Erfahren Sie mehr über das [Bereitstellen des Durchsatzes für eine Datenbank in Azure Cosmos](how-to-provision-database-throughput.md).
- Erfahren Sie mehr über das [Finden der Gebühr für Anforderungseinheiten für einen Vorgang](find-request-unit-charge.md).
- Erfahren Sie mehr über das [Optimieren der Kosten für bereitgestellten Durchsatz in Azure Cosmos DB](optimize-cost-throughput.md).
- Erfahren Sie mehr über das [Optimieren der Lese- und Schreibkosten in Azure Cosmos DB](optimize-cost-reads-writes.md).
- Erfahren Sie mehr über das [Optimieren der Abfragekosten in Azure Cosmos DB](./optimize-cost-reads-writes.md).
- Erfahren Sie mehr über das [Verwenden von Metriken zum Überwachen des Durchsatzes](use-metrics.md).
