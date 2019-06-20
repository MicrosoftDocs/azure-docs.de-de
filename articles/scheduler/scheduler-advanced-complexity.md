---
title: Erstellen erweiterter Auftragszeitpläne und -serien – Microsoft Azure Scheduler
description: Erfahren Sie, wie Sie erweiterte Zeitpläne und Serien für Aufträge in Azure Scheduler erstellen.
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.suite: infrastructure-services
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: a413261d251c8dfc1de9209168ee8137b85009f1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60531824"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Erstellen erweiterter Zeitpläne und Serien für Aufträge in Microsoft Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersetzt Azure Scheduler, der eingestellt wird. Zum Planen von Aufträgen sollten Sie stattdessen [Azure Logic Apps ausprobieren](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

In einem [Azure Scheduler](../scheduler/scheduler-intro.md)-Auftrag bestimmt der Zeitplan, wann und wie der Scheduler-Dienst den Auftrag ausführt. Mit Scheduler können Sie mehrere einmalige Zeitpläne und Zeitplanserien für einen Auftrag einrichten. Einmalige Zeitpläne werden nur einmal zu einem festgelegten Zeitpunkt ausgeführt und sind im Grunde Zeitplanserien, die nur einmal ausgeführt werden. Zeitplanserien werden mit einer festgelegten Häufigkeit ausgeführt. Aufgrund dieser Flexibilität kann Scheduler für eine Vielzahl von Geschäftsszenarien verwendet werden, beispielsweise:

* **Daten regelmäßig bereinigen**: Erstellen Sie einen täglichen Auftrag, der alle Tweets löscht, die älter als drei Monate sind.

* **Daten archivieren**: Erstellen Sie einen monatlichen Auftrag, der Ihren Rechnungsverlauf in einen Sicherungsdienst pusht.

* **Externe Daten anfordern**: Erstellen Sie einen Auftrag, der alle 15 Minuten ausgeführt wird und einen neuen Wetterbericht von NOAA pullt.

* **Bilder verarbeiten**: Erstellen Sie einen wöchentlichen Auftrag, der außerhalb der Spitzenzeiten ausgeführt wird und Cloud Computing nutzt, um im Laufe des Tags hochgeladene Bilder zu komprimieren.

In diesem Artikel werden Beispielaufträge beschrieben, die Sie mit Scheduler und der [Azure Scheduler-REST-API](/rest/api/scheduler) erstellen können. Zudem finden Sie hier die JSON-Definition (JavaScript Object Notation) für jeden Zeitplan. 

## <a name="supported-scenarios"></a>Unterstützte Szenarien

Diese Beispiele veranschaulichen die Vielzahl von Szenarien, die Azure Scheduler unterstützt, und zeigen, wie Sie Zeitpläne für verschiedene Verhaltensmuster erstellen können, beispielsweise:

* Einmalige Ausführung zu einem bestimmten Zeitpunkt (Datum und Uhrzeit).
* Ausführung und Ausführungsserie für eine bestimmte Anzahl von Malen.
* Sofortige Ausführung und Ausführungsserie.
* Ausführung und Ausführungsserie alle *n* Minuten, Stunden, Tage, Wochen oder Monate ab einem bestimmten Zeitpunkt.
* Ausführung und wöchentliche oder monatliche Ausführungsserie, jedoch nur an bestimmten Wochentagen oder Tagen des Monats.
* Ausführung und Ausführungsserie mehrmals für einen bestimmten Zeitraum. Beispielsweise am letzten Freitag und letzten Montag jedes Monats oder täglich um 05:15 Uhr und 17:15 Uhr.

Die oben genannten Szenarien werden in diesem Artikel an späterer Stelle ausführlicher beschrieben.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Erstellen eines Zeitplans mit der REST-API

Führen Sie die folgenden Schritte aus, um einen einfachen Zeitplan mit der [Azure Scheduler-REST-API](/rest/api/scheduler) zu erstellen:

1. Registrieren Sie Ihr Azure-Abonnement mit dem [Registrierungsvorgang der Resource Manager-REST-API](https://docs.microsoft.com/rest/api/resources/providers) bei einem Ressourcenanbieter. Der Anbietername für den Azure Scheduler-Dienst ist **Microsoft.Scheduler**. 

1. Erstellen Sie eine Auftragssammlung mit dem [Erstellungs- oder Aktualisierungsvorgang für Auftragssammlungen](https://docs.microsoft.com/rest/api/scheduler/jobcollections) in der Scheduler-REST-API. 

1. Erstellen Sie einen Auftrag mit dem [Erstellungs- oder Aktualisierungsvorgang für Aufträge](https://docs.microsoft.com/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Elemente des Auftragsschemas

Die folgende Tabelle enthält eine allgemeine Übersicht über die wichtigsten JSON-Elemente, die Sie zum Einrichten von Serien und Zeitplänen für Aufträge verwenden können. 

| Element | Erforderlich | BESCHREIBUNG | 
|---------|----------|-------------|
| **startTime** | Nein | Ein DateTime-Zeichenfolgenwert im [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601), der angibt, wann der Auftrag in einem einfachen Zeitplan erstmals gestartet wird. <p>Bei komplexen Zeitplänen wird der Auftrag frühestens bei **startTime** gestartet. | 
| **recurrence** | Nein | Die Wiederholungsregeln für die Ausführung des Auftrags. Das **recurrence**-Objekt unterstützt die folgenden Elemente: **frequency**, **interval**, **schedule**, **count** und **endTime**. <p>Wenn Sie das **recurrence**-Element verwenden, müssen Sie auch das **frequency**-Element verwenden. Andere **recurrence**-Elemente sind dagegen optional. |
| **frequency** | Ja, bei Verwendung von **recurrence** | Die Zeiteinheit zwischen den Ausführungen. Unterstützt werden folgende Werte: „Minute“, „Hour“, „Day“, „Week“, „Month“ und „Year“ | 
| **interval** | Nein | Eine positive ganze Zahl, die die Anzahl von Zeiteinheiten zwischen den Ausführungen basierend auf dem Wert von **frequency** bestimmt. <p>Ist **interval** beispielsweise auf 10 und **frequency** auf „Week“ festgelegt, wird der Auftrag alle zehn Wochen ausgeführt. <p>Hier lautet die höchste Anzahl von Intervallen für jede Häufigkeit wie folgt: <p>- 18 Monate <br>- 78 Wochen <br>- 548 Tage <br>- Für Stunden und Minuten ist der Bereich 1 <= <*Intervall*> <= 1.000. | 
| **schedule** | Nein | Definiert Änderungen an der Serie auf Grundlage der angegebenen Minutenmarkierungen, Stundenmarkierungen, Wochentage und Tage des Monats. | 
| **count** | Nein | Eine positive ganze Zahl, die angibt, wie oft dieser Auftrag ausgeführt wird, bevor er abgeschlossen ist. <p>Wenn beispielsweise für einen täglichen Auftrag **count** auf 7 festgelegt und das Startdatum Montag ist, wird der Auftrag Sonntag abgeschlossen. Liegt das Startdatum in der Vergangenheit, wird die erste Ausführung auf Grundlage des Erstellungszeitpunkts berechnet. <p>Ohne Angabe von **endTime** oder **count** wird der Auftrag unendlich ausgeführt. Es ist nicht möglich, **count** und **endTime** im selben Auftrag zu verwenden, es wird jedoch die Regel berücksichtigt, die zuerst abgeschlossen wird. | 
| **endTime** | Nein | Ein Date- oder DateTime-Zeichenfolgenwert im [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601), der angibt, wann die Ausführung des Auftrags beendet wird. Sie können einen Wert für **endTime** festlegen, der in der Vergangenheit liegt. <p>Ohne Angabe von **endTime** oder **count** wird der Auftrag unendlich ausgeführt. Es ist nicht möglich, **count** und **endTime** im selben Auftrag zu verwenden, es wird jedoch die Regel berücksichtigt, die zuerst abgeschlossen wird. |
|||| 

Dieses JSON-Schema beschreibt beispielsweise einen einfachen Zeitplan und eine einfache Serie für einen Auftrag: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

*Datumsangaben und DateTime-Werte*

* Datumsangaben in Scheduler-Aufträgen enthalten nur das Datum und entsprechen der [ISO 8601-Spezifikation](https://en.wikipedia.org/wiki/ISO_8601).

* DateTime-Werte in Scheduler-Aufträgen enthalten sowohl das Datum als auch die Uhrzeit, entsprechen der [ISO 8601-Spezifikation](https://en.wikipedia.org/wiki/ISO_8601) und werden als UTC-Angaben interpretiert, sofern keine UTC-Abweichung angegeben ist. 

Weitere Informationen finden Sie unter [Konzepte, Terminologie und Entitäten](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Details: startTime

In der folgenden Tabelle wird beschrieben, wie **startTime**-Werte die Ausführung eines Auftrags steuern:

| startTime | Keine Serie | Serie, kein Zeitplan | Wiederholung mit Zeitplan |
|-----------|---------------|-------------------------|--------------------------|
| **Keine Startzeit** | Sofortige einmalige Ausführung. | Sofortige einmalige Ausführung. Berechnet nachfolgende Ausführungen auf der Grundlage der letzten Ausführungszeit. | Sofortige einmalige Ausführung. Berechnet nachfolgende Ausführungen auf der Grundlage eines Serienzeitplans. | 
| **Startuhrzeit in der Vergangenheit** | Sofortige einmalige Ausführung. | Berechnung der ersten zukünftigen Ausführungszeit nach der Startzeit und Ausführung zu diesem Zeitpunkt. <p>Berechnet nachfolgende Ausführungen auf der Grundlage der letzten Ausführungszeit. <p>Siehe hierzu das Beispiel im Anschluss an diese Tabelle. | Start des Auftrags *frühestens* zur angegebenen Startzeit. Das erste Vorkommen basiert auf dem Zeitplan, der auf der Grundlage der Startzeit berechnet wird. <p>Berechnet nachfolgende Ausführungen auf der Grundlage eines Serienzeitplans. | 
| **Startzeit in der Zukunft oder aktuelle Uhrzeit** | Einmalige Ausführung zur angegebenen Startzeit. | Einmalige Ausführung zur angegebenen Startzeit. <p>Berechnet nachfolgende Ausführungen auf der Grundlage der letzten Ausführungszeit. | Start des Auftrags *frühestens* zur angegebenen Startzeit. Das erste Vorkommen basiert auf dem Zeitplan, der auf der Grundlage der Startzeit berechnet wird. <p>Berechnet nachfolgende Ausführungen auf der Grundlage eines Serienzeitplans. |
||||| 

Angenommen, Sie verwenden dieses Beispiel mit den folgenden Bedingungen: eine Startzeit in der Vergangenheit, eine Wiederholung, aber kein Zeitplan.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* Das aktuelle Datum und die aktuelle Uhrzeit sind der 08. April 2015, 13:00 Uhr.

* Das Startdatum und die Startuhrzeit sind mit „2015-04-07 14:00“ angegeben, d. h. ein Zeitpunkt vor dem aktuellen Datum und der aktuellen Uhrzeit.

* Die Wiederholung ist auf alle zwei Tage festgelegt.

1. Unter diesen Bedingungen erfolgt die erste Ausführung am 09. April 2015 um 14:00 Uhr. 

   Scheduler berechnet die Ausführungen auf Grundlage der Startzeit, verwirft alle Instanzen in der Vergangenheit und verwendet die nächste Instanz in der Zukunft. 
   In diesem Fall ist **startTime** auf den 07. April 2015 um 14:00 Uhr festgelegt. Die nächste Instanz folgt zwei Tage nach diesem Zeitpunkt, also am 09. April 2015 um 14.00 Uhr.

   Die erste Ausführungszeit ist identisch, unabhängig davon, ob **startTime** auf „2015-04-05 14:00“ oder „2015-04-01 14:00“ festgelegt ist. Nach der ersten Ausführung werden nachfolgende Ausführungen anhand des Zeitplans berechnet. 
   
1. Die Ausführungen erfolgen dann in der folgenden Reihenfolge: 
   
   1. 11\.04.2015 um 14:00 Uhr
   1. 13\.04.2015 um 14:00 Uhr 
   1. 15\.04.2015 um 14:00 Uhr
   1. Und so weiter.

1. Wenn für einen Auftrag ein Zeitplan, aber keine Stunden- und Minutenangaben festgelegt sind, werden als Werte standardmäßig die Stunden und Minuten der ersten Ausführung verwendet.

<a name="schedule"></a>

## <a name="details-schedule"></a>Details: schedule

Mithilfe von **schedule** lässt sich die Anzahl der Auftragsausführungen *begrenzen*. Beispiel: Wird für einen Auftrag mit einer monatlichen Häufigkeit (**frequency** ist „month“) ein Zeitplan angegeben, der nur am 31. Tag ausgeführt wird, wird der Auftrag nur in Monaten mit 31 Tagen ausgeführt.

Mithilfe von **schedule** lässt sich die Anzahl der Auftragsausführungen auch *erweitern*. Beispiel: Wird für einen Auftrag mit einer monatlichen Häufigkeit (**frequency** ist „month“) ein Zeitplan angegeben, der am ersten und zweiten Tag des Monats ausgeführt wird, wird der Auftrag nicht nur ein Mal im Monat, sondern jeweils am ersten und zweiten Tag des Monats ausgeführt.

Wenn Sie mehrere Zeitplanelemente angeben, werden diese in absteigender Reihenfolge ausgewertet: von der Wochennummer über den Tag des Monats und den Wochentag bis hin zu Stunde und Minute.

Die folgende Tabelle enthält eine ausführliche Beschreibung der Zeitplanelemente:

| JSON-Name | BESCHREIBUNG | Gültige Werte |
|:--- |:--- |:--- |
| **minutes** |Minuten der Stunde, in der der Auftrag ausgeführt wird. |Ein Array mit ganzen Zahlen. |
| **hours** |Stunden des Tages, an dem der Auftrag ausgeführt wird. |Ein Array mit ganzen Zahlen. |
| **weekDays** |Die Wochentage, an denen der Auftrag ausgeführt wird. Kann nur bei wöchentlicher Häufigkeit angegeben werden. |Ein Array aus den folgenden Werten (die maximale Arraygröße ist 7):<br />– „Monday“<br />– „Tuesday“<br />– „Wednesday“<br />– „Thursday“<br />– „Friday“<br />– „Saturday“<br />– „Sunday“<br /><br />Keine Beachtung der Groß-/Kleinschreibung. |
| **monthlyOccurrences** |Bestimmt, an welchen Tagen im Monat der Auftrag ausgeführt wird. Kann nur bei monatlicher Häufigkeit angegeben werden. |Ein Array aus **monthlyOccurrence**-Objekten:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **day** ist der Wochentag, an dem der Auftrag ausgeführt wird. Beispielsweise bedeutet *{Sunday}* , dass der Auftrag an jedem Sonntag des Monats ausgeführt wird. Erforderlich.<br /><br />**occurrence** ist das Vorkommen des Tages innerhalb des Monats. Beispielsweise bedeutet *{Sunday, -1}* den letzten Sonntag des Monats. Optional. |
| **monthDays** |Der Tag des Monats, an dem der Auftrag ausgeführt wird. Kann nur bei monatlicher Häufigkeit angegeben werden. |Ein Array mit den folgenden Werten:<br />– Beliebiger Wert, für den Folgendes gilt: <= -1 und >= -31<br />– Beliebiger Wert, für den Folgendes gilt: >= 1 und <= 31|

## <a name="examples-recurrence-schedules"></a>Beispiele: Serienzeitpläne

Die folgenden Beispiele zeigen verschiedene Serienzeitpläne. Die Beispiele beziehen sich auf das schedule-Objekt und seine untergeordneten Elemente.

Bei diesen Zeitplänen wird davon ausgegangen, dass **interval** auf 1 festgelegt ist.\. Die Beispiele gehen auch von den richtigen **frequency**-Werten für die Werte in **schedule** aus. Beispielsweise können nicht der **frequency**-Wert „day“ und die Änderung **monthDays** in **schedule** angegeben werden. Wir haben diese Einschränkungen weiter oben in diesem Artikel beschrieben.

| Beispiel | BESCHREIBUNG |
|:--- |:--- |
| `{"hours":[5]}` |Ausführung täglich um 05:00 Uhr.<br /><br />Scheduler gleicht jeden Wert für „hours“ nacheinander mit den Werten für „minutes“ ab, um eine Liste mit allen Zeiten zu erstellen, zu denen der Auftrag ausgeführt werden soll. |
| `{"minutes":[15], "hours":[5]}` |Ausführung täglich um 05:15 Uhr. |
| `{"minutes":[15], "hours":[5,17]}` |Ausführung täglich um 05:15 und 17:15 Uhr. |
| `{"minutes":[15,45], "hours":[5,17]}` |Ausführung täglich um 05:15 Uhr, 05:45 Uhr, 17:15 Uhr und 17:45 Uhr. |
| `{"minutes":[0,15,30,45]}` |Ausführung alle 15 Minuten. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Stündliche Ausführung.<br /><br />Dieser Auftrag wird einmal pro Stunde ausgeführt. Die Minute wird durch den Wert für **startTime** gesteuert, sofern dieser angegeben ist. Wenn kein **startTime**-Wert angegeben ist, werden die Minuten von der Erstellungszeit gesteuert. Lautet die Start- oder Erstellungszeit also beispielsweise 12:25 Uhr, wird der Auftrag um 00:25 Uhr, 01:25 Uhr, 02:25 Uhr, …, 23:25 Uhr ausgeführt.<br /><br />Der Zeitplan entspricht einem Auftrag mit einer stündlichen Häufigkeit (**frequency** ist „hour“) und einem **interval** von 1 (ohne Angabe eines **schedule**-Werts). Der Unterschied besteht darin, dass dieser Zeitplan mit unterschiedlichen Angaben für **frequency**- und **interval**-Werte auch zur Erstellung anderer Aufträge verwendet werden kann. Wird **frequency** beispielsweise auf „month“ festgelegt, wird der Zeitplan nur ein Mal im Monat ausgeführt (und nicht täglich wie bei einem **frequency**-Wert von „day“). |
| `{minutes:[0]}` |Ausführung jeweils zur vollen Stunde.<br /><br />Dieser Auftrag wird ebenfalls stündlich ausgeführt, aber immer zur vollen Stunde (also beispielsweise um 24: 00 Uhr, 01:00 Uhr, 02:00 Uhr usw.). Dieser Zeitplan entspricht einem Auftrag mit einer stündlichen Häufigkeit (**frequency** ist „hour“), einem **startTime**-Wert von null Minuten und ohne Angabe eines **schedule**-Werts (bei einem „frequency“-Wert von „day“). Wenn der **frequency**-Wert jedoch „week“ oder „month“ ist, wird der Zeitplan nur an einem Tag in der Woche bzw. im Monat ausgeführt. |
| `{"minutes":[15]}` |Stündliche Ausführung jeweils 15 Minuten nach der vollen Stunde.<br /><br />Ausführung immer zur vollen Stunde ab 00:15 Uhr (dann 01:15 Uhr, 02:15 Uhr usw.). Die Ausführung endet um 23:15 Uhr. |
| `{"hours":[17], "weekDays":["saturday"]}` |Wöchentliche Ausführung, immer samstags um 17:00 Uhr. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Wöchentliche Ausführung am Montag, Mittwoch und Freitag, jeweils um 17:00 Uhr. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Wöchentliche Ausführung am Montag, Mittwoch und Freitag, jeweils um 17:15 und 17:45 Uhr. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Wöchentliche Ausführung am Montag, Mittwoch und Freitag, jeweils um 05:00 Uhr und um 17:00 Uhr. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Wöchentliche Ausführung am Montag, Mittwoch und Freitag, jeweils um 05:15 Uhr, 05:45 Uhr, 17:15 Uhr und 17:45 Uhr. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Ausführung an Wochentagen im 15-Minuten-Takt. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Ausführung an Wochentagen zwischen 09:00 Uhr und 16:45 Uhr im 15-Minuten-Takt. |
| `{"weekDays":["sunday"]}` |Ausführung an Sonntagen zur Startzeit. |
| `{"weekDays":["tuesday", "thursday"]}` |Ausführung jeweils dienstags und donnerstags zur Startzeit. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Ausführung um 06:00 Uhr am 28. Tag jedes Monats (Voraussetzung: **frequency** ist „month“). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Ausführung um 06:00 Uhr am letzten Tag des Monats.<br /><br />Wenn Sie einen Auftrag am letzten Tag eines Monats ausführen möchten, verwenden Sie „-1“ anstelle von Tag 28, 29, 30 oder 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Ausführung jeweils am ersten und letzten Tag jedes Monats um 06:00 Uhr. |
| `{monthDays":[1,-1]}` |Ausführung jeweils am ersten und letzten Tag jedes Monats zur Startzeit. |
| `{monthDays":[1,14]}` |Ausführung jeweils am ersten und 14. Tag jedes Monats zur Startzeit. |
| `{monthDays":[2]}` |Ausführung jeweils am zweiten Tag des Monats zur Startzeit. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Ausführung am ersten Freitag jedes Monats um 05:00 Uhr. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Ausführung am ersten Freitag jedes Monats zur Startzeit. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Monatliche Ausführung am dritten Freitag ab Monatsende zur Startzeit. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Ausführung am ersten und letzten Freitag jedes Monats um 05:15 Uhr. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Ausführung am ersten und letzten Freitag jedes Monats zur Startzeit. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Ausführung am fünften Freitag jedes Monats zur Startzeit.<br /><br />Wenn kein fünfter Freitag im Monat vorhanden ist, wird der Auftrag nicht ausgeführt. Soll der Auftrag am letzten Freitag des Monats ausgeführt werden, empfiehlt sich unter Umständen die Verwendung von „-1“ anstelle von „5“. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Ausführung im 15-Minuten-Takt am letzten Freitag des Monats. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Ausführung um 05:15, 05:45, 17:15 und 17:45 Uhr am dritten Mittwoch jedes Monats. |

## <a name="see-also"></a>Weitere Informationen

* [Was ist der Azure Scheduler?](scheduler-intro.md)
* [Konzepte, Terminologie und Entitätshierarchie für Azure Scheduler](scheduler-concepts-terms.md)
* [Einschränkungen, Standardwerte und Fehlercodes für Azure Scheduler](scheduler-limits-defaults-errors.md)
