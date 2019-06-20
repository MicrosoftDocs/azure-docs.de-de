---
title: Überwachen der Ressourcennutzung und Abfragemetriken für einen Suchdienst – Azure Search
description: Aktivieren der Protokollierung, Abrufen von Abfrageaktivitätsmetriken, der Ressourcennutzung und anderer Daten von einem Azure Search-Dienst.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: bac897178c8220abe72a92a5cf14fc4767cdd3bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755062"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-search"></a>Überwachen des Ressourcenverbrauchs und der Abfrageaktivität in Azure Search

Auf der Seite „Übersicht“ Ihres Azure Search-Diensts können Sie Systemdaten zur Ressourcennutzung, Abfragemetriken und das noch verfügbare Kontingent zum Erstellen von weiteren Indizes, Indexern und Datenquellen anzeigen. Sie können auch das Portal verwenden, um die Protokollanalyse oder eine andere für die persistente Datensammlung verwendete Ressource zu konfigurieren. 

Das Einrichten von Protokollen ist hilfreich für die Selbstdiagnose und die Beibehaltung des Verwendungsverlaufs. Intern werden Protokolle für einen kurzen Zeitraum im Back-End gespeichert, der für den Fall, dass Sie ein Supportticket erstellen, für die Untersuchung und Analyse ausreicht. Wenn Sie Protokollinformationen steuern und darauf zugreifen möchten, sollten Sie eine der in diesem Artikel beschriebenen Lösungen einrichten.

In diesem Artikel erhalten Sie Informationen zu den Überwachungsoptionen sowie zum Aktivieren der Protokollierung und des Protokollspeichers und zum Anzeigen von Protokollinhalten.

## <a name="metrics-at-a-glance"></a>Metriken auf einen Blick

In den auf der Seite „Übersicht“ integrierten Abschnitten **Nutzung** und **Überwachung** werden Metriken zum Ressourcenverbrauch und zur Abfrageausführung angezeigt. Diese Informationen stehen zur Verfügung, sobald Sie den Dienst verwenden, ohne dass eine Konfiguration erforderlich ist. Diese Seite wird alle paar Minuten aktualisiert. Wenn Sie Entscheidungen dazu treffen möchten, [welcher Tarif für Produktionsworkloads verwendet werden soll](search-sku-tier.md) oder ob [die Anzahl der aktiven Replikate und Partitionen angepasst wird](search-capacity-planning.md), können Sie diese Metriken heranziehen. Anhand der Metriken können Sie sehen, wie schnell Ressourcen verbraucht werden und wie die vorhandene Last in der aktuellen Konfiguration verarbeitet wird.

Auf der Registerkarte **Nutzung** wird die Ressourcenverfügbarkeit relativ zu den aktuellen [Grenzwerten](search-limits-quotas-capacity.md) angezeigt. Die folgende Abbildung bezieht sich auf den kostenlosen Dienst, der auf 3 Objekte pro Typ und auf 50 MB Speicher begrenzt ist. Für einen Dienst mit dem Tarif „Basic“ oder „Standard“ gelten höhere Grenzwerte. Wenn Sie die Anzahl der Partitionen erhöhen, steigt der maximale Speicher proportional an.

![Verwendungsstatus relativ zu tatsächlichen Grenzwerten](./media/search-monitor-usage/usage-tab.png
 "Verwendungsstatus relativ zu tatsächlichen Grenzwerten")

## <a name="queries-per-second-qps-and-other-metrics"></a>Abfragen pro Sekunde (QPS) und weitere Metriken

Auf der Registerkarte **Überwachung** wird ein gleitender Durchschnitt für Metriken wie *Abfragen pro Sekunde* (QPS) auf Minutenbasis aggregiert. 
*Wartezeit bei Suchvorgängen* ist die Zeit, die der Suchdienst aggregiert auf Minutenbasis für die Verarbeitung von Suchabfragen benötigt hat. *Prozentsatz gedrosselter Suchabfragen* (nicht angezeigt) ist der Prozentsatz der gedrosselten Suchabfragen und wird auch auf Minutenbasis aggregiert.

Diese Zahlen sind ungefähre Werte und sollen Ihnen eine allgemeine Übersicht darüber geben, wie gut Anforderungen in Ihrem System verarbeitet werden. Der tatsächliche Wert für die Abfragen pro Sekunde kann höher oder niedriger als im Portal aufgezeichnet sein.

![Aktivität zu Abfragen pro Sekunde](./media/search-monitor-usage/monitoring-tab.png "Aktivität zu Abfragen pro Sekunde")

## <a name="activity-logs"></a>Aktivitätsprotokolle

Im **Aktivitätsprotokoll** werden Informationen von Azure Resource Manager erfasst. Beispiele für die im Aktivitätsprotokoll aufgeführten Informationen sind u.a. Erstellen oder Löschen eines Diensts, Aktualisieren einer Ressourcengruppe, Überprüfen auf Namensverfügbarkeit oder Abrufen eines Dienstzugriffsschlüssels zur Verarbeitung einer Anforderung. 

Auf das **Aktivitätsprotokoll** können Sie im linken Navigationsbereich, über „Benachrichtigungen“ auf der Befehlsleiste oben im Fenster oder über die Seite **Probleme diagnostizieren und beheben** zugreifen.

Für im Dienst enthaltene Aufgaben, z.B. Erstellen eines Index oder Löschen einer Datenquelle, werden für jede Anforderung generische Benachrichtigungen wie „Get Admin Key“ (Administratorschlüssel abrufen), jedoch nicht die jeweilige Aktion angezeigt. Für diese Informationsebene müssen Sie eine Add-On-Überwachungslösung aktivieren.

## <a name="add-on-monitoring-solutions"></a>Add-On-Überwachungslösungen

In Azure Search werden neben den verwalteten Objekten keine weiteren Daten gespeichert, d.h, Protokolldaten müssen extern gespeichert werden. Wenn Sie Protokolldaten speichern möchten, können Sie eine der unten aufgeführten Ressourcen konfigurieren. 

In der folgenden Tabelle werden die Optionen zum Speichern von Protokollen und Hinzufügen einer umfassenden Überwachung von Dienstvorgängen und Abfrageworkloads über Application Insights verglichen.

| Resource | Verwendung |
|----------|----------|
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | Protokollierte Ereignisse und Abfragemetriken, basierend auf den weiter unten beschriebenen Schemas und korreliert mit Benutzerereignissen in Ihrer App. Dies ist die einzige Lösung, bei der Benutzeraktionen oder Signale berücksichtigt werden, d.h., Ereignisse aus einer vom Benutzer initiierten Suche werden zugeordnet, anstatt dass die durch Anwendungscode übermittelten Anforderungen gefiltert werden. Um diese Lösung zu verwenden, kopieren Sie Instrumentationscode, und fügen Sie ihn in Ihren Quelldateien ein, um Anforderungsinformationen an Application Insights weiterzuleiten. Weitere Informationen finden Sie unter [Datenverkehrsanalyse durchsuchen](search-traffic-analytics.md). |
| [Azure Monitor-Protokolle](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | Protokollierte Ereignisse und Abfragemetriken, basierend auf den weiter unten beschriebenen Schemas. Ereignisse werden in einem Log Analytics-Arbeitsbereich protokolliert. Sie können Abfragen für einen Arbeitsbereich ausführen, um detaillierte Informationen aus dem Protokoll zurückzugeben. Weitere Informationen finden Sie unter [Anzeigen oder Analysieren der mit der Log Analytics-Protokollsuche gesammelten Daten](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata). |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Protokollierte Ereignisse und Abfragemetriken, basierend auf den weiter unten beschriebenen Schemas. Ereignisse werden in einem Blobcontainer protokolliert und in JSON-Dateien gespeichert. Verwenden Sie einen JSON-Editor, um die Dateiinhalte anzuzeigen.|
| [Event Hub](https://docs.microsoft.com/azure/event-hubs/) | Protokollierte Ereignisse und Abfragemetriken, basierend auf den in diesem Artikel beschriebenen Schemas. Wählen Sie diese Lösung als alternativen Datensammlungsdienst für sehr große Ereignisprotokolle aus. |

Azure Monitor-Protokolle und Blob Storage sind beide als kostenloser gemeinsamer Dienst verfügbar, sodass Sie sie während der Gültigkeitsdauer Ihres Azure-Abonnements kostenlos testen können. Application Insights kann kostenlos registriert und verwendet werden, sofern die Größe der Anwendungsdaten bestimmte Grenzwerte nicht überschreitet. (Details finden Sie unter [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/monitor/).)

Im nächsten Abschnitt werden die Schritte zum Aktivieren und Verwenden von Azure Blob Storage zum Erfassen von und Zugreifen auf Protokolldaten erläutert, die durch Azure Search-Vorgänge erstellt werden.

## <a name="enable-logging"></a>Aktivieren der Protokollierung

Die Protokollierung für die Indizierung und für Abfrageworkloads ist standardmäßig deaktiviert. Für die Protokollierungsstruktur und die langfristige externe Speicherung müssen Add-On-Lösungen eingesetzt werden. In Azure Search werden nur die in Azure Search erstellten und verwalteten Objekte gespeichert, sodass Protokolle an anderer Stelle gespeichert werden müssen.

In diesem Abschnitt erfahren Sie, wie Sie protokollierte Ereignisse und Metrikdaten mithilfe von Blob Storage speichern.

1. [Erstellen Sie ein Speicherkonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account), wenn Sie noch keines besitzen. Sie können es in der gleichen Ressourcengruppe wie Azure Search ablegen, um später die Bereinigung zu vereinfachen, wenn Sie alle in dieser Übung verwendeten Ressourcen löschen.

   Ihr Speicherkonto muss sich in der gleichen Region wie Azure Search befinden.

2. Öffnen Sie die Seite „Übersicht“ für Ihren Suchdienst. Scrollen Sie im linken Navigationsbereich nach unten zu **Überwachung**, und klicken Sie auf **Überwachung aktivieren**.

   ![Überwachung aktivieren](./media/search-monitor-usage/enable-monitoring.png "Überwachung aktivieren")

3. Wählen Sie die Daten aus, die Sie exportieren möchten: Protokolle, Metriken oder beides. Sie können sie in ein Speicherkonto kopieren, an einen Event Hub senden oder nach Azure Monitor-Protokollen exportieren.

   Für die Archivierung in Blob Storage muss nur das Speicherkonto vorhanden sein. Container und Blobs werden beim Exportieren von Protokolldaten nach Bedarf erstellt.

   ![Konfigurieren des Blob Storage-Archivs](./media/search-monitor-usage/configure-blob-storage-archive.png "Konfigurieren des Blob Storage-Archivs")

4. Speichern Sie das Profil.

5. Testen Sie die Protokollierung, indem Sie Objekte erstellen oder löschen (Protokollereignisse werden erstellt) und Abfragen übermitteln (Metriken werden generiert). 

Die Protokollierung ist aktiviert, nachdem Sie das Profil gespeichert haben. Container werden nur erstellt, wenn eine Aktivität zum Protokollieren oder Messen vorhanden ist. Beim Kopieren der Daten in ein Speicherkonto werden sie im JSON-Format formatiert und in zwei Containern platziert:

* „insights-logs-operationlogs“: für Suchdatenverkehrsprotokolle
* „insights-metrics-pt1m“: für Metriken

**Die Container werden nach einer Stunde in Blob Storage angezeigt. Es gibt ein Blob pro Stunde pro Container.**

Die Container können Sie mithilfe von [Visual Studio Code](#download-and-open-in-visual-studio-code) oder eines anderen JSON-Editors anzeigen. 

### <a name="example-path"></a>Beispielpfad

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Protokollschema
Blobs, die die Datenverkehrsprotokolle des Suchdiensts enthalten, sind entsprechend der Beschreibung in diesem Abschnitt strukturiert. Jedes Blob hat ein Stammobjekt mit dem Namen **records**, das ein Array von Protokollobjekten enthält. Jedes Blob enthält Einträge zu allen Vorgängen, die während einer bestimmten Stunde erfolgt sind.

| NAME | Type | Beispiel | Notizen |
| --- | --- | --- | --- |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |Zeitstempel des Vorgangs |
| resourceId |Zeichenfolge |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Ihre Ressourcen-ID |
| operationName |Zeichenfolge |„Query.Search“ |Der Name des Vorgangs |
| operationVersion |Zeichenfolge |"2019-05-06" |Die verwendete API-Version |
| category |Zeichenfolge |„OperationLogs“ |Konstante |
| resultType |Zeichenfolge |„Success“ |Mögliche Werte: Erfolgreich oder Fehler |
| resultSignature |int |200 |HTTP-Ergebniscode |
| durationMS |int |50 |Dauer des Vorgangs in Millisekunden |
| properties |object |Siehe hierzu die folgende Tabelle. |Objekt, das vorgangsspezifische Daten enthält |

**Eigenschaftsschema**

| NAME | Type | Beispiel | Notizen |
| --- | --- | --- | --- |
| BESCHREIBUNG |Zeichenfolge |„GET-/indexes('content')/docs“ |Endpunkt des Vorgangs |
| Abfrage |Zeichenfolge |"?search=AzureSearch&$count=true&api-version=2019-05-06" |Die Abfrageparameter |
| Dokumente |int |42 |Anzahl von verarbeiteten Dokumenten |
| IndexName |Zeichenfolge |„testindex“ |Name des Indexes, der dem Vorgang zugeordnet ist |

## <a name="metrics-schema"></a>Metrikenschema

Metriken werden für Abfrageanforderungen erfasst.

| NAME | Type | Beispiel | Notizen |
| --- | --- | --- | --- |
| resourceId |Zeichenfolge |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Ihre Ressourcen-ID |
| metricName |Zeichenfolge |„Latency“ |Der Name der Metrik |
| in |datetime |"2018-12-07T00:00:43.6872559Z" |Der Zeitstempel des Vorgangs |
| average |int |64 |Der Durchschnittswert der unformatierten Beispiele im Metrikzeitintervall |
| minimum |int |37 |Der Mindestwert der unformatierten Beispiele im Metrikzeitintervall |
| maximum |int |78 |Der Höchstwert der unformatierten Beispiele im Metrikzeitintervall |
| total |int |258 |Der Gesamtwert der unformatierten Beispiele im Metrikzeitintervall |
| count |int |4 |Die Anzahl der unformatierten Beispiele, die zum Generieren der Metrik verwendet werden |
| timegrain |Zeichenfolge |„PT1M“ |Das Aggregationsintervall der Metrik in ISO 8601 |

Alle Metriken werden in Intervallen von einer Minute gemeldet. Jede Metrik macht Mindest-, Höchst- und Durchschnittswerte pro Minute verfügbar.

Bei der Metrik „SearchQueriesPerSecond“ ist der Mindestwert der niedrigste Wert für Suchabfragen pro Sekunde, der während dieser Minute registriert wurde. Dasselbe gilt für den Höchstwert. Der Durchschnittswert ist das Aggregat der gesamten Minute.
Beispiel: Innerhalb einer Minute kann es für eine Sekunde eine sehr hohe Last geben (dies ist der Höchstwert für „SearchQueriesPerSecond“), gefolgt von 58 Sekunden mit mittlerer Last sowie einer Sekunde mit nur einer Abfrage, was der Mindestwert ist.

Für „ThrottledSearchQueriesPercentage“ entsprechen der Mindest-, Höchst-, Durchschnitts- und Gesamtwert demselben Wert, nämlich dem Prozentsatz von Suchabfragen, die gedrosselt wurden, basierend auf der Gesamtanzahl von Suchabfragen während einer Minute.

## <a name="download-and-open-in-visual-studio-code"></a>Herunterladen und Öffnen in Visual Studio Code

Sie können die Protokolldatei mit einem beliebigen JSON-Editor anzeigen. Wenn Sie über keinen verfügen, empfiehlt sich die Verwendung von [Visual Studio Code](https://code.visualstudio.com/download).

1. Öffnen Sie Ihr Speicherkonto im Azure-Portal. 

2. Klicken Sie im linken Navigationsbereich auf **Blobs**. **insights-logs-operationlogs** und **insights-metrics-pt1m** sollten angezeigt werden. Diese Container werden in Azure Search erstellt, wenn die Protokolldaten in Blob Storage exportiert werden.

3. Klicken Sie in der Ordnerhierarchie nach unten bis zur JSON-Datei.  Verwenden Sie das Kontextmenü, um die Datei herunterzuladen.

Nach dem Herunterladen der Datei können Sie sie in einem JSON-Editor öffnen und die Inhalte anzeigen.

## <a name="use-system-apis"></a>Verwenden von System-APIs
Sowohl die REST-API von Azure Search als auch das .NET SDK bieten programmgesteuerten Zugriff auf Dienstmetriken, Index- und Indexerinformationen und die Anzahl von Dokumenten.

* [Dienststatistiken abrufen](/rest/api/searchservice/get-service-statistics)
* [Indexstatistiken abrufen](/rest/api/searchservice/get-index-statistics)
* [Dokumentenanzahl](/rest/api/searchservice/count-documents)
* [Abrufen des Indexerstatus](/rest/api/searchservice/get-indexer-status)

Informationen zur Aktivierung mit PowerShell oder Azure CLI finden Sie in [dieser](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) Dokumentation.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Dienstverwaltung finden Sie unter [Verwalten Ihres Suchdiensts in Microsoft Azure](search-manage.md) und eine Optimierungsanleitung unter [Leistung und Optimierung](search-performance-optimization.md).