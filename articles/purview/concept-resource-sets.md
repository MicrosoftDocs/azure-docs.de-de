---
title: Grundlegendes zu Ressourcensätzen
description: In diesem Artikel wird erläutert, was Ressourcensätze sind und wie Azure Purview sie erstellt.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/19/2020
ms.openlocfilehash: 55efa9443fd59b66a7677c9c460e473715f201df
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550418"
---
# <a name="understanding-resource-sets"></a>Grundlegendes zu Ressourcensätzen

In diesem Artikel erfahren Sie, wie Azure Purview Datenressourcen mit Ressourcensätzen logischen Ressourcen zuordnet.

## <a name="background-info"></a>Hintergrundinformationen

In skalierbaren Datenverarbeitungssystemen wird eine einzelne Tabelle in der Regel in mehreren Dateien auf einem Datenträger gespeichert. Dieses Konzept wird in Azure Purview mithilfe von Ressourcensätzen dargestellt. Ein Ressourcensatz ist ein einzelnes Objekt im Katalog, das eine große Anzahl von Ressourcen im Speicher darstellt.

Angenommen, Ihr Spark-Cluster hat einen DataFrame in einer ADLS Gen2-Datenquelle persistent gespeichert. In Spark sieht die Tabelle zwar wie eine einzelne logische Ressource aus, aber auf dem Datenträger gibt es wahrscheinlich Tausende von Parquet-Dateien, von denen jede eine Partition des gesamten DataFrame-Inhalts darstellt. Für IoT-Daten und Webprotokolldaten gilt dieselbe Herausforderung. Stellen Sie sich vor, Sie verfügen über einen Sensor, der mehrmals pro Sekunde Protokolldateien ausgibt. Es dauert nicht lange, bis Hunderttausende von Protokolldateien dieses einzelnen Sensors vorliegen.

Um der Herausforderung zu begegnen, eine große Anzahl von Datenressourcen einer einzelnen logischen Ressource zuzuordnen, verwendet Azure Purview Ressourcensätze.

## <a name="how-azure-purview-detects-resource-sets"></a>So erkennt Azure Purview Ressourcensätze

Azure Purview unterstützt das Erkennen von Ressourcensätzen nur in Azure-Blobs, ADLS Gen1 und ADLS Gen2.

Azure Purview erkennt Ressourcensätze automatisch mithilfe eines Features, das als automatisierte Ressourcensatzermittlung bezeichnet wird. Dieses Feature untersucht alle Daten, die in der Überprüfung erfasst wurden, und vergleicht sie mit einem Satz von definierten Mustern.

Angenommen, Sie scannen eine Datenquelle, deren URL `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` ist. Azure Purview prüft die Pfadsegmente und bestimmt, ob sie mit den integrierten Mustern identisch sind. Integrierte Muster für GUIDs, Zahlen, Datumsformate, Lokalisierungscodes (z. B. „en-us“) usw. sind verfügbar. In diesem Fall entspricht das Zahlenmuster *23*. Azure Purview geht davon aus, dass diese Datei Teil eines Ressourcensatzes namens `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` ist.

Für eine URL wie `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` findet Azure Purview sowohl das Lokalisierungsmuster als auch das Zahlenmuster, wobei ein Ressourcensatz mit dem Namen `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` erzeugt wird.

Mit dieser Strategie ordnet Azure Purview die folgenden Ressourcen dem gleichen Ressourcensatz `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` zu:

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

> [!Note]
> Azure Data Lake Storage Gen2 ist jetzt allgemein verfügbar. Es wird empfohlen, ab sofort diese SKU zu verwenden. Weitere Informationen hierzu finden Sie auf der [Produktseite](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Dateitypen, die von Azure Purview nicht als Ressourcensätze erkannt werden

Purview versucht absichtlich nicht, die meisten Dokumentdateitypen wie Word, Excel oder PDF als Ressourcensätze zu klassifizieren. CSVs bilden eine Ausnahme, da es sich dabei um ein gängiges partitioniertes Dateiformat handelt.

## <a name="how-azure-purview-scans-resource-sets"></a>So überprüft Azure Purview Ressourcensätze

Wenn Azure Purview Ressourcen erkennt, die als Teil eines Ressourcensatzes eingeschätzt werden, erfolgt der Wechsel von der vollständigen Überprüfung zur Stichprobenüberprüfung. Bei einer Stichprobenüberprüfung wird nur eine Teilmenge der Dateien geöffnet, die als im Ressourcensatz befindlich eingeschätzt werden. Für jede geöffnete Datei wird das zugehörige Schema verwendet und werden die jeweiligen Klassifizierer ausgeführt. Azure Purview findet dann die neueste Ressource unter den geöffneten Ressourcen und verwendet das Schema und die Klassifizierungen dieser Ressource im Eintrag für den gesamten Ressourcensatz im Katalog.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Was Azure Purview über Ressourcensätze speichert

Zusätzlich zu den einzelnen Schemas und Klassifizierungen speichert Azure Purview die folgenden Informationen zu Ressourcensätzen:

- Daten aus der letzten gründlich gescannten Partitionsressource.
- Aggregierte Informationen über die Partitionsressourcen, aus denen der Ressourcensatz besteht.
- Eine Partitionsanzahl, die zeigt, wie viele Partitionsressourcen gefunden wurden.
- Eine Schemaanzahl, die zeigt, wie viele eindeutige Schemas im gründlich gescannten Stichprobensatz gefunden wurden. Dieser Wert ist entweder eine Zahl von 1–5 oder 5+ für Werte größer als 5.
- Eine Liste von Partitionstypen, wenn mehrere Partitionstypen im Ressourcensatz enthalten sind. Beispielsweise könnte ein IoT-Sensor sowohl XML- als auch JSON-Dateien ausgeben, obwohl beide logisch Teil desselben Ressourcensatzes sind.

## <a name="built-in-resource-set-patterns"></a>Integrierte Ressourcensatzmuster

Azure Purview unterstützt die folgenden Ressourcensatzmuster. Diese Muster können als Name in einem Verzeichnis oder als Teil eines Dateinamens angezeigt werden.

| Mustername | `Display name` | BESCHREIBUNG |
|--------------|--------------|-------------|
| GUID         | {GUID}       | Ein global eindeutiger Bezeichner wie in [RFC 4122](https://tools.ietf.org/html/rfc4122) definiert. |
| Number       | {N}          | Mindestens eine Stelle. |
| Datums-/Uhrzeitformate | {N}     | Azure Purview unterstützt verschiedene Arten von Datums-/Uhrzeitformaten, aber alle werden auf eine Reihe von {N}s reduziert. |
| 4ByteHex     | {HEX}        | Eine vierstellige Hexadezimalzahl. |
| Lokalisierung | {LOC}        | Ein Sprachtag wie in [BCP 47](https://tools.ietf.org/html/bcp47) definiert. Azure Purview unterstützt Tags, die entweder einen Bindestrich (-) oder einen Unterstrich (_) enthalten. Beispielsweise „en_ca“ und „en-ca“. |

## <a name="issues-with-resource-sets"></a>Probleme mit Ressourcensätzen

Obwohl Ressourcensätze in den meisten Fällen gut funktionieren, tritt möglicherweise das Problem auf, das Azure Purview wie folgt verfährt:

- Fälschlicherweise Kennzeichnung einer Ressource als Ressourcensatz
- Einfügen einer Ressource in den falschen Ressourcensatz
- Fälschlicherweise Kennzeichnung einer Ressource als kein Ressourcensatz

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit Data Catalog finden Sie unter [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md).
