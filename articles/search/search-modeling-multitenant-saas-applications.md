---
title: Modellieren der Mehrinstanzenfähigkeit für die Inhaltsisolation in einem Dienst – Azure Search
description: Erfahren Sie mehr über gängige Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen bei Verwendung von Azure Search.
manager: jlembicz
author: LiamCavanagh
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 58d7ca65a14f9f774b19796c9beae2a7c84102ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61288701"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen und Azure Search
Eine mehrinstanzenfähige Anwendung bietet dieselben Dienste und Funktionen einer beliebigen Anzahl von Mandanten, die die Daten der anderen Mandaten nicht anzeigen oder mit diesen gemeinsam nutzen können. In diesem Dokument werden Strategien zur Isolierung von Mandanten für mehrinstanzenfähige Anwendungen erläutert, die mit Azure Search erstellt werden.

## <a name="azure-search-concepts"></a>Azure Search-Konzepte
Azure Search ist eine Search-as-a-Service-Lösung, die Entwicklern ermöglicht, Anwendungen umfassende Suchfunktionen hinzuzufügen, ohne Infrastruktur verwalten oder Experte für das Abrufen von Informationen werden zu müssen. Daten werden in den Dienst hochgeladen und dann in der Cloud gespeichert. Mithilfe einfacher an die Azure Search-API gerichteter Anforderungen können die Daten geändert und durchsucht werden. Eine Übersicht über den Dienst finden Sie im [in diesem Artikel](https://aka.ms/whatisazsearch). Ehe wir näher auf Entwurfsmuster eingehen, ist es wichtig, einige Konzepte von Azure Search zu verstehen.

### <a name="search-services-indexes-fields-and-documents"></a>Suchdienste, Indizes, Felder und Dokumente
Zur Verwendung von Azure Search muss ein *Suchdienst*abonniert werden. Die in Azure Search hochgeladenen Daten werden in einem *Index* innerhalb des Suchdiensts gespeichert. In einem einzelnen Dienst kann es mehrere Indizes geben. Analog zu den vertrauten Konzepten von Datenbanken kann der Suchdienst mit einer Datenbank verglichen werden, während die Indizes im Dienst mit Tabellen in einer Datenbank vergleichbar sind.

Jeder Index in einen Suchdienst hat ein eigenes Schema, das mithilfe verschiedener anpassbarer *Felder*definiert wird. Daten werden einem Azure Search-Index in Form einzelner *Dokumente*hinzugefügt. Jedes Dokument muss in einen bestimmten Index hochgeladen werden und dem Schema dieses Indexes entsprechen. Beim Durchsuchen von Daten mithilfe von Azure Search werden die Volltextsuchabfragen auf einen bestimmten Index angewendet.  Wiederum analog zu Datenbanken können Felder mit den Spalten einer Tabelle und Dokumente mit Zeilen verglichen werden.

### <a name="scalability"></a>Skalierbarkeit
Ein Azure Search-Dienst im [Tarif „Standard“](https://azure.microsoft.com/pricing/details/search/) kann in zwei Bereichen skaliert werden: Speicherung und Verfügbarkeit.

* *Partitionen* können hinzugefügt werden, um die Speicherkapazität eines Suchdienst zu erhöhen.
* *Replikate* können einem Dienst hinzugefügt werden, um den Durchsatz von Anforderungen zu erhöhen, die ein Suchdienst verarbeiten kann.

Das Hinzufügen und Entfernen von Partitionen und Replikaten nach Belieben ermöglicht, dass die Kapazität des Suchdiensts mit der Datenmenge und dem Datenverkehr entsprechend den Anforderungen der Anwendung wachsen kann. Damit der Suchdienst eine [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)für Lesevorgänge erfüllen kann, sind zwei Replikate erforderlich. Damit der Dienst eine [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)für Lese- und Schreibvorgänge erfüllen kann, sind drei Replikate erforderlich.

### <a name="service-and-index-limits-in-azure-search"></a>Dienst- und Indexgrenzwerte für Azure Search
Azure Search bietet verschiedene [Tarife](https://azure.microsoft.com/pricing/details/search/) mit unterschiedlichen [Grenzwerten und Kontingenten](search-limits-quotas-capacity.md). Diese Grenzwerte gelten auf Dienst-, Index- und Partitionsebene.

|  | Basic | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Maximale Anzahl von Replikaten pro Dienst |3 |12 |12 |12 |12 |
| Maximale Anzahl von Partitionen pro Dienst |1 |12 |12 |12 |3 |
| Maximale Anzahl von Sucheinheiten (Replikate x Partitionen) pro Dienst |3 |36 |36 |36 |36 (max. 3 Partitionen) |
| Maximale Speicherkapazität pro Dienst |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maximale Speicherkapazität pro Partition |2 GB |25 GB |100 GB |200 GB |200 GB |
| Maximale Anzahl von Indizes pro Dienst |5 |50 |200 |200 |3\.000 (max. 1.000 Indizes/Partition) |

#### <a name="s3-high-density"></a>S3 High Density
Der Azure Search-Tarif „S3“ bietet als Option einen HD-Modus (High Density), der speziell auf Szenarien mit mehreren Mandanten ausgelegt ist. In vielen Fällen ist es notwendig, eine große Zahl von kleineren Mandanten unter einem einzelnen Dienst zu unterstützen, um die Vorteile der Einfachheit und Kosteneffizienz nutzen zu können.

Mit S3 HD können die vielen kleinen Indizes unter der Verwaltung eines einzelnen Suchdiensts gepackt werden, indem die Möglichkeit zum horizontalen Hochskalieren von Indizes mithilfe von Partitionen gegen die Möglichkeit eingetauscht wird, eine größere Anzahl von Indizes in einem einzelnen Dienst zu hosten.

Konkret kann ein S3-Dienst zwischen 1 und 200 Indizes aufweisen, die zusammen bis zu 1,4 Milliarden Dokumente hosten können. Ein S3 HD-Dienst ermöglicht dagegen einzelne Indizes mit bis zu 1 Million Dokumente, kann jedoch bis zu 1.000 Indizes pro Partition (bis zu 3.000 pro Dienst) mit einer Gesamtzahl von 200 Millionen Dokumenten pro Partition (bis zu 600 Millionen pro Dienst) verarbeiten.

## <a name="considerations-for-multitenant-applications"></a>Aspekte bei mehrinstanzenfähigen Anwendungen
Mehrinstanzenfähige Anwendungen müssen Ressourcen gleichmäßig den Mandanten zuteilen und für ein Maß an Datenschutz zwischen diesen sorgen. Beim Entwerfen der Architektur einer solchen Anwendung sind verschiedene Aspekte zu beachten:

* *Mandantenisolierung:* Anwendungsentwickler müssen geeignete Maßnahmen ergreifen, um sicherzustellen, dass Mandanten keinen nicht autorisierten oder unerwünschten Zugriff auf die Daten anderer Mandanten haben. Abgesehen vom Datenschutz erfordern Strategien für die Isolierung von Mandanten eine effektive Verwaltung gemeinsam genutzter Ressourcen und Schutz vor „lauten“, in diesem Fall überaus aktiven, Nachbarn.
* *Cloudressourcenkosten:* Wie andere Anwendungen auch müssen Softwarelösungen als Teil einer mehrinstanzenfähigen Anwendung wettbewerbsfähig bleiben.
* *Einfacher Betrieb:* Bei der Entwicklung einer mehrinstanzenfähigen Architektur sind Betrieb und Komplexität der Anwendung ein wichtiger Aspekt. Azure Search bietet eine [SLA für eine Verfügbarkeit von 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Globale Verteilung:* Mehrinstanzenfähige Anwendungen müssen gegebenenfalls Anforderungen von Mandanten erfüllen, die weltweit verteilt sind.
* *Skalierbarkeit:* Anwendungsentwickler müssen berücksichtigen, wie ein Kompromiss erreicht wird zwischen der Beibehaltung eines ausreichend niedrigen Grads an Anwendungskomplexität und dem Entwurf der Anwendung für eine Skalierung entsprechend der Anzahl der Mandanten sowie deren Daten und Workloads.

Azure Search bietet verschiedene Abgrenzungsmöglichkeiten, mit deren Hilfe die Daten und Workloads von Mandanten isoliert werden können.

## <a name="modeling-multitenancy-with-azure-search"></a>Modellieren der Mehrinstanzenfähigkeit mit Azure Search
Bei einem mehrinstanzenfähigen Szenario nutzt der Anwendungsentwickler einen oder mehrere Suchdienste und verteilt die Mandanten auf Dienste, Indizes oder beides. Azure Suche unterstützt beim Modellieren eines mehrinstanzenfähigen Szenarios mehrere Muster:

1. *Index pro Mandant:* Jeder Mandant verfügt über einen eigenen Index in einem Suchdienst, der mit anderen Mandanten gemeinsam genutzt wird.
2. *Dienst pro Mandant:* Jeder Mandant hat einen eigenen dedizierten Azure Search-Dienst, was ein Höchstmaß an Trennung von Daten und Workloads ermöglicht.
3. *Kombination beider Muster:* Größeren, aktiveren Mandanten werden dedizierte Dienste zugewiesen, während kleineren Mandanten einzelne Indizes in gemeinsam genutzten Diensten zugeteilt werden.

## <a name="1-index-per-tenant"></a>1. Index pro Mandant
![Eine Abbildung des Index-pro-Mandant-Modells](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Bei einem Index-pro-Mandant-Modell nutzen mehrere Mandanten einen einzelnen Azure Search-Dienst, wobei jeder Mandant einen eigenen Index hat.

Die Daten der Mandanten sind voneinander isoliert, da alle Suchanfragen und Dokumentvorgänge in Azure Search auf Indexebene erfolgen. Auf Anwendungsebene muss der Datenverkehr der verschiedenen Mandanten zu den ordnungsgemäßen Indizes geleitet werden, während auf Dienstebene Ressourcen für alle Mandanten verwaltet werden müssen.

Ein wesentliches Merkmal des Index-pro-Mandant-Modells ist die Fähigkeit des Anwendungsentwicklers, die abonnierte Kapazität eines Suchdienst für die Mandanten der Anwendung zu überschreiten. Wenn bei den Mandanten die Workload ungleichmäßig verteilt ist, kann die optimale Kombination von Mandanten auf die Indizes eines Suchdiensts verteilt werden. Der Zweck ist es, eine Anzahl sehr aktiver Mandanten mit intensiver Ressourcennutzung und zugleich eine große Menge weniger aktiver Mandanten zu unterstützen. Der Nachteil ist die Unfähigkeit des Modells, mit Situationen klar zu kommen, bei denen alle Mandanten gleichzeitig sehr aktiv sind.

Der Index-pro-Mandant-Modell bildet die Grundlage eines variablen Kostenmodells, bei dem ein gesamter Azure Search-Dienst vorab gebucht und anschließend mit Mandanten aufgefüllt wird. Dies ermöglicht, dass freie Kapazität für Tests und kostenlose Konten zur Verfügung gestellt wird.

Für Anwendungen mit globaler Verteilung ist das Index-pro-Mandant-Modell u.U. nicht das effizienteste. Wenn die Mandanten einer Anwendung auf der ganzen Welt verteilt sind, ist ggf. ein separater Dienst für jede Region erforderlich, was möglicherweise zur Verdopplung der jeweiligen Kosten führen kann.

Azure Search ermöglicht die Skalierung von sowohl einzelnen Indizes als auch der Gesamtanzahl von Indizes. Bei Wahl eines entsprechenden Tarifs können Partitionen und Replikate dem gesamte Suchdienst hinzugefügt werden, wenn ein einzelner Index im Dienst in Bezug auf Speicherung oder Datenverkehr zu groß geworden ist.

Wenn die Gesamtanzahl der Indizes für einen einzelnen Dienst zu groß wird, muss ein anderer Dienst bereitgestellt werden, um die neuen Mandanten zu unterstützen. Wenn Indizes zwischen Suchdiensten verschoben werden müssen, weil neue Dienste hinzugefügt werden, müssen die Daten aus dem Index manuell in einen anderen Index kopiert werden, da Azure Search das Verschieben eines Indexes nicht zulässt.

## <a name="2-service-per-tenant"></a>2. Dienst pro Mandant
![Eine Abbildung des Dienst-pro-Mandant-Modells](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

Bei einer Dienst-pro-Mandant-Architektur hat jeder Mandant einen eigenen Suchdienst.

Bei diesem Modell erzielt die Anwendung den Maximalgrad an Isolierung für die Mandanten. Jeder Dienst erhält dedizierten Speicher und Durchsatz für die Bewältigung von Suchanfragen und getrennte API-Schlüssel.

Für Anwendungen, bei denen Mandanten weltweit verteilt sind oder sich die Workload von Mandant zu Mandant nur geringfügig unterscheidet, ist das Dienst-pro-Mandant-Modell eine geeignete Wahl, da Ressourcen zur Bewältigung der Workloads der verschiedenen Mandanten nicht gemeinsam genutzt werden.

Ein Dienst-pro-Mandant-Modell bietet außerdem den Vorteil eines vorhersagbaren, festen Kostenmodells. Es sind keine Vorabinvestitionen in einen gesamten Suchdienst erforderlich, der mit Mandanten gefüllt werden muss. Allerdings sind die Kosten pro Mandant höher als beim Index-pro-Mandant-Modell.

Das Dienst-pro-Mandant-Modell eignet sich besonders für Anwendungen mit global verteilten Nutzern. Bei geografisch verteilten Mandanten ist es einfach, jedem Mandanten in der entsprechenden Region einen Dienst zur Verfügung zu stellen.

Die Herausforderung bei der Skalierung dieses Musters entstehen, wenn einzelne Mandanten die Kapazitätsgrenzen ihres Diensts erreichen. Azure Search unterstützt derzeit keine Aktualisierung des Tarifs eines Suchdiensts, weshalb alle Daten manuell in einen neuen Dienst kopiert werden müssen.

## <a name="3-mixing-both-models"></a>3. Kombinieren beider Modelle
Ein weiteres Muster für die Modellierung von Mehrinstanzenfähigkeit ist das Kombinieren des Index-pro-Mandant- und Dienst-pro-Mandant-Modells.

Durch Kombinieren der beiden Modelle können die größten Mandanten einer Anwendung dedizierte Dienste in Anspruch nehmen, während dem großen Rest weniger aktiver, kleinerer Mandanten die Indizes in einem gemeinsam genutzten Dienst zur Verfügung stehen. Dieses Modell stellt sicher, dass der Dienst den größten Mandanten stets eine hohe Leistung bietet, während zugleich die kleineren Mandaten vor einer Beeinträchtigung durch diese meist sehr aktiven Mandaten geschützt werden.

Allerdings erfordert das Umsetzen dieser Strategie Weitsicht bei der Vorhersage, welche Mandanten einen dedizierten und welche einen Index in einem gemeinsamen genutzt Dienst benötigen. Die Komplexität der Anwendung erhöht sich, da zwei mehrinstanzenfähige Modelle verwaltet werden müssen.

## <a name="achieving-even-finer-granularity"></a>Erreichen einer feineren Granularität
Die oben genannten Entwurfsmuster zum Modellieren mehrinstanzenfähiger Szenarien in Azure Search setzen einen einheitlichen Umfang voraus, bei dem jeder Mandant eine ganze Instanz einer Anwendung nutzt. Anwendungen müssen jedoch mitunter mit vielen kleineren Umfängen zurechtkommen.

Wenn das Dienst-pro-Mandant- und Index-pro-Mandant-Modell nicht ausreichend kleine Umfänge darstellen, ist es möglich, einen Index zu modellieren, mit dem ein noch feinerer Grad an Granularität erreicht wird.

Damit sich ein einzelner Index für verschiedene Clientendpunkte anders verhält, kann einem Index ein Feld hinzugefügt werden, dass jedem möglichen Client einen bestimmten Wert zuweist. Immer wenn ein Client Azure Search zum Abfragen oder Ändern eines Indexes aufruft, gibt der Code aus der Clientanwendung den entsprechenden Wert für dieses Feld mithilfe der [Filterfunktion](https://msdn.microsoft.com/library/azure/dn798921.aspx) von Azure Search zum Zeitpunkt der Abfrage an.

Mithilfe dieser Methode lassen sich eine Unterstützung getrennter Benutzerkonten und Berechtigungsstufen sowie sogar vollständig getrennter Anwendungen erreichen.

> [!NOTE]
> Der oben beschriebene Ansatz der Konfiguration eines einzelnen Indizes für mehrere Mandanten hat Auswirkungen auf die Relevanz der Suchergebnisse. Die Relevanzbewertungen der Suche werden auf Indexebene berechnet, nicht auf Mandantenebene. Dadurch werden Daten für alle Mandanten in die den Relevanzbewertungen zugrunde liegenden Statistiken einbezogen, z.B. die Ausdruckshäufigkeit.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
Azure Search ist eine hervorragende Wahl für viele Anwendungen. [Erfahren Sie mehr über die leistungsfähigen Funktionen des Diensts.](https://aka.ms/whatisazsearch) Berücksichtigen Sie beim Bewerten der verschiedenen Entwurfsmuster für mehrinstanzenfähige Anwendungen die [verschiedenen Tarife](https://azure.microsoft.com/pricing/details/search/) und zugehörigen [Dienstgrenzen](search-limits-quotas-capacity.md), um Azure Search perfekt an Anwendungsworkloads und Architekturen sämtlicher Größen anzupassen.

Alle Fragen zu Azure Search und Szenarien mit mehreren Mandanten können an azuresearch_contact@microsoft.com gesendet werden.

