---
title: 'Häufig gestellte Fragen: Azure Synapse Analytics'
description: Häufig gestellte Fragen zu Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/25/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: a427c77ec23bb933f96d8aec54ca33169aee84d4
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576025"
---
# <a name="azure-synapse-analytics-frequently-asked-questions"></a>Azure Synapse Analytics: Häufig gestellte Fragen

In diesem Leitfaden finden Sie die am häufigsten gestellten Fragen zu Azure Synapse Analytics.

## <a name="general"></a>Allgemein

### <a name="q-how-can-i-use-rbac-roles-to-secure-my-workspace"></a>F: Wie kann ich RBAC-Rollen verwenden, um meinen Arbeitsbereich zu schützen?

A: Mit Azure Synapse werden verschiedene Rollen sowie Bereiche für deren Zuweisung eingeführt, um das Schützen Ihres Arbeitsbereichs zu vereinfachen.

RBAC-Rollen von Synapse:
* Synapse-Administrator
* Synapse SQL-Administrator
* Synapse Spark-Administrator
* Synapse-Mitwirkender (Vorschau)
* Synapse-Artefaktherausgeber (Vorschau)
* Synapse-Artefaktbenutzer (Vorschau)
* Synapse-Compute-Operator (Vorschau)
* Synapse-Anmeldeinformationen-Benutzer (Vorschau)

Weisen Sie die RBAC-Rollen den folgenden RBAC-Bereichen zu, um Ihren Synapse-Arbeitsbereich zu schützen:
* Arbeitsbereiche
* Spark-Pools
* Integration Runtimes
* Verknüpfte Dienste
* Anmeldeinformationen

Darüber hinaus verfügen Sie bei dedizierten SQL-Pools über alle Sicherheitsfunktionen, mit denen Sie bereits vertraut sind.

### <a name="q-how-do-i-control-cont-dedicated-sql-pools-serverless-sql-pools-and-serverless-spark-pools"></a>F: Wie kann ich dedizierte SQL-Pools, serverlose SQL-Pools und serverlose Spark-Pools steuern?

A: Der Ausgangspunkt ist, dass von Azure Synapse die integrierte Kostenanalyse und die Kostenwarnungen auf Azure-Abonnementebene genutzt werden.

- Dedizierte SQL-Pools: Sie verfügen über direkten Einblick in die Kosten und besitzen die Kontrolle darüber, weil Sie die dedizierten SQL-Pools erstellen und deren Größen angeben. Außerdem können Sie mit Azure RBAC-Rollen steuern, welche Benutzer dedizierte SQL-Pools erstellen oder skalieren können.

- Serverlose SQL-Pools: Sie verfügen über Tools für die Überwachung und Kostenkontrolle, mit denen Sie die täglichen, wöchentlichen und monatlichen Ausgaben begrenzen können. Weitere Informationen finden Sie unter [Kostenverwaltung für serverlose SQL-Pools in Azure Synapse Analytics](./sql/data-processed.md). 

- Serverlose Spark-Pools: Sie können mit Synapse-RBAC-Rollen einschränken, wer Spark-Pools erstellen kann.  

### <a name="q-will-synapse-workspace-support-folder-organization-of-objects-and-granularity-at-ga"></a>F: Verfügt der Synapse-Arbeitsbereich in der Phase „Allgemeine Verfügbarkeit“ über Unterstützung für die Anordnung von Objekten in Ordnern und der Granularität?

A: Für Synapse-Arbeitsbereiche werden benutzerdefinierte Ordner unterstützt.

### <a name="q-can-i-link-more-than-one-power-bi-workspaces-to-a-single-azure-synapse-workspace"></a>F: Kann ich mehr als einen Power BI-Arbeitsbereich mit einem einzelnen Azure Synapse-Arbeitsbereich verknüpfen?
    
A: Derzeit können Sie mit einem Azure Synapse-Arbeitsbereich nur einen Power BI-Arbeitsbereich verknüpfen. 

### <a name="q-is-synapse-link-to-cosmos-db-ga"></a>F: Befindet sich Synapse Link für Cosmos DB in der Phase „Allgemeine Verfügbarkeit“?

A: Synapse Link für Apache Spark befindet sich in der Phase „Allgemeine Verfügbarkeit“. Synapse Link für serverlose SQL-Pools befindet sich in der öffentlichen Vorschauphase.

### <a name="q-does-azure-synapse-workspace-support-cicd"></a>F: Wird für den Azure Synapse-Arbeitsbereich CI/CD unterstützt? 

A: Ja! Alle Pipelineartefakte, Notebooks, SQL-Skripts und Spark-Auftragsdefinitionen befinden sich in Git. Alle Pooldefinitionen werden als ARM-Vorlagen in Git gespeichert. Dedizierte SQL-Poolobjekte (Schemas, Tabellen, Sichten usw.) werden über Datenbankprojekte mit CI/CD-Unterstützung verwaltet.

## <a name="pipelines"></a>Pipelines

### <a name="q-how-do-i-ensure-i-know-what-credential-is-being-used-to-run-a-pipeline"></a>F: Wie kann ich ermitteln, welche Anmeldeinformationen zum Ausführen einer Pipeline verwendet werden? 

A: Jede Aktivität in einer Synapse-Pipeline wird mit den Anmeldeinformationen ausgeführt, die im verknüpften Dienst angegeben sind.

### <a name="q-are-ssis-irs-supported-in-synapse-integrate"></a>F: Werden SSIS IRs in Synapse Integrate unterstützt?

Antwort: Derzeit nicht. 

### <a name="q-how-do-i-migrate-existing-pipelines-from-azure-data-factory-to-an-azure-synapse-workspace"></a>F: Wie kann ich vorhandene Pipelines aus Azure Data Factory zu einem Azure Synapse-Arbeitsbereich migrieren?

A: Aktuell müssen Sie Ihre Azure Data Factory-Pipelines und die zugehörigen Artefakte manuell neu erstellen, indem Sie den JSON-Code aus der ursprünglichen Pipeline exportieren und in Ihren Synapse-Arbeitsbereich importieren.

## <a name="apache-spark"></a>Apache Spark

### <a name="q-what-is-the-difference-between-apache-spark-for-synapse-and-apache-spark"></a>F: Worin besteht der Unterschied zwischen Apache Spark für Synapse und Apache Spark?

A: Apache Spark für Synapse ist mit Apache Spark identisch, verfügt aber zusätzlich über Unterstützung für Integrationen mit anderen Diensten (AAD, Azure ML usw.) und zusätzliche Bibliotheken (mssparkutils, Hummingbird) sowie vorab optimierte Leistungskonfigurationen.

Alle Workloads, die derzeit unter Apache Spark ausgeführt werden, sind ohne Änderungen auch unter MSFT Spark ausführbar. 

### <a name="q-what-versions-of-spark-are-available"></a>F: Welche Versionen von Spark sind verfügbar?

A: Azure Synapse Apache Spark verfügt über umfassende Unterstützung von Spark 2.4. Eine vollständige Liste mit den Kernkomponenten und derzeit unterstützten Versionen finden Sie unter [Versionsunterstützung für Apache Spark](./spark/apache-spark-version-support.md).

### <a name="q-is-there-an-equivalent-of-dbutils-in-azure-synapse-spark"></a>F: Gibt es in Azure Synapse Spark eine Option, die DBUtils entspricht?

A: Ja. Azure Synapse Apache Spark verfügt über die Bibliothek **mssparkutils**. Die vollständige Dokumentation zum Hilfsprogramm finden Sie unter [Einführung in Microsoft Spark-Hilfsprogramme](./spark/microsoft-spark-utilities.md).

### <a name="q-how-do-i-set-session-parameters-in-apache-spark"></a>F: Wie lege ich Sitzungsparameter in Apache Spark fest?

A: Verwenden Sie zum Festlegen von Sitzungsparametern den verfügbaren Magic-Befehl „%%configure“. Damit die Parameter wirksam werden, ist ein Neustart der Sitzung erforderlich. 

### <a name="q-how-do-i-set-cluster-level-parameters-in-a-serverless-spark-pool"></a>F: Wie kann ich in einem serverlosen Spark-Pool Parameter für die Clusterebene festlegen?

A: Zum Festlegen von Parametern für die Clusterebene können Sie eine „spark.conf“-Datei für den Spark-Pool angeben. Vom Pool werden dann die Parameter berücksichtigt, die in der Konfigurationsdatei enthalten sind. 

### <a name="q-can-i-run-a-multi-user-spark-cluster-in-azure-synapse-analytics"></a>F: Kann ich einen Spark-Cluster mit mehreren Benutzern in Azure Synapse Analytics ausführen?
 
A: Azure Synapse verfügt über zweckgebundene Engines für bestimmte Anwendungsfälle. Apache Spark für Synapse wurde als Auftragsdienst und nicht als Clustermodell konzipiert. Es gibt zwei Szenarien, in denen ein mehrbenutzerfähiges Clustermodell benötigt wird.

**Szenario 1: Viele Benutzer greifen auf einen Cluster zu, um Daten für BI-Zwecke bereitzustellen.**

Die einfachste Lösungsmöglichkeit ist das Aufbereiten der Daten mit Spark und die anschließende Nutzung der Bereitstellungsfunktionen von Synapse SQL, damit für Power BI eine Verbindung mit diesen Datasets hergestellt werden kann.

**Szenario 2: In einem Cluster sind mehrere Entwickler angeordnet, um Kosten zu sparen.**
 
Bei diesem Szenario sollte für jeden Entwickler ein serverloser Spark-Pool vorhanden sein, für den die Nutzung einer geringen Zahl von Spark-Ressourcen festgelegt ist. Da für serverlose Spark-Pools erst dann Kosten anfallen, wenn sie aktiv genutzt werden, kann bei mehreren vorhandenen Entwicklern eine Kostenreduzierung erzielt werden. Von den Pools werden Metadaten (Spark-Tabellen) gemeinsam genutzt, damit die Zusammenarbeit leicht möglich ist.

### <a name="q-how-do-i-include-manage-and-install-libraries"></a>F: Wie kann ich Bibliotheken einfügen, verwalten und installieren? 

A:  Sie können externe Pakete mit der Datei „requirements.txt“ über den Synapse-Arbeitsbereich oder das Azure-Portal installieren, während Sie den Spark-Pool erstellen. Weitere Informationen finden Sie unter [Verwalten von Bibliotheken für Apache Spark in Azure Synapse Analytics](./spark/apache-spark-azure-portal-add-libraries.md).

## <a name="dedicated-sql-pools"></a>Dedizierte SQL-Pools

### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-pools"></a>F: Welche Funktionsunterschiede gibt es zwischen dedizierten SQL-Pools und serverlosen Pools?

A: Eine vollständige Liste mit den Unterschieden finden Sie unter [T-SQL-Funktionsunterschiede in Synapse SQL](./sql/overview-features.md).

### <a name="q-now-that-azure-synapse-is-ga-how-do-i-move-my-dedicated-sql-pools-that-were-previously-standalone-into-azure-synapse"></a>F: Da Azure Synapse jetzt allgemein verfügbar ist: Wie kann ich meine dedizierten SQL-Pools, die bisher eigenständig waren, nach Azure Synapse verschieben? 

A: Eine „Verschiebung“ oder „Migration“ ist nicht möglich. Sie können auswählen, dass neue Arbeitsbereichsfeatures für Ihre vorhandenen Pools aktiviert werden sollen. Wenn Sie so vorgehen, kommt es nicht zu Breaking Changes, sondern Sie können die neuen Features nutzen, z. B. Synapse Studio, Spark und serverlose SQL-Pools.

### <a name="q-what-is-the-default-deployment-of-dedicated-sql-pools-now"></a>F: Wie sieht die Standardbereitstellung von dedizierten SQL-Pools jetzt aus? 

A: Standardmäßig werden alle neuen dedizierten SQL-Pools in einem Arbeitsbereich bereitgestellt. Bei Bedarf können Sie aber trotzdem noch einen dedizierten SQL-Pool (vormals SQL DW) basierend auf dem Formfaktor „Eigenständig“ erstellen. 


### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-sql-pool"></a>F: Welche Funktionsunterschiede gibt es zwischen dedizierten SQL-Pools und serverlosen SQL-Pools? 

A: Eine vollständige Liste mit den Unterschieden finden Sie unter [T-SQL-Funktionsunterschiede in Synapse SQL](./sql/overview-features.md).

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Azure Synapse Analytics](get-started.md)
* [Erstellen eines Arbeitsbereichs](quickstart-create-workspace.md)
* [Verwenden eines serverlosen SQL-Pools](quickstart-sql-on-demand.md)
