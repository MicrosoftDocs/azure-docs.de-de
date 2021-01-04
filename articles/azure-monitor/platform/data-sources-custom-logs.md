---
title: Sammeln von benutzerdefinierten Protokollen mit dem Log Analytics-Agent in Azure Monitor
description: Azure Monitor kann Ereignisse aus Textdateien auf Windows- und Linux-Computern erfassen.  Dieser Artikel enthält Informationen zum Definieren eines neuen benutzerdefinierten Protokolls sowie Details zu den Datensätzen, die in Azure Monitor erstellt werden.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: b2b27da096ed18170ca8c9d70f31dc955fb74950
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352829"
---
# <a name="collect-custom-logs-with-log-analytics-agent-in-azure-monitor"></a>Sammeln von benutzerdefinierten Protokollen mit dem Log Analytics-Agent in Azure Monitor

Mithilfe der Datenquelle „Benutzerdefinierte Protokolle“ für den Log Analytics-Agent in Azure Monitor können Ereignisse aus Textdateien auf Windows- und Linux-Computern erfasst werden. Viele Anwendungen protokollieren Informationen nicht in standardmäßigen Protokollierungsdiensten wie Windows-Ereignisprotokoll oder Syslog, sondern in Textdateien. Die erfassten Daten können entweder in Ihren Abfragen zu einzelnen Feldern aufgeschlüsselt oder während der Erfassung in einzelne Felder extrahiert werden.

> [!IMPORTANT]
> In diesem Artikel wird das Sammeln von benutzerdefinierten Protokollen mit dem [Log Analytics-Agent](log-analytics-agent.md) beschrieben, einem der von Azure Monitor verwendeten Agents. Andere Agents sammeln andere Daten und werden anders konfiguriert. Eine Liste der verfügbaren Agents und der von ihnen gesammelten Daten finden Sie unter [Übersicht über Azure Monitor-Agents](agents-overview.md).

![Benutzerdefinierte Protokollsammlung](media/data-sources-custom-logs/overview.png)

Die zu sammelnden Protokolldateien müssen folgende Kriterien erfüllen:

- Das Protokoll muss entweder pro Zeile einen einzelnen Eintrag enthalten, oder die Einträge müssen jeweils mit einem Zeitstempel in einem der folgenden Formate beginnen:

    JJJJ-MM-TT HH:MM:SS<br>M/T/JJJJ HH:MM:SS AM/PM<br>Mon TT, JJJJ HH:MM:SS<br />jjMMtt HH:mm:ss<br />ttMMjj HH:mm:ss<br />MMM t hh:mm:ss<br />tt/MMM/jjjj:HH:mm:ss zzz<br />jjjj-MM-ttTHH:mm:ssK

- Die Protokolldatei darf keine zirkuläre Protokollierung oder Protokollrotation zulassen, bei der die Datei mit neuen Einträgen überschrieben wird.
- Die Protokolldatei muss ASCII- oder UTF-8-Codierung verwenden.  Andere Formate wie UTF-16 werden nicht unterstützt.
- Für Linux wird die Zeitzonenkonvertierung für Zeitstempel in den Protokollen nicht unterstützt.

>[!NOTE]
> Azure Monitor erfasst doppelte Einträge in der Protokolldatei. Allerdings sind die Abfrageergebnisse inkonsistent, wenn die Anzahl angezeigter Filterergebnisse die Ergebnisanzahl übersteigt. Sie müssen unbedingt das Protokoll überprüfen, um festzustellen, ob dieses Verhalten durch die Anwendung, die es erstellt, verursacht wird, und es nach Möglichkeit vor der Erstellung der benutzerdefinierten Protokollsammlungsdefinition beheben.  
>

>[!NOTE]
> Für einen Log Analytics-Arbeitsbereich gelten die folgenden Einschränkungen:
> 
> * Es können nur 500 benutzerdefinierte Protokolle erstellt werden.
> * Eine Tabelle unterstützt nur maximal 500 Spalten. 
> * Die maximale Anzahl von Zeichen für den Spaltennamen ist 500. 
>

>[!IMPORTANT]
>Die benutzerdefinierte Protokollsammlung erfordert, dass die Anwendung, die die Protokolldatei schreibt, den Protokollinhalt regelmäßig auf den Datenträger leert. Der Grund dafür ist, dass die benutzerdefinierte Protokollsammlung darauf basiert, dass Benachrichtigungen über Änderungen am Dateisystem für die Protokolldatei nachverfolgt werden.

## <a name="defining-a-custom-log"></a>Definieren eines benutzerdefinierten Protokolls
Gehen Sie zum Definieren einer benutzerdefinierten Protokolldatei wie folgt vor.  Am Ende dieses Artikels finden Sie eine exemplarische Vorgehensweise für das Hinzufügen eines benutzerdefinierten Protokolls.

### <a name="step-1-open-the-custom-log-wizard"></a>Schritt 1: Öffnen des Assistenten für benutzerdefinierte Protokolle
Mit dem im Azure-Portal ausgeführten Assistenten für benutzerdefinierte Protokolle können Sie ein neues benutzerdefiniertes Protokoll für die Sammlung definieren.

1. Wählen Sie im Azure-Portal die Optionen **Log Analytics-Arbeitsbereiche** > Ihr Arbeitsbereich > **Erweiterte Einstellungen** aus.
2. Klicken Sie auf **Daten** > **Benutzerdefinierte Protokolle**.
3. Standardmäßig werden alle Konfigurationsänderungen automatisch per Push an alle Agents weitergegeben. Bei Linux-Agents wird eine Konfigurationsdatei an den Fluentd-Datensammler gesendet.
4. Klicken Sie auf **Hinzufügen+** , um den Assistenten für benutzerdefinierte Protokolle zu öffnen.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Schritt 2: Hochladen und Analysieren eines Beispielprotokolls
Als Erstes muss ein benutzerdefiniertes Beispielprotokoll hochgeladen werden.  Der Assistent überprüft die Einträge in dieser Datei und zeigt sie zur Überprüfung an.  Azure Monitor verwendet bei der Identifizierung der einzelnen Datensätze das von Ihnen angegebene Trennzeichen.

Das Standardtrennzeichen **Neue Zeile** wird für Protokolldateien verwendet, die pro Zeile jeweils einen einzelnen Eintrag enthalten.  Falls die Zeile mit einer Datums- und Uhrzeitangabe in einem kompatiblen Format beginnt, können Sie als Trennzeichen die Option **Zeitstempel** angeben. Diese Option unterstützt Einträge, die sich über mehrere Zeilen erstrecken.

Bei Verwendung der Zeitstempeloption wird die TimeGenerated-Eigenschaft der einzelnen, in Azure Monitor gespeicherten Datensätze in der Protokolldatei mit der Datums-/Uhrzeitangabe des jeweiligen Eintrags aufgefüllt.  Bei Verwendung eines Trennzeichens vom Typ „Neue Zeile“ wird die TimeGenerated-Eigenschaft mit dem Zeitpunkt (Datum und Uhrzeit) aufgefüllt, zu dem Azure Monitor den Eintrag erfasst hat.

1. Klicken Sie auf **Durchsuchen**, und navigieren Sie zu einer Beispieldatei.  Hinweis: Bei manchen Browsern ist diese Schaltfläche unter Umständen mit **Datei auswählen** beschriftet.
2. Klicken Sie auf **Weiter**.
3. Der Assistent für benutzerdefinierte Protokolle lädt die Datei hoch und führt die ermittelten Datensätze auf.
4. Ändern Sie das Trennzeichen, das zur Identifizierung eines neuen Datensatzes verwendet wird, und wählen Sie das Trennzeichen aus, das für die Einträge in Ihrer Protokolldatei am besten geeignet ist.
5. Klicken Sie auf **Weiter**.

### <a name="step-3-add-log-collection-paths"></a>Schritt 3: Hinzufügen von Protokollsammlungspfaden
Definieren Sie für den Agent mindestens einen Pfad, an dem sich das benutzerdefinierte Protokoll befindet.  Sie können entweder einen bestimmten Pfad und Namen für die Protokolldatei angeben oder einen Pfad mit einem Platzhalter für den Namen verwenden. Dadurch werden Anwendungen unterstützt, die täglich oder bei Erreichen einer bestimmten Dateigröße eine neue Datei erstellen. Sie können auch mehrere Pfade für eine einzelne Protokolldatei angeben.

Ein Beispiel: Angenommen, eine Anwendung erstellt jeden Tag eine Protokolldatei, und das Datum ist jeweils Teil des Dateinamens (etwa „log20100316.txt“). In einem solchen Fall kann beispielsweise ein Muster wie *log\*.txt* verwendet werden, um sämtliche Protokolldateien abzudecken, die nach dem Benennungsschema der Anwendung erstellt werden.

Die folgende Tabelle enthält Musterbeispiele für die Angabe verschiedener Protokolldateien:

| BESCHREIBUNG | `Path` |
|:--- |:--- |
| Alle Dateien auf dem Windows-Agent im Verzeichnis *C:\Logs* mit der Erweiterung „.txt“ |C:\Logs\\\*.txt |
| Alle Dateien auf dem Windows-Agent im Verzeichnis *C:\Logs* mit einem Namen, der mit „log“ beginnt und die Erweiterung „.txt“ besitzt |C:\Logs\log\*.txt |
| Alle Dateien auf dem Linux-Agent im Verzeichnis */var/log/audit* mit der Erweiterung „.txt“ |/var/log/audit/*.txt |
| Alle Dateien auf dem Linux-Agent im Verzeichnis */var/log/audit* mit einem Namen, der mit „log“ beginnt und die Erweiterung „.txt“ besitzt |/var/log/audit/log\*.txt |

1. Wählen Sie „Windows“ oder „Linux“ aus, um anzugeben, welches Pfadformat Sie hinzufügen.
2. Geben Sie den Pfad ein, und klicken Sie auf die Schaltfläche **+** .
3. Wiederholen Sie den Vorgang für jeden zusätzlichen Pfad.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Schritt 4. Angeben eines Namens und einer Beschreibung für das Protokoll
Der angegebene Name wird für den oben beschriebenen Protokolltyp verwendet.  Er endet immer mit „_CL“, um ihn als benutzerdefiniertes Protokoll zu kennzeichnen.

1. Geben Sie einen Namen für das Protokoll ein.  Das Suffix **\_CL** wird automatisch angehängt.
2. Geben Sie ggf. eine **Beschreibung** ein.
3. Klicken Sie auf **Weiter** , um die Definition des benutzerdefinierten Protokolls zu speichern.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Schritt 5: Überprüfen, ob die benutzerdefinierten Protokolle gesammelt werden
Es kann bis zu einer Stunde dauern, bis die ersten Daten aus einem neuen benutzerdefinierten Protokoll in Azure Monitor erscheinen.  Die Sammlung von Einträgen aus Protokollen, die sich am angegebenen Pfad befinden, beginnt, nachdem Sie das benutzerdefinierte Protokoll definiert haben.  Die bei der Erstellung des benutzerdefinierten Protokolls hochgeladenen Einträge werden zwar nicht gespeichert, es werden aber bereits vorhandene Einträge aus den gefundenen Protokolldateien gesammelt.

Sobald Azure Monitor mit der Erfassung von Einträgen aus dem benutzerdefinierten Protokoll begonnen hat, stehen die entsprechenden Datensätze über eine Protokollabfrage zur Verfügung.  Geben Sie in Ihrer Abfrage den Namen, mit dem Sie das benutzerdefinierte Protokoll benannt haben, als **Typ** an.

> [!NOTE]
> Sollte die RawData-Eigenschaft in der Abfrage nicht vorhanden sein, müssen Sie unter Umständen Ihren Browser schließen und wieder öffnen.

### <a name="step-6-parse-the-custom-log-entries"></a>Schritt 6: Analysieren der Einträge des benutzerdefinierten Protokolls
Der gesamte Protokolleintrag wird in einer einzelnen Eigenschaft namens **RawData** gespeichert.  Wahrscheinlich möchten Sie die verschiedenen Einzelinformationen der jeweiligen Einträge auf einzelne Eigenschaften für jeden Datensatz aufteilen. Optionen zum Aufschlüsseln von **RawData** in mehrere Eigenschaften finden Sie unter [Parse text data in Log Analytics](../log-query/parse-text.md) (Analysieren von Textdaten in Log Analytics).

## <a name="removing-a-custom-log"></a>Entfernen eines benutzerdefinierten Protokolls
Gehen Sie im Azure-Portal wie folgt vor, um ein benutzerdefiniertes Protokoll zu entfernen, das Sie zuvor definiert haben:

1. Klicken Sie im Menü **Daten** in den erweiterten **Einstellungen** für Ihren Arbeitsbereich auf **Benutzerdefinierte Protokolle**, um eine Liste mit allen Ihren benutzerdefinierten Protokollen anzuzeigen.
2. Klicken Sie neben dem benutzerdefinierten Protokoll, das Sie entfernen möchten, auf **Entfernen**.

## <a name="data-collection"></a>Datensammlung
Azure Monitor erfasst etwa alle fünf Minuten neue Einträge aus den einzelnen benutzerdefinierten Protokollen.  Der Agent protokolliert für jede Protokolldatei, aus der er Daten sammelt, seine Position.  Wenn der Agent für einen bestimmten Zeitraum offline geht, erfasst Azure Monitor Einträge ab dem Zeitpunkt der letzten Erfassung – unabhängig davon, ob die Einträge erstellt wurden, während der Agent offline war.

Sämtliche Inhalte des Protokolleintrags werden in eine einzelne Eigenschaft namens **RawData** geschrieben.  Methoden zum Aufschlüsseln der einzelnen importierten Protokolleinträge in mehrere Eigenschaften finden Sie unter [Parse text data in Log Analytics](../log-query/parse-text.md) (Analysieren von Textdaten in Log Analytics).

## <a name="custom-log-record-properties"></a>Eigenschaften benutzerdefinierter Protokolldatensätze
Benutzerdefinierte Protokolldatensätze besitzen einen Typ mit dem von Ihnen angegebenen Protokollnamen sowie die Eigenschaften aus der folgenden Tabelle:

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| TimeGenerated |Der Zeitpunkt (Datum und Uhrzeit), zu dem der Datensatz von Azure Monitor erfasst wurde.  Wenn das Protokoll ein zeitbasiertes Trennzeichen verwendet, handelt es sich hierbei um die Zeitangabe aus dem Eintrag. |
| SourceSystem |Die Art des Agents, auf dem das Ereignis gesammelt wurde. <br> OpsManager: Windows-Agent (Direktverbindung oder System Center Operations Manager) <br> Linux: Alle Linux-Agents |
| RawData |Der vollständige Text des gesammelten Eintrags. Wahrscheinlich möchten Sie [diese Daten in einzelne Eigenschaften aufschlüsseln](../log-query/parse-text.md). |
| ManagementGroupName |Name der Verwaltungsgruppe für System Center Operations Manager-Agents.  Bei anderen Agents lautet diese „AOI-\<workspace ID\>“. |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Exemplarische Vorgehensweise zum Hinzufügen eines benutzerdefinierten Protokolls
Der folgende Abschnitt enthält ein Beispiel für die Erstellung eines benutzerdefinierten Protokolls.  Das Beispielprotokoll enthält in jeder Zeile einen einzelnen Eintrag, der jeweils mit einer Datums- und Uhrzeitangabe beginnt, gefolgt von durch Trennzeichen getrennten Feldern für Code, Status und Meldung.  Hier einige Beispieleinträge:

```output
2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2019-08-27 01:38:22 302,Error,Application could not connect to database
2019-08-27 01:31:34 303,Error,Application lost connection to database
```

### <a name="upload-and-parse-a-sample-log"></a>Hochladen und Analysieren eines Beispielprotokolls
Wir stellen eine der Protokolldateien bereit und sehen die Ereignisse, die gesammelt werden.  In diesem Fall kann als Trennzeichen die Option „Neue Zeile“ verwendet werden.  Wenn sich allerdings im Protokoll ein einzelner Eintrag über mehrere Zeilen erstrecken kann, muss ein Trennzeichen vom Typ „Zeitstempel“ verwendet werden.

![Hochladen und Analysieren eines Beispielprotokolls](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Hinzufügen von Protokollsammlungspfaden
Die Protokolldateien befinden sich im Verzeichnis *C:\MyApp\Logs*.  Jeden Tag wird eine neue Datei erstellt, deren Name jeweils das Datum enthält. Dabei wird in das Muster *appJJJJMMTT.log* verwendet.  Eine geeignetes Muster für dieses Protokoll wäre *C:\MyApp\Logs\\\*.log*.

![Protokollsammlungspfad](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Angeben eines Namens und einer Beschreibung für das Protokoll
Wir verwenden den Namen *MyApp_CL* und geben eine **Beschreibung** ein.

![Protokollname](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Überprüfen, ob die benutzerdefinierten Protokolle gesammelt werden
Wir verwenden die einfache Abfrage *MyApp_CL*, um alle Datensätze aus dem gesammelten Protokoll zurückzugeben.

![Protokollabfrage ohne benutzerdefinierte Felder](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Alternativen zu benutzerdefinierten Protokollen
Benutzerdefinierte Protokolle sind zwar hilfreich, wenn Ihre Daten den oben aufgeführten Kriterien entsprechen, Fälle wie die folgenden erfordern jedoch eine andere Strategie:

- Die Daten weisen nicht die erforderliche Struktur auf, z.B. hat der Zeitstempel ein anderes Format.
- Die Protokolldatei erfüllt nicht die Anforderungen, z.B. Dateicodierung oder eine nicht unterstützte Ordnerstruktur.
- Die Daten erfordern vor dem Sammeln eine Vorverarbeitung oder müssen gefiltert werden. 

In Fällen, in denen Ihre Daten nicht mit benutzerdefinierten Protokollen gesammelt werden können, gibt es die folgenden alternativen Strategien:

- Verwenden Sie ein benutzerdefiniertes Skript oder eine andere Methode zum Schreiben von Daten in [Windows-Ereignisse](data-sources-windows-events.md) oder [Syslog](data-sources-syslog.md), die von Azure Monitor gesammelt werden. 
- Senden Sie die Daten mithilfe der [HTTP-Datensammler-API](data-collector-api.md) direkt an Azure Monitor. 

## <a name="next-steps"></a>Nächste Schritte
* Methoden zum Aufschlüsseln der einzelnen importierten Protokolleinträge in mehrere Eigenschaften finden Sie unter [Parse text data in Log Analytics](../log-query/parse-text.md) (Analysieren von Textdaten in Log Analytics).
* Erfahren Sie mehr über [Protokollabfragen](../log-query/log-query-overview.md) zum Analysieren der aus Datenquellen und Lösungen gesammelten Daten.
