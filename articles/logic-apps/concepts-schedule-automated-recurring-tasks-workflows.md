---
title: Planen von wiederkehrenden Aufgaben und Workflows in Azure Logic Apps
description: Übersicht über die Planung von wiederkehrenden automatisierten Aufgaben, Prozessen und Workflows mit Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 7f15dc5b28a44dc8405e2f0524913e6012ebe380
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66356047"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Planen und Ausführen von wiederkehrenden automatisierten Aufgaben, Prozessen und Workflows mit Azure Logic Apps

Logic Apps unterstützt Sie beim Erstellen und Ausführen von automatisierten wiederkehrenden Aufgaben und Prozessen nach einem bestimmten Zeitplan. Indem Sie einen Logik-App-Workflow erstellen, der mit einem integrierten Trigger des Typs „Serie“ oder „Gleitendes Fenster“ – hierbei handelt es sich um Zeitplantrigger – startet, können Sie Aufgaben sofort, zu einem späteren Zeitpunkt oder in sich wiederholenden Intervallen ausführen. Sie können Dienste innerhalb und außerhalb von Azure aufrufen (z.B. über HTTP- oder HTTPS-Endpunkte), Nachrichten in Azure-Diensten wie Azure Storage und Azure Service Bus posten oder Dateien in eine Dateifreigabe hochladen. Mit dem Serientrigger können Sie auch komplexe Zeitpläne und Wiederholungseinstellungen für die Ausführung von Aufgaben einrichten. Weitere Informationen zu den integrierten Zeitplantriggern und -aktionen finden Sie unter [Planen und Ausführen von wiederkehrenden automatisierten Aufgaben und Workflows mit Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Im Folgenden finden Sie einige Beispiele für die Art von Aufgaben, die Sie ausführen können:

* Abrufen von internen Daten, z.B. durch tägliches Ausführen einer gespeicherten SQL-Prozedur.

* Abrufen von externen Daten, indem alle 15 Minuten ein Pull für die Wetterprognose eines Wetterdiensts ausgeführt wird.

* Senden von Berichtsdaten, z.B. eine E-Mail mit einer Zusammenfassung aller Bestellungen der letzten Woche, die einen bestimmten Betrag übersteigen.

* Verarbeiten von Daten, z.B. Komprimieren der hochgeladenen Bilder des Tages an jedem Wochentag außerhalb der Spitzenzeiten.

* Bereinigen von Daten, z.B. Löschen aller Tweets, die älter als drei Monate sind.

* Archivieren von Daten, z.B. Senden von Rechnungen per Push an einen Sicherungsdienst um 1:00 Uhr jeden Morgen für die nächsten neun Monate.

Sie können auch die integrierten Zeitplanaktionen nutzen, um den Workflow vor dem Ausführen der nächsten Aktion anzuhalten. Beispiele:

* Versand einer E-Mail mit einem Statusupdate bis zum Erreichen eines Werktags verzögern.

* Workflow verzögern, bis ein HTTP-Aufruf abgeschlossen werden konnte, und erst dann den Vorgang fortsetzen und das Ergebnis abrufen.

Dieser Artikel beschreibt die Funktionen für die integrierten Zeitplantrigger und -aktionen.

## <a name="schedule-triggers"></a>Zeitplantrigger

Sie können Ihren Logik-App-Workflow mit dem Trigger „Serie“ oder dem Trigger „Gleitendes Fenster“ starten – beide gehören zu keinem bestimmten Dienst oder System wie etwa Office 365 Outlook oder SQL Server. Diese Trigger starten Ihren Workflow und führen ihn gemäß den von Ihnen angegebenen Wiederholungseinstellungen aus. Sie können das Intervall und die Häufigkeit auswählen, z.B. die Anzahl von Sekunden, Minuten und Stunden für beide Trigger oder die Anzahl von Tagen, Wochen oder Monaten für den Serientrigger. Sie können auch Startdatum und -uhrzeit sowie die Zeitzone festlegen. Bei jeder Triggerauslösung erstellt Logic Apps eine neue Workflowinstanz für Ihre Logik-App und führt diese aus.

Im Folgenden finden Sie die Unterschiede zwischen den beiden Triggern:

* **Serie**: Führt Ihren Workflow in regelmäßigen Intervallen aus, basierend auf dem von Ihnen angegebenen Zeitplan. Wenn Wiederholungen nicht ausgeführt werden, verarbeitet der Serientrigger diese fehlenden Wiederholungen nicht, sondern startet die Wiederholungen beim nächsten geplanten Intervall neu. Sie können Startdatum und -uhrzeit sowie die Zeitzone festlegen. Wenn Sie „Tag“ auswählen, können Sie die genaue Tageszeit in Stunden und Minuten angeben, z.B. jeden Tag um 14:30 Uhr. Wenn Sie „Woche“ auswählen, können Sie auch bestimmte Wochentage angeben, z.B. Mittwoch und Samstag. Weitere Informationen finden Sie unter [Erstellen, Planen und Ausführen von wiederkehrenden Aufgaben und Workflows mit dem Serientrigger](../connectors/connectors-native-recurrence.md).

* **Gleitendes Fenster**: Führt Ihren Workflow in regelmäßigen Intervallen aus, wobei Daten in kontinuierlichen Blöcken verarbeitet werden. Wenn Wiederholungen nicht ausgeführt werden, kehrt der Trigger „Gleitendes Fenster“ zurück und verarbeitet die fehlenden Wiederholungen. Sie können ein Startdatum und eine Startuhrzeit, eine Zeitzone und einen Zeitraum angeben, um den jede Wiederholung in Ihrem Workflow verzögert werden soll. Diese Trigger bietet keine Optionen zum Angeben von Tagen, Wochen, Monaten, Wochentagen oder der genauen Uhrzeit in Stunden und Minuten. Weitere Informationen finden Sie unter [Erstellen, Planen und Ausführen von wiederkehrenden Aufgaben und Workflows mit dem Trigger „Gleitendes Fenster“](../connectors/connectors-native-sliding-window.md).

## <a name="schedule-actions"></a>Zeitplanaktionen

Nach jeder Aktion in Ihrem Logik-App-Workflow können Sie die Aktionen „Verzögern“ und „Verzögern bis“ verwenden, damit Ihr Workflow vor der Ausführung der nächsten Aktionen eine bestimmte Zeit wartet.

* **Verzögern**: Vor der Ausführung der nächsten Aktion wird eine bestimmte Anzahl von Zeiteinheiten lang gewartet, z.B. Sekunden, Minuten, Stunden, Tage, Wochen oder Monate. Weitere Informationen finden Sie unter [Verzögern der nächsten Aktion in Workflows](../connectors/connectors-native-delay.md).

* **Verzögern bis**: Mit der Ausführung der nächsten Aktion wird bis zum angegebenen Datum und zur angegebenen Uhrzeit gewartet. Weitere Informationen finden Sie unter [Verzögern der nächsten Aktion in Workflows](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Muster für Startdatum und -uhrzeit

<a name="start-time"></a>

Die folgenden Muster veranschaulichen, wie Sie die Wiederholung mit dem Startdatum und der Startuhrzeit steuern können und wie der Logic Apps-Dienst diese Wiederholungen ausführt:

| Startzeit | Wiederholung ohne Zeitplan | Wiederholung mit Zeitplan (nur Serientrigger) |
|------------|-----------------------------|----------------------------------------------------|
| {keine} | Die erste Workload wird sofort ausgeführt. <p>Zukünftige Workloads werden basierend auf der letzten Ausführungszeit ausgeführt. | Die erste Workload wird sofort ausgeführt. <p>Zukünftige Workloads werden basierend auf dem angegebenen Zeitplan ausgeführt. |
| Startuhrzeit liegt in der Vergangenheit | Trigger **Serie**: Die Ausführungszeiten werden basierend auf der angegebenen Startuhrzeit berechnet, und vergangene Ausführungszeiten werden verworfen. Die nächste Workload wird zum nächsten zukünftigen Ausführungszeitpunkt ausgeführt. <p>Zukünftige Workloads werden basierend auf Berechnungen der letzten Ausführungszeit ausgeführt. <p><p>Trigger **Gleitendes Fenster**: Die Ausführungszeiten werden basierend auf der angegebenen Startuhrzeit berechnet, und vergangene Ausführungszeiten werden berücksichtigt. <p>Zukünftige Workloads werden basierend auf Berechnungen ab der angegebenen Startzeit ausgeführt. <p><p>Eine ausführlichere Erläuterung finden Sie im Beispiel im Anschluss an diese Tabelle. | Die erste Workload wird *frühestens* zur Startuhrzeit basierend auf dem Zeitplan ausgeführt, der anhand der Startuhrzeit berechnet wurde. <p>Zukünftige Workloads werden basierend auf dem angegebenen Zeitplan ausgeführt. <p>**Hinweis:** Wenn Sie eine Wiederholung mit einem Zeitplan angeben, aber keine Stunden oder Minuten für den Zeitplan, werden zukünftige Ausführungszeiten anhand der Stunden bzw. Minuten der ersten Ausführungszeit berechnet. |
| Startuhrzeit liegt in Gegenwart oder Zukunft | Die erste Workload wird zur angegebenen Startuhrzeit ausgeführt. <p>Zukünftige Workloads werden basierend auf Berechnungen der letzten Ausführungszeit ausgeführt. | Die erste Workload wird *frühestens* zur Startuhrzeit basierend auf dem Zeitplan ausgeführt, der anhand der Startuhrzeit berechnet wurde. <p>Zukünftige Workloads werden basierend auf dem angegebenen Zeitplan ausgeführt. <p>**Hinweis:** Wenn Sie eine Wiederholung mit einem Zeitplan angeben, aber keine Stunden oder Minuten für den Zeitplan, werden zukünftige Ausführungszeiten anhand der Stunden bzw. Minuten der ersten Ausführungszeit berechnet. |
||||

*Beispiel für eine Startuhrzeit in der Vergangenheit mit Wiederholung, aber ohne Zeitplan*

Annahme: Das aktuelle Datum ist der 8. September 2017, die aktuelle Uhrzeit ist 13:00 Uhr. Sie geben den 7. September 2017 als Startdatum und 14:00 Uhr als Startuhrzeit an – dieses Datum liegt in der Vergangenheit. Sie geben zudem eine Wiederholung an, die alle 2 Tage ausgeführt wird.

| Startzeit | Die aktuelle Zeit | Serie | Schedule |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(**07**.09.2017 um 14:00 Uhr) | 2017-09-**08**T13:00:00Z <br>(**08**.09.2017 um 13:00 Uhr) | alle 2 Tage | {keine} |
|||||

Für den Serientrigger berechnet die Logic Apps-Engine die Ausführungszeiten basierend auf der Startuhrzeit, verwirft vergangene Ausführungszeiten, verwendet die nächste in der Zukunft liegende Startuhrzeit für die erste Ausführung und berechnet zukünftige Ausführungen basierend auf der letzten Ausführungszeit.

Diese Wiederholung sieht wie folgt aus:

| Startzeit | Erste Ausführungszeit | Zukünftige Ausführungszeiten |
|------------|----------------|------------------|
| 2017-09-**07** um 14:00 Uhr | 2017-09-**09** um 14:00 Uhr | 2017-09-**11** um 14:00 Uhr </br>2017-09-**13** um 14:00 Uhr </br>2017-09-**15** um 14:00 Uhr </br>usw. |
||||

Es gilt also: Unabhängig davon, wie weit die angegebene Startzeit zurückliegt – z.B. am **05**.09.2017 um 14:00 Uhr oder am **01**.09.2017 um 14:00 Uhr –, wird für die erste Ausführung immer die nächste in der Zukunft liegende Startzeit verwendet.

Für den Trigger „Gleitendes Fenster“ berechnet die Logic Apps-Engine die Ausführungszeiten basierend auf der Startzeit, berücksichtigt vergangene Ausführungszeiten, verwendet die Startzeit für die erste Ausführung und berechnet zukünftige Ausführungen basierend auf der Startzeit.

Diese Wiederholung sieht wie folgt aus:

| Startzeit | Erste Ausführungszeit | Zukünftige Ausführungszeiten |
|------------|----------------|------------------|
| 2017-09-**07** um 14:00 Uhr | 2017-09-**07** um 14:00 Uhr | 2017-09-**09** um 14:00 Uhr </br>2017-09-**11** um 14:00 Uhr </br>2017-09-**13** um 14:00 Uhr </br>2017-09-**15** um 14:00 Uhr </br>usw. |
||||

Es gilt also: Unabhängig davon, wie weit die angegebene Startzeit zurückliegt – z.B. am **05**.09.2017 um 14:00 Uhr oder am **01**.09.2017 um 14:00 Uhr –, wird für die erste Ausführung immer die angegebene Startzeit verwendet.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Beispiele für Serien

Im Folgenden finden Sie einige Beispiele für Serien, die Sie für die Trigger einrichten können, die die Optionen unterstützen:

| Trigger | Serie | Intervall | Frequency | Startzeit | An diesen Tagen | Zu diesen Stunden | Zu diesen Minuten | Hinweis |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Serie, <br>Schiebefenster | Ausführung alle 15 Minuten (ohne Startdatum und -uhrzeit) | 15 | Minute | {keine} | {nicht verfügbar} | {keine} | {keine} | Dieser Zeitplan wird sofort gestartet, und anschließend werden basierend auf der letzten Ausführungszeit zukünftige Wiederholungen berechnet. |
| Serie, <br>Schiebefenster | Ausführung alle 15 Minuten (mit Startdatum und -uhrzeit) | 15 | Minute | *startDate*T*startTime*Z | {nicht verfügbar} | {keine} | {keine} | Dieser Zeitplan wird *frühestens* am angegebenen Startdatum zur entsprechenden Uhrzeit gestartet, und anschließend werden basierend auf der letzten Ausführungszeit zukünftige Wiederholungen berechnet. |
| Serie, <br>Schiebefenster | Ausführung einmal pro Stunde zur vollen Stunde (mit Startdatum und -uhrzeit) | 1 | Hour | *startDate*Thh:00:00Z | {nicht verfügbar} | {keine} | {keine} | Dieser Zeitplan wird *frühestens* am angegebenen Startdatum zur entsprechenden Uhrzeit gestartet. Zukünftige Wiederholungen werden jede Stunde beim Minutenwert „00“ ausgeführt. Die Berechnung erfolgt aus der Startzeit. <p>Wenn für die Häufigkeit „Week“ (Woche) oder „Month“ (Monat) ausgewählt wurde, wird der Zeitplan nur an einem Tag der Woche bzw. einem Tag des Monats ausgeführt. |
| Serie, <br>Schiebefenster | Ausführung täglich einmal pro Stunde (ohne Startdatum und -uhrzeit) | 1 | Hour | {keine} | {nicht verfügbar} | {keine} | {keine} | Dieser Zeitplan wird sofort gestartet, und anschließend werden basierend auf der letzten Ausführungszeit zukünftige Wiederholungen berechnet. <p>Wenn für die Häufigkeit „Week“ (Woche) oder „Month“ (Monat) ausgewählt wurde, wird der Zeitplan nur an einem Tag der Woche bzw. einem Tag des Monats ausgeführt. |
| Serie, <br>Schiebefenster | Ausführung täglich einmal pro Stunde (mit Startdatum und -uhrzeit) | 1 | Hour | *startDate*T*startTime*Z | {nicht verfügbar} | {keine} | {keine} | Dieser Zeitplan wird *frühestens* am angegebenen Startdatum zur entsprechenden Uhrzeit gestartet, und anschließend werden basierend auf der letzten Ausführungszeit zukünftige Wiederholungen berechnet. <p>Wenn für die Häufigkeit „Week“ (Woche) oder „Month“ (Monat) ausgewählt wurde, wird der Zeitplan nur an einem Tag der Woche bzw. einem Tag des Monats ausgeführt. |
| Serie, <br>Schiebefenster | Ausführung einmal pro Stunde jeweils 15 Minuten nach der vollen Stunde (mit Startdatum und -uhrzeit) | 1 | Hour | *startDate*T00:15:00Z | {nicht verfügbar} | {keine} | {keine} | Dieser Zeitplan wird *frühestens* am angegebenen Startdatum zur entsprechenden Uhrzeit gestartet. Zukünftige Wiederholungen werden jede Stunde beim Minutenwert „15“ ausgeführt. Die Berechnung erfolgt aus der Startzeit, die Ausführungen finden also um 00:15 Uhr, 01:15 Uhr, 02:15 Uhr usw. statt. |
| Serie | Ausführung einmal pro Stunde jeweils 15 Minuten nach der vollen Stunde (ohne Startdatum und -uhrzeit) | 1 | Day (Tag) | {keine} | {nicht verfügbar} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Dieser Zeitplan wird um 00:15, 1:15, 2:15 usw. ausgeführt. Außerdem entspricht dieser Zeitplan der Häufigkeit „Hour“ (Stunde) mit einer Startuhrzeit von „15“ Minuten. |
| Serie | Ausführung alle 15 Minuten beim angegebenen Minutenwert (ohne Startdatum und -uhrzeit). | 1 | Day (Tag) | {keine} | {nicht verfügbar} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Dieser Zeitplan wird erst gestartet, wenn die nächste angegebene 15-Minuten-Marke erreicht ist. |
| Serie | Ausführung täglich um 8:00 Uhr (ohne Startdatum und -uhrzeit) | 1 | Day (Tag) | {keine} | {nicht verfügbar} | 8 | {keine} | Dieser Zeitplan wird täglich um 8:00 Uhr basierend auf dem angegebenen Zeitplan ausgeführt. |
| Serie | Ausführung täglich um 8:00 Uhr (mit Startdatum und -uhrzeit) | 1 | Day (Tag) | *startDate*T08:00:00Z | {nicht verfügbar} | {keine} | {keine} | Dieser Zeitplan wird täglich um 8:00 Uhr basierend auf der angegebenen Startuhrzeit ausgeführt. | 
| Serie | Ausführung täglich um 8:30 Uhr (ohne Startdatum und -uhrzeit) | 1 | Day (Tag) | {keine} | {nicht verfügbar} | 8 | 30 | Dieser Zeitplan wird täglich um 8:30 Uhr basierend auf dem angegebenen Zeitplan ausgeführt. |
| Serie | Ausführung täglich um 8:30 Uhr (mit Startdatum und -uhrzeit) | 1 | Day (Tag) | *startDate*T08:30:00Z | {nicht verfügbar} | {keine} | {keine} | Dieser Zeitplan wird am angegebenen Startdatum um 8:30 Uhr gestartet. |
| Serie | Ausführung täglich um 8:30 und 16:30 Uhr | 1 | Day (Tag) | {keine} | {nicht verfügbar} | 8, 16 | 30 | |
| Serie | Ausführung täglich um 8:30 Uhr, 8:45 Uhr, 16:30 Uhr und 16:45 Uhr | 1 | Day (Tag) | {keine} | {nicht verfügbar} | 8, 16 | 30, 45 | |
| Serie | Ausführung jeden Samstag um 17:00 Uhr (ohne Startdatum und -uhrzeit) | 1 | Woche | {keine} | „Saturday“ (Samstag) | 17 | 00 | Dieser Zeitplan wird jeden Samstag um 17:00 Uhr ausgeführt. |
| Serie | Ausführung jeden Samstag um 17:00 Uhr (mit Startdatum und -uhrzeit) | 1 | Woche | *startDate*T17:00:00Z | „Saturday“ (Samstag) | {keine} | {keine} | Dieser Zeitplan wird *frühestens* am angegebenen Startdatum zur entsprechenden Uhrzeit gestartet, also hier am 9. September 2017 um 17:00 Uhr. Zukünftige Wiederholungen werden jeden Samstag um 17:00 Uhr ausgeführt. |
| Serie | Ausführung jeden Dienstag und Donnerstag um 17:00 Uhr | 1 | Woche | {keine} | „Tuesday“ (Dienstag), „Thursday“ (Donnerstag) | 17 | {keine} | Dieser Zeitplan wird jeden Dienstag und Donnerstag um 17:00 Uhr ausgeführt. |
| Serie | Ausführung einmal pro Stunde während der Geschäftszeiten | 1 | Woche | {keine} | Wählen Sie alle Tage mit Ausnahme von Samstag und Sonntag aus. | Wählen Sie die gewünschten Stunden des Tages aus. | Wählen Sie die gewünschten Minuten der Stunde aus. | Wenn Ihre Geschäftszeiten beispielsweise den Zeitraum von 8:00 bis 17:00 Uhr umfassen, wählen Sie „8, 9, 10, 11, 12, 13, 14, 15, 16, 17“ als Stunden des Tages aus. <p>Wählen Sie bei einer Geschäftszeit von 8:30 bis 17:30 Uhr die obigen Stunden des Tages sowie „30“ als Minuten der Stunde aus. |
| Serie | Ausführung einmal pro Tag an Wochenenden | 1 | Woche | {keine} | „Saturday“ (Samstag), „Sunday“ (Sonntag) | Wählen Sie die gewünschten Stunden des Tages aus. | Wählen Sie wie gewünscht die Minuten der Stunde aus. | Dieser Zeitplan wird jeden Samstag und Sonntag basierend auf dem angegebenen Zeitplan ausgeführt. |
| Serie | Ausführung alle zwei Wochen nur am Montag im Abstand von 15 Minuten | 2 | Woche | {keine} | „Monday“ (Montag) | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Dieser Zeitplan wird jeden zweiten Montag jeweils zur 15-Minuten-Marke ausgeführt. |
| Serie | Ausführung jeden Monat | 1 | Month (Monat) | *startDate*T*startTime*Z | {nicht verfügbar} | {nicht verfügbar} | {nicht verfügbar} | Dieser Zeitplan wird *frühestens* am angegebenen Startdatum zur angegebenen Uhrzeit gestartet. Zukünftige Wiederholungen werden basierend auf Startdatum und -uhrzeit berechnet. Wenn Sie Startdatum und -uhrzeit nicht angeben, werden in diesem Zeitplan das Datum und die Uhrzeit der Erstellung verwendet. |
| Serie | Ausführung jede Stunde an einem Tag des Monats | 1 | Month (Monat) | {siehe Hinweis} | {nicht verfügbar} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {siehe Hinweis} | Wenn Sie Startdatum und -uhrzeit nicht angeben, werden in diesem Zeitplan das Datum und die Uhrzeit der Erstellung verwendet. Geben Sie die Minuten der Stunde oder eine Startuhrzeit an, oder verwenden Sie die Uhrzeit der Erstellung, um die Minuten für den Zeitplan der Wiederholung zu steuern. Wenn die Startuhrzeit oder die Uhrzeit der Erstellung beispielsweise 8:25 Uhr lautet, wird dieser Zeitplan um 8:25, 9:25, 10:25 usw. ausgeführt. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Einmalige Ausführung

Wenn Sie Ihre Logik-App nur einmal in der Zukunft ausführen möchten, können Sie folgende Vorlage verwenden: **Planer: Einmalige Aufträge ausführen** öffnen. Wählen Sie nach dem Erstellen einer neuen Logik-App, aber vor dem Öffnen des Designers für Logik-Apps im Abschnitt **Vorlagen** in der Liste **Kategorie** die Option **Zeitplan** aus, und klicken Sie dann auf die folgende Vorlage:

![Auswählen der Vorlage „Scheduler: Einmalige Aufträge ausführen](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Wenn Sie Ihre Logik-App mit dem Trigger **Beim Empfang einer HTTP-Anforderung – Anforderung** starten, können Sie die Startzeit als Parameter für den Trigger übergeben. Verwenden Sie **Verzögern bis – Zeitplan** als erste Aktion, und geben Sie die Uhrzeit ein, zu der die nächste Aktion ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen, Planen und Ausführen von wiederkehrenden Aufgaben und Workflows mit dem Serientrigger](../connectors/connectors-native-recurrence.md)
* [Erstellen, Planen und Ausführen von wiederkehrenden Aufgaben und Workflows mit dem Trigger „Gleitendes Fenster“](../connectors/connectors-native-sliding-window.md)
* [Anhalten von Workflows mit Verzögerungsaktionen](../connectors/connectors-native-delay.md)