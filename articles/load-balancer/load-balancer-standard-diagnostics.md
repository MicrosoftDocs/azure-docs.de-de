---
title: Diagnose mit Metriken, Warnungen und Ressourcenintegrität – Azure Load Balancer Standard
description: Verwenden Sie die verfügbaren Informationen zu Metriken, Warnungen und Ressourcenintegrität für die Diagnose Ihres Azure Load Balancer Standard.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: allensu
ms.openlocfilehash: 9c322620e1d66182937be41bb02d48fd1469f459
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697559"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Load Balancer Standard-Diagnose mit Metriken, Warnungen und Ressourcenintegrität

Azure Load Balancer Standard bietet die folgenden Diagnosefunktionen:

* **Mehrdimensionale Metriken und Warnungen**: Stellt mehrdimensionale Diagnosefunktionen über [Azure Monitor](../azure-monitor/overview.md) für Load Balancer Standard-Konfigurationen bereit. Sie können Ihre Load Balancer Standard-Ressourcen überwachen, verwalten und hinsichtlich Fehlern behandeln.

* **Ressourcenintegrität**: Der Ressourcenintegritätsstatus Ihrer Load Balancer-Instanz ist auf der Seite „Ressourcenintegrität“ unter „Monitor“ verfügbar. Diese automatische Überprüfung informiert Sie über die aktuelle Verfügbarkeit Ihrer Load Balancer-Ressource.

Dieser Artikel enthält einen kurzen Überblick über diese Funktionen und zeigt Möglichkeiten auf, wie diese für Standard Load Balancer verwendet werden können. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>Mehrdimensionale Metriken

Azure Load Balancer stellt mehrdimensionale Metriken über die Azure-Metriken im Azure-Portal zur Verfügung und ermöglicht es Ihnen, in Echtzeit diagnostische Einblicke in Ihre Lastenausgleichsressourcen zu erhalten. 

Die verschiedenen Standard Load Balancer-Konfigurationen bieten die folgenden Metriken:

| Metrik | Ressourcentyp | BESCHREIBUNG | Empfohlene Aggregation |
| --- | --- | --- | --- |
| Datenpfadverfügbarkeit | Öffentlicher und interner Load Blancer | Standard Load Balancer wendet kontinuierlich den Datenpfad aus einer Region auf das Load Balancer-Front-End bis hin zum SDN-Stapel an, der Ihren virtuellen Computer unterstützt. Solange integre Instanzen verbleiben, folgt die Messung demselben Pfad wie der Datenverkehr mit Lastenausgleich Ihrer Anwendungen. Der Datenpfad, der von Ihren Kunden verwendet wird, wird ebenfalls überprüft. Die Messung ist für Ihre Anwendung nicht sichtbar und bewirkt keine Beeinträchtigung bei anderen Vorgängen.| Average |
| Integritätsteststatus | Öffentlicher und interner Load Blancer | Standard Load Balancer verwendet einen verteilten Integritätsprüfungsdienst, der die Integrität Ihres Anwendungsendpunkts gemäß Ihren Konfigurationseinstellungen überwacht. Diese Metrik stellt eine Aggregat- oder nach Endpunkt gefilterte Ansicht jedes Instanzendpunkts im Load Balancer-Pool bereit. Sie können sehen, wie Load Balancer die Integrität Ihrer Anwendung gemäß Ihrer Integritätsprüfungskonfiguration beurteilt. |  Average |
| SYN-Pakete (Synchronisierung) | Öffentlicher und interner Load Blancer | Standard Load Balancer beendet keine TCP-Verbindungen (Transmission Control Protocol) und interagiert nicht mit TCP- oder UDP-Paketdatenflüssen. Datenflüsse und deren Handshakes erfolgen immer zwischen der Quelle und der VM-Instanz. Für eine bessere Problembehandlung Ihrer TCP-Protokollszenarien können Sie SYN-Paketzähler verwenden, um zu verstehen, wie viele TCP-Verbindungsversuche vorgenommen werden. Die Metrik gibt die Anzahl der TCP-SYN-Pakete an, die empfangen wurden.| Average |
| SNAT-Verbindungen | Öffentlicher Load Balancer |Standard Load Balancer meldet die Anzahl von maskierten ausgehenden Datenflüssen an das Front-End mit der öffentlichen IP-Adresse. SNAT-Ports (Source Network Address Translation) stellen eine erschöpfbare Ressource dar. Diese Metrik kann einen Hinweis darauf geben, in welchem Umfang Ihre Anwendung SNAT für ausgehende Flows nutzt. Die Zählerstände für erfolgreiche und fehlgeschlagene ausgehende SNAT-Flows werden gemeldet und können zur Problembehebung und Analyse Ihrer ausgehenden Flows verwendet werden.| Average |
| Zugeordnete SNAT-Ports | Öffentlicher Load Balancer | Load Balancer Standard meldet die Anzahl der pro Back-End-Instanz zugeordneten SNAT-Ports. | Average. |
| Verwendete SNAT-Ports | Öffentlicher Load Balancer | Load Balancer Standard meldet die Anzahl der pro Back-End-Instanz genutzten SNAT-Ports. | Average | 
| Byteleistungsindikatoren |  Öffentlicher und interner Load Blancer | Standard Load Balancer meldet die pro Front-End verarbeiteten Daten. Sie bemerken möglicherweise, dass die Bytes nicht gleichmäßig auf die Back-End-Instanzen verteilt sind. Dies ist zu erwarten, da der Azure Load Balancer-Algorithmus auf Flows basiert. | Average |
| Paketleistungsindikatoren |  Öffentlicher und interner Load Blancer | Standard Load Balancer meldet die pro Front-End verarbeiteten Pakete.| Average |

  >[!NOTE]
  >Bei der Verwendung der Verteilung von Datenverkehr von einem internen Lastenausgleichsmodul über eine NVA oder Firewall, sind die Metriken „Synchronisierungspaket“, „Bytezähler“ und „Paketzähler“ nicht verfügbar und werden als Nullwerte angezeigt. 
  
### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Anzeigen Ihrer Load Balancer-Metriken im Azure-Portal

Das Azure-Portal macht die Load Balancer Metriken über die Seite „Metriken“ verfügbar, die sowohl auf der Seite „Load Balancer-Ressource“ für eine bestimmte Ressource als auch auf der Seite „Azure Monitor“ verfügbar ist. 

So zeigen Sie die Metriken für Ihre Standard Load Balancer-Ressourcen an
1. Wechseln Sie zur Seite „Metriken“, und führen Sie einen der folgenden Schritte aus:
   * Wählen Sie auf der Seite „Load Balancer-Ressource“ den Metriktyp in der Dropdownliste aus.
   * Wählen Sie auf der Seite „Azure Monitor“ die Load Balancer-Ressource aus.
2. Legen Sie den entsprechenden Metrikaggregationstyp fest.
3. Konfigurieren Sie optional die erforderliche Filterung und Gruppierung.
4. Konfigurieren Sie optional den Zeitbereich und die Aggregation. Standardmäßig wird die Uhrzeit in UTC (koordinierte Weltzeit) angezeigt.

  >[!NOTE] 
  >Die Zeitaggregation ist bei der Interpretation bestimmter Metriken wichtig, da die Daten einmal pro Minute erfasst werden. Wenn die Zeitaggregation auf fünf Minuten eingestellt ist und der Metrikaggregationstyp „Summe“ für Metriken wie die SNAT-Zuordnung verwendet wird, zeigt Ihr Diagramm das Fünffache der insgesamt zugeordneten SNAT-Ports an. 

![Metriken für Standard Load Balancer](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*Abbildung: Datenpfadverfügbarkeit-Metrik für Standard Load Balancer*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Programmgesteuertes Abrufen von mehrdimensionalen Metriken über APIs

Eine API-Anleitung zum Abrufen von Definitionen und Werten für multidimensionale Metriken finden Sie unter [Exemplarische Vorgehensweise für die Azure Monitor-REST-API](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-definitions-multi-dimensional-api). Diese Metriken können nur über die Option „Alle Metriken“ in ein Speicherkonto geschrieben werden. 

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>Konfigurieren von Warnungen für mehrdimensionale Metriken ###

Azure Load Balancer Standard unterstützt leicht konfigurierbare Warnungen für mehrdimensionale Metriken. Konfigurieren Sie benutzerdefinierte Schwellenwerte für bestimmte Metriken, um Warnungen mit unterschiedlichen Schweregraden auszulösen und so eine berührungslose Ressourcenüberwachung zu ermöglichen.

So konfigurieren Sie Warnungen:
1. Wechseln Sie zum Unterblatt „Warnung“ für den Lastenausgleich.
1. Erstellen einer neuen Warnungsregel
    1.  Konfigurieren der Warnungsbedingung
    1.  (Optional) Hinzufügen einer Aktionsgruppe für automatisierte Reparatur
    1.  Zuweisen von Schweregrad, Name und Beschreibung der Warnung, die eine intuitive Reaktion ermöglichen

  >[!NOTE]
  >Im Konfigurationsfenster für die Warnungsbedingung werden Zeitreihen für den Signalverlauf angezeigt. Es gibt eine Option zum Filtern dieser Zeitreihen nach Dimensionen, wie z. B. „Back-End-IP“. Dadurch wird das Zeitreihendiagramm gefiltert, aber **nicht** die Warnung selbst. Sie können keine Warnungen für bestimmte Back-End-IP-Adressen konfigurieren.

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>Allgemeine Diagnoseszenarien und empfohlene Ansichten

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-frontend"></a>Ist der Datenpfad aktiv und für mein Load Balancer-Front-End verfügbar?
<details><summary>Expand</summary>

Die Metrik der Datenpfadverfügbarkeit beschreibt die Integrität des regionsinternen Datenpfads zu dem Computehost, auf dem sich Ihre VMs befinden. Die Metrik reflektiert die Integrität der Azure-Infrastruktur. Anhand der Metrik können Sie:
- Die externe Verfügbarkeit Ihres Diensts überwachen
- Tiefer einsteigen und ermitteln, ob die Plattform, auf der Ihr Dienst bereitgestellt wird, fehlerfrei ist, oder ob Ihr Gastbetriebssystem oder die Anwendungsinstanz fehlerfrei ist.
- Bestimmen, ob sich ein Ereignis auf Ihren Dienst oder die zugrunde liegende Datenebene bezieht. Verwechseln Sie diese Metrik nicht mit dem Integritätsteststatus („Back-End-Instanzverfügbarkeit“).

So rufen Sie die Datenpfadverfügbarkeit für Ihre Standard Load Balancer-Ressourcen ab
1. Vergewissern Sie sich, dass die richtige Load Balancer-Ressource ausgewählt ist. 
2. Wählen Sie in der Dropdownliste **Metrik** die Option **Datenpfadverfügbarkeit** aus. 
3. Wählen in der Dropdownliste **Aggregation** die Option **Mittelwert** aus. 
4. Fügen Sie außerdem einen Filter für die Front-End-IP-Adresse oder den Front-End-Port als die Dimension mit der erforderlichen Front-End-IP-Adresse oder dem erforderlichen Front-End-Port hinzu, und gruppieren Sie diese dann nach der ausgewählten Dimension.

![VIP-Sondierung](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Abbildung: Front-End-Sondierungsdetails für Load Balancer*

Die Metrik wird durch eine aktive bandinterne Messung generiert. Ein Sondierungsdienst innerhalb der Region löst Datenverkehr für diese Messung aus. Der Dienst wird aktiviert, sobald Sie eine Bereitstellung mit einem öffentliche Front-End erstellen, und wird weiter ausgeführt, bis Sie das Front-End entfernen. 

Ein Paket, das dem Front-End und der Regel Ihrer Bereitstellung entspricht, wird regelmäßig generiert. Es durchläuft die Region von der Quelle bis zu dem Host, auf dem sich ein virtueller Computer im Back-End-Pool befindet. Die Load Balancer-Infrastruktur führt dieselben Lastenausgleichs- und Übersetzungsvorgänge aus, wie sie dies für jeden anderen Datenverkehr tut. Diese Sondierung erfolgt bandintern auf Ihrem Endpunkt mit Lastenausgleich. Sobald der Test (Sondierung) auf dem Computehost eingeht, auf dem sich ein fehlerfreier virtueller Computer im Back-End-Pool befindet, generiert der Computehost eine Antwort an den Sondierungsdienst. Ihr virtueller Computer „sieht“ diesen Datenverkehr nicht.

Fehler bei der Datenpfadverfügbarkeit aus folgenden Gründen:
- Ihre Bereitstellung hat keine fehlerfreien virtuellen Computer, die im Back-End-Pool verblieben sind. 
- Es ist ein Infrastrukturausfall aufgetreten.

Zu Diagnosezwecken können Sie die [Datenpfadverfügbarkeit-Metrik zusammen mit dem Integritätsteststatus](#vipavailabilityandhealthprobes) verwenden.

Verwenden Sie **Mittelwert** als Aggregation für die meisten Szenarien.
</details>

#### <a name="are-the-backend-instances-for-my-load-balancer-responding-to-probes"></a>Reagieren die Back-End-Instanzen für meinen Load Balancer auf Tests?
<details>
  <summary>Expand</summary>
Die Integritätsteststatus-Metrik beschreibt die Integrität Ihrer Anwendungsbereitstellung, wie Sie diese konfiguriert haben, als Sie den Integritätstest Ihres Load Balancers konfiguriert haben. Der Load Balancer verwendet den Status des Integritätstests, um zu bestimmen, wohin neue Datenflüsse gesendet werden sollen. Integritätstests stammen aus einer Azure-Infrastrukturadresse und können im Gastbetriebssystem des virtuellen Computers angezeigt werden.

So rufen Sie den Integritätsteststatus für Ihre Standard Load Balancer-Ressourcen ab
1. Wählen Sie die Metrik **Integritätsteststatus** mit dem Aggregationstyp **Mittelwert** aus. 
2. Wenden Sie einen Filter auf die erforderliche Front-End-IP-Adresse oder den erforderlichen Front-End-Port (oder auf beides) an.

Integritätstests schlagen aus den folgenden Gründen fehl:
- Sie konfigurieren einen Integritätstest für einen Port, der nicht lauscht oder nicht reagiert oder das falsche Protokoll verwendet. Wenn für Ihren Dienst DSR-Regeln (Direct Server Return oder Floating IP-Regeln) verwendet werden, vergewissern Sie sich, dass der Dienst auf die IP-Adresse der IP-Konfiguration der Netzwerkkarte und nicht nur auf den Loopback lauscht, der mit der Front-End-IP-Adresse konfiguriert ist.
- Ihr Test wird von der Netzwerksicherheitsgruppe, der Firewall des Gastbetriebssystems des virtuellen Computers oder den Anwendungsschichtfilter nicht zugelassen.

Verwenden Sie **Mittelwert** als Aggregation für die meisten Szenarien.
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Wie überprüfe ich meine Statistiken für ausgehende Verbindungen? 
<details>
  <summary>Expand</summary>
Die SNAT-Verbindungen-Metrik beschreibt das Volumen erfolgreicher und fehlgeschlagener Verbindungen für [ausgehende Datenflüsse](./load-balancer-outbound-connections.md).

Ein Volumen für fehlgeschlagene Verbindungen, das größer als 0 (null) ist, kennzeichnet eine SNAT-Portüberlastung. Sie müssen weitere Untersuchungen vornehmen, um festzustellen, wodurch diese Fehler verursacht werden können. Eine SNAT-Portüberlastung zeigt sich dadurch, dass kein [ausgehender Datenfluss](./load-balancer-outbound-connections.md) eingerichtet werden kann. Lesen Sie den Artikel über ausgehende Verbindungen, um die Szenarien und greifenden Mechanismen zu verstehen und Informationen über die Maßnahmen für Abmildern und Entwurf zu erhalten, um so eine SNAT-Portüberlastung zu verhindern. 

So rufen Sie SNAT-Verbindungsstatistiken ab
1. Wählen Sie den Metriktyp **SNAT-Verbindungen** und **Summe** als Aggregation aus. 
2. Gruppieren Sie nach dem **Verbindungsstatus** für die Anzahl erfolgreicher und die Anzahl fehlgeschlagener SNAT-Verbindungen, die durch unterschiedliche Linien dargestellt werden. 

![SNAT-Verbindung](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Abbildung: Anzahl von SNAT-Verbindungen des Load Balancers*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>Wie überprüfe ich die Verwendung und Zuordnung meines SNAT-Ports?
<details>
  <summary>Expand</summary>
Die Metrik „Verwendete SNAT-Ports“ verfolgt nach, wie viele SNAT-Ports für ausgehende Flows genutzt werden. Diese Metrik gibt an, wie viele eindeutige Flows zwischen einer Internetquelle und einer Back-End-VM oder einer VM-Skalierungsgruppe eingerichtet werden, die sich hinter einem Lastenausgleichsmodul befindet und keine öffentliche IP-Adresse besitzt. Durch Vergleich der Anzahl von verwendeten SNAT-Ports mit der Metrik der zugeordneten SNAT-Ports können Sie feststellen, ob eine SNAT-Überlastung (oder ein entsprechendes Risiko) für Ihren Dienst besteht, die zu Fehlern bei ausgehenden Flows führen kann. 

Wenn Ihre Metriken auf das Risiko eines [ausgehenden Flowfehlers](./load-balancer-outbound-connections.md) hinweisen, verweisen Sie auf den Artikel und unternehmen Sie Schritte, um dies zu minimieren, um die Dienstintegrität sicherzustellen.

So zeigen Sie die Verwendung und Zuordnung von SNAT-Ports an
1. Legen Sie die Zeitaggregation des Diagramms auf eine Minute fest, um sicherzustellen, dass die gewünschten Daten angezeigt werden.
1. Wählen Sie **Verwendete SNAT-Ports** und/oder **Zugeordnete SNAT-Ports** als Metriktyp und **Durchschnitt** als Aggregation aus.
    * Standardmäßig stellen diese Metriken die durchschnittliche Anzahl der SNAT-Ports dar, die den einzelnen Back-End-VM- oder VMSS-Instanzen zugewiesen sind oder von diesen verwendet werden, entsprechend aller öffentlichen Front-End-IP-Adressen, die der Load Balancer-Instanz zugeordnet sind, aggregiert über TCP und UDP.
    * Verwenden Sie zum Anzeigen der gesamten SNAT-Ports, die von der Load Balancer-Instanz verwendet oder ihr zugeordnet wurden, die Metrikaggregation **Summe**.
1. Filtern Sie nach einem bestimmten **Protokolltyp**, einer Reihe von **Back-End-IP-Adressen** und/oder **Front-End-IP-Adressen**.
1. Um die Integrität pro Back-End- oder Front-End-Instanz zu überwachen, wenden Sie die Teilung an. 
    * Durch die Teilung wird nur eine einzelne Metrik gleichzeitig angezeigt. 
1. So können Sie z. B. die SNAT-Nutzung für TCP-Flows pro Computer überwachen, indem Sie nach **Durchschnitt** aggregieren, nach **Back-End-IP-Adressen** teilen und nach **Protokolltyp** filtern. 

![SNAT-Zuordnung und -Verwendung](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*Abbildung: Durchschnittliche TCP-SNAT-Portzuordnung und -verwendung für eine Reihe von Back-End-VMs*

![SNAT-Verwendung nach Back-End-Instanz](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*Abbildung: TCP SNAT-Portverwendung pro Back-End-Instanz*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Wie überprüfe ich Versuche für eingehende/ausgehende Verbindungen für meinen Dienst?
<details>
  <summary>Expand</summary>
Eine SYN-Pakete-Metrik beschreibt das Volumen der TCP-SYN-Pakete, die angekommen sind oder gesendet wurden (für [ausgehende Datenflüsse](./load-balancer-outbound-connections.md)) und einem bestimmten Front-End zugeordnet sind. Mit dieser Metrik können TCP-Verbindungsversuche für Ihren Dienst ausgewertet werden.

Verwenden Sie **Gesamt** als Aggregation für die meisten Szenarien.

![SYN-Verbindung](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Abbildung: SYN-Anzahl des Load Balancers*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Wie überprüfe ich meine Netzwerkbandbreitennutzung? 
<details>
  <summary>Expand</summary>
Die Byte- und Paketleistungsindikatoren-Metrik beschreibt das Volumen der Bytes und Pakete, die jeweils pro Front-End von Ihrem Dienst gesendet oder empfangen wurden.

Verwenden Sie **Gesamt** als Aggregation für die meisten Szenarien.

So rufen Sie eine Byteanzahl- oder Paketzahlstatistik ab
1. Wählen Sie die Metriktypen **Byteanzahl** und/oder **Paketzahl** mit **Mittelwert** als Aggregation aus. 
2. Führen Sie einen der folgenden Schritte aus:
   * Wenden Sie einen Filter auf eine bestimmte Front-End-IP, einen Front-End-Port, eine Back-End-IP oder einen Back-End-Port an.
   * Rufen Sie die Gesamtstatistik für Ihre Load Balancer-Ressource ohne jegliche Filterung ab.

![Byteanzahl](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Abbildung: Byteanzahl des Load Balancers*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>Wie führe ich eine Diagnose meiner Load Balancer-Bereitstellung durch?
<details>
  <summary>Expand</summary>
Anhand einer Kombination der Metriken „Datenpfadverfügbarkeit“ und „Integritätsteststatus“ in einem einzigen Diagramm können Sie erkennen, wo das Problem zu suchen ist und wie es sich beheben lässt. Sie können sich davon überzeugen, dass Azure ordnungsgemäß funktioniert, und auf dieser Grundlage eindeutig feststellen, dass die Konfiguration oder die Anwendung die Hauptursache ist.

Sie können Integritätstestmetriken verwenden, um zu erkennen, wie Azure die Integrität Ihrer Bereitstellung entsprechend der Konfiguration beurteilt, die Sie bereitgestellt haben. Ein Anzeigen der Integritätstests ist immer ein hervorragender erster Schritt beim Überwachen oder Ermitteln einer Ursache.

Sie können einen Schritt weiter gehen und Datenpfadverfügbarkeitsmetriken verwenden, um zu erfahren, wie Azure die Integrität der zugrunde liegenden Datenebene beurteilt, die für Ihre spezielle Bereitstellung zuständig ist. Wenn Sie beide Metriken kombinieren, können Sie, wie in diesem Beispiel veranschaulicht, gezielt feststellen, wo sich der Fehler befindet:

![Kombinieren der Metriken „Datenpfadverfügbarkeit“ und „Integritätsteststatus“](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Abbildung: Kombinieren der Metriken „Datenpfadverfügbarkeit“ und „Integritätsteststatus“*

Im Diagramm werden die folgenden Informationen angezeigt:
- Die Infrastruktur, in der Ihre virtuellen Computer gehostet werden, war am Anfang des Diagramms nicht verfügbar und lag bei 0 Prozent. Später war die Infrastruktur fehlerfrei, die virtuellen Computer waren erreichbar, und im Back-End waren mehrere virtuelle Computer platziert. Diese Informationen sind durch die blaue Ablaufverfolgung für Datenpfadverfügbarkeit gekennzeichnet, die später bei 100 Prozent lag. 
- Der Integritätsteststatus, der durch die violette Ablaufverfolgung gekennzeichnet ist, liegt jedoch am Anfang des Diagramms bei 0 Prozent. Der eingekreiste Bereich in Grün markiert, ab wann der Integritätsteststatus fehlerfrei wurde und an welchem Punkt die Bereitstellung des Kunden neue Datenflüsse akzeptieren konnte.

Anhand des Diagramms kann der Kunde eigenständig eine Fehlerbehebung für die Bereitstellung vornehmen, ohne zu erraten oder beim Support zu erfragen, ob andere Probleme auftreten. Der Dienst war nicht verfügbar, weil Integritätstests wegen einer Fehlkonfiguration oder einer fehlerhaften Anwendung fehlgeschlagen sind.
</details>

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Ressourcenintegritätsstatus

Der Integritätsstatus für die Standard Load Balancer-Ressourcen wird über die vorhandene **Ressourcenintegrität** unter **Monitor > Service Health** verfügbar gemacht.

So zeigen Sie die Integrität Ihrer öffentlichen Standard Load Balancer-Ressourcen an
1. Wählen Sie **Monitor** > **Dienstintegrität** aus.

   ![Seite „Monitor“](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Abbildung: Link „Dienstintegrität“ in Azure Monitor*

2. Wählen Sie **Dienstintegrität** aus, und stellen Sie dann sicher, dass **Abonnement-ID** und **Ressourcentyp = Load Balancer** ausgewählt sind.

   ![Ressourcenintegritätsstatus](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Abbildung: Ressource für Integritätsanzeige auswählen*

3. Wählen Sie in der Liste die Load Balancer-Ressource aus, um deren Verlauf des Integritätsstatus anzuzeigen.

    ![Load Balancer-Integritätsstatus](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Abbildung: Load Balancer-Integritätsanzeige für Ressource*
 
Eine generische Beschreibung des Ressourcenintegritätsstatus finden Sie in der [Dokumentation zu Resource Health](../service-health/resource-health-overview.md). Bestimmte Status für Azure Load Balancer sind in der folgenden Tabelle aufgeführt: 

| Ressourcenintegritätsstatus | BESCHREIBUNG |
| --- | --- |
| Verfügbar | Ihre Load Balancer Standard-Ressource ist fehlerfrei und verfügbar. |
| Heruntergestuft | Für die Load Balancer Standard-Instanz liegen plattform- oder benutzerseitig initiierte Ereignisse vor, die sich auf die Leistung auswirken. Die Metrik für die Datenpfadverfügbarkeit hat mindestens zwei Minuten lang weniger als 90 %, aber mehr als 25 %Integrität gemeldet. Es treten mittlere bis schwerwiegende Leistungsbeeinträchtigungen auf. [Lesen Sie den Leitfaden zur Behandlung von Problemen mit der Datenpfadverfügbarkeit], um zu ermitteln, ob benutzerseitig initiierte Ereignisse vorliegen, die sich auf die Verfügbarkeit auswirken.
| Nicht verfügbar | Ihre Load Balancer Standard-Ressource ist nicht fehlerfrei. Die Metrik für die Datenpfadverfügbarkeit hat mindestens zwei Minuten lang weniger als 25 % Integrität gemeldet. Leistung und Verfügbarkeit für eingehende Verbindungen sind erheblich beeinträchtigt. Möglicherweise liegen Benutzer- oder Plattformereignisse vor, die eine Nichtverfügbarkeit verursachen. [Lesen Sie den Leitfaden zur Behandlung von Problemen mit der Datenpfadverfügbarkeit], um zu ermitteln, ob benutzerseitig initiierte Ereignisse vorliegen, die sich auf die Verfügbarkeit auswirken. |
| Unbekannt | Der Ressourcenintegritätsstatus Ihrer Load Balancer Standard-Ressource wurde noch nicht aktualisiert oder hat in den letzten 10 Minuten keine Informationen zur Datenpfadverfügbarkeit empfangen. Dieser Zustand sollte vorübergehend sein. Der korrekte Status wird angegeben, sobald Daten empfangen werden. |

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Load Balancer Standard](./load-balancer-overview.md).
- Weitere Informationen zu Ihren [ausgehenden Verbindungen für Load Balancer](./load-balancer-outbound-connections.md)
- Weitere Informationen finden Sie unter [Azure Monitor](../azure-monitor/overview.md).
- Weitere Informationen finden Sie unter [Azure Monitor REST-API](/rest/api/monitor/) und [Abrufen von Metriken über die REST-API](/rest/api/monitor/metrics/list).