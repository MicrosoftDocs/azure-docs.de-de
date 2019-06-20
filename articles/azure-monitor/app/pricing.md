---
title: Verwalten von Nutzung und Kosten für Azure Application Insights | Microsoft-Dokumentation
description: Verwalten Sie Telemetriedatenvolumen, und überwachen Sie Kosten in Application Insights.
services: application-insights
documentationcenter: ''
author: DaleKoetke
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 05/29/2019
ms.author: dalek
ms.openlocfilehash: ebcb0922335a2bdc5423ec4e4bfce7c1cd71c46a
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357263"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Verwalten der Nutzung und der Kosten für Application Insights

> [!NOTE]
> Dieser Artikel beschreibt, wie die Datennutzung in Application Insights analysiert wird.  Entsprechende Informationen finden Sie in den folgenden Artikeln.
> - [Überwachen der Nutzung und der geschätzten Kosten](../../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) beschreibt, wie die Nutzung und geschätzten Kosten über mehrere Azure-Überwachungsfeatures hinweg für unterschiedliche Preismodelle angezeigt werden. Außerdem wird beschrieben, wie Sie Ihr Preismodell ändern können.

Wenn Sie Fragen zu den Preisen für Application Insights haben, können Sie gerne eine Frage in unserem [Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights) posten.

## <a name="pricing-model"></a>Preismodell

Die Preise für [Azure Application Insights][start] basieren auf dem verbrauchtem Datenvolumen. Jede Application Insights-Ressource wird als separater Dienst abgerechnet und auf der Rechnung für Ihr Azure-Abonnement aufgeführt.

### <a name="data-volume-details"></a>Details zum Datenvolumen

* Das Datenvolumen bezieht sich auf die Anzahl von Bytes an Telemetriedaten, die von Application Insights empfangen werden. Das Datenvolumen wird anhand der Größe des nicht komprimierten JSON-Datenpakets gemessen, das Application Insights von Ihrer Anwendung empfängt. Für [in Analytics importierte Tabellendaten](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import) wird das Datenvolumen als die nicht komprimierte Größe von Dateien gemessen, die an Application Insights gesendet werden.
* Die Datenvolumengebühren Ihrer Anwendung werden jetzt ab April 2018 auf einem neuen Verbrauchszähler mit dem Namen **Data Ingestion** gemeldet. Diese neue Verbrauchseinheit wird von Überwachungstechnologien wie Applications Insights und Log Analytics gemeinsam genutzt und steht derzeit unter dem Dienstnamen **Log Analytics** zur Verfügung. 
* [Live Metrics Stream](../../azure-monitor/app/live-stream.md)-Daten werden bei der Preisgestaltung nicht berechnet.

Aktuelle Preise in Ihrer Währung und Region finden Sie auf der Seite [Application Insights – Preise][pricing].

### <a name="multi-step-web-tests"></a>Webtests mit mehreren Schritten

Für [mehrstufige Webtests](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) wird eine zusätzliche Gebühr erhoben. Mehrstufige Webtests sind Webtests, die eine Sequenz von Aktionen ausführen.

Es gibt keine gesonderte Gebühr für *Pingtests* einer einzelnen Seite. Telemetriedaten aus Pingtests und mehrstufigen Tests werden ebenso wie andere Telemetriedaten aus Ihrer App in Rechnung gestellt.

## <a name="review-usage-and-estimate-costs"></a>Überprüfen der Nutzung und Schätzen der Kosten

Application Insights erleichtert das Verständnis der basierend auf den aktuellen Verwendungsmustern voraussichtlich anfallenden Kosten. Öffnen Sie zunächst im Azure-Portal in der Application Insights-Ressource die Seite **Usage and estimated costs** (Nutzung und geschätzte Kosten):

![Auswählen von Preisen](./media/pricing/pricing-001.png)

A. Überprüfen Sie Ihr Datenvolumen für den Monat. Dies schließt alle nach einer beliebigen [Stichprobenerstellung](../../azure-monitor/app/sampling.md) in Ihren Server- und Client-Apps sowie Verfügbarkeitstests empfangenen und beibehaltenen Daten ein.  
B: Für [Webtests mit mehreren Schritten](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) wird eine gesonderte Gebühr erhoben. (Darin nicht enthalten sind einfache Verfügbarkeitstests, die von der Gebühr für das Datenvolumen abgedeckt sind.)  
C. Zeigen Sie Trends zum Datenvolumen für den letzten Monat an.  
D: Aktivieren Sie die Datenerfassungs-[Stichprobenerstellung](../../azure-monitor/app/sampling.md).   
E. Legen Sie die Obergrenze für das tägliche Datenvolumen fest.  

Um die Nutzung von Application Insights genauer zu untersuchen, öffnen Sie die Seite **Metriken**, fügen Sie die Metrik „Datenpunktvolumen“ hinzu, und wählen Sie dann die Option *Apply splitting* (Aufteilung anwenden) aus, um die Daten nach Telemetrieelementtyp aufzuteilen. 

Die Gebühren für Application Insights werden Ihrer Azure-Rechnung hinzugefügt. Sie können Details zu Ihrer Azure-Rechnung im Bereich **Abrechnung** des Azure-Portals oder im [Azure-Abrechnungsportal](https://account.windowsazure.com/Subscriptions) einsehen. 

![Auswählen der Option „Abrechnung“ im Menü auf der linken Seite](./media/pricing/02-billing.png)

## <a name="data-rate"></a>Datenrate
Das Volumen der Daten, die Sie senden, ist auf dreierlei Weise beschränkt:

* **Stichprobenerstellung**: Mit der Stichprobenerstellung kann die Menge der von Ihrem Server und Ihren Client-Apps gesendeten Telemetriedaten bei minimaler Verzerrung von Metriken verringert werden. Die Stichprobenerstellung ist Ihr wichtigstes Tool, um die Menge der von Ihnen gesendeten Daten zu optimieren. Erfahren Sie mehr über die [Merkmale von Stichproben](../../azure-monitor/app/sampling.md). 
* **Tägliche Obergrenze**: Beim Erstellen einer Application Insights-Ressource im Azure-Portal ist die tägliche Obergrenze auf 100 GB pro Tag festgelegt. Die Standardeinstellung beim Erstellen einer Application Insights-Ressource über Visual Studio ist klein (nur 32,3 MB pro Tag). Zur Vereinfachung von Tests ist der Standardwert für die tägliche Obergrenze festgelegt. Es ist vorgesehen, dass der Benutzer vor dem Bereitstellen der App in der Produktion die tägliche Obergrenze erhöht. 

    Die Obergrenze liegt bei 1.000 GB pro Tag, es sei denn, Sie fordern einen höheren Maximalwert für eine Anwendung mit hohem Datenverkehr an. 

    Überlegen Sie gründlich, wenn Sie die tägliche Obergrenze festlegen. Sie sollten darauf achten, *niemals die tägliche Obergrenze zu erreichen*. Anderenfalls verlieren Sie die Daten des restlichen Tages, und Sie können nicht Ihre Anwendung überwachen. Um die tägliche Obergrenze zu ändern, verwenden Sie die Option **Obergrenze für das tägliche Volumen**. Sie können über den Bereich **Nutzung und geschätzte Kosten** auf diese Option zugreifen. (Dies wird weiter unten in diesem Artikel ausführlicher beschrieben.)
    Wir haben die Beschränkung für bestimmte Abonnementtypen entfernt, bei denen Guthaben gewährt wurden, die nicht für Application Insights verwendet werden konnten. Wenn zuvor für das Abonnement ein Ausgabenlimit galt, enthielt das Dialogfeld mit der täglichen Obergrenze Anweisungen, wie das Ausgabenlimit zu entfernen ist und wie die tägliche Obergrenze über 32,3 MB pro Tag erhöht werden kann.
* **Drosselung**: Durch eine Drosselung wird die Datenrate auf 32.000 Ereignisse pro Sekunde eingeschränkt (gemittelt über 1 Minute pro Instrumentierungsschlüssel).

*Was geschieht, wenn meine App die Drosselungsrate überschreitet?*

* Die Datenmenge, die Ihre App sendet, wird minütlich gemessen. Wenn sie die pro Sekunde, über eine Minute gemittelt Datenmenge überschreitet, lehnt der Server einige Anforderungen ab. Das SDK puffert die Daten und versucht dann, diese erneut zu senden. Es führt zu einem mehrere Minuten andauernden Anstieg. Wenn Ihre App die Drosselungsrate beim Senden von Daten laufend überschreitet, werden einige Daten gelöscht. (Die ASP.NET-, Java- und JavaScript-SDKs versuchen, Daten auf diese Weise erneut zu senden, wohingegen andere SDKs gedrosselte Daten möglicherweise einfach löschen.) Tritt eine Drosselung auf, erhalten Sie zur Warnung eine Benachrichtigung über diesen Vorgang.

*Woher weiß ich, wie viele Daten meine App sendet?*

Sie können eine der folgenden Optionen verwenden, um festzustellen, wie viele Daten Ihre App sendet:

* Navigieren Sie zum Bereich **Nutzung und geschätzte Kosten**, um das Diagramm des täglichen Datenvolumens anzuzeigen. 
* Fügen Sie im Metrik-Explorer ein neues Diagramm hinzu. Wählen Sie für die Diagrammmetrik **Datenpunktvolumen** aus. Aktivieren Sie **Gruppierung**, und gruppieren Sie nach **Datentyp**.

## <a name="reduce-your-data-rate"></a>Verringern der Datenrate
Hier sind einige Schritte, die Sie ausführen können, um Ihr Datenvolumen zu reduzieren:

* Verwenden Sie [Stichproben](../../azure-monitor/app/sampling.md). Durch diese Technologie wird Ihre Datenrate ohne Datenschiefe bei Ihren Metriken reduziert. Sie können weiterhin zwischen verwandten Elementen in der Search navigieren. Bei Server-Apps erfolgt die Stichprobenerstellung automatisch.
* [Begrenzen Sie die Anzahl der gemeldeten AJAX-Aufrufe](../../azure-monitor/app/javascript.md#detailed-configuration) für jeden Seitenaufruf, oder deaktivieren Sie AJAX-Berichte.
* [Bearbeiten Sie „ApplicationInsights.config“](../../azure-monitor/app/configuration-with-applicationinsights-config.md), um nicht benötigte Sammlungsmodule zu deaktivieren. Das kann z. B. für Leistungsindikator- oder Abhängigkeitsdaten gelten.
* Teilen Sie Ihre Telemetrie auf getrennte Instrumentierungsschlüssel auf. 
* Aggregieren Sie Metriken vorab. Wenn Sie in Ihre App Aufrufe an TrackMetric eingefügt haben, können Sie Datenverkehr reduzieren, indem Sie die Überladung verwenden, die Ihre Berechnung des Durchschnitts und die Standardabweichung eines Batches von Messungen akzeptiert. Oder Sie können ein [vorab aggregierendes Paket](https://www.myget.org/gallery/applicationinsights-sdk-labs) verwenden.

## <a name="manage-the-maximum-daily-data-volume"></a>Verwalten des maximalen täglichen Datenvolumens

Zur Beschränkung der gesammelten Daten können Sie die Obergrenze für das tägliche Volumen verwenden. Wenn die Obergrenze erreicht ist, gehen sämtliche Telemetriedaten, die von Ihrer Anwendung gesendet wurden, für den restlichen Tag verloren. Von einer Erreichung der täglichen Obergrenze durch Ihre Anwendung wird *abgeraten*. Die Integrität und Leistung Ihrer Anwendung kann nicht nachverfolgt werden, nachdem sie die tägliche Obergrenze erreicht haben.

Verwenden Sie anstelle der Obergrenze für das tägliche Volumen die [Stichprobenerstellung](../../azure-monitor/app/sampling.md), um das Datenvolumen auf das gewünschte Niveau festzulegen. Verwenden Sie dann als letztes Mittel die tägliche Obergrenze für den Fall, dass Ihre Anwendung unerwartet beginnt, weitaus größere Mengen an Telemetriedaten zu senden.

Um die tägliche Obergrenze zu ändern, klicken Sie im Abschnitt **Konfigurieren** Ihrer Application Insights-Ressource auf der Seite **Nutzung und geschätzte Kosten** auf **Tägliche Obergrenze**.

![Anpassen der Volumenobergrenze für Telemetriedaten pro Tag](./media/pricing/pricing-003.png)

## <a name="sampling"></a>Stichproben
Die [Stichprobenerstellung](../../azure-monitor/app/sampling.md) ist eine Methode, die Rate, mit der Telemetriedaten an Ihre App gesendet werden, zu verringern. Gleichzeitig soll die Möglichkeit erhalten bleiben, bei Diagnosesuchläufen relevante Ereignisse zu ermitteln. So erhalten Sie auch korrekte Ereigniszahlen.

Die Stichprobenerstellung ist eine effektive Möglichkeit, die Gebühren zu senken und innerhalb Ihres monatlichen Kontingents zu bleiben. Der Algorithmus für die Stichprobenerstellung behält Elemente in Bezug auf die Telemetrie bei, sodass Sie beispielsweise in Search die Anforderung ermitteln können, die in Beziehung zu einer bestimmten Ausnahme steht. Der Algorithmus behält außerdem korrekte Zahlenwerte bei, sodass Sie im Metrik-Explorer die richtigen Werte für Anforderungsraten, Ausnahmeraten und weitere Messwerte sehen.

Es gibt verschiedene Formen der Stichprobenerstellung.

* Die [adaptive Stichprobenerstellung](../../azure-monitor/app/sampling.md) ist die Standardeinstellung für das ASP.NET-SDK. Die adaptive Stichprobenerstellung wird automatisch an die Menge der an Ihre App gesendeten Telemetriedaten angepasst. Sie erfolgt im SDK Ihrer Web-App automatisch, sodass der Telemetriedatenverkehr im Netzwerk verringert wird. 
* *Erfassungs-Stichprobenerstellung* stellt eine Alternative dar, die an dem Punkt arbeitet, an dem Telemetriedaten von Ihrer App den Application Insights-Dienst erreichen. Die Erfassungs-Stichprobenerstellung wirkt sich nicht auf die Menge der Telemetriedaten aus, die von Ihrer App gesendet werden, verringert jedoch die Menge der Daten, die vom Dienst beibehalten werden. Mit der Erfassungs-Stichprobenerstellung können Sie das Kontingent verringern, das von Telemetriedaten von Browsern und anderen SDKs beansprucht wird.

Um die Erfassungs-Stichprobenerstellung festzulegen, navigieren Sie zum Bereich **Preise**:

![Klicken Sie im Bereich „Kontingent und Preise“ auf die Kachel „Stichproben“, und wählen Sie dann eine Einheit für die Stichprobenerstellung aus.](./media/pricing/pricing-004.png)

> [!WARNING]
> Über den Bereich **Datensampling** wird nur der Wert der Erfassungs-Stichprobenerstellung gesteuert. Die Rate der Stichprobenerstellung, die vom Application Insights-SDK in Ihrer App angewendet wird, wird nicht widergespiegelt. Falls für die eingehenden Telemetriedaten bereits im SDK Stichproben erstellt wurden, wird die Erfassungs-Stichprobenerstellung nicht angewendet.
>

Verwenden Sie eine [Analytics-Abfrage](analytics.md), um den tatsächlichen Prozentsatz der Stichprobenerstellung unabhängig davon zu ermitteln, wo er angewendet wird. Die Abfrage sieht folgendermaßen aus:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

In jedem beibehaltenen Datensatz gibt `itemCount` die Anzahl ursprünglicher Datensätze an, die der Datensatz darstellt. Diese entspricht 1 + Anzahl der vorherigen verworfenen Datensätze. 

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
> Im April 2018 haben wir ein [neues Preismodell für die Azure-Überwachung eingeführt](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/). Bei diesem Modell gilt für das gesamte Portfolio der Überwachungsdienste das einfache Prinzip der nutzungsbasierten Bezahlung. Erfahren Sie mehr über das [neue Preismodell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), die [Bewertung der Auswirkungen einer Migration zu diesem Modell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) basierend auf Ihren Verwendungsmustern und [die Auswahl des neuen Modells](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model).

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Tarif „Pro Knoten“ und Berechtigungen für Operations Management Suite-Abonnements

Wie [bereits angekündigt](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/) können Kunden, die Operations Management Suite E1 und E2 erwerben, Application Insights mit dem Tarif „Pro Knoten“ als zusätzliche Komponente ohne weitere Kosten erhalten. Jede Einheit von Operations Management Suite E1 und E2 beinhaltet eine Berechtigung für einen Knoten mit dem Application Insights-Tarif „Pro Knoten“. Jeder Application Insights-Knoten ermöglicht das Erfassen von bis zu 200 MB Daten pro Tag (getrennt von der Log Analytics-Datenerfassung) bei einer Datenaufbewahrungsdauer von 90 Tagen ohne zusätzliche Kosten. Der Tarif wird weiter unten in diesem Artikel ausführlicher erläutert. 

Da dieser Tarif nur für Kunden mit einem Operations Management Suite-Abonnement gilt, wird für Kunden ohne Operations Management Suite-Abonnement keine Option für diesen Tarif angezeigt.

> [!NOTE]
> Um diese Berechtigung zu erhalten, muss für Ihre Application Insights-Ressourcen der Tarif „Pro Knoten“ gelten. Diese Berechtigung gilt nur basierend auf Knoten. Für Application Insights-Ressourcen im Tarif „Pro GB“ werden keine Vorteile gewährt. Diese Berechtigung ist nicht in den geschätzten Kosten ausgewiesen, die im Bereich **Nutzung und geschätzte Kosten** gezeigt werden. Außerdem gilt: Wenn Sie im April 2018 ein Abonnement zum neuen Preismodell für die Azure-Überwachung migriert haben, ist nur der Tarif „Pro GB“ verfügbar. Wenn Sie über ein Operations Management Suite-Abonnement verfügen, wird davon abgeraten, dieses zum neuen Preismodell für die Azure-Überwachung zu migrieren.

### <a name="how-the-per-node-tier-works"></a>Funktionsweise des Tarifs „Pro Knoten“

* Sie bezahlen für jeden Knoten, der Telemetriedaten für Apps mit dem Tarif „Pro Knoten“ sendet.
  * Ein *Knoten* ist entweder ein physischer oder ein virtueller Servercomputer oder eine PaaS-Rolleninstanz (Platform-as-a-Service), der bzw. die Ihre App hostet.
  * Entwicklungscomputer, Clientbrowser und mobile Geräte zählen nicht als Knoten.
  * Wenn die App mehrere Komponenten aufweist, die Telemetriedaten senden, z. B. ein Webdienst und ein Back-End-Worker, werden die Komponenten separat gezählt.
  * [Live Metrics Stream](../../azure-monitor/app/live-stream.md)-Daten werden bei der Preisgestaltung nicht berechnet. In einem Abonnement fallen die Gebühren pro Knoten und nicht pro App an. Wenn Sie fünf Knoten haben, die Telemetriedaten für 12 Apps senden, wird die Gebühr für fünf Knoten berechnet.
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
  * Wenn Sie eine neue SDK-Version mit einer App verwenden, die auf Clientcomputern oder Mobilgeräten ausgeführt wird, kann für die Anzahl der Knoten eine sehr große Zahl zurückgegeben werden (wegen der großen Anzahl von Clientcomputern oder Mobilgeräten). 

## <a name="automation"></a>Automation

Sie können mit der Azure-Ressourcenverwaltung ein Skript schreiben, um den Tarif festzulegen. [Weitere Informationen](powershell.md#price).


## <a name="next-steps"></a>Nächste Schritte

* [Stichproben](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
