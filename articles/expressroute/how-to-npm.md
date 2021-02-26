---
title: 'Azure ExpressRoute: Konfigurieren des Netzwerkleistungsmonitors für Leitungen'
description: Konfigurieren Sie die cloudbasierte Netzwerküberwachung für Azure ExpressRoute-Leitungen. Dies umfasst die Überwachung über das private Peering mit ExpressRoute und das Microsoft-Peering.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: 837d1f78a9fde72137383e6c22adb8870997dc9b
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986038"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Konfigurieren des Netzwerkleistungsmonitors für ExpressRoute

In diesem Artikel konfigurieren Sie eine Netzwerkleistungsmonitor-Erweiterung zum Überwachen von ExpressRoute. Der Netzwerkleistungsmonitor (Network Performance Monitor, NPM) ist eine cloudbasierte Lösung für die Netzwerküberwachung, die die Konnektivität zwischen Azure-Cloudbereitstellungen und lokalen Standorten (Zweigstellen usw.) überwacht. Der Netzwerkleistungsmonitor ist Teil von Azure Monitor-Protokollen. Der Netzwerkleistungsmonitor bietet nun eine Erweiterung für ExpressRoute, durch die Sie die Netzwerkleistung über ExpressRoute-Verbindungen überwachen können, die dafür konfiguriert sind, privates Peering oder Microsoft-Peering zu verwenden. Wenn Sie den Netzwerkleistungsmonitor für ExpressRoute konfigurieren, können Sie Netzwerkprobleme erkennen, die identifiziert und entfernt werden sollen. Dieser Dienst ist auch für Azure Government Cloud verfügbar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Ihre Möglichkeiten:

* Überwachen der Verluste und der Latenz über verschiedene VNETs sowie das Einrichten von Warnungen

* Überwachen aller Pfade (einschließlich der redundanten) des Netzwerks

* Problembehandlung bei vorübergehenden und Point-in-Time-Netzwerkproblemen, die schwer zu replizieren sind

* Ermitteln eines bestimmten Segments des Netzwerks, das für die beeinträchtigte Leistung verantwortlich ist

* Abrufen von Durchsatz pro virtuellem Netzwerk (wenn Sie Agents in jedem VNET installiert haben)

* Anzeigen des ExpressRoute-Systemstatus von einem früheren Zeitpunkt aus

## <a name="workflow"></a><a name="workflow"></a>Workflow

Überwachungs-Agents werden auf mehreren Servern (lokal und in Azure) installiert. Die Agents kommunizieren miteinander, senden jedoch keine Daten, sondern TCP-Handshakepakete. Die Kommunikation zwischen den Agents ermöglicht es Azure, die Netzwerktopologie und den Pfad zuzuordnen, die der Datenverkehr nutzen kann.

1. Erstellen Sie einen NPM-Arbeitsbereich. Dies entspricht einem Log Analytics-Arbeitsbereich.
2. Installieren und konfigurieren Sie Software-Agents. (Sie müssen keine Software-Agents installieren und konfigurieren, wenn die Überwachung nur über Microsoft-Peering erfolgen soll.) 
    * Installieren Sie Überwachungs-Agents auf den lokalen Servern und den virtuellen Azure-Computern (für das private Peering).
    * Konfigurieren Sie die Einstellungen der Server der Überwachungs-Agents, um diesen die Kommunikation zu ermöglichen. (Öffnen Sie die Firewallports usw.)
3. Konfigurieren Sie Regeln für die Netzwerksicherheitsgruppen, um den auf virtuellen Azure-Computern installierten Überwachungs-Agents die Kommunikation mit den lokalen Überwachungs-Agents zu ermöglichen.
4. Richten Sie die Überwachung ein: automatisches Erkennen und Verwalten der Sichtbarkeit von Netzwerken im Netzwerkleistungsmonitor.

Wenn Sie bereits einen Netzwerkleistungsmonitor verwenden, um andere Objekte oder Dienste zu überwachen, und bereits einen Arbeitsbereich in einer der unterstützten Regionen besitzen, können Sie die Schritte 1 und 2 überspringen und Ihre Konfiguration mit Schritt 3 beginnen.

## <a name="step-1-create-a-workspace"></a><a name="configure"></a>Schritt 1: Erstellen eines Arbeitsbereichs

Erstellen Sie einen Arbeitsbereich im Abonnement mit den VNETs, die mit den ExpressRoute-Leitungen verknüpft sind.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) das Abonnement, in dem Peering der VNETs mit Ihrer ExpressRoute-Leitung eingerichtet ist. Suchen Sie anschließend über den **Marketplace** in der Liste der Dienste nach „Netzwerkleistungsmonitor“. Klicken Sie in die Ausgabe, um die Seite **Netzwerkleistungsmonitor** zu öffnen.

   >[!NOTE]
   >Sie können einen neuen Arbeitsbereich erstellen oder einen vorhandenen Arbeitsbereich verwenden. Wenn Sie einen vorhandenen Arbeitsbereich verwenden möchten, müssen Sie sicherstellen, dass der Arbeitsbereich zur neuen Abfragesprache migriert wurde. [Weitere Informationen](../azure-monitor/log-query/log-query-overview.md)
   >

   ![Portal](./media/how-to-npm/3.png)<br><br>
2. Klicken Sie im unteren Bereich der Hauptseite **Netzwerkleistungsmonitor** auf **Erstellen**, um die Seite **Network Performance Monitor – Create new solution** (Netzwerkleistungsmonitor – Neue Lösung erstellen) zu öffnen. Klicken Sie auf **Log Analytics Workspace – select a workspace** (Log Analytics-Arbeitsbereich – Arbeitsbereich auswählen), um die Seite „Arbeitsbereich“ zu öffnen. Klicken Sie auf **+ Neuen Arbeitsbereich erstellen**, um die Seite „Arbeitsbereich“ zu öffnen.
3. Klicken Sie auf der Seite **Log Analytics-Arbeitsbereich** auf **Neu erstellen**, und konfigurieren Sie die folgenden Einstellungen:

   * Log Analytics-Arbeitsbereich: Geben Sie einen Namen für Ihren Arbeitsbereich ein.
   * Abonnement: Wenn Sie über mehrere Abonnements verfügen, wählen Sie das Abonnement aus, das Sie dem neuen Arbeitsbereich zuordnen möchten.
   * Ressourcengruppe: Erstellen Sie eine Ressourcengruppe, oder verwenden Sie eine vorhandene.
   * Speicherort: Wird verwendet, um den Speicherort des Speicherkontos für die Protokolle der Agent-Verbindung anzugeben.
   * Tarif: Wählen Sie einen Tarif aus.
  
     >[!NOTE]
     >Die ExpressRoute-Verbindung kann sich in einer beliebigen Region in der Welt befinden. Sie muss sich nicht in der gleichen Region befinden wie der Arbeitsbereich.
     >
  
     ![Arbeitsbereich](./media/how-to-npm/4.png)<br><br>
4. Klicken Sie auf **OK**, um die Einstellungsvorlage zu speichern und bereitzustellen. Sobald die Vorlage überprüft wurde, klicken Sie auf **Erstellen**, um den Arbeitsbereich bereitzustellen.
5. Nachdem der Arbeitsbereich bereitgestellt wurde, navigieren Sie zu der Ressource **NetworkMonitoring(Name)** , die Sie erstellt haben. Überprüfen Sie die Einstellungen, und klicken Sie dann auf **Für die Lösung ist eine weitere Konfiguration erforderlich**.

   ![Zusätzliche Konfiguration](./media/how-to-npm/5.png)

## <a name="step-2-install-and-configure-agents"></a><a name="agents"></a>Schritt 2: Installieren und Konfigurieren von Agents

### <a name="21-download-the-agent-setup-file"></a><a name="download"></a>2.1: Laden Sie die Setup-Datei für den Agent herunter.

1. Wechseln Sie zur Registerkarte **Allgemeine Einstellungen** auf der Seite **Konfiguration des Netzwerkleistungsmonitors** für Ihre Ressource. Klicken Sie auf den Agent, der dem Prozessor des Servers aus dem Abschnitt **Installieren der Log Analytics-Agents** entspricht, und laden Sie die Setupdatei herunter.
2. Kopieren Sie dann die **Arbeitsbereich-ID** und den **Primärschlüssel**, und fügen Sie diese in Editor ein.
3. Laden Sie das PowerShell-Skript aus dem Abschnitt **Konfigurieren der Log Analytics-Agents für die Überwachung per TCP-Protokoll** herunter. Das PowerShell-Skript unterstützt Sie beim Öffnen des relevanten Firewallports für die TCP-Transaktionen.

   ![PowerShell-Skript](./media/how-to-npm/7.png)

### <a name="22-install-a-monitoring-agent-on-each-monitoring-server-on-each-vnet-that-you-want-to-monitor"></a><a name="installagent"></a>2.2: Installieren eines Überwachungs-Agents auf jedem Überwachungsserver (für jedes zu überwachende VNET)

Es wird empfohlen, dass Sie zur Bereitstellung von Redundanz mindestens zwei Agents auf jeder Seite der ExpressRoute-Verbindung (z.B. lokal, Azure-VNETs) installieren. Der Agent muss auf einem Windows Server-Computer (2008 SP1 oder höher) installiert werden. Die Überwachung von ExpressRoute-Leitungen unter Verwendung des Windows Desktop- oder Linux-Betriebssystems wird nicht unterstützt. Führen Sie die folgenden Schritte aus, um die Agents zu installieren:
   
  >[!NOTE]
  >Agents, die von SCOM (einschließlich [MMA](/previous-versions/system-center/system-center-2012-R2/dn465154(v=sc.12))) gepusht werden, sind möglicherweise nicht in der Lage, ihren Standort konsistent zu ermitteln, wenn sie in Azure gehostet werden. Es wird empfohlen, diese Agents nicht in Azure-VNETs zu verwenden, um ExpressRoute zu überwachen.
  >

1. Führen Sie das **Setup** aus, um den Agent auf jedem Server zu installieren, den Sie zum Überwachen von ExpressRoute verwenden möchten. Der Server, den Sie für die Überwachung verwenden, kann lokal oder ein virtueller Computer sein und benötigt Zugriff auf das Internet. Sie müssen mindestens einen Agent lokal und einen in jedem Netzwerksegment, das in Azure überwacht werden soll, installieren.
2. Klicken Sie auf der Seite **Willkommen** auf **Weiter**.
3. Lesen Sie die Seite **Lizenzbedingungen** durch, und klicken Sie anschließend auf **Ich stimme zu**.
4. Auf der Seite **Zielordner** können Sie den Standardinstallationsordner entweder ändern oder beibehalten. Klicken Sie anschließend auf **Weiter**.
5. Auf der Seite **Agent-Setupoptionen** können Sie auswählen, ob der Agent mit Azure Monitor-Protokollen oder Operations Manager verbunden werden soll. Sie können die Auswahl auch leer lassen, wenn Sie den Agent später konfigurieren möchten. Klicken Sie auf **Weiter**, nachdem Sie Ihre Auswahl getroffen haben.

   * Wenn Sie eine Verbindung mit **Azure Log Analytics** herstellen möchten, fügen Sie die **Arbeitsbereichs-ID** und den **Arbeitsbereichsschlüssel** (Primärschlüssel) ein, die Sie im vorherigen Abschnitt in Editor kopiert haben. Klicken Sie auf **Weiter**.

     ![ID und Schlüssel](./media/how-to-npm/8.png)
   * Wenn Sie eine Verbindung zu **Operations Manager** herstellen möchten, geben Sie auf der Seite **Verwaltungsgruppenkonfiguration** den **Namen der Verwaltungsgruppe**, den **Verwaltungsserver** und den **Verwaltungsserverport** ein. Klicken Sie auf **Weiter**.

     ![Operations Manager](./media/how-to-npm/9.png)
   * Wählen Sie auf der Seite **Agentaktionskonto**  entweder das Konto **Lokales System** oder **Domäne oder lokaler Computer** aus. Klicken Sie auf **Weiter**.

     ![Konto](./media/how-to-npm/10.png)
6. Überprüfen Sie Ihre Auswahl auf der Seite **Bereit zum Installieren**, und klicken Sie dann auf **Installieren**.
7. Klicken Sie auf der Seite **Die Konfiguration wurde erfolgreich abgeschlossen** auf **Fertig stellen**.
8. Nach Abschluss wird der Microsoft Monitoring Agent in der Systemsteuerung angezeigt. Dort können Sie Ihre Konfiguration überprüfen und sicherstellen, dass der Agent mit Azure Monitor-Protokollen verbunden ist. Nach dem Herstellen der Verbindung zeigt der Agent folgende Meldung an: **Der Microsoft Monitoring Agent hat erfolgreich eine Verbindung mit dem Microsoft Operations Management Suite-Dienst hergestellt.**

9. Wiederholen Sie diese Schritte für jedes VNET, das Sie überwachen müssen.

### <a name="23-configure-proxy-settings-optional"></a><a name="proxy"></a>2.3: Konfigurieren von Proxyeinstellungen (optional)

Wenn Sie einen Webproxy verwenden, um auf das Internet zuzugreifen, verwenden Sie folgende Schritte, um die Proxyeinstellungen für den Microsoft Monitoring Agent zu konfigurieren. Führen Sie diese Schritte für jeden Server aus. Wenn Sie viele Server konfigurieren müssen, ist es möglicherweise einfacher, diesen Prozess mithilfe eines Skripts zu automatisieren. Wenn dies der Fall ist, finden Sie weitere Informationen unter [So konfigurieren Sie Proxyeinstellungen für den Microsoft Monitoring Agent mithilfe eines Skripts](../azure-monitor/platform/agent-windows.md).

So konfigurieren Sie Proxyeinstellungen für den Microsoft Monitoring Agent über die Systemsteuerung:

1. Öffnen Sie die **Systemsteuerung**.
2. Öffnen Sie **Microsoft Monitoring Agent**.
3. Klicken Sie auf die Registerkarte **Proxyeinstellungen** .
4. Wählen Sie **Proxyserver verwenden** aus, und geben Sie die URL und gegebenenfalls die Portnummer ein. Wenn der Proxyserver eine Authentifizierung erfordert, geben Sie den Benutzernamen und das Kennwort für den Zugriff auf den Proxyserver ein.

   ![proxy](./media/how-to-npm/11.png)

### <a name="24-verify-agent-connectivity"></a><a name="verifyagent"></a>2.4: Überprüfen der Agentkonnektivität

Sie können problemlos überprüfen, ob die Agents kommunizieren.

1. Öffnen Sie die **Systemsteuerung** auf einem Server mit dem Überwachungs-Agent.
2. Öffnen Sie den **Microsoft Monitoring Agent**.
3. Klicken Sie auf die Registerkarte **Azure Log Analytics**.
4. In der Spalte **Status** sollte angezeigt werden, dass der Agent erfolgreich mit Azure Monitor-Protokollen verbunden wurde.

   ![status](./media/how-to-npm/12.png)

### <a name="25-open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>2.5: Öffnen der Firewallports auf den Servern des Überwachungs-Agents

Für die Verwendung des TCP-Protokolls müssen Sie die Firewallports öffnen, um sicherzustellen, dass die Überwachungs-Agents kommunizieren können.

Sie können ein PowerShell-Skript ausführen, um die Registrierungsschlüssel zu erstellen, die vom Netzwerkleistungsmonitor vorausgesetzt werden. Das Skript erstellt außerdem Regeln für die Windows-Firewall, damit Agents TCP-Verbindungen miteinander herstellen können. Die vom Skript erstellten Registrierungsschlüssel geben an, ob die Debugprotokolle und der Pfad zur Protokolldatei protokolliert werden sollen. Ferner definieren sie den für die Kommunikation verwendeten Agent-TCP-Port. Die Werte für diese Schlüssel werden vom Skript automatisch festgelegt. Ändern Sie diese Schlüssel nicht manuell.

Port 8084 wird standardmäßig geöffnet. Sie können einen benutzerdefinierten Port verwenden, indem Sie im Skript den Parameter „portNumber“ angeben. In diesem Fall müssen Sie jedoch den gleichen Port für alle Server angeben, auf denen Sie das Skript ausführen.

>[!NOTE]
>Das PowerShell-Skript „EnableRules“ konfiguriert Windows-Firewallregeln nur auf dem Server, auf dem das Skript ausgeführt wird. Bei Verwendung einer Netzwerkfirewall sollten Sie sicherstellen, dass diese den Datenverkehr zum TCP-Port erlaubt, der vom Netzwerkleistungsmonitor verwendet wird.
>
>

Öffnen Sie auf den Agent-Servern ein PowerShell-Fenster mit Administratorrechten. Führen Sie das PowerShell-Skript [EnableRules](https://aka.ms/npmpowershellscript) (das Sie zuvor heruntergeladen haben) aus. Verwenden Sie keine Parameter.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="step-3-configure-network-security-group-rules"></a><a name="opennsg"></a>Schritt 3: Konfigurieren von Regeln für Netzwerksicherheitsgruppen

Zum Überwachen von Agent-Servern in Azure konfigurieren Sie Regeln für Netzwerksicherheitsgruppen, um TCP-Datenverkehr auf einem Port zuzulassen, der vom Netzwerkleistungsmonitor für synthetische Transaktionen verwendet wird. Der Standardport ist 8084. Dadurch kann ein Überwachungs-Agent, der auf einem virtuellen Azure-Computer installiert ist, mit einem lokalen Überwachungs-Agent kommunizieren.

Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Netzwerksicherheitsgruppen](../virtual-network/tutorial-filter-network-traffic.md).

>[!NOTE]
>Stellen Sie sicher, dass Sie die Agents (sowohl den lokalen Server-Agent als auch den Azure-Server-Agent) installiert und das PowerShell-Skript ausgeführt haben, bevor Sie mit diesem Schritt fortfahren.
>

## <a name="step-4-discover-peering-connections"></a><a name="setupmonitor"></a>Schritt 4: Ermitteln von Peeringverbindungen

1. Navigieren Sie zur Übersichtskachel „Netzwerkleistungsmonitor“, indem Sie zur Seite **Alle Ressourcen** wechseln und dann auf den in eine Zulassungsliste aufgenommenen NPM-Arbeitsbereich klicken.

   ![NPM-Arbeitsbereich](./media/how-to-npm/npm.png)
2. Klicken Sie auf die Übersichtskachel **Netzwerkleistungsmonitor**, um das Dashboard zu öffnen. Das Dashboard enthält die Seite „ExpressRoute“, die anzeigt, dass ExpressRoute sich in einem nicht konfigurierten Zustand befindet. Klicken Sie auf **Featureeinrichtung**, um die Konfigurationsseite des Netzwerkleistungsmonitors zu öffnen.

   ![Featureeinrichtung](./media/how-to-npm/npm2.png)
3. Navigieren Sie auf der Konfigurationsseite zur Registerkarte „ExpressRoute-Peerings“, die sich im linken Bereich befindet. Klicken Sie auf **Jetzt ermitteln**.

   ![Ermitteln](./media/how-to-npm/13.png)
4. Nach Abschluss der Ermittlung sehen Sie eine Liste mit folgenden Elementen:
   * Alle Microsoft-Peeringverbindungen in der oder den ExpressRoute-Leitung(en), die diesem Abonnement zugeordnet ist/sind.
   * Alle privaten Peeringverbindungen, die mit den mit diesem Abonnement verknüpften VNets verbunden sind.
            
## <a name="step-5-configure-monitors"></a><a name="configmonitor"></a>Schritt 5: Konfigurieren von Monitoren

In diesem Abschnitt konfigurieren Sie die Monitore. Führen Sie die Schritte für den Typ des Peerings, den Sie überwachen möchten: **privates Peering**, oder **Microsoft-Peering**.

### <a name="private-peering"></a>Privates Peering

Wenn die Ermittlung abgeschlossen ist, werden Regeln für eindeutige **Verbindungsnamen** und **VNet-Namen** angezeigt. Diese Regeln sind zunächst deaktiviert.

![rules](./media/how-to-npm/14.png)

1. Aktivieren Sie das Kontrollkästchen **Dieses Peering überwachen**.
2. Aktivieren Sie das Kontrollkästchen **Integritätsüberwachung für dieses Peering aktivieren**.
3. Wählen Sie die Überwachungsbedingungen aus. Sie können benutzerdefinierte Schwellenwerte für das Generieren von Integritätsereignissen festlegen, indem Sie Schwellenwerte eingeben. Sobald der Wert einer Bedingung den für Sie festgelegten Schwellenwert für das ausgewählte Netzwerk-/Subnetzwerkpaar überschreitet, wird ein Integritätsereignis generiert.
4. Klicken Sie auf die „Lokale Agents“-Schaltfläche **Agents hinzufügen** , um die lokalen Server hinzuzufügen, über die Sie die private Peeringverbindung überwachen möchten. Stellen Sie sicher, dass Sie nur Agents auswählen, die über Konnektivität mit dem Microsoft-Dienstendpunkt verfügen, den Sie im Abschnitt für Schritt2 angegeben. Die lokalen Agents müssen den Endpunkt mithilfe der ExpressRoute-Verbindung erreichen können.
5. Speichern Sie die Einstellungen.
6. Nach dem Aktivieren der Regeln und dem Auswählen der Werte und Agents, die Sie überwachen möchten, gibt es eine Wartezeit von etwa 30 bis 60 Minuten, bis die Werte aufgefüllt und die Kacheln **ExpressRoute-Überwachung** verfügbar sind.

### <a name="microsoft-peering"></a>Microsoft-Peering

Klicken Sie für das Microsoft-Peering auf die Microsoft-Peeringverbindung(en), die Sie überwachen möchten, und konfigurieren Sie die Einstellungen.

1. Aktivieren Sie das Kontrollkästchen **Dieses Peering überwachen**. 
2. (Optional) Sie können den Zielendpunkt für den Microsoft-Dienst ändern. Standardmäßig wählt NPM einen Microsoft-Dienstendpunkt als Ziel. NPM überwacht die Konnektivität zwischen Ihren lokalen Servern und diesem Zielendpunkt über ExpressRoute. 
    * Um den Zielendpunkt zu ändern, klicken Sie auf den Link **(Bearbeiten)** unter **Ziel:** , und wählen Sie einen anderen Zielendpunkt für den Microsoft-Dienst in der Liste der URLs aus.
      ![Ziel bearbeiten](./media/how-to-npm/edit_target.png)<br>

    * Sie können eine benutzerdefinierte URL oder eine IP-Adresse verwenden. Diese Option ist besonders wichtig bei Verwendung des Microsoft-Peerings zum Herstellen einer Verbindung mit Azure-PaaS-Diensten, wie z.B. Azure Storage, SQL-Datenbanken und Websites, die über öffentliche IP-Adressen angeboten werden. Zu diesem Zweck klicken Sie auf den Link **(Stattdessen eine benutzerdefinierte URL oder IP-Adresse verwenden)** am Ende der URL-Liste, und geben Sie dann den öffentlichen Endpunkt des Azure-PaaS-Diensts ein, der über ExpressRoute-/Microsoft-Peering verbunden ist.
    ![Benutzerdefinierte URL](./media/how-to-npm/custom_url.png)<br>

    * Wenn Sie diese optionalen Einstellungen verwenden, stellen Sie sicher, dass hier nur der Microsoft-Dienstendpunkt aktiviert ist. Der Endpunkt muss mit ExpressRoute verbunden und für die lokalen Agents erreichbar sein.
3. Aktivieren Sie das Kontrollkästchen **Integritätsüberwachung für dieses Peering aktivieren**.
4. Wählen Sie die Überwachungsbedingungen aus. Sie können benutzerdefinierte Schwellenwerte für das Generieren von Integritätsereignissen festlegen, indem Sie Schwellenwerte eingeben. Sobald der Wert einer Bedingung den für Sie festgelegten Schwellenwert für das ausgewählte Netzwerk-/Subnetzwerkpaar überschreitet, wird ein Integritätsereignis generiert.
5. Klicken Sie auf die „Lokale Agents“-Schaltfläche **Agents hinzufügen** , um die lokalen Server hinzuzufügen, über die Sie die Microsoft-Peeringverbindung überwachen möchten. Stellen Sie sicher, dass Sie nur Agents auswählen, die über Konnektivität mit den Microsoft-Dienstendpunkten verfügen, den Sie im Abschnitt für Schritt2 angegeben. Die lokalen Agents müssen den Endpunkt mithilfe der ExpressRoute-Verbindung erreichen können.
6. Speichern Sie die Einstellungen.
7. Nach dem Aktivieren der Regeln und dem Auswählen der Werte und Agents, die Sie überwachen möchten, gibt es eine Wartezeit von etwa 30 bis 60 Minuten, bis die Werte aufgefüllt und die Kacheln **ExpressRoute-Überwachung** verfügbar sind.

## <a name="step-6-view-monitoring-tiles"></a><a name="explore"></a>Schritt 6: Anzeigen der Überwachungskacheln

Sobald Ihnen die Überwachungskacheln angezeigt werden, werden Ihre ExpressRoute-Verbindungen und -Verbindungsressourcen vom Netzwerkleistungsmonitor überwacht. Klicken Sie auf die Microsoft-Peeringkachel, um einen Drilldown in die Integrität der Microsoft-Peeringverbindungen auszuführen.

![Überwachungskacheln](./media/how-to-npm/15.png)

### <a name="network-performance-monitor-page"></a><a name="dashboard"></a>Seite des Netzwerkleistungsmonitors

Die Seite des Netzwerkleistungsmonitors enthält eine Seite für ExpressRoute, die eine Übersicht über die Integrität der ExpressRoute-Verbindungen und -Peerings anzeigt.

![Der Screenshot zeigt ein Dashboard mit einer Übersicht über die Integrität der ExpressRoute-Verbindungen und -Peerings.](./media/how-to-npm/dashboard.png)

### <a name="list-of-circuits"></a><a name="circuits"></a>Liste der Leitungen

Klicken Sie auf die Kachel **ExpressRoute-Verbindungen**, um eine Liste aller überwachten ExpressRoute-Verbindungen anzuzeigen. Sie können eine Verbindung auswählen und deren Integritätsstatus, Trenddiagramme für den Paketverlust, Bandbreitennutzung und Latenz anzuzeigen. Die Diagramme sind interaktiv. Sie können ein benutzerdefiniertes Zeitfenster für das Zeichnen der Diagramme auswählen. Sie können die Maus über einen Bereich des Diagramms ziehen, um dieses zu vergrößern und detaillierte Datenpunkte anzuzeigen.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend-of-loss-latency-and-throughput"></a><a name="trend"></a>Trend des Verlusts, der Latenz und des Durchsatzes

Die Diagramme für Bandbreite, Latenz und Verlust sind interaktiv. Sie können jeden Bereich dieser Diagramme mithilfe der Maussteuerung vergrößern. Sie können auch die Daten anderer Intervalle für Bandbreite, Latenz und Verlust anzeigen, indem Sie auf **Datum/Uhrzeit** klicken. Diese Angabe finden Sie oben links unter der Schaltfläche „Aktionen“.

![Trend](./media/how-to-npm/16.png)

### <a name="peerings-list"></a><a name="peerings"></a>Liste der Peerings

Klicken Sie auf die Kachel **Private Peerings** auf dem Dashboard, um eine Liste aller Verbindungen mit virtuellen Netzwerken über privates Peering anzuzeigen. Hier können Sie eine virtuelle Netzwerkverbindung auswählen und deren Integritätsstatus, Trenddiagramme für Paketverlust, Bandbreitennutzung und Latenz anzeigen lassen.

![Liste der Verbindungen](./media/how-to-npm/peerings.png)

### <a name="nodes-view"></a><a name="nodes"></a>Knoten anzeigen

Um die Liste aller Links zwischen den lokalen Knoten und den Azure-VMs/Microsoft-Dienstendpunkten für die ausgewählten ExpressRoute-Peeringverbindung anzuzeigen, klicken Sie auf **Knotenverbindungen anzeigen**. Sie können den Status jeder Verknüpfung sowie den Trend von Verlusten und Wartezeiten anzeigen, die ihnen zugeordnet sind.

![Knoten anzeigen](./media/how-to-npm/nodes.png)

### <a name="circuit-topology"></a><a name="topology"></a>Topologie der Verbindung

Klicken Sie auf die Kachel **Topologie**, um die Topologie der Verbindung anzuzeigen. Dadurch gelangen Sie zur Ansicht der Topologie für die ausgewählte Verbindung oder das ausgewählte Peering. Das Topologiediagramm enthält die Wartezeit für jedes Segment im Netzwerk. Jeder Layer-3-Hop wird von einem Knoten des Diagramms dargestellt. Durch das Klicken auf einen Hop werden weitere Details zum Hop angezeigt.

Sie können den Sichtbarkeitsgrad erhöhen, um lokale Hops einzuschließen, indem Sie den Schieberegler unter **Filter** bewegen. Das Bewegen des Schiebereglers nach links oder rechts vergrößert oder verkleinert die Anzahl der Hops im Topologiediagramm. Die Latenz ist für jedes Segment sichtbar, wodurch eine schnellere Isolation von Segmenten mit hoher Latenz in Ihrem Netzwerk ermöglicht wird.

![Filter](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Detaillierte Topologieansicht einer Verbindung

Diese Ansicht zeigt die VNET-Verbindungen.
![detaillierte Topologie](./media/how-to-npm/17.png)