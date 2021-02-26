---
title: Verwalten von Nutzung und Kosten für Azure Application Insights | Microsoft-Dokumentation
description: Verwalten Sie Telemetriedatenvolumen, und überwachen Sie Kosten in Application Insights.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: DaleKoetke
ms.author: dalek
ms.date: 5/7/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 477a96f1bf66255b11b2fee36c38e55b18cddb69
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99556124"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Verwalten der Nutzung und der Kosten für Application Insights

> [!NOTE]
> In diesem Artikel wird beschrieben, wie Sie die Kosten für Application Insights ermitteln und steuern.  In einem verwandten Artikel, [Überwachen der Nutzung und der geschätzten Kosten](../platform/usage-estimated-costs.md), wird erläutert, wie die Nutzung und geschätzten Kosten über mehrere Azure-Überwachungsfeatures hinweg für unterschiedliche Preismodelle angezeigt werden.

Application Insights ist so konzipiert, dass es alles bietet, was Sie für die Überwachung der Verfügbarkeit, Leistung und Nutzung Ihrer Webanwendungen benötigen – ganz gleich, ob diese in Azure oder lokal gehostet werden. Zudem unterstützt Application Insights beliebte Sprachen und Frameworks wie .NET, Java und Node.js und kann in DevOps-Prozesse und -Tools wie Azure DevOps, Jira und PagerDuty integriert werden. Es ist wichtig zu verstehen, wodurch sich die Kosten für die Überwachung Ihrer Anwendungen ergeben. In diesem Artikel erfahren Sie, wie die Kosten für die Anwendungsüberwachung entstehen und wie Sie diese proaktiv überwachen und steuern können.

Wenn Sie Fragen zu den Preisen für Application Insights haben, können Sie diese auf der [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-monitor.html) posten.

## <a name="pricing-model"></a>Preismodell

Die Preise für [Azure Application Insights][start] richten sich nach einem Modell mit **nutzungsbasierter Bezahlung** basierend auf dem verbrauchten Datenvolumen und mit optional längerer Datenaufbewahrung. Jede Application Insights-Ressource wird als separater Dienst abgerechnet und auf der Rechnung für Ihr Azure-Abonnement aufgeführt. Das Datenvolumen wird anhand der Größe des nicht komprimierten JSON-Datenpakets gemessen, das Application Insights von Ihrer Anwendung empfängt. Für die Verwendung von [Live Metrics Stream](./live-stream.md) fällt keine Gebühr für das Datenvolumen an.

Für [mehrstufige Webtests](./availability-multistep.md) wird eine zusätzliche Gebühr erhoben. Mehrstufige Webtests sind Webtests, die eine Sequenz von Aktionen ausführen. Es gibt keine gesonderte Gebühr für *Pingtests* einer einzelnen Seite. Telemetriedaten aus Pingtests und mehrstufigen Tests werden ebenso wie andere Telemetriedaten aus Ihrer App in Rechnung gestellt.

Die Application Insights-Option zum [Aktivieren von Warnungen für benutzerdefinierte Metrikdimensionen](./pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) kann ebenfalls zusätzliche Kosten erzeugen, da dies zur Erstellung zusätzlicher Metriken vor der Aggregation führen kann. [Erfahren Sie mehr](./pre-aggregated-metrics-log-metrics.md) über protokollbasierte und vorab aggregierte Metriken in Application Insights sowie über die [Preise](https://azure.microsoft.com/pricing/details/monitor/) für benutzerdefinierte Metriken in Azure Monitor.

### <a name="workspace-based-application-insights"></a>Arbeitsbereichsbasierte Application Insights-Instanz

Für Application Insights-Ressourcen, die ihre Daten an einen Log Analytics-Arbeitsbereich senden (als [arbeitsbereichsbasierte Application Insights-Ressourcen](create-workspace-resource.md) bezeichnet), erfolgt die Abrechnung für die Datenerfassung und -aufbewahrung über den Arbeitsbereich, in dem sich die Application Insights-Daten befinden. Auf diese Weise können Kunden alle Optionen des[Preismodells](../platform/manage-cost-storage.md#pricing-model) von Log Analytics nutzen, z. B. Kapazitätsreservierung und nutzungsbasierte Zahlung. Log Analytics verfügt auch noch über weitere Optionen für die Datenaufbewahrung, z. B. [Aufbewahrung nach Datentyp](../platform/manage-cost-storage.md#retention-by-data-type). Für Application Insights-Datentypen im Arbeitsbereich gilt eine gebührenfreie Aufbewahrungsdauer von 90 Tagen. Die Nutzung von Webtests und die Aktivierung von Warnungen für benutzerdefinierte Metrikdimensionen wird weiterhin über Application Insights gemeldet. Machen Sie sich damit vertraut, wie Sie die Kosten für die Datenerfassung und -aufbewahrung in Log Analytics nachverfolgen können, indem Sie die Informationen unter [Verstehen Ihrer Nutzung und Schätzen von Kosten](../platform/manage-cost-storage.md#understand-your-usage-and-estimate-costs), [Anzeigen des Log Analytics-Verbrauchs auf Ihrer Azure-Rechnung](../platform/manage-cost-storage.md#viewing-log-analytics-usage-on-your-azure-bill) und zu [Log Analytics-Abfragen](#data-volume-for-workspace-based-application-insights-resources) lesen. 

## <a name="estimating-the-costs-to-manage-your-application"></a>Schätzen der Kosten für die Verwaltung Ihrer Anwendung

Wenn Sie Application Insights noch nicht verwenden, können Sie mit dem [Azure Monitor-Preisrechner](https://azure.microsoft.com/pricing/calculator/?service=monitor) die Kosten für die Nutzung von Application Insights schätzen. Geben Sie „Azure Monitor“ im Suchfeld ein, und klicken Sie auf die ausgegebene Azure Monitor-Kachel. Scrollen Sie auf der Seite nach unten bis zu „Azure Monitor“, und wählen Sie in der Dropdownliste „Typ“ den Eintrag „Application Insights“ aus.  Hier können Sie die Menge der Daten in GB eingeben, die pro Monat erwartungsgemäß gesammelt werden. Die Frage ist also, wie viele Daten bei der Überwachung Ihrer Anwendung von Application Insights gesammelt werden.

Hierfür gibt es zwei Ansätze: Verwenden der standardmäßigen Überwachung und der adaptiven Stichprobenerstellung, die im ASP.NET SDK verfügbar ist, oder Schätzen der wahrscheinlichen Datenerfassung auf Grundlage der von anderen ähnlichen Kunden gemachten Erfahrungen.

### <a name="data-collection-when-using-sampling"></a>Datensammlung unter Verwendung der Stichprobenerstellung

Bei der [adaptiven Stichprobenerstellung](sampling.md#adaptive-sampling) des ASP.NET SDK wird die Datenmenge automatisch angepasst, damit sie eine festgelegte maximale Datenverkehrsrate für die standardmäßige Application Insights-Überwachung nicht überschreitet. Wenn die Anwendung wenig Telemetriedaten erzeugt (z.B. beim Debuggen oder aufgrund geringer Nutzung), werden keine Elemente vom Prozessor für Stichprobenentnahmen gelöscht, solange die Datenmenge unter dem konfigurierten Wert für Ereignisse pro Sekunde liegt. Bei einer Anwendung mit hohem Datenvolumen und dem Standardschwellenwert von fünf Ereignissen pro Sekunde, beschränkt die adaptive Stichprobenerstellung die Anzahl der täglichen Ereignisse auf 432.000. Bei einer typischen durchschnittlichen Ereignisgröße von 1 KB entspricht dies 13,4 GB an Telemetriedaten pro 31-Tage-Monat pro Knoten, auf dem Ihre Anwendung gehostet wird (da die Stichprobenerstellung lokal auf jedem Knoten erfolgt). 

Bei SDKs, die keine adaptive Stichprobenerstellung unterstützen, können Sie die [Erfassungs-Stichprobenerstellung](./sampling.md#ingestion-sampling) verwenden, bei der Stichproben erstellt werden, wenn die Daten von Application Insights basierend auf einem Prozentsatz aufzubewahrender Daten empfangen werden, oder Sie können die [Stichprobenerstellung mit festem Prozentsatz für ASP.NET-, ASP.NET Core- und Java-Websites](sampling.md#fixed-rate-sampling) verwenden, um den von Ihrem Webserver und Webbrowsern gesendeten Datenverkehr zu reduzieren

### <a name="learn-from-what-similar-customers-collect"></a>Erfahrungen ähnlicher Kunden bei der Datensammlung

Wenn Sie im Preisrechner für die Azure-Überwachung für Application Insights die Funktion „Schätzung des Datenvolumens anhand der Anwendungsaktivität“ aktivieren, können Sie Angaben zu Ihrer Anwendung machen (Anforderungen pro Monat und Seitenaufrufe pro Monat, falls Sie clientseitige Telemetriedaten sammeln), und der Rechner teilt Ihnen dann den Medianwert und das 90. Percentil der von ähnlichen Anwendungen gesammelten Datenmenge mit. Diese Anwendungen umfassen die gesamte Bandbreite an Application Insights-Konfigurationen (z.B. weisen einige die standardmäßige [Stichprobenerstellung](./sampling.md) auf, einige keine Stichprobenerstellung usw.), sodass Sie bei der Stichprobenerstellung immer noch die Menge der erfassten Daten auf einen Wert weit unterhalb des Medianwerts reduzieren können. Dies ist jedoch ein Ausgangspunkt, um die Erfahrungen anderer, ähnlicher Kunden zu verstehen.

## <a name="understand-your-usage-and-estimate-costs"></a>Verstehen Ihrer Nutzung und Schätzen von Kosten

Application Insights erleichtert das Verständnis der basierend auf den aktuellen Verwendungsmustern voraussichtlich anfallenden Kosten. Öffnen Sie zunächst im Azure-Portal in der Application Insights-Ressource die Seite **Usage and estimated costs** (Nutzung und geschätzte Kosten):

![Auswählen von Preisen](./media/pricing/pricing-001.png)

A. Überprüfen Sie Ihr Datenvolumen für den Monat. Dies schließt alle nach einer beliebigen [Stichprobenerstellung](./sampling.md) in Ihren Server- und Client-Apps sowie Verfügbarkeitstests empfangenen und beibehaltenen Daten ein.  
B. Für [Webtests mit mehreren Schritten](./availability-multistep.md) wird eine gesonderte Gebühr erhoben. (Darin nicht enthalten sind einfache Verfügbarkeitstests, die von der Gebühr für das Datenvolumen abgedeckt sind.)  
C. Zeigen Sie Trends zum Datenvolumen für den letzten Monat an.  
D: Aktivieren Sie die Datenerfassungs-[Stichprobenerstellung](./sampling.md).
E. Legen Sie die Obergrenze für das tägliche Datenvolumen fest.  

(Beachten Sie, dass sämtliche Preise, die auf den in diesem Artikel enthaltenen Screenshots angezeigt werden, nur Beispielzwecken dienen. Aktuelle Preise in Ihrer Währung und für Ihre Region finden Sie auf der Seite [Application Insights – Preise][pricing].)

Um die Nutzung von Application Insights genauer zu untersuchen, öffnen Sie die Seite **Metriken**, fügen Sie die Metrik „Datenpunktvolumen“ hinzu, und wählen Sie dann die Option *Apply splitting* (Aufteilung anwenden) aus, um die Daten nach Telemetrieelementtyp aufzuteilen.

Die Gebühren für Application Insights werden Ihrer Azure-Rechnung hinzugefügt. Sie können Details zu Ihrer Azure-Rechnung im Abschnitt **Kostenverwaltung + Abrechnung** des Azure-Portals oder im [Azure-Abrechnungsportal](https://account.windowsazure.com/Subscriptions) anzeigen.  Informationen zur Nutzung für Application Insights finden Sie unten im Abschnitt [Anzeigen der Application Insights-Nutzung auf Ihrer Azure-Rechnung](#viewing-application-insights-usage-on-your-azure-bill). 

![Auswählen der Option „Abrechnung“ im Menü auf der linken Seite](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Verwenden von Metriken für Datenvolumen
<a id="understanding-ingested-data-volume"></a>

Um mehr über Ihre Datenvolumen zu erfahren, wählen Sie **Metriken** für Ihre Application Insights-Ressource aus, und fügen Sie ein neues Diagramm hinzu. Wählen Sie für die Diagrammmetrik unter **Protokollbasierte Metriken** die Option **Datenpunktvolumen** aus. Klicken Sie auf **Teilung anwenden**, und wählen Sie die Gruppierung nach **`Telemetryitem`-Typ** aus.

![Verwenden von Metriken zum Überprüfen des Datenvolumens](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Abfragen zum Verstehen der Details zum Datenvolumen

Es gibt zwei Ansätze zum Untersuchen des Datenvolumens für Application Insights. Beim ersten Ansatz werden aggregierte Informationen in der Tabelle `systemEvents` aggregiert, und beim zweiten wird die `_BilledSize`-Eigenschaft verwendet, die für jedes erfasste Ereignis verfügbar ist. `systemEvents` enthält keine Informationen zur Datengröße für [arbeitsbereichsbasierte Application Insights-Instanzen](#data-volume-for-workspace-based-application-insights-resources).

#### <a name="using-aggregated-data-volume-information"></a>Verwenden von aggregierten Informationen zum Datenvolumen

Sie können beispielsweise die folgende Abfrage verwenden, um mithilfe der Tabelle `systemEvents` das in den letzten 24 Stunden erfasste Datenvolumen anzuzeigen:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Um ein Diagramm des Datenvolumens (in Bytes) nach Datentyp für die letzten 30 Tage anzuzeigen, können Sie die folgende Abfrage verwenden:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Diese Abfrage kann in einer [Azure-Protokollwarnung](../platform/alerts-unified-log.md) verwendet werden, um Warnungen für Datenvolumen einzurichten.  

Wenn Sie mehr über die Änderungen Ihrer Telemetriedaten erfahren möchten, können Sie mit der folgenden Abfrage die Anzahl von Ereignissen nach Typ abrufen:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Verwenden der Informationen zur Datengröße nach Ereignis

Sie können die `_BilledSize`-Eigenschaft verwenden, die für jedes erfasste Ereignis verfügbar ist, um weitere Details zur Quelle Ihres Datenvolumens zu erhalten.

Wenn Sie beispielsweise anzeigen möchten, für welche Vorgänge in den letzten 30 Tagen das höchste Datenvolumen generiert wurde, können Sie für alle Abhängigkeitsereignisse die Summe für `_BilledSize` bilden:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

#### <a name="data-volume-for-workspace-based-application-insights-resources"></a>Datenvolumen für arbeitsbereichsbasierte Application Insights-Ressourcen

Navigieren Sie zum Log Analytics-Arbeitsbereich, und führen Sie die folgende Abfrage aus, um die Trends zum Datenvolumen für alle [arbeitsbereichsbasierten Application Insights-Ressourcen](create-workspace-resource.md) eines Arbeitsbereichs für die letzte Woche anzuzeigen:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now())
| summarize sum(_BilledSize) by _ResourceId, bin(TimeGenerated, 1d)
| render areachart
```

Verwenden Sie im Log Analytics-Arbeitsbereich Folgendes, um für eine bestimmte arbeitsbereichsbasierte Application Insights-Ressource die Trends zum Datenvolumen nach Typ abzufragen:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now())
| where _ResourceId contains "<myAppInsightsResourceName>"
| summarize sum(_BilledSize) by Type, bin(TimeGenerated, 1d)
| render areachart
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Anzeigen der Application Insights-Nutzung auf Ihrer Azure-Rechnung

Azure bietet im Hub [Azure Cost Management und Abrechnung](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) eine Vielzahl nützlicher Funktionen. Mithilfe der Funktion „Kostenanalyse“ können Sie beispielsweise Ihre Ausgaben für Azure-Ressourcen überprüfen. Wenn Sie einen Filter nach Ressourcentyp („microsoft.insights/components“ für Application Insights) hinzufügen, können Sie Ihre Ausgaben nachverfolgen. Wählen Sie anschließend unter „Gruppieren nach“ die Option „Kategorie der Verbrauchseinheit“ oder „Verbrauchseinheit“ aus.  Für Application Insights-Ressourcen mit den aktuellen Tarifen wird ein Großteil der Nutzung als Log Analytics-Daten für die Kategorie „Verbrauchseinheit“ angezeigt, da ein zentrales Protokoll-Back-End für alle Azure Monitor-Komponenten vorhanden ist. 

Ein umfassenderes Verständnis Ihres Verbrauchs erlangen Sie, indem Sie [Ihre Verbrauchsdaten aus dem Azure-Portal herunterladen](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal).
In der heruntergeladenen Tabelle ist der Verbrauch pro Azure-Ressource pro Tag aufgeführt. In dieser Excel-Tabelle können Sie den Verbrauch für Ihre Application Insights-Ressourcen ermitteln, indem Sie zuerst nach der Spalte „Kategorie der Verbrauchseinheit“ filtern, um „Application Insights“ und „Log Analytics“ anzuzeigen und dann in der Spalte „Instanz-ID“ einen Filter namens „contains microsoft.insights/components“ hinzufügen.  Die meiste Application Insights-Nutzung wird in Verbrauchseinheiten der entsprechenden Kategorie von Log Analytics gemeldet, da für alle Azure Monitor-Komponenten ein einzelnes Protokoll-Back-End vorhanden ist.  Nur Application Insights-Ressourcen zu Legacytarifen und mehrstufige Webtests werden in der Kategorie der Verbrauchseinheit von Application Insights gemeldet.  Der Verbrauch wird in der Spalte „Verbrauchte Menge“ aufgeführt, und die Einheit für jeden Eintrag wird in der Spalte „Maßeinheit“ angegeben.  Es sind weitere Einzelheiten verfügbar, anhand derer Sie [Ihre Microsoft Azure-Rechnung verstehen](../../cost-management-billing/understand/review-individual-bill.md).

## <a name="managing-your-data-volume"></a>Verwalten des Datenvolumens

Das von Ihnen gesendete Datenvolumen kann mithilfe der folgenden Verfahren verwaltet werden:

* **Stichprobenerstellung**: Mit der Stichprobenerstellung kann die Menge der von Ihrem Server und Ihren Client-Apps gesendeten Telemetriedaten bei minimaler Verzerrung von Metriken verringert werden. Die Stichprobenerstellung ist Ihr wichtigstes Tool, um die Menge der von Ihnen gesendeten Daten zu optimieren. Erfahren Sie mehr über die [Merkmale von Stichproben](./sampling.md).

* **Begrenzen von AJAX-Aufrufen**: Sie können die [Anzahl der gemeldeten AJAX-Aufrufe für jeden Seitenaufruf begrenzen](./javascript.md#configuration) oder AJAX-Berichte deaktivieren. Beachten Sie, dass durch die Deaktivierung von AJAX-Aufrufen die [JavaScript-Korrelation](./javascript.md#enable-correlation) deaktiviert wird.

* **Deaktivieren nicht benötigter Module**: [Bearbeiten Sie „ApplicationInsights.config“](./configuration-with-applicationinsights-config.md), um nicht benötigte Sammlungsmodule zu deaktivieren. Das kann z. B. für Leistungsindikator- oder Abhängigkeitsdaten gelten.

* **Aggregieren von Metriken vorab**: Wenn Sie in Ihre App Aufrufe an TrackMetric eingefügt haben, können Sie Datenverkehr reduzieren, indem Sie die Überladung verwenden, die Ihre Berechnung des Durchschnitts und die Standardabweichung eines Batches von Messungen akzeptiert. Oder Sie können ein [vorab aggregierendes Paket](https://www.myget.org/gallery/applicationinsights-sdk-labs) verwenden.
 
* **Tägliche Obergrenze**: Beim Erstellen einer Application Insights-Ressource im Azure-Portal ist die tägliche Obergrenze auf 100 GB pro Tag festgelegt. Die Standardeinstellung beim Erstellen einer Application Insights-Ressource über Visual Studio ist klein (nur 32,3 MB pro Tag). Zur Vereinfachung von Tests ist der Standardwert für die tägliche Obergrenze festgelegt. Es ist vorgesehen, dass der Benutzer vor dem Bereitstellen der App in der Produktion die tägliche Obergrenze erhöht. 

    Die Obergrenze liegt bei 1.000 GB pro Tag, es sei denn, Sie fordern einen höheren Maximalwert für eine Anwendung mit hohem Datenverkehr an.
    
    E-Mails mit Warnungen zur täglichen Obergrenze werden an Konten gesendet, die Mitglieder der folgenden Rollen für Ihre Application Insights-Ressource sind: „Dienstadministrator“, „Kontoadministrator“, „Co-Administrator“, „Besitzer“.

    Überlegen Sie gründlich, wenn Sie die tägliche Obergrenze festlegen. Sie sollten darauf achten, *niemals die tägliche Obergrenze zu erreichen*. Anderenfalls verlieren Sie die Daten des restlichen Tages, und Sie können nicht Ihre Anwendung überwachen. Um die tägliche Obergrenze zu ändern, verwenden Sie die Option **Obergrenze für das tägliche Volumen**. Sie können über den Bereich **Nutzung und geschätzte Kosten** auf diese Option zugreifen. (Dies wird weiter unten in diesem Artikel ausführlicher beschrieben.)
    
    Wir haben die Beschränkung für bestimmte Abonnementtypen entfernt, bei denen Guthaben gewährt wurden, die nicht für Application Insights verwendet werden konnten. Wenn zuvor für das Abonnement ein Ausgabenlimit galt, enthielt das Dialogfeld mit der täglichen Obergrenze Anweisungen, wie das Ausgabenlimit zu entfernen ist und wie die tägliche Obergrenze über 32,3 MB pro Tag erhöht werden kann.
    
* **Drosselung**: Durch eine Drosselung wird die Datenrate auf 32.000 Ereignisse pro Sekunde eingeschränkt (gemittelt über 1 Minute pro Instrumentierungsschlüssel). Die Datenmenge, die Ihre App sendet, wird minütlich gemessen. Wenn sie die pro Sekunde, über eine Minute gemittelt Datenmenge überschreitet, lehnt der Server einige Anforderungen ab. Das SDK puffert die Daten und versucht dann, diese erneut zu senden. Es führt zu einem mehrere Minuten andauernden Anstieg. Wenn Ihre App die Drosselungsrate beim Senden von Daten laufend überschreitet, werden einige Daten gelöscht. (Die ASP.NET-, Java- und JavaScript-SDKs versuchen, Daten auf diese Weise erneut zu senden, wohingegen andere SDKs gedrosselte Daten möglicherweise einfach löschen.) Tritt eine Drosselung auf, erhalten Sie zur Warnung eine Benachrichtigung über diesen Vorgang.

## <a name="manage-your-maximum-daily-data-volume"></a>Verwalten Ihres maximalen täglichen Datenvolumens

Zur Beschränkung der gesammelten Daten können Sie die Obergrenze für das tägliche Volumen verwenden. Wenn die Obergrenze erreicht ist, gehen sämtliche Telemetriedaten, die von Ihrer Anwendung gesendet wurden, für den restlichen Tag verloren. Von einer Erreichung der täglichen Obergrenze durch Ihre Anwendung wird *abgeraten*. Die Integrität und Leistung Ihrer Anwendung kann nicht nachverfolgt werden, nachdem sie die tägliche Obergrenze erreicht haben.

Verwenden Sie anstelle der Obergrenze für das tägliche Volumen die [Stichprobenerstellung](./sampling.md), um das Datenvolumen auf das gewünschte Niveau festzulegen. Verwenden Sie dann als letztes Mittel die tägliche Obergrenze für den Fall, dass Ihre Anwendung unerwartet beginnt, weitaus größere Mengen an Telemetriedaten zu senden.

### <a name="identify-what-daily-data-limit-to-define"></a>Identifizieren des zu definierenden Tageslimits für Daten

Informieren Sie sich unter „Analysieren der Datennutzung in Application Insights“ über den Datenerfassungstrend sowie über die zu definierende tägliche Volumenobergrenze. Wählen Sie die Obergrenze mit Bedacht, da Sie Ihre Ressourcen nach dem Erreichen des Limits nicht mehr überwachen können.

### <a name="set-the-daily-cap"></a>Festlegen der täglichen Obergrenze

Um die tägliche Obergrenze zu ändern, klicken Sie im Abschnitt **Konfigurieren** Ihrer Application Insights-Ressource auf der Seite **Nutzung und geschätzte Kosten** auf **Tägliche Obergrenze**.

![Anpassen der Volumenobergrenze für Telemetriedaten pro Tag](./media/pricing/pricing-003.png)

Wenn Sie [die tägliche Obergrenze über Azure Resource Manager](./powershell.md) ändern möchten, müssen Sie die Eigenschaft `dailyQuota` ändern.  Über Azure Resource Manager können Sie auch den Zeitpunkt für die tägliche Kontingentzurücksetzung (`dailyQuotaResetTime`) und den Warnschwellenwert (`warningThreshold`) der täglichen Obergrenze festlegen.

### <a name="create-alerts-for-the-daily-cap"></a>Erstellen von Warnungen für die tägliche Obergrenze

Bei der täglichen Obergrenze von Application Insights wird ein Ereignis im Azure-Aktivitätsprotokoll erstellt, wenn für das erfasste Datenvolumen die Warnstufe oder die tägliche Obergrenze erreicht wird.  Sie können [basierend auf diesen Aktivitätsprotokollereignissen eine Warnung erstellen](../platform/alerts-activity-log.md#create-with-the-azure-portal). Die Signalnamen für diese Ereignisse lauten:

* Warnungsschwellenwert für Tageslimit der Application Insights-Komponente erreicht

* Tageslimit für Application Insights-Komponente erreicht

## <a name="sampling"></a>Stichproben
Die [Stichprobenentnahme](./sampling.md) ist eine Methode, die Rate, mit der Telemetriedaten an Ihre App gesendet werden, zu verringern. Gleichzeitig soll die Möglichkeit erhalten bleiben, bei Diagnosesuchläufen verknüpfte Ereignisse zu ermitteln. So erhalten Sie auch korrekte Ereigniszahlen.

Die Stichprobenerstellung ist eine effektive Möglichkeit, die Gebühren zu senken und innerhalb Ihres monatlichen Kontingents zu bleiben. Der Algorithmus für die Stichprobenerstellung behält Elemente in Bezug auf die Telemetrie bei, sodass Sie beispielsweise in Search die Anforderung ermitteln können, die in Beziehung zu einer bestimmten Ausnahme steht. Der Algorithmus behält außerdem korrekte Zahlenwerte bei, sodass Sie im Metrik-Explorer die richtigen Werte für Anforderungsraten, Ausnahmeraten und weitere Messwerte sehen.

Es gibt verschiedene Formen der Stichprobenerstellung.

* Die [adaptive Stichprobenerstellung](./sampling.md) ist die Standardeinstellung für das ASP.NET-SDK. Die adaptive Stichprobenerstellung wird automatisch an die Menge der an Ihre App gesendeten Telemetriedaten angepasst. Sie erfolgt im SDK Ihrer Web-App automatisch, sodass der Telemetriedatenverkehr im Netzwerk verringert wird. 
* *Erfassungs-Stichprobenerstellung* stellt eine Alternative dar, die an dem Punkt arbeitet, an dem Telemetriedaten von Ihrer App den Application Insights-Dienst erreichen. Die Erfassungs-Stichprobenerstellung wirkt sich nicht auf die Menge der Telemetriedaten aus, die von Ihrer App gesendet werden, verringert jedoch die Menge der Daten, die vom Dienst beibehalten werden. Mit der Erfassungs-Stichprobenerstellung können Sie das Kontingent verringern, das von Telemetriedaten von Browsern und anderen SDKs beansprucht wird.

Um die Erfassungs-Stichprobenerstellung festzulegen, navigieren Sie zum Bereich **Preise**:

![Klicken Sie im Bereich „Kontingent und Preise“ auf die Kachel „Stichproben“, und wählen Sie dann eine Einheit für die Stichprobenerstellung aus.](./media/pricing/pricing-004.png)

> [!WARNING]
> Über den Bereich **Datensampling** wird nur der Wert der Erfassungs-Stichprobenerstellung gesteuert. Die Rate der Stichprobenerstellung, die vom Application Insights-SDK in Ihrer App angewendet wird, wird nicht widergespiegelt. Falls für die eingehenden Telemetriedaten bereits im SDK Stichproben erstellt wurden, wird die Erfassungs-Stichprobenerstellung nicht angewendet.
>

Verwenden Sie eine [Analytics-Abfrage](../log-query/log-query-overview.md), um den tatsächlichen Prozentsatz der Stichprobenerstellung unabhängig davon zu ermitteln, wo er angewendet wird. Die Abfrage sieht folgendermaßen aus:

```kusto
requests | where timestamp > ago(1d)
| summarize 100/avg(itemCount) by bin(timestamp, 1h)
| render areachart
```

In jedem beibehaltenen Datensatz gibt `itemCount` die Anzahl ursprünglicher Datensätze an, die der Datensatz darstellt. Diese entspricht 1 + Anzahl der vorherigen verworfenen Datensätze.

## <a name="change-the-data-retention-period"></a>Ändern des Datenaufbewahrungszeitraums

Application Insights Ressourcen werden standardmäßig 90 Tage lang aufbewahrt. Für jede Application Insights Ressource können unterschiedliche Aufbewahrungszeiträume ausgewählt werden. Der vollständige Satz verfügbarer Aufbewahrungszeiträume beträgt 30, 60, 90, 120, 180, 270, 365, 550 oder 730 Tage. Informieren Sie sich über die [Preise](https://azure.microsoft.com/pricing/details/monitor/) für eine längere Datenaufbewahrung. 

Wenn Sie die Aufbewahrungsdauer ändern möchten, rufen Sie in der Application Insights-Ressource die Seite **Nutzung und geschätzte Kosten** auf und wählen Sie die Option **Datenaufbewahrung**  aus:

![Screenshot, der zeigt, wo der Datenaufbewahrungszeitraum geändert wird](./media/pricing/pricing-005.png)

Wenn die Aufbewahrungsdauer gesenkt wird, besteht ein Toleranzzeitraum von mehreren Tagen, bevor die ältesten Daten entfernt werden.

Die Aufbewahrungsdauer kann auch [programmgesteuert mithilfe von PowerShell](powershell.md#set-the-data-retention) festgelegt werden, indem der Parameter `retentionInDays` verwendet wird. Wenn Sie die Datenaufbewahrung auf 30 Tage festlegen, können Sie mit dem Parameter `immediatePurgeDataOn30Days` eine sofortige Bereinigung älterer Daten auslösen. Dies kann für Szenarien im Zusammenhang mit Compliance nützlich sein. Diese Bereinigungsfunktion wird nur über Azure Resource Manager bereitgestellt und sollte mit äußerster Sorgfalt verwendet werden. Die tägliche Zurücksetzungszeit für das Datenvolumenlimit kann mithilfe von Azure Resource Manager konfiguriert werden, um den `dailyQuotaResetTime`-Parameter festzulegen.

## <a name="data-transfer-charges-using-application-insights"></a>Datenübertragungsgebühren bei Verwendung von Application Insights

Das Senden von Daten an Application Insights kann Gebühren für Datenbandbreiten verursachen. Wie [auf der Seite Azure-Bandbreitenpreise](https://azure.microsoft.com/pricing/details/bandwidth/) beschrieben, wird die Datenübertragung zwischen Azure-Diensten in zwei Regionen mit dem normalen Preis als ausgehende Datenübertragung abgerechnet. Eingehende Datenübertragungen sind kostenlos. Diese Gebühr ist jedoch sehr niedrig (wenige %) im Vergleich zu den Kosten für die Erfassung von Application Insights-Protokolldaten. Folglich muss sich die Kontrolle von Kosten für Log Analytics auf Ihr erfasstes Datenvolumen konzentrieren. Anleitungen zu einem besseren Verständnis davon, finden Sie [hier](#managing-your-data-volume).

## <a name="limits-summary"></a>Zusammenfassung der Grenzwerte

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Deaktivieren der E-Mails zur täglichen Obergrenze

Um die E-Mails zum täglichen Volumenobergrenze zu ändern, klicken Sie im Abschnitt **Konfigurieren** Ihrer Application Insights-Ressource auf der Seite **Nutzung und geschätzte Kosten** auf **Tägliche Obergrenze**. Es gibt Einstellungen zum Senden von E-Mail bei Erreichen der Obergrenze sowie zum Senden bei Erreichen einer anpassbaren Warnstufe. Wenn Sie alle E-Mails zur täglichen Volumenobergrenze deaktivieren möchten, deaktivieren Sie beide Kontrollkästchen.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Älterer Enterprise-Tarif (pro Knoten)

Für Kunden, die schon lange Azure Application Insights nutzen, gibt es weiterhin zwei mögliche Tarife: Basic und Enterprise. Der Basic-Tarif entspricht der obigen Beschreibung und ist der Standardtarif. Er umfasst alle Features des Enterprise-Tarifs ohne zusätzliche Kosten. Im Basic-Tarif wird in erster Linie das Volumen der erfassten Daten abgerechnet.

> [!NOTE]
> Diese älteren Tarife wurden umbenannt: Der Enterprise-Tarif heißt jetzt **Pro Knoten**, der Basic-Tarif heißt jetzt **Pro GB**. Diese neuen Namen werden nachfolgend und im Azure-Portal verwendet.  

Für den Tarif „Pro Knoten“ (ehemals „Enterprise“) wird eine knotenbasierte Gebühr erhoben, und jedem Knoten ist ein tägliches Datenkontingent zugewiesen. Im Tarif „Pro Knoten“ werden die Daten, die über das enthaltene Datenkontingent hinausgehen, berechnet. Bei Verwendung der Operations Management Suite sollten Sie sich für den Tarif „Pro Knoten“ entscheiden.

Aktuelle Preise in Ihrer Währung und für Ihre Region finden Sie auf der Seite [Application Insights – Preise](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> Im April 2018 haben wir ein [neues Preismodell für die Azure-Überwachung eingeführt](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/). Bei diesem Modell gilt für das gesamte Portfolio der Überwachungsdienste das einfache Prinzip der nutzungsbasierten Bezahlung. Erfahren Sie mehr über das [neue Preismodell](../platform/usage-estimated-costs.md), die [Bewertung der Auswirkungen einer Migration zu diesem Modell](../platform/usage-estimated-costs.md#understanding-your-azure-monitor-costs) basierend auf Ihren Verwendungsmustern und [die Auswahl des neuen Modells](../platform/usage-estimated-costs.md#azure-monitor-pricing-model).

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Tarif „Pro Knoten“ und Berechtigungen für Operations Management Suite-Abonnements

Wie [bereits angekündigt](/archive/blogs/msoms/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription) können Kunden, die Operations Management Suite E1 und E2 erwerben, Application Insights mit dem Tarif „Pro Knoten“ als zusätzliche Komponente ohne weitere Kosten erhalten. Jede Einheit von Operations Management Suite E1 und E2 beinhaltet eine Berechtigung für einen Knoten mit dem Application Insights-Tarif „Pro Knoten“. Jeder Application Insights-Knoten ermöglicht das Erfassen von bis zu 200 MB Daten pro Tag (getrennt von der Log Analytics-Datenerfassung) bei einer Datenaufbewahrungsdauer von 90 Tagen ohne zusätzliche Kosten. Der Tarif wird weiter unten in diesem Artikel ausführlicher erläutert.

Da dieser Tarif nur für Kunden mit einem Operations Management Suite-Abonnement gilt, wird für Kunden ohne Operations Management Suite-Abonnement keine Option für diesen Tarif angezeigt.

> [!NOTE]
> Um diese Berechtigung zu erhalten, muss für Ihre Application Insights-Ressourcen der Tarif „Pro Knoten“ gelten. Diese Berechtigung gilt nur basierend auf Knoten. Für Application Insights-Ressourcen im Tarif „Pro GB“ werden keine Vorteile gewährt. Diese Berechtigung ist nicht in den geschätzten Kosten ausgewiesen, die im Bereich **Nutzung und geschätzte Kosten** gezeigt werden. Außerdem gilt: Wenn Sie im April 2018 ein Abonnement zum neuen Preismodell für die Azure-Überwachung migriert haben, ist nur der Tarif „Pro GB“ verfügbar. Wenn Sie über ein Operations Management Suite-Abonnement verfügen, wird davon abgeraten, dieses zum neuen Preismodell für die Azure-Überwachung zu migrieren.

### <a name="how-the-per-node-tier-works"></a>Funktionsweise des Tarifs „Pro Knoten“

* Sie bezahlen für jeden Knoten, der Telemetriedaten für Apps mit dem Tarif „Pro Knoten“ sendet.
  * Ein *Knoten* ist entweder ein physischer oder ein virtueller Servercomputer oder eine PaaS-Rolleninstanz (Platform-as-a-Service), der bzw. die Ihre App hostet.
  * Entwicklungscomputer, Clientbrowser und mobile Geräte zählen nicht als Knoten.
  * Wenn die App mehrere Komponenten aufweist, die Telemetriedaten senden, z. B. ein Webdienst und ein Back-End-Worker, werden die Komponenten separat gezählt.
  * [Live Metrics Stream](./live-stream.md)-Daten werden bei der Preisgestaltung nicht berechnet. In einem Abonnement fallen die Gebühren pro Knoten und nicht pro App an. Wenn Sie fünf Knoten haben, die Telemetriedaten für 12 Apps senden, wird die Gebühr für fünf Knoten berechnet.
* Obwohl Gebühren pro Monat angegeben sind, müssen Sie nur für jede Stunde bezahlen, in der ein Knoten Telemetriedaten von einer App sendet. Die Stundengebühr entspricht der angegebenen Monatsgebühr, die durch 744 (die Anzahl der Stunden eines Monats mit 31 Tagen) dividiert wurde.
* Eine Datenvolumenzuteilung von 200 MB pro Tag ist für jeden erkannten Knoten vorgesehen (mit stundenbezogener Granularität). Eine nicht genutzte Datenzuteilung wird nicht von einem Tag auf den nächsten übertragen.
  * Wenn Sie sich für den Tarif „Pro Knoten“ entscheiden, wird jedem Abonnement basierend auf der Anzahl von Knoten, die in diesem Abonnement Telemetriedaten an Application Insights-Ressourcen senden, ein tägliches Datenkontingent zugeordnet. Wenn Sie also fünf Knoten haben, die den ganzen Tag Daten senden, wird Ihnen insgesamt 1 GB für alle Application Insights-Ressourcen in diesem Abonnement zugeteilt. Es ist dabei unerheblich, ob bestimmte Knoten mehr Daten senden als andere, da die enthaltene Datenmenge für alle Knoten zusammen berechnet wird. Wenn die Application Insights-Ressourcen an einem Tag mehr Daten empfangen als in der täglichen Datenzuteilung für dieses Abonnement vorgesehen ist, gelten die Überschreitungsgebühren pro GB. 
  * Das tägliche Datenkontingent wird berechnet aus der Anzahl der Stunden pro Tag (nach UTC), die jeder Knoten Telemetriedaten sendet, dividiert durch 24 und multipliziert mit 200 MB. Wenn Sie also vier Knoten haben, die an 15 Stunden des Tages Telemetriedaten senden, sind für diesen Tag ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB an Daten enthalten. Bei einem Preis von 2,30 USD pro GB für Datenüberschreitung wäre die Gebühr 1,15 USD, wenn die Knoten an diesem Tag 1 GB an Daten senden.
  * Das tägliche Kontingent des Tarifs „Pro Knoten“ wird nicht für Anwendungen verwendet, für die Sie den Tarif „Pro GB“ gewählt haben. Ein nicht verwendetes Kontingent wird nicht von Tag zu Tag übernommen.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Beispiele, wie unterschiedliche Anzahlen von Knoten bestimmt werden

| Szenario                               | Gesamtanzahl der Knoten pro Tag |
|:---------------------------------------|:----------------:|
| 1 Anwendung verwendet 3 Azure App Service-Instanzen und 1 virtuellen Server | 4 |
| Drei Anwendungen, die auf zwei virtuellen Computern ausgeführt werden; die Application Insights-Ressourcen für diese Anwendungen gehören zum gleichen Abonnement und unterliegen dem Tarif „Pro Knoten“. | 2 | 
| 4-Anwendungen, deren Insights-Ressourcen sich im selben Abonnement befinden; jede Anwendung wird in 2 Instanzen in 16 Nebenstunden und in 4 Instanzen in 8 Spitzenstunden ausgeführt | 13,33 |
| Clouddienste mit 1 Workerrolle und 1 Webrolle, die je 2 Instanzen ausführen | 4 | 
| Ein Service Fabric-Cluster mit 5 Knoten, in dem 50 Microservices ausgeführt werden; jeder Microservice führt 3 Instanzen aus | 5|

* Die genaue Knotenanzahl hängt vom Application Insights SDK ab, das von der Anwendung verwendet wird. 
  * Ab der SDK-Version 2.2 melden sowohl das Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) als auch das [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) jeden Anwendungshost als einen Knoten. Beispiele sind der Computername für einen physischen Server und VM-Hosts oder der Instanzname für Clouddienste.  Die einzige Ausnahme ist eine Anwendung, die nur [.NET Core](https://dotnet.github.io/) und das Application Insights Core SDK verwendet. In diesem Fall wird nur ein Knoten für alle Hosts gemeldet, weil der Hostname nicht verfügbar ist.
  * Für frühere Versionen des SDK verhält sich das [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) wie die neueren SDK-Versionen, das [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) meldet jedoch nur einen Knoten, unabhängig von der Anzahl der Anwendungshosts.
  * Wird in Ihrer Anwendung das SDK verwendet, um **roleInstance** auf einen benutzerdefinierten Wert festzulegen, wird standardmäßig dieser Wert verwendet, um die Anzahl der Knoten zu bestimmen.
  * Wenn Sie eine neue SDK-Version mit einer App verwenden, die auf Clientcomputern oder Mobilgeräten ausgeführt wird, kann für die Anzahl der Knoten eine große Zahl zurückgegeben werden (wegen der großen Anzahl von Clientcomputern oder Mobilgeräten).

## <a name="automation"></a>Automation

Sie können mit der Azure-Ressourcenverwaltung ein Skript schreiben, um den Tarif festzulegen. [Weitere Informationen](powershell.md#price).

## <a name="next-steps"></a>Nächste Schritte

* [Stichprobenentnahme](./sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ./app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/

