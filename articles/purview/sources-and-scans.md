---
title: Unterstützte Datenquellen und Dateitypen
description: Dieser Artikel enthält konzeptionelle Details zu den Datenquellen und Dateitypen, die in Purview unterstützt werden.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 6432cc9affd34c0fa9b832aea91932a2b9e7540f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576671"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Unterstützte Datenquellen und Dateitypen in Azure Purview

In diesem Artikel sind die Datenquellen, Dateitypen und Überprüfungskonzepte erläutert, die in Purview unterstützt werden.

## <a name="supported-data-sources"></a>Unterstützte Datenquellen

Azure Purview unterstützt die folgenden Quellen:

| Speichertyp | Unterstützter Authentifizierungstyp | Einrichten von Überprüfungen über UX/PowerShell |
| ---------- | ------------------- | ------------------------------ |
| Lokaler SQL Server                   | SQL-Auth.                        | UX                                |
| Azure Synapse Analytics (ehemals SQL DW)            | SQL-Auth., Dienstprinzipal, MSI               | UX                             |
| Azure SQL-Datenbank (DB)                  | SQL-Auth., Dienstprinzipal, MSI               | UX |
| Verwaltete Azure SQL-Datenbank-Instanz      | SQL-Auth., Dienstprinzipal, MSI               | UX    |
| Azure Blob Storage                       | Kontoschlüssel, Dienstprinzipal, MSI | UX            |
| Azure-Daten-Explorer                      | Dienstprinzipal                              | UX            |
| Azure Data Lake Storage Gen1 (ADLS Gen1) | Dienstprinzipal, MSI                              | UX            |
| Azure Data Lake Storage Gen2 (ADLS Gen2) | Kontoschlüssel, Dienstprinzipal, MSI            | UX            |
| Azure Cosmos DB                          | Kontoschlüssel                                    | UX            |


> [!Note]
> Azure Data Lake Storage Gen2 ist jetzt allgemein verfügbar. Es wird empfohlen, ab sofort diese SKU zu verwenden. Weitere Informationen hierzu finden Sie auf der [Produktseite](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="file-types-supported-for-scanning"></a>Dateitypen, die für Überprüfungen unterstützt werden

Die folgenden Dateitypen werden für Überprüfungen, Schemaextraktion und Klassifizierung unterstützt, wo dies zutrifft:

- Dateierweiterungen der unterstützten strukturierten Dateiformate: AVRO, ORC, PARQUET, CSV, JSON, PSV, SSV, TSV, TXT, XML
- Dateierweiterungen der unterstützten Dokumentdateiformate: DOC, DOCM, DOCX, DOT, ODP, ODS, ODT, PDF, POT, PPS, PPSX, PPT, PPTM, PPTX, XLC, XLS, XLSB, XLSM, XLSX, XLT
- Purview unterstützt auch benutzerdefinierte Dateierweiterungen und benutzerdefinierte Parser.

## <a name="sampling-within-a-file"></a>Sampling (Überprüfung) innerhalb einer Datei

In der Purview-Terminologie:
- L1-Überprüfung (Scan): Extrahiert grundlegende Informationen und Metadaten wie Dateiname, Dateigröße und vollqualifizierter Name
- L2-Überprüfung: Extrahiert das Schema für strukturierte Dateitypen und Datenbanktabellen
- L3-Überprüfung: Extrahiert das Schema, sofern verfügbar, und wendet System- und benutzerdefinierte Klassifizierungsregeln auf die überprüfte Datei an

Bei allen strukturierten Dateiformaten werden Dateien vom Purview-Scanner auf folgende Weise überprüft:

- Bei strukturierten Dateitypen überprüft der Scanner 128 Zeilen in jeder Spalte oder 1 MB, je nachdem, welcher Wert kleiner ist.
- Bei Dokumentdateiformaten überprüft der Scanner 20 MB von jeder Datei.
    - Ist eine Dokumentdatei größer als 20 MB, wird sie keiner tiefen Überprüfung unterzogen (sie wird nicht klassifiziert). In diesem Fall erfasst Purview nur grundlegende Metadaten, z. B. Dateiname und vollqualifizierter Name.

## <a name="resource-set-file-sampling"></a>Ressourcensatzüberprüfung

Ein Ordner oder eine Gruppe von Partitionsdateien wird in Purview als *Ressourcensatz* erkannt, wenn der Ordner oder die Gruppe mit einer Systemressourcensatzrichtlinie oder einer benutzerdefinierten Ressourcensatzrichtlinie übereinstimmt. Wird ein Ressourcensatz erkannt, überprüft Purview jeden darin enthaltenen Ordner. Weitere Informationen zu Ressourcensätzen finden Sie [hier](concept-resource-sets.md).

Dateiüberprüfung für Ressourcensätze nach Dateitypen:

- **Dateien mit Trennzeichen (CSV, PSV, SSV, TSV)** : 1 aus 100 Dateien wird in einem Ordner oder einer Gruppe von Partitionsdateien, die als „ Ressourcensatz“' angesehen werden, überprüft (L3-Überprüfung).
- **Data Lake-Dateitypen (Parquet, Avro, Orc)** : 1 aus 18446744073709551615 (long max) Dateien wird in einem Ordner oder einer Gruppe von Partitionsdateien, die als *Ressourcensatz* angesehen werden, überprüft (L3-Überprüfung).
- **Andere strukturierte Dateitypen (JSON, XML, TXT)** : 1 aus 100 Dateien wird in einem Ordner oder einer Gruppe von Partitionsdateien, die als „ Ressourcensatz“' angesehen werden, überprüft (L3-Überprüfung).
- **SQL-Objekte und CosmosDB-Entitäten**: Jede Datei wird einer L3-Überprüfung unterzogen.
- **Dokumentdateitypen**: Jede Datei wird einer L3-Überprüfung unterzogen. Ressourcensatzmuster gelten für diese Dateitypen nicht.

## <a name="classification"></a>Klassifizierung

Alle 105 Systemklassifizierungsregeln gelten für strukturierte Dateiformate. Nur die MCE-Klassifizierungsregeln gelten für Dokumentdateitypen (nicht die nativen RegEx-Muster der Datenüberprüfung, sondern Bloomfilter-basierte Erkennung). Weitere Informationen zu den unterstützten Klassifizierungen finden Sie unter [Unterstützte Klassifizierungen in Azure Purview](supported-classifications.md).

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Scannen von Daten mit Azure Purview (Vorschau)](tutorial-scan-data.md)
- [Verwalten von Datenquellen in Azure Purview (Vorschau)](manage-data-sources.md).