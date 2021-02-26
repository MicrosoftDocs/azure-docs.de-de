---
title: Was ist Azure Synapse Analytics?
description: Eine Übersicht über Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/28/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 7882fc6a6823a93d09c04a8ae2c19005df4843d8
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917092"
---
# <a name="what-is-azure-synapse-analytics"></a>Was ist Azure Synapse Analytics?

Unternehmensanalysen müssen für jede Art von Daten im großen Umfang funktionieren, unabhängig davon, ob es sich um Rohdaten, optimierte Daten oder stark kuratierte Daten handelt. Hierzu mussten Unternehmen in der Regel Big Data- und Data Warehousing-Technologien in umfangreiche Datenpipelines kombinieren, die mit Daten in relationalen Speichern und Data Lakes arbeiten können. Solche Lösungen sind schwer zu erstellen, zu verwalten und zu schützen. Ihre Komplexität verzögert die Bereitstellung der Erkenntnisse, die Unternehmen benötigen.

**Azure Synapse** ist ein integrierter Analysedienst zur schnelleren Gewinnung von Erkenntnissen aus Data Warehouses und Big Data-Systemen. In Azure Synapse ist die jeweils beste Technologie aus unterschiedlichen Bereichen vereint: **SQL**-Technologie für Data Warehousing in Unternehmen, **Spark**-Technologie für Big Data-Zwecke, **Pipelines** für die Datenintegration und ETL/ELT sowie eine tiefe Integration in andere Azure-Dienste, z. B. **Power BI**, **Cosmos DB** und **Azure ML**.

## <a name="key-features--benefits"></a>Wichtige Features und Vorteile

### <a name="industry-leading-sql"></a>Branchenführendes SQL

* **Synapse SQL** ist ein System für verteilte Abfragen für T-SQL, mit dem Data Warehousing- und Datenvirtualisierungsszenarien realisiert werden können und T-SQL auf Streaming- und Machine Learning-Szenarien erweitert wird.
* Synapse SQL bietet sowohl **serverlose** als auch **dedizierte** Ressourcenmodelle sowie bedarfsorientierte Verbrauchs- und Abrechnungsoptionen. Erstellen Sie dedizierte SQL-Pools zur Reservierung von Rechenleistung für in SQL-Tabellen gespeicherte Daten, um von planbarer Leistung und planbaren Kosten zu profitieren. Für ungeplante Workloads oder Workloads mit Bedarfsspitzen kann der stets verfügbare, serverlose SQL-Endpunkt verwendet werden.
* Nutzen Sie die integrierten **Streamingfunktionen**, um Daten aus Clouddatenquellen in SQL-Tabellen zu übertragen.
* Integrieren Sie mithilfe von **Machine Learning**-Modellen KI in SQL, um Daten per [T-SQL-Vorhersagefunktion (PREDICT)](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true) zu bewerten.

### <a name="industry-standard-apache-spark"></a>Branchenübliches Apache Spark

**Apache Spark für Azure Synapse** bietet eine tiefe und nahtlose Integration von Apache Spark – der beliebtesten Open-Source-basierten Big Data-Engine für Datenaufbereitung, Datentechnik, ETL und maschinelles Lernen.

* ML-Modelle mit SparkML-Algorithmen und AzureML-Integration für Apache Spark 2.4 mit integrierter Unterstützung von Linux Foundation Delta Lake
* Vereinfachtes Ressourcenmodell, bei dem Sie sich nicht mehr mit der Verwaltung von Clustern auseinandersetzen müssen
* Schneller Spark-Start und aggressive automatische Skalierung
* Nutzung Ihrer C#-Kenntnisse und Wiederverwendung von bereits vorhandenem .NET-Code in einer Spark-Anwendung dank integrierter Unterstützung von .NET für Spark

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>Interoperabilität von SQL und Apache Spark für Ihren Data Lake

Azure Synapse beseitigt die herkömmlichen Technologiebarrieren bei der gemeinsamen Verwendung von SQL und Spark. Beide Lösungen können nach Bedarf und Kenntnisstand miteinander kombiniert werden.

* Dank eines gemeinsam genutzten, Hive-kompatiblen Metadatensystems können Tabellen, die in Dateien im Data Lake definiert sind, nahtlos von Spark oder Hive verwendet werden.
* SQL und Spark können zur direkten Erkundung und Analyse von gespeicherten Parquet-, CSV-, TSV- und JSON-Dateien im Data Lake verwendet werden.
* Schnelles, skalierbares Laden und Entladen von Daten zwischen SQL- und Spark-Datenbanken

### <a name="built-in-data-integration-via-pipelines"></a>Integrierte Datenintegration über Pipelines

In Azure Synapse sind die gleiche Datenintegrationsengine und die gleichen Umgebungen integriert wie in Azure Data Factory, was die Erstellung umfangreicher bedarfsorientierter ETL-Pipelines ermöglicht, ohne Azure Synapse Analytics zu verlassen.

* Erfassen von Daten aus über 90 Datenquellen
* Kein Programmieraufwand für ETL dank Datenflussaktivitäten
* Orchestrieren von Notebooks, Spark-Aufträgen, gespeicherten Prozeduren, SQL-Skripts und mehr

### <a name="unified-management-monitoring-and-security"></a>Einheitliche Verwaltung, Überwachung und Sicherheit

Mit Azure Synapse erhalten Unternehmen eine zentrale Lösung für die Verwaltung von Analyseressourcen, für die Nutzungs- und Aktivitätsüberwachung sowie für die Gewährleistung der Sicherheit.

* Zuweisung von Benutzern zu einer Rolle, um den Zugriff auf Analyseressourcen zu vereinfachen
* Differenzierte Zugriffssteuerung für Daten und Code
* Zentrales Dashboard zum Überwachen von Ressourcen, Nutzung und Benutzern in SQL und Spark

### <a name="synapse-studio"></a>Synapse Studio

**Synapse Studio** ist die webbasierte Umgebung, in der sämtliche Funktionen für Dateningenieure zusammengefasst sind. Dadurch können Dateningenieure alle Aufgaben, die für eine umfassende Lösung erforderlich sind, an einem zentralen Ort erledigen.

* Erstellung einer End-to-End-Analyselösung an einem zentralen Ort: Erfassung, Erkundung, Aufbereitung, Orchestrierung und Visualisierung
* Branchenführende Produktivität für Dateningenieure, die SQL- oder Spark-Code schreiben: Erstellung, Debugging und Leistungsoptimierung
* Integration in Unternehmensprozesse mit CI/CD

## <a name="engage-with-the-synapse-engineering-team"></a>Kontakt mit dem Synapse-Entwicklungsteam aufnehmen

- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse): Stellen Sie Fragen zur Entwicklung.
- [Microsoft F&A-Seite](/answers/topics/azure-synapse-analytics.html): Stellen Sie technische Fragen.

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Azure Synapse Analytics](get-started.md)
* [Erstellen eines Arbeitsbereichs](quickstart-create-workspace.md)
* [Verwenden eines serverlosen SQL-Pools](quickstart-sql-on-demand.md)
