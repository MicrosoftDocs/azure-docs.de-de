---
title: Azure Data Lake Storage Gen2 – Einführung
description: Bietet eine Übersicht zu Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: daa5efcf1be075e81e402edff56239a7cf21db18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939225"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Einführung in Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 setzt auf [Azure Blob Storage](storage-blobs-introduction.md) auf und bietet eine Reihe von Funktionen für die Big Data-Analyse. Azure Data Lake Storage Gen2 führt die Funktionen unserer beiden vorhandenen Speicherdienste Azure Blob Storage und Azure Data Lake Storage Gen1 zusammen. Features von [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index) (z.B. Dateisystemsemantik, Sicherheit auf Verzeichnis- und Dateiebene und Skalierbarkeit) werden mit den kostengünstigen, mehrstufigen Speicherlösungen, Hochverfügbarkeits- und Notfallwiederherstellungsfunktionen von [Azure Blob Storage](storage-blobs-introduction.md) kombiniert.

## <a name="designed-for-enterprise-big-data-analytics"></a>Für Big Data-Analysen in Großunternehmen entwickelt

Mit Data Lake Storage Gen2 wird Azure Storage zur Grundlage für das Erstellen von Enterprise Data Lakes in Azure. Data Lake Storage Gen2 wurde eigens für die Verarbeitung mehrerer Petabyte an Informationen bei gleichzeitiger Unterstützung eines Durchsatzes von Hunderten von Gigabit konzipiert und bietet Ihnen eine einfache Möglichkeit, riesige Datenmengen zu verwalten.

Ein wesentlicher Bestandteil von Data Lake Storage Gen2 ist das Hinzufügen eines [hierarchischen Namespace](data-lake-storage-namespace.md) zum Blobspeicher. Der hierarchische Namespace organisiert Objekte/Dateien in einer Hierarchie von Verzeichnissen für den effizienten Datenzugriff. Eine allgemeine Namenskonvention für die Objektspeicherung verwendet Schrägstriche im Namen, um eine hierarchische Verzeichnisstruktur nachzuahmen. Mit Data Lake Storage Gen2 wird diese Struktur Wirklichkeit. Vorgänge wie das Umbenennen oder Löschen eines Verzeichnisses werden zu einzelnen atomaren Metadatenvorgängen im Verzeichnis, anstatt alle Objekte aufzuzählen und zu verarbeiten, die das Namenspräfix des Verzeichnisses gemeinsam haben.

In der Vergangenheit mussten bei cloudbasierten Analysen Kompromisse in puncto Leistung, Verwaltung und Sicherheit eingegangen werden. Data Lake Storage Gen2 geht jeden dieser Aspekte auf folgende Weise an:

-   Die **Leistung** ist optimiert, da Sie keine Daten als Voraussetzung für die Analyse kopieren oder transformieren müssen. Der hierarchische Namespace verbessert die Leistung von Verzeichnisverwaltungsvorgängen erheblich, wodurch die gesamte Auftragsverarbeitungsleistung gesteigert wird.

-   Die **Verwaltung** ist einfacher, weil Sie Dateien mithilfe von Verzeichnissen und Unterverzeichnissen organisieren und bearbeiten können.

-   Die **Sicherheit** ist durchsetzbar, da Sie POSIX-Berechtigungen für Verzeichnisse oder einzelne Dateien definieren können.

-   **Kosteneffizienz** wird ermöglicht, da Data Lake Storage Gen2 auf kostengünstigem [Azure Blob Storage](storage-blobs-introduction.md) aufsetzt. Durch diese zusätzlichen Features werden die Gesamtbetriebskosten für Big Data-Analysen in Azure weiter gesenkt.

## <a name="key-features-of-data-lake-storage-gen2"></a>Besondere Features von Data Lake Storage Gen2

-   **Hadoop-kompatibler Zugriff**: Data Lake Storage Gen2 ermöglicht einen mit einem [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) vergleichbaren Zugriff auf Daten sowie deren Verwaltung. Der neue [ABFS](data-lake-storage-abfs-driver.md)-Treiber ist in allen Apache Hadoop-Umgebungen verfügbar, einschließlich [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index) und [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/), um auf die in Data Lake Storage Gen2 gespeicherten Daten zuzugreifen.

-   **Obermenge von POSIX-Berechtigungen**: Das Sicherheitsmodell für Data Lake Gen2 unterstützt Zugriffssteuerungslisten und POSIX-Berechtigungen sowie zusätzliche Granularität speziell für Data Lake Storage Gen2. Die Einstellungen können über den Storage-Explorer oder Frameworks wie Hive und Spark konfiguriert werden.

-   **Kosteneffizienz**: Data Lake Storage Gen2 bietet kostengünstige Speicherkapazität und -transaktionen. Während Daten ihren gesamten Lebenszyklus durchlaufen, ändern sich Abrechnungstarife, wodurch die Kosten durch integrierte Funktionen wie [Azure Blob Storage-Lebenszyklus](storage-lifecycle-management-concepts.md) auf ein Minimum reduziert werden.

-   **Optimierter Treiber:** Der ABFS-Treiber ist speziell für Big Data-Analysen [optimiert](data-lake-storage-abfs-driver.md). Die entsprechenden REST-APIs werden über den Endpunkt `dfs.core.windows.net` bereitgestellt.

### <a name="scalability"></a>Skalierbarkeit

Azure Storage ist unabhängig davon skalierbar, ob der Zugriff über Data Lake Storage Gen2- oder Blob Storage-Schnittstellen erfolgt. Die Lösung ist in der Lage, *viele Exabyte an Daten* zu speichern und zu verwalten. Diese Speichermenge steht mit einem Durchsatz zur Verfügung, der in Gigabit pro Sekunde (GBit/s) bei hohen Graden von Ein-/Ausgabevorgängen pro Sekunde (IOPS) gemessen wird. Über die reine dauerhafte Speicherung hinaus erfolgt die Verarbeitung mit nahezu konstanten Wartezeiten pro Anforderung, die auf Dienst-, Konten- und Dateiebene gemessen werden.

### <a name="cost-effectiveness"></a>Kosteneffizienz

Einer der vielen Vorteile davon, dass Data Lake Storage Gen2 auf Azure Blob Storage aufsetzt, sind die niedrigen Kosten für Speicherkapazität und -transaktionen. Im Gegensatz zu anderen Cloudspeicherdiensten müssen in Data Lake Storage Gen2 gespeicherte Daten vor der Analyse nicht verschoben oder transformiert werden. Weitere Informationen zu den Preisen finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage).

Zusätzlich verbessern Features wie der [hierarchische Namespace](data-lake-storage-namespace.md) die Gesamtleistung vieler Analyseaufträge erheblich. Diese Leistungsverbesserung bedeutet, dass Sie weniger Rechenleistung benötigen, um die gleiche Datenmenge zu verarbeiten, was zu niedrigeren Gesamtbetriebskosten für den gesamten Analyseauftrag führt.

### <a name="one-service-multiple-concepts"></a>Ein Dienst, mehrere Konzepte

Data Lake Storage Gen2 bietet eine zusätzliche Möglichkeit für Big Data-Analysen, da die Lösung auf Azure Blob Storage aufbaut. Während es viele Vorteile bei der Nutzung vorhandener Plattformkomponenten von Blobs zur Erstellung und zum Betrieb von Data Lakes für Analysen gibt, führt dies zu mehreren Konzepten, die die gleichen, gemeinsamen Aspekte beschreiben.

Die folgenden Begriffe stellen äquivalente Entitäten dar, wie sie durch verschiedene Konzepte beschrieben werden. Sofern nicht anders angegeben, sind diese Einheiten direkte Synonyme:

| Konzept                                | Organisation auf oberster Ebene | Organisation auf niedriger Ebene                                            | Datencontainer |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobs – universeller Objektspeicher | Container              | Virtuelles Verzeichnis (nur SDK, stellt keinen atomarische Bearbeitung bereit) | Blob           |
| ADLS Gen2 – Analysespeicher          | Dateisystem             | Verzeichnis                                                           | Datei           |

## <a name="supported-open-source-platforms"></a>Unterstützte Open-Source-Plattformen

Mehrere Open-Source-Plattformen unterstützen Data Lake Storage Gen2. Diese Plattformen werden in der folgenden Tabelle aufgeführt.

> [!NOTE]
> Nur die Versionen, die in dieser Tabelle genannt werden, werden unterstützt.

| Plattform |  Unterstützte Version(en) | Weitere Informationen |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6 und höher | [Welche Apache Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 3.2 und höher | [Apache Hadoop-Releasearchiv](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1 und höher | [Cloudera Enterprise 6.x, Anmerkungen zu dieser Version](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 5.1 und höher | [Databricks Runtime-Versionen](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 3.1.x und höher | [Configuring Cloud Data Access](https://docs.hortonworks.com/HDPDocuments/Cloudbreak/Cloudbreak-2.9.0/cloud-data-access/content/cb_configuring-access-to-adls2.html) (Konfigurieren des Zugriffs auf Clouddaten) |

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln werden einige der wichtigsten Konzepte von Data Lake Storage Gen2 beschrieben. Zudem wird erläutert, wie Sie Ihre Daten speichern, darauf zugreifen, sie verwalten und Einsichten daraus gewinnen können:

-   [Hierarchischer Namespace](data-lake-storage-namespace.md)
-   [Erstellen eines Speicherkontos](data-lake-storage-quickstart-create-account.md)
-   [Verwenden eines Data Lake Storage Gen2-Kontos in Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md)
