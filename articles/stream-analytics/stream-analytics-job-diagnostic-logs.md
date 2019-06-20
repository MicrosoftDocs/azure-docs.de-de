---
title: Problembehandlung für Azure Stream Analytics mit Diagnoseprotokollen
description: In diesem Artikel wird beschrieben, wie Sie Diagnoseprotokolle in Azure Stream Analytics analysieren.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: ff2930fbe0e53c4b3c1223f87919c0913296d07c
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515914"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Problembehandlung bei Azure Stream Analytics mit Diagnoseprotokollen

In einigen Fällen beendet ein Azure Stream Analytics-Auftrag unerwartet die Verarbeitung. Es ist wichtig, diese Art von Ereignissen behandeln zu können. Fehler können durch ein unerwartetes Abfrageergebnis, die Verbindung zu Geräten oder einen unerwarteten Dienstausfall verursacht werden. Durch die Diagnoseprotokolle in Stream Analytics können Sie die Ursache der Probleme bei ihrem Auftreten feststellen und die Wiederherstellungszeit verkürzen.

## <a name="log-types"></a>Protokolltypen

Stream Analytics bietet zwei Typen von Protokollen:

* [Aktivitätsprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (immer aktiviert) bieten Einblicke in die Vorgänge, die bei Aufträgen durchgeführt werden.

* [Diagnoseprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (konfigurierbar), die umfangreichere Einblicke in sämtliche Vorgänge eines Auftrags bieten. Diagnoseprotokolle starten, wenn der Auftrag erstellt wurde, und enden, wenn der Auftrag gelöscht wird. Sie behandeln Ereignisse bei der Aktualisierung des Auftrags und während der Ausführung.

> [!NOTE]
> Sie können Dienste wie Azure Storage, Azure Event Hubs und Azure Monitor-Protokolle verwenden, um nicht konforme Daten zu analysieren. Gebühren werden basierend auf dem Preismodell für diese Dienste in Rechnung gestellt.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Debuggen mithilfe von Aktivitätsprotokollen

Aktivitätsprotokolle sind standardmäßig aktiviert und geben allgemeine Einblicke in Vorgänge, die von Ihrem Stream Analytics-Auftrag ausgeführt werden. In Aktivitätsprotokollen enthaltene Informationen können helfen, die Grundursache von Problemen zu finden, die Auswirkungen auf den Auftrag haben. Führen Sie die folgenden Schritte aus, um Aktivitätsprotokolle in Stream Analytics zu verwenden:

1. Melden Sie sich beim Azure-Portal an, und wählen Sie **Aktivitätsprotokoll** unter **Übersicht** aus.

   ![Stream Analytics-Aktivitätsprotokoll](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Sie sehen eine Liste der Vorgänge, die ausgeführt wurden. Jeder Vorgang, der bei Ihrem Auftrag einen Fehler verursacht hat, ist mit einer roten Infoblase versehen.

3. Klicken Sie auf einen Vorgang, um dessen Zusammenfassung anzuzeigen. Die Informationen sind hier häufig beschränkt. Weitere Informationen zum Vorgang finden Sie unter **JSON**.

   ![Vorgangszusammenfassung im Stream Analytics-Aktivitätsprotokoll](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Scrollen Sie nach unten zum JSON-Abschnitt **Eigenschaften**, der Details zum Fehler bereitstellt, der den fehlerhaften Vorgang verursacht hat. In diesem Beispiel war der Fehler auf einen Runtimefehler aufgrund außerhalb des zulässigen Bereichs liegender Breitengradwerte zurückzuführen. Abweichungen in den Daten, die von einem Stream Analytics-Auftrag verarbeitet werden, bewirken einen Datenfehler. Es stehen Informationen zu verschiedenen [Fehlern bei der Eingabe und Ausgabe von Daten sowie zu den Gründen für deren Auftreten](https://docs.microsoft.com/azure/stream-analytics/data-errors) bereit.

   ![JSON-Fehlerdetails](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Sie können basierend auf der Fehlermeldung im JSON-Format Korrekturmaßnahmen vornehmen. In diesem Beispiel müssen Prüfungen, ob der Breitengradwert zwischen -90 Grad und 90 Grad liegt, der Abfrage hinzugefügt werden.

6. Wenn die Fehlermeldung in den Aktivitätsprotokollen bei der Identifizierung der Grundursache nicht hilfreich ist, aktivieren Sie Diagnoseprotokolle, und verwenden Sie Azure Monitor-Protokolle.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Senden von Diagnosedaten an Azure Monitor-Protokolle

Sie sollten unbedingt Diagnoseprotokolle aktivieren und an Azure Monitor-Protokolle senden. Diagnoseprotokolle sind standardmäßig **deaktiviert**. Um die Diagnoseprotokolle zu aktivieren, führen Sie folgende Schritte aus:

1.  Melden Sie sich im Azure-Portal an, und navigieren Sie zu Ihrem Stream Analytics-Auftrag. Wählen Sie unter **Überwachung** die Option **Diagnoseprotokolle** aus. Wählen Sie dann **Diagnose aktivieren** aus.

    ![Blattnavigation zu Diagnoseprotokollen](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Erstellen Sie einen **Namen** in **Diagnoseeinstellungen**, und aktivieren Sie das Kontrollkästchen neben **An Log Analytics senden**. Fügen Sie dann einen bereits vorhandenen **Log Analytics-Arbeitsbereich** hinzu, oder erstellen Sie einen neuen. Aktivieren Sie die Kontrollkästchen für **Ausführung** und **Erstellung** unter **LOG** und **AllMetrics** unter **METRIK**. Klicken Sie auf **Speichern**.

    ![Einstellungen für Diagnoseprotokolle](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Wenn Ihr Stream Analytics-Auftrag gestartet wird, werden Diagnoseprotokolle an Ihren Log Analytics-Arbeitsbereich weitergeleitet. Navigieren Sie zum Log Analytics-Arbeitsbereich, und wählen Sie **Protokolle** unter dem Abschnitt **Allgemein** aus.

   ![Azure Monitor-Protokolle unter dem Abschnitt „Allgemein“](./media/stream-analytics-job-diagnostic-logs/log-analytics-logs.png)

4. Sie können [Ihre eigene Abfrage schreiben](../azure-monitor/log-query/get-started-portal.md), um nach Begriffen zu suchen, Trends zu identifizieren, Muster zu analysieren und basierend auf Ihren Daten Informationen zu gewinnen. Beispielsweise können Sie eine Abfrage schreiben, um nur die Diagnoseprotokolle mit der Nachricht „Fehler beim Streamingauftrag“ herauszufiltern. Diagnoseprotokolle von Azure Stream Analytics sind in der **AzureDiagnostics**-Tabelle gespeichert.

   ![Diagnoseabfrage und Ergebnisse](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-query.png)

5. Speichern Sie eine Abfrage, die nach den richtigen Protokollen sucht, indem Sie **Speichern** auswählen und einen Namen und eine Kategorie angeben. Anschließend können Sie eine Warnung erstellen, indem Sie **Neue Warnungsregel** auswählen. Geben Sie als Nächstes die Warnungsbedingung an. Wählen Sie **Bedingung** aus, und geben Sie Schwellenwert und Häufigkeit für die Auswertung dieser benutzerdefinierten Protokollsuche an.  

   ![Diagnoseprotokoll-Suchabfrage](./media/stream-analytics-job-diagnostic-logs/search-query.png)

6. Wählen Sie die Aktionsgruppe aus, und geben Sie Warnungsdetails wie Name und Beschreibung an, bevor Sie die Warnungsregel erstellen können. Sie können die Diagnoseprotokolle der verschiedenen Aufträge an den gleichen Log Analytics-Arbeitsbereich weiterleiten. So können Sie einmal Warnungen einrichten, die für alle Aufträge gelten.  

## <a name="diagnostics-log-categories"></a>Kategorien der Diagnoseprotokolle

Azure Stream Analytics erfasst zwei Kategorien von Diagnoseprotokollen:

* **Erstellung**: Erfasst Protokollereignisse zu Auftragserstellungsvorgängen wie Erstellen von Aufträgen, Hinzufügen und Löschen von Eingaben und Ausgaben, Hinzufügen und Aktualisieren der Abfrage sowie Starten und Beenden des Auftrags.

* **Ausführung**: Erfasst Ereignisse, die während der Ausführung eines Auftrags auftreten.
    * Verbindungsfehler
    * Datenverarbeitungsfehler wie Folgende:
        * Ereignisse, die nicht mit der Abfragedefinition übereinstimmen (nicht übereinstimmende Feldtypen und Werte, fehlende Felder usw.)
        * Fehler bei der Auswertung von Ausdrücken
    * Andere Ereignisse und Fehler

## <a name="diagnostics-logs-schema"></a>Diagnoseprotokollschema

Alle Protokolle werden im JSON-Format gespeichert. Jeder Eintrag enthält folgende allgemeine Zeichenfolgenfelder:

NAME | BESCHREIBUNG
------- | -------
time | Zeitstempel (UTC) des Protokolls.
resourceId | ID der Ressource, über die der Vorgang stattfand, in Großbuchstaben. Beinhaltet die Abonnement-ID, die Ressourcengruppe und den Auftragsnamen. Beispiel: **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Protokollkategorie: **Ausführung** oder **Erstellung**.
operationName | Der Name des protokollierten Vorgangs. Beispielsweise **Ereignisse senden: Fehler beim Schreiben der SQL-Ausgabe nach mysqloutput**.
status | Der Status des Vorgangs. Beispiele: **Fehlgeschlagen** oder **Erfolgreich**.
level | Protokollebene. Beispiele: **Fehler**, **Warnung** oder **Information**.
properties | Spezifisches Detail des Protokolleintrags; als JSON-Zeichenfolge serialisiert. Weitere Informationen finden Sie in den folgenden Abschnitten dieses Artikels.

### <a name="execution-log-properties-schema"></a>Eigenschaftsschema der Ausführungsprotokolle

Ausführungsprotokolle enthalten Informationen zu Ereignissen, die während der Ausführung des Stream Analytics-Auftrags aufgetreten sind. Das Schema der Eigenschaften variiert abhängig davon, ob das Ereignis ein Datenfehler oder ein generisches Ereignis ist.

### <a name="data-errors"></a>Datenfehler

Alle Fehler, die auftreten, während der Auftrag Daten in dieser Kategorie von Protokollen verarbeitet. Diese Protokolle werden am häufigsten bei Lese-, Serialisierungs- und Schreibvorgängen von Daten erstellt. Diese Protokolle enthalten keine Verbindungsfehler. Verbindungsfehler werden als generische Ereignisse behandelt.

NAME | BESCHREIBUNG
------- | -------
`Source` | Name der Auftragseingabe oder -ausgabe, bei der der Fehler aufgetreten ist.
`Message` | Mit dem Fehler verknüpfte Meldung.
Type | Fehlertyp. Beispiele: **DataConversionError**, **CsvParserError** oder **ServiceBusPropertyColumnMissingError**.
Daten | Enthält Daten, die hilfreich sind, um die Ursache des Fehlers genau zu lokalisieren. Unterliegt je nach Größe Kürzungen.

Je nach dem Wert für **operationName** entsprechen Datenfehler folgendem Schema:

* **Die Serialisierung von Ereignissen** tritt bei Lesevorgängen von Ereignissen auf. Sie treten auf, wenn die Daten bei der Eingabe aus einem der folgenden Gründen nicht das Abfrageschema erfüllen:

   * *Typenkonflikt während des (De)serialisierens von Ereignissen*: Identifiziert das Feld, das den Fehler verursacht.

   * *Ein Ereignis kann nicht gelesen werden, ungültige Serialisierung*: Enthält Informationen über die Stelle in den Eingabedaten, an der der Fehler aufgetreten ist. Enthält den Blobnamen für die Eingabe mit formlosen Objekten, Offset und eine Stichprobe der Daten.

* **Das Senden von Ereignissen** tritt bei Schreibvorgängen auf. Diese identifizieren das Streamingereignis, das den Fehler verursacht hat.

### <a name="generic-events"></a>Generische Ereignisse

Generische Ereignisse verarbeiten alles andere.

NAME | BESCHREIBUNG
-------- | --------
Error | (optional) Fehlerinformationen. In der Regel sind dies Ausnahmeinformationen, sofern diese verfügbar sind.
`Message`| Protokollmeldung.
Type | Meldungstyp. Wird der internen Kategorisierung von Fehlern zugeordnet. Beispiele: **JobValidationError** oder **BlobOutputAdapterInitializationFailure**.
Korrelations-ID | Ein [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)-Wert, der die Auftragsausführung eindeutig identifiziert. Alle Ausführungsprotokolleinträge ab dem Zeitpunkt, an dem der Auftrag gestartet wird, bis zum Beenden des Auftrags weisen denselben Wert für **Korrelations-ID** auf.

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Referenz zur Stream Analytics-Abfragesprache](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenz zur REST-API für die Stream Analytics-Verwaltung](https://msdn.microsoft.com/library/azure/dn835031.aspx)
