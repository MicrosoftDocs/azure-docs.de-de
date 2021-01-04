---
title: Verbindungsmonitor in Azure | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie mit dem Verbindungsmonitor die Netzwerkkommunikation in einer verteilten Umgebung überwachen.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: c5c3f8dedad47a819ce3648a0b81ffa3e65a1a1e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544886"
---
# <a name="network-connectivity-monitoring-with-connection-monitor"></a>Überwachung der Netzwerkkonnektivität mit dem Verbindungsmonitor

Der Verbindungsmonitor bietet eine einheitliche End-to-End-Verbindungsüberwachung in Azure Network Watcher. Das Feature für den Verbindungsmonitor unterstützt Hybrid- und Azure-Cloudbereitstellungen. Network Watcher stellt Tools für das Überwachen, Diagnostizieren und Anzeigen von Verbindungsmetriken für Ihre Azure-Bereitstellungen zur Verfügung.

Nachfolgend sind einige Anwendungsfälle für den Verbindungsmonitor aufgeführt:

- Ihre Front-End-Webserver-VM kommuniziert in einer Anwendung mit mehreren Ebenen mit einer Datenbankserver-VM, und Sie möchten die Netzwerkkonnektivität zwischen beiden VMs überprüfen.
- Sie möchten, dass VMs in der Region „USA, Osten“ VMs in der Region „USA, Mitte“ pingen, und Sie möchten die regionsübergreifenden Netzwerklatenzen vergleichen.
- Sie verfügen über mehrere lokale Unternehmensstandorte in Seattle, Washington, und in Ashburn, Virginia. Ihre Unternehmensstandorte sind mit Microsoft 365-URLs verbunden. Sie möchten für die Benutzer der Microsoft 365-URLs die Latenzen in Seattle und Ashburn vergleichen.
- Ihre Hybridanwendung benötigt eine Verbindung mit einem Azure Storage-Endpunkt. Der lokale Standort und die Azure-Anwendung sind mit dem gleichen Azure Storage-Endpunkt verbunden. Sie möchten die Latenzen des lokalen Standorts mit den Latenzen der Azure-Anwendung vergleichen.
- Sie möchten die Konnektivität zwischen den lokalen Setups und den Azure-VMs überprüfen, auf denen die Cloudanwendung gehostet wird.

Der Verbindungsmonitor bietet eine Kombination aus dem Besten von zwei Features: dem Feature [Verbindungsmonitor (Klassisch)](./network-watcher-monitoring-overview.md#monitor-communication-between-a-virtual-machine-and-an-endpoint) von Network Watcher und dem Feature [Dienstkonnektivitätsmonitor](../azure-monitor/insights/network-performance-monitor-service-connectivity.md) des Netzwerkleistungsmonitors (NPM), [ExpressRoute-Überwachung](../expressroute/how-to-npm.md) und [Leistungsüberwachung](../azure-monitor/insights/network-performance-monitor-performance-monitor.md).

Nachfolgend sind einige Vorteile des Verbindungsmonitors aufgeführt:

* Einheitliche, intuitive Oberfläche zum Überwachen von Azure- und Hybridbereitstellungen
* Regionsübergreifende und arbeitsbereichsübergreifende Konnektivitätsüberwachung
* Häufigere Prüfungen und bessere Einsicht in die Netzwerkleistung
* Schnellere Warnungen für Ihre Hybridbereitstellungen
* Unterstützung für Konnektivitätsprüfungen basierend auf HTTP, TCP und ICMP 
* Metriken und Unterstützung für Log Analytics, sowohl für Azure- als auch für Nicht-Azure-Testsetups

![Diagramm der Interaktion des Verbindungsmonitors mit Azure-VMs, Nicht-Azure-Hosts, Endpunkten und Datenspeicherorten](./media/connection-monitor-2-preview/hero-graphic.png)

Führen Sie die folgenden Schritte aus, um den Verbindungsmonitor für die Überwachung zu verwenden: 

1. Installieren Sie Überwachungs-Agents.
1. Aktivieren Sie Network Watcher für Ihr Abonnement.
1. Erstellen Sie einen Verbindungsmonitor.
1. Richten Sie die Datenanalyse und Warnungen ein.
1. Diagnostizieren Sie Problemen in Ihrem Netzwerk.

Die folgenden Abschnitte enthalten weitere Informationen zu diesen Schritten.

## <a name="install-monitoring-agents"></a>Installieren von Überwachungs-Agents

Der Verbindungsmonitor verwendet einfache ausführbare Dateien zum Ausführen von Konnektivitätsprüfungen. Er unterstützt Konnektivitätsprüfungen sowohl in Azure-Umgebungen als auch lokalen Umgebungen. Die verwendete ausführbare Datei hängt davon ab, ob Ihre VM in Azure oder lokal gehostet wird.

### <a name="agents-for-azure-virtual-machines"></a>Agents für Azure-VMs

Damit der Verbindungsmonitor Ihre Azure-VMs als Überwachungsquellen erkennt, installieren Sie die VM-Erweiterung für den Network Watcher-Agent auf den VMs. Diese Erweiterung wird auch als *Network Watcher-Erweiterung* bezeichnet. Für Azure-VMs muss die Erweiterung eine End-to-End-Überwachung und andere erweiterte Funktionen auslösen. 

Sie können die Network Watcher-Erweiterung beim [Erstellen einer VM](./connection-monitor.md#create-the-first-vm) installieren. Die Network Watcher-Erweiterung für [Linux](../virtual-machines/extensions/network-watcher-linux.md) und [Windows](../virtual-machines/extensions/network-watcher-windows.md) kann auch separat installiert und konfiguriert sowie eine separate Problembehandlung durchgeführt werden.

Durch Regeln für eine Netzwerksicherheitsgruppe (NSG) oder Firewall kann die Kommunikation zwischen Quelle und Ziel blockiert werden. Der Verbindungsmonitor erkennt dieses Problem und zeigt es als Diagnosemeldung in der Topologie an. Damit eine Verbindungsüberwachung möglich ist, müssen Sie zunächst sicherstellen, dass die NSG- und Firewallregeln eine Paketübertragung über TCP oder ICMP zwischen der Quelle und dem Ziel zulassen.

### <a name="agents-for-on-premises-machines"></a>Agents für lokale Computer

Damit der Verbindungsmonitor die lokalen Computer als Quellen für die Überwachung erkennt, installieren Sie den Log Analytics-Agent auf den Computern. Dann aktivieren Sie die Netzwerkleistungsmonitor-Lösung. Diese Agents sind mit den Log Analytics-Arbeitsbereichen verknüpft. Daher müssen Sie die Arbeitsbereichs-ID und den Primärschlüssel einrichten, bevor die Agents mit der Überwachung beginnen können.

Informationen zum Installieren des Log Analytics-Agents für Windows-Computer finden Sie unter [Azure Monitor-VM-Erweiterung für Windows](../virtual-machines/extensions/oms-windows.md).

Wenn der Pfad Firewalls oder virtuelle Netzwerkgeräte (NVAs) umfasst, stellen Sie sicher, dass das Ziel erreichbar ist.

## <a name="enable-network-watcher-on-your-subscription"></a>Aktivieren von Network Watcher für Ihr Abonnement

Für alle Abonnements, die über ein virtuelles Netzwerk verfügen, wird Network Watcher aktiviert. Wenn Sie ein virtuelles Netzwerk in Ihrem Abonnement erstellen, wird Network Watcher automatisch in der Region und für das Abonnement des virtuellen Netzwerks aktiviert. Diese automatische Aktivierung wirkt sich nicht auf Ihre Ressourcen aus, und es fallen auch keine Gebühren an. Stellen Sie sicher, dass Network Watcher für Ihr Abonnement nicht explizit deaktiviert ist. 

Weitere Informationen finden Sie unter [Aktivieren von Network Watcher](./network-watcher-create.md).

## <a name="create-a-connection-monitor"></a>Erstellen eines Verbindungsmonitors 

Der Verbindungsmonitor überwacht die Kommunikation in regelmäßigen Abständen. Er informiert Sie über Änderungen der Erreichbarkeit und Latenz. Sie können auch die aktuelle und die frühere Netzwerktopologie zwischen Quell-Agents und Zielendpunkten überprüfen.

Ziele können Azure-VMs oder lokale Computer sein, auf denen ein Überwachungs-Agent installiert ist. Zielendpunkte können Microsoft 365-URLs, Dynamics 365-URLs, benutzerdefinierte URLs, Azure-VM-Ressourcen-IDs, IPv4/IPv6-Adressen, FQDNs oder beliebige Domänennamen sein.

### <a name="access-connection-monitor"></a>Zugreifen auf den Verbindungsmonitor

1. Navigieren Sie auf der Startseite des Azure-Portals zu **Network Watcher**.
1. Wählen Sie links im Abschnitt **Überwachung** die Option **Verbindungsmonitor** aus.
1. Es werden alle Verbindungsmonitore angezeigt, die im Verbindungsmonitor erstellt wurden. Zum Anzeigen der Verbindungsmonitore, die auf der klassischen Benutzeroberfläche des Verbindungsmonitors erstellt wurden, wechseln Sie zur Registerkarte **Verbindungsmonitor**.
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Screenshot: Verbindungsmonitore, die im Verbindungsmonitor erstellt wurden" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>Erstellen eines Verbindungsmonitors

In Verbindungsmonitoren, die Sie im Feature „Verbindungsmonitor“ erstellen, können Sie sowohl lokale Computer als auch Azure-VMs als Quellen hinzufügen. Mit diesen Verbindungsmonitoren kann auch die Konnektivität mit Endpunkten überwacht werden. Die Endpunkte können sich in Azure oder einer beliebigen anderen URL oder IP-Adresse befinden.

Der Verbindungsmonitor umfasst die folgenden Entitäten:

* **Verbindungsmonitorressource**: Dies ist eine regionsspezifische Azure-Ressource. Alle folgenden Entitäten sind Eigenschaften einer Verbindungsmonitorressource.
* **Endpunkt**: Eine Quelle oder ein Ziel, die bzw. das an Konnektivitätsprüfungen beteiligt ist. Beispiele für Endpunkte umfassen Azure-VMs, lokale Agents, URLs und IP-Adressen.
* **Testkonfiguration**: Eine protokollspezifische Konfiguration für einen Test. Je nach ausgewähltem Protokoll können Sie den Port, Schwellenwerte, die Testhäufigkeit und weitere Parameter definieren.
* **Testgruppe**: Die Gruppe, die Quellendpunkte, Zielendpunkte und Testkonfigurationen enthält. Ein Verbindungsmonitor kann mehrere Testgruppen enthalten.
* **Test**: Die Kombination aus einem Quellendpunkt, einem Zielendpunkt und einer Testkonfiguration. Ein Test ist die differenzierteste Ebene, auf der Überwachungsdaten verfügbar sind. Die Überwachungsdaten umfassen den Prozentsatz von Überprüfungen mit Fehlern und die Roundtripzeit.

 ![Diagramm eines Verbindungsmonitors mit Definition der Beziehung zwischen Testgruppen und Tests](./media/connection-monitor-2-preview/cm-tg-2.png)

Sie können mit dem [Azure-Portal](./connection-monitor-create-using-portal.md) oder mit [ARMClient](./connection-monitor-create-using-template.md) eine Vorschau für den Verbindungsmonitor erstellen.

Alle Quellen, Ziele und Testkonfigurationen, die Sie einer Testgruppe hinzufügen, werden auf einzelne Tests aufgeteilt. Hier folgt ein Beispiel dafür, wie Quellen und Ziele aufgeteilt werden:

* Testgruppe: TG1
* Quellen: 3 (A, B, C)
* Ziele: 2 (D, E)
* Testkonfigurationen: 2 (Config 1, Config 2)
* Gesamtzahl erstellter Tests: 12

| Testnummer | `Source` | Destination | Testkonfiguration |
| --- | --- | --- | --- |
| 1 | Ein | D | Config 1 |
| 2 | Ein | D | Config 2 |
| 3 | Ein | E | Config 1 |
| 4 | Ein | E | Config 2 |
| 5 | B | D | Config 1 |
| 6 | B | D | Config 2 |
| 7 | B | E | Config 1 |
| 8 | B | E | Config 2 |
| 9 | C | D | Config 1 |
| 10 | C | D | Config 2 |
| 11 | C | E | Config 1 |
| 12 | C | E | Config 2 |

### <a name="scale-limits"></a>Skalierungslimits

Für Verbindungsmonitore gelten die folgenden Skalierungslimits:

* Maximale Anzahl von Verbindungsmonitoren pro Abonnement und Region: 100
* Maximale Anzahl von Testgruppen pro Verbindungsmonitor: 20
* Maximale Anzahl von Quellen und Zielen pro Verbindungsmonitor: 100
* Maximale Anzahl von Testkonfigurationen pro Verbindungsmonitor:

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analysieren von Überwachungsdaten und Festlegen von Warnungen

Nachdem Sie einen Verbindungsmonitor erstellt haben, überprüfen die Quellen die Konnektivität mit den Zielen auf Grundlage der Testkonfiguration.

### <a name="checks-in-a-test"></a>Überprüfungen in einem Test

Basierend auf dem von Ihnen in der Testkonfiguration ausgewählten Protokoll führt der Verbindungsmonitor eine Reihe von Überprüfungen für das Quelle-Ziel-Paar aus. Die Überprüfungen werden gemäß der ausgewählten Testhäufigkeit durchgeführt.

Wenn Sie HTTP verwenden, berechnet der Dienst die Anzahl von HTTP-Antworten, die einen gültigen Antwortcode zurückgegeben haben. Gültige Antwortcodes können mithilfe von PowerShell und der CLI festgelegt werden. Das Ergebnis bestimmt den Prozentsatz der Überprüfungen mit Fehlern. Zum Berechnen der Roundtripzeit misst der Dienst die Zeit zwischen einem HTTP-Aufruf und der Antwort.

Wenn Sie TCP oder ICMP verwenden, berechnet der Dienst den Prozentsatz an Paketverlusten, um den Prozentsatz der Überprüfungen mit Fehlern zu ermitteln. Zum Berechnen der Roundtripzeit misst der Dienst die Zeit, die zum Empfangen der Bestätigung (ACK) für die gesendeten Pakete benötigt wird. Wenn Sie Traceroutedaten für Ihre Netzwerktests aktiviert haben, können Sie sich Hop-by-Hop-Verlust und -Latenz für Ihr lokales Netzwerk anzeigen lassen.

### <a name="states-of-a-test"></a>Teststatus

Basierend auf den Daten, die bei den Überprüfungen zurückgegeben werden, können Tests einen der folgenden Status aufweisen:

* **Erfolgreich**: Die tatsächlichen Werte für den Prozentsatz der Überprüfungen mit Fehlern und die Roundtripzeit liegen innerhalb der angegebenen Schwellenwerte.
* **Fehler**: Die tatsächlichen Werte für den Prozentsatz der Überprüfungen mit Fehlern oder die Roundtripzeit lagen oberhalb der angegebenen Schwellenwerte. Wenn kein Schwellenwert angegeben ist, weist ein Test den Fehlerstatus auf, sobald der Prozentsatz der Überprüfungen mit Fehlern bei 100 liegt.
* **Warnung**: 
     * Wenn ein Schwellenwert angegeben ist und der Verbindungsmonitor bei fehlerhaften Überprüfungen einen Prozentsatz von mehr als 80 Prozent des Schwellenwerts beobachtet, wird der Test als Warnung gekennzeichnet.
     * Wenn keine Schwellenwerte angegeben sind, weist der Verbindungsmonitor automatisch einen Schwellenwert zu. Wird dieser Schwellenwert überschritten, ändert sich der Teststatus in „Warnung“. Für die Roundtripzeit (RTT) in TCP- oder ICMP-Tests beträgt der Schwellenwert 750 ms. Für den Prozentsatz der fehlerhaften Überprüfungen liegt der Schwellenwert bei 10 %. 
* **Unbestimmt** : Keine Daten im Log Analytics-Arbeitsbereich.  Überprüfen Sie die Metriken. 
* **Nicht ausgeführt** : Deaktiviert durch Deaktivierung der Testgruppe.  

### <a name="data-collection-analysis-and-alerts"></a>Datensammlung, Datenanalyse und Datenwarnungen

Die vom Verbindungsmonitor gesammelten Daten werden im Log Analytics-Arbeitsbereich gespeichert. Sie haben diesen Arbeitsbereich beim Erstellen des Verbindungsmonitors eingerichtet. 

Über Azure Monitor-Metriken sind auch Überwachungsdaten verfügbar. Mit Log Analytics können Sie die Überwachungsdaten über den gewünschten Zeitraum aufbewahren. Azure Monitor speichert Metriken standardmäßig nur für 30 Tage. 

Sie können [metrikbasierte Warnungen für die Daten festlegen](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Überwachungsdashboards

Auf den Überwachungsdashboards wird eine Liste der Verbindungsmonitore angezeigt, auf die Sie für Ihre Abonnements, Regionen, Zeitstempel, Quellen und Zieltypen zugreifen können.

Wenn Sie von Network Watcher zum Verbindungsmonitor wechseln, können Sie Daten auf folgende Arten anzeigen:

* **Verbindungsmonitor**: Dies ist eine Liste aller Verbindungsmonitore, die für Ihre Abonnements, Regionen, Zeitstempel, Quellen und Zieltypen erstellt wurden. Dies ist die Standardansicht.
* **Testgruppen**: Dies ist eine Liste aller Testgruppen, die für Ihre Abonnements, Regionen, Zeitstempel, Quellen und Zieltypen erstellt wurden. Diese Testgruppen sind nicht nach Verbindungsmonitoren gefiltert.
* **Test**: Dies ist eine Liste aller Tests, die für Ihre Abonnements, Regionen, Zeitstempel, Quellen und Zieltypen ausgeführt werden. Diese Tests sind nicht nach Verbindungsmonitoren oder Testgruppen gefiltert.

In der folgenden Abbildung sind die drei Datenansichten mit Pfeil 1 gekennzeichnet.

Sie können auf dem Dashboard die einzelnen Verbindungsmonitore erweitern, um deren Testgruppen anzuzeigen. Anschließend können Sie die einzelnen Testgruppen erweitern, um die darin ausgeführten Tests anzuzeigen. 

Für eine Liste sind folgende Filter verfügbar:

* **Filter der obersten Ebene**: Durchsuchen Sie die Liste nach Text, Entitätstyp (Verbindungsmonitor, Testgruppe oder Test), Zeitstempel und Umfang. Der Umfang umfasst Abonnements, Regionen, Quellen und Zieltypen. Siehe Kasten 1 in der folgenden Abbildung.
* **Zustandsbasierte Filter**: Filtern Sie nach dem Zustand des Verbindungsmonitors, der Testgruppe oder des Tests. Siehe Kasten 2 in der folgenden Abbildung.
* **Warnungsbasierte Filter**: Filtern Sie nach Warnungen, die bei der Verbindungsmonitorressource ausgelöst werden. Siehe Kasten 3 in der folgenden Abbildung.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="Screenshot: Filtern von Ansichten der Verbindungsmonitore, Testgruppen und Tests im Verbindungsmonitor" lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
Wenn Sie beispielsweise alle Tests im Verbindungsmonitor anzeigen möchten, bei denen die Quell-IP 10.192.64.56 lautet, gehen Sie folgendermaßen vor:
1. Ändern Sie die Ansicht in **Test**.
1. Geben Sie *10.192.64.56* in das Suchfeld ein.
1. Wählen Sie unter **Umfang** im Filter der obersten Ebene **Quellen** aus.

Wenn Sie nur Tests mit Fehlern im Verbindungsmonitor anzeigen möchten, bei denen die Quell-IP 10.192.64.56 lautet, gehen Sie folgendermaßen vor:
1. Ändern Sie die Ansicht in **Test**.
1. Wählen Sie aus den zustandsbasierten Filtern die Option **Fehler** aus.
1. Geben Sie *10.192.64.56* in das Suchfeld ein.
1. Wählen Sie unter **Umfang** im Filter der obersten Ebene **Quellen** aus.

Wenn Sie nur Tests mit Fehlern im Verbindungsmonitor anzeigen möchten, bei denen das Ziel „outlook.office365.com“ lautet, gehen Sie folgendermaßen vor:
1. Ändern Sie die Ansicht in **Test**.
1. Wählen Sie aus den zustandsbasierten Filtern die Option **Fehler** aus.
1. Geben Sie *office.live.com* in das Suchfeld ein.
1. Wählen Sie unter **Umfang** im Filter der obersten Ebene **Ziele** aus.
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="Screenshot: Gefilterte Ansicht zur ausschließlichen Anzeige von Tests mit Fehlern für das Ziel „outlook.office365.com“" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

Um den Grund für einen fehlerhaften Verbindungsmonitor oder eine fehlerhafte Testgruppe oder einen fehlerhaften Test zu erfahren, klicken Sie auf die Spalte mit dem Grund.  Dadurch erfahren Sie, welcher Schwellenwert (Prozentsatz der fehlerhaften Überprüfungen oder RTT) überschritten wurde, sowie die entsprechenden Diagnosemeldungen.
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="Screenshot: Die Fehlerursache für einen Verbindungsmonitor, einen Test oder eine Testgruppe wird angezeigt." lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
Wenn Sie die Trends bei der Roundtripzeit und dem Prozentsatz der Überprüfungen mit Fehlern für einen Verbindungsmonitor anzeigen möchten, gehen Sie folgendermaßen vor:
1. Wählen Sie den Verbindungsmonitor aus, den Sie untersuchen möchten.

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="Screenshot: Metriken für einen Verbindungsmonitor, nach Testgruppe angezeigt" lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

1. Die folgenden Abschnitte werden angezeigt:  
    1. Essentials: Ressourcenspezifische Eigenschaften des ausgewählten Verbindungsmonitors 
    1. Zusammenfassung: 
        1. Aggregierte Trendlinien für RTT und den Prozentsatz der fehlerhaften Überprüfungen für alle Tests im Verbindungsmonitor. Sie können eine bestimmte Zeit zum Anzeigen der Details festlegen.
        1. Die Top 5 der Testgruppen, Quellen und Ziele, basierend auf dem RTT oder dem Prozentsatz der fehlerhaften Überprüfungen. 
    1. Registerkarten für Testgruppen , Quellen, Ziele und Testkonfigurationen – Listet Testgruppen, Quellen oder Ziele im Verbindungsmonitor auf. Fehlerhafte Testüberprüfungen, aggregierte RTT und Werte für Prozentsatz der fehlerhaften Überprüfungen.  Sie können auch in die Vergangenheit wechseln, um Daten anzuzeigen. 
    1. Probleme: Probleme der Hopebene für jeden Test im Verbindungsmonitor. 

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="Screenshot: Metriken für einen Verbindungsmonitor, nach Testgruppenteil 2 angezeigt" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. Sie haben folgende Möglichkeiten:
    * Klicken Sie auf „Alle Tests anzeigen“, um alle Tests im Verbindungsmonitor anzuzeigen.
    * Klicken Sie auf „Alle Testgruppen, Testkonfigurationen, Quellen und Ziele anzeigen“, um bestimmte Details anzuzeigen. 
    * Wählen Sie eine Testgruppe, Testkonfiguration, Quelle oder ein Ziel aus, um alle Tests in der Entität anzuzeigen.

1. Über die Ansicht „Alle Tests anzeigen“ haben Sie folgende Möglichkeiten:
    * Wählen Sie Tests aus, und klicken Sie auf „Vergleichen“.
    
    :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="Screenshot: Vergleich zweier Tests." lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
    * Verwenden Sie Cluster, um zusammengesetzte Ressourcen wie VNET, Subnets um deren untergeordneten Ressourcen zu erweitern.
    * Zeigen Sie die Topologie für beliebige Tests an, indem Sie auf „Topologie“ klicken.

Wenn Sie die Trends bei der Roundtripzeit und dem Prozentsatz der Überprüfungen mit Fehlern für eine Testgruppe anzeigen möchten, gehen Sie folgendermaßen vor:
1. Wählen Sie die Testgruppe aus, die Sie untersuchen möchten. 
1. Sie werden ähnlich wie beim Verbindungsmonitor angezeigt: Essentials, Zusammenfassung, Tabelle für Testgruppen, Quellen, Ziele und Testkonfigurationen. Navigieren Sie sie wie bei einem Verbindungsmonitor.

Wenn Sie die Trends bei der Roundtripzeit und dem Prozentsatz der Überprüfungen mit Fehlern für einen Test anzeigen möchten, gehen Sie folgendermaßen vor:
1. Wählen Sie den Test aus, den Sie untersuchen möchten. Sie sehen die Netzwerktopologie und die End-to-End-Trenddiagramme für den Prozentsatz fehlerhafter Überprüfungen sowie die Roundtripzeit. Wählen Sie in der Topologie einen beliebigen Hop im Pfad aus, um die erkannten Probleme anzuzeigen. (Diese Hops sind Azure-Ressourcen.) Für lokale Netzwerke steht diese Funktion derzeit nicht zur Verfügung.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="Screenshot: Topologieansicht eines Tests" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Protokollabfragen in Log Analytics

Mit Log Analytics lassen sich benutzerdefinierte Ansichten Ihrer Überwachungsdaten erstellen. Alle auf der Benutzeroberfläche angezeigten Daten stammen aus Log Analytics. Sie können Daten im Repository interaktiv analysieren. Korrelieren Sie die Daten aus der Agent-Integritätsdiagnose oder anderen Lösungen, die auf Log Analytics basieren. Exportieren Sie die Daten nach Excel oder Power BI, oder erstellen Sie einen freigabefähigen Link.

#### <a name="metrics-in-azure-monitor"></a>Metriken in Azure Monitor

In Verbindungsmonitoren, die vor dem Verbindungsmonitor erstellt wurden, sind alle vier Metriken verfügbar: ProbesFailedPercent, AverageRoundtripMs, ChecksFailedPercent (Preview) und RoundTripTimeMs (Preview). In Verbindungsmonitoren, die im Verbindungsmonitor erstellt wurden, sind Daten nur für die mit *(Preview)* (Vorschau) gekennzeichneten Metriken verfügbar.

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="Screenshot: Metriken im Verbindungsmonitor" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

Legen Sie bei Verwendung von Metriken den Ressourcentyp auf „Microsoft.Network/networkWatchers/connectionMonitors“ fest.

| Metrik | `Display name` | Einheit | Aggregationstyp | BESCHREIBUNG | Dimensionen |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | Fehlerhafte Tests in Prozent | Prozentwert | Average | Prozentsatz fehlerhafter Tests bei der Konnektivitätsüberwachung. | Keine Dimensionen |
| AverageRoundtripMs | Durchschn. Roundtripzeit (ms) | Millisekunden | Average | Durchschnittliche Netzwerk-Roundtripzeit für die Konnektivitätsüberwachung von Testdatenverkehr, der zwischen Quelle und Ziel gesendet wurde. |             Keine Dimensionen |
| ChecksFailedPercent (Preview) | % der Überprüfungen mit Fehlern (Vorschau) | Prozentwert | Average | Prozentsatz der Überprüfungen mit Fehlern für einen Test. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Region |
| RoundTripTimeMs (Vorschau) | Roundtripzeit (ms) (Vorschau) | Millisekunden | Average | Roundtripzeit für Überprüfungen, die zwischen Quelle und Ziel gesendet wurden. Es wird kein Durchschnittswert gebildet. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Region |

#### <a name="metric-based-alerts-for-connection-monitor"></a>Auf Metriken basierende Warnungen für den Verbindungsmonitor

Sie können Metrikwarnungen für Verbindungsmonitore mithilfe der folgenden Methoden erstellen. 

1. Vom Verbindungsmonitor aus, während der Erstellung des Verbindungsmonitors [unter Verwendung des Azure-Portals](connection-monitor-preview-create-using-portal.md#) 
1. Vom Verbindungsmonitor aus, unter Verwendung von „Warnungen konfigurieren“ auf dem Dashboard 
1. Von Azure Monitor aus, um eine Warnung in Azure Monitor zu erstellen: 
    1. Wählen Sie die Verbindungsmonitorressource aus, die Sie im Verbindungsmonitor erstellt haben.
    1. Stellen Sie sicher, dass **Metrik** als Signaltyp für den Verbindungsmonitor angezeigt wird.
    1. Wählen Sie unter **Bedingung hinzufügen** für **Signalname** die Option **ChecksFailedPercent(Preview)** oder **RoundTripTimeMs(Preview)** aus.
    1. Wählen Sie für **Signaltyp** die Option **Metriken** aus. Wählen Sie z. B. **ChecksFailedPercent(Preview)** aus.
    1. Alle Dimensionen für die Metrik werden aufgelistet. Wählen Sie den Dimensionsnamen und -wert aus. Wählen Sie z. B. **Quelladresse** aus, und geben Sie dann die IP-Adresse einer beliebigen Quelle in Ihrem Verbindungsmonitor ein.
    1. Geben Sie unter **Warnungslogik** die folgenden Details ein:
        * **Bedingungstyp**: **Statisch**
        * **Bedingung** und **Schwellenwert**
        * **Aggregationsgranularität und Häufigkeit der Auswertung**: Der Verbindungsmonitor aktualisiert die Daten jede Minute.
    1. Wählen Sie unter **Aktionen** Ihre Aktionsgruppe aus.
    1. Legen Sie die Warnungsdetails fest.
    1. Erstellen Sie die Warnungsregel.

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="Screenshot: Der Bereich „Regel erstellen“ in Azure Monitor. Quelladresse und Name des Quellendpunkts sind hervorgehoben." lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>Diagnostizieren von Problemen in Ihrem Netzwerk

Mit dem Verbindungsmonitor können Sie Probleme in Ihrem Verbindungsmonitor und Ihrem Netzwerk diagnostizieren. Probleme in Ihrem Hybridnetzwerk werden von den Log Analytics-Agents erkannt, die Sie zuvor installiert haben. Probleme in Azure werden von der Network Watcher-Erweiterung erkannt. 

Sie können Probleme im Azure-Netzwerk in der Netzwerktopologie anzeigen.

Bei Netzwerken, deren Quellen sich auf lokalen VMs befinden, können die folgenden Probleme erkannt werden:

* Timeout bei der Anforderung.
* Der Endpunkt wurde nicht über DNS aufgelöst (vorübergehend oder dauerhaft). Die URL ist ungültig.
* Es wurden keine Hosts gefunden.
* Die Quelle kann keine Verbindung zum Ziel herstellen. Das Ziel ist nicht über ICMP erreichbar.
* Zertifikatbezogene Probleme: 
    * Der Agent muss mit dem Clientzertifikat authentifiziert werden. 
    * Es besteht kein Zugriff auf die Zertifikatssperrliste. 
    * Der Hostname des Endpunkts stimmt nicht mit dem Antragsteller des Zertifikats oder dem alternativen Antragstellernamen überein. 
    * Das Stammzertifikat fehlt im Speicher der vertrauenswürdigen Zertifizierungsstellen auf dem lokalen Computer der Quelle. 
    * Das SSL-Zertifikat ist abgelaufen, ungültig, widerrufen oder nicht kompatibel.

Bei Netzwerken, deren Quellen sich auf Azure-VMs befinden, können die folgenden Probleme erkannt werden:

* Probleme mit Agents:
    * Der Agent wurde angehalten.
    * Fehler bei der DNS-Auflösung.
    * Es lauscht keine Anwendung oder kein Listener am Zielport.
    * Der Socket konnte nicht geöffnet werden.
* Probleme mit dem VM-Status: 
    * Wird gestartet
    * Wird beendet
    * Beendet
    * Zuordnung wird aufgehoben
    * Zuordnung aufgehoben
    * Wird neu gestartet
    * Nicht zugewiesen
* Der ARP-Tabelleneintrag fehlt.
* Der Datenverkehr wurde aufgrund von Problemen mit der lokalen Firewall oder NSG-Regeln blockiert.
* Probleme mit dem Gateway des virtuellen Netzwerks: 
    * Es fehlen Routen.
    * Der Tunnel zwischen zwei Gateways ist getrennt oder fehlt.
    * Das zweite Gateway wurde nicht vom Tunnel gefunden.
    * Es wurden keine Peeringinformationen gefunden.
* Die Route fehlte in Microsoft Edge.
* Der Datenverkehr wurde aufgrund von Systemrouten oder UDR unterbrochen.
* BGP ist für die Gatewayverbindung nicht aktiviert.
* Der DIP-Test ist beim Lastenausgleich nicht verfügbar.

## <a name="next-steps"></a>Nächste Schritte
    
   * Informationen zum [Erstellen eines Verbindungsmonitors mithilfe des Azure-Portals](./connection-monitor-create-using-portal.md)  
   * Informationen zum [Erstellen eines Verbindungsmonitors mithilfe von ARMClient](./connection-monitor-create-using-template.md)