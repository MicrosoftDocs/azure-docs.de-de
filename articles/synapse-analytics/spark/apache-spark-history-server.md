---
title: Verwenden des erweiterten Spark-Verlaufsservers zum Debuggen von Apps
description: Verwenden des erweiterten Spark-Verlaufsservers zum Debuggen und Diagnostizieren von Spark-Anwendungen in Azure Synapse Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 94c30cad1e09a01686a9042a6271a152d0f433fd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995385"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Verwenden des erweiterten Apache Spark-Verlaufsservers zum Debuggen und Diagnostizieren von Apache Spark-Anwendungen

Dieser Artikel enthält Anleitungen zur Verwendung des erweiterten Apache Spark-Verlaufsservers zum Debuggen und Diagnostizieren abgeschlossener und aktiver Spark-Anwendungen.

Die Erweiterung umfasst die Registerkarten „Daten“, „Graph“ und „Diagnose“. Auf der Registerkarte **Daten** können Sie die Eingabe- und Ausgabedaten des Spark-Auftrags überprüfen. Auf der Registerkarte **Graph** können Sie den Datenfluss anzeigen und den Auftragsgraphen wiedergeben. Auf der Registerkarte **Diagnose** werden die Registerkarten **Datenschiefe**, **Zeitabweichung** und **Executor Usage Analysis** (Analyse zur Executorauslastung) angezeigt.

## <a name="access-the-apache-spark-history-server"></a>Zugreifen auf den Apache Spark-Verlaufsserver

Der Apache Spark-Verlaufsserver ist die Webbenutzeroberfläche für abgeschlossene und ausgeführte Spark-Anwendungen. Sie können die Webbenutzeroberfläche des Apache Spark-Verlaufsservers aus Azure Synapse Analytics öffnen.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Öffnen der Webbenutzeroberfläche des Spark-Verlaufsservers von einem Apache Spark-Anwedungsknoten aus

1. Öffnen Sie [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Wählen Sie zuerst **Überwachen** und dann **Apache Spark-Anwendungen** aus.

    ![Wählen Sie „Überwachen“ aus, und wählen Sie dann „Spark-Anwendung“ aus.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Wählen Sie eine Anwendung aus, und öffnen Sie dann **Protokollabfrage**, indem Sie dies auswählen.

    ![Öffnen Sie das Protokollabfragefenster.](./media/apache-spark-history-server/open-application-window.png)

4. Wählen Sie **Spark-Verlaufsserver** aus, woraufhin die Webbenutzeroberfläche des Spark-Verlaufsservers angezeigt wird.

    ![Öffnen Sie den Spark-Verlaufsserver.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>Öffnen der Webbenutzeroberfläche des Spark-Verlaufsservers vom Datenknoten aus

1. Wählen Sie in Ihrem Azure Synapse Studio-Notebook **Spark-Verlaufsserver** in der Ausgabezelle der Auftragsausführung oder im Statusbereich am unteren Rand des Notebook-Dokuments aus. Wählen Sie **Sitzungsdetails** aus.

   ![Starten des Spark-Verlaufsservers 1](./media/apache-spark-history-server/launch-history-server2.png "Starten des Spark-Verlaufsservers")

2. Wählen Sie im einblendbaren Bereich **Spark-Verlaufsserver** aus.

   ![Starten des Spark-Verlaufsservers 2](./media/apache-spark-history-server/launch-history-server.png "Starten des Spark-Verlaufsservers")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Erkunden der Registerkarte „Daten“ im Spark-Verlaufsserver

Wählen Sie die Auftrags-ID für den Auftrag aus, den Sie anzeigen möchten. Wählen Sie dann im Menü „Extras“ **Daten** aus, um die Datenansicht aufzurufen. In diesem Abschnitt zeigen wir Ihnen, wie Sie verschiedene Aufgaben auf der Registerkarte „Daten“ ausführen.

* Überprüfen Sie die Registerkarten **Inputs** (Eingaben), **Outputs** (Ausgaben) und **Table Operations** (Tabellenvorgänge) einzeln.

    ![Daten für Spark-Anwendung – Registerkarten](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Kopieren Sie alle Zeilen, indem Sie **Kopieren** auswählen.

    ![Daten für Spark-Anwendung – Kopieren](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Speichern Sie alle Daten als CSV-Datei, indem Sie **CSV** auswählen.

    ![Daten für Spark-Anwendung – Speichern](./media/apache-spark-history-server/apache-spark-data-save.png)

* Suchen Sie, indem Sie Schlüsselwörter in das Feld **Suche** eingeben. Die Suchergebnisse werden sofort angezeigt.

    ![Daten für Spark-Anwendung – Suchen](./media/apache-spark-history-server/apache-spark-data-search.png)

* Wählen Sie die Spaltenüberschrift aus, um die Tabelle zu sortieren, wählen Sie das Pluszeichen aus, um eine Zeile zu erweitern und weitere Details anzuzeigen, oder wählen Sie das Minuszeichen aus, um eine Zeile zu reduzieren.

    ![Daten für Spark-Anwendung – Tabelle](./media/apache-spark-history-server/apache-spark-data-table.png)

* Laden Sie ein einzelne Datei herunter, indem Sie **Unvollständiger Download** auswählen. Die ausgewählte Datei wird lokal heruntergeladen. Wenn die Datei nicht mehr vorhanden ist, wird eine neue Registerkarte mit einer Fehlermeldung angezeigt.

    ![Daten für Spark-Anwendung – Zeile herunterladen](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Um einen vollständigen oder relativen Pfad zu kopieren, wählen Sie eine der Optionen **Vollständigen Pfad kopieren** und **Relativen Pfad kopieren** aus, die im erweiterten Dropdownmenü angezeigt werden. Für Azure Data Lake Storage-Dateien startet **Im Azure Storage-Explorer öffnen** den Azure Storage-Explorer und der Ordner gesucht, wenn Sie angemeldet sind.

    ![Daten für Spark-Anwendung – Pfad kopieren](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Wählen Sie die Seitenzahlen unter der Tabelle aus, um durch Seiten zu navigieren, wenn zu viele Zeilen für die Anzeige auf einer Seite vorhanden sind.

    ![Daten für Spark-Anwendung – Seite](./media/apache-spark-history-server/apache-spark-data-page.png)

* Zeigen Sie auf das Fragezeichen neben **Daten**, um die QuickInfo anzuzeigen, oder wählen Sie das Fragezeichen aus, um weitere Informationen zu erhalten.

    ![Daten für Spark-Anwendung – Weitere Informationen](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Senden Sie Feedback mit Problemen, indem Sie **Provide us feedback** (Feedback abgeben) auswählen.

    ![Spark-Diagramm – Feedback abgeben, Forts.](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Registerkarte „Graph“ im Apache Spark-Verlaufsserver

Wählen Sie die Auftrags-ID für den Auftrag aus, den Sie anzeigen möchten. Wählen Sie dann im Menü „Extras“ auf **Graph** aus, um die Auftragsdiagrammansicht aufzurufen.

### <a name="overview"></a>Übersicht

Sie sehen eine Übersicht Ihres Auftrags in dem generierten Auftragsdiagramm. Standardmäßig werden in dem Diagramm alle Aufträge angezeigt. Sie können diese Ansicht anhand der **Auftrags-ID** filtern.

![Spark-Anwendung und Auftragsdiagramm – Auftrags-ID](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Anzeige

Standardmäßig ist die Anzeige **Status** ausgewählt. Sie können den Datenfluss überprüfen, indem Sie **Read** (Gelesen) oder **Written** (Geschrieben) in der Dropdownliste **Anzeige** auswählen.

![Spark-Anwendung und Auftragsdiagramm – Anzeigen](./media/apache-spark-history-server/sparkui-graph-display.png)

In der Diagrammknotenanzeige werden die Farben gemäß der Legende des Wärmebilds angezeigt.

![Spark-Anwendung und Auftragsdiagramm – Wärmebild](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Wiedergabe

Wählen Sie **Wiedergabe** aus, um den Auftrag wiederzugeben. Sie können jederzeit **Stopp** auswählen, um die Wiedergabe zu beenden. Die Auftragsfarben zeigen bei der Wiedergabe verschiedene Status an:

|Color|Bedeutung|
|-|-|
|Grün|Succeeded (Erfolgreich): die abgeschlossen wurden.|
|Orange|Erneut versucht: Auftragsinstanzen, die nicht erfolgreich waren, aber das Endergebnis des Auftrags nicht beeinträchtigen. Diese Aufträge enthielten doppelte oder wiederholte Instanzen, werden später aber möglicherweise erfolgreich ausgeführt.|
|Blau|Running (Wird ausgeführt): die gerade ausgeführt werden.|
|White|Wartend oder übersprungen: deren Ausführung noch ansteht oder bei denen eine Phase übersprungen wurde.|
|Red|Fehler: fehlgeschlagene Aufträge.|

In der folgenden Abbildung werden die Statusfarben Grün, Orange und Blau angezeigt.

![Spark-Anwendung und Auftragsdiagramm – Farbbeispiel: Ausführung](./media/apache-spark-history-server/sparkui-graph-color-running.png)

In der folgenden Abbildung werden die Statusfarben Grün und Weiß angezeigt.

![Spark-Anwendung und Auftragsdiagramm – Farbbeispiel: Übersprungen](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

In der folgenden Abbildung werden die Statusfarben Rot und Grün angezeigt.

![Spark-Anwendung und Auftragsdiagramm – Farbbeispiel: Fehler](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> Jeder Auftrag darf wiedergegeben werden. Für unvollständige Aufträge wird keine Wiedergabe unterstützt.

### <a name="zoom"></a>Zoom

Scrollen Sie mit Ihrem Mausrad, um in das Auftragsdiagramm hinein- oder herauszuzoomen, oder wählen Sie **Mit Zoom anpassen** aus, um das Diagramm an die Anzeige anzupassen.

![Spark-Anwendung und Auftragsdiagramm – Mit Zoom Anpassen](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>QuickInfos

Zeigen Sie mit der Maus auf den Diagrammknoten, um bei fehlgeschlagenen Aufträgen die QuickInfo anzuzeigen, und wählen Sie eine Phase aus, um die Seite zu dieser Phase zu öffnen.

![Spark-Anwendung und Auftragsdiagramm – QuickInfo](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

Auf der Registerkarte „Auftragsdiagramm“ wird eine QuickInfo und ein kleines Symbol angezeigt, wenn es Aufträge gibt, die die folgenden Bedingungen erfüllen:

|Bedingung|BESCHREIBUNG|
|-|-|
|Datenschiefe|Größe der gelesenen Daten > durchschnittliche Größe der gelesenen Daten aller Aufträge innerhalb dieser Phase * 2 und Größe der gelesenen Daten > 10 MB|
|Zeitabweichung|Ausführungszeit > durchschnittliche Ausführungszeit aller Aufgaben in dieser Phase * 2 und Ausführungszeit > 2 Minuten|
   
![Spark-Anwendung und Auftragsdiagramm – Symbol für Schiefe/Abweichung](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Diagrammknotenbeschreibung

Der Auftragsdiagrammknoten zeigt die folgenden Informationen zu den einzelnen Phasen an:

  * ID
  * Name oder Beschreibung
  * Anzahl der Aufträge insgesamt
  * Gelesene Daten: die Summe der Eingabegröße und der Shuffle-Lesegröße
  * Geschriebene Daten: die Summe der Ausgabegröße und der Shuffle-Schreibgröße.
  * Ausführungszeit: die Zeit zwischen der Startzeit des ersten Versuchs und der Endzeit des letzten Versuchs
  * Zeilenanzahl: die Summe der Eingabe und Ausgabedatensätze sowie die Datensätze der zufälligen Lese- und Schreibvorgänge
  * Status

    > [!NOTE]  
    > Standardmäßig zeigt der Auftragsdiagrammknoten Informationen zum letzten Versuch der einzelnen Phasen an (mit Ausnahme der Phase „Ausführungszeit“). Während der Wiedergabe zeigt der Diagrammknoten jedoch Informationen zu jedem Versuch an.
    >  
    > Für die Datengröße des Lese-und Schreibvorgangs ist 1 MB = 1000 KB = 1000 × 1000 Bytes.

### <a name="provide-feedback"></a>Feedback geben

Senden Sie Feedback mit Problemen, indem Sie **Provide us feedback** (Feedback abgeben) auswählen.

![Spark-Anwendung und Auftragsdiagramm – Feedback](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Erkunden der Registerkarte „Diagnose“ im Apache Spark-Verlaufsserver

Um auf die Registerkarte Diagnose zuzugreifen, wählen Sie eine Auftrags-ID aus. Wählen Sie dann im Menü „Extras“ **Diagnose** aus, um die Auftragsdiagnoseansicht aufzurufen. Die Registerkarte „Diagnosis“ (Diagnose) umfasst die folgenden weiteren Registerkarten: **Data Skew** (Datenschiefe), **Time Skew** (Zeitabweichung) und **Executor Usage Analysis** (Analyse zur Executorauslastung).

Überprüfen Sie die **Datenschiefe**, **Zeitabweichung** und **Executor-Nutzungsanalyse**, indem Sie die entsprechenden Registerkarten auswählen.

![SparkUI-Diagnoseregisterkarte „Datenschiefe“, Forts.](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Datenschiefe

Wenn Sie die Registerkarte **Datenschiefe** auswählen, werden die entsprechenden Aufgaben mit Abweichungen basierend auf den angegebenen Parametern angezeigt.

* **Parameter festlegen:** Im ersten Abschnitt werden die Parameter angezeigt, die zum Erkennen von Datenschiefe verwendet werden. Die Standardregel lautet wie folgt: Task-Datenlesevorgänge > 3 durchschnittliche Task-Datenlesevorgänge und Task-Datenlesevorgänge > 10 MB. Wenn Sie Ihre eigene Regel für Aufgaben mit Abweichungen definieren möchten, können Sie die Parameter selbst festlegen. Die Abschnitte **Schiefe Phase** und **Skew Chart** (Diagramm zur Datenschiefe) werden entsprechend aktualisiert.

* **Schiefe Phase:** Im zweiten Abschnitt werden die Phasen angezeigt, die Aufgaben mit Abweichungen entsprechend den oben angegebenen Kriterien enthalten. Wenn in einer Stufe mehr als ein schiefer Auftrag vorhanden ist, werden in der Tabelle mit den schiefen Aufträgen nur die schiefsten Aufträge angezeigt (z. B. der größte Datensatz für die Datenschiefe).

    ![SparkUI-Diagnoseregisterkarte „Datenschiefe“](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Skew Chart** (Diagramm zur Datenschiefe): Wenn eine Zeile in der Tabelle zu den Phasen mit Datenschiefe ausgewählt ist, werden im Diagramm zur Datenschiefe basierend auf den gelesenen Daten und der Ausführungszeit weitere Details zur Auftragsverteilung angezeigt. Die schiefen Aufträge sind rot und die normalen Aufträge sind blau markiert. In dem Diagramm werden bis zu 100 Beispielaufgaben angezeigt, außerdem werden die Aufgabendetails im unteren Bereich auf der rechten Seite angezeigt.

    ![SparkUI – Datenschiefediagramm für Phase 10](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Zeitabweichung

Auf der Registerkarte **Time Skew** (Zeitabweichung) werden schiefe Aufträge basierend auf ihrer Ausführungszeit angezeigt.

* **Parameter festlegen:** Im ersten Abschnitt werden die Parameter angezeigt, die zum Erkennen der Zeitabweichung verwendet werden. Die Standardkriterien zum Erkennen von Zeitabweichungen sind: Ausführungszeit der Aufgabe > 3-mal durchschnittliche Ausführungszeit und Ausführungszeit der Aufgabe > 30 Sekunden. Sie können die Parameter Ihren Anforderungen entsprechend anpassen. Unter **Skewed Stage** (Phase mit Datenschiefe) und **Skew Chart** (Diagramm zur Datenschiefe) werden wie auf der obenstehenden Registerkarte **Data Skew** (Datenschiefe) die Informationen zu den einzelnen Phasen und Aufträgen angezeigt.

* Wenn Sie **Zeitabweichung** auswählen, wird das gefilterte Ergebnis im Abschnitt **Schiefe Phase** entsprechend den im Abschnitt **Parameter festlegen** angegebenen Parametern angezeigt. Wenn Sie ein Element im Abschnitt **Schiefe Phase** auswählen, wird das entsprechende Diagramm in Abschnitt 3 entworfen, und die Aufgabendetails werden im rechten unteren Bereich angezeigt.

    ![SparkUI-Diagnose – Abschnitt „Zeitabweichung“](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Analyse zur Executorauslastung

Im Diagramm zur Executorauslastung werden die Executorzuordnung und der Ausführungsstatus des Spark-Auftrags angezeigt.  

1. Wenn Sie **Executor Usage Analysis** (Executor-Nutzungsanalyse) auswählen, werden vier Kurventypen zur Executor-Nutzung entworfen: die **zugewiesenen Executors**, die **ausgeführten Executors**, die **Executors im Leerlauf** und die **Max. Executor-Instanzen**. Durch jedes Ereignis, bei dem Executors hinzugefügt oder entfernt werden, wird die Anzahl der zugewiesenen Executors erhöht bzw. verringert. Sie können sich auf der Registerkarte „Jobs“ (Aufträge) den Ereignisverlauf ansehen, falls Sie sich für weitere Vergleiche interessieren.

   ![SparkUI-Diagnose – Registerkarte „Executors“](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Wählen Sie das Farbsymbol aus, um den entsprechenden Inhalt in allen Entwürfen auszuwählen oder die Auswahl aufzuheben.

    ![SparkUI-Diagnose – Diagramm auswählen](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Bekannte Probleme

Eingabe-/Ausgabedaten, die resiliente verteilte Datasets (RDDS) verwenden, werden nicht auf der Registerkarte „Daten“ angezeigt.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Synapse Analytics](../overview-what-is.md)
- [Dokumentation zu .NET für Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

