---
title: Lösungsmuster in Azure Stream Analytics
description: Informationen zu Lösungsmustern für Azure Stream Analytics.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 80843abe130f1388a5d4081adab7b9128446763b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65761993"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Lösungsmuster in Azure Stream Analytics

Wie viele andere Dienste in Azure wird Stream Analytics am besten mit anderen Diensten verwendet, um eine größere End-to-End-Lösung zu erstellen. Dieser Artikel beschreibt einfache Azure Stream Analytics-Lösungen und verschiedene Architekturmuster. Sie können auf Basis dieser Muster komplexere Lösungen entwickeln. Die in diesem Artikel beschriebenen Muster können in einer Vielzahl von Szenarien verwendet werden. Beispiele für Muster für bestimmte Szenarien finden Sie unter [Azure-Lösungsarchitekturen](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-with-a-real-time-dashboard"></a>Erstellen eines Stream Analytics-Auftrags mit einem Echtzeitdashboard

Da Azure Stream Analytics sehr benutzerfreundlich ist, können Sie schnell Echtzeitdashboards und Warnungen bereitstellen. Eine einfache Lösung erfasst Ereignisse von Event Hubs oder IoT Hub und [speist das Power BI-Dashboard mit einem Streamingdataset](/power-bi/service-real-time-streaming). Weitere Informationen finden Sie in dem ausführlichen Tutorial [Analysieren von Telefonanrufdaten mit Stream Analytics und Visualisieren der Ergebnisse in einem Power BI-Dashboard](stream-analytics-manage-job.md).

![ASA – Power BI-Dashboard](media/stream-analytics-solution-patterns/pbidashboard.png)

Diese Lösung kann in nur wenigen Minuten im Azure-Portal erstellt werden. Sie erfordert keine aufwändige Codierung, und die Geschäftslogik wird in der SQL-Sprache geschrieben.

Dieses Echtzeit-Dashboard-Lösungsmuster bietet die geringste Latenz zwischen der Ereignisquelle und dem Power BI-Dashboard in einem Browser. Azure Stream Analytics ist der einzige Azure-Dienst mit dieser integrierten Funktion.

## <a name="use-sql-for-dashboard"></a>Verwenden von SQL für das Dashboard

Das Power BI-Dashboard bietet niedrige Latenz, aber es kann nicht verwendet werden, um voll funktionsfähige Power BI-Berichte zu erstellen. Einem gängigen Muster für die Berichterstellung folgend sollten Sie Ihre Daten zuerst in eine SQL-Datenbank ausgeben. Verwenden Sie dann den SQL-Connector von Power BI für die SQL-Abfrage der aktuellen Daten.

![ASA – SQL-Dashboard](media/stream-analytics-solution-patterns/sqldashboard.png)

Die Verwendung der SQL-Datenbank bietet Ihnen mehr Flexibilität auf Kosten erhöhter Latenz. Diese Lösung ist optimal für Aufträge, bei denen die Latenzanforderungen über einer Sekunde liegen. Mit dieser Methode können Sie das Power BI-Hilfsprogramm maximal ausnutzen, um Daten gezielter für Berichte auszuwerten. Sie erhalten auch die Flexibilität der Verwendung anderer Dashboardlösungen, wie z.B. Tableau.

SQL ist kein Datenspeicher mit hohem Durchsatz, und der maximale Durchsatz zu einer SQL-Datenbank von Azure Stream Analytics beträgt 24 MB/s. Wenn die Ereignisquellen in der Lösung Daten mit höherer Rate produzieren, müssen Sie die Ausgaberate an SQL mit der Verarbeitungslogik in Stream Analytics reduzieren. Techniken wie z.B. Filtern, Aggregat im Fenstermodus, Musterabgleich mit temporalen Verknüpfungen und Analysefunktionen können verwendet werden. Techniken, mit denen die Ausgaberate an SQL weiter optimiert werden kann, werden in [Azure Stream Analytics-Ausgabe an Azure SQL-Datenbank](stream-analytics-sql-output-perf.md) beschrieben.

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Integrieren von Echtzeiteinblicken in Ihre Anwendung mit Ereignisnachrichten

Die zweithäufigste Verwendung von Stream Analytics ist das Generieren von Echtzeitwarnungen. Bei diesem Lösungsmuster kann Geschäftslogik in Stream Analytics zum Erkennen von [temporalen und räumlichen Mustern](stream-analytics-geospatial-functions.md) oder [Anomalien](stream-analytics-machine-learning-anomaly-detection.md) und anschließendem Generieren von Warnsignalen verwendet werden. Hier kann im Gegensatz zur Dashboardlösung, wo Stream Analytics Power BI als bevorzugten Endpunkt verwendet, eine Anzahl von Zwischendatensenken verwendet werden. Zu diesen Senken zählen Event Hubs, Service Bus und Azure Functions. Als Entwickler der Anwendung müssen Sie entscheiden, welche Datensenke am besten für Ihr Szenario geeignet sind.

Downstreamereignis-Verarbeitungslogik muss zum Generieren von Warnungen in Ihren vorhandenen Geschäftsworkflow implementiert werden. Da Sie benutzerdefinierte Logik in Azure Functions implementieren können, ist Azure Functions die schnellste Möglichkeit, diese Integration durchzuführen. Ein Tutorial zur Verwendung von Azure Functions als Ausgabe für einen Stream Analytics-Auftrag finden Sie unter [Ausführen von Azure Functions in Azure Stream Analytics-Aufträgen](stream-analytics-with-azure-functions.md). Azure Functions unterstützt auch verschiedene Arten von Benachrichtigungen einschließlich Text und E-Mail. Logic Apps kann auch für eine solche Integration verwendet werden, mit Event Hubs zwischen Stream Analytics und Logic Apps.

![ASA – Ereignisnachrichten-App](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Event Hubs bietet auf der anderen Seite den flexibelsten Integrationspunkt. Viele andere Dienste wie Azure Data Explorer and Time Series Insight können Ereignisse von Event Hubs verarbeiten. Es können direkt Verbindungen von Diensten mit der Event Hubs-Senke von Azure Stream Analytics hergestellt werden, um die Lösung fertigzustellen. Event Hubs ist auch der Nachrichtenbroker mit dem höchsten Durchsatz, der für solche Integrationsszenarien in Azure verfügbar ist.

## <a name="dynamic-applications-and-websites"></a>Dynamische Anwendungen und Websites

Sie können mithilfe von Azure Stream Analytics und Azure SignalR Service benutzerdefinierte Visualisierungen in Echtzeit erstellen, z.B. Dashboard- oder Kartenvisualisierung. Mit SignalR können Webclients aktualisiert und dynamische Inhalte in Echtzeit angezeigt werden.

![ASA – dynamische App](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Integrieren von Echtzeiteinblicken in Ihre Anwendung mit Datenspeichern

Die meisten Webdienste und Webanwendungen verwenden heute ein Anforderung/Antwort-Muster für die Darstellungsschicht. Das Anforderung/Antwort-Muster ist einfach zu erstellen und kann mithilfe eines zustandslosen Front-Ends und skalierbaren Speichern wie Cosmos DB einfach mit schnellen Antwortzeiten skaliert werden.

Hohe Datenvolumen führen in einem CRUD-basierten System häufig zu Leistungsengpässen. Das [Ereignissourcing-Lösungsmuster](/azure/architecture/patterns/event-sourcing) wird verwendet, um Leistungsengpässe zu beheben. Das Extrahieren von temporalen Mustern und Einblicken aus einem herkömmlichen Datenspeicher ist auch schwierig und ineffizient. Moderne, von einem umfangreichen Datenvolumen gesteuerte Anwendungen übernehmen häufig eine datenflussbasierte Architektur. Azure Stream Analytics ist als Computeengine für bewegte Daten zentrales A und O in dieser Architektur.

![ASA – Ereignissourcing-App](media/stream-analytics-solution-patterns/eventsourcingapp.png)

Bei diesem Lösungsmuster werden Ereignisse von Azure Stream Analytics verarbeitet und in Datenspeichern aggregiert. Die Anwendungsschicht interagiert über das herkömmliche Anforderung/Antwort-Muster mit Datenspeichern. Aufgrund der Fähigkeit von Stream Analytics zum Verarbeiten einer großen Anzahl von Ereignissen in Echtzeit ist die Anwendung hoch skalierbar, ohne dass die Datenspeicherschicht übermäßig groß angelegt werden muss. Die Datenspeicherschicht ist im Wesentlichen eine materialisierte Sicht in das System. In [Azure Stream Analytics-Ausgabe an Azure Cosmos DB](stream-analytics-documentdb-output.md) wird beschrieben, wie Cosmos DB als Stream Analytics-Ausgabe verwendet wird.

In realen Anwendungen, in denen die Verarbeitungslogik komplex ist und die Notwendigkeit besteht, bestimmte Teile der Logik unabhängig zu aktualisieren, können mehrere Stream Analytics-Aufträge mit Event Hubs als Zwischenereignisbroker zusammengestellt werden.

![ASA – komplexe Ereignissourcing-App](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Dieses Muster verbessert Resilienz und Verwaltbarkeit des Systems. Obwohl Stream Analytics exakt eine Verarbeitung gewährleistet, besteht eine geringe Wahrscheinlichkeit, dass Ereignisse doppelt im zwischengeschalteten Event Hubs eingehen. Für den nachgelagerten Stream Analytics-Auftrag ist wichtig, Ereignisse mit Logikschlüsseln in einem Rückblickfenster zu deduplizieren. Weitere Informationen zur Ereignisübermittlung finden Sie in der Referenz zu [Garantien zur Ereignisbereitstellung](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

## <a name="use-reference-data-for-application-customization"></a>Verwenden von Verweisdaten für die Anwendungsanpassung

Das Verweisdatenfeature von Azure Stream Analytics wurde speziell für die Anpassung von z.B. Warnungsschwellenwert, Verarbeitungsregeln und [Geofences](geospatial-scenarios.md) durch Endbenutzer entworfen. Die Anwendungsschicht kann Parameteränderungen annehmen und in einer SQL-Datenbank speichern. Der Stream Analytics-Auftrag fragt in regelmäßigen Abständen Änderungen von der Datenbank ab und ermöglicht den Zugriff auf die Anpassungsparameter über eine Verweisdatenverknüpfung. Weitere Informationen zum Verwenden von Verweisdaten für die Anwendungsanpassung finden Sie unter [Verwenden von Verweisdaten aus einer SQL-Datenbank für einen Azure Stream Analytics-Auftrag (Vorschauversion)](sql-reference-data.md) und [JOIN-Anweisung für Verweisdaten](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Dieses Muster kann auch verwendet werden, um eine Regelengine dort zu implementieren, wo die Schwellenwerte der Regeln aus den Verweisdaten definiert werden. Weitere Informationen zu Regeln finden Sie unter [Verarbeiten von konfigurierbaren schwellenwertbasierten Regeln in Azure Stream Analytics](stream-analytics-threshold-based-rules.md).

![ASA – Verweisdaten-App](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Hinzufügen von Machine Learning zu Ihren Einblicken in Echtzeit

Das integrierte [Modell zur Anomalieerkennung](stream-analytics-machine-learning-anomaly-detection.md) von Azure Stream Analytics ist eine komfortable Möglichkeit, Machine Learning in Ihre Echtzeitanwendung einzuführen. Informationen zu einem umfassenderen Bereich der Machine Learning-Anforderungen finden Sie unter [Durchführen von Standpunktanalysen mit Azure Stream Analytics und Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

Erfahrene Benutzer, die Onlinetraining und -bewertung in dieselbe Stream Analytics-Pipeline integrieren möchten, sollten sich dieses Beispiel ansehen, wo dies mit [linearer Regression](stream-analytics-high-frequency-trading.md) durchgeführt wird.

![ASA – Machine Learning-App](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Data Warehousing nahezu in Echtzeit

Ein weiteres häufiges Muster ist das Data Warehousing in Echtzeit, auch als Streaming-Data Warehouse bezeichnet. Zusätzlich zu Ereignissen, die von Ihrer Anwendung bei Event Hubs und IoT Hub eintreffen, kann [Azure Stream Analytics auf IoT Edge](stream-analytics-edge.md) verwendet werden, um Anforderungen an Datenbereinigung, Reduzierung der Datenmenge sowie Datenspeicher und Weiterleitung zu erfüllen. Auf IoT Edge ausgeführt kann Stream Analytics Bandbreiteneinschränkungen und Verbindungsprobleme im System ordnungsgemäß behandeln. Der SQL-Ausgabeadapter kann für die Ausgabe an SQL Data Warehouse verwendet werden; allerdings ist der maximale Durchsatz auf 10MB/s beschränkt.

![ASA – Data Warehousing](media/stream-analytics-solution-patterns/datawarehousing.png)

Eine Möglichkeit, den Durchsatz mit einem Latenzkompromiss zu verbessern, ist, die Ereignisse in Azure Blob Storage zu archivieren und sie dann [mit Polybase in SQL Data Warehouse zu importieren](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md). Sie müssen die Ausgabe von Stream Analytics in Blob Storage und die Eingabe aus Blob Storage in SQL Data Warehouse durch [Archivieren der Daten nach Zeitstempel](stream-analytics-custom-path-patterns-blob-storage-output.md) manuell zusammenfügen und in regelmäßigen Abständen importieren.

Bei diesem Nutzungsmuster wird Azure Stream Analytics als eine nahezu in Echtzeit wirkende ETL-Engine verwendet. Neu ankommende Ereignisse werden kontinuierlich transformiert und für die Nutzung durch den nachgelagerten Analysedienst gespeichert.

![ASA – Data Warehousing mit hohem Durchsatz](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Archivieren von Echtzeitdaten für die Analyse

Die meisten Data Science- und Analyseaktivitäten werden immer noch offline durchgeführt. Daten können über Azure Data Lake Store Gen2-Ausgabe und Parquet-Ausgabeformate von Azure Stream Analytics archiviert werden. Diese Funktion vermeidet die Probleme der direkten Datenübertragung in Azure Data Lake Analytics, Azure Databricks und Azure HDInsight. Azure Stream Analytics wird in dieser Lösung als eine nahezu in Echtzeit wirkende ETL-Engine verwendet. Sie können in Data Lake archivierte Daten mit verschiedenen Computeengines untersuchen.

![ASA – Offlineanalyse](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Verwenden von Verweisdaten zur Anreicherung

Datenanreicherung ist oft eine Anforderung für ETL-Engines. Azure Stream Analytics unterstützt die Datenanreicherung mit [Verweisdaten](stream-analytics-use-reference-data.md) aus SQL-Datenbank und Azure Blob Storage. Die Datenanreicherung ist sowohl für in Azure Data Lake als auch SQL Data Warehouse landende Daten möglich.

![ASA – Offlineanalyse mit Datenanreicherung](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Operationalisieren von Einblicken aus archivierten Daten

Wenn Sie das Offlineanalysemuster mit dem Nahezu-in-Echtzeit-Anwendungsmuster kombinieren, können Sie eine Feedbackschleife erstellen. Die Feedbackschleife ermöglicht der Anwendung die automatische Anpassung an sich ändernde Muster in den Daten. Diese Rückmeldungsschleife kann so einfach sein wie das Ändern des Schwellenwerts für die Warnung oder so komplex wie das erneute Trainieren von Machine Learning-Modellen. Dieselbe Lösungsarchitektur kann gleichermaßen auf in der Cloud und auf IoT Edge ausgeführte ASA-Aufträge angewendet werden.

![ASA – Operationalisierung von Einblicken](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Überwachung von ASA-Aufträgen

Ein Azure Stream Analytics-Auftrag kann rund um die Uhr zur kontinuierlichen Verarbeitung eingehender Ereignisse in Echtzeit ausgeführt werden. Die Betriebszeitgarantie ist für die Integrität der gesamten Anwendung entscheidend. Obwohl Stream Analytics als einziger Streaminganalysedienst in der Branche eine [Verfügbarkeitsgarantie von 99,9%](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) bietet, bleibt Ihnen vielleicht dennoch ein gewisses Maß an Ausfallzeiten nicht erspart. Im Laufe der Jahre wurden bei Stream Analytics Metriken, Protokolle und Auftragsstatus eingeführt, die die Integrität der Aufträge widerspiegeln. Sie werden alle in der Benutzeroberfläche des Diensts Azure Monitor angezeigt und können nach der OMS exportiert werden. Weitere Informationen finden Sie unter [Grundlegendes zur Stream Analytics-Auftragsüberwachung und zum Überwachen von Abfragen](stream-analytics-monitoring.md).

![ASA – Überwachung](media/stream-analytics-solution-patterns/monitoring.png)

Zwei Hauptkriterien sind zu überwachen:

- [Status „Fehler“ des Auftrags](job-states.md)

    Zuallererst müssen Sie sicherstellen, dass der Auftrag ausgeführt wird. Wenn der Auftrag sich nicht im Ausführungsstatus befindet, werden keine neuen Metriken oder Protokolle generiert. Aufträge können sich aus unterschiedlichen Gründen in einem fehlerhaften Zustand befinden, einschließlich einer hohen SU-Auslastung (d.h. Mangel an Ressourcen).

- [Wasserzeichenverzögerungs-Metriken](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Diese Metrik reflektiert, wie weit Ihre Verarbeitungspipeline in Gesamtbetrachtungszeit (Sekunden) zurückliegt. Die Verzögerung ist zum Teil mit ihrer Verarbeitungslogik verbunden. Daher ist die Überwachung der zunehmenden Trends viel wichtiger als die Überwachung des absoluten Werts. Der Verzögerung im stabilen Zustand sollten Sie mit dem Anwendungsentwurf begegnen, nicht mit Überwachung oder Warnungen.

Bei einem Ausfall sollten Sie zuerst in Aktivitätsprotokollen und [Diagnoseprotokollen](stream-analytics-job-diagnostic-logs.md) nach Fehlern suchen.

## <a name="build-resilient-and-mission-critical-applications"></a>Erstellen robuster und unternehmenskritischer Anwendungen

Unabhängig von der SLA-Garantie von Azure Stream Analytics und der Sorgfalt, mit der Sie die End-to-End-Anwendung ausführen, können Ausfälle auftreten. Wenn Ihre Anwendung unternehmenskritisch ist, müssen Sie auf Ausfälle vorbereitet sein, um ordnungsgemäß wiederherstellen zu können.

Bei Warnungsanwendungen ist es am wichtigsten, die nächste Warnung zu erkennen. Sie können wählen, den Auftrag bei der Wiederherstellung vom aktuellen Zeitpunkt aus neu zu starten und vergangene Warnungen zu ignorieren. Die Auftragsstartzeit-Semantik liegt beim ersten Ausgabezeitpunkt, nicht dem ersten Eingabezeitpunkt. Die Eingabe wird um einen entsprechenden Zeitraum zurückgespult, um sicherzustellen, dass die erste Ausgabe zum angegebenen Zeitpunkt vollständig und korrekt ist. Sie erhalten keine teilweisen Aggregate und lösen als Ergebnis unerwartet Warnungen aus.

Sie können auch wählen, die Ausgabe zu einem bestimmten Zeitpunkt in der Vergangenheit zu starten. Dank der Aufbewahrungsrichtlinien für Event Hubs und IoT Hub steht eine ausreichende Datenmenge zur Verarbeitung von einem Punkt in der Vergangenheit aus zur Verfügung. Die Frage ist nur, wie schnell Sie zum aktuellen Zeitpunkt aufholen und ob Sie rechtzeitig beginnen können, neue Warnungen zu generieren. Daten verlieren im Laufe der Zeit schnell an Wert, daher ist es wichtig, schnell zum aktuellen Zeitpunkt aufzuholen. Es gibt zwei Möglichkeiten, schnell aufzuholen:

- Stellen Sie während des Aufholens weitere Ressourcen (SU) bereit.
- Starten Sie ab dem aktuellen Zeitpunkt neu.

Ab dem aktuellen Zeitpunkt neu zu starten ist einfach, allerdings mit dem Nachteil verbunden, dass eine Lücke während der Verarbeitung hinterlassen wird. Ein derartiger Neustart mag für Warnungsszenarien in Ordnung sein, kann aber für Dashboardszenarien problematisch sein und kommt für Archivierungs- und Data Warehousing-Szenarien nicht infrage.

Die Bereitstellung weiterer Ressourcen kann den Prozess beschleunigen, aber die Auswirkungen eines plötzlichen Anstiegs der Verarbeitungsrate sind komplex.

- Prüfen Sie, ob Ihr Auftrag für eine größere Anzahl von SUs skalierbar ist. Nicht alle Abfragen sind skalierbar. Sie müssen sicherstellen, dass Ihre Abfrage [parallelisiert](stream-analytics-parallelization.md) ist.

- Stellen Sie sicher, dass genügend Partitionen in den vorgelagerten Event Hubs- oder IoT Hub-Instanzen vorhanden sind, sodass Sie weitere Durchsatzeinheiten (Throughput Units, TUs) zum Skalieren des Eingabedurchsatzes hinzufügen können. Beachten Sie, dass jede Event Hubs-TU eine maximale Ausgaberate von 2MB/s hat.

- Stellen Sie sicher, dass Sie genügend Ressourcen in den Ausgabesenken (d.h. SQL-Datenbank, Cosmos DB) bereitgestellt haben, damit sie den plötzlichen Anstieg der Ausgabe nicht drosseln, was manchmal zum Sperren des Systems führen kann.

Das Wichtigste ist, die Änderung der Verarbeitungsrate vorwegzunehmen, diese Szenarien vor dem Start der Produktion zu testen und bereit zu sein, die Verarbeitung während der Wiederherstellungszeit ordnungsgemäß zu skalieren.

Im extremen Szenario, dass alle eingehenden Ereignisse verzögert sind, [ist es möglich, dass alle verzögerten Ereignisse gelöscht werden](stream-analytics-time-handling.md), wenn Sie ein spätes Ankunftsfenster für Ihren Auftrag angewendet haben. Das Löschen der Ereignisse mag zunächst mysteriös erscheinen, doch Stream Analytics erwartet als Echtzeitverarbeitungs-Engine, dass eingehende Ereignisse nahe an der Gesamtbetrachtungszeit liegen. Sie muss Ereignisse löschen, die diese Einschränkungen verletzen.

### <a name="backfilling-process"></a>Abgleichprozess

Glücklicherweise kann das vorherige Datenarchivierungsmuster verwendet werden, um diese verspäteten Ereignisse ordnungsgemäß zu verarbeiten. Dahinter steht der Gedanke, dass der Archivierungsauftrag eingehende Ereignisse zur Eingangszeit verarbeitet und Ereignisse in Azure Blob Storage oder Azure Data Lake Store mit ihrer Ereigniszeit im richtigen Zeitbucket archiviert. Ganz gleich, wie spät ein Ereignis eingeht, es wird nie gelöscht. Es gelangt immer in den richtigen Zeitbucket. Während der Wiederherstellung können die archivierten Ereignisse erneut verarbeitet und die Ergebnisse mit dem Speicher Ihrer Wahl abgeglichen werden.

![ASA – Abgleich](media/stream-analytics-solution-patterns/backfill.png)

Der Abgleich muss mit einem Offline-Batchverarbeitungssystem erfolgen, dessen Programmiermodell sich wahrscheinlich von dem von Azure Stream Analytics unterscheidet. Dies bedeutet, dass Sie die gesamte Verarbeitungslogik erneut implementieren müssen.

Für den Abgleich ist es immer noch wichtig, zumindest vorübergehend mehr Ressourcen für die Ausgabesenken bereitzustellen, um einen höheren Durchsatz zu verarbeiten, als die Verarbeitung im stabilen Zustand erfordert.

|Szenarien  |Nur ab jetzt neu starten  |Vom letzten Stoppzeitpunkt aus neu starten |Ab jetzt neu starten + Abgleich mit archivierten Ereignissen|
|---------|---------|---------|---------|
|**Dashboarding**   |Erstellt Lücke    |OK für kurzen Ausfall    |Verwendung für langen Ausfall |
|**Warnungen**   |Akzeptabel |OK für kurzen Ausfall    |Nicht erforderlich |
|**Ereignissourcing-App** |Akzeptabel |OK für kurzen Ausfall    |Verwendung für langen Ausfall |
|**Data Warehousing**   |Datenverlust  |Akzeptabel |Nicht erforderlich |
|**Offlineanalyse**  |Datenverlust  |Akzeptabel |Nicht erforderlich|

## <a name="putting-it-all-together"></a>Zusammenfügen des Gesamtbilds

Es ist nicht schwierig, sich vorzustellen, dass alle oben genannten Lösungsmuster in einem komplexen End-to-End-System kombiniert werden können. Das kombinierte System kann Dashboards, Warnungen, Ereignissourcinganwendungen, Data Warehousing und Offlineanalysefunktionen enthalten.

Entscheidend ist, dass Sie Ihr System in kombinierbaren Mustern entwerfen, damit jedes Subsystem unabhängig erstellt, getestet, aktualisiert und wiederhergestellt werden kann.

## <a name="next-steps"></a>Nächste Schritte

Sie haben jetzt eine Vielzahl von Lösungsmustern mit Azure Stream Analytics kennengelernt. Nun können Sie tiefer einsteigen und Ihren ersten Stream Analytics-Auftrag erstellen:

* [Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md)
* [Erstellen eines Stream Analytics-Auftrags mithilfe von Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Erstellen eines Stream Analytics-Auftrags mithilfe von Visual Studio](stream-analytics-quick-create-vs.md)
