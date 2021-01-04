---
title: Leistungsstufen für Blockblobspeicher – Azure Storage
description: Hierin wird der Unterschied zwischen den Premium- und Standard-Leistungsstufen für Azure-Blockblobspeicher erläutert.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 404999cfac5037702eb61fdf74b2c5245ce9eb30
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95526118"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Leistungsstufen für Blockblobspeicher

Da Unternehmen leistungsrelevante cloudbasierte Anwendungen bereitstellen, ist es wichtig, über Optionen für eine kostengünstige Datenspeicherung auf verschiedenen Leistungsstufen zu verfügen.

Azure-Blockblobspeicher bietet zwei verschiedene Leistungsstufen:

- **Premium**: optimiert für hohe Transaktionsraten und einstellige konsistente Speicherlatenzen
- **Standard**: optimiert für hohe Kapazität und hohen Durchsatz

Die folgenden Überlegungen gelten für die unterschiedlichen Leistungsstufen:

| Bereich |Standardleistung  |Premium-Leistung  |
|---------|---------|---------|
|Regionale Verfügbarkeit     |   Alle Regionen      | In [ausgewählten Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|Unterstützte [Speicherkontotypen](../common/storage-account-overview.md#types-of-storage-accounts)     |     Universell v2, BlobStorage, Universell v1    |    BlockBlobStorage     |
|Unterstützt [Blockblobs mit hohem Durchsatz](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    Ja, bei PutBlock- oder PutBlob-Größen von mehr als 4 MiB     |    Ja, bei PutBlock- oder PutBlob-Größen von mehr als 256 KiB    |
|Redundanz     |     Weitere Informationen finden Sie unter [Speicherkontentypen](../common/storage-account-overview.md#types-of-storage-accounts).   |  Unterstützt derzeit nur lokal redundanten Speicher (Locally Redundant Storage, LRS) und zonenredundanten Speicher (Zone Redudant Storage, ZRS).<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup>Zonenredundanter Speicher (ZRS) ist in ausgewählten Regionen für Blockblob-Speicherkonten mit Premium-Leistung verfügbar.</div>

Bezüglich der Kosten bietet die Premium-Leistung optimierte Preise für Anwendungen mit hohen Transaktionsraten, um die [Gesamtspeicherkosten für diese Workloads zu senken](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/).

## <a name="premium-performance"></a>Premium-Leistung

Blockblobspeicher mit Premium-Leistung macht Daten über Hochleistungshardware verfügbar. Daten werden auf SSD-Datenträgern (Solid State Drives) gespeichert, die für niedrige Latenz optimiert sind. Der Durchsatz ist bei SSDs im Vergleich zu herkömmlichen Festplatten höher.

Speicher mit Premium-Leistung eignet sich ideal für Workloads, die schnelle und konsistente Antwortzeiten benötigen. Er ist am besten für Workloads geeignet, die viele kleine Transaktionen durchführen. Beispielworkloads umfassen:

- **Interaktive Workloads**. Für diese Workloads sind sofortige Updates und Benutzerfeedback erforderlich (z. B. bei E-Commerce- und Kartierungsanwendungen). Beispielsweise werden in einer E-Commerce-Anwendung weniger häufig angezeigte Elemente wahrscheinlich nicht zwischengespeichert. Sie müssen dem Kunden jedoch sofort bei Bedarf angezeigt werden.

- **Analysen**. In einem IoT-Szenario können jede Sekunde viele kleinere Schreibvorgänge in die Cloud übermittelt werden. Große Datenmengen können aufgenommen, zu Analysezwecken aggregiert und dann fast unmittelbar gelöscht werden. Dank der hohen Aufnahmefähigkeiten des Premium-Blockblobspeichers kann er für diesen Workloadtyp effizient eingesetzt werden.

- **Künstliche Intelligenz/Machine Learning (KI/ML)** . Bei AI/ML geht es um den Nutzung und Verarbeitung verschiedener Datentypen wie visuelle Elemente, Sprache und Text. Dieser High Performance Compting-Workloadtyp verarbeitet große Datenmengen, die eine schnelle Reaktion und effiziente Erfassungszeiten für die Datenanalyse erfordern.

- **Datentransformation**. Für Prozesse, die eine konstante Bearbeitung, Änderung und Konvertierung von Daten erfordern, werden sofortige Updates benötigt. Um eine genaue Datendarstellung zu erzielen, müssen Änderungen den Consumern dieser Daten sofort angezeigt werden.

## <a name="standard-performance"></a>Standardleistung

Die Standardleistung unterstützt verschiedene [Zugriffsebenen](storage-blob-storage-tiers.md), um Daten möglichst kostengünstig zu speichern. Sie ist für eine hohe Kapazität und einen hohen Durchsatz bei großen Datasets optimiert.

- **Sicherung und Datasets für die Notfallwiederherstellung**. Speicher des Typs „Standardleistung“ bietet kostengünstige Ebenen, sodass er für kurz- und langfristige Notfallwiederherstellungs-Datasets, sekundäre Sicherungen sowie die Archivierung von Konformitätsdaten ein perfekter Anwendungsfall ist.

- **Medieninhalt**. Auf Bilder und Videos wird oft zugegriffen, wenn sie zum ersten Mal erstellt und gespeichert werden, doch dieser Inhaltstyp wird seltener verwendet, wenn er älter wird. Speicher des Typs „Standardleistung“ bietet geeignete Ebenen für Anforderungen von Medieninhalten. 

- **Datenverarbeitung in Massen**. Diese Arten von Workloads eignen sich für Standardspeicher, weil sie statt konsistenter niedriger Latenz kostengünstigen Speicher mit hohem Durchsatz erfordern. Große, rohe Datasets werden zur Verarbeitung bereitgestellt und schließlich zu kälteren Ebenen migriert.

## <a name="migrate-from-standard-to-premium"></a>Migrieren von Standard zu Premium

Sie können kein vorhandenes Speicherkonto mit Standard-Leistung in ein Blockblob-Speicherkonto mit Premium-Leistung konvertieren. Um zu einem Speicherkonto mit Premium-Leistung zu migrieren, müssen Sie ein Blockblob-Speicherkonto erstellen und die Daten zum neuen Konto migrieren. Weitere Informationen finden Sie unter [Erstellen eines Blockblob-Speicherkontos](storage-blob-create-account-block-blob.md).

Zum Kopieren von Blobs zwischen Speicherkonten können Sie die neueste Version des Befehlszeilenprogramms [AzCopy](../common/storage-use-azcopy-blobs.md) verwenden. Für die Datenverschiebung und Transformation sind auch andere Tools, wie Azure Data Factory, verfügbar.

## <a name="blob-lifecycle-management"></a>Lebenszyklusverwaltung für Blobs

Die Blob Storage-Lebenszyklusverwaltung bietet eine umfassende, regelbasierte Richtlinie:

- **Premium**: Daten laufen am Ende ihres Lebenszyklus ab.
- **Standard**: Daten werden auf die bestmögliche Zugriffsebene übertragen und am Ende des Lebenszyklus laufen die Daten ab.

Weitere Informationen finden Sie unter [Verwalten des Azure Blob Storage-Lebenszyklus](storage-lifecycle-management-concepts.md).

Daten, die in einem Premium-Blockblob-Speicherkonto gespeichert sind, können nicht zwischen den Ebenen „Heiß“, „Kalt“ und „Archiv“ verschoben werden. Sie können jedoch Blobs von einem Blockblob-Speicherkonto auf die Zugriffsebene „Heiß“ in einem *anderen* Konto kopieren. Verwenden Sie die API [Put Block From URL](/rest/api/storageservices/put-block-from-url) oder [AzCopy v10](../common/storage-use-azcopy-v10.md), um Daten in ein anderes Konto zu kopieren. Die API **Put Block From URL** kopiert Daten synchron auf dem Server. Der Aufruf wird erst abgeschlossen, wenn alle Daten vom ursprünglichen Serverstandort zum Zielstandort verschoben wurden.

## <a name="next-steps"></a>Nächste Schritte

Evaluieren von „Heiß“, „Kalt“ und „Archiv“ in GPv2- und Blobspeicherkonten

- [Informationen zum Aktivieren von Blobdaten aus der Archivzugriffsebene](storage-blob-rehydration.md)
- [Auswerten der Nutzung vorhandener Speicherkonten durch Aktivierung von Azure Storage-Metriken](./monitor-blob-storage.md)
- Prüfen der Preise für „Heiß“, „Kalt“ und „Archiv“ in Blobspeicher- und GPv2-Konten nach Region unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
- [Überprüfen der Preise für Datenübertragungen unter Datenübertragungen – Preisdetails](https://azure.microsoft.com/pricing/details/data-transfers/)