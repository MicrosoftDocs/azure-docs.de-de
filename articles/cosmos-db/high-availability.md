---
title: Hochverfügbarkeit in Azure Cosmos DB
description: Dieser Artikel beschreibt, wie Azure Cosmos DB Hochverfügbarkeit bietet.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 58507703ca3440e73dbc41757e0bc70f56e886c3
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360155"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Wie bietet Azure Cosmos DB Hochverfügbarkeit?
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB bietet hauptsächlich auf zwei Arten Hochverfügbarkeit. Zum einen repliziert Azure Cosmos DB Daten in mehrere in einem Cosmos-Konto konfigurierte Regionen, zum anderen verwaltet Azure Cosmos DB vier Replikate von Daten innerhalb einer Region.

Azure Cosmos DB ist ein global verteilter Datenbankdienst und ein grundlegender Azure-Dienst. Der Dienst ist standardmäßig in [allen Regionen verfügbar, in denen Azure verfügbar ist](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all). Sie können Ihrem Azure Cosmos-Konto eine beliebige Anzahl von Azure-Regionen zuordnen, und Ihre Daten werden automatisch und auf transparente Weise repliziert. Sie können für Ihr Azure Cosmos-Konto jederzeit eine Region hinzufügen oder entfernen. Cosmos DB ist in allen fünf Azure-Cloudumgebungen verfügbar, die für Kunden bereitgestellt werden:

* **Öffentliche Azure-Cloud**, die global verfügbar ist.

* **Azure China 21Vianet** ist dank einer exklusiven Partnerschaft zwischen Microsoft und 21Vianet, einem der größten Internetanbieter in China, verfügbar.

* **Azure Deutschland** stellt Dienste basierend auf einem Datentreuhänder-Modell bereit. Damit wird sichergestellt, dass Kundendaten in Deutschland und unter der Kontrolle von T-Systems International GmbH, einem Subunternehmen von Deutsche Telekom, verbleiben. Dieses Unternehmen fungiert als Datentreuhänder in Deutschland.

* **Azure Government** ist in vier Regionen der USA für US-Regierungsbehörden und ihre Partner verfügbar.

* **Azure Government für das US-Verteidigungsministerium** ist in zwei Regionen der USA für das US-Verteidigungsministerium verfügbar.

Innerhalb einer Region verwaltet Azure Cosmos DB vier Kopien Ihrer Daten als Replikate in physischen Partitionen, wie in der folgenden Abbildung dargestellt:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Physische Partitionierung" border="false":::

* Die Daten in Azure Cosmos-Containern sind [horizontal partitioniert](partitioning-overview.md).

* Eine Partitionsgruppe ist eine Sammlung mehrerer Replikatgruppen. In jeder Region wird jede Partition durch einen Replikatsatz geschützt. Dabei werden alle Schreibvorgänge repliziert, und die Mehrheit der Replikate führt ein dauerhaftes Commit für sie aus. Replikate werden auf 10 bis 20 Fehlerdomänen verteilt.

* Jede Partition wird in allen Regionen repliziert. Jede Region enthält alle Datenpartitionen eines Azure Cosmos-Containers und kann Schreibvorgänge zulassen sowie Lesevorgänge verarbeiten.  

Wenn Ihr Azure Cosmos-Konto über *N* Azure-Regionen verteilt ist, gibt es mindestens *N* x 4 Kopien von allen Ihren Daten. Ein Azure Cosmos-Konto in mehr als zwei Regionen verbessert die Verfügbarkeit Ihrer Anwendung und sorgt für eine geringe Latenz zwischen den zugeordneten Regionen.

## <a name="slas-for-availability"></a>SLAs für Verfügbarkeit

Als global verteilte Datenbank bietet Azure Cosmos DB umfangreiche SLAs, die Durchsatz, Latenz im 99. Perzentil, Konsistenz und Hochverfügbarkeit umfassen. In der Tabelle unten sind die Garantieangaben für die Hochverfügbarkeit aufgeführt, die von Azure Cosmos DB für Konten mit einer oder mehreren Regionen gelten. Für Hochverfügbarkeit konfigurieren Sie Ihre Azure Cosmos-Konten immer mit mehreren Schreibregionen.

|Vorgangsart  | Eine Region |Mehrere Regionen (eine Schreibregion)|Mehrere Regionen (mehrere Schreibregionen) |
|---------|---------|---------|-------|
|Schreibvorgänge    | 99,99    |99,99   |99,999|
|Lesevorgänge     | 99,99    |99,999  |99,999|

> [!NOTE]
> In der Praxis ist die tatsächliche Verfügbarkeit von Schreibvorgängen für die Modelle „Begrenzte Veraltung“, „Sitzung“, „Präfixkonsistenz“ und „Letztliche Konsistenz“ deutlich höher als die veröffentlichten SLAs. Die tatsächliche Verfügbarkeit von Lesevorgängen ist für alle Konsistenzebenen deutlich höher als die veröffentlichten SLAs.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Hochverfügbarkeit mit Azure Cosmos DB bei regionalen Ausfällen

Für die seltenen Fälle eines regionalen Ausfalls stellt Azure Cosmos DB sicher, dass Ihre Datenbank immer hoch verfügbar ist. Im Folgenden wird das Verhalten von Azure Cosmos DB – abhängig von der Konfiguration Ihres Azure Cosmos-Kontos – bei einem Ausfall beschrieben:

* Bevor ein Schreibvorgang dem Client gegenüber bestätigt wird, wird bei Azure Cosmos DB von einem Quorum von Replikaten in der Region, die die Schreibvorgänge akzeptiert, ein dauerhaftes Commit ausgeführt. Weitere Informationen finden Sie unter [Konsistenzebenen und Durchsatz](./consistency-levels.md#consistency-levels-and-throughput).

* Konten mit mehreren Regionen, die mit mehreren Schreibregionen konfiguriert sind, sind sowohl für Schreib- als auch für Lesevorgänge hoch verfügbar. Regionale Failover werden im Azure Cosmos DB-Client erkannt und ausgeführt. Sie werden auch sofort ausgeführt und erfordern keine Änderungen durch die Anwendung.

* Konten mit einer Region sind nach einem regionalen Ausfall ggf. nicht mehr verfügbar. Es wird immer empfohlen, **mindestens zwei Regionen** (idealerweise mindestens zwei Schreibregionen) für Ihr Azure Cosmos-Konto einzurichten, um die permanente Hochverfügbarkeit sicherzustellen.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Konten mit mehreren Regionen mit einer einzelnen Schreibregion (Ausfall einer Schreibregion)

* Während eines Ausfalls der Schreibregion wird das Azure Cosmos-Konto automatisch eine sekundäre Region zur neuen primären Schreibregion machen, wenn die Option **Automatisches Failover aktivieren** für das Azure Cosmos-Konto konfiguriert ist. Wenn der entsprechende Vorgang aktiviert ist, erfolgt das Failover in eine andere Region in der von Ihnen festgelegten Reihenfolge der Regionspriorität.

* Nachdem die betroffene Region wieder in den Onlinezustand versetzt wurde, stehen sämtliche Daten aus Schreibvorgängen, die während des Ausfalls der Region nicht repliziert wurden, über den [Konfliktfeed](how-to-manage-conflicts.md#read-from-conflict-feed) zur Verfügung. Anwendungen können den Konfliktfeed lesen, die Konflikte auf Grundlage der anwendungsspezifischen Logik lösen und die aktualisierten Daten nach Bedarf in den Azure Cosmos-Container zurückschreiben.

* Wenn die vom Ausfall betroffene Schreibregion wiederhergestellt ist, steht sie automatisch als Leseregion zur Verfügung. Sie können zurück zur wiederhergestellten Region als Schreibregion wechseln. Sie können die Regionen per [PowerShell, Azure CLI oder Azure-Portal](how-to-manage-database-account.md#manual-failover) wechseln. Vor, während und nach dem Wechsel zur Schreibregion tritt kein **Daten- oder Verfügbarkeitsverlust** auf, und Ihre Anwendung ist weiterhin hochverfügbar.

> [!IMPORTANT]
> Wir empfehlen Ihnen dringend, die für Produktionsworkloads genutzten Azure Cosmos-Konten zu konfigurieren, um **automatische Failover zu ermöglichen**. Für ein manuelles Failover ist eine Verbindung zwischen der sekundären und primären Schreibregion erforderlich, damit eine Konsistenzprüfung durchgeführt und sichergestellt werden kann, dass es beim Failover nicht zu Datenverlust kommt. Wenn die primäre Region nicht verfügbar ist, kann diese Konsistenzprüfung nicht vollständig durchgeführt werden, und das manuelle Failover ist nicht erfolgreich. Dies hat einen Verlust der Schreibverfügbarkeit für die Dauer des regionalen Ausfalls zur Folge.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Konten mit mehreren Regionen mit einer einzelnen Schreibregion (Ausfall einer Leseregion)

* Bei einem Ausfall der Leseregion besteht für Azure Cosmos-Konten mit einer Konsistenzebene bzw. hoher Konsistenz mit drei oder mehr Leseregionen weiterhin Hochverfügbarkeit für Lese- und Schreibvorgänge.

* Für Azure Cosmos-Konten mit hoher Konsistenz mit maximal zwei Leseregionen (einschließlich der Lese- und Schreibregion) geht die Schreibverfügbarkeit bei einem Ausfall der Leseregion verloren.

* Die betreffende Region wird automatisch getrennt und als offline gekennzeichnet. Die [Azure Cosmos DB SDKs](sql-api-sdk-dotnet.md) leiten Leseaufrufe an die nächste verfügbare Region in der Liste der bevorzugten Regionen weiter.

* Wenn keine der Regionen in der Liste verfügbar ist, wird für die Aufrufe automatisch ein Fallback zur aktuellen Schreibregion durchgeführt.

* Zur Verarbeitung des Ausfalls einer Leseregion sind keine Änderungen an Ihrem Anwendungscode erforderlich. Wenn die betreffende Leseregion wieder online ist, wird sie automatisch mit der aktuellen Schreibregion synchronisiert und steht dann wieder für die Verarbeitung von Leseanforderungen zur Verfügung.

* Nachfolgende Lesevorgänge werden an die wiederhergestellte Region weitergeleitet, ohne dass Änderungen an Ihrem Anwendungscode erforderlich sind. Sowohl beim Failover als auch beim erneuten Verknüpfen einer zuvor ausgefallene Region gelten weiterhin die Lesekonsistenzgarantien von Azure Cosmos DB.

* Auch in dem seltenen und unglücklichen Fall, in dem die Azure-Region dauerhaft nicht mehr wiederhergestellt werden kann, kommt es nicht zu einem Datenverlust, wenn für Ihr Azure Cosmos-Konto mit mehreren Regionen die Konsistenz *Sicher* konfiguriert ist. Im Falle einer dauerhaft nicht wiederherstellbaren Schreibregion, einem Azure Cosmos-Konto mit mehreren Regionen, das mit Konsistenz vom Typ „Begrenzte Veraltung“ konfiguriert ist, ist das Fenster für potenziellen Datenverlust auf das Veraltungsfenster (*K* oder *T*) beschränkt, wobei K=100.000 Aktualisierungen und T=5 Minuten. Für Sitzungs-, Präfixkonsistenz- und letztliche Konsistenzebenen ist das mögliche Datenverlustfenster auf maximal 15 Minuten beschränkt. Weitere Informationen zu RTO- und RPO-Zielen für Azure Cosmos DB finden Sie unter [Konsistenzebenen und Datendauerhaftigkeit](./consistency-levels.md#rto).

## <a name="availability-zone-support"></a>Unterstützung von Verfügbarkeitszonen

Neben regionsübergreifender Resilienz kann nun beim Auswählen einer Region, die Ihrer Azure Cosmos-Datenbank zugeordnet werden soll, auch **Zonenredundanz** aktiviert werden.

Durch die Unterstützung von Verfügbarkeitszonen stellt Azure Cosmos DB sicher, dass Replikate in mehreren Zonen einer bestimmten Region platziert werden, um Hochverfügbarkeit und Resilienz bei Zonenausfällen zu gewährleisten. Wartezeit und andere SLAs bleiben in dieser Konfiguration unverändert. Sollte eine einzelne Zone ausfallen, sorgt die Zonenredundanz für vollständige Dauerhaftigkeit (RPO: 0) und Verfügbarkeit (RTO: 0) der Daten.

Zonenredundanz ist eine *Ergänzungsfunktion* für die [Replikation in Schreibvorgängen in mehreren Regionen](how-to-multi-master.md). Zonenredundanz allein reicht jedoch nicht aus, um regionale Resilienz zu erreichen. Daher empfiehlt es sich beispielsweise bei regionalen Ausfällen oder bei regionsübergreifenden Zugriffen mit geringer Wartezeit, neben der Zonenredundanz auch mehrere Schreibregionen zu verwenden.

Wenn Sie für Ihr Azure Cosmos-Konto Schreibvorgänge in mehreren Regionen konfigurieren, können Sie die Zonenredundanz ohne zusätzliche Kosten aktivieren. Lesen Sie andernfalls weiter unten den Hinweis zu den Preisen für die Unterstützung der Zonenredundanz. Sie können die Zonenredundanz für eine vorhandene Region Ihres Azure Cosmos-Kontos aktivieren, indem Sie die Region entfernen und anschließend mit aktivierter Zonenredundanz wieder hinzufügen. Eine Liste der Regionen, in denen Verfügbarkeitszonen unterstützt werden, finden Sie in der Dokumentation zu [Verfügbarkeitszonen](../availability-zones/az-region.md).

In der folgenden Tabelle ist die Hochverfügbarkeitsfunktion verschiedener Kontokonfigurationen zusammengefasst:

|KPI  |Einzelne Region ohne Verfügbarkeitszonen (keine VZ)  |Einzelne Region mit Verfügbarkeitszonen (VZ)  |Schreibvorgänge in mehreren Regionen mit Verfügbarkeitszonen (VZ, zwei Regionen) – empfohlene Einstellung |
|---------|---------|---------|---------|
|Schreibverfügbarkeit (SLA) | 99,99 % | 99,99 % | 99,999% |
|Leseverfügbarkeit (SLA)  | 99,99 % | 99,99 % | 99,999% |
|Preis | Preis für einzelne Region | Preis für einzelne Region (Verfügbarkeitszone) | Preis für mehrere Regionen |
|Zonenfehler: Datenverlust | Datenverlust | Kein Datenverlust | Kein Datenverlust |
|Zonenfehler: Verfügbarkeit | Verlust der Verfügbarkeit | Kein Verlust der Verfügbarkeit | Kein Verlust der Verfügbarkeit |
|Wartezeit beim Lesen | Regionsübergreifend | Regionsübergreifend | Niedrig |
|Wartezeit beim Schreiben | Regionsübergreifend | Regionsübergreifend | Niedrig |
|Regionaler Ausfall: Datenverlust | Datenverlust |  Datenverlust | Datenverlust <br/><br/> Bei Verwendung der Konsistenz vom Typ „Begrenzte Veraltung“ mit mehreren Schreibregionen und mehreren Regionen sind Datenverluste auf die begrenzte Veraltung beschränkt, die für Ihr Konto konfiguriert ist. <br /><br />Sie können Datenverluste bei einem regionalen Ausfall vermeiden, indem Sie eine hohe Konsistenz mit mehreren Regionen konfigurieren. Bei dieser Option müssen aber Kompromisse in Bezug auf die Verfügbarkeit und Leistung eingegangen werden. Dies kann nur für Konten konfiguriert werden, die für Schreibvorgänge in eine einzelne Region eingerichtet wurden. |
|Regionaler Ausfall: Verfügbarkeit | Verlust der Verfügbarkeit | Verlust der Verfügbarkeit | Kein Verlust der Verfügbarkeit |
|Throughput | X RU/s (bereitgestellter Durchsatz) | X RU/s (bereitgestellter Durchsatz * 1,25) | 2X RU/s (bereitgestellter Durchsatz) <br/><br/> Bei diesem Konfigurationsmodus wird im Vergleich zu einer einzelnen Region mit Verfügbarkeitszonen die doppelte Menge an Durchsatz benötigt, da zwei Regionen vorhanden sind. |

> [!NOTE]
> Für die Unterstützung von Verfügbarkeitszonen müssen für ein Azure Cosmos-Konto mit mehreren Regionen Schreibvorgänge in mehreren Regionen aktiviert sein.

Sie können die Zonenredundanz aktivieren, wenn Sie eine Region zu neuen oder bereits vorhandenen Azure Cosmos-Konten hinzufügen. Wenn Sie die Zonenredundanz für Ihr Azure Cosmos-Konto aktivieren möchten, müssen Sie das Flag `isZoneRedundant` für einen bestimmten Standort auf `true` festlegen. Dieses Flag kann in der Standorteigenschaft festgelegt werden. Mit dem folgenden PowerShell-Codeausschnitt wird beispielsweise die Zonenredundanz für die Region „Asien, Südosten“ aktiviert:

Verfügbarkeitszonen können über Folgendes aktiviert werden:

* [Azure portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Azure-Befehlszeilenschnittstelle](manage-with-cli.md#add-or-remove-regions)

* [Azure-Ressourcen-Manager-Vorlagen](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>Erstellen hochverfügbarer Anwendungen

* Überprüfen Sie während dieser Ereignisse das erwartete [Verhalten der Azure Cosmos DB-SDKs](troubleshoot-sdk-availability.md) und welche Konfigurationen sich darauf auswirken.

* Um Hochverfügbarkeit von Schreib- und Lesevorgängen zu garantieren, konfigurieren Sie Ihr Azure Cosmos-Konto so, dass es mindestens zwei Regionen mit mehreren Schreibregionen umfasst. Diese Konfiguration sorgt für höchste Verfügbarkeit, geringstmögliche Latenz und optimale Skalierbarkeit für Lese- und Schreibvorgänge und wird durch SLAs unterstützt. Weitere Informationen finden Sie unter [Konfigurieren eines Azure Cosmos-Kontos mit mehreren Schreibregionen](tutorial-global-distribution-sql-api.md).

* Aktivieren Sie für Azure Cosmos-Konten mit mehreren Regionen, die mit einer einzelnen Schreibregion konfiguriert sind, [„Automatisches Failover“ über die Azure CLI oder das Azure-Portal](how-to-manage-database-account.md#automatic-failover). Wenn Sie das automatische Failover aktivieren, führt Cosmos DB bei einem regionalen Ausfall ein automatisches Failover für Ihr Konto aus.  

* Auch wenn Ihr Azure Cosmos-Konto hochverfügbar ist, ist Ihre Anwendung unter Umständen nicht richtig dafür konzipiert, hochverfügbar zu bleiben. Um die End-to-End-Hochverfügbarkeit Ihrer Anwendung zu testen, deaktivieren Sie im Rahmen von Übungen für Anwendungstests oder Notfallwiederherstellungen vorübergehend das automatische Failover für das Konto, rufen das [manuelle Failover per PowerShell, Azure CLI oder Azure-Portal](how-to-manage-database-account.md#manual-failover) auf und überwachen dann das Failover Ihrer Anwendung. Nach der Fertigstellung können Sie einen Failover zurück zur primären Region durchführen und den automatischen Failover für das Konto wiederherstellen.

* Bei einer global verteilten Datenbankumgebung besteht eine direkte Beziehung zwischen der Konsistenzebene und der Datendauerhaftigkeit im Falle eines regionsweiten Ausfalls. Wenn Sie Ihren Plan für die Geschäftskontinuität entwickeln, müssen Sie wissen, wie viel Zeit maximal vergehen darf, bis die Anwendung nach einer Störung vollständig wiederhergestellt ist. Die Zeit, die für die vollständige Wiederherstellung einer Anwendung erforderlich ist, wird als RTO (Recovery Time Objective) bezeichnet. Sie müssen auch wissen, über welchen Zeitraum kürzlich durchgeführte Datenupdates maximal verloren gehen dürfen, wenn die Anwendung nach einer Störung wiederhergestellt wird. Der Zeitraum der Updates, der verloren gehen darf, wird als RPO (Recovery Point Objective) bezeichnet. Informationen zum Anzeigen von RPO und RTO für Azure Cosmos DB finden Sie unter [Kompromisse in Bezug auf Konsistenz, Verfügbarkeit und Leistung](./consistency-levels.md#rto).

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie die folgenden Artikel lesen:

* [Kompromisse in Bezug auf Verfügbarkeit und Leistung für verschiedene Konsistenzebenen](./consistency-levels.md)

* [Globales Skalieren von bereitgestelltem Durchsatz](./request-units.md)

* [Globale Verteilung: Hintergrundinformationen](global-dist-under-the-hood.md)

* [Konsistenzebenen in Azure Cosmos DB](consistency-levels.md)

* [Konfigurieren eines Cosmos-Kontos mit mehreren Schreibregionen](how-to-multi-master.md)

* [Diagnostizieren und Beheben von Problemen bei der Verfügbarkeit von Azure Cosmos-SDKs in Umgebungen mit mehreren Regionen](troubleshoot-sdk-availability.md)