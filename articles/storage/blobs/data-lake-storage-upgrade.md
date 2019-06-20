---
title: Upgrade von Big Data-Analyselösungen von Azure Data Lake Storage Gen1 auf Azure Data Lake Storage Gen2
description: Durchführen eines Upgrades für Ihre Lösung für die Verwendung von Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 02/07/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 84e3aff9c1c8cb3e7fe399c861c2c7d58c278fed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64730539"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>Upgrade von Big Data-Analyselösungen von Azure Data Lake Storage Gen1 auf Azure Data Lake Storage Gen2

Wenn Sie Azure Data Lake Storage Gen1 in Ihren Big Data-Analyselösungen verwenden, hilft Ihnen dieser Leitfaden beim Durchführen eines Upgrades für diese Lösungen zur Verwendung von Azure Data Lake Storage Gen2. Sie können dieses Dokument verwenden, um die Abhängigkeiten zu bewerten, über die Ihre Lösung auf Data Lake Storage Gen1 verfügt. In diesem Leitfaden wird zudem erläutert, wie das Upgrade geplant und ausgeführt wird.

Wir helfen Ihnen bei den folgenden Aufgaben:

:heavy_check_mark: Bewerten Ihrer Upgradebereitschaft

:heavy_check_mark: Planen eines Upgrades

:heavy_check_mark: Durchführen des Upgrades

## <a name="assess-your-upgrade-readiness"></a>Bewerten Ihrer Upgradebereitschaft

Unser Ziel ist es, dass alle Funktionen, die sich in Data Lake Storage Gen1 befinden, auch in Data Lake Storage Gen2 zur Verfügung stehen. Die Art und Weise, wie diese Funktionen verfügbar gemacht werden, also z. B. in SDK, CLI usw., kann sich zwischen Data Lake Storage Gen1 und Data Lake Storage Gen2 unterscheiden. Anwendungen und Dienste, die mit Data Lake Storage Gen1 arbeiten, müssen genauso mit Data Lake Storage Gen2 funktionieren. Schließlich werden einige der Funktionen nicht sofort in Data Lake Storage Gen2 verfügbar sein. Sobald diese jedoch verfügbar sind, werden wir sie in diesem Dokument bekanntgeben.

Die nächsten Abschnitte helfen Ihnen bei der Entscheidung, wie Sie am besten ein Upgrade auf Data Lake Storage Gen2 durchführen und wann dieses Upgrade am sinnvollsten ist.

### <a name="data-lake-storage-gen1-solution-components"></a>Lösungskomponenten von Data Lake Storage Gen1

Wenn Sie Data Lake Storage Gen1 in Ihren Analyselösungen oder Pipelines verwenden, sind dort in den meisten Fällen weitere Technologien vorhanden, die Sie nutzen, um die gesamte End-to-End-Funktionalität zu erzielen. In diesem [Artikel](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) werden die unterschiedlichen Komponenten des Datenflusses beschrieben, die die Erfassung, Verarbeitung und die Nutzung von Daten mit sich bringen.

Zusätzlich gibt es übergreifende Komponenten zum Bereitstellen, Verwalten und Überwachen dieser Komponenten. Jede dieser Komponenten arbeitet über eine Schnittstelle, die am besten zur jeweiligen Komponente passt, mit Data Lake Storage Gen1. Wenn Sie ein Upgrade für Ihre Lösung auf Data Lake Storage Gen2 planen, müssen Sie die Schnittstellen beachten, die verwendet werden. Sie müssen sowohl ein Upgrade der Verwaltungsschnittstellen als auch der Datenschnittstellen durchführen, da jede Schnittstelle unterschiedliche Anforderungen besitzt.

![Lösungskomponenten von Data Lake Storage](./media/data-lake-storage-upgrade/solution-components.png)

**Abbildung 1** oben stellt die Funktionskomponenten dar, die in den meisten Analyselösungen vorkommen.

**Abbildung 2** stellt ein Beispiel dar, das zeigt, wie diese Komponenten mithilfe bestimmter Technologien implementiert werden.

Die Speicherfunktion in **Abbildung 1** wird von Data Lake Storage Gen1 bereitgestellt (**Abbildung 2**). Beachten Sie, wie die unterschiedlichen Komponenten im Datenfluss über REST-APIs oder Java SDK mit Data Lake Storage Gen1 interagieren. Beachten Sie außerdem, wie die übergreifenden Funktionskomponenten mit Data Lake Storage Gen1 interagieren. Die Bereitstellungskomponente verwendet Azure-Ressourcenvorlagen, wobei die Überwachungskomponente, die Azure Monitor-Protokolle nutzt, operative Daten verwendet, die von Data Lake Storage Gen1 stammen.

Für ein Upgrade für eine Lösung von Data Lake Storage Gen1 auf Data Lake Storage Gen2 müssen Sie die Daten und Metadaten kopieren und die Datenflüsse neu verknüpfen. Anschließend müssen alle Komponenten mit Data Lake Storage Gen2 funktionieren.

In den Abschnitten unten erhalten Sie Informationen, die Ihnen dabei helfen, bessere Entscheidungen zu treffen:

:heavy_check_mark: Plattformfunktionen

:heavy_check_mark: Programmierschnittstellen

:heavy_check_mark: Azure-Ökosystem

:heavy_check_mark: Ökosystem der Partnerunternehmen

:heavy_check_mark: Informationen zum Vorgang

In jedem Abschnitt können Sie die „Must-haves“ für Ihr Upgrade bestimmen. Nachdem Sie sichergestellt haben, dass die Funktionen verfügbar sind oder genügend Problemumgehungen zur Verfügung stehen, fahren Sie mit dem Abschnitt [Planen eines Upgrades](#planning-for-an-upgrade) in diesem Leitfaden fort.

### <a name="platform-capabilities"></a>Plattformfunktionen

In diesem Abschnitt wird beschrieben, welche Data Lake Storage Gen1-Plattformfunktionen derzeit in Data Lake Storage Gen2 verfügbar sind.

| | Data Lake Storage Gen1 | Ziel von Data Lake Storage Gen2 | Verfügbarkeitsstatus von Data Lake Storage Gen2  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Datenorganisation| Unterstützt als Ordner und Dateien gespeicherte Daten | Unterstützt als Objekte bzw. Blobs gespeicherte Daten sowie Ordner und Dateien: [Hierarchischer Namespacedienst](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Unterstützt als Ordner und Dateien gespeicherte Daten: *jetzt verfügbar* <br><br> Unterstützt als Objekte/Blobs gespeicherte Daten: *noch nicht verfügbar* |
| Namespace| Hierarchical | Hierarchical |  *Jetzt verfügbar*  |
| API  | REST-API über HTTPS | REST-API über HTTP/HTTPS| *Jetzt verfügbar* |
| Serverseitige API| [WebHDFS-kompatible REST-API](https://msdn.microsoft.com/library/azure/mt693424.aspx) | REST-API des Azure Blob-Diensts [Data Lake Storage Gen2-REST-API](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage Gen2-REST-API: *jetzt verfügbar* <br><br> REST-API für Azure Blob-Dienst: *noch nicht verfügbar*       |
| Hadoop-Dateisystemclient | Ja ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Ja ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Jetzt verfügbar*  |  
| Datenvorgänge: Autorisierung  | Auf Azure Active Directory-Identitäten basierende POSIX-Zugriffssteuerungslisten (ACLs), die auf Datei- und Ordnerebene festgelegt sind  | Auf dem [gemeinsam verwendeten Schlüssel](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) von Azure Active Directory-Identitäten basierte POSIX-Zugriffssteuerungslisten (ACLs) auf Datei- und Ordnerebene für die Autorisierung der rollenbasierten Zugriffssteuerung ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) auf Kontoebene für den Zugriff auf Container | *Jetzt verfügbar* |
| Datenvorgänge: Protokolle  | Ja | Einmalige Anforderungen für Protokolle für eine bestimmte Dauer über die Azure-Überwachungsintegration für Supporttickets. | Einmalige Anforderungen für Protokolle für eine bestimmte Dauer über Supporttickets: *jetzt verfügbar*<br><br> Azure-Überwachungsintegration: *noch nicht verfügbar* |
| Verschlüsselung für ruhende Daten | Transparent, serverseitig mit dienstverwalteten Schlüsseln und mit kundenverwalteten Schlüsseln im Azure Key Vault | Transparent, serverseitig mit dienstverwalteten Schlüsseln und mit kundenverwalteten Schlüsseln im Azure Key Vault | Dienstverwaltete Schlüssel: *jetzt verfügbar*<br><br> Kundenverwaltete Schlüssel: *noch nicht verfügbar*  |
| Verwaltungsvorgänge (z.B. Kontoerstellung) | [Rollenbasierte Zugriffssteuerung](https://docs.microsoft.com/azure/role-based-access-control/overview) (Role-Based Access Control, RBAC) von Azure zur Kontoverwaltung | [Rollenbasierte Zugriffssteuerung](https://docs.microsoft.com/azure/role-based-access-control/overview) (Role-Based Access Control, RBAC) von Azure zur Kontoverwaltung | *Jetzt verfügbar*|
| Entwickler-SDKs | .NET, Java, Python, Node.js  | .NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS| *Noch nicht verfügbar* |
| |Optimierte Leistung für parallele Analyseworkloads. Hoher Durchsatz, hohe IOPS. | Optimierte Leistung für parallele Analyseworkloads. Hoher Durchsatz, hohe IOPS. | *Jetzt verfügbar* |
| Unterstützung von virtuellen Netzwerken (VNETs)  | [Integration des virtuellen Netzwerks](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Verwenden von Dienstendpunkten für Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Jetzt verfügbar* |
| Größenbeschränkungen | Keine Beschränkungen für Kontogrößen, Dateigrößen oder die Anzahl von Dateien. | Keine Beschränkungen für Kontogrößen oder die Anzahl von Dateien. Die Dateigröße ist auf 5 TB beschränkt. | *Jetzt verfügbar*|
| Georedundanz| Lokal redundant (LRS, lokal redundanter Speicher) | Lokal redundant (LRS), zonenredundant (ZRS) global redundant (GRS), global redundant mit Lesezugriff (RA-GRS). [Hier](https://docs.microsoft.com/azure/storage/common/storage-redundancy) finden Sie weitere Informationen dazu.| *Jetzt verfügbar* |
| Regionale Verfügbarkeit | Siehe [hier](https://azure.microsoft.com/regions/) | Alle [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Jetzt verfügbar*                                                                                                                           |
| Preis                                       | Siehe [Preise](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Siehe [Preise](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| Verfügbarkeits-SLA                            | Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel (SLAs)](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/).                                                                   | Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel (SLAs)](https://azure.microsoft.com/support/legal/sla/storage/v1_3/).                                                                                                                                                                                                                                                                                                                                                | *Jetzt verfügbar*                                                                                                                           |
| Datenverwaltung                             | Ablaufdatum der Datei                                                                                                                                        | Richtlinien für den Lebenszyklus                                                                                                                                                                                                                                                                                                                                                                                                          | *Noch nicht verfügbar*                                                                                                                       |

### <a name="programming-interfaces"></a>Programmierschnittstellen

Diese Tabelle beschreibt, welche APIs für Ihre benutzerdefinierten Anwendungen verfügbar sind. Damit alles etwas übersichtlicher ist, haben wir diese APIs in zwei Typen unterteilt: Verwaltungs-APIs und Dateisystem-APIs.

Verwaltungs-APIs helfen Ihnen bei der Verwaltung von Konten, während Dateisystem-APIs Sie beim Ausführen der Dateien und Ordner unterstützen.

|  APIs                           |  Data Lake Storage Gen1                                                                                                                                                                                                                                                                                                   | Verfügbarkeit für Data Lake Storage Gen2: mit der Authentifizierung mit gemeinsam verwendetem Schlüssel | Verfügbarkeit für Data Lake Storage Gen2: mit der OAuth-Authentifizierung                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK: Verwaltung                  | [Link](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *Nicht unterstützt*                                                      | *Jetzt verfügbar:* [Link](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| .NET SDK: Dateisystem                  | [Link](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | *Noch nicht verfügbar*                                                | *Noch nicht verfügbar*                                                                                                                                             |
| Java SDK: Verwaltung                  | [Link](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Nicht unterstützt*                                                      | *Jetzt verfügbar:* [Link](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| Java SDK: Dateisystem                  | [Link](https://docs.microsoft.com/java/api/overview/azure/datalake)                                                                                                                                                                                                                                         | *Noch nicht verfügbar*                                                | *Noch nicht verfügbar*                                                                                                                                             |
| Node.js: Verwaltung                   | [Link](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | Nicht unterstützt                                                      | *Jetzt verfügbar:* [Link](https://azure.github.io/azure-storage-node/)                                                                                            |
| Node.js: Dateisystem                   | [Link](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *Noch nicht verfügbar*                                                | *Noch nicht verfügbar*                                                                                                                                             |
| Python: Verwaltung                    | [Link](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *Nicht unterstützt*                                                      | *Jetzt verfügbar:* [Link](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python. Dateisystem                    | [Link](https://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | *Noch nicht verfügbar*                                                | *Noch nicht verfügbar*                                                                                                                                             |
| REST API: Verwaltung                  | [Link](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Nicht unterstützt*                                                      | *Jetzt verfügbar:*                                                                                                                                               |
| REST-API: Dateisystem                  | [Link](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | *Jetzt verfügbar*                                                    | *Jetzt verfügbar:* [Link](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| PowerShell: Verwaltung und Dateisystem | [Link](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | Verwaltung: Nicht unterstützt; Dateisystem: *Noch nicht verfügbar*        | Verwaltung: *jetzt verfügbar –* [Link](https://docs.microsoft.com/powershell/module/az.storage) <br><br>Dateisystem: *Noch nicht verfügbar* |
| CLI: Verwaltung                       | [Link](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Nicht unterstützt*                                                      | *Jetzt verfügbar:* [Link](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| CLI: Dateisystem                       | [Link](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | *Noch nicht verfügbar*                                                | *Noch nicht verfügbar*                                                                                                                                             |
| Azure Resource Manager-Vorlagen: Verwaltung             | [Vorlage1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Vorlage2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Vorlage3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Nicht unterstützt*                                                      | *Jetzt verfügbar:* [Link](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Azure-Ökosystem

Wenn Sie Data Lake Storage Gen1 verwenden, können Sie eine Vielzahl an Microsoft-Diensten und -Produkten in Ihren End-to-End-Pipelines verwenden. Diese Dienste und Produkte arbeiten entweder direkt oder indirekt mit Data Lake Storage Gen1. In dieser Tabelle ist eine Liste der Dienste aufgeführt, die wir so verändert haben, dass sie nun mit Data Lake Storage Gen1 funktionieren. Zudem sind die Dienste aufgelistet, die derzeit mit Data Lake Storage Gen2 kompatibel sind.

| **Bereich**             | **Verfügbarkeit für Data Lake Storage Gen1**                                                                                                                                    | **Verfügbarkeit für Data Lake Storage Gen2: mit der Authentifizierung mit gemeinsam verwendetem Schlüssel**                                                                                                           | **Verfügbarkeit für Data Lake Storage Gen2: mit OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Analyseframework  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Jetzt verfügbar*                                                                                                                                                              | *Jetzt verfügbar*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3.6: *Jetzt verfügbar*; HDInsight 4.0: *Noch nicht verfügbar*      | HDInsight 3.6 ESP: *jetzt verfügbar* <br><br>  HDInsight 4.0 ESP: *noch nicht verfügbar*                                                                 |
|                      | Databricks Runtime 3.1 und höher                                                                                                                                               | [Databricks Runtime 5.1 und höher](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2): *Jetzt verfügbar* | [Databricks Runtime 5.1 und höher](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2): *Jetzt verfügbar*                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Nicht unterstützt*                                                                                                                                                              | *Jetzt verfügbar*: [Link](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| Datenintegration     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Version 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage): *Jetzt verfügbar*, Version 1: *Nicht unterstützt*                               | [Version 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage): *jetzt verfügbar* <br><br> Version 1: *nicht unterstützt*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Nicht unterstützt*                                                                                                                                                              | *Nicht unterstützt*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Noch nicht verfügbar*                                                                                                                                                          | *Noch nicht verfügbar*                                                                                                                             |
|                      | [Data Catalog](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Noch nicht verfügbar*                                                                                                                                                          | *Noch nicht verfügbar*                                                                                                                             |
|                      | [Logik-Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Noch nicht verfügbar*                                                                                                                                                          | *Noch nicht verfügbar*                                                                                                                             |
| IoT                  | [Event Hubs Capture](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Noch nicht verfügbar*                                                                                                                                                          | *Noch nicht verfügbar*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Noch nicht verfügbar*                                                                                                                                                          | *Noch nicht verfügbar*                                                                                                                             |
| Nutzung          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Noch nicht verfügbar*                                                                                                                                                          | *Noch nicht verfügbar*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Noch nicht verfügbar*                                                                                                                                                          | *Noch nicht verfügbar*                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Noch nicht verfügbar*                                                                                                                                                          | *Noch nicht verfügbar*                                                                                                                             |
| Produktivität         | [Azure-Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Nicht unterstützt*                                                                                                                                                              | Kontoverwaltung: *jetzt verfügbar* <br><br>Datenvorgänge: *-*  *noch nicht verfügbar*                                                                   |
|                      | [Data Lake-Tools für Visual Studio](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Noch nicht verfügbar*                                                                                                                                                          | *Noch nicht verfügbar*                                                                                                                             |
|                      | [Azure Storage-Explorer](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Jetzt verfügbar*                                                                                                                                                              | *Jetzt verfügbar*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Noch nicht verfügbar*                                                                                                                                                          | *Noch nicht verfügbar*                                                                                                                             |

### <a name="partner-ecosystem"></a>Ökosystem der Partnerunternehmen

Diese Tabelle zeigt eine Liste der Drittanbieterdienste und -Produkte, die so geändert wurden, dass sie mit Data Lake Storage Gen1 funktionieren. Zusätzlich wird dargestellt, welche Dienste und Produkte mit Data Lake Storage Gen2 kompatibel sind.

| Bereich            | Partner  | Produkt/Dienst  | Verfügbarkeit für Data Lake Storage Gen1                                                                                                                                               | Verfügbarkeit für Data Lake Storage Gen2: mit der Authentifizierung mit gemeinsam verwendetem Schlüssel                                                   | Verfügbarkeit für Data Lake Storage Gen2: mit OAuth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Analyseframework | Cloudera     | CDH                  | [Link](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Noch nicht verfügbar*                                                                                                  | [Link](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Link](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Nicht unterstützt*                                                                                                                  | *Noch nicht verfügbar*                                                                                                  |
|                     | Hortonworks  | HDP 3.0              | [Link](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | *Noch nicht verfügbar*                                                                                                  | *Noch nicht verfügbar*                                                                                                  |
|                     | Qubole       |                      | [Link](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Noch nicht verfügbar*                                                                                                  | *Noch nicht verfügbar*                                                                                                  |
| ETL                 | StreamSets   |                      | [Link](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Noch nicht verfügbar*                                                                                                  | *Noch nicht verfügbar*                                                                                                  |
|                     | Informatica  |                      | [Link](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Noch nicht verfügbar*                                                                                                  | *Noch nicht verfügbar*                                                                                                  |
|                     | Attunity     |                      | [Link](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Noch nicht verfügbar*                                                                                                  | *Noch nicht verfügbar*                                                                                                  |
|                     | Alteryx      |                      | [Link](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Noch nicht verfügbar*                                                                                                  | *Noch nicht verfügbar*                                                                                                  |
|                     | ImanisData   |                      | [Link](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Noch nicht verfügbar*                                                                                                  | *Noch nicht verfügbar*                                                                                                  |
|                     | WANdisco     |                      | [Link](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Link](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Link](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Informationen zum Vorgang

Data Lake Storage Gen1 überträgt bestimmte Informationen und Daten per Push an andere Dienste, was Ihnen beim Operationalisieren Ihrer Pipelines hilft. Diese Tabelle zeigt die Verfügbarkeit für entsprechende Unterstützung in Data Lake Storage Gen2.

| Datentyp                                                                                       | Verfügbarkeit für Data Lake Storage Gen1                                                                 | Verfügbarkeit für Data Lake Storage Gen2                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Abrechnungsdaten: Verbrauchseinheiten, die zunächst an das Commerce-Team für die Abrechnung gesendet und dann für Kunden verfügbar gemacht werden  | *Jetzt verfügbar*                                                                                             | *Jetzt verfügbar*                                                                                                                           |
| Aktivitätsprotokolle                                                                                          | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Einmalige Anforderungen für Protokolle für eine bestimmte Dauer über Supporttickets: *Jetzt verfügbar* Azure-Überwachungsintegration: *Noch nicht verfügbar* |
| Diagnoseprotokolle                                                                                        | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | Einmalige Anforderungen für Protokolle für eine bestimmte Dauer über Supporttickets: *Jetzt verfügbar* Azure-Überwachungsintegration: *Noch nicht verfügbar* |
| metrics                                                                                                | *Nicht unterstützt*                                                                                               | *Jetzt verfügbar:* [Link](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Planen eines Upgrades

In diesem Abschnitt wird davon ausgegangen, dass Sie den Abschnitt [Bewerten Ihrer Upgradebereitschaft](#assess-your-upgrade-readiness) in diesem Leitfaden gelesen haben und dass alle Ihre Abhängigkeiten erfüllt sind. Falls Funktionen vorhanden sind, die immer noch nicht in Data Lake Storage Gen2 verfügbar sind, fahren Sie nur fort, wenn Sie die entsprechenden Problemumgehungen kennen. In den folgenden Abschnitten erhalten Sie Unterstützung zur Planung Ihres Upgrades für Ihre Pipelines. Im Abschnitt [Durchführung des Upgrades](#performing-the-upgrade) in diesem Leitfaden wird beschrieben, wie Sie das Upgrade letztendlich durchführen.

### <a name="upgrade-strategy"></a>Upgradestrategie

Der wichtigste Teil des Upgrades ist die Entscheidung über die Strategie. Diese Entscheidung bestimmt, welche Möglichkeiten Ihnen zur Verfügung stehen.

In dieser Tabelle sind einige bekannte Strategien aufgeführt, die zum Migrieren von Datenbanken, Hadoop-Clustern usw. verwendet wurden. Wir übernehmen ähnliche Strategien in unserem Leitfaden und passen sie an unseren Kontext an.

| Strategie                   | Vorteile                                                                                  | Nachteile                                                           | Einsatzgebiete                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Per Lift & Shift migrieren                 | Die einfachste Lösung                                                                                 | Erfordert eine Unterbrechung zum Kopieren von Daten, Verschieben von Aufträgen sowie von eingehenden bzw. ausgehenden Datenverkehr                                             | Für einfachere Lösungen, bei denen nicht mehrere Lösungen auf dasselbe Gen1-Konto zugreifen und sie deshalb zusammen schnell und kontrolliert verschoben werden können.                                                  |
| Einmaliges Kopieren und inkrementelles Kopieren | Reduzierung von Downtime durch Ausführen von Kopiervorgängen im Hintergrund während das Quellsystem noch aktiv ist | Erfordert Downtime zum Verschieben von eingehenden und ausgehenden Datenverkehr                   | Die zu kopierende Datenmenge ist groß, und die Downtime, die mit Lift & Shift einher geht, ist nicht akzeptabel. Vor dem Übergang sind möglicherweise Tests mit wichtigen Produktionsdaten auf dem Zielsystem erforderlich. |
| Parallele Einführung              | Geringeste Downtime. Dadurch können Anwendungen nach eigenem Ermessen migrieren.           | Am umfangreichsten, da die bidirektionale Synchronisierung zwischen den beiden Systemen erforderlich ist | Für komplexe Szenarios, in denen auf Data Lake Storage Gen1 basierende Anwendungen nicht auf einmal umgestellt werden können sondern inkrementell umgestellt werden müssen                                                      |

Weiter unten finden Sie weitere Informationen zu den für jede Strategie erforderlichen Schritten. Diese erläutern, was Sie mit den im Upgrade beteiligten Komponenten tun. Dazu gehören das gesamte Quellsystem, das gesamte Zielsystem, Eingangsquellen für das Quellsystem, ausgehende Ziel für das Quellsystem sowie in jedem Quellsystem ausgeführte Aufträge.

Diese Schritte sind nicht verpflichtend. Sie sind eher dazu gedacht, den Rahmen für jede unserer Strategien festzulegen. Wir stellen Fallstudien für jede Strategie zu Verfügung, sobald diese implementiert werden.

#### <a name="lift-and-shift"></a>Per Lift & Shift migrieren

1. Halten Sie das Quellsystem an, also die Eingangsquellen, Aufträge und ausgehenden Ziele.
2. Kopieren Sie alle Daten aus dem Quellsystem in das Zielsystem.
3. Verweisen Sie alle Eingangsquellen auf das Zielsystem. Verweisen Sie vom Zielsystem auf das ausgehende Ziel.
4. Verschieben Sie alle Aufträge in das Zielsystem, ändern Sie sie, und führen Sie sie aus.
5. Deaktivieren Sie das Quellsystem.

#### <a name="copy-once-and-copy-incremental"></a>Einmaliges Kopieren und inkrementelles Kopieren

1. Kopieren Sie die Daten aus dem Quellsystem in das Zielsystem.
2. Kopieren Sie die inkrementellen Daten in regelmäßigen Abständen aus dem Quellsystem in das Zielsystem.
3. Verweisen Sie vom Zielsystem auf das ausgehende Ziel.
4. Verschieben Sie alle Aufträge im Zielsystem, ändern Sie sie, und führen Sie sie aus.
5. Verweisen Sie bei Bedarf Eingangsquellen inkrementell auf das Zielsystem.
6. Sobald alle Eingangsquellen auf das Zielsystem verweisen, führen Sie folgende Schritte aus:
    1. Deaktivieren Sie den inkrementellen Kopiervorgang.
    2. Deaktivieren Sie das Quellsystem.

#### <a name="parallel-adoption"></a>Parallele Einführung

1. Richten Sie das Zielsystem ein.
2. Richten Sie eine bidirektionale Replikation zwischen dem Quell- und dem Zielsystem ein.
3. Verweisen Sie Eingangsquellen inkrementell auf das Zielsystem.
4. Verschieben Sie Aufträge inkrementell auf das Zielsystem, ändern Sie sie, und führen Sie sie aus.
5. Verweisen Sie inkrementell vom Zielsystem auf ausgehende Ziele.
6. Sobald alle ursprünglichen Eingangsquellen, Aufträge und ausgehenden Ziele auf dem Zielsystem funktionieren, deaktivieren Sie das Quellsystem.

### <a name="data-upgrade"></a>Datenupgrade

Die allgemeine Strategie, die Sie zum Durchführen Ihres Upgrades nutzen (im Abschnitt [Upgradestrategie](#upgrade-strategy) in diesem Leitfaden näher beschrieben), bestimmt die Tools, die Sie für Ihr Datenupgrade verwenden können. Die unten aufgeführten Tools basieren auf den aktuellen Informationen und sind nur Vorschläge. 

#### <a name="tools-guidance"></a>Toolanweisungen

| Strategie                       | Tools                                                                                                             | Vorteile                                                                                                                             | Überlegungen                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Migration per Lift & Shift**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Verwalteter Clouddienst                                                                                                                | Kopiert nur Daten. ACLs können derzeit nicht kopiert werden.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Bekannte Toolberechtigungen, die von Hadoop bereitgestellt werden. ACLs können beispielsweise mit diesem Tool kopiert werden.                                                   | Erfordert ein Cluster, das gleichzeitig sowohl mit Data Lake Storage Gen1 als auch Gen2 eine Verbindung herstellen kann.                                                                                                                                                                                   |
| **Einmaliges Kopieren und inkrementelles Kopieren** | Azure Data Factory                                                                                                    | Verwalteter Clouddienst                                                                                                                | Daten müssen in einer Zeitreihe organisiert werden, um das inkrementelle Kopieren in ADF zu unterstützen. Das kürzeste Intervall zwischen inkrementellen Kopiervorgängen beträgt [15 Minuten](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). Bei kürzeren Intervallen funktioniert ADF nicht. ACLs können derzeit nicht kopiert werden. |
| **Parallele Einführung**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Die konsistente Replikation wird unterstützt. Wenn eine reine Hadoop-Umgebung verwendet wird, die mit Azure Data Lake Storage verbunden ist, wird die bidirektionale Replikation unterstützt. | Wenn Sie keine reine Hadoop-Umgebung verwenden, kann sich die Replikation verzögern.                                                                                                                                                                                                                                                  |

Beachten Sie, dass einige Drittanbieter das Upgrade von Data Lake Storage Gen1 auf Data Lake Storage Gen2 verarbeiten können, ohne dass die oben aufgeführten Tools zum Kopieren von Daten bzw. Metadaten erforderlich sind (Zum Beispiel [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). Diese Drittanbieter verfügen über eine zentrale Anlaufstelle, über die die Datenmigration sowie die Workloadmigration durchgeführt werden können. Möglicherweise müssen Sie ein Out-of-band-Upgrade für alle Tools durchführen, die sich außerhalb des Ökosystems befinden.

#### <a name="considerations"></a>Überlegungen

* Sie müssen zunächst das Data Lake Storage Gen2-Konto manuell erstellen, bevor Sie mit dem Upgrade beginnen können. Der Grund dafür ist, dass die aktuellen Anweisungen keinen automatischen Prozess für das Gen2-Konto auf Grundlage Ihrer Gen1-Kontoinformationen enthalten. Vergleichen Sie die Prozesse zur Kontenerstellung für [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) und [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account) miteinander.

* Data Lake Storage Gen2 unterstützt nur Dateien mit einer Größe von bis zu 5 TB. Sie müssen für ein Upgrade auf Data Lake Storage Gen2 möglicherweise die Dateien in Data Lake Storage Gen1 auf eine Größe von weniger als 5 TB verkleinern.

* Wenn Sie ein Tool verwenden, dass keine ACLs kopiert bzw. Sie die ACLs nicht kopieren möchten, müssen Sie die ACLs manuell auf das Ziel auf der entsprechenden obersten Ebene festlegen. Diesen Vorgang können Sie mit dem Storage-Explorer durchführen. Stellen Sie sicher, dass es sich bei diesen ACLs um die Standard-ACLs handelt, damit die Dateien und Ordner, die Sie kopieren, diese erben.

* In Data Lake Storage Gen1 ist die höchste Ebene für ACLs der Stamm des Kontos. In Data Lake Storage Gen2 ist die höchste Ebene, die Sie für ACLs festlegen können, jedoch der Stammordner in einem Dateisystem und nicht das gesamte Konto. Wenn Sie also Standard-ACLs auf Kontoebene festlegen möchten, müssen Sie diese auf allen Dateisystemen in Ihrem Data Lake Storage Gen2-Konto duplizieren.

* Einschränkungen bei der Dateibenennung unterscheiden sich zwischen den beiden Speichersystemen. Diese Unterschiede sind besonders wichtig, wenn Sie einen Kopiervorgang von Data Lake Storage Gen2 zu Data Lake Storage Gen1 durchführen, da Gen1 über mehr Einschränkungen verfügt.

### <a name="application-upgrade"></a>Anwendungsupgrade

Wenn Sie Anwendungen auf Data Lake Storage Gen1 oder Data Lake Storage Gen2 erstellen müssen, müssen Sie zunächst eine geeignete Programmierschnittstelle auswählen. Wenn Sie auf dieser Schnittstelle eine API aufrufen, müssen Sie den entsprechenden URI sowie die entsprechenden Anmeldeinformationen bereitstellen. Die Darstellungen dieser drei Elemente, also die API, der URI und wie diese Anmeldeinformationen bereitgestellt werden, unterscheiden sich zwischen Data Lake Storage Gen1 und Data Lake Storage Gen2, deshalb müssen Sie diese drei Konstrukte als Teil des Anwendungsupgrades entsprechend zuordnen.

#### <a name="uri-changes"></a>URI-Änderungen

Die Hauptaufgabe besteht hier darin, URIs mit dem Präfix `adl://` in URIs mit dem Präfix `abfss://` zu übersetzen.

Das URI-Schema für Data Lake Storage Gen1 wird [hier](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) genauer erläutert, aber im Allgemeinen ist es *adl://mydatalakestore.azuredatalakestore.net/\<file_path\>.*

Das URI-Schema für den Zugriff auf Data Lake Storage Gen2-Dateien wird [hier](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) genauer erläutert, im Allgemeinen ist es jedoch `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.widows.net/<PATH>`.

Sie müssen Ihre vorhandenen Anwendungen durchlaufen und sicherstellen, dass Sie die URIs entsprechend geändert haben, sodass auf die URIs von Data Lake Storage Gen2 verwiesen wird. Sie müssen ebenso die entsprechenden Anmeldeinformationen hinzufügen. Zuletzt ist die Art und Weise, wie Sie die ursprünglichen Anwendungen außer Betrieb nehmen und durch die neue Anwendung ersetzen, eng mit Ihrer allgemeinen Upgradestrategie verbunden.

#### <a name="custom-applications"></a>Benutzerdefinierte Anwendungen

Je nach Schnittstelle, die Ihre Anwendung mit Data Lake Storage Gen1 verwendet, müssen Sie diese ändern, um sie an Data Lake Storage Gen2 anzupassen.

##### <a name="rest-apis"></a>REST-APIs

Wenn Ihre Anwendung Data Lake Store-REST-APIs verwendet, müssen Sie Ihre Anwendung so verändern, dass sie nun Data Lake Storage Gen2-REST-APIs verwendet. Die Links stehen im Abschnitt *Programmierschnittstellen* zur Verfügung.

##### <a name="sdks"></a>SDKs

Wie im Abschnitt *Bewerten Ihrer Upgradebereitschaft* schon erwähnt, sind SDKs derzeit nicht verfügbar. Wenn Sie Ihre Anwendungen nach Data Lake Storage Gen2 portieren möchten, sollten Sie so lange damit warten, bis unterstützte SDKs verfügbar sind.

##### <a name="powershell"></a>PowerShell

PowerShell-Unterstützung ist derzeit für die Datenebene nicht verfügbar, wie schon unter „Bewerten Ihrer Upgradestrategie“ erwähnt.

Ersetzen Sie die Cmdlets der Verwaltungsebene mit den entsprechenden Cmdlets in Data Lake Storage Gen2. Die Links stehen im Abschnitt *Programmierschnittstellen* zur Verfügung.

##### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

Wie zuvor schon unter *Bewerten Ihrer Upgradebereitschaft* erläutert, ist die CLI-Unterstützung derzeit nicht für die Datenebene verfügbar.

Sie können Befehle der Verwaltungsebene durch die entsprechenden Befehle in Data Lake Storage Gen2 ersetzen. Die Links stehen im Abschnitt *Programmierschnittstellen* zur Verfügung.

### <a name="analytics-frameworks-upgrade"></a>Upgrade für das Analyseframework

Wenn Ihre Anwendung Metadaten zu Informationen im Speicher erstellt, wie etwa explizite Datei- und Ordnerpfade, müssen Sie zusätzliche Aktionen nach dem Upgrade der Speicher- bzw. Metadaten durchführen. Dies gilt insbesondere für Analyseframeworks wie Azure HDInsight, Databricks usw., die in der Regel Katalogdateien in den Speicherdaten erstellen.

Analyseframeworks arbeiten mit in Remotespeichern wie Data Lake Storage Gen1 und Gen2 gespeicherten Daten und Metadaten. Also theoretisch können die Engines kurzlebig sein und nur verwendet werden, wenn Aufträge für die gespeicherten Dateien ausgeführt werden müssen.

Die Analyseframeworks erstellen möglicherweise jedoch explizite Verweise auf die im Remotespeicher gespeicherten Dateien und Ordner, um die Leistung zu verbessern, und erstellen anschließend einen Cache, in dem diese gespeichert werden. Wenn sich der URI der Remotedaten ändert, z. B. wenn ein Cluster, der zuvor Daten in Data Lake Storage Gen1 gespeichert hatte, nun in Data Lake Storage Gen2 speichern möchte, ändert sich der URI für denselben kopierten Inhalt. Nachdem also ein Upgrade für die Daten und Metadaten durchgeführt wurde, müssen diese Engines ebenfalls aktualisiert oder erneut initialisiert werden.

Als Teil des Planungsprozesses müssen Sie Ihre Anwendung bestimmen und herausfinden, wie Metadateninformationen erneut initialisiert werden können, um auf Daten zu verweisen, die nun in Data Lake Storage Gen2 gespeichert sind. Unten finden Sie einen Leitfaden für häufig verwendete Analyseframeworks und den jeweiligen Upgradeschritten für diese.

#### <a name="azure-databricks"></a>Azure Databricks

Je nach Ihrer ausgewählten Upgradestrategie können die Schritte variieren. Im aktuellen Abschnitt wird davon ausgegangen, dass Sie die „Lift & Shift“-Strategie verwenden. Es wird ebenso erwartet, dass der vorhandene Databricks-Arbeitsbereich, der auf Daten in einem Data Lake Storage Gen1-Konto zugegriffen hat, mit den Daten funktioniert, die in das Data Lake Storage Gen2-Konto kopiert wurden.

Stellen Sie zunächst sicher, dass Sie das Gen2-Konto erstellt und dann die Daten und Metadaten von Gen1 über ein entsprechendes Tool herüberkopiert haben. Diese Tools werden im Abschnitt [Datenupgrade](#data-upgrade) in diesem Leitfaden beschrieben.

Führen Sie anschließend ein [Upgrade](https://docs.azuredatabricks.net/user-guide/clusters/index.html) für Ihren vorhandenen Databricks-Cluster aus, sodass dieser nun die Komponente Databricks Runtime 5.1 oder höher verwendet, die Data Lake Storage Gen2 in der Regel unterstützt.

Die nachfolgenden Schritte hängen davon ab, wie der vorhandene Databricks-Arbeitsbereich auf Daten im Data Lake Storage Gen1-Konto zugreift. Dies kann über die adl://-URIs [direkt](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) über Notebooks oder über [Bereitstellungspunkte](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs) erfolgen.

Wenn Sie direkt über Notebooks zugreifen, indem Sie die gesamten adl://-URIs angeben, müssen Sie jedes Notebook durchlaufen und die Konfiguration ändern, um auf den entsprechenden Data Lake Storage Gen2-URI zuzugreifen.

Des Weiteren müssen Sie das Notebook erneut konfigurieren, sodass es auf das Data Lake Storage Gen2-Konto verweist. Es sind keine weiteren Änderungen notwendig. Die Notebooks sollten so funktionieren, wie zuvor auch.

Wenn Sie eine andere Upgradestrategie nutzen, können Sie eine Mischung der oben genannten Schritte verwenden, um Ihren Anforderungen gerecht zu werden.

### <a name="azure-ecosystem-upgrade"></a>Upgrade des Azure-Ökosystems

Alle Tools und Dienste, die im Abschnitt [Azure-Ökosystem](#azure-ecosystem) dieses Leitfadens erläutert werden, müssen konfiguriert werden, um mit Data Lake Storage Gen2 zu funktionieren.

Stellen Sie zuerst sicher, dass in Data Lake Storage Gen2 eine Integration verfügbar ist.

Danach müssen die oben genannten Elemente, z. B. der URI und die Anmeldeinformationen, geändert werden. Sie können die vorhandene Instanz ändern, die mit Data Lake Storage Gen1 funktioniert. Alternativ können Sie eine neue Instanz erstellen, die mit Data Lake Storage Gen2 arbeitet.

### <a name="partner-ecosystem-upgrade"></a>Upgrade für Ökosystem aus Partnerunternehmen

Arbeiten Sie mit dem Partnerunternehmen zusammen, das die entsprechenden Komponenten und Tools bereitstellt, um sicherzustellen, dass dieses Unternehmen mit Data Lake Storage Gen2 arbeiten kann. 

## <a name="performing-the-upgrade"></a>Durchführen des Upgrades

### <a name="pre-upgrade"></a>Vor dem Upgrade

Als Teil des Upgrades haben Sie die Abschnitte *Bewerten Ihrer Upgradebereitschaft* sowie [Planen Ihres Upgrades](#planning-for-an-upgrade) durchlaufen und alle nötigen Informationen erhalten sowie einen Plan erstellt, der Ihre Anforderungen erfüllt. Während dieser Phase haben Sie wahrscheinlich eine Testaufgabe durchgeführt.

### <a name="in-upgrade"></a>Während des Upgrades

Je nach Ihrer ausgewählten Strategie und der Komplexität Ihrer Lösung könnte diese Phase kurz oder lang ausfallen. Wenn sie lang ist, müssen mehrere Workloads inkrementell zu Data Lake Storage Gen2 verschoben werden. Dies ist der wichtigste Teil Ihres Upgrades.

### <a name="post-upgrade"></a>Nach dem Upgrade

Nachdem Sie den Übergangsvorgang abgeschlossen haben, bestehen die letzten Schritte aus der ausführlichen Überprüfung. Diese besteht darin, dass überprüft wird, ob Daten zuverlässig kopiert wurden (ist jedoch nicht darauf beschränkt), ob ACLs ordnungsgemäß festgelegt wurden, ob End-to-End-Pipelines korrekt funktionieren usw. Nachdem die Überprüfungen abgeschlossen wurden, können Sie nun Ihre alten Pipelines deaktivieren, Ihre Data Lake Storage Gen1-Konten löschen und sofort mit Ihren auf Data Lake Storage Gen2 basierten Lösung starten.

## <a name="conclusion"></a>Zusammenfassung

Der in diesem Dokument bereitgestellte Leitfaden sollte Sie beim Upgrade Ihrer Lösung zur Verwendung von Data Lake Storage Gen2 unterstützen. 

Wenn Sie weitere Fragen oder Feedback für uns haben, schreiben Sie unten einen Kommentar, oder geben Sie Ihr Feedback im [Azure-Feedbackforum](https://feedback.azure.com/forums/327234-data-lake).
