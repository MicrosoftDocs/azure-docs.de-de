---
title: Überwachen der Leistung von AKS-Clustern mit Azure Monitor für Container | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie mit Azure Monitor für Container die Leistung analysieren und Daten protokollieren können.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2019
ms.author: magoedte
ms.openlocfilehash: 531e51fbddb99ebba11284d5291b4cca26559bc1
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65906777"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Verstehen der Leistung von AKS-Clustern mit Azure Monitor für Container 
Mit Azure Monitor für Container können Sie die Leistungsdiagramme und den Integritätsstatus verwenden, um die Workload Ihrer Azure Kubernetes Service-Cluster (AKS) aus zwei Perspektiven zu überwachen, direkt aus einem AKS-Cluster oder aus allen AKS-Clustern in einem Abonnement von Azure Monitor. Die Anzeige von Azure Container Instances (ACI) ist auch möglich, wenn Sie einen bestimmten AKS-Cluster überwachen.

Dieser Artikel soll Ihr Verständnis für die Benutzererfahrung in den beiden Perspektiven schärfen und aufzeigen, wie Sie erkannte Probleme schnell bewerten, untersuchen und beheben.

Weitere Informationen zum Aktivieren von Azure Monitor für Container finden Sie unter [Onboarding von Azure Monitor für Container](container-insights-onboard.md).

Azure Monitor bietet eine Multi-Cluster-Ansicht, die den Integritätsstatus aller überwachten AKS-Cluster zeigt, auf denen Linux und Windows Server 2019 ausgeführt wird und die in Ressourcengruppen in Ihrem Abonnement bereitgestellt sind.  Es zeigt erkannte AKS-Cluster, die nicht von der Lösung überwacht werden. Sie können die Clusterintegrität auf einen Blick einschätzen und von diesem Punkt aus einen Drilldown zur Seite des Knotens und der Controllerleistung ausführen oder alternativ zu den Leistungsdiagrammen für den Cluster navigieren.  Für AKS-Cluster, die ermittelt und als nicht überwacht erkannt wurden, können Sie die Überwachung jederzeit aktivieren.  

Die wichtigsten Unterschiede zwischen der Überwachung eines Windows Server-Clusters mit Azure Monitor für Container gegenüber einem Linux-Cluster sind die folgenden:

- Für Windows-Knoten und -Container ist keine Arbeitsspeicher-RSS-Metrik verfügbar. 
- Für Windows-Knoten sind keine Informationen zur Speicherkapazität des Datenträgers verfügbar.
- Eine Unterstützung von Liveprotokollen ist verfügbar, ausgenommen Windows-Containerprotokolle.
- Nur Pod-Umgebungen werden überwacht, nicht aber Docker-Umgebungen.
- Mit der Vorschauversion werden maximal 30 Windows Server-Container unterstützt. Diese Einschränkung gilt nicht für Linux-Container.  

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.
Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

## <a name="multi-cluster-view-from-azure-monitor"></a>Multi-Cluster-Ansicht in Azure Monitor 
Zum Anzeigen des Integritätsstatus aller bereitgestellten AKS-Cluster wählen Sie im linken Seitenbereich im Azure-Portal **Überwachen** aus.  Wählen Sie im Abschnitt **Insights** die Option **Container** aus.  

![Beispiel für ein Multi-Cluster-Dashboard in Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview.png)

Auf der Registerkarte **Überwachte Cluster** finden Sie die folgenden Informationen:

1. Wie viele Cluster sich in einem kritischen oder fehlerhaften Zustand im Vergleich zur Anzahl der Cluster in fehlerfreiem Zustand oder zu denjenigen befinden, die keine Daten übermitteln (was als unbekannter Zustand bezeichnet wird)
2. Sind alle meine Bereitstellungen von [Azure Kubernetes Engine (AKS-engine)](https://github.com/Azure/aks-engine) fehlerfrei?
3. Wie viele Knoten, Benutzer und Systempods sind pro Cluster bereitgestellt?
4. Wie viel Speicherplatz ist auf dem Datenträger verfügbar? Gibt es ein Kapazitätsproblem?

Folgende Integritätsstatus sind enthalten: 

* **Fehlerfrei** – es wurden keine Probleme für die VM erkannt, und sie funktioniert wie erforderlich. 
* **Kritisch** – mindestens ein kritischer Fehler wurde entdeckt, der behandelt werden muss, um den normalen Betriebszustand wiederherzustellen.
* **Warnung** – es wurde mindestens ein Problem entdeckt, das behandelt werden muss, weil sonst der Übergang zu einem kritischen Integritätszustand erfolgen kann.
* **Unbekannt** – wenn der Dienst keine Verbindung mit einem Knoten oder einem Pod herstellen konnte, wechselt der Status zu „Unbekannt“.
* **Nicht gefunden**: Entweder der Arbeitsbereich, die Ressourcengruppe oder das Abonnement mit dem Arbeitsbereich für diese Lösung wurde gelöscht.
* **Nicht autorisiert**: Der Benutzer verfügt nicht über die erforderlichen Berechtigungen, um die Daten im Arbeitsbereich zu lesen.
* **Fehler**: Beim Versuch, Daten aus dem Arbeitsbereich zu lesen, ist ein Fehler aufgetreten.
* **Falsch konfiguriert**: Azure Monitor für Container wurde im angegebenen Arbeitsbereich nicht ordnungsgemäß konfiguriert.
* **Keine Daten**: In den letzten 30 Minuten wurden keine Daten an den Arbeitsbereich gemeldet.

Der Integritätsstatus berechnet den Gesamtstatus eines Clusters als den *schlechtesten* der drei Zustände, mit einer Ausnahme – wenn einer der drei Zustände *unbekannt* ist, ist der Gesamtstatus des Clusters **Unbekannt**.  

Die folgende Tabelle stellt eine Aufschlüsselung der Berechnung dar, die die Integritätszustände für einen überwachten Cluster in der Multi-Cluster-Ansicht steuert.

| |Status |Verfügbarkeit |  
|-------|-------|-----------------|  
|**Benutzerpod**| | |  
| |Healthy |100 % |  
| |Warnung |90–99 % |  
| |Kritisch |<90 % |  
| |Unknown |Wenn keine Meldung in den letzten 30 Minuten erfolgt ist |  
|**Systempod**| | |  
| |Healthy |100 % |
| |Warnung |– |
| |Kritisch |<100 % |
| |Unknown |Wenn keine Meldung in den letzten 30 Minuten erfolgt ist |
|**Node** | | |
| |Healthy |>85 % |
| |Warnung |60–84 % |
| |Kritisch |<60 % |
| |Unknown |Wenn keine Meldung in den letzten 30 Minuten erfolgt ist |

Aus der Liste der Cluster können Sie einen Drilldown zur Seite **Cluster** ausführen, indem Sie auf den Namen eines Clusters klicken, zur Leistungsseite **Knoten**, indem Sie in der Spalte **Knoten** des betreffenden Clusters auf den Rollup der Knoten klicken oder einen Drilldown zur Leistungsseite **Controller** ausführen, indem Sie auf den Rollup in der Spalte **Benutzerpods** oder **Systempods** klicken.   

## <a name="view-performance-directly-from-an-aks-cluster"></a>Direktes Anzeigen der Leistung auf einem AKS-Cluster
Der Zugriff auf Azure Monitor für Container ist direkt auf einem AKS-Cluster möglich, indem Sie im linken Randbereich **Insights** auswählen. Die Darstellung der Informationen zu Ihrem AKS-Cluster ist in vier Perspektiven unterteilt:

- Cluster
- Nodes 
- Controllers  
- Container

Die Standardseite, die beim Klicken auf **Insights** geöffnet wird, ist **Cluster**, und sie umfasst vier Liniendiagramme, die wichtige Leistungsmetriken Ihres Clusters darstellen. 

![Beispielleistungsdiagramme auf der Registerkarte „Cluster“](./media/container-insights-analyze/containers-cluster-perfview.png)

Das Leistungsdiagramm zeigt vier Leistungsmetriken an:

- **Knoten-CPU-Auslastung&nbsp;%**: Eine aggregierte Ansicht der CPU-Auslastung für den gesamten Cluster. Sie können die Ergebnisse für den Zeitbereich filtern, indem Sie **Mittelw.**, **Min.**, **Max.**, **50.**, **90.** und **95.** im Perzentilselektor oberhalb des Diagramms auswählen, entweder einzeln oder kombiniert. 
- **Knotenspeicherauslastung&nbsp;%**: Eine aggregierte Ansicht der Speicherauslastung für den gesamten Cluster. Sie können die Ergebnisse für den Zeitbereich filtern, indem Sie **Mittelw.**, **Min.**, **Max.**, **50.**, **90.** und **95.** im Perzentilselektor oberhalb des Diagramms auswählen, entweder einzeln oder kombiniert. 
- **Knotenanzahl**: Die Anzahl von Knoten und der Status von Kubernetes. Die Status der dargestellten Clusterknoten sind *Alle*, *Bereit* und *Nicht bereit*. Der Status kann im Selektor oberhalb des Diagramms einzeln oder kombiniert gefiltert werden. 
- **Podanzahl der Aktivität**: Die Podanzahl und der Status von Kubernetes. Die Status der dargestellten Pods sind *Alle*, *Ausstehend*, *Wird ausgeführt* und *Unbekannt*. Der Status kann im Selektor oberhalb des Diagramms einzeln oder kombiniert gefiltert werden. 

Mit den Pfeiltasten nach links/rechts können Sie durch jeden Datenpunkt im Diagramm blättern und mit den Pfeiltasten nach oben/unten durch die Perzentilzeilen.

Azure Monitor für Container unterstützt auch den Azure Monitor-[Metrik-Explorer](../platform/metrics-getting-started.md), in dem Sie eigene Boxplotdiagramme erstellen, Trends korrelieren und untersuchen und an Dashboards anheften können. Im Metrik-Explorer können Sie die Kriterien, die Sie für die Visualisierung Ihrer Metriken festgelegt haben, als Grundlage einer [metrikbasierten Warnungsregel](../platform/alerts-metric.md) verwenden.  

## <a name="view-container-metrics-in-metrics-explorer"></a>Anzeigen von Containermetriken im Metrik-Explorer
Im Metrik-Explorer können Sie Metriken für aggregierte Knoten und Podnutzung aus Azure Monitor für Container anzeigen. In der folgenden Tabelle sind die Details zur Verwendung der Metrikdiagramme für die Visualisierung von Containermetriken zusammengefasst.

|Namespace | Metrik |
|----------|--------|
| insights.container/nodes | |
| | cpuUsageMillicores |
| | cpuUsagePercentage |
| | memoryRssBytes |
| | memoryRssPercentage |
| | memoryWorkingSetBytes |
| | memoryWorkingSetPercentage |
| | nodesCount |
| insights.container/pods | |
| | PodCount |

Sie können die [Teilung](../platform/metrics-charts.md#apply-splitting-to-a-chart) einer Metrik anwenden, um sie nach Dimension anzuzeigen und damit zu visualisieren, wie verschiedene Segmente der Metrik zusammenhängen. Für einen Knoten können Sie das Diagramm nach der Dimension *Host* segmentieren und in einem Pod können Sie es nach folgenden Dimensionen segmentieren:

* Controller
* Kubernetes-Namespace
* Knoten
* Phase

## <a name="analyze-nodes-controllers-and-container-health"></a>Analysieren von Knoten, Controllern und der Containerintegrität

Wenn Sie zur Registerkarte **Knoten**, **Controller** und **Container** wechseln, wird der Eigenschaftenbereich automatisch rechts auf der Seite angezeigt. Dort werden die Eigenschaften des ausgewählten Elements einschließlich der Bezeichnungen angezeigt, die Sie definieren, um die Kubernetes-Objekte zu organisieren. Wenn ein Linux-Knoten ausgewählt ist, werden im Abschnitt **Local Disk Capacity** (Kapazität der lokalen Festplatte) auch der verfügbare Speicherplatz auf der Festplatte und der prozentual für jede Festplatte auf dem Knoten genutzte Speicherplatz angezeigt. Klicken Sie auf den **>>**-Link im Bereich, um ihn anzuzeigen\auszublenden. 

![Beispiel für den Eigenschaftenbereich von Kubernetes-Perspektiven](./media/container-insights-analyze/perspectives-preview-pane-01.png)

Wenn Sie die Objekte in der Hierarchie erweitern, wird der Eigenschaftenbereich auf Basis des ausgewählten Objekts aktualisiert. Im Bereich können Sie auch Kubernetes-Ereignisse mit vordefinierten Protokollsuchen anzeigen, indem Sie am oberen Rand des Bereichs auf **Kubernetes-Ereignisprotokolle anzeigen** klicken. Weitere Informationen zum Anzeigen von Kubernetes-Protokolldaten finden Sie unter [Suchen von Protokollen zur Datenanalyse](container-insights-log-search.md). Beim Überprüfen von Cluster-Ressourcen können Sie Protokolle zu Containern und Ereignissen in Echtzeit sehen. Weitere Informationen zu diesem Feature und zu der Konfiguration, die für die Erteilung und Kontrolle des Zugriffs erforderlich ist, finden Sie unter [Anzeigen von Containerprotokollen in Echtzeit mit Azure Monitor für Container](container-insights-live-logs.md). 

Verwenden Sie oben auf der Seite die Option **+ Filter hinzufügen**, um die Ergebnisse für die Ansicht nach **Dienst**, **Knoten**, **Namespace** oder **Knotenpool** zu filtern. Nach der Auswahl des Filterbereichs wählen Sie dann unter den im Feld **Wert(e) auswählen** angezeigten Werten aus.  Nachdem der Filter konfiguriert wurde, wird er global auf alle Perspektiven des AKS-Clusters angewendet.  Die Formel unterstützt nur das Gleichheitszeichen.  Sie können zusätzlich zum obersten weitere Filter hinzufügen, um Ihre Ergebnisse weiter einzugrenzen.  Wenn Sie beispielsweise einen Filter nach **Knoten** festgelegt haben, könnten Sie mit einem zweiten Filter **Dienst** oder **Namespace** auswählen.  

![Beispiel zur Verwendung des Filters zum Eingrenzen der Ergebnisse](./media/container-insights-analyze/add-filter-option-01.png)

Ein auf einer Registerkarte angewendeter Filter bleibt wirksam, wenn Sie zu einer anderen Registerkarte wechseln, und wird durch Klicken auf das **x**-Symbol neben dem angegebenen Filter gelöscht.   

Auf der Registerkarte **Knoten** folgt die Zeilenhierarchie dem Kubernetes-Objektmodell, das mit einem Knoten in Ihrem Cluster beginnt. Erweitern Sie den Knoten, und Sie können mindestens einen Pod ansehen, der auf dem Knoten ausgeführt wird. Wenn mehrere Container zu einem Pod zusammengefasst sind, werden sie als letzte Zeile in der Hierarchie angezeigt. Sie können auch anzeigen, wie viele nicht auf Pods bezogene Workloads auf dem Host ausgeführt werden, falls Prozessor oder Arbeitsspeicher des Hosts überlastet sind.

![Beispiel für eine Kubernetes-Knotenhierarchie in der Leistungsansicht](./media/container-insights-analyze/containers-nodes-view.png)

Windows Server-Container, auf denen das Windows Server 2019-Betriebssystem ausgeführt wird, werden in der Liste nach allen Linux-basierten Knoten angezeigt. Wenn Sie einen Windows Server-Knoten erweitern, können Sie einen oder mehrere Pods und Container anzeigen, die auf dem Knoten ausgeführt werden. Wenn ein Knoten ausgewählt ist, zeigt der Bereich „Eigenschaften“ Versionsinformationen an, ausgenommen Informationen zum Agent, da auf Windows Server-Knoten kein Agent installiert ist.  

![Beispiel-Knotenhierarchie mit aufgeführten Windows Server-Knoten](./media/container-insights-analyze/nodes-view-windows.png) 

Virtuelle Knoten mit Azure Container Instances, die das Linux-Betriebssystem ausführen, werden nach dem letzten AKS-Clusterknoten in der Liste angezeigt.  Wenn Sie einen virtuellen ACI-Knoten erweitern, können Sie eine oder mehrere ACI-Pods und -Container anzeigen, die auf dem Knoten ausgeführt werden.  Metriken werden nicht für Knoten gesammelt und gemeldet, sondern nur für Pods.

![Beispielknotenhierarchie mit aufgelisteten Container Instances](./media/container-insights-analyze/nodes-view-aci.png)

Auf einem erweiterten Knoten können Sie per Drilldown von dem Pod oder Container, der auf dem Knoten ausgeführt wird, zum Controller navigieren, um für diesen Controller gefilterte Leistungsdaten anzuzeigen. Klicken Sie auf den Wert in der Spalte **Controller** für den spezifischen Knoten.   
![Exemplarischer Drilldownvorgang vom Knoten zum Controller in der Leistungsansicht](./media/container-insights-analyze/drill-down-node-controller.png)

Im oberen Bereich der Seite können Sie Controller oder Container auswählen und den Status sowie die Ressourcenauslastung für diese Objekte überprüfen.  Wenn Sie stattdessen die Arbeitsspeicherauslastung überprüfen möchten, wählen Sie aus der Dropdownliste **Metrik** die Option **Arbeitsspeicher RSS** oder **Arbeitssatz für Arbeitsspeicher** aus. **Arbeitsspeicher RSS** wird nur für die Kubernetes-Version 1.8 und höher unterstützt. Andernfalls werden Werte für **Min&nbsp;%** als *NaN&nbsp;%* angezeigt. Dieser numerische Datentypwert stellt einen nicht definierten oder nicht darstellbaren Wert dar. 

![Leistungsansicht zu den Containerknoten](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Standardmäßig beziehen sich Leistungsdaten auf die letzten sechs Stunden, jedoch können Sie den Zeitraum mithilfe der Option **TimeRange** oben links ändern. Außerdem haben Sie die Möglichkeit, die Ergebnisse im Zeitbereich durch Auswahl von **Mittelw.**, **Min.**, **Max.**, **50.**, **90.** und **95.** im Perzentilselektor zu filtern. 

![Perzentilauswahl für die Datenfilterung](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Wenn Sie den Mauszeiger über das Balkendiagramm unter der Spalte **Trend** bewegen, zeigt jeder Balken innerhalb eines Stichprobenzeitraums von 15 Minuten entweder die CPU- oder Speicherauslastung an, je nachdem, welche Metrik ausgewählt ist. Nachdem Sie das Trenddiagramm über eine Tastatur ausgewählt haben, können Sie mit den Tasten Alt+Bild-auf oder Alt+Bild-ab durch jeden Balken einzeln blättern und die gleichen Details wie beim Bewegen mit der Maus erhalten.

![Beispiel für ein Trendbalkendiagramm mit Mauszeigerbewegung](./media/container-insights-analyze/containers-metric-trend-bar-01.png)    

Beachten Sie im nächsten Beispiel, dass für den ersten Knoten in der Liste, *aks-nodepool1-*, der Wert von **Container** 9 ist, was einen Rollup der Gesamtzahl der bereitgestellten Container darstellt.

![Beispiel für ein Rollup der Container pro Knoten](./media/container-insights-analyze/containers-nodes-containerstotal.png)

So können Sie schnell feststellen, ob Sie das richtige Verhältnis zwischen Containern und Knoten im Cluster haben. 

Die folgende Tabelle beschreibt die Informationen, die bei der Anzeige von Knoten erscheinen:

| Column | BESCHREIBUNG | 
|--------|-------------|
| NAME | Der Name des Hosts. |
| Status | Kubernetes-Ansicht des Knotenstatus. |
| Mittelw.&nbsp;%, Min.&nbsp;%, Max.&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Durchschnittlicher Prozentsatz von Knoten basierend auf dem Perzentil für die ausgewählte Dauer. |
| Mittelw., Min., Max., 50., 90. | Tatsächlicher Durchschnittswert der Knoten basierend auf dem Perzentil für den ausgewählten Zeitraum. Der Mittelwert wird anhand des festgelegten CPU-/Arbeitsspeichergrenzwerts für einen Knoten gemessen. Bei Pods und Containern ist dies der vom Host gemeldete Mittelwert. |
| Container | Anzahl von Containern |
| Betriebszeit | Stellt den Zeitraum dar, der seit dem Start oder Neustart eines Knotens verstrichen ist. |
| Controllers | Nur für Container und Pods. Diese Spalte zeigt an, welcher Controller enthalten ist. Nicht alle Pods befinden sind in einem Controller, sodass einige Spalten **N/V** anzeigen. | 
| Trend Mittelw.&nbsp;%, Min.&nbsp;%, Max.&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Balkendiagrammtrend, der die durchschnittliche Perzentilmetrik des Controllers in Prozent anzeigt. |

Wählen Sie im Selektor **Controller** aus.

![Auswählen der Controlleransicht](./media/container-insights-analyze/containers-controllers-tab.png)

Hier können Sie die Leistungsintegrität Ihrer Controller und virtuellen ACI-Knoten oder virtuellen Knotenpods anzeigen, die nicht mit einem Controller verbunden sind.

![Leistungsansicht des Controllers <Name>](./media/container-insights-analyze/containers-controllers-view.png)

Die Zeilenhierarchie beginnt mit einem Controller. Wenn Sie einen Controller erweitern, wird mindestens ein Pod angezeigt.  Wenn Sie einen Pod erweitern, wird in der letzten Zeile der Container angezeigt, der mit dem Pod verknüpft ist. Von einem erweiterten Controller aus können Sie per Drilldown zu dem Knoten navigieren, auf dem er ausgeführt wird, um für diesen Controller gefilterte Leistungsdaten anzuzeigen. ACI-Pods, die nicht mit einem Controller verbunden sind, werden als letztes in der Liste aufgeführt.

![Beispielcontrollerhierarchie mit aufgelisteten Container Instances-Pods](./media/container-insights-analyze/controllers-view-aci.png)

Klicken Sie auf den Wert in der Spalte **Knoten** für den spezifischen Controller.   

![Exemplarischer Drilldownvorgang vom Knoten zum Controller in der Leistungsansicht](./media/container-insights-analyze/drill-down-controller-node.png)

Die folgende Tabelle beschreibt die Informationen, die bei der Anzeige von Controllern erscheinen:

| Column | BESCHREIBUNG | 
|--------|-------------|
| NAME | Der Name des Controllers.|
| Status | Der Rollupstatus der Container, wenn deren Ausführung mit einem Status abgeschlossen wurde, z. B. *OK*, *Abgebrochen*, *Fehler*, *Beendet* oder *Angehalten*. Wenn der Container ausgeführt wird, der Status jedoch entweder nicht richtig angezeigt oder nicht vom Agent übernommen wurde und seit über 30 Minuten nicht antwortet, ist der Status *Unbekannt*. Zusätzliche Details zu dem Symbol „Status“ werden in der folgenden Tabelle angegeben.|
| Mittelw.&nbsp;%, Min.&nbsp;%, Max.&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Durchschnittlicher Rollup des durchschnittlichen Prozentsatzes jeder Entität für die ausgewählte Metrik und das ausgewählte Perzentil. |
| Mittelw., Min., Max., 50., 90.  | Rollup der durchschnittlichen CPU-Millicore oder Speicherleistung des Containers für das ausgewählte Perzentil. Der Mittelwert wird ausgehend vom festgelegten CPU-/Speichergrenzwert für einen Pod gemessen. |
| Container | Gesamtanzahl der Container für den Controller oder Pod. |
| Neustarts | Rollup der Anzahl von Containerneustarts. |
| Betriebszeit | Stellt den Zeitraum dar, der seit dem Start eines Containers verstrichen ist. |
| Knoten | Nur für Container und Pods. Diese Spalte zeigt an, welcher Controller enthalten ist. | 
| Trend Mittelw.&nbsp;%, Min.&nbsp;%, Max.&nbsp;%, 50.&nbsp;%, 90.&nbsp;%| Balkendiagrammtrend, der die durchschnittliche Perzentilmetrik des Controllers anzeigt. |

Die Symbole im Statusfeld zeigen den Onlinestatus von Containern an:
 
| Symbol | Status | 
|--------|-------------|
| ![Statussymbol für bereit und ausgeführt](./media/container-insights-analyze/containers-ready-icon.png) | Wird ausgeführt (bereit)|
| ![Statussymbol für wartend oder angehalten](./media/container-insights-analyze/containers-waiting-icon.png) | Wartend oder angehalten|
| ![Symbol für zuletzt gemeldeten Ausführungsstatus](./media/container-insights-analyze/containers-grey-icon.png) | Zuletzt als ausgeführt gemeldet, hat aber seit mehr als 30 Minuten nicht geantwortet|
| ![Statussymbol für erfolgreiches Anhalten/Stoppen](./media/container-insights-analyze/containers-green-icon.png) | Erfolgreich beendet oder Fehler beim Beenden|

Das Statussymbol zeigt basierend auf dem, was der Pod bereitstellt, eine Anzahl an. Es werden die beiden schlechtesten Status angezeigt. Wenn Sie den Mauszeiger über den Status bewegen, wird ein Rollupstatus aller Pods im Container angezeigt. Wenn der Status „Bereit“ nicht vorliegt, wird der Statuswert **(0)** angezeigt. 

Wählen Sie im Selektor **Container** aus.

![Auswählen der Containeransicht](./media/container-insights-analyze/containers-containers-tab.png)

Hier können Sie die Leistungsintegrität Ihrer Azure Kubernetes- und Azure Container Instances-Container anzeigen.  

![Leistungsansicht des Controllers <Name>](./media/container-insights-analyze/containers-containers-view.png)

Von einem Container aus können Sie per Drilldown zu einem Pod oder Knoten navigieren, um für das entsprechende Objekt gefilterte Leistungsdaten anzuzeigen. Klicken Sie auf den Wert in der Spalte **Pod** oder **Knoten** für den spezifischen Container.   

![Exemplarischer Drilldownvorgang vom Knoten zum Controller in der Leistungsansicht](./media/container-insights-analyze/drill-down-controller-node.png)

Die folgende Tabelle beschreibt die Informationen, die bei der Anzeige von Containern erscheinen:

| Column | BESCHREIBUNG | 
|--------|-------------|
| NAME | Der Name des Controllers.|
| Status | Status der Container, sofern vorhanden. Zusätzliche Informationen zum Statussymbol finden Sie in der folgenden Tabelle.|
| Mittelw.&nbsp;%, Min.&nbsp;%, Max.&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Rollup des durchschnittlichen Prozentsatzes jeder Entität für die ausgewählte Metrik und das ausgewählte Perzentil. |
| Mittelw., Min., Max., 50., 90.  | Rollup der durchschnittlichen CPU-Millicore oder Speicherleistung des Containers für das ausgewählte Perzentil. Der Mittelwert wird ausgehend vom festgelegten CPU-/Speichergrenzwert für einen Pod gemessen. |
| Pod | Container, in dem sich der Pod befindet.| 
| Knoten |  Der Knoten, in dem sich der Container befindet. | 
| Neustarts | Stellt den Zeitraum dar, der seit dem Start eines Containers verstrichen ist. |
| Betriebszeit | Stellt den Zeitraum dar, der seit dem Start oder Neustart eines Containers verstrichen ist. |
| Trend Mittelw.&nbsp;%, Min.&nbsp;%, Max.&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Balkendiagrammtrend, der die durchschnittliche Perzentilmetrik des Containers in Prozent anzeigt. |

Die Symbole im Statusfeld zeigen die Onlinestatus der Pods an, wie in der folgenden Tabelle beschrieben:
 
| Symbol | Status |  
|--------|-------------|  
| ![Statussymbol für bereit und ausgeführt](./media/container-insights-analyze/containers-ready-icon.png) | Wird ausgeführt (bereit)|  
| ![Statussymbol für wartend oder angehalten](./media/container-insights-analyze/containers-waiting-icon.png) | Wartend oder angehalten|  
| ![Symbol für zuletzt gemeldeten Ausführungsstatus](./media/container-insights-analyze/containers-grey-icon.png) | Zuletzt als ausgeführt gemeldet, hat aber seit mehr als 30 Minuten nicht geantwortet|  
| ![Statussymbol für „Beendet“](./media/container-insights-analyze/containers-terminated-icon.png) | Erfolgreich beendet oder Fehler beim Beenden|  
| ![Symbol für den Status „Fehler“](./media/container-insights-analyze/containers-failed-icon.png) | Status „Fehler“ |  

## <a name="next-steps"></a>Nächste Schritte
- Informationen zum Erstellen von Warnungen für hohe CPU- und Arbeitsspeicherauslastung zur Unterstützung Ihrer DevOps oder Betriebsprozesse und -verfahren finden Sie unter [Erstellen von Leistungswarnungen mit Azure Monitor für Container](container-insights-alerts.md). 
- Sehen Sie sich die [Beispiele zu Protokollabfragen](container-insights-log-search.md#search-logs-to-analyze-data) an, die auch vordefinierte Abfragen enthalten. Mit diesen Materialien können Sie Auswertungen von bzw. Anpassungen für Warnungen, Visualisierungen und Analysen von Clustern vornehmen.
