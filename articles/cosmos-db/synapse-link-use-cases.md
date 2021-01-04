---
title: Analytische Echtzeitanwendungsfälle für Azure Synapse Link für Azure Cosmos DB
description: Erfahren Sie, wie Azure Synapse Link für Azure Cosmos DB in Lieferkettenanalysen, Prognosen, Berichten, Echtzeitpersonalisierungen und IOT-Predictive Maintenance verwendet wird.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ramkris
ms.openlocfilehash: 9855a53ebdf7501907b3e1e63d59823d3a0b0209
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463123"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-near-real-time-analytics-use-cases"></a>Azure Synapse Link für Azure Cosmos DB: Anwendungsfälle für Analysen in Quasi-Echtzeit
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

[Azure Synapse Link](synapse-link.md) für Azure Cosmos DB ist eine cloudnative hybride Verarbeitungsfunktion für Transaktionen und Analysen (Hybrid Transactional and Analytical Processing, HTAP), mit der Sie in Azure Cosmos DB in Quasi-Echtzeit Analysen für operative Daten ausführen können. Synapse Link sorgt für eine nahtlose Integration zwischen Azure Cosmos DB und Azure Synapse Analytics.

Sie möchten sicher wissen, in welchen Branchenanwendungsfällen diese cloudnative HTAP-Funktion genutzt werden kann, um Analysen in Quasi-Echtzeit für operative Datenanalysen durchzuführen. Im Folgenden sind drei häufige Anwendungsfälle für Azure Synapse Link für Azure Cosmos DB aufgeführt:

* Lieferkettenanalysen, Vorhersagen und Berichte
* Echtzeitpersonalisierung
* Predictive Maintenance und Anomalieerkennung in IOT-Szenarien

> [!NOTE]
> Azure Synapse Link für Azure Cosmos DB zielt auf ein Szenario ab, in dem Unternehmensteams Analysen in Quasi-Echtzeit ausführen möchten. Diese Analysen werden ohne ETL für operative Daten ausgeführt, die über transaktionale, auf Azure Cosmos DB basierende Anwendungen generiert werden. Dies bedeutet jedoch nicht, dass bei herkömmlichen Data Warehouse-Anforderungen wie Workloadverwaltung, hoher Parallelität und Persistenzaggregaten über mehrere Datenquellen hinweg kein separates Data Warehouse mehr erforderlich ist.

## <a name="supply-chain-analytics-forecasting--reporting"></a>Lieferkettenanalysen, Vorhersagen und Berichte

Untersuchungen zeigen, dass eine Einbettung von Big Data-Analysen in Lieferkettenvorgängen zu Verbesserungen führt, was die Lieferzeiten und die Lieferketteneffizienz anbelangt.

Hersteller führen ein Onboarding auf cloudnative Technologien durch, um die Einschränkungen der Legacysysteme im Bereich Enterprise Resource Planning (ERP) und Supply Chain Management (SCM) zu überwinden. Da Lieferketten minütlich zunehmende Mengen an operativen Daten (Bestellungs-, Versand-, Transaktionsdaten) erzeugen, benötigen Hersteller eine operative Datenbank. Diese operative Datenbank wird skaliert, um die Datenvolumen und eine analytische Plattform zu verarbeiten und ein Maß an kontextbezogener Intelligence in Echtzeit zu erhalten, mit dem Sie eine führende Position einnehmen.

Die folgende Architektur zeigt die Leistungsfähigkeit von Azure Cosmos DB als cloudnative Betriebsdatenbank und Synapse Link in der Lieferkettenanalyse:

:::image type="content" source="./media/synapse-link-use-cases/supply-chain-analytics.png" alt-text="Azure Synapse Link für Azure Cosmos DB in der Lieferkettenanalyse" border="false":::

Basierend auf der vorherigen Architektur sind die folgenden Anwendungsfälle mit Synapse Link für Azure Cosmos DB möglich:

* **Vorbereiten und Trainieren von Vorhersagepipelines:** Generieren Sie mithilfe von Machine Learning-Entsprechungen Erkenntnisse zu den operativen Daten der Lieferkette. Auf diese Weise können Sie die Bestands- und Betriebskosten senken und die Bestellzeiten für Kunden verkürzen.

  Synapse Link ermöglicht es Ihnen, die sich ändernden operativen Daten in Azure Cosmos DB ohne manuelle ETL-Prozesse zu analysieren. Dies spart Ihnen zusätzliche Kosten und verringert die Wartezeit und die betriebliche Komplexität. Synapse Link ermöglicht Data Engineers und Data Scientists das Erstellen stabiler Vorhersagepipelines:

  * Fragen Sie operative Daten aus dem Azure Cosmos DB-Analysespeicher ab, indem Sie die native Integration in Apache Spark-Pools in Azure Synapse Analytics nutzen. Sie können die Daten in einem interaktiven Notebook oder geplanten Remoteaufträge ohne komplexe Datentechnik abfragen.

  * Erstellen Sie Machine Learning-Modelle (ML) mit Spark ML-Algorithmen und Azure ML-Integration in Azure Synapse Analytics.

  * Schreiben Sie die Ergebnisse nach dem Modellrückschluss wieder in Azure Cosmos DB, um eine operative Bewertung in Quasi-Echtzeit vorzunehmen.

* **Operative Berichterstellung:** Lieferkettenteams benötigen flexible und benutzerdefinierte Berichte zu präzisen operativen Echtzeitdaten. Diese Berichte sind erforderlich, um eine Momentaufnahmesicht über die Effektivität, Rentabilität und Produktivität der Lieferkette zu erhalten. Dadurch können Datenanalysten und andere wichtige Beteiligte das Geschäft ständig neu evaluieren und Bereiche identifizieren, in denen die Betriebskosten gesenkt werden können. 

  Synapse Link für Azure Cosmos DB ermöglicht umfassende Szenarien für Business Intelligence (BI) und die Berichterstellung:

  * Fragen Sie operative Daten aus dem Azure Cosmos DB-Analysespeicher mithilfe der nativen Integration mit serverlosen SQL-Pools und der vollständigen Ausdrucksfähigkeit von T-SQL ab.

  * Modellieren und veröffentlichen Sie automatisch aktualisierte BI-Dashboards über Azure Cosmos DB mit Unterstützung für serverlose SQL-Pools für vertraute BI-Tools, wie beispielsweise Azure Analysis Services, Power BI Premium usw.

Im Folgenden finden Sie eine Anleitung für die Datenintegration für Batch- und Streamingdaten in Azure Cosmos DB:

* **Batchdatenintegration und Orchestrierung:** Da Lieferketten immer komplexer werden, müssen die Datenplattformen der Lieferketten in verschiedene Datenquellen und -formate integriert werden können. In Azure Synapse sind das gleiche Datenintegrationsmodul und die gleichen Oberflächen wie in Azure Data Factory integriert. Dank dieser Integration können Data Engineers umfassende Datenpipelines erstellen, ohne dass ein separates Orchestrierungsmodul erforderlich ist:

  * Verschieben Sie Daten aus über 85 unterstützten Datenquellen in [Azure Cosmos DB mit Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

  * Schreiben Sie codefreie ETL-Pipelines in Azure Cosmos DB einschließlich [Relations-Hierarchie- und Hierarchie-Hierarchie-Zuordnungen mit Zuordnungsdatenflüssen](../data-factory/how-to-sqldb-to-cosmosdb.md).

* **Integration und Verarbeitung von Streamingdaten:** Mit der Zunahme des industriellen IoT (die Nachverfolgung von Waren über die gesamten Lieferkette hinweg mithilfe von Sensoren, vernetzte Logistikflotten usw.) gibt es immer mehr Echtzeitdaten, die in Datenströmen generiert werden, die in herkömmliche, sich langsam bewegende Daten integriert werden müssen, um Erkenntnisse zu generieren. Azure Stream Analytics ist ein empfohlener Dienst für das Streaming von ETL und die Verarbeitung in Azure mit einer [Vielzahl von Szenarien](../stream-analytics/streaming-technologies.md). Azure Stream Analytics unterstützt [Azure Cosmos DB als native Datensenke](../stream-analytics/stream-analytics-documentdb-output.md).

## <a name="real-time-personalization"></a>Echtzeitpersonalisierung

Einzelhändler müssen sichere und skalierbare E-Commerce-Lösungen erstellen, die die Anforderungen von Kunden und Unternehmen erfüllen. Diese E-Commerce-Lösungen müssen eine Kundenbindung durch angepasste Produkte und Angebote herstellen, Transaktionen schnell und sicher verarbeiten und sich auf Fulfillment und Kundendienst konzentrieren. Azure Cosmos DB und Synapse Link für Azure Cosmos DB ermöglichen es Einzelhändlern, personalisierte Empfehlungen für Kunden in Echtzeit zu generieren. Sie verwenden anpassbare Konsistenzeinstellungen mit geringer Wartezeit, um unmittelbare Erkenntnisse zu erhalten, wie sie in der folgenden Architektur dargestellt sind:

:::image type="content" source="./media/synapse-link-use-cases/real-time-personalization.png" alt-text="Azure Synapse Link für Azure Cosmos DB in Echtzeitpersonalisierung" border="false":::

Anwendungsfälle für Synapse Link für Azure Cosmos DB:

* **Vorbereiten und Trainieren von Vorhersagepipelines:** Mithilfe von Synapse Spark und Machine Learning-Modellen können Sie Erkenntnisse zu den operativen Daten Ihrer Geschäftseinheiten oder Kundensegmente generieren. Dies entspricht der personalisierten Bereitstellung für die Zielkundensegmente, prädiktiven Endbenutzererfahrungen und einem gezielten Marketing, damit Sie die Anforderungen der Endbenutzer erfüllen können.

## <a name="iot-predictive-maintenance"></a>IOT-Predictive Maintenance

Industrielle IOT-Innovationen haben die Ausfallzeiten von Geräten enorm verringert und die Effizienz in allen Branchen gesteigert. Eine dieser Innovationen sind Predictive Maintenance-Analysen von Geräten am Edge der Cloud.

Im Folgenden sehen Sie eine Architektur, die die cloudnativen HTAP-Funktionen von Azure Synapse Link für Azure Cosmos DB in IOT-Predictive Maintenance nutzt:

:::image type="content" source="./media/synapse-link-use-cases/iot-predictive-maintenance.png" alt-text="Azure Synapse Link für Azure Cosmos DB in IoT-Predictive Maintenance" border="false" :::

Anwendungsfälle für Synapse Link für Azure Cosmos DB:

* **Vorbereiten und Trainieren von Vorhersagepipelines:** Die operativen Verlaufsdaten von IoT-Gerätesensoren können zum Trainieren von Vorhersagemodellen wie Anomaliedetektoren verwendet werden. Diese Anomaliedetektoren werden dann für die Echtzeitüberwachung wieder am Edge bereitgestellt. Eine solche Schleife ermöglicht es, Vorhersagemodelle durchgehend neu zu trainieren.

* **Operative Berichterstellung:** Mit der Zunahme der Initiativen für digitale Zwillinge erfassen Unternehmen große Mengen an operativen Daten von einer großen Anzahl von Sensoren, um eine digitale Kopie jedes Computers zu erstellen. Diese Daten unterstützen die BI-Anforderungen, Trends über Verlaufsdaten zusätzlich zu Echtzeitanwendungen über aktuelle heiße Daten zu verstehen.

## <a name="sample-scenario-htap-for-azure-cosmos-db"></a>Beispielszenario: HTAP für Azure Cosmos DB

Seit fast zehn Jahren wird Azure Cosmos DB von Tausenden von Kunden für unternehmenskritische Anwendungen genutzt, die eine elastische Skalierung, eine schlüsselfertige globale Verteilung sowie eine Replikation von Schreibvorgängen in mehreren Regionen für geringe Wartezeiten und hohe Verfügbarkeit für Lese- und Schreibvorgänge in ihren Transaktionsworkloads benötigen.
 
In der folgenden Liste finden Sie eine Übersicht über die verschiedenen Workloadmuster, die mit Betriebsdaten mithilfe von Azure Cosmos DB unterstützt werden:

* Echtzeit-Apps und -Services
* Ereignisstromverarbeitung
* BI-Dashboards
* Big Data-Analyse
* Machine Learning

Azure Synapse Link ermöglicht Azure Cosmos DB nicht nur die Unterstützung von Transaktionsworkloads, sondern auch das Durchführen von Analyseworkloads in Quasi-Echtzeit für operative Verlaufsdaten. Dies geschieht ohne ETL-Anforderungen und mit einer garantierten Leistungsisolation von den Transaktionsworkloads.

Die folgende Abbildung zeigt Workloadmuster mithilfe von Azure Cosmos DB: :::image type="content" source="./media/synapse-link-use-cases/synapse-link-workload-patterns.png" alt-text="Workloadmuster für Azure Synapse Link für Azure Cosmos DB" border="false":::

Die Vorteile von Azure Cosmos DB als einzelne Echtzeitdatenbank, die sowohl transaktionale als auch analytische Anforderungen einer Bestandsverwaltungsplattform unterstützt, soll am Beispiel des E-Commerce-Unternehmens CompanyXYZ verdeutlicht werden, das in mehr als 20 Ländern/Regionen tätig ist.

* Die Arbeit von CompanyXYZ hängt vom Bestandsverwaltungssystem ab – Verfügbarkeit und Zuverlässigkeit sind daher zentrale Anforderungen. Vorteile von Azure Cosmos DB:

  * Aufgrund der engen Integration in die Azure-Infrastruktur und der transparenten globalen Replikation von Schreibvorgängen in mehreren Regionen bietet Azure Cosmos DB eine branchenführende [Hochverfügbarkeit von 99,999 %](high-availability.md) gegenüber regionalen Ausfällen.

* Die Lieferkettenpartner von CompanyXYZ befinden sich möglicherweise an separaten geografischen Standorten, sie benötigen jedoch eine zentrale Ansicht des weltweiten Produktbestands für ihre lokalen Vorgänge. Dies umfasst auch das Lesen von Aktualisierungen, die von anderen Lieferkettenpartnern in Echtzeit durchgeführt werden. Außerdem können Aktualisierungen vorgenommen werden, ohne dass die Gefahr von Konflikten mit anderen Partnern mit hohem Durchsatz besteht. Vorteile von Azure Cosmos DB:

  * Dank des eindeutigen Protokolls für die Replikation von Schreibvorgängen in mehreren Regionen und des schreiboptimierten Transaktionsspeichers ohne Latches garantiert Azure Cosmos DB Wartezeiten von unter 10 ms für indizierte Lese- und Schreibvorgänge auf der ganzen Welt im 99. Quantil.

  * Hohe Durchsatzerfassung von Batch- und Streamingdatenfeeds mit [Echtzeitindizierung](index-policy.md) im Transaktionsspeicher.

  * Der Azure Cosmos DB-Transaktionsspeicher bietet drei Optionen mehr als die beiden Extreme der starken und der letztlichen Konsistenz, um den [Kompromiss zwischen Verfügbarkeit und Leistung](./consistency-levels.md) zu erreichen, der den geschäftlichen Anforderungen am ehesten entspricht.

* Die Lieferkettenpartner von CompanyXYZ weisen hochgradig schwankende Datenverkehrsmuster auf, die von Hunderten bis zu Millionen von Anforderungen pro Sekunde reichen. Daher muss die Bestandsverwaltungsplattform mit unerwarteten Bursts im Datenverkehr umgehen.  Vorteile von Azure Cosmos DB:

  * Der Transaktionsspeicher von Azure Cosmos DB unterstützt die elastische Skalierbarkeit von Speicher und Durchsatz mithilfe der horizontalen Partitionierung. Bei Containern und Datenbanken, die im Autopilot-Modus konfiguriert wurden, wird der bereitgestellte Durchsatz automatisch und verzögerungsfrei den Anwendungsanforderungen entsprechend skaliert, ohne dass dies Auswirkungen auf Verfügbarkeit, Latenz, Durchsatz oder Leistung der allgemeinen Workload hat.

* CompanyXYZ muss eine sichere Analyseplattform einrichten, um systemweite Bestandsdaten zu erfassen, damit Analysen und Erkenntnisse zu Lieferkettenpartnern, Geschäftseinheiten und Funktionen möglich sind. Die Analyseplattform muss die Zusammenarbeit über das System, herkömmliche BI-/Berichtsanwendungsfälle, erweiterte Analyseanwendungsfälle und intelligente Prognoselösungen über die betrieblichen Bestandsdaten hinweg ermöglichen. Vorteile der Verwendung von Synapse Link für Azure Cosmos DB:

  * Mithilfe des [Azure Cosmos DB-Analysespeichers](analytical-store-introduction.md), einem vollständig isolierten Columnstore, ermöglicht Synapse Link Analysen ohne ETL (Extract-Transform-Load) in [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) für Ihre verteilten operativen Daten im großen Stil.  Business Analysts, Data Engineers und Data Scientists können jetzt Synapse Spark oder Synapse SQL austauschbar verwenden, um Business Intelligence-, Analyse- und Machine Learning-Pipelines in Quasi-Echtzeit auszuführen, ohne dass sich dies auf die Leistung der Transaktionsworkloads in Azure Cosmos DB auswirkt. Weitere Informationen finden Sie unter [Vorteile von Synapse Link in Azure Cosmos DB](synapse-link.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Dokumenten:

* [Azure Synapse Link für Azure Cosmos DB](synapse-link.md) 

* [Azure Cosmos DB-Analysespeicher](analytical-store-introduction.md)

* [Arbeiten mit Azure Synapse Link für Azure Cosmos DB](configure-synapse-link.md)

* [Häufig gestellte Fragen zu Azure Synapse Link für Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Apache Spark in Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md)

* [Laufzeitunterstützung für serverlose SQL-Pools in Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md)