---
title: Azure Data Lake Storage Gen 1-Edition von Azure Stream Analytics
description: In diesem Artikel wird Azure Data Lake Storage Gen 1 als Ausgabeoption für Azure Stream Analytics beschrieben.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 688cd7c2e54a6b4c6b5a11705e10c33604324f09
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576280"
---
# <a name="azure-data-lake-storage-gen-1-output-from-azure-stream-analytics"></a>Azure Data Lake Storage Gen 1-Edition von Azure Stream Analytics

Stream Analytics unterstützt Ausgaben von [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage ist ein unternehmensweites Hyperscale-Repository für Big Data-Analyseworkloads. Mit Data Lake Storage können Sie Daten unabhängig von Größe, Typ und Erfassungsgeschwindigkeit zur Durchführung operativer und explorativer Analysen speichern. Stream Analytics muss autorisiert werden, um auf Data Lake Storage zugreifen zu können.

Die Azure Data Lake Storage-Ausgabe aus Stream Analytics ist nicht in den Regionen Azure China 21Vianet und Azure Deutschland (T-Systems International) verfügbar.

## <a name="output-configuration"></a>Ausgabekonfiguration

In der folgenden Tabelle sind Eigenschaftsnamen und deren Beschreibungen für die Konfiguration Ihrer Data Lake Storage Gen1-Ausgabe aufgeführt.

| Eigenschaftenname | BESCHREIBUNG |
| --- | --- |
| Ausgabealias | Ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an Data Lake Store weiterzuleiten. |
| Subscription | Das Abonnement, das Ihr Azure Data Lake Storage-Konto enthält. |
| Kontoname | Der Name des Data Lake Store-Kontos, an das Sie die Ausgabe senden. Eine Dropdownliste der in Ihrem Abonnement verfügbaren Data Lake Store-Konten wird angezeigt. |
| Präfixmuster des Pfads | Der Dateipfad, mit dem Ihre Dateien im angegebenen Data Lake Store-Konto geschrieben werden. Sie können eine oder mehrere Instanzen der Variablen {date} und {time} angeben:<br /><ul><li>Beispiel 1: folder1/logs / {date} / {time}</li><li>Beispiel 2: folder1/logs / {date}</li></ul><br />Der Zeitstempel der erstellten Ordnerstruktur folgt der UTC, nicht der lokalen Zeit.<br /><br />Wenn das Dateipfadmuster keinen nachgestellten Schrägstrich („/“) enthält, wird auch das letzte Muster im Dateipfad als Dateinamenpräfix behandelt. <br /><br />In diesen Fällen werden neue Dateien erstellt:<ul><li>Änderung im Ausgabeschema</li><li>Externer oder interner Neustart eines Auftrags</li></ul> |
| Datumsformat | Optional. Wenn das date-Token im Pfadpräfix verwendet wird, können Sie das Datumsformat auswählen, unter dem die Dateien gespeichert werden. Beispiel: YYYY/MM/DD |
|Zeitformat | Optional. Wenn das time-Token im Pfadpräfix verwendet wird, können Sie das Zeitformat auswählen, unter dem die Dateien gespeichert werden. Der einzige derzeit unterstützte Wert ist HH |
| Ereignisserialisierungsformat | Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt.|
| Codieren | Bei Verwendung des CSV- oder JSON-Formats muss eine Codierung angegeben werden. UTF-8 ist derzeit das einzige unterstützte Codierungsformat.|
| Trennzeichen | Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der CSV-Daten. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabstopp und senkrechter Strich.|
| Format | Gilt nur für die JSON-Serialisierung. **Separate Zeile** gibt an, dass die Ausgabe so formatiert wird, dass jedes JSON-Objekt in einer neuen Zeile enthalten ist. Wenn Sie **Separate Zeile** auswählen, wird der JSON-Code objektweise gelesen. Der gesamte Inhalt an sich wäre kein gültiger JSON-Code.  **Array** gibt an, dass die Ausgabe als Array aus JSON-Objekten formatiert wird. Dieses Array wird nur geschlossen, wenn der Auftrag beendet wird oder Stream Analytics mit dem nächsten Zeitfenster fortfährt. Im Allgemeinen ist es besser, in separaten Zeilen geschriebenen JSON-Code zu verwenden, da er keine spezielle Behandlung erfordert, während noch in die Ausgabedatei geschrieben wird.|
| Authentifizierungsmodus | Sie können den Zugriff auf Ihr Data Lake Storage-Konto mit einer [verwalteten Identität](stream-analytics-managed-identities-adls.md) (Vorschau) oder einem Benutzertoken autorisieren. Nachdem Sie Zugriff gewährt haben, können Sie ihn auch wiederrufen, indem Sie das Kennwort für das Benutzerkonto ändern, die Data Lake Storage-Ausgabe für diesen Auftrag löschen oder den Stream Analytics-Auftrag löschen. |

## <a name="partitioning"></a>Partitionierung

Verwenden Sie für den Partitionsschlüssel die {date}- und {time}-Token im Pfadpräfixmuster. Wählen Sie ein Datumsformat wie JJJJ/MM/TT, TT/MM/JJJJ oder MM-TT-JJJJ aus. Verwenden Sie HH für das Zeitformat. Die Anzahl der Ausgabeschreiber folgt der Eingabepartitionierung für [vollständig parallelisierbare Abfragen](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Ausgabebatchgröße

Die maximale Nachrichtengröße finden Sie unter [Grenzwerte für Data Lake Storage](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits). Verwenden Sie zur Optimierung der Batchgröße bis zu 4 MB pro Schreibvorgang.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mit der Azure CLI](quick-create-azure-cli.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mithilfe einer ARM-Vorlage](quick-create-azure-resource-manager.md)
* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mit Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mithilfe von Visual Studio](stream-analytics-quick-create-vs.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags in Visual Studio Code](quick-create-visual-studio-code.md)