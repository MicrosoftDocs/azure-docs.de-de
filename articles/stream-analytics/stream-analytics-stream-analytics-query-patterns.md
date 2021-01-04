---
title: Allgemeine Abfragemuster in Azure Stream Analytics
description: In diesem Artikel werden verschiedene allgemeine Abfragemuster und -entwürfe beschrieben, die nützlich in Azure Stream Analytics-Aufträgen sind.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/18/2019
ms.custom: devx-track-js
ms.openlocfilehash: f0c5363cfec42ba78ee6c41a1970211518b74a71
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127534"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Allgemeine Abfragemuster in Azure Stream Analytics

Abfragen in Azure Stream Analytics werden in einer SQL-ähnlichen Abfragesprache ausgedrückt. Diese Sprachkonstrukte sind im Handbuch [Referenz zur Stream Analytics-Abfragesprache](/stream-analytics-query/stream-analytics-query-language-reference) dokumentiert. 

Der Abfrageentwurf kann einfache Pass-Through-Logik zum Verschieben von Ereignisdaten aus einem Eingabestream in einen Ausgabedatenspeicher ausdrücken oder umfangreiche Musterabgleiche und temporale Analysen durchführen, um Aggregate in verschiedenen Zeitfenstern zu berechnen. Dieser Vorgang wird im Leitfaden [Erstellen einer IoT-Lösung mithilfe von Stream Analytics](stream-analytics-build-an-iot-solution-using-stream-analytics.md) erläutert. Sie können Daten aus mehreren Eingaben verknüpfen, um Streamingereignisse zu kombinieren. Zudem können Sie Suchvorgänge für statische Verweisdaten ausführen, um die Ereigniswerte zu ergänzen. Sie können auch Daten in mehrere Ausgaben schreiben.

Dieser Artikel zeigt anhand von Praxisbeispielen Lösungen für mehrere weit verbreitete Abfragemuster.

## <a name="supported-data-formats"></a>Unterstützte Datenformate

Azure Stream Analytics unterstützt die Verarbeitung von Ereignissen in den Datenformaten CSV, JSON und Avro.

Sowohl JSON als auch Avro können komplexe Typen enthalten, z.B. geschachtelte Objekte (Datensätze) oder Arrays. Weitere Informationen zum Umgang mit diesen komplexen Datentypen finden Sie im Artikel zum Thema [Analysieren von JSON- und AVRO-Daten in Azure Stream Analytics](stream-analytics-parsing-json.md).

## <a name="send-data-to-multiple-outputs"></a>Senden von Daten an mehrere Ausgaben

Für die Ausgabe von Daten an unterschiedliche Ausgabesenken können mehrere **SELECT** -Anweisungen verwendet werden. Beispielsweise kann eine **SELECT** -Anweisung eine auf dem Schwellenwert basierende Warnung ausgeben, eine andere kann hingegen Ereignis in einen Blobspeicher ausgeben.

**Eingabe** :

| Make | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**ArchiveOutput-Ausgabe** :

| Make | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**AlertOutput-Ausgabe** :

| Make | Time | Anzahl |
| --- | --- | --- |
| Make2 |2015-01-01T00:00:10.0000000Z |3 |

**Query** (Abfrage):

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count]
INTO
    AlertOutput
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING
    [Count] >= 3
```

Mit der **INTO** -Klausel wird Stream Analytics mitgeteilt, in welche Ausgabe die Daten geschrieben werden sollen. Die erste **SELECT** -Anweisung definiert eine Pass-Through-Abfrage, die Daten aus der Eingabe empfängt und sie an die Ausgabe mit dem Namen **ArchiveOutput** sendet. Die zweite Abfrage führt eine einfache Aggregation und Filterung durch, bevor die Ergebnisse an ein nachgeschaltetes Warnsystem namens **AlertOutput** gesendet wird.

Beachten Sie, dass mit der **WITH** -Klausel mehrere Unterabfrageblöcke definiert werden können. Diese Option hat den Vorteil, dass weniger Leser für die Eingabequelle geöffnet werden müssen.

**Query** (Abfrage):

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

Weitere Informationen finden Sie unter [**WITH** -Klausel](/stream-analytics-query/with-azure-stream-analytics).

## <a name="simple-pass-through-query"></a>Einfache Pass-Through-Abfrage

Eine einfache Pass-Through-Abfrage kann verwendet werden, um die Daten des Eingabestreams in die Ausgabe zu kopieren. Wenn z. B. ein Stream mit Echtzeit-Fahrzeuginformationen für eine Buchstabenanalyse in einer SQL-Datenbank gespeichert werden muss, führt eine einfache Pass-Through-Abfrage den Auftrag aus.

**Eingabe** :

| Make | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Ausgabe** :

| Make | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Query** (Abfrage):

```SQL
SELECT
    *
INTO Output
FROM Input
```

Eine **SELECT** *-Abfrage projiziert alle Felder eines eingehenden Ereignisses und sendet sie an die Ausgabe. Mit **SELECT-** können Sie auf dieselbe Weise nur erforderliche Felder aus der Eingabe projizieren. Wenn wie in diesem Beispiel nur die Felder *Make* und *Time* eines Fahrzeugs die einzigen erforderlichen Felder sind, die gespeichert werden müssen, können diese Felder in der **SELECT** -Anweisung angegeben werden.

**Eingabe** :

| Make | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000Z |1500 |

**Ausgabe** :

| Make | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:04.0000000Z |

**Query** (Abfrage):

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```

## <a name="string-matching-with-like-and-not-like"></a>Zeichenfolgenabgleich mit LIKE und NOT LIKE

Mit **LIKE** und **NOT LIKE** kann verifiziert werden, ob ein Feld mit einem bestimmten Muster übereinstimmt. Beispielsweise kann ein Filter erstellt werden, um nur die Nummernschilder zurückzugeben, die mit dem Buchstaben „A“ beginnen und mit der Zahl 9 enden.

**Eingabe** :

| Make | License_plate | Time |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Ausgabe** :

| Make | License_plate | Time |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Query** (Abfrage):

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

Verwenden Sie die **LIKE** -Anweisung, um den Feldwert von **License_plate** zu überprüfen. Dieser sollte mit „A“ beginnen, gefolgt von einer leeren Zeichenfolge oder einer Zeichenfolge mit einer beliebigen Anzahl von Zeichen und mit „9“ enden.

## <a name="calculation-over-past-events"></a>Berechnung für vergangene Ereignisse

Mit der **LAG** -Funktion können vergangene Ereignisse innerhalb eines Zeitfensters betrachtet und mit dem aktuellen Ereignis verglichen werden. Beispielsweise kann die aktuelle Fahrzeugmarke ausgegeben werden, wenn sie sich von der des letzten Fahrzeugs unterscheidet, das die Mautstation passiert hat.

**Eingabe** :

| Make | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |

**Ausgabe** :

| Make | Time |
| --- | --- |
| Make2 |2015-01-01T00:00:02.0000000Z |

**Query** (Abfrage):

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

Mit **LAG** können Sie einen Blick in den Eingabestream des vorherigen Ereignisses werfen, den Wert *Make* abrufen, ihn mit dem Wert *Make* des aktuellen Ereignisses vergleichen und das Ereignis ausgeben.

Weitere Informationen finden Sie unter [**LAG**](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>Zurückgeben des letzten Ereignisses in einem Zeitfenster

Da Ereignisse vom System in Echtzeit verarbeitet werden, gibt es keine Funktion, die feststellen kann, ob ein Ereignis für dieses Zeitfenster als letztes eintrifft. Dazu muss der Eingabestream mit einem anderen Stream verbunden werden, bei dem der Zeitpunkt eines Ereignisses die maximale Zeit für alle Ereignisse in diesem Fenster ist.

**Eingabe** :

| License_plate | Make | Time |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**Ausgabe** :

| License_plate | Make | Time |
| --- | --- | --- |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**Query** (Abfrage):

```SQL
WITH LastInWindow AS
(
    SELECT 
        MAX(Time) AS LastEventTime
    FROM 
        Input TIMESTAMP BY Time
    GROUP BY 
        TumblingWindow(minute, 10)
)

SELECT 
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

Der erste Schritt in der Abfrage sucht den maximalen Zeitstempel in 10-Minuten-Fenstern, d. h. den Zeitstempel des letzten Ereignisses für dieses Fenster. Im zweiten Schritt werden die Ergebnisse der ersten Abfrage mit dem ursprünglichen Stream zusammengeführt, um nach dem Ereignis zu suchen, das dem letzten Zeitstempel des jeweiligen Zeitfensters entspricht. 

**DATEDIFF** ist eine datumsspezifische Funktion, die den Zeitunterschied zwischen zwei DateTime-Feldern vergleicht und zurückgibt. Weitere Informationen finden Sie unter [date-Funktionen](/stream-analytics-query/date-and-time-functions-azure-stream-analytics).

Weitere Informationen zum Verknüpfen von Streams finden Sie unter [**JOIN**](/stream-analytics-query/join-azure-stream-analytics).

## <a name="data-aggregation-over-time"></a>Datenaggregation im Zeitverlauf

Um Informationen über ein Zeitfenster zu berechnen, können Daten zusammen aggregiert werden. In diesem Beispiel wird für jede spezifische Fahrzeugmarke die Anzahl während der letzten 10 Sekunden berechnet.

**Eingabe** :

| Make | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000Z |1500 |

**Ausgabe** :

| Make | Anzahl |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

**Query** (Abfrage):

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Diese Aggregation gruppiert die Fahrzeuge nach dem Wert für *Make* und zählt sie alle 10 Sekunden. Die Ausgabe enthält die Werte für *Make* und *Count* für die Fahrzeuge, die die Mautstation passiert haben.

TumblingWindow ist eine Fensterfunktion zum Gruppieren von Ereignissen. Eine Aggregation kann auf alle gruppierten Ereignisse angewendet werden. Weitere Informationen finden Sie unter [Fensterfunktionen](stream-analytics-window-functions.md).

Weitere Informationen zur Aggregation finden Sie unter [Aggregatfunktionen](/stream-analytics-query/aggregate-functions-azure-stream-analytics).

## <a name="periodically-output-values"></a>Regelmäßige Ausgabewerte

Im Falle von unregelmäßigen oder fehlenden Ereignissen kann aus einer Eingabe mit wenigen Daten eine regelmäßige Intervallausgabe generiert werden. Generieren Sie z.B. alle 5 Sekunden ein Ereignis, das den zuletzt angezeigten Datenpunkt meldet.

**Eingabe** :

| Time | Wert |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Ausgabe (erste zehn Zeilen)** :

| Window_end | Last_event.Time | Last_event.Value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**Query** (Abfrage):

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

Diese Abfrage generiert alle fünf Sekunden Ereignisse und gibt das letzte zuvor empfangene Ereignis aus. Die Dauer eines **HOPPINGWINDOW** legt fest, wie weit die Abfrage zurückreicht, um das letzte Ereignis zu suchen.

Weitere Informationen finden Sie unter [Springendes Fenster](/stream-analytics-query/hopping-window-azure-stream-analytics).

## <a name="correlate-events-in-a-stream"></a>Korrelieren von Ereignissen in einem Stream

Das Korrelieren von Ereignissen im gleichen Stream kann durch die Betrachtung vergangener Ereignisse mithilfe der **LAG** -Funktion erfolgen. So kann z. B. jedes Mal eine Ausgabe generiert werden, wenn zwei Fahrzeuge derselben *Marke* in den letzten 90 Sekunden nacheinander die Mautstation passiert haben.

**Eingabe** :

| Make | License_plate | Time |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Make1 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make2 |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Make1 |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Ausgabe** :

| Make | Time | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Query** (Abfrage):

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

Die **LAG** -Funktion kann in den Eingabestream des vorherigen Ereignisses schauen und den *Make* -Wert abrufen, um diesen mit dem *Make* -Wert des aktuellen Ereignisses zu vergleichen.  Sobald die Bedingung erfüllt ist, können die Daten aus dem vorherigen Ereignis mithilfe von **LAG** in die **SELECT** -Anweisung projiziert werden.

Weitere Informationen finden Sie unter [LAG](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="detect-the-duration-between-events"></a>Ermitteln der Dauer zwischen Ereignissen

Die Dauer eines Ereignisses kann berechnet werden, indem das letzte Startereignis betrachtet wird, sobald ein Endereignis empfangen wurde. Diese Abfrage kann nützlich sein, um die Zeit zu ermitteln, die ein Benutzer auf einer Seite oder mit einer Funktion verbringt.

**Eingabe** :  

| Benutzer | Funktion | Ereignis | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Start |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |Ende |2015-01-01T00:00:08.0000000Z |

**Ausgabe** :  

| Benutzer | Funktion | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Query** (Abfrage):

```SQL
SELECT
    [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
FROM input TIMESTAMP BY Time
WHERE
    Event = 'end'
```

Die **LAST** -Funktion kann verwendet werden, um das letzte Ereignis innerhalb einer bestimmten Bedingung abzurufen. In diesem Beispiel ist die Bedingung ein Ereignis vom Typ „Start“, das die Suche nach **PARTITION BY** -Benutzer und -Funktion partitioniert. Auf diese Weise werden alle Benutzer und Funktionen bei der Suche nach dem Startereignis unabhängig voneinander behandelt. **LIMIT DURATION** begrenzt die zeitliche Rückwärtssuche auf 1 Stunde zwischen dem End- und dem Startereignis.

## <a name="count-unique-values"></a>Zählen eindeutiger Werte

**COUNT** und **DISTINCT** zählen die Anzahl eindeutiger Feldwerte, die im Stream innerhalb eines bestimmten Zeitraums vorkommen. Es kann eine Abfrage erstellt werden, um zu berechnen, wie viele individuelle *Fahrzeugmarken* in einem Zeitraum von zwei Sekunden die Mautstation passieren.

**Eingabe** :

| Make | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**Ausgabe:**

| Count_make | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Abfrage:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**COUNT (DISTINCT Make)** gibt die Anzahl der unterschiedlichen Werte der Spalte **Make** innerhalb eines Zeitfensters zurück.
Weitere Informationen finden Sie unter [**COUNT** -Aggregatfunktion](/stream-analytics-query/count-azure-stream-analytics).

## <a name="retrieve-the-first-event-in-a-window"></a>Abrufen des ersten Ereignisses in einem Zeitfenster

**IsFirst** kann verwendet werden, um das erste Ereignis in einem Zeitfenster abzurufen. Beispiel: Ausgabe der ersten Fahrzeuginformationen alle 10 Minuten.

**Eingabe** :

| License_plate | Make | Time |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**Ausgabe** :

| License_plate | Make | Time |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |

**Query** (Abfrage):

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**IsFirst** kann die Daten auch partitionieren und das erste Ereignis für jede spezifische *Fahrzeugmarke* berechnen, die in jedem 10-Minuten-Intervall gefunden wird.

**Ausgabe** :

| License_plate | Make | Time |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**Query** (Abfrage):

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

Weitere Informationen finden Sie unter [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics).

## <a name="remove-duplicate-events-in-a-window"></a>Entfernen duplizierter Ereignisse in einem Fenster

Wenn Sie einen Vorgang ausführen, wie z. B. die Berechnung von Durchschnittswerten für Ereignisse in einem bestimmten Zeitfenster, müssen duplizierte Ereignisse herausgefiltert werden. Im folgenden Beispiel ist das zweite Ereignis ein Duplikat des ersten.

**Eingabe** :  

| deviceId | Time | attribute | Wert |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatur |50 |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatur |50 |
| 2 |2018-07-27T00:00:01.0000000Z |Temperatur |40 |
| 1 |2018-07-27T00:00:05.0000000Z |Temperatur |60 |
| 2 |2018-07-27T00:00:05.0000000Z |Temperatur |50 |
| 1 |2018-07-27T00:00:10.0000000Z |Temperatur |100 |

**Ausgabe** :  

| AverageValue | deviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Query** (Abfrage):

```SQL
With Temp AS (
SELECT
    COUNT(DISTINCT Time) AS CountTime,
    Value,
    DeviceId
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Value,
    DeviceId,
    SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**COUNT (DISTINCT Time)** gibt die Anzahl der unterschiedlichen Werte der Spalte „Time“ innerhalb eines Zeitfensters zurück. Die Ausgabe des ersten Schritts kann dann verwendet werden, um den Durchschnitt pro Gerät zu berechnen, indem Sie Duplikate entfernen.

Weitere Informationen finden Sie unter [COUNT (DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics).

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>Logik für verschiedene Fälle/Werte (CASE-Anweisungen)

**CASE** -Anweisungen können basierend auf einem bestimmten Kriterium verschiedene Berechnungen für verschiedene Felder durchführen. Weisen Sie beispielsweise Fahrzeuge von *Make1* Spur „A“ zu und jegliche anderen Marken Spur „B“.

**Eingabe** :

| Make | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**Ausgabe** :

| Make |Dispatch_to_lane | Time |
| --- | --- | --- |
| Make1 |„A“ |2015-01-01T00:00:01.0000000Z |
| Make2 |„B“ |2015-01-01T00:00:02.0000000Z |

**Lösung** :

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

Der **CASE** -Ausdruck vergleicht einen Ausdruck mit einem Set von einfachen Ausdrücken, um das Ergebnis zu ermitteln. In diesem Beispiel werden Fahrzeuge von *Make1* an Spur „A“ weitergeleitet, Fahrzeuge einer anderen beliebigen anderen Marke werden Spur „B“ zugewiesen.

Weitere Informationen finden Sie unter [CASE-Ausdruck](/stream-analytics-query/case-azure-stream-analytics).

## <a name="data-conversion"></a>Datenkonvertierung

Daten können mithilfe der **CAST** -Methode in Echtzeit umgewandelt werden. Beispielsweise kann das Fahrzeuggewicht vom Typ **nvarchar(max)** in den Typ **bigint** umgewandelt und in einer numerischen Berechnung verwendet werden.

**Eingabe** :

| Make | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Ausgabe** :

| Make | Weight |
| --- | --- |
| Make1 |3000 |

**Query** (Abfrage):

```SQL
SELECT
    Make,
    SUM(CAST(Weight AS BIGINT)) AS Weight
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Verwenden Sie eine **CAST** -Anweisung, um den jeweiligen Datentyp anzugeben. Die Liste der unterstützten Datentypen finden Sie unter [Datentypen (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

Weitere Informationen finden Sie unter [Funktionen für die Datenkonvertierung](/stream-analytics-query/conversion-functions-azure-stream-analytics).

## <a name="detect-the-duration-of-a-condition"></a>Ermitteln der Dauer einer Bedingung

Für Bedingungen, die mehrere Ereignisse umfassen, kann mit der **LAG** -Funktion die Dauer der Bedingung bestimmt werden. Beispiel: Aufgrund eines Fehlers wurde für alle Fahrzeuge ein falsches Gewicht (über 20.000 Pfund) erfasst. Nun soll ermittelt werden, wie lange dieser Fehler aufgetreten ist.

**Eingabe** :

| Make | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |2000 |
| Make2 |2015-01-01T00:00:02.0000000Z |25000 |
| Make1 |2015-01-01T00:00:03.0000000Z |26000 |
| Make2 |2015-01-01T00:00:04.0000000Z |25000 |
| Make1 |2015-01-01T00:00:05.0000000Z |26000 |
| Make2 |2015-01-01T00:00:06.0000000Z |25000 |
| Make1 |2015-01-01T00:00:07.0000000Z |26000 |
| Make2 |2015-01-01T00:00:08.0000000Z |2000 |

**Ausgabe** :

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Query** (Abfrage):

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
Der erste **SELECT** -Anweisung korreliert die aktuelle Gewichtungsmessung mit der vorherigen Messung und projiziert sie zusammen mit der aktuellen Messung. Die zweite **SELECT** -Anweisung geht zum letzten Ereignis zurück, bei dem der Wert für *previous_weight* kleiner als 20.000 ist, das aktuelle Gewicht kleiner als 20.000 ist und der Wert für *previous_weight* des aktuellen Ereignisses größer als 20.000 war.

„End_fault“ ist das aktuelle nicht fehlerhafte Ereignis, bei dem das vorherige Ereignis fehlerhaft war, und „Start_fault“ ist das letzte nicht fehlerhafte Ereignis vor diesem Ereignis.

## <a name="process-events-with-independent-time-substreams"></a>Verarbeiten von Ereignissen mit unabhängiger Zeit (Teilstreams)

Eintreffen von Ereignissen mit Verzögerung oder in falscher Reihenfolge aufgrund von Uhrabweichungen zwischen Ereignisproduzenten oder Partitionen bzw. Netzwerklatenz.
Im folgenden Beispiel liegt die Geräteuhr für *TollID* 2 fünf Sekunden hinter *TollID* 1 und die Geräteuhr für *TollID* 3 zehn Sekunden hinter *TollID* 1. Eine Berechnung kann unabhängig für jede Mautstation erfolgen, wobei nur die eigenen Uhrendaten als Zeitstempel berücksichtigt werden.

**Eingabe** :

| LicensePlate | Make | Time | TollId |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Make2 |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Make1 |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Make2 |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Make1 |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Make4 |2015-07-27T00:00:07.0000000Z | 3 |

**Ausgabe** :

| TollId | Anzahl |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Query** (Abfrage):

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

Die **TIMESTAMP BY OVER** -Klausel betrachtet die Zeitachse jedes Geräts unabhängig voneinander mit Teilstreams. Die Ausgabeereignisse für jede *TollID* werden beim Berechnen generiert. Das bedeutet, dass die Ereignisse gemäß der jeweiligen *TollID* sortiert werden. Sie werden nicht neu angeordnet, als würden alle Geräte dieselbe Uhrzeit anzeigen.

Weitere Informationen finden Sie unter [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering).

## <a name="session-windows"></a>Sitzungsfenster

Ein Sitzungsfenster ist ein Fenster, das beim Auftreten von Ereignissen immer größer wird und sich für die Berechnung schließt, wenn nach einer bestimmten Zeitspanne kein Ereignis empfangen wird oder wenn das Fenster seine maximale Dauer erreicht.
Dieses Fenster ist besonders nützlich, wenn Benutzerinteraktionsdaten berechnet werden. Ein Fenster beginnt, wenn ein Benutzer mit dem System zu interagieren beginnt, und schließt sich, wenn keine weiteren Ereignisse mehr registriert werden, d. h. wenn der Benutzer die Interaktion beendet hat.
Wenn ein Benutzer beispielsweise mit einer Webseite interagiert, auf der die Anzahl der Klicks protokolliert wird, kann mithilfe eines Sitzungsfensters bestimmt werden, wie lange der Benutzer mit der Website interagiert hat.

**Eingabe** :

| User_id | Time | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000Z | "www.example.com/e.html" |

**Ausgabe** :

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | 2017-01-26T00:01:10.0000000Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000Z | 2017-01-26T00:01:15.0000000Z | 20 |

**Query** (Abfrage):

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

Die **SELECT** -Anweisung projiziert die für die Benutzerinteraktion relevanten Daten zusammen mit der Dauer der Interaktion. Die Daten werden nach Benutzer und nach einem **SessionWindow** gruppiert, das sich schließt, wenn innerhalb von 1 Minute keine Interaktion stattfindet. Die maximale Fenstergröße beträgt 60 Minuten.

Weitere Informationen zu **SessionWindow** finden Sie unter [Sitzungsfenster](/stream-analytics-query/session-window-azure-stream-analytics).

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Spracherweiterbarkeit mit benutzerdefinierten Funktionen in JavaScript und C#

Die Azure Stream Analytics-Abfragesprache kann mithilfe von benutzerdefinierten Funktionen erweitert werden, die in JavaScript oder C# geschrieben werden. Benutzerdefinierte Funktionen (User Defined Functions, UDF) sind benutzerdefinierte/komplexe Berechnungen, die nicht ohne weiteres mithilfe der **SQL** -Sprache ausgedrückt werden können. Diese UDFs können einmalig definiert und mehrmals innerhalb einer Abfrage verwendet werden. Eine UDF kann beispielsweise zum Konvertieren eines hexadezimalen Werts *nvarchar (max)* in einen Wert *bigint* verwendet werden.

**Eingabe** :

| Device_id | HexValue |
| --- | --- |
| 1 | "B4" |
| 2 | "11B" |
| 3 | "121" |

**Ausgabe** :

| Device_id | Decimal |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

Die benutzerdefinierte Funktion berechnet den Wert *bigint* aus dem HexValue für jedes verarbeitete Ereignis.

Weitere Informationen finden Sie unter [JavaScript](./stream-analytics-javascript-user-defined-functions.md) und [C#](./stream-analytics-edge-csharp-udf.md).

## <a name="advanced-pattern-matching-with-match_recognize"></a>Erweiterter Musterabgleich mit MATCH_RECOGNIZE

**MATCH_RECOGNIZE** ist ein erweiterter Mechanismus zum Musterabgleich, mit dem eine Sequenz von Ereignissen mit einem klar definierten Muster an regulären Ausdrücken abgeglichen werden kann.
Beispielsweise wird ein Geldautomat in Echtzeit auf Ausfälle überwacht. Wenn während des Betriebs des Geldautomaten zwei aufeinander folgende Warnmeldungen auftreten, muss der Administrator benachrichtigt werden.

**Eingabe** :

| ATM_id | Operation_id | Return_Code | Time |
| --- | --- | --- | --- |
| 1 | "Entering Pin" | „Success“ | 2017-01-26T00:10:00.0000000Z |
| 2 | "Opening Money Slot" | „Success“ | 2017-01-26T00:10:07.0000000Z |
| 2 | "Closing Money Slot" | „Success“ | 2017-01-26T00:10:11.0000000Z |
| 1 | "Entering Withdraw Quantity" | „Success“ | 2017-01-26T00:10:08.0000000Z |
| 1 | "Opening Money Slot" | "Warning" | 2017-01-26T00:10:14.0000000Z |
| 1 | "Printing Bank Balance" | "Warning" | 2017-01-26T00:10:19.0000000Z |

**Ausgabe** :

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Opening Money Slot" | 2017-01-26T00:10:14.0000000Z |

```SQL
SELECT *
FROM input TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    LIMIT DURATION(minute, 1)
    PARTITON BY ATM_id
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

Diese Abfrage gleicht mindestens zwei aufeinander folgende Fehlerereignisse ab und generiert einen Alarm, wenn die Bedingungen erfüllt sind.
**PATTERN** definiert den regulären Ausdruck, der für den Abgleich verwendet werden soll. In diesem Fall ist das eine beliebige Anzahl von erfolgreichen Vorgängen, gefolgt von mindestens zwei aufeinanderfolgenden Fehlern.
Erfolg und Fehler werden mit dem Wert „Return_Code“ definiert. Sobald die Bedingung erfüllt ist, wird der Wert für **MEASURES** mit *ATM_id* , der erste Warnvorgang und der Zeitpunkt der ersten Warnung, projiziert.

Weitere Informationen finden Sie unter [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics).

## <a name="geofencing-and-geospatial-queries"></a>Geofencing und räumliche Abfragen

Im Lieferumfang von Azure Stream Analytics sind räumliche Funktionen enthalten, die verwendet werden können, um Anwendungen für Szenarios wie Flottenmanagement, Fahrgemeinschaften, vernetzte Autos und Asset Tracking zu implementieren.
Räumliche Daten können entweder im Format GeoJSON oder im Format WKT als Teil von Ereignisdatenströmen oder Referenzdaten erfasst werden.
Ein Unternehmen, das beispielsweise auf die Herstellung von Maschinen für den Druck von Pässen spezialisiert ist, vermietet diese an Behörden und Konsulate. Der Standort dieser Maschinen wird streng kontrolliert, um die Verlagerung und den möglichen Einsatz zur Fälschung von Pässen zu vermeiden. Jedes Gerät ist mit einem GPS-Tracker ausgestattet, der diese Informationen an einen Azure Stream Analytics-Auftrag weiterleitet.
Der Hersteller möchte den Standort dieser Maschinen im Auge behalten und benachrichtigt werden, wenn eine davon einen autorisierten Bereich verlässt. So kann er die Maschine ferngesteuert abschalten, die Behörden alarmieren und die Ausrüstung zurückholen.

**Eingabe** :

| Equipment_id | Equipment_current_location | Time |
| --- | --- | --- |
| 1 | "POINT(-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000Z |
| 1 | "POINT(-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000Z |
| 1 | "POINT(-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000Z |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

**Verweisdateneingabe** :

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))" |

**Ausgabe** :

| Equipment_id | Equipment_alert_location | Time |
| --- | --- | --- |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

Mit der Abfrage kann der Hersteller den Standort der Maschinen automatisch überwachen und erhält Warnmeldungen, wenn eine Maschine den zulässigen Geofence verlässt. Dank der integrierten räumlichen Funktion können Benutzer GPS-Daten innerhalb der Abfrage ohne Bibliotheken von Drittanbietern verwenden.

Weitere Informationen finden Sie im Artikel [Geofencing and geospatial aggregation scenarios with Azure Stream Analytics (Szenarios mit Geofencing und räumlicher Aggregation mit Azure Stream Analytics)](geospatial-scenarios.md).

## <a name="get-help"></a>Hier erhalten Sie Hilfe

Weitere Unterstützung finden Sie auf der [Frageseite von Microsoft Q&A (Fragen und Antworten) zu Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nächste Schritte
* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](/rest/api/streamanalytics/)