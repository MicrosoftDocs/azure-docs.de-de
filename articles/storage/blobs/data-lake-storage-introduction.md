---
title: Azure Data Lake Storage Gen2 – Einführung
description: Lesen Sie hier eine Einführung in Azure Data Lake Storage Gen2. Sie erhalten Informationen zu den wichtigsten Features. Sehen Sie sich die unterstützten Blob Storage-Features, Azure-Dienstintegrationen und Plattformen an.
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 5f2f3cfc5ccbdd6a3d3d3ede5bb39a3f6f548b19
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913095"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Einführung in Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 setzt auf [Azure Blob Storage](storage-blobs-introduction.md) auf und bietet eine Reihe von Funktionen für die Big Data-Analyse. 

Data Lake Storage Gen2 vereint die Funktionen von [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml) und Azure Blob Storage. Beispielsweise bietet Data Lake Storage Gen2 Dateisystemsemantik, Sicherheit auf Dateiebene und Skalierung. Da diese Funktionen auf Blob Storage basieren, profitieren Sie gleichzeitig von kostengünstigem, mehrstufigem Speicher mit Hochverfügbarkeit und Notfallwiederherstellungsfunktionen.

## <a name="designed-for-enterprise-big-data-analytics"></a>Für Big Data-Analysen in Großunternehmen entwickelt

Mit Data Lake Storage Gen2 wird Azure Storage zur Grundlage für das Erstellen von Enterprise Data Lakes in Azure. Data Lake Storage Gen2 wurde eigens für die Verarbeitung mehrerer Petabyte an Informationen bei gleichzeitiger Unterstützung eines Durchsatzes von Hunderten von Gigabit konzipiert und bietet Ihnen eine einfache Möglichkeit, riesige Datenmengen zu verwalten.

Ein wesentlicher Bestandteil von Data Lake Storage Gen2 ist das Hinzufügen eines [hierarchischen Namespace](data-lake-storage-namespace.md) zum Blobspeicher. Der hierarchische Namespace organisiert Objekte/Dateien in einer Hierarchie von Verzeichnissen für den effizienten Datenzugriff. Eine allgemeine Namenskonvention für die Objektspeicherung verwendet Schrägstriche im Namen, um eine hierarchische Verzeichnisstruktur nachzuahmen. Mit Data Lake Storage Gen2 wird diese Struktur Wirklichkeit. Vorgänge wie das Umbenennen oder Löschen eines Verzeichnisses werden zu einzelnen atomaren Metadatenvorgängen im Verzeichnis. Es müssen nicht alle Objekte aufgezählt und verarbeitet werden, die das Namenspräfix des Verzeichnisses gemeinsam haben.

Data Lake Storage Gen2 baut auf Blobspeicher auf und verbessert wie folgt die Leistung, Verwaltung und Sicherheit:

-   Die **Leistung** ist optimiert, da Sie keine Daten als Voraussetzung für die Analyse kopieren oder transformieren müssen. Im Vergleich zum flachen Namespace im Blobspeicher verbessert der hierarchische Namespace die Leistung von Verzeichnisverwaltungsvorgängen erheblich, wodurch die gesamte Auftragsverarbeitungsleistung gesteigert wird.

-   Die **Verwaltung** ist einfacher, weil Sie Dateien mithilfe von Verzeichnissen und Unterverzeichnissen organisieren und bearbeiten können.

-   Die **Sicherheit** ist durchsetzbar, da Sie POSIX-Berechtigungen für Verzeichnisse oder einzelne Dateien definieren können.

Data Lake Storage Gen2 ist darüber hinaus sehr kostengünstig, da diese SKU auf dem kostengünstigen [Azure Blob-Speicher](storage-blobs-introduction.md) aufbaut. Durch diese zusätzlichen Features werden die Gesamtbetriebskosten für Big Data-Analysen in Azure weiter gesenkt.

## <a name="key-features-of-data-lake-storage-gen2"></a>Besondere Features von Data Lake Storage Gen2

-   **Hadoop-kompatibler Zugriff:** Data Lake Storage Gen2 ermöglicht einen mit einem [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) vergleichbaren Zugriff auf Daten sowie deren Verwaltung. Der neue [ABFS-Treiber](data-lake-storage-abfs-driver.md) (für den Datenzugriff) ist in allen Apache Hadoop-Umgebungen verfügbar. Zu diesen Umgebungen zählen unter anderem [Azure HDInsight](../../hdinsight/index.yml) *,* [Azure Databricks](/azure/databricks/) und [Azure Synapse Analytics](/azure/synapse-analytics).

-   **Obermenge von POSIX-Berechtigungen:** Das Sicherheitsmodell für Data Lake Gen2 unterstützt Zugriffssteuerungslisten und POSIX-Berechtigungen sowie zusätzliche Granularität speziell für Data Lake Storage Gen2. Die Einstellungen können über den Storage-Explorer oder Frameworks wie Hive und Spark konfiguriert werden.

-   **Kosteneffizienz:** Data Lake Storage Gen2 bietet kostengünstige Speicherkapazität und -transaktionen. Features wie [Azure Blob Storage-Lebenszyklus](storage-lifecycle-management-concepts.md) tragen während des Lebenszyklus der Daten zur Kostenoptimierung bei.

-   **Optimierter Treiber:** Der ABFS-Treiber ist speziell für Big Data-Analysen [optimiert](data-lake-storage-abfs-driver.md). Die entsprechenden REST-APIs werden über den Endpunkt `dfs.core.windows.net` bereitgestellt.

### <a name="scalability"></a>Skalierbarkeit

Azure Storage ist unabhängig davon skalierbar, ob der Zugriff über Data Lake Storage Gen2- oder Blob Storage-Schnittstellen erfolgt. Die Lösung ist in der Lage, *viele Exabyte an Daten* zu speichern und zu verwalten. Diese Speichermenge steht mit einem Durchsatz zur Verfügung, der in Gigabit pro Sekunde (GBit/s) bei hohen Graden von Ein-/Ausgabevorgängen pro Sekunde (IOPS) gemessen wird. Die Verarbeitung erfolgt mit nahezu konstanten Wartezeiten pro Anforderung, die auf Dienst-, Konten- und Dateiebene gemessen werden.

### <a name="cost-effectiveness"></a>Kosteneffizienz

Da Data Lake Storage Gen2 auf Azure Blob Storage basiert, sind die Kosten für Speicherkapazität und Transaktionen geringer. Anders als bei anderen Cloudspeicherdiensten müssen Ihre Daten vor der Analyse nicht verschoben oder transformiert werden. Weitere Informationen zu den Preisen finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage).

Zusätzlich verbessern Features wie der [hierarchische Namespace](data-lake-storage-namespace.md) die Gesamtleistung vieler Analyseaufträge erheblich. Diese Leistungsverbesserung bedeutet, dass Sie weniger Rechenleistung benötigen, um die gleiche Datenmenge zu verarbeiten, was zu niedrigeren Gesamtbetriebskosten für den gesamten Analyseauftrag führt.

### <a name="one-service-multiple-concepts"></a>Ein Dienst, mehrere Konzepte

Da Data Lake Storage Gen2 auf Azure Blob Storage basiert, können mehrere Konzepte die gleichen gemeinsamen Aspekte beschreiben.

Die folgenden Begriffe stellen äquivalente Entitäten dar, wie sie durch verschiedene Konzepte beschrieben werden. Sofern nicht anders angegeben, sind diese Einheiten direkte Synonyme:

| Konzept                                | Organisation auf oberster Ebene | Organisation auf niedriger Ebene                                            | Datencontainer |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobs – universeller Objektspeicher | Container              | Virtuelles Verzeichnis (nur SDK, stellt keinen atomarische Bearbeitung bereit) | Blob           |
| Azure Data Lake Storage Gen2 – Analytics Storage          | Container            | Verzeichnis                                                           | Datei           |

## <a name="supported-blob-storage-features"></a>Unterstützte Blob Storage-Features

Für Ihr Konto stehen Blob Storage-Features wie [Diagnoseprotokollierung](../common/storage-analytics-logging.md), [Zugriffsebenen](storage-blob-storage-tiers.md) und [Richtlinien für die Blob Storage-Lebenszyklusverwaltung](storage-lifecycle-management-concepts.md) zur Verfügung. 

Eine Liste der unterstützen Blob Storage-Features finden Sie unter [Unterstützte Blob Storage-Features in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Unterstützte Azure-Dienstintegrationen

Von Azure Data Lake Storage Gen2 werden verschiedene Azure-Dienste unterstützt. Mit diesen Diensten können Sie Daten erfassen, Analysen durchführen und visuelle Darstellungen erstellen. Eine Liste der unterstützten Azure-Dienste finden Sie unter [Azure-Dienste, die Azure Data Lake Storage Gen2 unterstützen](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Unterstützte Open-Source-Plattformen

Mehrere Open-Source-Plattformen unterstützen Data Lake Storage Gen2. Eine vollständige Liste finden Sie unter [Open-Source-Plattformen, die Azure Data Lake Storage Gen2 unterstützen](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Weitere Informationen

- [Bekannte Probleme mit Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Multiprotokollzugriff für Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)