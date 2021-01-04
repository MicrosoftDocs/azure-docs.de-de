---
title: Azure Synapse Link für Azure Cosmos DB, Vorteile, Anwendungsmöglichkeiten
description: Erfahren Sie mehr über Azure Synapse Link für Azure Cosmos DB. Synapse Link ermöglicht Ihnen das Ausführen von Analysen in Quasi-Echtzeit (HTAP) mithilfe von Azure Synapse Analytics über operative Daten in Azure Cosmos DB.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.reviewer: sngun
ms.openlocfilehash: d083bc0b7726a284dcfd03e49d47c2a342db023c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461808"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db"></a>Was ist Azure Synapse Link für Azure Cosmos DB?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Die Unterstützung von serverlosen SQL-Pools in Synapse für Azure Synapse Link für Azure Cosmos DB befindet sich derzeit in der Vorschauphase. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Synapse Link für Azure Cosmos DB ist eine cloudnative hybride Verarbeitungsfunktion für Transaktionen und Analysen (Hybrid Transactional and Analytical Processing, HTAP), mit der Sie in Azure Cosmos DB in Quasi-Echtzeit Analysen für operative Daten ausführen können. Azure Synapse Link sorgt für eine nahtlose Integration zwischen Azure Cosmos DB und Azure Synapse Analytics.

Mithilfe des [Azure Cosmos DB-Analysespeichers](analytical-store-introduction.md), einem vollständig isolierten Columnstore, ermöglicht Azure Synapse Link keine maßstabgerechten ETL-Analysen (Extract-Transform-Load) in [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) für Ihre operativen Daten. Business Analysts, Data Engineers und Data Scientists können Synapse Spark oder Synapse SQL jetzt austauschbar verwenden, um Business Intelligence-, Analyse- und Machine Learning-Pipelines in Quasi-Echtzeit auszuführen. Dies können Sie erreichen, ohne dass es sich auf die Leistung der Transaktionsworkloads in Azure Cosmos DB auswirkt. 

Die folgende Abbildung zeigt die Integration von Azure Synapse Link in Azure Cosmos DB und Azure Synapse Analytics: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Architekturdiagramm für die Integration von Azure Synapse Analytics in Azure Cosmos DB" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Vorteile

Um umfangreiche operative Datasets zu analysieren und gleichzeitig die Auswirkungen auf die Leistung unternehmenskritischer Transaktionsworkloads zu minimieren, werden die operativen Daten in Azure Cosmos DB von ETL-Pipelines (Extrahieren, Transformieren, Laden) extrahiert und verarbeitet. ETL-Pipelines erfordern viele Datenverschiebungsebenen, die zu einer starken betrieblichen Komplexität und zu Leistungseinbußen bei ihren Transaktionsworkloads führt. Außerdem nimmt die Wartezeit von der Ursprungszeit zum Analysieren der operativen Daten zu.

Im Vergleich zu herkömmlichen ETL-basierten Lösungen bietet Azure Synapse Link für Azure Cosmos DB mehrere Vorteile, wie z. B.:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Verringerte Komplexität ohne zu verwaltende ETL-Aufträge

Azure Synapse Link ermöglicht Ihnen den direkten Zugriff auf den Azure Cosmos DB-Analysespeicher mithilfe von Azure Synapse Analytics ohne komplexe Datenverschiebungen. Alle Aktualisierungen der operativen Daten werden im Analysespeicher in Quasi-Echtzeit ohne ETL- oder Änderungsfeedaufträge angezeigt. Sie können umfangreiche Analysen für den Analysespeicher von Azure Synapse Analytics ohne eine zusätzliche Datentransformation ausführen.

### <a name="near-real-time-insights-into-your-operational-data"></a>Einblicke in Ihre operativen Daten in Quasi-Echtzeit

Sie können mit Azure Synapse Link jetzt umfassende Einblicke in Ihre operativen Daten in Quasi-Echtzeit erhalten. ETL-basierte Systeme verfügen tendenziell über längere Wartezeiten bei der Analyse der operativen Daten, da zum Extrahieren, Transformieren und Laden dieser Daten viele Ebenen erforderlich sind. Mit nativer Integration des Azure Cosmos DB-Analysespeichers in Azure Synapse Analytics können Sie operative Daten in Quasi-Echtzeit analysieren, um neue Geschäftsszenarien zu ermöglichen. 

### <a name="no-impact-on-operational-workloads"></a>Keine Auswirkungen auf operative Workloads

Mit Azure Synapse Link können Sie analytische Abfragen für einen Azure Cosmos DB-Analysespeicher (einen separaten Columnstore) ausführen, während die Transaktionsvorgänge mithilfe des bereitgestellten Durchsatzes für die Transaktionsworkload (ein zeilenbasierter Transaktionsspeicher) verarbeitet werden.  Die Analyseworkload wird unabhängig vom Transaktionsworkload-Datenverkehr bereitgestellt, ohne dass der für Ihre operativen Daten bereitgestellte Durchsatz beansprucht wird.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Optimiert für umfangreiche Analyseworkloads

Der Azure Cosmos DB-Analysespeicher ist optimiert, um Skalierbarkeit, Elastizität und Leistung für Analyseworkloads ohne jegliche Abhängigkeit von den Computelaufzeiten bereitzustellen. Die Speichertechnologie ist für die Optimierung Ihrer Analyseworkloads selbstverwaltet. Dank in Azure Synapse Analytics integrierter Unterstützung bietet der Zugriff auf diese Speicherebene Einfachheit und hohe Leistung.

### <a name="cost-effective"></a>Kosteneffizienz

Mit Azure Synapse Link erhalten Sie eine kostenoptimierte, vollständig verwaltete Lösung für die operative Analyse. Die zusätzlichen Speicherebenen und die Computekapazität entfallen, die bei herkömmlichen ETL-Pipelines zum Analysieren operativer Daten erforderlich sind. 

Der Azure Cosmos DB-Analysespeicher verfügt über ein nutzungsbasiertes Preismodell basierend auf Datenspeicher, analytischen Lese-/Schreibvorgängen und ausgeführten Abfragen. Es ist nicht erforderlich, dass Sie einen Durchsatz bereitstellen, wie es heute bei Transaktionsworkloads erforderlich ist. Durch den Zugriff auf Ihre Daten mit äußerst elastischen Computemodulen aus Azure Synapse Analytics werden die Gesamtkosten für die Ausführung von Speicher und Compute sehr günstig.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Analysen für lokal verfügbare, global verteilte Schreibvorgänge in mehreren Regionen

Sie können analytische Abfragen effektiv für die nächstgelegene regionale Kopie der Daten in Azure Cosmos DB ausführen. Azure Cosmos DB bietet eine fortschrittliche Aktiv-/Aktiv-Funktion zum Ausführen der global verteilten Analyseworkloads zusammen mit Transaktionsworkloads.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>Ermöglichen von HTAP-Szenarien für Ihre operativen Daten

Synapse Link verbindet den Azure Cosmos DB-Analysespeicher mit Laufzeitunterstützung für die Azure Synapse-Analyse. Diese Integration ermöglicht Ihnen das Erstellen cloudnativer HTAP-Lösungen (Hybrid Transactional/Analytical Processing, hybride transaktionale/analytische Verarbeitung), die Erkenntnisse basierend auf Echtzeitaktualisierungen Ihrer operativen Daten über große Datasets generieren. Dies ermöglicht neue Geschäftsszenarien, um Warnungen auf Grundlage von Livetrends auszugeben, Dashboards in Quasi-Echtzeit zu erstellen und geschäftliche Erfahrungen ausgehend vom Benutzerverhalten zu erhalten.

### <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB-Analysespeicher

Der Azure Cosmos DB-Analysespeicher ist eine spaltenorientierte Darstellung Ihrer operativen Daten in Azure Cosmos DB. Dieser Analysespeicher eignet sich für schnelle, kostengünstige Abfragen für große operative Datasets, ohne dass dabei Daten kopiert werden und ohne Auswirkungen auf die Leistung der Transaktionsworkloads.

Der Analysespeicher ruft automatisch Einfüge-, Aktualisierungs- und Löschvorgänge in Ihren Transaktionsworkloads in Quasi-Echtzeit als vollständig verwaltete Funktion (automatische Synchronisierung) von Azure Cosmos DB auf. Es ist kein Änderungsfeed oder ETL erforderlich. 

Wenn Sie über ein global verteiltes Azure Cosmos DB-Konto verfügen, ist es nach dem Aktivieren des Analysespeichers für einen Container in allen Regionen für dieses Konto verfügbar. Weitere Informationen zum Analysespeicher finden Sie in der [Übersicht über den Azure Cosmos DB-Analysespeicher](analytical-store-introduction.md).

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integration in Azure Synapse Analytics

Mit Synapse Link können Sie jetzt ausgehend von Azure Synapse Analytics direkt eine Verbindung mit Ihren Azure Cosmos DB-Containern herstellen und ohne separate Connectors auf den Analysespeicher zugreifen. Azure Synapse Analytics unterstützt derzeit Synapse Link mit [Synapse Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) und [serverlosen SQL-Pools](../synapse-analytics/sql/on-demand-workspace-overview.md).

Sie können die Daten aus dem Azure Cosmos DB-Analysespeicher gleichzeitig abfragen, wobei Interoperabilität für verschiedene von Azure Synapse Analytics unterstützte Analyselaufzeiten unterstützt wird. Zum Analysieren der operativen Daten sind keine weiteren Datentransformationen erforderlich. Sie können die Analysespeicherdaten mithilfe von Folgendem abfragen und analysieren:

* Synapse Apache Spark mit vollständiger Unterstützung für Scala, Python, SparkSQL und C#. Synapse Spark ist von zentraler Bedeutung für Datentechnik- und Data Science-Szenarien.

* Serverlose SQL-Pools mit T-SQL und Unterstützung für bekannte BI-Tools (z. B. Power BI Premium usw.)

> [!NOTE]
> Aus Azure Synapse Analytics können Sie auf Analyse- und Transaktionsspeicher in Ihrem Azure Cosmos DB-Container zugreifen. Wenn Sie jedoch umfangreiche Analysen oder Scans für Ihre operativen Daten ausführen möchten, empfiehlt es sich, den Analysespeicher zu verwenden, um Leistungseinbußen bei Transaktionsworkloads zu vermeiden.

> [!NOTE]
> Sie können Analysen mit geringer Latenz in einer Azure-Region ausführen, indem Sie Ihren Azure Cosmos DB-Container mit der Synapse-Laufzeit in dieser Region verbinden.

Diese Integration ermöglicht die folgenden HTAP-Szenarien für verschiedene Benutzer:

* Ein BI-Entwickler, der einen Power BI-Bericht modellieren und veröffentlichen möchte, um direkt über Synapse SQL auf die operativen Daten in Azure Cosmos DB zuzugreifen.

* Ein Datenanalyst, der Erkenntnisse aus den operativen Daten in einem Azure Cosmos DB-Container ableiten möchte, indem er ihn mit Synapse SQL abfragt, die Daten maßstabgerecht liest und diese Ergebnisse mit anderen Datenquellen kombiniert.

* Ein Datenanalyst, der Synapse Spark verwenden möchte, um eine Funktion zu finden, die das Modell verbessert und es ohne eine komplexe Datentechnik trainiert. Außerdem können Sie die Ergebnisse des Modells im Anschluss in Azure Cosmos DB schreiben, um eine Echtzeitbewertung der Daten über Spark Synapse durchzuführen.

* Ein Data Engineer, der Daten für Consumer zugänglich machen möchte, indem er SQL- oder Spark-Tabellen über Azure Cosmos DB-Container ohne manuelle ETL-Prozesse erstellt.

Weitere Informationen zur Azure Synapse Analytics-Laufzeitunterstützung für Azure Cosmos DB finden Sie unter [Unterstützung für Azure Synapse Analytics für Cosmos DB](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md).

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>Anwendungsmöglichkeiten für Azure Synapse Link für Azure Cosmos DB

Synapse Link wird in den folgenden Fällen empfohlen:

* Wenn Sie Azure Cosmos DB-Kunde sind und Analysen, BI und maschinelles Lernen für Ihre operativen Daten ausführen möchten. In solchen Fällen bietet Synapse Link eine stärker integrierte Analyse, ohne dass sich dies auf den bereitgestellten Durchsatz Ihres Transaktionsspeichers auswirkt. Beispiel:

  * Wenn Sie Analysen oder BI auf Ihren operativen Azure Cosmos DB-Daten direkt mithilfe von separaten Connectors ausführen, oder

  * Wenn Sie ETL-Prozesse ausführen, um operative Daten in ein separates Analysesystem zu extrahieren.
 
In solchen Fällen bietet Synapse Link eine stärker integrierte Analyse, ohne dass sich dies auf den bereitgestellten Durchsatz Ihres Transaktionsspeichers auswirkt.

Synapse Link wird nicht empfohlen, wenn Sie herkömmliche Data Warehouse-Anforderungen wie hohe Parallelität, Workloadverwaltung und Persistenz von Aggregaten über mehrere Datenquellen hinweg haben. Weitere Informationen finden Sie unter den [gängigen Szenarien, die mit Azure Synapse Link für Azure Cosmos DB](synapse-link-use-cases.md).

## <a name="limitations"></a>Einschränkungen

* Derzeit wird Azure Synapse Link für Azure Cosmos DB für die SQL-API und die Azure Cosmos DB-API für MongoDB unterstützt. Sie wird nicht für die Gremlin-API und die Tabellen-API unterstützt. Die Unterstützung für die Cassandra-API befindet sich in der privaten Vorschau. Weitere Informationen erhalten Sie vom [Azure Synapse Link-Team](mailto:cosmosdbsynapselink@microsoft.com).  

* Zurzeit kann der analytische Speicher nur für neue Container aktiviert werden. Wenn Sie den analytischen Speicher für vorhandene Container verwenden möchten, migrieren Sie Daten mithilfe von [Azure Cosmos DB-Migrationstools](cosmosdb-migrationchoices.md) aus Ihren vorhandenen Containern in neue Container. Sie können Synapse Link für neue und vorhandene Azure Cosmos DB-Konten aktivieren.

* Für Container mit aktiviertem Analysespeicher wird derzeit die automatische Sicherung und Wiederherstellung Ihrer Daten im Analysespeicher nicht unterstützt. Wenn Synapse Link für ein Datenbankkonto aktiviert ist, erstellt Azure Cosmos DB weiterhin automatisch im geplanten Sicherungsintervall [Sicherungen](./online-backup-and-restore.md) Ihrer Daten im Transaktionsspeicher (nur) von Containern. Beachten Sie dabei Folgendes: Wenn ein Container mit aktiviertem Analysespeicher in einem neuen Konto wiederhergestellt wird, ist im wiederhergestellten Container nur der Transaktionsspeicher aktiviert, nicht jedoch der Analysespeicher. 

* Der Zugriff auf den Azure Cosmos DB-Analysespeicher mit Synapse SQL (bereitgestellt) ist derzeit nicht verfügbar.

## <a name="pricing"></a>Preise

Das Abrechnungsmodell für Azure Synapse Link umfasst die Kosten für die Nutzung des Azure Cosmos DB-Analysespeichers und der Synapse-Runtime. Weitere Informationen finden Sie unter den [Preisen für den Azure Cosmos DB-Analysespeicher](analytical-store-introduction.md#analytical-store-pricing) und den [Preisen für Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Dokumenten:

* [Übersicht über den Azure Cosmos DB-Analysespeicher](analytical-store-introduction.md)

* [Erste Schritte mit Azure Synapse Link für Azure Cosmos DB](configure-synapse-link.md)
 
* [In der Azure Synapse Analytics-Laufzeit unterstützte Features](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Häufig gestellte Fragen zu Azure Synapse Link für Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Anwendungsfälle für Azure Synapse Link für Azure Cosmos DB](synapse-link-use-cases.md)