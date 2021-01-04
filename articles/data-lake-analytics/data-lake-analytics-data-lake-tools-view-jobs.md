---
title: Verwenden des Auftragsbrowsers und der Auftragsansicht – Azure Data Lake Analytics
description: In diesem Artikel erfahren Sie, wie Sie den Auftragsbrowser und die Auftragsansicht für Azure Data Lake Analytics-Aufträge verwenden.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 08/02/2017
ms.openlocfilehash: a1e9a9df4c2ec57dfeec8cf5ddd5348228b9cc3e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018561"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Verwenden des Auftragsbrowsers und der Auftragsansicht für Azure Data Lake Analytics
Der Azure Data Lake Analytics-Dienst archiviert die übermittelten Aufträge in einem Abfragespeicher. In diesem Artikel erfahren Sie, wie Sie den Auftragsbrowser und die Auftragsansicht in Azure Data Lake-Tools für Visual Studio verwenden, um die Verlaufsinformationen zu Aufträgen zu suchen. 

Data Lake Analytics archiviert Aufträge standardmäßig für 30 Tage. Der Ablaufzeitraum kann im Azure-Portal über die benutzerdefinierte Ablaufrichtlinie konfiguriert werden. Nach dem Ablauf können Sie nicht mehr auf die Auftragsinformationen zugreifen. 

## <a name="prerequisites"></a>Voraussetzungen
Informationen finden Sie unter [Voraussetzungen für Azure Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Öffnen des Auftragsbrowsers
Rufen Sie den Auftragsbrowser in Visual Studio über **Server-Explorer > Azure > Data Lake Analytics > Aufträge** auf.  Mithilfe des Auftragsbrowsers können Sie auf den Abfragespeicher eines Data Lake Analytics-Kontos zugreifen. Im Auftragsbrowser wird links der Abfragespeicher mit allgemeinen Auftragsinformationen und rechts die Auftragsansicht mit ausführlichen Auftragsinformationen angezeigt.

## <a name="job-view"></a>Die Auftragsansicht
Die Auftragsansicht zeigt detaillierte Informationen zu einem Auftrag an. Sie können einen Auftrag öffnen, indem Sie im Auftragsbrowser darauf doppelklicken oder indem Sie im Data Lake-Menü auf die Auftragsansicht klicken. Daraufhin sollte ein Dialogfeld mit der URL des Auftrags angezeigt werden.

![Der Auftragsbrowser der Data Lake-Tools für Visual Studio](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Die Auftragsansicht enthält Folgendes:

* API-Zusammenfassung
  
    Aktualisieren Sie die Auftragsansicht, um aktuellere Informationen über die ausgeführten Aufträge abzurufen.
  
  * Auftragsstatus (Diagramm):
    
      Der Auftragsstatus stellt die Phasen des Auftrags dar:
    
      ![Screenshot der Phasen eines Azure Data Lake Analytics-Auftrags](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Wird vorbereitet: Laden Sie Ihr Skript in die Cloud hoch, und kompilieren und optimieren Sie das Skript mithilfe des Kompilierungsdiensts.
    * In Warteschlange eingereiht: Aufträge werden in die Warteschlange eingereiht, während sie auf ausreichend Ressourcen warten, oder die Aufträge überschreiten die maximale Anzahl gleichzeitiger Aufträge pro Kontobeschränkung. Die Prioritätseinstellung bestimmt die Reihenfolge der in der Warteschlange eingereihten Aufträge – je niedriger die Zahl, desto höher die Priorität.
    * Running (Wird ausgeführt): Der Auftrag wird derzeit in Ihrem Data Lake Analytics-Konto ausgeführt.
    * Wird abgeschlossen: Der Auftrag wird abgeschlossen (z.B. das Abschließen der Datei).
      
      Der Auftrag kann in jeder Phase einen Fehler hervorrufen. Es kann z.B. Kompilierungsfehler in der Vorbereitungsphase, Timeoutfehler in der Warteschlangenphase und Ausführungsfehler in der Ausführungsphase usw. geben.
  * Basic Information
    
      Die grundlegenden Auftragsinformationen werden im unteren Teil des Bereichs „Auftragszusammenfassung“ angezeigt.
    
      ![Screenshot der Auftragszusammenfassung mit Beschreibungen in Textfeldern](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Job Result (Auftragsergebnis): Erfolgreich oder fehlerhaft. Der Auftrag kann in jeder Phase einen Fehler hervorrufen.
    * Total Duration (Gesamtdauer): Gesamtbetrachtungszeit (Dauer) zwischen dem Zeitpunkt der Übermittlung und dem Endzeitpunkt.
    * Total Compute Time (Computezeit gesamt): Die Summe aller Ausführungszeiten von Scheitelpunkten; die Zeit, in der der Auftrag in nur einem Scheitelpunkt ausgeführt wird. Weitere Informationen zu Scheitelpunkten finden Sie unter „Total Vertices (Summe der Scheitelpunkte)“.
    * Submit/Start/End Time (Übertragungszeit/Startzeit/Beendigungszeit): Der Zeitpunkt, an dem der Auftrag an Data Lake Analytics übertragen wird oder an dem Data Lake Analytics den Auftrag erfolgreich oder fehlerhaft startet oder beendet.
    * Kompilierung/In Warteschlange eingereiht/Wird ausgeführt: Gesamtbetrachtungszeit, die in den Phasen „Wird vorbereitet“, „In Warteschlange eingereiht“ und „Wird ausgeführt“ aufgewendet wurde.
    * Konto: Das Data Lake Analytics-Konto, das für die Ausführung des Auftrags verwendet wurde.
    * Autor: Der Benutzer, der den Auftrag übermittelt hat. Es kann sich um das Konto einer realen Person oder um ein Systemkonto handeln.
    * Priorität: Die Priorität des Auftrags. Je niedriger die Zahl, desto höher die Priorität. Sie wirkt sich nur auf die Reihenfolge der Aufträge in der Warteschlange aus. Die Festlegung einer höheren Priorität verdrängt nicht die zurzeit ausgeführten Aufträge.
    * Parallelität: Die angeforderte maximale Anzahl von gleichzeitigen Azure Data Lake Analytics-Einheiten (Azure Data Lake Analytics Units; ADLAUs), sogenannte Scheitelpunkte. Derzeit entspricht ein Scheitelpunkt einem virtuellen Computer mit zwei virtuellen Kernen und 6 GB RAM, obwohl dies in zukünftigen Data Lake Analytics-Updates upgegradet werden kann.
    * Bytes Left (Übrige Bytes): Bytes, die bis zum Abschließen des Auftrags noch verarbeitet werden müssen.
    * Bytes read/written (Gelesene/geschriebene Bytes): Bytes, die seit dem Beginn der Ausführung des Auftrags gelesen oder geschrieben wurden.
    * Total Vertices (Summe der Scheitelpunkte): Der Auftrag wird in viele Arbeitsschritte unterteilt, und jeder Arbeitsschritt wird Scheitelpunkt genannt. Dieser Wert beschreibt, aus wie vielen Arbeitsschritten der Auftrag besteht. Scheitelpunkte können als eine grundlegende Prozesseinheit (sogenannte Azure Data Lake Analytics-Einheiten) verstanden und parallel ausgeführt werden. 
    * Completed/Running/Failed (Abgeschlossen/Wird ausgeführt/Fehler): Die Anzahl der abgeschlossen, ausgeführten und fehlerhaften Scheitelpunkte. Scheitelpunkte können sowohl aufgrund von Benutzercode als auch von Systemfehlern Fehler ausgeben. Das System führt für fehlerhafte Scheitelpunkte aber automatisch einige Wiederholungsversuche aus. Wenn der Scheitelpunkt nach den Wiederholungsversuchen weiterhin Fehler hervorruft, verursacht der gesamte Auftrag einen Fehler.
* Auftragsdiagramm
  
    Ein U-SQL-Skript stellt die Logik der Transformation von Eingabe- in Ausgabedaten dar. Das Skript wird in der Vorbereitungsphase in einen physischen Ausführungsplan kompiliert und optimiert. Das Auftragsdiagramm zeigt den physischen Ausführungsplan an.  Das folgende Diagramm veranschaulicht diesen Prozess:
  
    ![Azure Data Lake Analytics – Phasenstatus](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Ein Auftrag wird in viele Arbeitsschritte unterteilt. Jeder Arbeitsschritt wird Scheitelpunkt genannt. Die Scheitelpunkte werden als Superscheitelpunkt (Super Vertex), d. h. in Phasen, gruppiert und als Auftragsgraph visualisiert. Die grünen Kästchen im Auftragsdiagramm zeigen die Phasen an.
  
    Jeder Scheitelpunkt einer Phase verrichtet dieselbe Arbeit mit unterschiedlichen Teilen der gleichen Daten. Wenn beispielsweise hunderte von Scheitelpunkten aus einer Datei mit TB-Daten lesen, liest jeder Scheitelpunkt einen Abschnitt. Diese Schnittpunkte werden in derselben Phase gruppiert und arbeiten mit verschiedenen Teilen der gleichen Eingabedatei.
  
  * <a name="state-information"></a>Phaseninformationen
    
      In einer bestimmten Phase werden einige Zahlen im Placard angezeigt.
    
      ![Azure Data Lake Analytics – Auftragsdiagramm, Phasen](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 Extract: Der Name einer Phase, benannt nach einer Zahl und der Vorgangsmethode.
    * 84 vertices (84 Scheitelpunkte): Die Gesamtanzahl der Scheitelpunkte in dieser Phase. Die Abbildung zeigt, in wie viele Arbeitsschritte diese Phase unterteilt wurde.
    * 12.90 s/vertex (12,9 s/Scheitelpunkt): Die durchschnittliche Ausführungszeit eines Scheitelpunkts für diese Phase. In dieser Abbildung wurde sie mit SUM berechnet (jede Scheitelpunkt-Ausführungszeit) / (Gesamtanzahl der Scheitelpunkte). Dies bedeutet, dass wenn Sie alle Scheitelpunkte zuweisen könnten, die parallel ausgeführt werden, würde die gesamte Phase in 12,9 s abgeschlossen werden. Dies bedeutet auch, dass wenn alle Arbeiten in dieser Phase nacheinander erfolgen, würden die Kosten #vertices * die durchschnittliche Zeit betragen.
    * 850,895 rows written (850.895 geschriebene Zeilen): Gesamtanzahl der in dieser Phase geschriebenen Zeilen.
    * R/W: Menge der in dieser Phase in Bytes gelesenen (read) und geschriebenen (written) Daten.
    * Farben: Farben werden in der Phase verwendet, um den unterschiedlichen Status von Scheitelpunkten anzugeben.
      
      * Grün zeigt an, dass der Scheitelpunkt erfolgreich durchgeführt wurde.
      * Orange zeigt an, dass für den Scheitelpunkt ein Wiederholungsversuch durchgeführt wurde. Der wiederholte Scheitelpunkts hat einen Fehler verursacht, konnte jedoch automatisch und erfolgreich vom System wiederholt werden, und die gesamte Phase wurde erfolgreich abgeschlossen. Wenn der Scheitelpunkt wiederholt wird, jedoch erneut einen Fehler verursacht, ändert sich die Farbe auf Rot, und der gesamte Auftrag schlägt fehl.
      * Rot deutet auf einen Fehler hin, was bedeutet, dass ein bestimmter Scheitelpunkt mehrmals vom System wiederholt wurde, aber immer noch fehlerhaft ist. Dieses Szenario führt dazu, dass der gesamte Auftrag fehlschlägt.
      * Blau bedeutet, dass ein bestimmter Scheitelpunkt ausgeführt wird.
      * Weiß zeigt an, dass der Scheitelpunkt wartet. Der Scheitelpunkt wartet möglicherweise auf die Durchführung, sobald eine ADLAU verfügbar wird, oder auf Eingabe, da seine Eingabedaten möglicherweise nicht bereitstehen.
      
      Sie erhalten weitere Informationen zu der Phase, indem Sie den Mauszeiger über einen Status bewegen:
      
      ![Azure Data Lake Analytics – Auftragsdiagramm, Details zu Phasen](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Vertices (Scheitelpunkte): Beschreibt die Details zu den Scheitelpunkten, z.B. wie viele Scheitelpunkte insgesamt vorhanden sind, wie viele Scheitelpunkte abgeschlossen wurden, wie viele fehlerhaft sind oder noch ausgeführt werden bzw. warten usw.
  * Data read cross/intra pod (Auf mehreren/auf demselben Pod gelesene Daten): Dateien und Daten werden in mehreren Pods im verteilten Dateisystem gespeichert. Der Wert beschreibt, wie viele Daten in dem gleichen oder mehreren Pods gelesen wurden.
  * Total compute time (Computezeit gesamt): Die Summe aller Ausführungszeiten von Scheitelpunkten in dieser Phase; die Zeit, die die Ausführung aller Arbeiten in der Phase in nur einem Scheitelpunkt kosten würde.
  * Data and rows written/read (Geschriebene/gelesene Daten und Zeilen): Gibt an, wie viele Daten oder Zeilen gelesen oder geschrieben wurden oder gelesen werden müssen.
  * Vertex read failures (Fehler beim Lesen des Scheitelpunkts): Beschreibt, wie viele Scheitelpunkte beim Auslesen von Daten einen Fehler verursacht haben.
  * Vertex duplicate discards (Verwerfen von Scheitelpunktduplikaten): Wenn ein Scheitelpunkt zu langsam ausgeführt wird, kann das System mehrere Scheitelpunkte planen, die den gleichen Arbeitsschritt ausführen sollen. Die übrigen Scheitelpunkte werden verworfen, sobald einer der Scheitelpunkte erfolgreich abgeschlossen wird. „Vertex duplicate discards“ (Anzahl der verworfenen Vertexduplikate) zeichnet die Anzahl der Scheitelpunkte auf, die in der Phase als Duplikate verworfen werden.
  * Vertex revocations (Widerrufen des Scheitelpunkts): Der Scheitelpunkt war erfolgreich, musste jedoch aus bestimmten Gründen später erneut ausgeführt werden. Wenn der spätere Scheitelpunkt Eingabezwischendaten verliert, fordert er den vorherigen Scheitelpunkt zu einer erneuten Ausführung auf.
  * Vertex schedule executions (Geplante Ausführung von Scheitelpunkten): Die Gesamtzeit, die für die Scheitelpunkte geplant wurde.
  * Min/Average/Max Vertex data read (Minimale/durchschnittliche/maximale vom Scheitelpunkt gelesene Datenmenge): Die minimale, durchschnittliche oder maximale Menge aller vom Scheitelpunkt gelesenen Daten.
  * Duration (Dauer): Die Gesamtbetrachtungszeit einer Phase, die Sie für das Laden des Profils benötigen, um diesen Wert anzuzeigen.
  * Auftragswiedergabe
    
      Data Lake Analytics führt Aufträge aus und archiviert die Scheitelpunkte, die die Auftragsinformationen ausführen, z.B. die Start- und Endzeit von Scheitelpunkten, wann sie Fehler verursachen, wie sie wiederholt werden usw. Alle Informationen werden automatisch im Abfragespeicher protokolliert und im entsprechenden Auftragsprofil gespeichert. Sie können das Auftragsprofil über „Load Profile“ (Profil laden) in der Auftragsansicht herunterladen und die Auftragswiedergabe nach dem Herunterladen des Auftragsprofils anzeigen.
    
      Die Auftragswiedergabe ist eine kurze Visualisierung dessen, was im Cluster geschehen ist. Damit können Sie den Status der Auftragsausführung verfolgen und Unregelmäßigkeiten und Engpässe in Bezug auf die Leistung sehr kurzfristig visuell erkennen (normalerweise in weniger als 30 s).
  * Anzeigen des Auftragswärmebilds 
    
      Das Auftragswärmebild kann in der Dropdownliste „Display“ (Anzeigen) unter „Job Graph“ (Auftragsdiagramm) ausgewählt werden. 
    
      ![Azure Data Lake Analytics – Auftragsdiagramm, Anzeigen des Wärmebilds](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Das Wärmebild zeigt E/A, Zeit und Durchsatz eines Auftrags. Sie können mithilfe des Wärmebilds herausfinden, wofür der Auftrag die meiste Zeit benötigt oder ob Ihr Auftrag auf der E/A-Ebene operiert usw.
    
      ![Azure Data Lake Analytics – Auftragsdiagramm, Wärmebildbeispiel](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Fortschritt: Der Fortschritt der Auftragsausführung, siehe Informationen unter „Phaseninformationen“.
    * Data read/written (Gelesene/geschriebene Daten): Das Wärmebild der insgesamt in den einzelnen Phasen gelesenen bzw. geschriebenen Daten.
    * Compute time (Computezeit): Das Wärmebild von SUM (Ausführungszeiten aller Scheitelpunkte); die Zeit, die die Ausführung aller Arbeiten in der Phase mit nur einem Scheitelpunkt dauern würde.
    * Average execution time per node (Durchschnittliche Ausführungszeit pro Knoten): Das Wärmebild von SUM (Ausführungszeit aller Scheitelpunkte) / (Anzahl der Scheitelpunkte). Dies bedeutet, dass wenn Sie alle Scheitelpunkte zuweisen könnten, die parallel ausgeführt werden, würde die gesamte Phase in diesem Zeitfenster abgeschlossen werden.
    * Input/Output throughput (Eingabe-/Ausgabedurchsatz): Das Wärmebild des Eingabe-/Ausgabedurchsatzes einer jeden Phase; Sie können hiermit erkennen, ob Ihr Auftrag ein E/A-Grenzauftrag ist.
* Metadatenvorgänge
  
    Sie können in Ihrem U-SQL-Skript einige Metadatenvorgänge wie z.B. das Erstellen einer Datenbank, das Löschen einer Tabelle usw. ausführen. Diese Vorgänge werden unter „Metadata Operations“ (Metadatenvorgänge) nach der Kompilierung dargestellt. Sie können hier Assertionen suchen und Entitäten erstellen oder ablegen.
  
    ![Azure Data Lake Analytics – Auftragsansicht, Metadatenvorgänge](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Der Statusverlauf
  
    Den Statusverlauf (State History) wird ebenfalls unter „Auftragszusammenfassung“ dargestellt, Sie können hier jedoch weitere Informationen erhalten. Hier finden Sie z.B. detaillierte Informationen dazu, wann der Auftrag vorbereitet, in die Warteschlange eingereiht, ausgeführt oder beendet wurde. Sie sehen außerdem, wie oft der Auftrag kompiliert wurde (CcsAttempts: 1), wann der Auftrag an den Cluster übermittelt wurde (Detail: Dispatching job to cluster) usw.
  
    ![Azure Data Lake Analytics – Auftragsansicht, Statusverlauf](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnose
  
    Das Tool diagnostiziert die Ausführung eines Auftrags automatisch. Sie erhalten Benachrichtigungen, falls bei Ihren Aufträgen Fehler oder Leistungsprobleme auftreten. Beachten Sie bitte, dass Sie das Profil herunterladen müssen, um hier vollständige Informationen erhalten. 
  
    ![Azure Data Lake Analytics – Auftragsansicht, Diagnose](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Warnings (Warnungen): Eine Warnung wird hier mit Compilerwarnung angezeigt. Klicken Sie auf den Link „x issue(s) found“ (x gefundene Probleme), um weitere Details anzuzeigen.
  * Vertex run too long (Scheitelpunkt zu lange ausgeführt): Wenn die Zeit für einen beliebigen Scheitelpunkt abläuft (z.B. 5 Stunden), finden Sie hier die entsprechenden Probleme.
  * Resource usage (Ressourcenverbrauch): Wenn Sie mehr oder zu wenig Parallelität als erforderlich zuweisen, finden Sie hier die entsprechenden Probleme. Sie können auf „Resource usage“ klicken, um weitere Details anzuzeigen und Was-wäre-wenn-Szenarien auszuführen, um eine bessere Ressourcenzuweisung zu suchen (weitere Informationen finden Sie in diesem Handbuch).
  * Memory check (Speicherüberprüfung): Wenn ein beliebiger Scheitelpunkt mehr als 5 GB Speicher verwendet, finden Sie hier die entsprechenden Probleme. Die Ausführung eines Auftrags kann vom System abgebrochen werden, wenn mehr Speicher als die Systemeinschränkung verwendet wird.

## <a name="job-detail"></a>Auftragsdetails
Unter „Job Detail“ (Auftragsdetails) werden die detaillierten Informationen des Auftrags angezeigt, einschließlich „Script“ (Skript), „Resources“ (Ressourcen) und „Vertex Execution View“ (Scheitelpunktausführungsansicht).

![Azure Data Lake Analytics – Auftragsdetails](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Skript
  
    Das U-SQL-Skript des Auftrags wird im Abfragespeicher gespeichert. Sie können das ursprüngliche U-SQL-Skript anzeigen und es erneut senden, falls erforderlich.
* Ressourcen
  
    Sie finden die Auftragskompilierungsausgaben im Abfragespeicher unter „Resources“ gespeichert. Hier Sie finden z.B. „algebra.xml“, womit das Auftragsdiagramm angezeigt wird, die von Ihnen registrierten Assemblys usw.
* Scheitelpunktausführungsansicht
  
    Es zeigt die Ausführungsdetails zu den Scheitelpunkten. Das Auftragsprofil archiviert jedes Ausführungsprotokoll von Scheitelpunkten, wie z.B. die gelesene bzw. geschriebene Gesamtdatenmenge, die Laufzeit, den Status usw. Durch diese Sicht erhalten Sie weitere Informationen zur Ausführung eines Auftrags. Weitere Informationen finden Sie unter [Use the Vertex Execution View in Data Lake Tools for Visual Studio (Verwenden der Scheitelpunktausführungsansicht in Data Lake-Tools für Visual Studio)](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Nächste Schritte
* Informationen zum Protokollieren von Diagnoseinformationen finden Sie unter [Zugreifen auf Diagnoseprotokolle für Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Eine komplexere Abfrage finden Sie unter [Analysieren von Websiteprotokollen mit Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Weitere Informationen zur Verwendung der Scheitelpunktausführungsansicht finden Sie unter [Verwenden der Scheitelpunktausführungsansicht in Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

