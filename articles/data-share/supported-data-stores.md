---
title: Unterstützte Datenspeicher in Azure Data Share
description: Erfahren Sie mehr über die Datenspeicher, die zur Verwendung in Azure Data Share unterstützt werden.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 12/7/2020
ms.openlocfilehash: 566fd2c9c31933420769f7200a0434cc53f8c2f3
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853140"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Unterstützte Datenspeicher in Azure Data Share

Azure Data Share bietet eine offene und flexible Datenfreigabe, einschließlich der Möglichkeit zur Freigabe aus und in unterschiedliche Datenspeicher. Datenanbieter können Daten aus einem Datenspeichertyp freigeben, und ihre Datenconsumer können den Datenspeicher auswählen, in dem Daten empfangen werden sollen. 

In diesem Artikel erfahren Sie mehr über die umfangreiche Gruppe von Azure-Datenspeichern, die in Azure Data Share unterstützt werden. Außerdem erhalten Sie Informationen zu den Kombinationen von Datenspeichern, die von Datenanbietern und Datenconsumern genutzt werden können. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Welche Datenspeicher werden in Azure Data Share unterstützt? 

In der folgenden Tabelle sind die unterstützten Datenquellen für Azure Data Share aufgeführt. 

| Datenspeicher | Momentaufnahmebasierte Freigabe (vollständige Momentaufnahme) | Momentaufnahmebasierte Freigabe (inkrementelle Momentaufnahme) | Direkte Freigabe 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Azure SQL-Datenbank |✓ | | |
| Azure Synapse Analytics (ehemals Azure SQL DW) |✓ | | |
| Dedizierter SQL-Pool von Azure Synapse Analytics (Arbeitsbereich) |✓ | | |
| Azure-Daten-Explorer | | |✓ |

## <a name="data-store-support-matrix"></a>Datenspeicher-Unterstützungsmatrix

Azure Data Share bietet Datenconsumern Flexibilität bei der Entscheidung für einen Datenspeicher, in dem Daten empfangen werden. Beispielsweise können Daten, die von Azure SQL-Datenbank freigegeben werden, in Azure Data Lake Store Gen2, Azure SQL-Datenbank oder Azure Synapse Analytics empfangen werden. Kunden können beim Konfigurieren einer empfangenen Datenfreigabe das Format auswählen, in dem Daten empfangen werden sollen. 

In der folgenden Tabelle sind die verschiedenen Kombinationen und Auswahlmöglichkeiten aufgeführt, die Datenconsumern beim Akzeptieren und Konfigurieren ihrer Datenfreigabe zur Verfügung stehen. Weitere Informationen zum Konfigurieren von Datasetzuordnungen finden Sie unter [Konfigurieren von Datasetzuordnungen](how-to-configure-mapping.md).

| Datenspeicher | Azure Blob Storage | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL-Datenbank | Azure Synapse Analytics (ehemals Azure SQL DW) | Dedizierter SQL-Pool von Azure Synapse Analytics (Arbeitsbereich) | Azure-Daten-Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Azure Blob Storage | ✓ || ✓ |||
| Azure Data Lake Storage Gen1 | ✓ | | ✓ |||
| Azure Data Lake Storage Gen2 | ✓ | | ✓ |||
| Azure SQL-Datenbank | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Azure Synapse Analytics (ehemals Azure SQL DW) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Dedizierter SQL-Pool von Azure Synapse Analytics (Arbeitsbereich) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Azure-Daten-Explorer ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Freigeben über ein Speicherkonto
Azure Data Share unterstützt die Freigabe von Dateien, Ordnern und Dateisystemen über Azure Data Lake Gen1 und Azure Data Lake Gen2. Außerdem unterstützt es die Freigabe von Blobs, Ordnern und Containern über Azure Blob Storage. Derzeit wird nur das Blockblob unterstützt. Wenn Dateisysteme, Container oder Ordner in einer auf Momentaufnahmen basierenden Freigabe freigegeben werden, kann der Datenconsumer wahlweise eine vollständige Kopie der Freigabedaten erstellen oder die Funktion der inkrementellen Momentaufnahme nutzen, um nur neue oder aktualisierte Dateien zu kopieren. Die inkrementelle Momentaufnahme basiert auf dem Zeitpunkt der letzten Änderung der Dateien. Vorhandene Dateien mit demselben Namen werden überschrieben.

Ausführliche Informationen finden Sie unter [Freigeben und Empfangen von Daten aus Azure Blob Storage und Azure Data Lake Storage](how-to-share-from-storage.md).

## <a name="share-from-a-sql-based-source"></a>Freigeben über eine SQL-basierte Quelle
Azure Data Share unterstützt die Freigabe sowohl von Tabellen als auch Sichten aus Azure SQL-Datenbank und Azure Synapse Analytics (ehemals Azure SQL DW) sowie die Freigabe von Tabellen aus einem dedizierten SQL-Pool von Azure Synapse Analytics (Arbeitsbereich). Die Freigabe aus einem serverlosen SQL-Pool von Azure Synapse Analytics (Arbeitsbereich) wird derzeit nicht unterstützt. Datenconsumer können die Daten in Azure Data Lake Storage Gen2 und Azure Blob Storage als CSV- oder Parquet-Datei, in Azure SQL-Datenbank und Azure Synapse Analytics als Tabellen akzeptieren.

Wenn Sie Daten in Azure Data Lake Store Gen2 oder Azure Blob Storage akzeptieren, überschreiben vollständige Momentaufnahmen den Inhalt der Zieldatei (sofern bereits vorhanden).
Wenn Daten in einer Tabelle empfangen werden und die Zieltabelle noch nicht vorhanden ist, erstellt Azure Data Share die SQL-Tabelle mit dem Quellschema. Wenn bereits eine Zieltabelle mit demselben Namen vorhanden ist, wird sie gelöscht und mit der letzten vollständigen Momentaufnahme überschrieben. Inkrementelle Momentaufnahmen werden derzeit nicht unterstützt.

Ausführliche Informationen finden Sie unter [Freigeben und Empfangen von Daten aus Azure SQL-Datenbank und Azure Synapse Analytics](how-to-share-from-sql.md).

## <a name="share-from-azure-data-explorer"></a>Freigeben über Azure Data Explorer
Azure Data Share unterstützt die Möglichkeit zur direkten Freigabe von Datenbanken über Azure Data Explorer-Cluster. Der Datenanbieter kann auf Datenbank- oder Clusterebene freigeben. Wenn der Datenconsumer auf Datenbankebene freigegeben wurde, kann er nur auf die vom Datenanbieter freigegebene(n) Datenbank(en) zugreifen. Wenn der Datenconsumer auf Clusterebene freigegeben wurde, kann er auf alle Datenbanken aus dem Cluster des Anbieters zugreifen, einschließlich zukünftiger Datenbanken, die vom Datenanbieter erstellt werden.

Für den Zugriff auf freigegebene Datenbanken benötigt der Datenconsumer seinen eigenen Azure Data Explorer-Cluster. Der Azure Data Explorer-Cluster des Datenconsumers muss sich im selben Azure-Rechenzentrum wie der Azure Data Explorer-Cluster des Datenanbieters befinden. Beim Herstellen einer Freigabebeziehung erstellt Azure Data Share eine symbolische Verknüpfung zwischen den Azure Data Explorer-Clustern des Anbieters und des Consumers. Daten, die im Batchmodus im Azure Data Explorer-Ausgangscluster erfasst werden, sind nach wenigen Sekunden bis zu einigen Minuten im Zielcluster verfügbar.

Ausführliche Informationen finden Sie unter [Verwenden von Azure Data Share zum Freigeben von Daten für Azure Data Explorer](/azure/data-explorer/data-share). 

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Freigeben von Daten finden Sie im Tutorial zum [Freigeben Ihrer Daten](share-your-data.md).
