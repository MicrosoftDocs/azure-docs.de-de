---
title: Behandlung allgemeiner Probleme in Azure Stream Analytics
description: In diesem Artikel werden einige allgemeine Probleme in Azure Stream Analytics und Schritte beschrieben, um diese Probleme zu beheben.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 0191c56e1140870b1710b48c4fa1189fd92a337b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61362115"
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Häufig auftretende Probleme bei Stream Analytics und Schritten zur Problembehandlung

## <a name="troubleshoot-malformed-input-events"></a>Problembehandlung bei falsch formatierten Eingabeereignissen

 Serialisierungsprobleme werden verursacht, wenn der Eingabedatenstrom Ihres Stream Analytics-Auftrags falsch formatierte Nachrichten enthält. Beispielsweise kann eine falsch formatierte Nachricht durch eine fehlende Klammer oder eine fehlende geschweifte Klammer in einem JSON-Objekt oder ein falsches Zeitstempelformat in einem Zeitfeld verursacht werden. 
 
 Wenn ein Stream Analytics-Auftrag eine falsch formatierte Nachricht aus einer Eingabe empfängt, wird diese gelöscht und der Benutzer mit einer Warnung benachrichtigt. Ein Warnsymbol wird auf der Kachel **Eingaben** des Stream Analytics-Auftrags angezeigt (dieses Warnzeichen wird so lange angezeigt, wie sich der Auftrag im Ausführungsstatus befindet):

![Kachel „Eingaben“ im Azure Stream Analytics-Dashboard](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Weitere Informationen können Sie sehen, wenn Sie die Diagnoseprotokolle aktivieren, um Details zur Warnung anzuzeigen. Bei falsch formatierten Eingabeereignissen enthalten die Ausführungsprotokolle einen Eintrag mit der Meldung, die etwa wie folgt aussieht: „Meldung: Die Eingabeereignisse aus Ressource \<blob URI> konnten nicht als JSON deserialisiert werden.“ 

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

1. Navigieren Sie zur Eingabekachel, und klicken Sie darauf, um Warnungen anzuzeigen.

2. Auf der Kachel „Eingabedetails“ werden Warnungen mit Details zum Problem angezeigt. Nachfolgend sehen Sie ein Beispiel für eine Warnmeldung. Diese Warnmeldung enthält die Partition, den Offset und die Sequenznummern mit falsch formatierten JSON-Daten. 

   ![Eingabewarnmeldung mit Offset](media/stream-analytics-malformed-events/warning-message-with-offset.png)

3. Führen Sie zum Abrufen der JSON-Daten mit falschem Format den Code „CheckMalformedEvents.cs“ aus. In diesem Beispiel finden Sie im [GitHub-Beispielrepository](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Dieser Code liest die Partitions-ID und den Offset und gibt die Daten aus dem Offset aus. 

4. Nach dem Lesen der Daten können Sie das Serialisierungsformat analysieren und korrigieren.

5. Sie können auch [Ereignisse von einem IoT Hub mit dem Service Bus-Explorer lesen](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="delayed-output"></a>Verzögerte Ausgabe

### <a name="first-output-is-delayed"></a>Verzögerung bei der ersten Ausgabe
Wenn ein Stream Analytics-Auftrag gestartet wird, werden die Eingabeereignisse gelesen, aber es kann unter bestimmten Umständen zu einer Verzögerung bei der Ausgabe kommen.

Große Zeitwerte in zeitlichen Abfrageelementen können zur Ausgabeverzögerung beitragen. Um eine korrekte Ausgabe für große Zeitfenster zu erzeugen, startet der Streamingauftrag mit dem Lesen der Daten vom spätesten möglichen Zeitpunkt (bis zu sieben Tage her), um das Zeitfenster zu füllen. Während dieser Zeit wird keine Ausgabe erzeugt, bis das Auslesen der ausstehenden Eingabeereignisse abgeschlossen ist. Dieses Problem kann auftreten, wenn das System ein Upgrade der Streamingaufträge ausführt und damit den Auftrag neu startet. Solche Upgrades finden in der Regel alle paar Monate statt. 

Formulieren Sie Ihre Stream Analytics-Abfrage daher nach Ihrem Ermessen. Wenn Sie ein großes Zeitfenster (mehr als mehrere Stunden, bis zu sieben Tage) für zeitliche Elemente in der Abfragesyntax des Auftrags verwenden, kann dies zu einer Verzögerung bei der ersten Ausgabe beim Start oder Neustart des Auftrags führen.  

Eine Lösung für diese Art der ersten Ausgabeverzögerung ist die Verwendung von Abfrageparallelisierungsmethoden (Partitionierung der Daten) oder das Hinzufügen weiterer Streamingeinheiten, um den Durchsatz zu verbessern, bis der Auftrag aufgeholt wird.  Weitere Informationen finden Sie unter [Überlegungen beim Erstellen von Stream Analytics-Aufträgen](stream-analytics-concepts-checkpoint-replay.md).

Diese Faktoren haben Auswirkungen auf die Schnelligkeit der ersten Ausgabe, die erzeugt wird:

1. Verwenden von Aggregaten im Fenstermodus (GROUP BY von rollierenden, springenden und gleitenden Fenstern)
   - Für Aggregate von rollierenden oder springenden Fenstern werden die Ergebnisse am Ende des Fensterzeitraums generiert. 
   - Für ein gleitendes Fenster werden die Ergebnisse generiert, wenn ein Ereignis in das gleitende Fenster eintritt oder dieses verlässt. 
   - Wenn Sie eine große Fenstergröße (> 1 Stunde) planen, ist es am besten, wenn Sie sich für ein springendes oder gleitendes Fenster entscheiden, damit Sie die Ausgabe häufiger sehen können.

2. Verwenden von temporalen Joins (JOIN mit DATEDIFF-Funktion)
   - Übereinstimmungen werden generiert, sobald beide Seiten der übereinstimmenden Ereignisse eintreten.
   - Daten ohne Übereinstimmung (LEFT OUTER JOIN) werden am Ende des DATEDIFF-Fensters für jedes Ereignis auf der linken Seite generiert.

3. Verwenden von temporalen Analysefunktionen (ISFIRST, LAST und LAG mit LIMIT DURATION-Funktion)
   - Bei analytischen Funktionen wird die Ausgabe für jedes Ereignis generiert. Es gibt keine Verzögerung.

### <a name="output-falls-behind"></a>Zurückfallende Ausgabe
Wenn Sie während der normalen Ausführung des Auftrags feststellen, dass die Ausgabe des Auftrags hinterherhinkt (immer längere Wartezeit), können Sie die Grundursachen ermitteln, indem Sie diese Faktoren daraufhin untersuchen:
- Ob die Downstreamsenke gedrosselt wird.
- Ob die Upstreamquelle gedrosselt wird.
- Ob die Verarbeitungslogik in der Abfrage rechenintensiv ist.

Um diese Details anzuzeigen, wählen Sie im Azure-Portal den Streamingauftrag und das **Auftragsdiagramm** aus. Für jede Eingabe gibt es eine Backlogereignismetrik pro Partition. Wenn die Backlogereignismetrik weiter ansteigt, ist dies ein Indikator für begrenzte Systemressourcen. Gründe hierfür können eine Drosselung der Ausgabesenke oder eine hohe CPU-Auslastung sein. Weitere Informationen zur Verwendung des Auftragsdiagramms finden Sie unter [Datengesteuertes Debuggen mithilfe des Auftragsdiagramms](stream-analytics-job-diagram-with-metrics.md).

## <a name="handle-duplicate-records-in-azure-sql-database-output"></a>Handhaben doppelter Datensätze in der Azure SQL-Datenbank-Ausgabe

Wenn Sie Azure SQL-Datenbank als Ausgabe an einen Stream Analytics-Auftrag konfigurieren, fügt dieser die Datensätze als Masseneintrag in der Zieltabelle hinzu. Im Allgemeinen garantiert Azure Stream Analytics [mindestens eine Übermittlung]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) an die Ausgabesenke. Sie können jedoch auch eine [genau einmalige Übermittlung]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) an die SQL-Ausgabe erreichen, wenn für die SQL-Tabelle eine UNIQUE-Einschränkung definiert wurde. 

Sobald eindeutige Schlüsseleinschränkungen in der SQL-Tabelle festgelegt sind und doppelte Datensätze in die SQL-Tabelle eingefügt werden, entfernt Azure Stream Analytics den doppelten Datensatz. Es teilt die Daten in Batches auf und fügt die Batches rekursiv ein, bis ein einziger doppelter Datensatz gefunden wird. Wenn der Streamingauftrag eine beträchtliche Anzahl von doppelten Zeilen hat, muss dieser Teilen-und-Einfügen-Prozess die Duplikate einzeln ignorieren. Dies ist weniger effizient und zeitaufwendig. Wenn Sie im Aktivitätsprotokoll mehrere Warnmeldungen zu Schlüsselverstößen innerhalb der letzten Stunde finden, ist es äußerst wahrscheinlich, dass Ihre SQL-Ausgabe den gesamten Auftrag verlangsamt. 

Um dieses Problem zu beheben, sollten Sie [den Index konfigurieren]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql), der die Schlüsselverstöße verursacht, indem Sie die Option IGNORE_DUP_KEY aktivieren. Durch Aktivieren dieser Option wird bei Masseneinfügungen das Ignorieren doppelter Werte durch SQL zugelassen. SQL Azure generiert anstelle eines Fehlers einfach eine Warnung. Azure Stream Analytics gibt daraufhin keine Fehler zu Primärschlüsselverstößen mehr zurück.

Beachten Sie die folgenden Beobachtungen, wenn Sie IGNORE_DUP_KEY für mehrere Typen von Indizes konfigurieren:

* IGNORE_DUP_KEY kann nicht für eine PRIMARY KEY- oder UNIQUE-Einschränkung festgelegt werden, die ALTER INDEX verwendet. Sie müssen stattdessen den Index löschen und neu erstellen.  
* Sie können die Option IGNORE_DUP_KEY mit ALTER INDEX für einen eindeutigen Index festlegen, wenn sich dieser von der PRIMARY KEY-/UNIQUE-Einschränkung unterscheidet und mit einer CREATE INDEX- oder INDEX-Definition erstellt wurde.  
* IGNORE_DUP_KEY gilt nicht für Spaltenspeicherindizes, da für solche Indizes keine Eindeutigkeit erzwungen werden kann.  

## <a name="next-steps"></a>Nächste Schritte
* [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
