---
title: Problembehandlung von Azure Stream Analytics-Ausgaben
description: In diesem Artikel werden Verfahren zur Problembehandlung Ihrer Ausgangsverbindungen in Azure Stream Analytics-Aufträgen beschrieben.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 92cb427149e6e6cbddfb96c6e4488017641e6482
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60761742"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Problembehandlung von Azure Stream Analytics-Ausgaben

Auf dieser Seite werden häufige Probleme mit Ausgangsverbindungen und deren Behandlung und Behebung beschrieben.

## <a name="output-not-produced-by-job"></a>Nicht vom Auftrag erzeugte Ausgabe 
1.  Überprüfen Sie für die einzelnen Ausgaben die Verbindung mit Ausgaben mithilfe der Schaltfläche **Verbindung testen**.

2.  Sehen Sie sich die [**Überwachungsmetriken**](stream-analytics-monitoring.md) auf der Registerkarte **Überwachen** an. Da die Werte aggregiert werden, werden die Metriken mit einer Verzögerung von wenigen Minuten angezeigt.
    - Wenn für „Eingabeereignisse“ ein höherer Wert als „0“ angezeigt wird, kann der Auftrag Eingabedaten lesen. Wenn für „Eingabeereignisse“ ein kleinerer Wert als „0“ angezeigt wird, gehen Sie wie folgt vor:
      - Um festzustellen, ob die Datenquelle gültige Daten enthält, überprüfen Sie diese mithilfe des [Service Bus-Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Diese Prüfung gilt, wenn der Auftrag Event Hub als Eingabe verwendet.
      - Überprüfen Sie, ob das gewünschte Datenserialisierungsformat und die gewünschte Datencodierung verwendet werden.
      - Wenn der Auftrag einen Event Hub verwendet wird, überprüfen Sie, ob der Text der Meldung *Null* lautet.
      
    - Wenn für „Datenkonvertierungsfehler“ ein höherer Wert als „0“ angezeigt wird und sich dieser Wert ständig erhöht, kann dies Folgendes bedeuten:
      - Das Ausgabeereignis entspricht nicht dem Schema der Zielsenke. 
      - Das Ereignisschema stimmt möglicherweise nicht mit dem festgelegten oder erwarteten Schema der Ereignisse in der Abfrage überein.
      - Die Datentypen einiger Felder im Ereignis entsprechen möglicherweise nicht den Erwartungen.
      
    - Wenn für „Laufzeitfehler“ ein höherer Wert als „0“ angezeigt wird, bedeutet das, dass der Auftrag die Daten empfangen kann, bei der Verarbeitung der Abfrage jedoch Fehler generiert werden.
      - Navigieren Sie zur Fehlersuche zu [Überwachungsprotokolle](../azure-resource-manager/resource-group-audit.md) und filtern Sie nach dem Status *Fehler*.
      
    - Wenn für „InputEvents“ ein höherer Wert als „0“ und für „OutputEvents“ der Wert „0“ angezeigt wird, kann dies Folgendes bedeuten:
      - Die Abfrageverarbeitung hat zu null Ausgabeereignissen geführt.
      - Ereignisse oder die jeweiligen Felder sind unter Umständen falsch formatiert, daher wurden bei der Abfrageverarbeitung keine Ausgaben generiert.
      - Im Rahmen des Auftrags können Daten aus Konnektivitäts- oder Authentifizierungsgründen nicht per Push an die Ausgabesenke übertragen werden.
      
    - Für alle zuvor genannten Fehler enthalten die Meldungen der Vorgangsprotokolle weitere Details (einschließlich Details dazu, was geschieht). Ausgenommen sind Fälle, in denen die Abfragelogik alle Ereignisse herausgefiltert hat. Wenn bei der Verarbeitung mehrerer Ereignisse Fehler generiert werden, protokolliert Stream Analytics innerhalb von zehn Minuten die ersten drei Fehlermeldungen desselben Typs in Vorgangsprotokollen. Weitere identische Fehler werden dann mit folgender Meldung unterdrückt: „Es treten zu schnell Fehler auf. Diese werden unterdrückt“.
    
## <a name="job-output-is-delayed"></a>Verzögerung bei der Auftragsausgabe

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

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Warnung vor Schlüsselverletzungen bei der Ausgabe von Azure SQL-Datenbank

Wenn Sie Azure SQL-Datenbank als Ausgabe an einen Stream Analytics-Auftrag konfigurieren, fügt dieser die Datensätze als Masseneintrag in der Zieltabelle hinzu. Im Allgemeinen garantiert Azure Stream Analytics [mindestens eine Übermittlung]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) an die Ausgabesenke. Sie können jedoch auch eine [genau einmalige Übermittlung]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) an die SQL-Ausgabe erreichen, wenn für die SQL-Tabelle eine UNIQUE-Einschränkung definiert wurde. 

Sobald eindeutige Schlüsseleinschränkungen in der SQL-Tabelle festgelegt sind und doppelte Datensätze in die SQL-Tabelle eingefügt werden, entfernt Azure Stream Analytics den doppelten Datensatz. Es teilt die Daten in Batches auf und fügt die Batches rekursiv ein, bis ein einziger doppelter Datensatz gefunden wird. Wenn der Streamingauftrag eine beträchtliche Anzahl von doppelten Zeilen hat, muss dieser Teilen-und-Einfügen-Prozess die Duplikate einzeln ignorieren. Dies ist weniger effizient und zeitaufwendig. Wenn Sie im Aktivitätsprotokoll mehrere Warnmeldungen zu Schlüsselverstößen innerhalb der letzten Stunde finden, ist es äußerst wahrscheinlich, dass Ihre SQL-Ausgabe den gesamten Auftrag verlangsamt. 

Um dieses Problem zu beheben, sollten Sie [den Index konfigurieren]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql), der die Schlüsselverstöße verursacht, indem Sie die Option IGNORE_DUP_KEY aktivieren. Durch Aktivieren dieser Option wird bei Masseneinfügungen das Ignorieren doppelter Werte durch SQL zugelassen. SQL Azure generiert anstelle eines Fehlers einfach eine Warnung. Azure Stream Analytics gibt daraufhin keine Fehler zu Primärschlüsselverstößen mehr zurück.

Beachten Sie die folgenden Beobachtungen, wenn Sie IGNORE_DUP_KEY für mehrere Typen von Indizes konfigurieren:

* IGNORE_DUP_KEY kann nicht für eine PRIMARY KEY- oder UNIQUE-Einschränkung festgelegt werden, die ALTER INDEX verwendet. Sie müssen stattdessen den Index löschen und neu erstellen.  
* Sie können die Option IGNORE_DUP_KEY mit ALTER INDEX für einen eindeutigen Index festlegen, wenn sich dieser von der PRIMARY KEY-/UNIQUE-Einschränkung unterscheidet und mit einer CREATE INDEX- oder INDEX-Definition erstellt wurde.  
* IGNORE_DUP_KEY gilt nicht für Spaltenspeicherindizes, da für solche Indizes keine Eindeutigkeit erzwungen werden kann.  

## <a name="get-help"></a>Hier erhalten Sie Hilfe

Um Hilfe zu erhalten, nutzen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
