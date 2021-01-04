---
title: Gesamtkosten mit Azure Cosmos DB
description: In diesem Artikel werden die Gesamtkosten von Azure Cosmos DB mit lokalen Datenbanken und über IaaS gehosteten Datenbanken verglichen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: eb06d101170685bea5ff231612e9ffbf8df467da
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079290"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Gesamtkosten mit Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB ist mit differenzierter Mehrinstanzenfähigkeit und Ressourcenkontrolle konzipiert. Dank dieses Designs kann Azure Cosmos DB zu deutlich niedrigeren Kosten betrieben werden und hilft so dem Benutzer, Geld zu sparen. Azure Cosmos DB unterstützt derzeit mehr als 280 Kundenworkloads auf einem einzelnen Computer (wobei sich die Dichte ständig erhöht) und zigtausend Kundenworkloads innerhalb eines Clusters. Für Replikate von Kundenworkloads wird ein Lastenausgleich über verschiedene Computer in einem Cluster und über mehrere Cluster in einem Rechenzentrum hinweg vorgenommen. Weitere Informationen finden Sie unter [Azure Cosmos DB: Erweitern der Grenzen global verteilter Datenbanken](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). Aufgrund der Ressourcenkontrolle, der Mehrinstanzenfähigkeit und der nativen Integration in die übrige Azure-Infrastruktur ist Azure Cosmos DB im Durchschnitt um das 4- bis 6-Fache günstiger als MongoDB, Cassandra oder andere OSS-NoSQL-Datenbanken, die über IaaS ausgeführt werden, und bis um das 10-Fache günstiger als lokal ausgeführte Datenbank-Engines. Lesen Sie hierzu das Whitepaper [Total Cost of (Non) Ownership of a NoSQL Database Cloud Service](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf) (Gesamtkosten eines NoSQL-Datenbank-Clouddiensts).

OSS-NoSQL-Datenbanklösungen wie Apache Cassandra, MongoDB und HBase wurden für die lokale Nutzung entwickelt. Wenn sie als verwalteter Dienst angeboten werden, entsprechen sie einer Resource Manager-Vorlage mit einer Mandantendatenbank zum Verwalten der bereitgestellten Cluster und zum Überwachen des Supports. OSS-NoSQL-Architekturen erfordern erheblichen Betriebsaufwand und sehr viel Know-how, das möglicherweise nur mit hohem finanziellen Aufwand zu finden ist. Andererseits ist Azure Cosmos DB ein vollständig verwalteter Clouddienst, der Entwicklern die Möglichkeit bietet, sich auf geschäftliche Innovationen statt auf die Verwaltung und Wartung der Datenbankinfrastruktur zu konzentrieren.

Im Gegensatz zu dem cloudnativen Datenbankdienst Azure Cosmos DB wurden OSS-NoSQL-Datenbank-Engines nicht mit Ressourcenkontrolle oder differenzierter Mehrinstanzenfähigkeit als architektonischem Grundprinzip entworfen und erstellt. OSS-NoSQL-Datenbank-Engines wie Cassandra und MongoDB gehen von der Grundannahme aus, dass alle Ressourcen des virtuellen Computers, auf dem sie ausgeführt werden, für die Verwendung verfügbar sind. Viele dieser Datenbank-Engines können nicht funktionieren, wenn die Menge der Ressourcen unter einen bestimmten Schwellenwert sinkt. Zum Beispiel bei kleinen VM-Instanzen sind sie mit vom Hersteller empfohlenen Konfigurationen verfügbar, die in der Regel umfangreiche virtuelle Computer zu höheren Kosten nahelegen. Daher ist es nicht möglich, eine OSS-NoSQL- oder eine beliebige andere lokale Datenbank-Engine zu hosten und sie über ein nutzungsbasiertes Abrechnungsmodell wie „Anforderungen pro Sekunde (RPS)“ oder „Speicherverbrauch“ zur Verfügung zu stellen.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Gesamtkosten von Azure Cosmos DB

Durch das serverlose Bereitstellungsmodell von Azure Cosmos DB entfällt die Bereitstellung überdimensionierter Kapazitäten für die Datenbankinfrastruktur. Azure Cosmos DB-Ressourcen werden bereitgestellt, ohne dass spezielle Konfigurationen oder eine Lizenzierung erforderlich sind. Daher können beim Ausführen von Anwendungen mit Azure Cosmos DB-Unterstützung mehr als 70 % der Gesamtkosten gegenüber OSS-NoSQL-Datenbanken eingespart werden. Einige Echtzeitbeispiele finden Sie unter [Anwendungsfälle von Kunden](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Das Preismodell für Azure Cosmos DB bietet folgende weitere Vorteile:

* **Gutes Preis-/Leistungsverhältnis:** Marktanalysten, Kunden und Partner haben bestätigt, dass Azure Cosmos DB im Vergleich zu den Lösungen, die Kunden erhalten, wenn sie diese eigenständig oder über andere Anbieter implementieren, viele sehr wertvolle Funktionen zu einem viel günstigeren Preis bietet. Die Datenbankfunktionen wie globale Verteilung, Schreibvorgänge in mehreren Regionen, klar definierte und intuitive Konsistenzmodelle und die automatische Indizierung sind in Azure Cosmos DB in hohem Maße vereinfacht und verursachen weder Mehraufwand noch Komplexität oder Ausfallzeiten.

* **Keine NoSQL-DevOps-Verwaltung erforderlich:** Mit Azure Cosmos DB sind keine DevOps zum Verwalten von Bereitstellungen, Ausführen von Wartungsarbeiten, für Skalierung oder Patches erforderlich. Sie können alle Workloads ausführen, die Sie mit einem lokal oder über eine Cloudinfrastruktur gehosteten OSS-NoSQL-Cluster ausführen würden.

:::image type="content" source="./media/total-cost-ownership/tco.png" alt-text="Gesamtkosten von Azure Cosmos DB" border="false":::

* **Möglichkeit zur elastischen Skalierung:** Der Durchsatz von Azure Cosmos DB kann zentral hoch- und herunterskaliert werden, sodass Sie die Gesamtkosten außerhalb der Spitzenzeiten senken können. OSS-NoSQL-Cluster, die in einer Cloudinfrastruktur bereitgestellt werden, bieten nur begrenzte Elastizität, und lokale Bereitstellungen sind per se nicht elastisch. In Azure Cosmos DB ist eine lineare Skalierung des Durchsatzes garantiert, wenn Sie mehr Durchsatz bereitstellen. Diese Garantie (im 99. Perzentil bei beliebiger Skalierung) wird durch SLAs mit finanzieller Absicherung zugesichert.

* **Größenvorteile:** Ein verwalteter Dienst wie Azure Cosmos DB wird mit einer großen Anzahl von Knoten betrieben und ist nativ in Netzwerk, Speicher und Computeressourcen integriert. Aufgrund der umfangreichen Standardisierung von Azure Cosmos DB können Sie Kosten sparen.

* **Für die Cloud optimiert:** Azure Cosmos DB wurde von Grund auf mit differenzierter Mehrinstanzenfähigkeit und Leistungsisolation entwickelt. Das bietet die Möglichkeit, zigtausend Mandanten und deren Workloads über Cluster und Rechenzentren hinweg optimal zu platzieren und auszuführen sowie einen Lastenausgleich vorzunehmen. Im Gegensatz dazu wird die aktuelle Generation von OSS-NoSQL-Datenbanken lokal betrieben unter der Annahme, dass der gesamte virtuelle Computer die Workload eines einzelnen Mandanten ausführt. Diese Datenbanken sind auch nicht darauf ausgelegt, Infrastruktur und Hardware eines Cloudanbieters in vollem Umfang zu nutzen. Zum Beispiel kennt eine OSS-NoSQL-Datenbank-Engine die Unterschiede zwischen dem Ausfall eines virtuellen Computers und einem routinemäßigen Image-Upgrade nicht. Sie ist sich auch nicht der Tatsache bewusst, dass der Premium-Datenträger bereits über die Drei-Wege-Replikation repliziert wird. Sie kann diese Vorteile weder nutzen noch Vorteile und Einsparungen an die Kunden weitergeben.

* **Stundenweise Abrechnung:** Umfangreiche Workloads, die jederzeit skaliert werden müssen, werden nur nach Stunden abgerechnet. Die Workloads für eine Anwendung variieren in der Regel übers Jahr und durch die Daten, die abgefragt werden. Mit Azure Cosmos DB können Sie nach Bedarf hoch- oder herunterskalieren und bezahlen nur das, was Sie wirklich benötigen. Bei lokalen oder über IaaS gehostete Systeme können Sie dieses Modell nicht anwenden, da es keine Möglichkeit gibt, Ihre Hardware stundenweise außer Betrieb zu nehmen. In solchen Fällen können Sie mit Azure Cosmos DB im Schnitt eine 10- bis 14-Fache Einsparung erzielen.

* **Zahlreiche Features werden kostenlos bereitgestellt:** In Azure Cosmos DB sind Schreibworkloads im Vergleich zu alternativen Datenbankdiensten erheblich kostengünstiger. Darüber hinaus bietet Azure Cosmos DB unter anderem Funktionen wie [automatische Indizierung](index-policy.md), [Gültigkeitsdauer](time-to-live.md) und [Änderungsfeed](change-feed.md), die in der Regel von anderen Datenbankdiensten extra berechnet werden, kostenlos an.

* **Verwendung einer einheitlichen Währung für verschiedene Workloads:** Im Gegensatz zu anderen Angeboten müssen Sie bei Azure Cosmos DB Workloads nicht segmentieren (z. B. in Lese- und Schreibvorgänge) oder Durchsatz nach Workloadtyp (Lesedurchsatz und Schreibdurchsatz) bereitstellen. In Azure Cosmos DB wird der bereitgestellte Durchsatz mithilfe einer einheitlichen und normalisierten Währung in Form von Anforderungseinheiten oder RUs/Sek. reserviert. Azure Cosmos DB zwingt Sie nicht, Ihre Workloads zu priorisieren, eine Kapazitätsplanung vorzunehmen oder jeden Kapazitätstyp separat zu bezahlen. Ein derartiger Ansatz ermöglicht Ihnen, dieselben RUs/Sek. zwischen verschiedenen Operationen und Workloadtypen auszutauschen.

* **Zum Skalieren müssen keine virtuellen Computer bereitgestellt werden:** Die meisten operativen Datenbanken setzen zum Skalieren große virtuelle Computer voraus, um das „Noisy-Neighbor“-Problem zu vermeiden und eine freie Ressourcenkontrolle zu ermöglichen. Die Vorgaben und die Kosten für die Vorleistung gehen zu Lasten des Kunden. Mit Azure Cosmos DB können Sie klein anfangen und nahtlos bis zur Größe von umfangreichen Workloads wachsen – ohne jede Ausfallzeit und ohne Auswirkungen auf die Datenverfügbarkeit.

* **Maximale Nutzung des bereitgestellten Durchsatzes:** Dank der Subcore-Multiplexing-Technologie in Azure Cosmos DB können Sie den bereitgestellten Durchsatz in höherem Umfang auslasten als über IaaS gehostete Optionen oder Angebote von Drittanbietern. Diese Methode sorgt für erheblich höhere Einsparungen als die alternativen Lösungen.

* **Weitreichende Integration von Azure Cosmos DB in andere Azure-Dienste:** Azure Cosmos DB verfügt über eine native Integration mit Netzwerk, Compute, Azure Functions (serverlos), Azure IoT und anderen Azure-Diensten. Dank dieser Integration erhalten Sie die beste Leistung und Geschwindigkeit der Datenreplikation über die ganze Welt mit zuverlässigen Garantien. Drittanbieter können mit ihren Lösungen dem nicht entsprechen oder würden für das Angebot derartiger Funktionen in der Regel einen Aufpreis berechnen.

* **Automatische Hochverfügbarkeit mit standardmäßig mindestens 10 bis 20 Fehlerdomänen:** Azure Cosmos DB unterstützt die Verteilung von Workloads über Fehlerdomänen hinweg, ein Feature, das für die Hochverfügbarkeit von entscheidender Bedeutung ist. Azure Cosmos DB bietet 99,999 % Hochverfügbarkeit für Lese- und Schreibvorgänge im 99. Perzentil überall auf der Welt. Wenn Sie derartige Features eigenständig oder über eine Drittanbieterlösung implementieren würden, entstünden sehr hohe Kosten.

* **Sie erhalten automatisch alle Enterprise-Funktionen, ohne zusätzliche Kosten :** Azure Cosmos DB stellt im Vergleich zu unseren Mitbewerbern eine äußerst umfangreiche Lösung mit Compliance-Zertifizierungen, Sicherheit und Verschlüsselung für Daten im Ruhezustand und während der Übertragung ohne zusätzliche Kosten bereit. Sie erhalten automatisch die regionale Verfügbarkeit an jedem beliebigen Ort auf der Welt. Ihre Datenbank kann sich über eine beliebige Anzahl von Azure-Regionen erstrecken, und Sie können an jedem Punkt Regionen hinzufügen oder entfernen.

* **Mit reservierter Kapazität können Sie bis zu 65 % der Kosten sparen:** Die [reservierte Kapazität](cosmos-db-reserved-capacity.md) von Azure Cosmos DB hilft Ihnen, Geld zu sparen, indem Sie die Ressourcen von Azure Cosmos DB entweder für ein Jahr oder drei Jahre im Voraus bezahlen. Mit der Vorabfestlegung und Bezahlung für ein Jahr oder drei Jahre können Sie Ihre Kosten deutlich senken und gegenüber der regulären Preisgestaltung zwischen 20 % und 65 % sparen. Für Ihre unternehmenskritischen Workloads können Sie bessere SLAs in Bezug auf die Kapazitätsbereitstellung erhalten.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen unter [Warum ist das Preismodell von Azure Cosmos DB kosteneffizient für den Kunden?](total-cost-ownership.md)
* Weitere Informationen unter [Optimale Entwicklungs- und Testmöglichkeiten mit Azure Cosmos DB](optimize-dev-test.md)
* Weitere Informationen unter [Optimieren der Durchsatzkosten](optimize-cost-throughput.md)
* Weitere Informationen unter [Optimieren der Speicherkosten](optimize-cost-storage.md)
* Weitere Informationen unter [Optimieren der Kosten für Lese- und Schreibvorgänge](optimize-cost-reads-writes.md)
* Weitere Informationen unter [Optimieren der Kosten von Abfragen](./optimize-cost-reads-writes.md)
* Weitere Informationen unter [Optimieren der Kosten bei Cosmos-Konten mit mehreren Regionen](optimize-cost-regions.md)
* Weitere Informationen im Whitepaper [Total Cost of (Non) Ownership of a NoSQL Database Cloud Service](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf) (Gesamtkosten eines NoSQL-Datenbank-Clouddiensts).
