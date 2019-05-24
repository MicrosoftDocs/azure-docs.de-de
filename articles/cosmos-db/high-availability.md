---
title: Hochverfügbarkeit in Azure Cosmos DB
description: Dieser Artikel beschreibt, wie Azure Cosmos DB Hochverfügbarkeit bietet.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 74e2d7901d127c9dd7edd8509e5bba082c4ad220
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978974"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Hochverfügbarkeit mit Azure Cosmos DB

Azure Cosmos DB repliziert Ihre Daten transparent in allen Azure-Regionen, die Ihrem Cosmos-Konto zugewiesen sind. Cosmos DB verwendet mehrere Redundanzebenen für Ihre Daten, wie in der folgenden Abbildung dargestellt:

![Physische Partitionierung](./media/high-availability/cosmosdb-data-redundancy.png)

- Die Daten in Cosmos-Containern sind [horizontal partitioniert](partitioning-overview.md).

- In jeder Region wird jede Partition durch einen Replikatsatz geschützt. Dabei werden alle Schreibvorgänge repliziert, und die Mehrheit der Replikate führt ein dauerhaftes Commit für sie aus. Replikate werden auf 10 bis 20 Fehlerdomänen verteilt.

- Jede Partition wird in allen Regionen repliziert. Jede Region enthält alle Datenpartitionen eines Cosmos-Containers und kann Schreibvorgänge zulassen sowie Lesevorgänge verarbeiten.  

Wenn Ihr Cosmos-Konto über *N* Azure-Regionen verteilt ist, gibt es mindestens *N* x 4 Kopien von allen Ihren Daten. Neben dem Zugriff mit geringer Latenz auf Ihre Daten und der Skalierung des Schreib- und Lesedurchsatzes in den Regionen, die Ihrem Cosmos-Konto zugeordnet sind, verbessert die Bereitstellung mehrerer Regionen (größer als *N*) die Verfügbarkeit noch mehr.  

## <a name="slas-for-availability"></a>SLAs für Verfügbarkeit

Als global verteilte Datenbank bietet Cosmos DB umfangreiche SLAs, die Durchsatz, Latenz im 99. Perzentil, Konsistenz und Hochverfügbarkeit umfassen. In der Tabelle unten sind die Garantieangaben für die Hochverfügbarkeit aufgeführt, die von Cosmos DB für Konten mit einer oder mehreren Regionen gelten. Für Hochverfügbarkeit konfigurieren Sie Ihre Cosmos-Konten immer mit mehreren Schreibregionen.

|Vorgangsart  | Eine Region |Mehrere Regionen (eine Schreibregion)|Mehrere Regionen (mehrere Schreibregionen) |
|---------|---------|---------|-------|
|Schreibvorgänge    | 99,99    |99,99   |99,999|
|Lesevorgänge     | 99,99    |99,999  |99,999|

> [!NOTE]
> In der Praxis ist die tatsächliche Verfügbarkeit von Schreibvorgängen für die Modelle „Begrenzte Veraltung“, „Sitzung“, „Präfixkonsistenz“ und „Letztliche Konsistenz“ deutlich höher als die veröffentlichten SLAs. Die tatsächliche Verfügbarkeit von Lesevorgängen ist für alle Konsistenzebenen deutlich höher als die veröffentlichten SLAs.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Hochverfügbarkeit mit Cosmos DB bei regionalen Ausfällen

Regionale Ausfälle sind keine Seltenheit. Deswegen stellt Azure Cosmos DB sicher, dass Ihre Datenbank immer hochverfügbar ist. Im Folgenden wird das Verhalten von Cosmos DB – abhängig von der Konfiguration Ihres Cosmos-Kontos – bei einem Ausfall beschrieben:

- Bevor ein Schreibvorgang dem Client gegenüber bestätigt wird, wird bei Cosmos DB von einem Quorum von Replikaten in der Region, die die Schreibvorgänge akzeptiert, ein dauerhaftes Commit ausgeführt.

- Konten mit mehreren Regionen, die mit mehreren Schreibregionen konfiguriert sind, sind sowohl für Schreib- als auch für Lesevorgänge hoch verfügbar. Regionale Failover werden sofort ausgeführt und erfordern keine Änderungen von der Anwendung.

- **Konten mit mehreren Regionen mit einer einzelnen Schreibregion (Ausfall einer Schreibregion):** Diese Konten bleiben bei Ausfall einer Schreibregion hochverfügbar für Lesevorgänge. Für Schreibvorgänge müssen Sie jedoch in Ihrem Cosmos-Konto die Option **Automatisches Failover aktivieren** auswählen, damit ein Failover für die betreffende Region in eine andere Region ausgeführt werden kann. Das Failover erfolgt in der Reihenfolge der von Ihnen angegebenen Priorisierung der Regionen. Wenn die betreffende Region wieder online ist, werden die nicht replizierten Daten, die sich bei Ausfall in der betreffenden Schreibregion befanden, über den [Konfliktfeed](how-to-manage-conflicts.md#read-from-conflict-feed) bereitgestellt. Anwendungen können den Konfliktfeed lesen, die Konflikte auf Grundlage der anwendungsspezifischen Logik lösen und aktualisierte Daten nach Bedarf in den Cosmos-Container zurückschreiben. Wenn die vom Ausfall betroffene Schreibregion wiederhergestellt ist, steht sie automatisch als Leseregion zur Verfügung. Sie können ein manuelles Failover aufrufen und die betreffende Region als Schreibregion konfigurieren. Sie können mithilfe der [Azure-Befehlszeilenschnittstelle oder des Azure-Portals](how-to-manage-database-account.md#manual-failover) wieder ein manuelles Failover ausführen. Es gibt **keinerlei Daten- oder Verfügbarkeitsverluste** – weder vor oder nach dem manuellen Failover noch währenddessen. Ihre Anwendung ist weiterhin hochverfügbar. 

- **Konten mit mehreren Regionen mit einer einzelnen Schreibregion (Ausfall einer Leseregion):** Diese Konten bleiben bei Ausfall einer Leseregion hochverfügbar für Lese- und Schreibvorgänge. Die betreffende Region wird automatisch von der Schreibregion getrennt und als offline gekennzeichnet. Die [Cosmos DB SDKs](sql-api-sdk-dotnet.md) leiten Leseaufrufe an die nächste verfügbare Region in der Liste der bevorzugten Regionen weiter. Wenn keine der Regionen in der Liste verfügbar ist, wird für die Aufrufe automatisch ein Fallback zur aktuellen Schreibregion durchgeführt. Zur Verarbeitung des Ausfalls einer Leseregion sind keine Änderungen an Ihrem Anwendungscode erforderlich. Wenn die betreffende Region wieder online ist, wird die vom Ausfall betroffene Leseregion automatisch mit der aktuellen Schreibregion synchronisiert. Sie steht dann wieder für die Verarbeitung von Leseanforderungen zur Verfügung. Nachfolgende Lesevorgänge werden an die wiederhergestellte Region weitergeleitet, ohne dass Änderungen an Ihrem Anwendungscode erforderlich sind. Sowohl beim Failover als auch beim erneuten Verknüpfen einer zuvor ausgefallene Region gelten weiterhin die Lesekonsistenzgarantien von Cosmos DB.

- Konten mit einer Region sind nach einem regionalen Ausfall ggf. nicht mehr verfügbar. Es wird immer empfohlen, **mindestens zwei Regionen** (idealerweise mindestens zwei Schreibregionen) für Ihr Cosmos-Konto einzurichten, um die permanente Hochverfügbarkeit sicherzustellen.

- Auch in einem extrem seltenen und unglücklichen Fall, in dem die Azure-Region dauerhaft nicht mehr wiederhergestellt werden kann, kommt es nicht zu einem Datenverlust, wenn für Ihr Cosmos-Konto mit mehreren Regionen die Standardkonsistenzebene *Sicher* konfiguriert ist. Im Falle einer dauerhaft nicht mehr wiederherstellbaren Schreibregion ist das Fenster für potenzielle Datenverluste für die Cosmos-Konten mit mehreren Regionen, für die eine Konsistenz vom Typ „Begrenzte Veraltung“ konfiguriert ist, auf das Fenster der Veraltung (*K* oder *T*) begrenzt. Für die Konsistenzebenen „Sitzung“, „Präfixkonsistenz“ und „Letztlich“ ist das Fenster für potenzielle Datenverluste auf maximal fünf Sekunden begrenzt.

## <a name="building-highly-available-applications"></a>Erstellen hochverfügbarer Anwendungen

- Um Hochverfügbarkeit von Schreib- und Lesevorgängen zu garantieren, konfigurieren Sie Ihr Cosmos-Konto so, dass es mindestens zwei Regionen mit mehreren Schreibregionen umfasst. Diese Konfiguration sorgt für höchste Verfügbarkeit, geringstmögliche Latenz und optimale Skalierbarkeit für Lese- und Schreibvorgänge und wird durch SLAs unterstützt. Weitere Informationen finden Sie unter [Konfigurieren eines Cosmos-Kontos mit mehreren Schreibregionen](tutorial-global-distribution-sql-api.md).

- Aktivieren Sie für Cosmos-Konten mit mehreren Regionen, die mit einer einzelnen Schreibregion konfiguriert sind, [„Automatisches Failover“ über die Azure-Befehlszeilenschnittstelle oder das Azure-Portal](how-to-manage-database-account.md#automatic-failover). Wenn Sie das automatische Failover aktivieren, führt Cosmos DB bei einem regionalen Ausfall ein automatisches Failover für Ihr Konto aus.  

- Selbst wenn Ihr Cosmos-Konto hochverfügbar ist, ist Ihre Anwendung möglicherweise nicht richtig dafür konzipiert, hochverfügbar zu bleiben. Um die End-to-End-Hochverfügbarkeit Ihrer Anwendung zu testen, rufen Sie im Rahmen Ihrer Anwendungs- oder Notfallwiederherstellungstests regelmäßig über die [Azure-Befehlszeilenschnittstelle oder das Azure-Portal ein manuelles Failover](how-to-manage-database-account.md#manual-failover) auf.

- Bei einer global verteilten Datenbankumgebung besteht eine direkte Beziehung zwischen der Konsistenzebene und der Datendauerhaftigkeit bei einem Ausfall in der gesamten Region. Wenn Sie Ihren Plan für die Geschäftskontinuität entwickeln, müssen Sie wissen, wie viel Zeit maximal vergehen darf, bis die Anwendung nach einer Störung vollständig wiederhergestellt ist. Die Zeit, die für die vollständige Wiederherstellung einer Anwendung erforderlich ist, wird als RTO (Recovery Time Objective) bezeichnet. Sie müssen auch wissen, über welchen Zeitraum kürzlich durchgeführte Datenupdates maximal verloren gehen dürfen, wenn die Anwendung nach einer Störung wiederhergestellt wird. Der Zeitraum der Updates, der verloren gehen darf, wird als RPO (Recovery Point Objective) bezeichnet. Informationen zum Anzeigen von RPO und RTO für Azure Cosmos DB finden Sie unter [Kompromisse in Bezug auf Konsistenz, Verfügbarkeit und Leistung](consistency-levels-tradeoffs.md#rto).

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie die folgenden Artikel lesen:

* [Kompromisse in Bezug auf Verfügbarkeit und Leistung für verschiedene Konsistenzebenen](consistency-levels-tradeoffs.md)
* [Globales Skalieren von bereitgestelltem Durchsatz](scaling-throughput.md)
* [Globale Verteilung: Hintergrundinformationen](global-dist-under-the-hood.md)
* [Konsistenzebenen in Azure Cosmos DB](consistency-levels.md)
* [Konfigurieren eines Cosmos-Kontos mit mehreren Schreibregionen](how-to-multi-master.md)
