---
title: Erkenntnisse für Azure Load Balancer
description: Nutzen Sie die Erkenntnisse zum Lastenausgleich, um Fehlerquellen schneller zu finden und fundierte Entwurfsentscheidungen zu treffen.
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: errobin
ms.openlocfilehash: 63b91194c9ffb10fd8f4c5f1341eaf74bc81f5e1
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694880"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>Verwenden von Erkenntnissen zum Überwachen und Konfigurieren Ihrer Azure Load Balancer-Instanz

Azure Monitor für Netzwerke bietet Visualisierungen der funktionalen Abhängigkeiten und vorkonfigurierte Metrikdashboards für Ihre Load Balancer-Instanzen. Diese visuellen Elemente helfen Ihnen, fundierte Entwurfsentscheidungen zu treffen und Fehler schnell zu lokalisieren, zu diagnostizieren und zu beheben.

>[!NOTE] 
>Beachten Sie, dass sich dieses Feature in der Vorschauphase befindet und die Ansicht der funktionalen Abhängigkeiten sowie das vorkonfigurierte Dashboard noch geändert werden können, um das Feature zu verbessern.

>[!IMPORTANT]
>Load Balancer Standard ist erforderlich, um Metriken aus dem Load Balancer-Namespace im vorkonfigurierten Metrikdashboard anzuzeigen. Sie können zwar weiterhin Metriken der VM, der VM-Skalierungsgruppe und der Verbindungsmonitor-Namespaces anzeigen, es wird jedoch empfohlen, für alle Produktionsworkloads ein [Upgrade auf Standard](./upgrade-basic-standard.md) durchführen, um die umfangreichen Load Balancer-Metriken verwenden zu können.

## <a name="functional-dependency-view"></a>Ansicht der funktionalen Abhängigkeiten

Mithilfe der Ansicht der funktionalen Abhängigkeiten können Sie auch die komplexesten Load Balancer-Setups anzeigen. Dank des visuellen Feedbacks zu Ihrer aktuellen Load Balancer-Konfiguration können Sie Aktualisierungen vornehmen, ohne Ihre Konfiguration aus dem Blick zu verlieren.

Der Zugriff auf diese Ansicht erfolgt über das Blatt „Erkenntnisse“ Ihrer Load Balancer-Ressource in Azure.

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="Abbildung der Ansicht der funktionalen Abhängigkeiten. Das Front-End der Load Balancer-Instanz stellt mithilfe der konfigurierten Regeln eine Verbindung mit den Mitgliedern im Back-End-Pool her. Für Load Balancer Standard sind die Linien von den Lastenausgleichsregeln zu den Instanzen im Back-End-Pool basierend auf dem Integritätsteststatus farblich gekennzeichnet." border="true":::

Für Load Balancer Standard werden Ihre Back-End-Pool-Ressourcen gemäß dem Integritätsteststatus farblich codiert, um die aktuelle Verfügbarkeit Ihres Back-End-Pools für den Datenverkehr anzuzeigen. Neben der obigen Topologie wird ein Graph mit dem zeitlichen Verlauf des Integritätsstatus angezeigt, der eine Momentaufnahmeansicht der Integrität Ihrer Anwendung bietet.

## <a name="metrics-dashboard"></a>Metrikdashboard

Sie können auf dem Blatt „Erkenntnisse“ Ihrer Load Balancer-Instanz ausführlichere Metriken auswählen, um eine vorkonfigurierte [Azure Monitor-Arbeitsmappe](../azure-monitor/platform/workbooks-overview.md) mit visuellen Elementen zu Metriken anzuzeigen, die für bestimmte Aspekte Ihrer Load Balancer-Instanz wichtig sind. Ganz oben auf diesem Dashboard werden der Load Balancer-Status und Links zu relevanter Dokumentation angezeigt.

Als Erstes wird die Registerkarte „Übersicht“ angezeigt. Sie können durch die verfügbaren Registerkarten navigieren, die jeweils visuelle Elemente zu einem bestimmten Aspekt Ihrer Load Balancer-Instanz enthalten. Unten auf jeder Registerkarte des Dashboards finden Sie passende Anleitungen.

Derzeit sind die folgenden Registerkarten auf dem Dashboard verfügbar:
* Übersicht
* Front-End- und Back-End-Verfügbarkeit
* Datendurchsatz
* Flowverteilung
* Verbindungsmonitore
* Metrikdefinitionen 

### <a name="overview-tab"></a>Registerkarte „Übersicht“
Die Registerkarte „Übersicht“ enthält ein durchsuchbares Raster mit der allgemeinen Datenpfadverfügbarkeit und dem Integritätsteststatus zu jeder Front-End-IP-Adresse, die an Ihre Load Balancer-Instanz angefügt ist. Diese Metriken geben an, ob die Front-End-IP-Adresse antwortet und ob die Compute-Instanzen in Ihrem Back-End-Pool einzeln auf eingehende Verbindungen reagieren.

Sie können auf dieser Seite auch den allgemeinen Datendurchsatz für jede Front-End-IP-Adresse anzeigen, um einen Eindruck davon zu erhalten, ob der ein- und ausgehende Datenverkehr den Erwartungen entspricht. Falls Sie Unregelmäßigkeiten bei den Werten entdecken, gelangen Sie über die Anleitung unten auf der Seite zur entsprechenden Registerkarte.

### <a name="frontend-and-backend-availability-tab"></a>Registerkarten „Front-End-Verfügbarkeit“ und „Back-End-Verfügbarkeit“
Auf den Registerkarten „Front-End-Verfügbarkeit“ und „Back-End-Verfügbarkeit“ werden Metriken zum Datenpfaddurchsatz und zum Integritätsteststatus in einigen hilfreichen Ansichten angezeigt. Der erste Graph zeigt den Aggregatwert, über den Sie feststellen können, ob ein Problem vorliegt. In den restlichen Graphen werden diese Metriken auf verschiedene Dimensionen aufgeteilt, sodass Sie die Ursachen aller Probleme bei der Eingangsverfügbarkeit identifizieren und beheben können.

Einen Workflow zum Anzeigen dieser Graphen finden Sie unten auf der Seite zusammen mit häufigen Ursachen für verschiedene Symptome. 

### <a name="data-throughput-tab"></a>Registerkarte „Datendurchsatz“
Auf der Registerkarte „Datendurchsatz“ können Sie den gesamten Durchsatz für ein- und ausgehende Daten überprüfen, um zu ermitteln, ob die Datenverkehrsmuster den Erwartungen entsprechen. Sie zeigt den Durchsatz für ein- und ausgehende Daten durch eine Aufteilung nach Front-End-IP-Adresse und Front-End-Port an. Auf diese Weise können Sie ermitteln, ob und wie die ausgeführten Dienste jeweils funktionieren.

### <a name="flow-distribution"></a>Flowverteilung
Mithilfe der Registerkarte „Flowverteilung“ können Sie die Anzahl der Flows visualisieren und verwalten, die von Ihren Back-End-Instanzen empfangen und generiert werden. Sie zeigt die Erstellungsrate und die Anzahl der Flows für ein- und ausgehenden Datenverkehr sowie den Netzwerkdatenverkehr, den die einzelnen virtuellen Computer und Instanzen von VM-Skalierungsgruppen empfangen. 

Diese Ansichten können Ihnen Aufschluss darüber geben, ob Ihre Load Balancer-Konfiguration oder die Datenverkehrsmuster zu einem unausgeglichenen Datenverkehr führen. Angenommen beispielsweise, Sie haben Sitzungsaffinität konfiguriert, aber ein einzelner Client führt eine unverhältnismäßig hohe Anzahl von Anforderungen durch. In diesem Fall werden Sie informiert, wenn Sie den [Grenzwert pro virtuellem Computer](../virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) für Ihre Computergröße erreichen.

### <a name="connection-monitors"></a>Verbindungsmonitore
Auf der Registerkarte „Verbindungsmonitore“ wird die Roundtriplatenz auf einer globalen Karte für alle [Verbindungsmonitore](../network-watcher/connection-monitor.md) angezeigt, die Sie konfiguriert haben. Diese visuellen Elemente bieten nützliche Informationen für Dienste mit strengen Latenzanforderungen. Damit Sie Ihre Anforderungen erfüllen können, müssen Sie möglicherweise zusätzliche regionale Bereitstellungen hinzufügen oder auf ein [regionsübergreifendes Lastenausgleichsmodell](./cross-region-overview.md) umstellen.

### <a name="metric-definitions"></a>Metrikdefinitionen
Die Registerkarte „Metrikdefinitionen“ enthält alle im Artikel [Mehrdimensionale Metriken](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics) aufgeführten Informationen.

## <a name="next-steps"></a>Nächste Schritte
* Sehen Sie sich das Dashboard an, und geben Sie über den unten stehenden Link Feedback, wenn Sie Verbesserungsvorschläge haben.
* [Lesen Sie die Dokumentation zu Metriken, um zu verstehen, wie die einzelnen Metriken berechnet werden.](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)
* [Erstellen Sie Verbindungsmonitore für Ihre Load Balancer-Instanzen.](../network-watcher/connection-monitor.md)
* [Erstellen Sie Ihre eigenen Arbeitsmappen.](../azure-monitor/platform/workbooks-overview.md) Anregungen erhalten Sie durch Klicken auf die Schaltfläche „Bearbeiten“ auf Ihrem detaillierten Metrikdashboard.