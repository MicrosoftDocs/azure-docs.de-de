---
title: Blobspeicher- und Azure Data Lake Gen2-Ausgabe von Azure Stream Analytics
description: In diesem Artikel werden Blobspeicher und Azure Data Lake Gen 2 als Ausgabe für Azure Stream Analytics beschrieben.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 3ce22837da2ca30249b399a297b6188d950b1ea4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935146"
---
# <a name="blob-storage-and-azure-data-lake-gen2-output-from-azure-stream-analytics"></a>Blobspeicher- und Azure Data Lake Gen2-Ausgabe von Azure Stream Analytics

Mit Data Lake Storage Gen2 wird Azure Storage zur Grundlage für das Erstellen von Enterprise Data Lakes in Azure. Data Lake Storage Gen2 wurde eigens für die Verarbeitung mehrerer Petabyte an Informationen bei gleichzeitiger Unterstützung eines Durchsatzes von Hunderten von Gigabit konzipiert und bietet Ihnen eine einfache Möglichkeit, riesige Datenmengen zu verwalten. Ein wesentlicher Bestandteil von Data Lake Storage Gen2 ist das Hinzufügen eines hierarchischen Namespace zum Blobspeicher.

Azure Blob Storage bietet eine kostengünstige und skalierbare Lösung zum Speichern von großen Mengen unstrukturierter Daten in der Cloud. Eine Einführung in Blobspeicher und seine Nutzung finden Sie unter [Hochladen, Herunterladen und Auflisten von Blobs mit dem Azure-Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="output-configuration"></a>Ausgabekonfiguration

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer Blob- oder ADLS Gen2-Ausgabe.

| Eigenschaftenname       | BESCHREIBUNG                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Ausgabealias        | Ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Blobspeicher weiterzuleiten. |
| Speicherkonto     | Der Name des Speicherkontos, an das Sie die Ausgabe senden.               |
| Speicherkontoschlüssel | Der geheime Schlüssel, der dem Speicherkonto zugeordnet ist.                              |
| Speichercontainer   | Eine logische Gruppierung für Blobs, die im Azure Blob-Dienst gespeichert sind. Wenn Sie ein Blob in den Blobdienst hochladen, müssen Sie einen Container für das Blob angeben. |
| Pfadmuster | Optional. Das Dateipfadmuster, mit dem Ihre Blobs im angegebenen Container geschrieben werden. <br /><br /> In dem Pfadmuster können Sie mindestens eine Instanz der date- und time-Variablen verwenden, um die Häufigkeit anzugeben, mit der Blobs geschrieben werden: <br /> {date}, {time} <br /><br />Mithilfe von benutzerdefinierter Blob-Partitionierung können Sie einen benutzerdefinierten {field}-Namen aus Ihren Ereignissen angeben, um Blobs zu partitionieren. Der Feldname ist alphanumerisch und kann Leerstellen, Bindestriche und Unterstriche enthalten. Für benutzerdefinierte Felder gelten die folgenden Einschränkungen: <ul><li>Bei Feldnamen wird nicht zwischen Groß- und Kleinschreibung unterschieden. Der Dienst kann z. B. nicht zwischen Spalte „ID“ und Spalte „id“ unterscheiden.</li><li>Geschachtelte Felder sind nicht zulässig. Verwenden Sie stattdessen einen Alias in der Auftragsabfrage zum Vereinfachen des Felds.</li><li>Ausdrücke können nicht als Feldname verwendet werden.</li></ul> <br />Diese Funktion ermöglicht die Verwendung von Konfigurationen für benutzerdefinierte Angaben des Datums-/Uhrzeitformats im Pfad. Benutzerdefinierte Datums- und Uhrzeitformate müssen einzeln nacheinander angegeben und in das Schlüsselwort „{datetime:\<specifier>}“ eingeschlossen werden. Zulässige Eingaben für \<specifier> sind „yyyy“, „MM“, „M“, „dd“, „d“, „HH“, „H“, „mm“, „m“, „ss“ und „s“. Das Schlüsselwort „{datetime:\<specifier>}“ kann mehrmals im Pfad verwendet werden, um benutzerdefinierte Konfigurationen für Datum und Uhrzeit zu bilden. <br /><br />Beispiele: <ul><li>Beispiel 1: cluster1/logs/{date}/{time}</li><li>Beispiel 2: cluster1/logs/{date}</li><li>Beispiel 3: cluster1/{client_id}/{date}/{time}</li><li>Beispiel 4: cluster1/{datetime:ss}/{myField}; hierbei lautet die Abfrage: SELECT data.myField AS myField FROM Input;</li><li>Beispiel 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />Der Zeitstempel der erstellten Ordnerstruktur folgt der UTC, nicht der lokalen Zeit. [System.Timestamp](./stream-analytics-time-handling.md#choose-the-best-starting-time) ist die Zeit, die für die gesamte zeitbasierte Partitionierung aufgewendet wird.<br /><br />Die Dateibenennung verwendet die nachstehende Konvention: <br /><br />{Präfixmuster des Pfads}/schemaHashcode_Guid_Number.extension<br /><br /> In diesem Fall steht „Guid“ für den eindeutigen Bezeichner, der einem internen Writer zugewiesen ist, der für das Schreiben in eine Blobdatei erstellt wurde. Die Zahl stellt den Index des Blobblocks dar. <br /><br /> Beispielausgabedateien:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Weitere Informationen zu diesem Feature finden Sie unter [Azure Stream Analytics – benutzerdefinierte Ausgabepartitionierung von Blobs](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Datumsformat | Optional. Wenn das date-Token im Pfadpräfix verwendet wird, können Sie das Datumsformat auswählen, unter dem die Dateien gespeichert werden. Beispiel: YYYY/MM/DD |
| Zeitformat | Optional. Wenn das time-Token im Pfadpräfix verwendet wird, können Sie das Zeitformat auswählen, unter dem die Dateien gespeichert werden. Der einzige derzeit unterstützte Wert ist HH |
| Ereignisserialisierungsformat | Das Serialisierungsformat für Ausgabedaten. JSON, CSV, Avro und Parquet werden unterstützt. |
|Mindestanzahl von Zeilen |Die minimale Zeilenanzahl pro Batch. Für Parquet erstellt jeder Batch eine neue Datei. Der aktuelle Standardwert beträgt 2.000 Zeilen und der zulässige Höchstwert 10.000 Zeilen.|
|Maximaler Zeitraum |Die maximale Wartezeit pro Batch. Nach Ablauf dieser Zeit wird der Batch auch dann in die Ausgabe geschrieben, wenn die Anforderung der Mindestanzahl von Zeilen nicht erfüllt ist. Der aktuelle Standardwert beträgt 1 Minute und der zulässige Höchstwert 2 Stunden. Wenn Ihre Blobausgabe eine Pfadmusterhäufigkeit aufweist, kann die Wartezeit nicht über dem Partitionszeitbereich liegen.|
| Codieren    | Bei Verwendung des CSV- oder JSON-Formats muss eine Codierung angegeben werden. UTF-8 ist derzeit das einzige unterstützte Codierungsformat. |
| Trennzeichen   | Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der CSV-Daten. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabstopp und senkrechter Strich. |
| Format      | Gilt nur für die JSON-Serialisierung. **Separate Zeile** gibt an, dass die Ausgabe so formatiert wird, dass jedes JSON-Objekt in einer neuen Zeile enthalten ist. Wenn Sie **Separate Zeile** auswählen, wird der JSON-Code objektweise gelesen. Der gesamte Inhalt an sich wäre kein gültiger JSON-Code. **Array** gibt an, dass die Ausgabe als Array aus JSON-Objekten formatiert wird. Dieses Array wird nur geschlossen, wenn der Auftrag beendet wird oder Stream Analytics mit dem nächsten Zeitfenster fortfährt. Im Allgemeinen ist es besser, in separaten Zeilen geschriebenen JSON-Code zu verwenden, da er keine spezielle Behandlung erfordert, während noch in die Ausgabedatei geschrieben wird. |

## <a name="blob-output-files"></a>Blobausgabedateien

Wenn Sie Blobspeicher als Ausgabe verwenden, wird in den folgenden Fällen eine neue Datei im Blob erstellt:

* Wenn die Datei die maximale Anzahl von zulässigen Blöcken (derzeit 50.000) überschreitet. Sie können die maximale zulässige Anzahl von Blöcken erreichen, ohne die maximal zulässige Blobgröße zu erreichen. Wenn die Ausgaberate z.B. hoch ist, können Sie mehr Bytes pro Block vorhanden sein, und die Datei ist größer. Wenn die Ausgaberate niedrig ist, weist jeder Block weniger Daten auf, und die Datei ist kleiner.
* Wenn es eine Schemaänderung in der Ausgabe gibt und das Ausgabeformat ein festes Schema erfordert (CSV, Avro, Parquet).
* Wenn ein Auftrag neu gestartet wird, entweder extern, weil er von einem Benutzer beendet und gestartet wurde, oder intern zu Systemwartungs- oder Fehlerbehebungszwecken.
* Wenn die Abfrage vollständig partitioniert ist und für jede Ausgabepartition eine neue Datei erstellt wird.
* Wenn der Benutzer eine Datei oder einen Container des Speicherkontos löscht.
* Wenn die Ausgabe zeitpartitioniert ist, indem das Pfadpräfixmuster und ein neues Blob verwendet wird, wenn die Abfrage zur nächsten Stunde wechselt.
* Wenn die Ausgabe über ein benutzerdefiniertes Feld partitioniert und pro Partitionsschlüssel ein neues Blob erstellt wird, falls es noch nicht vorhanden ist.
* Falls die Ausgabe über ein benutzerdefiniertes Feld partitioniert wird, für das die Kardinalität des Partitionsschlüssels den Wert 8,000 übersteigt und pro Partitionsschlüssel ein neues Blob erstellt wird.

## <a name="partitioning"></a>Partitionierung

Verwenden Sie für Partitionsschlüssel die Token {date} und {time} aus Ihren Ereignisfeldern im Pfadmuster. Wählen Sie ein Datumsformat wie JJJJ/MM/TT, TT/MM/JJJJ oder MM-TT-JJJJ. „HH“ wird für das Uhrzeitformat verwendet. Die Blobausgabe kann durch ein einzelnes benutzerdefiniertes Ereignisattribut („{fieldname}“ oder „{datetime:\<specifier>}“) partitioniert werden. Die Anzahl der Ausgabeschreiber folgt der Eingabepartitionierung für [vollständig parallelisierbare Abfragen](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Ausgabebatchgröße

Die maximale Nachrichtengröße finden Sie unter [Grenzwerte für Azure Storage](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Die maximale Blobblockgröße beträgt 4 MB und die maximale Anzahl von Blobblöcken 50.000. |

## <a name="next-steps"></a>Nächste Schritte

* [Verwendung von verwalteten Identitäten (Vorschau) zum Authentifizieren von Azure Stream Analytics-Aufträgen für Azure Blob Storage](blob-output-managed-identity.md)
* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md)
