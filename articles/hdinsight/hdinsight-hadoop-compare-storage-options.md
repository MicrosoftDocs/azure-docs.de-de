---
title: Vergleich der Speicheroptionen für die Verwendung mit Azure HDInsight-Clustern
description: Dieser Artikel bietet einen Überblick über die Speichertypen und deren Funktionsweise mit Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 34eeeed2b3c44336cd4aa1219d54b1811c6988f5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952317"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Vergleich der Speicheroptionen für die Verwendung mit Azure HDInsight-Clustern

Sie haben beim Erstellen von HDInsight-Clustern die Auswahl zwischen verschiedenen Azure Storage-Diensten:

* [Azure Blob Storage mit HDInsight](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2 mit HDInsight](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen1 mit HDInsight](./overview-data-lake-storage-gen1.md)

Dieser Artikel bietet eine Übersicht über diese Speichertypen und ihre besonderen Merkmale.

## <a name="storage-types-and-features"></a>Speichertypen und Features

Die folgende Tabelle enthält die Azure Storage-Dienste, die mit den unterschiedlichen Versionen von HDInsight unterstützt werden:

| Speicherdienst | Kontotyp | Namespacetyp | Unterstützte Dienste | Unterstützte Leistungsstufen | Unterstützte Zugriffsebenen | HDInsight-Version | Clustertyp |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Allgemein v2 | Hierarchisch (Dateisystem) | Blob | Standard | Heiß, Kalt, Archiv | 3.6 und höher | Alle außer Spark 2.1 und 2.2|
|Azure Storage| Allgemein v2 | Object | Blob | Standard | Heiß, Kalt, Archiv | 3.6 und höher | All |
|Azure Storage| Allgemein v1 | Object | Blob | Standard | – | All | All |
|Azure Storage| Blob Storage** | Object | Blockblob | Standard | Heiß, Kalt, Archiv | All | All |
|Azure Data Lake Storage Gen1| – | Hierarchisch (Dateisystem) | – | – | – | Nur 3.6 | Alle mit Ausnahme von HBase |
|Azure Storage| Blockblob| Object | Blockblob | Premium | –| 3.6 und höher | Nur HBase mit beschleunigten Schreibvorgängen|
|Azure Data Lake Storage Gen2| Blockblob| Hierarchisch (Dateisystem) | Blockblob | Premium | –| 3.6 und höher | Nur HBase mit beschleunigten Schreibvorgängen|

**Für HDInsight-Cluster können nur sekundäre Speicherkonten den Typ „BlobStorage“ aufweisen, und der Seitenblob ist keine unterstützte Speicheroption.

Weitere Informationen zu Azure Storage-Kontotypen finden Sie unter [Azure-Speicherkonten – Übersicht](../storage/common/storage-account-overview.md).

Weitere Informationen zu Azure Storage-Zugriffsebenen finden Sie unter [Azure Blob Storage: Speicherebenen „Premium“ (Vorschauversion), „Heiß“, „Kalt“ und „Archiv“](../storage/blobs/storage-blob-storage-tiers.md).

Sie können einen Cluster mit Kombinationen von Diensten für den primären und optional für den sekundären Speicher erstellen. Die folgende Tabelle enthält die Clusterspeicherkonfigurationen, die derzeit in HDInsight unterstützt werden:

| HDInsight-Version | Primärer Speicher | Sekundärer Speicher | Unterstützt |
|---|---|---|---|
| 3.6 und 4.0 | Universell V1, Universell V2 | Universell V1, Universell V2, BlobStorage (Blockblobs) | Ja |
| 3.6 und 4.0 | Universell V1, Universell V2 | Data Lake Storage Gen2 | Nein |
| 3.6 und 4.0 | Data Lake Storage Gen2* | Data Lake Storage Gen2 | Ja |
| 3.6 und 4.0 | Data Lake Storage Gen2* | Universell V1, Universell V2, BlobStorage (Blockblobs) | Ja |
| 3.6 und 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Nein |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Ja |
| 3.6 | Data Lake Storage Gen1 | Universell V1, Universell V2, BlobStorage (Blockblobs) | Ja |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Nein |
| 4,0 | Data Lake Storage Gen1 | Any | Nein |
| 4,0 | Universell V1, Universell V2 | Data Lake Storage Gen1 | Nein |

*Hierbei kann es sich um ein oder mehrere Data Lake Storage Gen2-Konten handeln, sofern alle für die Verwendung derselben verwalteten Identität für den Clusterzugriff eingerichtet wurden.

> [!NOTE]
> Primärer Data Lake Storage Gen2-Speicher wird für Spark 2.1- und 2.2-Cluster nicht unterstützt.

## <a name="data-replication"></a>Datenreplikation

Azure HDInsight speichert keine Kundendaten. Das primäre Speichermedium für einen Cluster sind die zugeordneten Speicherkonten. Sie können Ihren Cluster an ein vorhandenes Speicherkonto anfügen oder bei der Clustererstellung ein neues Speicherkonto erstellen. Wenn Sie ein neues Konto erstellen, wird es als LRS-Konto (lokal redundanter Speicher) erstellt, das neben den Anforderungen an die Datenresidenz in der Region auch die im [Trust Center](https://azuredatacentermap.azurewebsites.net) definierten Anforderungen erfüllt.

Sie können überprüfen, ob HDInsight ordnungsgemäß für die Speicherung von Daten in einer einzelnen Region konfiguriert ist, indem Sie sicherstellen, dass das mit HDInsight verknüpfte Speicherkonto ein LRS-Konto ist oder einer anderen im [Trust Center](https://azuredatacentermap.azurewebsites.net) definierten Speicheroption entspricht.
 
## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über Azure Storage in HDInsight](./overview-azure-storage.md)
* [Übersicht über Azure Data Lake Storage Gen1 in HDInsight](./overview-data-lake-storage-gen1.md)
* [Übersicht über Azure Data Lake Storage Gen2 in HDInsight](./overview-data-lake-storage-gen2.md)
* [Einführung in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Einführung in Azure Storage](../storage/common/storage-introduction.md)
