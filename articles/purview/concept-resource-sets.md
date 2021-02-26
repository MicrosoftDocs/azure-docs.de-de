---
title: Grundlegendes zu Ressourcensätzen
description: In diesem Artikel wird erläutert, was Ressourcensätze sind und wie Azure Purview sie erstellt.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 39a1400bfe642672ba276686851d4f0edebbeb00
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387851"
---
# <a name="understanding-resource-sets"></a>Grundlegendes zu Ressourcensätzen

In diesem Artikel erfahren Sie, wie Azure Purview Datenressourcen mit Ressourcensätzen logischen Ressourcen zuordnet.
## <a name="background-info"></a>Hintergrundinformationen

In skalierbaren Datenverarbeitungssystemen wird eine einzelne Tabelle in der Regel in mehreren Dateien auf einem Datenträger gespeichert. Dieses Konzept wird in Azure Purview mithilfe von Ressourcensätzen dargestellt. Ein Ressourcensatz ist ein einzelnes Objekt im Katalog, das eine große Anzahl von Ressourcen im Speicher darstellt.

Angenommen, Ihr Spark-Cluster hat einen DataFrame in einer Azure Data Lake Storage (ADLS) Gen2-Datenquelle persistent gespeichert. In Spark sieht die Tabelle zwar wie eine einzelne logische Ressource aus, aber auf dem Datenträger gibt es wahrscheinlich Tausende von Parquet-Dateien, von denen jede eine Partition des gesamten DataFrame-Inhalts darstellt. Für IoT-Daten und Webprotokolldaten gilt dieselbe Herausforderung. Stellen Sie sich vor, Sie verfügen über einen Sensor, der mehrmals pro Sekunde Protokolldateien ausgibt. Es dauert nicht lange, bis Hunderttausende von Protokolldateien dieses einzelnen Sensors vorliegen.

Um der Herausforderung zu begegnen, eine große Anzahl von Datenressourcen einer einzelnen logischen Ressource zuzuordnen, verwendet Azure Purview Ressourcensätze.

## <a name="how-azure-purview-detects-resource-sets"></a>So erkennt Azure Purview Ressourcensätze

Azure Purview unterstützt das Erkennen von Ressourcensätzen in Azure Blob Storage, ADLS Gen1 und ADLS Gen2.

Azure Purview erkennt beim Scannen automatisch Ressourcensätze. Dieses Feature untersucht alle Daten, die in der Überprüfung erfasst wurden, und vergleicht sie mit einem Satz von definierten Mustern.

Angenommen, Sie scannen eine Datenquelle, deren URL `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` ist. Azure Purview prüft die Pfadsegmente und bestimmt, ob sie mit den integrierten Mustern identisch sind. Integrierte Muster für GUIDs, Zahlen, Datumsformate, Lokalisierungscodes (z. B. „en-us“) usw. sind verfügbar. In diesem Fall entspricht das Zahlenmuster *23*. Azure Purview geht davon aus, dass diese Datei Teil eines Ressourcensatzes namens `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` ist.

Für eine URL wie `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` findet Azure Purview sowohl das Lokalisierungsmuster als auch das Zahlenmuster, wobei ein Ressourcensatz mit dem Namen `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` erzeugt wird.

Mit dieser Strategie ordnet Azure Purview die folgenden Ressourcen dem gleichen Ressourcensatz `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` zu:

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Dateitypen, die von Azure Purview nicht als Ressourcensätze erkannt werden

Purview versucht absichtlich nicht, die meisten Dokumentdateitypen wie Word, Excel oder PDF als Ressourcensätze zu klassifizieren. Das CSV-Format bildet eine Ausnahme, da es sich dabei um ein gängiges partitioniertes Dateiformat handelt.

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
### <a name="regex-based-patterns"></a>Auf Regex basierende Muster

| Mustername | Anzeigename | BESCHREIBUNG |
|--------------|--------------|-------------|
| Guid         | {GUID}       | Ein global eindeutiger Bezeichner wie in [RFC 4122](https://tools.ietf.org/html/rfc4122) definiert. |
| Number       | {N}          | Mindestens eine Dezimalstelle. |
| Datums-/Uhrzeitformate | {Jahr}{Monat}{Tag}{N}     | Wir unterstützen verschiedene Datums-/Uhrzeitformate, aber alle Formate werden als {Jahr}[Trennzeichen]{Monat}[Trennzeichen]{Tag} oder Reihe von {N}s dargestellt. |
| 4ByteHex     | {HEX}        | Eine vierstellige Hexadezimalzahl. |
| Lokalisierung | {LOC}        | Ein Sprachtag, wie in [BCP 47](https://tools.ietf.org/html/bcp47) definiert. Namen mit den Zeichen - sowie _ werden unterstützt (z. B. en_ca und en-ca). |

### <a name="complex-patterns"></a>Komplexe Muster

| Mustername | Anzeigename | BESCHREIBUNG |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Dateibezeichner der Spark-Partition |
| Date(jjjj/mm/tt)InPath  | {Jahr}/{Monat}/{Tag} | Muster für Jahr/Monat/Tag, das sich über mehrere Ordner erstreckt |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Anzeigen von Ressourcensätzen im Azure Purview-Katalog

Wenn Azure Purview eine Gruppe von Ressourcen einem Ressourcensatz zuordnet, wird versucht, die nützlichsten Informationen zu extrahieren, um sie als Anzeigenamen im Katalog zu verwenden. Einige Beispiele für die standardmäßig angewendete Benennungskonvention: 

### <a name="example-1"></a>Beispiel 1

Qualifizierter Name: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Anzeigename: „name of spark output“(Name der Spark-Ausgabe)

### <a name="example-2"></a>Beispiel 2

Qualifizierter Name: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Anzeigename: „my partitioned data“ (meine partitionierten Daten)

### <a name="example-3"></a>Beispiel 3

Qualifizierter Name: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Anzeigename: „data“ (Daten)

## <a name="known-issues-with-resource-sets"></a>Bekannte Probleme mit Ressourcensätzen

Obwohl Ressourcensätze in den meisten Fällen gut funktionieren, tritt möglicherweise das Problem auf, bei dem Azure Purview wie folgt verfährt:

- Fälschlicherweise Kennzeichnung einer Ressource als Ressourcensatz
- Einfügen einer Ressource in den falschen Ressourcensatz
- Fälschlicherweise Kennzeichnung einer Ressource als kein Ressourcensatz

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit Azure Purview finden Sie unter [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md).
