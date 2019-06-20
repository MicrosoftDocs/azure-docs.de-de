---
title: Bereitstellungsstrategien und bewährte Methoden zur Optimierung der Leistung – Azure Search
description: Lernen Sie Techniken und bewährte Methoden zum Optimieren der Leistung von Azure Search und zum Konfigurieren der optimalen Skalierung kennen.
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 32352a857f0a74dc008dc1ad76b4a5951a36b956
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024549"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>Bereitstellungsstrategien und bewährte Methoden zur Optimierung der Leistung in Azure Search

Dieser Artikel beschreibt bewährte Methoden für fortgeschrittene Szenarien mit hohen Anforderungen an Skalierbarkeit und Verfügbarkeit. 

## <a name="develop-baseline-numbers"></a>Entwickeln von Baselinewerten
Bei der Optimierung der Suchleistung sollten Sie sich darauf konzentrieren, die Ausführungszeit der Abfrage zu verkürzen. Zu diesem Zweck müssen Sie wissen, wie eine typische Suchabfrage aussieht. Die folgenden Richtlinien können Ihnen helfen, Baselinewerte für Abfragen zu ermitteln.

1. Wählen Sie eine Ziellatenz aus (also die maximale Zeitdauer), die bis zum Abschluss einer typischen Suchanforderung vergehen darf.
2. Erstellen und testen Sie eine Workload für Ihren Suchdienst mit einem realistischen Dataset, um diese Latenzraten zu messen.
3. Beginnen Sie mit einer geringen Anzahl von Abfragen pro Sekunde, und erhöhen Sie die im Test ausgeführte Abfrageanzahl dann schrittweise, bis die Abfragelatenz die definierte Ziellatenz unterschreitet. Dies ist ein wichtiger Benchmark, mit dem Sie die Skalierung planen können, wenn die Nutzungsrate Ihrer Anwendung steigt.
4. Verwenden Sie HTTP-Verbindungen nach Möglichkeit wieder. Bei Verwendung des Azure Search .NET SDK sollten Sie eine Instanz oder eine [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient)-Instanz wiederverwenden. Wenn Sie die REST-API nutzen, sollten Sie eine einzelne HttpClient-Instanz wiederverwenden.
5. Variieren Sie die Inhalte von Abfrageanforderungen, sodass die Suche über verschiedene Teile Ihres Index erfolgt. Das Variieren ist wichtig, denn wenn Sie immer wieder die gleichen Suchanforderungen ausführen, führt die Zwischenspeicherung von Daten dazu, dass die Leistung besser aussieht als bei ungleichartigen Abfragen.
6. Variieren Sie die Struktur von Abfrageanforderungen, sodass Sie verschiedene Arten von Suchabfragen erhalten. Nicht jede Suchabfrage wird mit der gleichen Leistung ausgeführt. Eine Dokumentsuche oder ein Suchvorschlag beispielsweise wird in der Regel schneller durchgeführt als eine Abfrage mit einer großen Anzahl von Facets und Filtern. Die Testzusammenstellung sollte verschiedene Abfragen etwa im gleichen Verhältnis enthalten, wie Sie sie in der Produktion erwarten würden.  

Beim Erstellen dieser Testworkloads müssen Sie einige Merkmale von Azure Search berücksichtigen:

+ Es ist möglich, den Dienst durch zu viele gleichzeitige Suchabfragen zu überladen. In diesem Fall werden HTTP 503-Antwortcodes angezeigt. Um den Fehler 503 während des Tests zu vermeiden, empfiehlt es sich, mit Suchanforderungen mit unterschiedlichem Umfang zu beginnen, um die Unterschiede in den Latenzraten zu ermitteln, die sich beim Hinzufügen weiterer Suchanforderungen ergeben.

+ Azure Search führt keine Indizierungsaufgaben im Hintergrund aus. Wenn Ihr Dienst Abfrage- und Indizierungsworkloads gleichzeitig verarbeitet, berücksichtigen Sie dies, indem Sie entweder Indizierungsaufträge in Ihre Abfragetests integrieren oder Optionen für die Ausführung von Indizierungsaufträgen während der Nebenzeiten untersuchen.

> [!NOTE]
> [Visual Studio-Funktionen für Auslastungstests](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) eignen sich sehr gut zum Ausführen Ihrer Benchmarktests, da Sie damit HTTP-Anforderungen so ausführen können, wie es für Suchabfragen bei Azure Search notwendig ist. Außerdem lassen sich Suchanforderungen parallelisieren.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>Skalieren für hohes Abfragevolumen und gedrosselte Anforderungen
Wenn Sie zu viele gedrosselte Anforderungen empfangen oder die Ziellatenzraten aufgrund einer erhöhten Abfragelast überschreiten, können Sie die Latenzraten auf zwei Arten senken:

1. **Erhöhen der Anzahl der Replikate:**  Ein Replikat ist eine Kopie Ihrer Daten und ermöglicht Azure Search, die Anforderungslast auf die verschiedenen Kopien zu verteilen.  Der gesamte Lastenausgleich und die Replikation der Daten auf die Kopien werden von Azure Search verwaltet. Sie können die Anzahl der Replikate, die Ihrem Dienst zugeordnet sind, jederzeit ändern.  In einem Suchdienst mit Tarif „Standard“ können Sie bis zu 12 Replikate zuordnen, im Tarif „Basic“ bis zu drei. Replikate können über das [Azure-Portal](search-create-service-portal.md) oder mithilfe von [PowerShell](search-manage-powershell.md) angepasst werden.
2. **Wechseln zu einem höheren Search-Tarif:**  Für Azure Search stehen [verschiedene Tarife](https://azure.microsoft.com/pricing/details/search/) zur Verfügung, von denen jeder eine andere Leistungsstufe bietet.  Zuweilen treten so viele Abfragen auf, dass Ihr aktueller Tarif keine ausreichend niedrigen Latenzraten mehr bieten kann, selbst wenn Sie bereits die maximale Anzahl von Replikaten zugewiesen haben. In diesem Fall sollten Sie in Erwägung ziehen, in einen höheren Tarif zu wechseln, wie z.B. zu Azure Search S3, der sich sehr gut für Szenarien mit einer großen Anzahl von Dokumenten und extrem hohen Abfrageworkloads eignet.

## <a name="scaling-for-slow-individual-queries"></a>Skalieren für langsame Einzelabfragen
Ein weiterer Grund für hohe Latenzraten kann darin liegen, dass eine einzelne Abfrage zu lange dauert. In diesem Fall hilft es nicht, weitere Replikate hinzuzufügen. Die folgenden beiden Optionen können helfen:

1. **Erhöhen der Anzahl der Partitionen:** Eine Partition ist ein Mechanismus, mit dem Ihre Daten auf zusätzliche Ressourcen aufgeteilt werden. Das Hinzufügen einer zweiten Partition teilt die Daten auf zwei Gruppen auf, eine dritte Partition teilt sie auf drei auf und so weiter. Ein positiver Nebeneffekt ist, dass langsamere Abfragen aufgrund der parallelen Datenverarbeitung manchmal schneller erfolgen. Wir haben eine Parallelisierung bei Abfragen mit geringer Selektivität festgestellt, z.B. Abfragen, die vielen Dokumenten entsprechen, oder Facets, bei denen Zählungen über eine große Anzahl von Dokumenten erfolgen. Da für die Bewertung der Relevanz von Dokumenten oder die Ermittlung der Anzahl von Dokumenten eine umfassende Berechnung erforderlich ist, können durch das Hinzufügen weiterer Partitionen Abfragen schneller abgeschlossen werden.  
   
   In einem Suchdienst mit Tarif „Standard“ können maximal 12 Partitionen eingerichtet werden, im Tarif „Basic“ nur eine.  Partitionen können über das [Azure-Portal](search-create-service-portal.md) oder mithilfe von [PowerShell](search-manage-powershell.md) angepasst werden.

2. **Begrenzen von Feldern mit hoher Kardinalität:** Ein Feld mit hoher Kardinalität ist ein Feld, in dem Facets oder Filter verwendet werden können und das eine beträchtliche Anzahl von eindeutigen Werten besitzt. Daher werden bei der Berechnung von Ergebnissen erhebliche Ressourcen beansprucht. Wenn Sie z.B. ein Feld mit einer Produkt-ID oder einer Beschreibung zur Verwendung von Facets oder Filtern einrichten, ist die Kardinalität hoch, da die meisten Werte für jedes Dokument eindeutig sind. Verwenden Sie so wenig Felder mit Kardinalität wie irgend möglich.

3. **Wechseln zu einem höheren Search-Tarif:**  Der Wechsel in einen anderen Azure Search-Tarif ist eine weitere Möglichkeit, die Leistung langsamer Abfragen zu verbessern. Jeder höhere Tarif bietet schnellere CPUs und mehr Arbeitsspeicher, was sich jeweils positiv auf die Abfrageleistung auswirkt.

## <a name="scaling-for-availability"></a>Skalieren zur Erhöhung der Verfügbarkeit
Replikate können nicht nur zur Verringerung von Abfragelatenzen beitragen, sondern auch Hochverfügbarkeit ermöglichen. Mit einem einzigen Replikat müssen Sie mit regelmäßigen Ausfallzeiten aufgrund von Serverneustarts nach Softwareupdates oder anderen Wartungsereignissen rechnen.  Daher müssen Sie überlegen, ob Ihre Anwendung Hochverfügbarkeit sowohl für Suchvorgänge (Abfragen) als auch für Schreibvorgänge (Indizierungsereignisse) erfordert. Azure Search bietet SLA-Optionen in allen kostenpflichtigen Tarifen mit den folgenden Attributen:

* Zwei Replikate für Hochverfügbarkeit von schreibgeschützten Workloads (Abfragen)
* Drei oder mehr Replikate für Hochverfügbarkeit von Lese-/ Schreibworkloads (Abfragen und Indizierung)

Weitere Informationen hierzu finden Sie unter [SLA für Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Bei Replikaten handelt es sich, wie bereits gesagt, um Kopien Ihrer Daten. Wenn Sie also mehrere Replikate einrichten, kann Azure Search Computerneustarts und Wartungsaktivitäten für ein Replikat durchführen, während gleichzeitig Abfragen weiterhin in den anderen Replikaten ausgeführt werden können. Wenn Sie im Gegensatz dazu Replikate entfernen, tritt eine Verschlechterung der Abfrageleistung auf, vorausgesetzt, dass diese Replikate eine nicht ausgelastete Ressource waren.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Skalieren für geografisch verteilte Workloads und Georedundanz
Bei geografisch verteilten Workloads treten für Benutzer, deren Standort weit von dem Rechenzentrum entfernt ist, in dem Azure Search gehostet wird, höhere Latenzraten auf. Eine Gegenmaßnahme besteht darin, mehrere Suchdienste in Regionen bereitzustellen, die sich in größerer geografischer Nähe zu diesen Benutzern befinden. Azure Search stellt derzeit keine automatisierte Methode zur Georeplikation von Azure Search-Indizes über Regionen hinweg bereit. Es gibt allerdings einige Verfahren, mit denen sich ein solcher Prozess einfach implementieren und verwalten lässt. Diese werden in den nächsten Abschnitten beschrieben.

Das Ziel bei der Einrichtung geografisch verteilter Suchdienste ist es, über mindestens zwei Indizes in mindestens zwei Regionen zu verfügen, sodass Benutzer zu dem Azure Search-Dienst weitergeleitet werden können, der die geringste Latenz bietet. Das folgende Beispiel veranschaulicht das Konzept:

   ![Tabelle der Dienste nach Region][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Synchronisieren von Daten über mehrere Azure Search-Dienste hinweg
Es gibt zwei Optionen für die Synchronisierung Ihrer verteilten Suchdienste: den [Azure Search-Indexer](search-indexer-overview.md) und die Push-API (auch als [Azure Search-REST-API](https://docs.microsoft.com/rest/api/searchservice/) bezeichnet).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Verwenden von Indexern zur Aktualisierung von Inhalten für mehrere Dienste

Wenn Sie bereits einen Indexer für einen Dienst verwenden, können Sie einen zweiten Indexer für einen zweiten Dienst so konfigurieren, dass er dasselbe Datenquellenobjekt verwendet und somit Daten vom selben Standort abruft. Jeder Dienst in jeder Region hat einen eigenen Indexer und einen Zielindex (der Suchindex wird nicht gemeinsam verwendet, sodass Daten dupliziert werden), aber jeder Indexer verweist auf die gleiche Datenquelle.

Diese Architektur würde ganz allgemein in etwa wie folgt aussehen.

   ![Einzelne Datenquelle mit verteilten Indexer- und Dienstkombinationen][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Verwenden von REST-APIs zur Übertragung von Inhaltsupdates für mehrere Dienste
Wenn Sie die Azure Search-REST-API zum [Übertragen von Inhalten in Ihren Azure Search-Index](https://docs.microsoft.com/rest/api/searchservice/update-index) verwenden, können Sie die verschiedenen Suchdienste synchronisieren, indem Sie bei jedem Update per Push alle Änderungen an alle Suchdienste übertragen. Stellen Sie in Ihrem Code sicher, dass auch Fälle verarbeitet werden, in denen beim Update eines Suchdiensts ein Fehler auftritt, dies aber auch bei anderen Suchdiensten der Fall ist.

## <a name="leverage-azure-traffic-manager"></a>Nutzen von Azure Traffic Manager
[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) können Sie Anfragen an mehrere Websites an verschiedenen geografischen Standorten weiterleiten, an denen dann mehrere Azure Search-Dienste zum Einsatz kommen. Traffic Manager kann testen, ob Azure Search verfügbar ist, und Benutzer während eines Ausfalls an alternative Suchdienste weiterleiten – dies ist ein großer Vorteil. Wenn Sie Suchanfragen über Azure-Websites weiterleiten, ermöglicht Azure Traffic Manager zudem den Lastenausgleich in Fällen, in denen die Website erreichbar ist, aber nicht Azure Search. Hier finden Sie ein Beispiel für eine Architektur mit Traffic Manager.

   ![Tabelle der Dienste nach Region mit Traffic Manager im Zentrum][3]

## <a name="monitor-performance"></a>Überwachen der Leistung
Azure Search bietet die Möglichkeit, die Leistung Ihres Diensts über das [Durchsuchen der Datenverkehrsanalyse](search-traffic-analytics.md) zu analysieren und zu überwachen. Wenn Sie diese Funktionalität aktivieren und eine Instrumentierung zu Ihrer Client-App hinzufügen, können Sie optional die einzelnen Suchvorgänge sowie aggregierte Metriken in einem Azure Storage-Konto protokollieren, das dann zur Analyse verarbeitet oder in Power BI visualisiert werden kann. Metriken, die auf diese Weise erfasst werden, stellen Leistungsstatistiken wie beispielsweise die durchschnittliche Anzahl von Abfragen oder die Antwortzeiten für Abfragen bereit. Darüber hinaus können Sie mit der Vorgangsprotokollierung Details bestimmter Suchoperationen anzeigen.

Die Datenverkehrsanalyse ist nützlich,um die Latenzraten aus der Perspektive von Azure Search zu verstehen. Da die protokollierten Leistungsmetriken für Abfragen auf dem Zeitraum basieren, der für die vollständige Verarbeitung einer Abfrage in Azure Search erforderlich ist (von dem Zeitpunkt, zu dem eine Antwort angefordert wurde, bis zu dem Zeitpunkt, zu dem sie gesendet wird), können Sie anhand dieser Metriken ermitteln, ob Latenzprobleme seitens des Azure Search-Diensts oder außerhalb des Diensts auftreten (z.B. aufgrund einer Netzwerklatenz).  

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu den Tarifen und den Grenzwerte für jeden Tarif finden Sie unter [Grenzwerte für den Azure Search-Dienst](search-limits-quotas-capacity.md).

Weitere Informationen zu Partitions- und Replikatkombinationen finden Sie unter [Kapazitätsplanung](search-capacity-planning.md) .

Das folgende Video zeigt weitere Details zur Leistung und veranschaulicht, wie die in diesem Artikel beschriebenen Optimierungen implementiert werden können:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
