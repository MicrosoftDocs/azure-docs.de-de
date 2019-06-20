---
title: Begriffe zur Wiederherstellung von Prüfpunkten und Wiedergabeaufträgen in Azure Stream Analytics
description: In diesem Artikel werden Begriffe zur Wiederherstellung von Prüfpunkten und Wiedergabeaufträgen in Azure Stream Analytics beschrieben.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 9dcfbd4b5fcc8462c88b16f585424166ecd3d499
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61361880"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Begriffe zu Prüfpunkten und zur Wiedergabe bei Azure Stream Analytics-Aufträgen
In diesem Artikel werden Begriffe zu internen Prüfpunkten und zur Wiedergabe in Azure Stream Analytics sowie deren Auswirkung auf die Wiederherstellung von Aufträgen beschrieben. Bei jeder Ausführung eines Stream Analytics-Auftrags werden intern Zustandsinformationen verwaltet. Die Zustandsinformationen werden in regelmäßigen Abständen in einem Prüfpunkt gespeichert. In einigen Szenarien werden die Prüfpunktinformationen bei einem Auftragsfehler oder einem Upgrade für die Auftragswiederherstellung verwendet. In anderen Fällen kann der Prüfpunkt nicht für die Wiederherstellung verwendet werden, sodass eine Wiedergabe erforderlich ist.

## <a name="stateful-query-logicin-temporal-elements"></a>Zustandsbehaftete Abfragelogik in temporalen Elementen
Eine einzigartige Funktion eines Azure Stream Analytics-Auftrags besteht darin, eine zustandsbehaftete Verarbeitung wie etwa Aggregate, temporale Verknüpfungen und temporale Analysefunktionen im Fenstermodus auszuführen. Die einzelnen Operatoren speichern Zustandsinformationen bei der Auftragsausführung. Die maximale Fenstergröße für diese Abfrageelemente beträgt sieben Tage. 

Der Begriff der temporalen Fenster kommt in mehreren Stream Analytics-Abfrageelementen vor:
1. Fensteraggregate (GROUP BY von rollierenden, springenden und gleitenden Fenstern)

2. Temporale Joins (JOIN mit DATEDIFF-Funktion)

3. Temporale Analysefunktionen (ISFIRST, LAST und LAG mit LIMIT DURATION)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Auftragswiederherstellung nach einem Knotenausfall wie bei einem Betriebssystemupgrade
Jedes Mal, wenn ein Stream Analytics-Auftrag ausgeführt wird, wird er für die Ausführung von Aufgaben in mehreren Workerknoten intern horizontal hochskaliert. Für den Zustand der einzelnen Workerknoten wird alle paar Minuten ein Prüfpunkt erstellt, was eine Systemwiederherstellung nach einem Ausfall vereinfacht.

In einigen Fällen tritt bei einem bestimmten Workerknoten möglicherweise ein Fehler auf oder ein Betriebssystemupgrade für diesen Workerknoten kann stattfinden. Für die automatische Wiederherstellung erhält Stream Analytics einen neuen fehlerfreien Knoten, und der vorherige Zustand des Workerknotens seit dem letzten verfügbaren Prüfpunkt wird wiederhergestellt. Um fortzufahren, ist eine kleine Wiedergabemenge erforderlich, um den Zustand ab dem Zeitpunkt der Prüfpunkterstellung wiederherzustellen. In der Regel beträgt die Wiederherstellungslücke nur wenige Minuten. Wenn genügend Streamingeinheiten für den Auftrag ausgewählt sind, sollte die Wiedergabe schnell abgeschlossen werden. 

In einer vollständig parallelen Abfrage verhält sich der Zeitaufwand nach einem Ausfall des Workerknotens proportional zu Folgendem:

[Rate der Eingabeereignisse] x [Länge der Lücke] / [Anzahl der Verarbeitungspartitionen]

Wenn Sie jemals eine beträchtliche Verarbeitungsverzögerung aufgrund eines Knotenausfalls und Betriebssystemupgrades beobachten sollten, sollten Sie die Abfrage vollständig parallel ausführen und den Auftrag für die Zuordnung weiterer Streamingeinheiten skalieren. Weitere Informationen finden Sie unter [Skalieren eines Azure Stream Analytics-Auftrags zur Erhöhung des Durchsatzes](stream-analytics-scale-jobs.md).

Derzeit zeigt Stream Analytics keinen Bericht an, wenn diese Art von Wiederherstellungsprozess stattfindet.

## <a name="job-recovery-from-a-service-upgrade"></a>Auftragswiederherstellung nach einem Dienstupgrade 
Microsoft führt gelegentlich Upgrades für die Binärdateien durch, die Stream Analytics-Aufträge im Azure-Dienst ausführen. Derzeit wird für die vom Benutzer ausgeführten Aufträge ein Upgrade auf eine neuere Version durchgeführt, und der Auftrag wird automatisch neu gestartet. 

Das Format des Wiederherstellungsprüfpunkts zwischen Upgrades wird derzeit nicht gespeichert. Der Zustand der Streamingabfrage muss daher vollständig mithilfe der Wiedergabemethode wiederhergestellt werden. Damit Stream Analytics-Aufträge genau dieselbe Eingabe wiedergeben können wie vorher, muss unbedingt eine Aufbewahrungsrichtlinie für die Quelldaten mit mindestens den Fenstergrößen in Ihrer Abfrage festgelegt werden. Anderenfalls sind möglicherweise falsche oder Teilergebnisse beim Dienstupgrade die Folge, da die Quelldaten nicht weit genug in der Vergangenheit beibehalten wurden, um die vollständige Fenstergröße einzuschließen.

Grundsätzlich verhält sich die erforderliche Wiedergabemenge proportional zur Größe des Fensters multipliziert mit der durchschnittlichen Ereignisrate. Beispiel: Bei einem Auftrag mit einer Eingangsrate von 1000 Ereignissen pro Sekunde gilt eine Fenstergröße von mehr als einer Stunde als große Wiedergabegröße. Möglicherweise müssen bis zu einer Stunde an Daten erneut verarbeitet werden, um den Zustand zu initialisieren, sodass vollständige und korrekte Ergebnisse erzielt werden können, was zu einer verzögerten Ausgabe (keine Ausgabe) über einen längeren Zeitraum führen kann. Abfragen ohne Fenster oder andere temporale Operatoren, wie `JOIN` oder `LAG` würden eine Wiedergabemenge von 0 haben.

## <a name="estimate-replay-catch-up-time"></a>Einschätzung der Wiedergabeaufholzeit
Um die Länge der Verzögerung aufgrund eines Dienstupgrades einzuschätzen, können Sie das folgende Verfahren ausführen:

1. Laden Sie den ausreichend Daten in den Event Hub für Eingaben, um die größte Fenstergröße in Ihrer Abfrage mit der erwarteten Ereignisrate abzudecken. Der Zeitstempel der Ereignisse sollte während dieses Zeitraums nahe an der Gesamtbetrachtungszeit liegen, so als würde es sich um einen aktiven Eingabefeed handeln. Wenn Sie z.B. über ein Fenster von 3 Tagen in Ihrer Abfrage verfügen, senden Sie drei Tage lang Ereignisse an Event Hub und fahren Sie auch darüber hinaus damit fort. 

2. Starten Sie den Auftrag mit **Jetzt** als Startzeit. 

3. Messen Sie den Zeitraum zwischen der Startzeit und dem Zeitpunkt, an dem die erste Ausgabe generiert wird. Der Zeitraum ist ein ungefährer Schätzwert dafür, wie lang die Verzögerung bei einem Auftrag im Falle eines Dienstupgrades wäre.

4. Wenn die Verzögerung zu lang ist, versuchen Sie, Ihren Auftrag zu partitionieren und die Anzahl der SUs zu erhöhen, damit die Last auf eine größere Anzahl von Knoten verteilt wird. Alternativ können Sie die Fenstergrößen in Ihrer Abfrage verkürzen und weitere Aggregationen oder andere zustandsbehaftete Verarbeitungen für die Ausgabe ausführen, die vom Stream Analytics-Auftrag in der Downstreamsenke (z.B. mithilfe von Azure SQL-Datenbank) erzeugt werden.

Hinsichtlich des allgemeinen Problems mit der Dienststabilität beim Upgrade unternehmenskritischer Aufträge empfiehlt es sich, doppelte Aufträge in paarweise zugeordneten Azure-Regionen auszuführen. Weitere Informationen finden Sie unter [Gewährleisten der Zuverlässigkeit von Stream Analytics-Aufträgen während Dienstupdates](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Auftragswiederherstellung bei einem vom Benutzer initiierten Vorgang zum Beenden und Starten
Um die Abfragesyntax für einen Streamingauftrag zu bearbeiten oder Eingaben und Ausgaben anzupassen, muss der Auftrag beendet werden, damit die Änderungen übernommen werden und ein Upgrade für den Auftragsentwurf durchgeführt wird. In solchen Szenarien, in denen ein Benutzer den Streamingauftrag beendet und wieder startet, ähnelt das Wiederherstellungsszenario einem Dienstupgrade. 

Prüfpunktdaten können nicht für den Neustart eines vom Benutzer initiierten Auftrags verwendet werden. Um die Verzögerung der Ausgabe bei einem solchen Neustart einzuschätzen, wenden Sie dasselbe Verfahren wie im vorherigen Abschnitt beschrieben sowie eine vergleichbare Entschärfung bei einer zu langen Verzögerung an.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Zuverlässigkeit und Skalierbarkeit finden Sie in folgenden Artikeln:
- [Tutorial: Einrichten von Warnungen für Azure Stream Analytics-Aufträge](stream-analytics-set-up-alerts.md)
- [Skalieren eines Azure Stream Analytics-Auftrags zur Erhöhung des Durchsatzes](stream-analytics-scale-jobs.md)
- [Gewährleisten der Zuverlässigkeit von Stream Analytics-Aufträgen während Dienstupdates](stream-analytics-job-reliability.md)
