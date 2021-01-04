---
title: Problembehandlung von Azure Stream Analytics mit Ressourcenprotokollen
description: In diesem Artikel wird beschrieben, wie Sie Ressourcenprotokolle in Azure Stream Analytics analysieren.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.custom: contperf-fy21q1
ms.date: 06/18/2020
ms.openlocfilehash: b29e0f99cb4549370be49dc5a1b11d367e30d8c0
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029140"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-resource-logs"></a>Problembehandlung von Azure Stream Analytics mit Ressourcenprotokollen

In einigen Fällen beendet ein Azure Stream Analytics-Auftrag unerwartet die Verarbeitung. Es ist wichtig, diese Art von Ereignissen behandeln zu können. Fehler können durch ein unerwartetes Abfrageergebnis, die Verbindung zu Geräten oder einen unerwarteten Dienstausfall verursacht werden. Durch die Ressourcenprotokolle in Stream Analytics können Sie die Ursache der Probleme bei ihrem Auftreten feststellen und die Wiederherstellungszeit verkürzen.

Ressourcenprotokolle sollten unbedingt für alle Aufträge aktiviert werden, da dies beim Debuggen und Überwachen sehr hilfreich ist.

## <a name="log-types"></a>Protokolltypen

Stream Analytics bietet zwei Typen von Protokollen:

* [Aktivitätsprotokolle](../azure-monitor/platform/platform-logs-overview.md) (immer aktiviert) bieten Einblicke in die Vorgänge, die bei Aufträgen durchgeführt werden.

* [Ressourcenprotokolle](../azure-monitor/platform/platform-logs-overview.md) (konfigurierbar), die umfangreichere Einblicke in sämtliche Vorgänge eines Auftrags bieten. Ressourcenprotokolle starten, wenn der Auftrag erstellt wurde, und enden, wenn der Auftrag gelöscht wird. Sie behandeln Ereignisse bei der Aktualisierung des Auftrags und während der Ausführung.

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

4. Scrollen Sie nach unten zum JSON-Abschnitt **Eigenschaften**, der Details zum Fehler bereitstellt, der den fehlerhaften Vorgang verursacht hat. In diesem Beispiel war der Fehler auf einen Runtimefehler aufgrund außerhalb des zulässigen Bereichs liegender Breitengradwerte zurückzuführen. Abweichungen in den Daten, die von einem Stream Analytics-Auftrag verarbeitet werden, bewirken einen Datenfehler. Es stehen Informationen zu verschiedenen [Fehlern bei der Eingabe und Ausgabe von Daten sowie zu den Gründen für deren Auftreten](./data-errors.md) bereit.

   ![JSON-Fehlerdetails](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Sie können basierend auf der Fehlermeldung im JSON-Format Korrekturmaßnahmen vornehmen. In diesem Beispiel müssen Prüfungen, ob der Breitengradwert zwischen -90 Grad und 90 Grad liegt, der Abfrage hinzugefügt werden.

6. Wenn die Fehlermeldung in den Aktivitätsprotokollen bei der Identifizierung der Grundursache nicht hilfreich ist, aktivieren Sie Ressourcenprotokolle, und verwenden Sie Azure Monitor-Protokolle.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Senden von Diagnosedaten an Azure Monitor-Protokolle

Sie sollten unbedingt Ressourcenprotokolle aktivieren und an Azure Monitor-Protokolle senden. Sie sind standardmäßig **deaktiviert**. Um sie zu aktivieren, führen Sie die folgenden Schritte aus:

1.  Sofern nicht bereits geschehen, erstellen Sie einen Log Analytics-Arbeitsbereich. Es wird empfohlen, dass sich Ihr Log Analytics-Arbeitsbereich in derselben Region befindet, in der auch Ihr Stream Analytics-Auftrag ausgeführt wird.

2.  Melden Sie sich im Azure-Portal an, und navigieren Sie zu Ihrem Stream Analytics-Auftrag. Wählen Sie unter **Überwachung** die Option **Diagnoseprotokolle** aus. Wählen Sie dann **Diagnose aktivieren** aus.

    ![Blattnavigation zu Ressourcenprotokollen](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Geben Sie einen **Namen** in **Diagnoseeinstellungen: Name** ein, und aktivieren Sie die Kontrollkästchen für **Ausführung** und **Erstellung** unter **log** sowie **AllMetrics** unter **metric**. Aktivieren Sie dann **An Log Analytics senden**, und wählen Sie Ihren Arbeitsbereich aus. Klicken Sie auf **Speichern**.

    ![Einstellungen für Ressourcenprotokolle](./media/stream-analytics-job-diagnostic-logs/logs-setup.png)

3. Wenn Ihr Stream Analytics-Auftrag gestartet wird, werden Ressourcenprotokolle an Ihren Log Analytics-Arbeitsbereich weitergeleitet. Wenn Sie Ressourcenprotokolle für Ihren Auftrag anzeigen möchten, wählen Sie die Option **Protokolle** im Abschnitt **Überwachung** aus.

   ![Screenshot: Menü „Allgemein“ mit ausgewählter Option „Protokolle“.](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics bietet vordefinierte Abfragen, mit denen Sie problemlos nach den für Sie interessanten Protokollen suchen können. Sie können alle vordefinierten Abfragen im linken Bereich auswählen und dann **Ausführen** auswählen. Ihnen werden die Abfrageergebnisse im unteren Bereich angezeigt. 

   ![Der Screenshot zeigt Protokolle für einen Stream Analytics-Auftrag an.](./media/stream-analytics-job-diagnostic-logs/logs-example.png)

## <a name="resource-log-categories"></a>Ressourcenprotokollkategorien

Azure Stream Analytics erfasst zwei Kategorien von Ressourcenprotokollen:

* **Erstellung**: Erfasst Protokollereignisse zu Auftragserstellungsvorgängen wie Erstellen von Aufträgen, Hinzufügen und Löschen von Eingaben und Ausgaben, Hinzufügen und Aktualisieren der Abfrage sowie Starten und Beenden des Auftrags.

* **Ausführung**: Erfasst Ereignisse, die während der Ausführung eines Auftrags auftreten.
    * Verbindungsfehler
    * Datenverarbeitungsfehler wie Folgende:
        * Ereignisse, die nicht mit der Abfragedefinition übereinstimmen (nicht übereinstimmende Feldtypen und Werte, fehlende Felder usw.)
        * Fehler bei der Auswertung von Ausdrücken
    * Andere Ereignisse und Fehler

## <a name="resource-logs-schema"></a>Ressourcenprotokollschema

Alle Protokolle werden im JSON-Format gespeichert. Jeder Eintrag enthält folgende allgemeine Zeichenfolgenfelder:

Name | BESCHREIBUNG
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

Alle Fehler, die auftreten, während der Auftrag Daten in dieser Kategorie von Protokollen verarbeitet. Diese Protokolle werden am häufigsten bei Lese-, Serialisierungs- und Schreibvorgängen von Daten erstellt. Diese Protokolle enthalten keine Verbindungsfehler. Verbindungsfehler werden als generische Ereignisse behandelt. Weitere Informationen zu den Gründen für verschiedene Fehler bei der Ein- und Ausgabe von Daten finden Sie im Artikel [Datenfehler in Azure Stream Analytics](./data-errors.md).

Name | BESCHREIBUNG
------- | -------
`Source` | Name der Auftragseingabe oder -ausgabe, bei der der Fehler aufgetreten ist.
`Message` | Mit dem Fehler verknüpfte Meldung.
type | Fehlertyp. Beispiele: **DataConversionError**, **CsvParserError** oder **ServiceBusPropertyColumnMissingError**.
Daten | Enthält Daten, die hilfreich sind, um die Ursache des Fehlers genau zu lokalisieren. Unterliegt je nach Größe Kürzungen.

Je nach dem Wert für **operationName** entsprechen Datenfehler folgendem Schema:

* **Die Serialisierung von Ereignissen** tritt bei Lesevorgängen von Ereignissen auf. Sie treten auf, wenn die Daten bei der Eingabe aus einem der folgenden Gründen nicht das Abfrageschema erfüllen:

   * *Typenkonflikt während des (De)serialisierens von Ereignissen*: Identifiziert das Feld, das den Fehler verursacht.

   * *Ein Ereignis kann nicht gelesen werden, ungültige Serialisierung*: Enthält Informationen über die Stelle in den Eingabedaten, an der der Fehler aufgetreten ist. Enthält den Blobnamen für die Eingabe mit formlosen Objekten, Offset und eine Stichprobe der Daten.

* **Das Senden von Ereignissen** tritt bei Schreibvorgängen auf. Diese identifizieren das Streamingereignis, das den Fehler verursacht hat.

### <a name="generic-events"></a>Generische Ereignisse

Generische Ereignisse verarbeiten alles andere.

Name | BESCHREIBUNG
-------- | --------
Fehler | (optional) Fehlerinformationen. In der Regel sind dies Ausnahmeinformationen, sofern diese verfügbar sind.
`Message`| Protokollmeldung.
type | Meldungstyp. Wird der internen Kategorisierung von Fehlern zugeordnet. Beispiele: **JobValidationError** oder **BlobOutputAdapterInitializationFailure**.
Korrelations-ID | GUID, die die Auftragsausführung eindeutig identifiziert. Alle Ausführungsprotokolleinträge ab dem Zeitpunkt, an dem der Auftrag gestartet wird, bis zum Beenden des Auftrags weisen denselben Wert für **Korrelations-ID** auf.

## <a name="next-steps"></a>Nächste Schritte

* [Datenfehler in Azure Stream Analytics](./data-errors.md)
* [Referenz zur Stream Analytics-Abfragesprache](/stream-analytics-query/stream-analytics-query-language-reference)