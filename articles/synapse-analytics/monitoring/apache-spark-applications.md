---
title: Überwachen von Apache Spark-Anwendungen mithilfe von Synapse Studio
description: Verwenden Sie Synapse Studio, um Ihre Apache Spark-Anwendungen zu überwachen.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 00407b4a55b3c074fa77d4c3664f6e218c23fe21
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584516"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Verwenden von Synapse Studio zum Überwachen Ihrer Apache Spark-Anwendungen

Mit Azure Synapse Analytics können Sie mithilfe von Apache Spark Notebooks, Aufträge und andere Arten von Anwendungen in ihren Apache Spark-Pools in Ihrem Arbeitsbereich ausführen.

In diesem Artikel wird erläutert, wie Sie Ihre Apache Spark-Anwendungen überwachen, was es Ihnen gestattet, den aktuellen Status, die aktuellen Probleme und den Fortschritt im Auge zu behalten.

Dieses Tutorial enthält die folgenden Aufgaben:

* Überwachen aktiver Apache Spark-Anwendungen
* Anzeigen einer abgeschlossenen Apache Spark-Anwendung
* Anzeigen einer abgebrochenen Apache Spark-Anwendung
* Debuggen von Fehlern bei Apache Spark-Anwendungen

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich zunächst, dass die folgenden Anforderungen erfüllt sind bzw. dass Folgendes vorhanden ist:

- Ein Synapse Studio-Arbeitsbereich. Entsprechende Anweisungen finden Sie unter [Erstellen eines Synapse Studio-Arbeitsbereichs](../../machine-learning/how-to-manage-workspace.md#create-a-workspace).

- Ein Apache Spark-Pool.

## <a name="view-apache-spark-applications"></a>Anzeigen von Apache Spark-Anwendungen 
Sie können alle Apache Spark-Anwendungen unter **Überwachen** -> **Apache Spark-Anwendungen** anzeigen.
   ![Apache Spark-Anwendungen](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>Anzeigen abgeschlossener Apache Spark-Anwendungen

Öffnen Sie **Überwachen**, und wählen Sie dann **Apache Spark-Anwendungen** aus. Um die Details zu abgeschlossenen Apache Spark-Anwendungen anzuzeigen, wählen Sie die Apache Spark-Anwendung aus, und zeigen Sie die Details an.

  ![Abgeschlossenen Auftrag auswählen](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Überprüfen Sie **Abgeschlossene Aufgaben**, **Status** und **Gesamtdauer**.

2. Aktualisieren Sie die Protokollabfrage.

3. Öffnen Sie den Link zum Apache Spark-Verlaufsserver, indem Sie auf **Spark-Verlaufsserver** klicken.

4. Überprüfen Sie die Informationen unter **Zusammenfassung**.

5. Überprüfen Sie die **Protokolle**. Sie können verschiedene Arten von Protokollen aus der Dropdownliste auswählen, und Sie können die Protokollinformationen herunterladen, indem Sie auf **Protokolle herunterladen** klicken und das Kontrollkästchen **Fehler und Warnungen filtern** aktivieren, um die von Ihnen benötigten Fehler und Warnungen zu filtern.

6. Sie sehen eine Übersicht Ihres Auftrags in dem generierten Auftragsdiagramm. Standardmäßig werden in dem Diagramm alle Aufträge angezeigt. Sie können diese Ansicht anhand der **Auftrags-ID** filtern.

7. Standardmäßig ist die Anzeige **Status** ausgewählt. Sie können den Datenfluss überprüfen, indem Sie **Progress (Fortschritt)** /**Read (Gelesen)** /**Written (Geschrieben)** /**Duration (Dauer)** in der Dropdownliste **Anzeige** auswählen.

8. Klicken Sie zur Wiedergabe des Auftrags auf die Schaltfläche **Wiedergabe**. Sie können jederzeit auf die Schaltfläche **Beenden** klicken, um den Vorgang anzuhalten.

9. Scrollen Sie zum Vergrößern und Verkleinern des Auftragsgraphen mit der Maus oder über die Scrollleiste. Sie können auch **Zoom anpassen** auswählen, um ihn an den Bildschirm anzupassen.

10. Der Auftragsdiagrammknoten zeigt die folgenden Informationen zu den einzelnen Phasen an:

    * ID

    * Name oder Beschreibung

    * Anzahl der Aufträge insgesamt

    * Gelesene Daten: die Summe der Eingabegröße und der Shuffle-Lesegröße

    * Geschriebene Daten: die Summe der Ausgabegröße und der Shuffle-Schreibgröße.

    * Ausführungszeit: die Zeit zwischen der Startzeit des ersten Versuchs und der Endzeit des letzten Versuchs

    * Zeilenanzahl: die Summe der Eingabe und Ausgabedatensätze sowie die Datensätze der zufälligen Lese- und Schreibvorgänge

    * Status

     ![Abgeschlossenen Auftrag anzeigen](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. Klicken Sie auf **Details anzeigen** im Graphen, um die Details für die Phase anzuzeigen.

    ![Details für Phase](./media/how-to-monitor-spark-applications/details-for-stage.png)


## <a name="monitor-running-apache-spark-application"></a>Überwachen aktiver Apache Spark-Anwendungen

Öffnen Sie **Überwachen**, und wählen Sie dann **Apache Spark-Anwendungen** aus. Um die Details zu den ausgeführten Apache Spark-Anwendungen anzuzeigen, wählen Sie die sendende Apache Spark-Anwendung aus, und zeigen Sie die Details an. Wenn die Apache Spark-Anwendung noch ausgeführt wird, können Sie den Fortschritt überwachen.

   ![Ausgeführten Auftrag auswählen](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Überprüfen Sie **Abgeschlossene Aufgaben**, **Status** und **Gesamtdauer**.

2. **Brechen** Sie die Apache Spark-Anwendung ab.

3. **Aktualisieren** Sie die Protokollabfrage.

4. Klicken Sie auf die Schaltfläche für die **Spark-Benutzeroberfläche**, um zur Seite „Spark-Auftrag“ zu wechseln.

5. Sehen Sie sich das Diagramm an. Sie sehen eine Übersicht Ihres Auftrags in dem generierten Auftragsdiagramm. Weitere Informationen finden Sie in den Schritten 6, 7, 8, 9 und 10 unter [Anzeigen abgeschlossener Apache Spark-Anwendungen](#view-completed-apache-spark-application).

6. Überprüfen Sie die Informationen unter **Zusammenfassung**.

7. Überprüfen Sie die Diagnose auf der Registerkarte **Diagnose**.

8. Überprüfen Sie die **Protokolle** auf dieser Registerkarte. Sie können verschiedene Arten von Protokollen aus der Dropdownliste auswählen, und Sie können die Protokollinformationen herunterladen, indem Sie auf **Protokolle herunterladen** klicken und das Kontrollkästchen **Fehler und Warnungen filtern** aktivieren, um die von Ihnen benötigten Fehler und Warnungen zu filtern.

    ![Ausgeführten Auftrag anzeigen](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-canceled-apache-spark-application"></a>Anzeigen abgebrochener Apache Spark-Anwendungen

Öffnen Sie **Überwachen**, und wählen Sie dann **Apache Spark-Anwendungen** aus. Um die Details zu abgebrochenen Apache Spark-Anwendungen anzuzeigen, wählen Sie die Apache Spark-Anwendung aus, und zeigen Sie die Details an.

 ![Abgebrochenen Auftrag auswählen](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Überprüfen Sie **Abgeschlossene Aufgaben**, **Status** und **Gesamtdauer**.

2. Aktualisieren Sie die Protokollabfrage.

3. Öffnen Sie den Link zum Apache Spark-Verlaufsserver, indem Sie auf **Spark-Verlaufsserver** klicken.

4. Sehen Sie sich das Diagramm an. Sie sehen eine Übersicht Ihres Auftrags in dem generierten Auftragsdiagramm. Weitere Informationen finden Sie in den Schritten 6, 7, 8, 9 und 10 unter [Anzeigen abgeschlossener Apache Spark-Anwendungen](#view-completed-apache-spark-application).

5. Überprüfen Sie die Informationen unter **Zusammenfassung**.

6. Überprüfen Sie die **Protokolle**. Sie können verschiedene Arten von Protokollen aus der Dropdownliste auswählen, und Sie können die Protokollinformationen herunterladen, indem Sie auf **Protokolle herunterladen** klicken und das Kontrollkästchen **Fehler und Warnungen filtern** aktivieren, um die von Ihnen benötigten Fehler und Warnungen zu filtern.

7. Klicken Sie auf **Details anzeigen** im Graphen, um die Details für die Phase anzuzeigen.

   ![Abgebrochenen Auftrag anzeigen](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Debuggen von Fehlern bei Apache Spark-Anwendungen

Öffnen Sie **Überwachen**, und wählen Sie dann **Apache Spark-Anwendungen** aus. Um die Details zu Apache Spark-Anwendungen mit Fehlern anzuzeigen, wählen Sie die Apache Spark-Anwendung aus, und zeigen Sie die Details an.

![Auftrag mit Fehlern auswählen](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Überprüfen Sie **Abgeschlossene Aufgaben**, **Status** und **Gesamtdauer**.

2. Aktualisieren Sie die Protokollabfrage.

3. Öffnen Sie den Link zum Apache Spark-Verlaufsserver, indem Sie auf **Spark-Verlaufsserver** klicken.

4. Sehen Sie sich das Diagramm an. Sie sehen eine Übersicht Ihres Auftrags in dem generierten Auftragsdiagramm. Weitere Informationen finden Sie in den Schritten 6, 7, 8, 9 und 10 unter [Anzeigen abgeschlossener Apache Spark-Anwendungen](#view-completed-apache-spark-application).

5. Überprüfen Sie die Informationen unter **Zusammenfassung**.

6. Überprüfen Sie die Fehlerinformationen.

   ![Informationen zu Auftrag mit Fehlern](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="compare-apache-spark-applications"></a>Vergleichen von Apache Spark-Anwendungen
Klicken Sie auf die Schaltfläche **Anwendungen vergleichen**, und wählen Sie eine Anwendung aus, um die Leistung zu vergleichen. Benutzer erkennen intuitiv den Unterschied zwischen den beiden Anwendungen.

![Anwendungen vergleichen](./media/how-to-monitor-spark-applications/compare-applications.png)

1. Zeigen Sie mit der Maus auf eine Anwendung, woraufhin das Symbol **Anwendungen vergleichen** angezeigt wird.

2. Klicken Sie auf das Symbol **Anwendungen vergleichen**, woraufhin die Seite „Anwendungen vergleichen“ angezeigt wird.

3. Klicken Sie auf die Schaltfläche **Anwendung auswählen**, um die Seite **Vergleichsanwendung auswählen** zu öffnen.

4. Bei der Auswahl der Vergleichsanwendung müssen Benutzer entweder die Anwendungs-URL eingeben oder eine Auswahl aus der wiederkehrenden Liste treffen. Klicken Sie dann auf die Schaltfläche **OK**. 

   ![Vergleichsanwendung auswählen](./media/how-to-monitor-spark-applications/choose-comparison-application.png)

5. Das Vergleichsergebnis wird auf der Seite „Anwendungen vergleichen“ angezeigt.

   ![Vergleichsergebnis](./media/how-to-monitor-spark-applications/comparison-result.png)


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Überwachen von Pipelineausführungen finden Sie im Artikel [Überwachen von Pipelineausführungen mithilfe von Synapse Studio](how-to-monitor-pipeline-runs.md).
