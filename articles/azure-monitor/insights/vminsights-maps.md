---
title: Anzeigen von App-Abhängigkeiten mit Azure Monitor für VMs
description: Die Dienstzuordnung ist ein Feature von Azure Monitor für VMs. Sie ermittelt automatisch Anwendungskomponenten auf Windows- und Linux-Systemen und stellt die Kommunikation zwischen Diensten dar. In diesem Artikel wird beschrieben, wie Sie dieses Feature in unterschiedlichen Szenarios verwenden.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: 32ad92b333eda8cd429303177dba160f0b58af54
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95985797"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-to-understand-application-components"></a>Verwenden des Zuordnungsfeatures in Azure Monitor für VMs zum Analysieren von Anwendungskomponenten
In Azure Monitor für VMs können Sie sich ermittelte Anwendungskomponenten für Windows- und Linux-VMs anzeigen lassen, die in Azure oder in Ihrer eigenen Umgebung ausgeführt werden. Sie können die VMs auf zwei Arten überwachen. Entweder rufen Sie die Zuordnung direkt über eine VM oder über Azure Monitor auf, um sich die Komponenten in den verschiedenen VM-Gruppen anzusehen. In diesem Artikel werden die beiden Anzeigemethoden beschrieben. Außerdem wird erklärt, wie Sie das Zuordnungsfeature verwenden. 

Informationen zum Konfigurieren von Azure Monitor for VMs finden Sie unter [Enable Azure Monitor for VMs](vminsights-enable-overview.md) (Aktivieren von Azure Monitor for VMs).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure
Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="introduction-to-the-map-experience"></a>Einführung in die Benutzeroberfläche des Zuordnungsfeatures
Bevor Sie sich genauer mit dem Zuordnungsfeature befassen, sollten Sie nachvollziehen können, wie damit Informationen dargestellt und visualisiert werden. Ob Sie das Zuordnungsfeature direkt über eine VM oder über Azure Monitor nutzen, spielt für die Benutzeroberfläche keine Rolle, da diese immer gleich ist. Der einzige Unterschied besteht darin, dass Sie in Azure Monitor alle Mitglieder einer mehrschichtigen Anwendung oder eines Clusters in einer Zuordnung sehen können.

Das Zuordnungsfeature visualisiert die VM-Abhängigkeiten, indem es laufende Prozesse ermittelt. Für diese werden folgende Eigenschaften erfasst: 

- aktive Netzwerkverbindungen zwischen den Servern
- Latenz für eingehende und ausgehende Verbindungen
- in einem bestimmten Zeitraum verwendete Ports für Architekturen, die über TCP-Verbindungen kommunizieren  
 
Erweitern Sie eine VM, um sich Prozessdetails und nur die Prozesse anzeigen zu lassen, die mit der VM kommunizieren. Mit der Clientgruppe wird die Anzahl der Front-End-Clients dargestellt, die sich mit einer VM verbinden. Mit den Serverportgruppen wird die Anzahl der Back-End-Server dargestellt, mit denen sich die VM verbindet. Erweitern Sie eine Serverportgruppe, um die Detailliste für die Server aufzurufen, die über diesen Port eine Verbindung herstellen.  

Wenn Sie eine VM auswählen, werden im Bereich **Eigenschaften** auf der rechten Seite die VM-Eigenschaften angezeigt. Diese umfassen Systeminformationen, die vom Betriebssystem übermittelt werden, Eigenschaften der Azure-VM und ein Ringdiagramm, das eine Übersicht über die ermittelten Verbindungen enthält. 

![Bereich „Eigenschaften“](./media/vminsights-maps/properties-pane-01.png)

Klicken Sie rechts im Bereich auf **Protokollereignisse**, um eine Liste mit den Daten aufzurufen, die die VM an Azure Monitor gesendet hat. Diese Daten sind für Abfragen verfügbar.  Klicken Sie auf einen Eintragstyp, um die Seite **Protokolle** zu öffnen. Dort werden die Ergebnisse für diesen Eintragstyp angezeigt. Außerdem sehen Sie eine vorkonfigurierte Abfrage, die für die VM gefiltert ist.  

![Bereich „Protokollereignisse“](./media/vminsights-maps/properties-pane-logs-01.png)

Schließen Sie die Seite **Protokolle**, und wechseln Sie zurück zum Bereich **Eigenschaften**. Klicken Sie dort auf **Warnungen**, um sich Warnungen zu VM-Integritätskriterien anzeigen zu lassen. Azure-Warnungen sind so in das Zuordnungsfeature integriert, dass für einen ausgewählten Server und Zeitbereich Warnungen angezeigt werden. Für den Server wird ein Symbol angezeigt, wenn aktuelle Warnungen vorliegen. Im Bereich **Computerwarnungen** werden die Warnungen aufgelistet. 

![Bereich „Warnungen“](./media/vminsights-maps/properties-pane-alerts-01.png)

Damit das Zuordnungsfeature die relevanten Warnungen anzeigen kann, müssen Sie eine Warnungsregel erstellen, die für einen bestimmten Computer ausgelöst wird:

- Beziehen Sie eine Klausel zur Gruppierung der Warnungen nach Computer ein (z. B. **by Computer interval 1 minute**).
- Legen Sie als Auslöser der Warnung eine Metrik fest.

Weitere Informationen zu Azure-Warnungen und zum Erstellen von Warnungsregeln finden Sie unter [Einheitliche Oberfläche für Warnungen in Azure Monitor](../platform/alerts-overview.md).

In der **Legende** oben rechts werden die Symbole und die Rollen in einer Zuordnung beschrieben. Mit den Zoomsteuerelementen unten rechts können Sie sich diese Zuordnung genauer ansehen und darin navigieren. Dabei haben Sie die Möglichkeit, die Zoomstufe einzustellen und die Größe der Zuordnung an die Seite anzupassen.  

## <a name="connection-metrics"></a>Verbindungsmetriken
Im Bereich **Verbindungen** werden Standardmetriken für die ausgewählte Verbindung der VM über den TCP-Port angezeigt. Die Metriken beinhalten die Antwortzeit, die Anforderungen pro Minute, den Datenverkehrsdurchsatz und Links.  

![Diagramme zur Netzwerkkonnektivität im Bereich „Verbindungen“](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Verbindungsfehler
In der Zuordnung werden Verbindungsfehler für Prozesse und Computer angezeigt. Eine gestrichelte rote Linie zeigt an, dass ein Clientsystem einen Prozess oder Port nicht erreichen kann. Wenn für Systeme der Dependency-Agent eingesetzt wird, meldet dieser fehlgeschlagene Verbindungsversuche. Das Zuordnungsfeature überwacht einen Prozess, indem es TCP-Sockets erfasst, die keine Verbindung herstellen können. Der Fehler kann durch eine Firewall, eine fehlerhafte Konfiguration auf dem Client oder Server oder durch einen nicht verfügbaren Remotedienst verursacht werden.

![Eine fehlgeschlagene Verbindung in der Zuordnung](./media/vminsights-maps/map-group-failed-connection-01.png)

Die Analyse fehlgeschlagener Verbindungen erleichtert es Ihnen, Fehler zu beheben, das Ergebnis einer Migration zu überprüfen, die Sicherheitslage zu untersuchen und die grundlegende Architektur eines Diensts nachzuvollziehen. Verbindungsfehler sind zwar in einigen Fällen unbedenklich, weisen jedoch häufig auf ein Problem hin. Sie können beispielsweise dann auftreten, wenn eine Failoverumgebung nicht mehr erreichbar ist oder zwei Logikschichten nach einer Cloudmigration nicht mehr kommunizieren können.

### <a name="client-groups"></a>Clientgruppen
Clientgruppen stehen für Clientcomputer, die mit dem zugeordneten Computer eine Verbindung herstellen. Eine Clientgruppe stellt die Clients für einen einzelnen Prozess oder Computer dar.

![Eine Clientgruppe in der Zuordnung](./media/vminsights-maps/map-group-client-groups-01.png)

Wählen Sie die Gruppe aus, um die überwachten Clients und IP-Adressen der Systeme in einer Clientgruppe anzuzeigen. Die Inhalte der Gruppe sind unten aufgeführt.  

![Liste mit IP-Adressen einer Clientgruppe in der Zuordnung](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Wenn die Gruppe überwachte und nicht überwachte Clients beinhaltet, können Sie den entsprechenden Abschnitt des Ringdiagramms auswählen, um nach den Clients zu filtern.

### <a name="server-port-groups"></a>Serverportgruppen
Serverportgruppen stellen Ports auf Servern dar, für die eingehende Verbindungen vom zugeordneten Computer bestehen. Die Gruppe enthält den Serverport zusammen mit der Anzahl von Servern, die mit diesem Port verbunden sind. Wählen Sie die Gruppe aus, um sich die einzelnen Server und Verbindungen anzusehen. 

![Eine Serverportgruppe in der Zuordnung](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Wenn die Gruppe überwachte und nicht überwachte Server beinhaltet, können Sie den entsprechenden Abschnitt des Ringdiagramms auswählen, um nach den Servern zu filtern.

## <a name="view-a-map-from-a-vm"></a>Anzeigen einer Zuordnung über eine VM 

Gehen Sie wie folgt vor, um über eine VM direkt auf Azure Monitor für VMs zuzugreifen:

1. Wählen Sie im Azure-Portal die Option **Virtual Machines** aus. 
2. Wählen Sie aus der Liste eine VM aus. Wählen Sie im Abschnitt **Überwachung** die Option **Insights** aus.  
3. Wählen Sie die Registerkarte **Zuordnung** aus.

Mithilfe der Zuordnung werden die Abhängigkeiten der VM visualisiert. Dazu werden die ausgeführten Prozessgruppen und Prozesse mit aktiven Netzwerkverbindungen in einem bestimmten Zeitbereich ermittelt.  

Standardmäßig zeigt die Zuordnung die letzten 30 Minuten. Sie können auch Abhängigkeiten abfragen, die maximal eine Stunde zurückliegen. Nutzen Sie das Auswahlelement **TimeRange** oben links, um die Abfrage auszuführen. Das ist beispielsweise während eines Incidents oder zur Anzeige eines Status vor einer Änderung sinnvoll.  

![Screenshot der Registerkarte „Zuordnung“ im Abschnitt „Überwachung – Insights“ des Azure-Portals mit einem Diagramm der Abhängigkeiten zwischen virtuellen Computern](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Anzeigen einer Zuordnung über eine VM-Skalierungsgruppe

Gehen Sie wie folgt vor, um direkt über eine VM-Skalierungsgruppe auf Azure Monitor für VMs zuzugreifen:

1. Klicken Sie im Azure-Portal auf **VM-Skalierungsgruppen**.
2. Wählen Sie aus der Liste eine VM aus. Wählen Sie anschließend im Abschnitt **Überwachung** die Option **Insights** aus.  
3. Wählen Sie die Registerkarte **Zuordnung** aus.

In der Zuordnung werden alle Instanzen, die in der Skalierungsgruppe enthalten sind, zusammen mit den Abhängigkeiten der Gruppe als Gruppenknoten angezeigt. Im erweiterten Knoten werden die Instanzen der Skalierungsgruppe aufgeführt. Sie können durch die Instanzen scrollen und dabei 10 Einträge gleichzeitig überspringen. 

Wenn Sie eine Zuordnung für eine bestimmte Instanz auswählen möchten, wählen Sie diese Instanz zuerst aus. Klicken Sie dann zuerst auf die **Auslassungspunkte** rechts daneben und anschließend auf **Serverübersicht laden**. In der dargestellten Zuordnung werden die Prozessgruppen und Prozesse mit aktiven Netzwerkverbindungen in einem bestimmten Zeitbereich angezeigt. 

Standardmäßig zeigt die Zuordnung die letzten 30 Minuten. Sie können auch Abhängigkeiten abfragen, die maximal eine Stunde zurückliegen. Nutzen Sie das Auswahlelement **TimeRange**, um die Abfrage auszuführen. Das ist beispielsweise während eines Incidents oder zur Anzeige eines Status vor einer Änderung sinnvoll.

![Screenshot der Registerkarte „Zuordnung“ im Abschnitt „Überwachung – Insights“ des Azure-Portals mit einem Diagramm der Abhängigkeiten zwischen VM-Skalierungsgruppen](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Sie können auch die Zuordnung einer bestimmten Instanz über die Ansicht **Instanzen** für Ihre VM-Skalierungsgruppe aufrufen. Wechseln Sie im Abschnitt **Einstellungen** zu **Instanzen** > **Insights**.

## <a name="view-a-map-from-azure-monitor"></a>Anzeigen einer Zuordnung über Azure Monitor

In Azure Monitor stellt das Zuordnungsfeature eine globale Sicht Ihrer VMs und deren Abhängigkeiten dar. Gehen Sie wie folgt vor, um über Azure Monitor auf das Zuordnungsfeature zuzugreifen:

1. Wählen Sie im Azure-Portal die Option **Überwachen** aus. 
2. Wählen Sie im Abschnitt **Insights** die Option **Virtuelle Computer** aus.
3. Wählen Sie die Registerkarte **Zuordnung** aus.

   ![Azure Monitor-Übersicht über Zuordnung mit mehreren VMs](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Wählen Sie oben auf der Seite mit dem Auswahlelement **Arbeitsbereich** einen Arbeitsbereich aus. Wenn Sie über mehrere Log Analytics-Arbeitsbereiche verfügen, wählen Sie denjenigen aus, der für die Lösung aktiviert ist und an den VMs Daten übermitteln. 

Mit dem Auswahlelement **Gruppe** werden Abonnements, Ressourcengruppen, [Computergruppen](../platform/computer-groups.md) und VM-Skalierungsgruppen mit Computern angezeigt, die sich auf den ausgewählten Arbeitsbereich beziehen. Ihre Auswahl gilt nur für das Zuordnungsfeature und nicht für das Integritäts- oder Leistungsfeature.

Standardmäßig zeigt die Zuordnung die letzten 30 Minuten. Sie können auch Abhängigkeiten abfragen, die maximal eine Stunde zurückliegen. Nutzen Sie das Auswahlelement **TimeRange**, um die Abfrage auszuführen. Das ist beispielsweise während eines Incidents oder zur Anzeige eines Status vor einer Änderung sinnvoll.  

## <a name="next-steps"></a>Nächste Schritte

Wie Sie Engpässe ermitteln, die Leistung überprüfen und die Gesamtauslastung Ihrer VMs nachvollziehen können, erfahren Sie unter [Anzeigen des Leistungsstatus in Azure Monitor für VMs](vminsights-performance.md). 

