---
title: Verbinden von Computern über das Log Analytics-Gateway | Microsoft-Dokumentation
description: Verbinden Sie Geräte und durch Operations Manager überwachte Computer mit dem Log Analytics-Gateway, um Daten an den Azure Automation- und Log Analytics-Dienst zu senden, wenn sie nicht über Internetzugriff verfügen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: dc4d1b852b0a498de0834731b2b1cd1225b9748b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008853"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Verbinden von Computern ohne Internetzugriff über das Log Analytics-Gateway in Azure Monitor

>[!NOTE]
>Durch den Übergang von Microsoft Operations Management Suite (OMS) in Microsoft Azure Monitor ändert sich die Terminologie. Dieser Artikel bezieht sich auf das OMS-Gateway als das Azure Log Analytics-Gateway. 
>

Dieser Artikel beschreibt, wie Sie die Kommunikation mit Azure Automation and Azure Monitor über das Log Analytics-Gateway konfigurieren, wenn Computer, die direkt verbunden sind oder von Operations Manager überwacht werden, über keinen Internetzugang verfügen. 

Das Log Analytics-Gateway ist ein HTTP-Forwardproxy, der HTTP-Tunnel mit dem Befehl „HTTP CONNECT“ unterstützt. Dieses Gateway sendet im Namen der Computer, die keine direkte Verbindung mit dem Internet herstellen können, Daten an Azure Automation und einen Log Analytics-Arbeitsbereich in Azure Monitor. 

Das Log Analytics-Gateway unterstützt Folgendes:

* Berichterstattung an dieselben Log Analytics-Arbeitsbereiche, die auf den einzelnen Agents dahinter sowie mit Azure Automation Hybrid Runbook Workern konfiguriert sind.  
* Direkt mit einem Log Analytics-Arbeitsbereich in Azure Monitor verbundene Windows-Computer mit Microsoft Monitoring Agent.
* Direkt mit einem Log Analytics-Arbeitsbereich in Azure Monitor verbundene Linux-Computer mit dem Log Analytics-Agent für Linux.  
* System Center Operations Manager 2012 SP1 mit UR7, Operations Manager 2012 R2 mit UR3 oder eine Verwaltungsgruppe in Operations Manager 2016 oder höher, die in Log Analytics integriert ist.  

Einige IT-Sicherheitsrichtlinien lassen keine Internetverbindung für Netzwerkcomputer zu. Diese nicht verbundenen Computer können beispielsweise POS-Geräte (Point of Sale) oder Server sein, die IT-Dienste unterstützen. Um diese Geräte mit Azure Automation oder einen Log Analytics-Arbeitsbereich zu verbinden, damit Sie sie verwalten und überwachen können, konfigurieren Sie sie so, dass sie direkt mit dem Log Analytics-Gateway kommunizieren. Das Log Analytics-Gateway kann Konfigurationsinformationen empfangen und Daten in ihrem Auftrag weiterleiten. Wenn die Computer mit dem Log Analytics-Agent konfiguriert werden, um eine direkte Verbindung mit einem Log Analytics-Arbeitsbereich herzustellen, kommunizieren die Computer stattdessen mit dem Log Analytics-Gateway.  

Das Log Analytics-Gateway überträgt Daten von den Agents direkt an den Dienst. Die Daten werden während der Übertragung nicht analysiert, und das Gateway nimmt keine Zwischenspeicherung von Daten vor, wenn die Verbindung mit dem Dienst unterbrochen wird. Kann das Gateway nicht mit dem Dienst kommunizieren, wird der Agent weiterhin ausgeführt und stellt die gesammelten Daten in die Warteschlange auf dem Datenträger des überwachten Computers. Nachdem die Verbindung wiederhergestellt wurde, sendet der Agent die gesammelten zwischengespeicherten Daten an Azure Monitor.

Wenn eine Operations Manager-Verwaltungsgruppe in Log Analytics integriert wird, können die Verwaltungsserver so konfiguriert werden, dass sie eine Verbindung mit dem Log Analytics-Gateway herstellen, um Konfigurationsinformationen zu empfangen und gesammelte Daten abhängig von der aktivierten Lösung zu senden.  Operations Manager-Agents senden einige Daten an den Verwaltungsserver. Agents können z.B. Operations Manager-Warnungen, Daten zur Konfigurationsbewertung, Instanz-Speicherplatzdaten und Kapazitätsdaten senden. Andere umfangreiche Daten wie etwa IIS-Protokolle (Internet Information Services, Internetinformationsdienste), Leistungsdaten und Sicherheitsereignisse werden direkt an das Log Analytics-Gateway gesendet. 

Wenn mindestens ein Operations Manager-Gatewayserver zur Überwachung nicht vertrauenswürdiger Systeme in einem Umkreisnetzwerk oder einem isolierten Netzwerk bereitgestellt wird, können diese Server nicht mit einem Log Analytics-Gateway kommunizieren.  Operations Manager-Gatewayserver können nur mit einem Verwaltungsserver kommunizieren.  Wenn eine Operations Manager-Verwaltungsgruppe für die Kommunikation mit dem Log Analytics-Gateway konfiguriert ist, werden die Proxykonfigurationsinformationen automatisch an alle per Agent verwalteten Computer verteilt, die zum Sammeln von Protokolldaten für Azure Monitor konfiguriert sind. Das gilt auch, wenn die Einstellung leer ist.

Zur Gewährleistung der Hochverfügbarkeit für direkt verbundene Gruppen oder Operations Management-Gruppen, die über das Gateway mit einem Log Analytics-Arbeitsbereich kommunizieren, leiten Sie Datenverkehr mithilfe des Netzwerklastenausgleichs (NLB) um und verteilen ihn auf mehrere Gatewayserver. Wenn ein Gatewayserver ausfällt, wird der Datenverkehr auf diese Weise an einen anderen verfügbaren Knoten umgeleitet.  

Der Computer, auf dem das Log Analytics-Gateway ausgeführt wird, benötigt den Windows-Agent von Log Analytics, um die Dienstendpunkte zu identifizieren, mit denen das Gateway kommunizieren muss. Der Agent muss das Gateway auch anweisen, an dieselben Arbeitsbereiche zu berichten, mit denen die Agents oder die Operations Manager-Verwaltungsgruppe hinter dem Gateway konfiguriert sind. Diese Konfiguration ermöglicht dem Gateway und dem Agent die Kommunikation mit ihrem zugewiesenen Arbeitsbereich.

Ein Gateway kann mehrfach vernetzt mit bis zu vier Arbeitsbereichen sein. Dies ist die Gesamtzahl der Arbeitsbereiche, die ein Windows-Agent unterstützt.  

Jeder Agent benötigt eine Netzwerkverbindung mit dem entsprechenden Gateway, damit Agents automatisch Daten an das und vom Gateway übertragen können. Sie sollten das Gateway nicht auf einem Domänencontroller installieren. Bei Linux-Computern, die sich hinter einem Gatewayserver befinden, kann die Methode der [Installation mit einem Wrapperskript](agent-linux.md#install-the-agent-using-wrapper-script) nicht verwendet werden, um den Log Analytics-Agent für Linux zu installieren. Der Agent muss manuell heruntergeladen, auf den Computer kopiert und manuell installiert werden, da das Gateway nur die Kommunikation mit den zuvor erwähnten Azure-Diensten unterstützt.

Die folgende Abbildung zeigt den Datenfluss von direkten Agents durch das Gateway an Azure Automation und Log Analytics. Die Proxykonfiguration des Agents muss mit dem Port übereinstimmen, mit dem das Log Analytics-Gateway konfiguriert ist.  

![Abbildung zur direkten Kommunikation von Agents mit Diensten](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Die folgende Abbildung zeigt den Datenfluss von einer Operations Manager-Verwaltungsgruppe an Log Analytics.   

![Abbildung zur Kommunikation von Operations Manager mit Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Einrichten des Systems

Computer, die das Log Analytics-Gateway ausführen, müssen über die folgende Konfiguration verfügen:

* Windows 10, Windows 8.1 oder Windows 7
* Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 oder Windows Server 2008
* Microsoft .NET Framework 4.5
* Vierkernprozessor und 8 GB Arbeitsspeicher (Mindestanforderung) 
* [Log Analytics-Agent für Windows](agent-windows.md), konfiguriert für die Kommunikation mit demselben Arbeitsbereich wie die Agents, die über das Gateway kommunizieren.

### <a name="language-availability"></a>Verfügbare Sprachen

Das Log Analytics-Gateway ist in den folgenden Sprachen verfügbar:

- Chinesisch (vereinfacht)
- Chinesisch (traditionell)
- Tschechisch
- Niederländisch
- Englisch
- Französisch
- Deutsch
- Ungarisch
- Italienisch
- Japanisch
- Koreanisch
- Polnisch
- Portugiesisch (Brasilien)
- Portugiesisch (Portugal)
- Russisch
- Spanisch (international)

### <a name="supported-encryption-protocols"></a>Unterstützte Verschlüsselungsprotokolle

Das Log Analytics-Gateway unterstützt nur Transport Layer Security (TLS) 1.0, 1.1 und 1.2.  Secure Sockets Layer (SSL) wird nicht unterstützt.  Um die Sicherheit der Daten während der Übertragung an Log Analytics zu gewährleisten, konfigurieren Sie das Gateway so, dass es mindestens TLS 1.2 verwendet. Ältere Versionen von TLS- oder SSL sind anfällig. Obwohl sie zurzeit Abwärtskompatibilität zulassen, sollten Sie diese älteren Versionen vermeiden.  

Weitere Informationen finden Sie unter [Senden von Daten über TLS 1.2](./data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Unterstützte Anzahl von Agent-Verbindungen

Die folgende Tabelle zeigt in etwa, wie viele Agents mit einem Gatewayserver kommunizieren können. Die Unterstützung basiert auf Agents, die alle 6 Sekunden ungefähr 200 KB Daten hochladen. Das Datenvolumen pro getestetem Agent beträgt etwa 2,7 GB pro Tag.

|Gateway |Unterstützte Agents (ungefähr)|  
|--------|----------------------------------|  
|CPU: Intel Xeon-Prozessor E5-2660 v3 \@ 2,6 GHz, 2 Kerne<br> Memory: 4 GB<br> Netzwerkbandbreite: 1 GBit/s| 600|  
|CPU: Intel Xeon-Prozessor E5-2660 v3 \@ 2,6 GHz, 4 Kerne<br> Memory: 8 GB<br> Netzwerkbandbreite: 1 GBit/s| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Herunterladen des Log Analytics-Gateways

Rufen Sie die neueste Version der Log Analytics-Gatewaysetupdatei aus dem Microsoft Download Center ([Downloadlink](https://go.microsoft.com/fwlink/?linkid=837444)) oder dem Azure-Portal ab.

Um das Log Analytics-Gateway aus dem Azure-Portal abzurufen, gehen Sie folgendermaßen vor:

1. Durchsuchen Sie die Liste mit den Diensten, und wählen Sie **Log Analytics** aus. 
1. Wählen Sie einen Arbeitsbereich aus.
1. Klicken Sie auf dem Blatt für Ihren Arbeitsbereich unter **Allgemein** auf **Schnellstart**. 
1. Wählen Sie unter **Datenquelle zum Herstellen einer Verbindung mit dem Arbeitsbereich auswählen** die Option **Computer** aus.
1. Wählen Sie auf dem Blatt **Direkt-Agent** die Option **Log Analytics-Gateway herunterladen** aus.
 
   ![Screenshot der zum Herunterladen des Log Analytics-Gateways erforderlichen Schritte](./media/gateway/download-gateway.png)

oder 

1. Klicken Sie in Ihrem Arbeitsbereichblatt unter **Einstellungen** auf **Erweiterte Einstellungen**.
1. Navigieren Sie zu **Verbundene Quellen** > **Windows-Server**, und wählen Sie **Log Analytics-Gateway herunterladen** aus.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Installieren des Log Analytics-Gateways mit dem Setup-Assistenten

Führen Sie zum Installieren des Gateways mit dem Setup-Assistenten die folgenden Schritte aus. 

1. Doppelklicken Sie im Zielordner auf **Log Analytics gateway.msi**.
1. Wählen Sie auf der Seite **Willkommen** die Option **Weiter** aus.

   ![Screenshot der Homepage des Gateway-Setup-Assistenten](./media/gateway/gateway-wizard01.png)

1. Wählen Sie auf der Seite **Lizenzvertrag** die Option **Ich stimme den Bedingungen des Lizenzvertrags zu.** aus, um den Lizenzbestimmungen zuzustimmen, und wählen Sie dann **Weiter** aus.
1. Gehen Sie auf der Seite für die **Port- und Proxyadresse** wie folgt vor:

   a. Geben Sie die für das Gateway zu verwendende TCP-Portnummer ein. Das Setup verwendet diese Portnummer, um eine Eingangsregel für die Windows-Firewall zu konfigurieren.  Der Standardwert ist 8080.
      Zulässig sind Portnummern im Bereich zwischen 1 und 65535. Wenn die Eingabe außerhalb dieses Bereichs liegt, wird eine Fehlermeldung angezeigt.

   b. Falls der Server, auf dem das Gateway installiert ist, über einen Proxy kommunizieren muss, geben Sie die Proxyadresse ein, mit der das Gateway eine Verbindung herstellen muss. Geben Sie z. B. `http://myorgname.corp.contoso.com:80` ein  Wenn Sie dieses Feld leer lassen, versucht das Gateway, eine direkte Verbindung mit dem Internet herzustellen.  Falls der Proxyserver eine Authentifizierung erfordert, geben Sie einen Benutzernamen und ein Kennwort ein.

   c. Wählen Sie **Weiter** aus.

   ![Screenshot der Konfiguration für den Gatewayproxy](./media/gateway/gateway-wizard02.png)

1. Falls Microsoft Update nicht aktiviert ist, wird die Seite „Microsoft Update“ angezeigt, auf der Sie Microsoft Update aktivieren können. Treffen Sie eine Auswahl, und klicken Sie anschließend auf **Weiter**. Fahren Sie andernfalls mit dem nächsten Schritt fort.
1. Übernehmen Sie auf der Seite **Zielordner** entweder den Standardordner „C:\Programme\OMS Gateway“, oder geben Sie den Speicherort ein, an dem Sie das Gateway installieren möchten. Wählen Sie **Weiter** aus.
1. Wählen Sie auf der Seite **Bereit zur Installation** die Option **Installieren** aus. Wenn die Benutzerkontensteuerung eine Installationsberechtigung anfordert, wählen Sie **Ja** aus.
1. Klicken Sie nach Abschluss des Setups auf **Fertig stellen**. Überprüfen Sie, ob der Dienst ausgeführt wird: Öffnen Sie dazu das Snap-In „services.msc“, und überprüfen Sie, ob **OMS-Gateway** in der Liste mit den Diensten angezeigt wird und den Status **Wird ausgeführt** aufweist.

   ![Screenshot der lokalen Dienste, der zeigt, dass das OMS-Gateway ausgeführt wird](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>Installieren des Log Analytics-Gateways über die Befehlszeile

Die heruntergeladene Datei für das Gateway ist ein Windows Installer-Paket, mit dem die automatische Installation über die Befehlszeile oder mit einer anderen automatisierten Methode unterstützt wird. Wenn Sie nicht mit den standardmäßigen Befehlszeilenoptionen für Windows Installer vertraut sind, finden Sie entsprechende Informationen unter [Command-line options](/windows/desktop/msi/command-line-options) (Befehlszeilenoptionen).
 
In der folgenden Tabelle sind die beim Setup unterstützten Parameter aufgeführt.

|Parameter| Notizen|
|----------|------| 
|PORTNUMBER | Nummer des TCP-Ports für das Gateway, an dem gelauscht werden soll |
|PROXY | IP-Adresse des Proxyservers |
|INSTALLDIR | Vollqualifizierter Pfad zum Angeben des Installationsverzeichnisses für Dateien der Gatewaysoftware |
|USERNAME | Benutzer-ID für die Authentifizierung beim Proxyserver |
|PASSWORD | Kennwort für die Benutzer-ID für die Authentifizierung beim Proxy |
|LicenseAccepted | Geben Sie den Wert **1** an, um zu überprüfen, ob Sie dem Lizenzvertrag zugestimmt haben. |
|HASAUTH | Geben Sie den Wert **1** an, wenn die Parameter USERNAME und PASSWORD angegeben sind. |
|HASPROXY | Geben Sie bei der Angabe einer IP-Adresse für den Parameter **PROXY** den Wert **1** an. |

Geben Sie Folgendes ein, um das Gateway automatisch zu installieren und mit einer spezifischen Proxyadresse und einer spezifischen Portnummer zu konfigurieren:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

Mit der Befehlszeilenoption „/qn“ wird das Setup bei der automatischen Installation ausgeblendet, mit der Befehlszeilenoption „/qb“ wird das Setup angezeigt.  

Wenn Sie für die Authentifizierung beim Proxy Anmeldeinformationen angeben müssen, geben Sie Folgendes ein:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

Nach der Installation können Sie mit den folgenden PowerShell-Cmdlets bestätigen, dass die Einstellungen akzeptiert werden (mit Ausnahme des Benutzernamens und Kennworts):

- **Get-OMSGatewayConfig:** gibt den TCP-Port zurück, für den das Gateway zum Lauschen konfiguriert ist.
- **Get-OMSGatewayRelayProxy:** gibt die IP-Adresse des Proxyservers zurück, den Sie für die Kommunikation mit dem Gateway konfiguriert haben.

## <a name="configure-network-load-balancing"></a>Konfigurieren des Netzwerklastenausgleichs

Für Hochverfügbarkeit können Sie das Gateway mit Netzwerklastenausgleich (Network Load Balancing, NLB) konfigurieren. Hierzu können Sie entweder den [Netzwerklastenausgleich (NLB)](/windows-server/networking/technologies/network-load-balancing) von Microsoft, [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) oder einen hardwarebasierten Lastenausgleich verwenden. Beim Lastenausgleich werden die angeforderten Verbindungen von Log Analytics-Agents oder Operations Manager-Verwaltungsservern zur Verwaltung des Datenverkehrs für alle Knoten umgeleitet. Wenn ein Gatewayserver ausfällt, wird der Datenverkehr zu anderen Knoten umgeleitet.

### <a name="microsoft-network-load-balancing"></a>Microsoft-Netzwerklastenausgleich

Informationen zum Entwerfen und Bereitstellen eines Netzwerklastenausgleichs-Clusters unter Windows Server 2016 finden Sie unter [Netzwerklastenausgleich](/windows-server/networking/technologies/network-load-balancing). In den folgenden Schritten wird das Konfigurieren eines Clusters mit Microsoft-Netzwerklastenausgleich beschrieben.  

1. Melden Sie sich mit einem Administratorkonto bei dem Windows-Server an, der dem NLB-Cluster angehört.  
2. Öffnen Sie im Server-Manager den Netzwerklastenausgleich-Manager, klicken Sie auf **Tools**, und klicken Sie anschließend auf **Netzwerklastenausgleich-Manager**.
3. Um einen Log Analytics-Gatewayserver mit dem installierten Microsoft Monitoring Agent zu verbinden, klicken Sie mit der rechten Maustaste auf die IP-Adresse des Clusters, und klicken Sie dann auf **Host dem Cluster hinzufügen**. 

    ![Netzwerklastenausgleich-Manager – Host zum Cluster hinzufügen](./media/gateway/nlb02.png)
 
4. Geben Sie die IP-Adresse des Gatewayservers ein, mit dem Sie eine Verbindung herstellen möchten. 

    ![Netzwerklastenausgleich-Manager – Host dem Cluster hinzufügen: Verbinden](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Weitere Informationen zum Entwerfen und Bereitstellen von Azure Load Balancer finden Sie unter [Was ist Azure Load Balancer?](../../load-balancer/load-balancer-overview.md). Um einen grundlegenden Lastausgleich bereitzustellen, folgen Sie den in diesem [Schnellstart](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) beschriebenen Schritten mit Ausnahme der im Abschnitt **Erstellen des Back-End-Servers** beschriebenen Schritte.   

> [!NOTE]
> Die Konfiguration von Azure Load Balancer mit der **SKU „Basic“** erfordert, dass virtuelle Azure-Computer zu einer Verfügbarkeitsgruppe gehören. Weitere Informationen zu Verfügbarkeitsgruppen finden Sie unter [Verwalten der Verfügbarkeit virtueller Windows-Computer in Azure](../../virtual-machines/manage-availability.md). Wenn Sie vorhandene virtuelle Computer einer Verfügbarkeitsgruppe hinzufügen möchten, finden Sie weitere Informationen unter [Festlegen einer Azure Resource Manager-VM-Verfügbarkeitsgruppe](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4).
> 

Nachdem der Lastausgleich erstellt wurde, muss ein Back-End-Pool erstellt werden, der den Datenverkehr auf mindestens einen Gatewayserver verteilt. Führen Sie die im Abschnitt [Erstellen von Ressourcen für den Lastenausgleich](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) im Schnellstartartikel beschriebenen Schritte aus.  

>[!NOTE]
>Der Integritätstest sollte so konfiguriert werden, dass er den TCP-Port des Gatewayservers verwendet. Abhängig von der Reaktion auf Integritätsüberprüfungen werden der Lastenausgleichsrotation durch den Integritätstest dynamisch Gatewayserver hinzugefügt oder aus der Rotation entfernt. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Konfigurieren des Log Analytics-Agents und der Operations Manager-Verwaltungsgruppe

Im diesem Abschnitt erfahren Sie, wie Sie direkt verbundene Log Analytics-Agents, eine Operations Manager-Verwaltungsgruppe oder Azure Automation Hybrid Runbook Worker mit dem Log Analytics-Gateway konfigurieren, um die Kommunikation mit Azure Automation und Log Analytics zu ermöglichen.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Konfigurieren eines eigenständigen Log Analytics-Agents

Beim Konfigurieren eines Log Analytics-Agents ersetzen Sie den Proxyserverwert durch die IP-Adresse und die Portnummer des Log Analytics-Gatewayservers. Wenn Sie mehrere Gatewayserver hinter einem Lastenausgleich bereitgestellt haben, handelt es sich bei der Log Analytics-Agent-Proxykonfiguration um die virtuelle IP-Adresse des Lastenausgleichs.  

>[!NOTE]
>Informationen zur Installation des Log Analytics-Agents auf dem Gateway und auf Windows-Computern, die direkt mit Log Analytics verbunden sind, finden Sie unter [Verbinden von Windows-Computern mit dem Log Analytics-Dienst in Azure](agent-windows.md). Informationen zum Verbinden von Linux-Computern finden Sie unter [Verbinden von Linux-Computern mit Azure Monitor](agent-linux.md). 
>

Nach der Installation des Agents auf dem Gatewayserver konfigurieren Sie ihn für die Kommunikation mit mindestens einem Arbeitsbereich-Agent, der mit dem Gateway kommuniziert. Wenn der Log Analytics-Windows-Agent nicht auf dem Gateway installiert ist, wird Ereignis 300 in das OMS-Gatewayereignisprotokoll geschrieben und gibt an, dass der Agent installiert werden muss. Wenn der Agent installiert ist, aber nicht zum Berichten an denselben Arbeitsbereich konfiguriert ist wie die Agents, die darüber kommunizieren, wird Ereignis 105 in das gleiche Protokoll geschrieben und gibt an, dass der Agent auf dem Gateway zum Berichten an denselben Arbeitsbereich konfiguriert werden muss wie die Agents, die mit dem Gateway kommunizieren.

Nachdem Sie die Konfiguration abgeschlossen haben, starten Sie den **OMS-Gatewaydienst** neu, um die Änderungen zu übernehmen. Andernfalls lehnt das Gateway Agents ab, die mit Log Analytics zu kommunizieren versuchen, und meldet das Ereignis 105 im OMS-Gatewayereignisprotokoll. Dies gilt auch beim Hinzufügen oder Entfernen eines Arbeitsbereichs aus der Agent-Konfiguration auf dem Gatewayserver.

Weitere Informationen zum Automation Hybrid Runbook Worker finden Sie unter [Automatisieren von Ressourcen in Ihrem Datencenter oder Ihrer Cloud mit Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Konfigurieren von Operations Manager, wenn alle Agents den gleichen Proxyserver verwenden

Die Operations Manager-Proxykonfiguration wird automatisch auf alle Agents angewendet, die Operations Manager unterstellt sind. Das gilt auch, wenn die Einstellung leer ist.  

Um das OMS-Gateway für die Unterstützung von Operations Manager zu verwenden, müssen Sie über Folgendes verfügen:

* Auf dem OMS-Gatewayserver muss Microsoft Monitoring Agent (Version 8.0.10900.0 oder höher) installiert und mit denselben Log Analytics-Arbeitsbereichen konfiguriert sein, denen Ihre Verwaltungsgruppe unterstellt ist.
* Internetkonnektivität. Alternativ muss das OMS-Gateway mit einem Proxyserver verbunden sein, der über einen Internetzugang verfügt.

> [!NOTE]
> Wenn Sie keinen Wert für das Gateway angeben, werden mithilfe leere Werte an alle Agents gepusht.
>

Wenn sich Ihre Operations Manager-Verwaltungsgruppe erstmals bei einem Log Analytics-Arbeitsbereich registriert, ist die Option zum Angeben der Proxykonfiguration für die Verwaltungsgruppe in der Betriebskonsole nicht verfügbar. Diese Option ist nur verfügbar, wenn die Verwaltungsgruppe beim Dienst registriert wurde.  

Um die Integration zu konfigurieren, aktualisieren Sie die Systemproxykonfiguration mit Netsh auf dem System, auf dem Sie die Betriebskonsole ausführen, und auf allen Verwaltungsservern in der Verwaltungsgruppe. Folgen Sie diesen Schritten:

1. Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten:

   a. Wählen Sie **Start** aus, und geben Sie dann **cmd** ein.  

   b. Klicken Sie mit der rechten Maustaste auf **Eingabeaufforderung**, und wählen Sie **Als Administrator ausführen** aus.  

1. Geben Sie den folgenden Befehl ein:

   `netsh winhttp set proxy <proxy>:<port>`

Nach Abschluss der Log Analytics-Integration können Sie die Änderung durch Ausführen von `netsh winhttp reset proxy` entfernen. Verwenden Sie dann in der Betriebskonsole die Option **Proxyserver konfigurieren**, um den Log Analytics-Gatewayserver anzugeben. 

1. Wählen Sie in der Operations Manager-Konsole unter **Operations Management Suite** die Option **Verbindung** aus, und wählen Sie dann **Proxyserver konfigurieren** aus.

   ![Screenshot von Operations Manager mit der Auswahl „Proxyserver konfigurieren“](./media/gateway/scom01.png)

1. Wählen Sie **Proxyserver für Zugriff auf den Operations Management Suite-Dienst verwenden** aus, und geben Sie die IP-Adresse des Log Analytics-Gatewayservers oder die virtuelle IP-Adresse des Lastenausgleichs ein. Achten Sie darauf, dass Sie mit dem Präfix `http://` beginnen.

   ![Screenshot von Operations Manager mit der Proxyserveradresse](./media/gateway/scom02.png)

1. Wählen Sie **Fertig stellen** aus. Ihre Operations Manager-Verwaltungsgruppe ist nun so konfiguriert, dass sie über den Gatewayserver mit dem Log Analytics-Dienst kommuniziert.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Konfigurieren von Operations Manager, wenn bestimmte Agents einen Proxyserver verwenden

Bei großen oder komplexen Umgebungen soll der Log Analytics-Gatewayserver unter Umständen nur von bestimmten Servern (oder Gruppen) verwendet werden.  Für diese Server können Sie den Operations Manager-Agent nicht direkt aktualisieren, da dieser Wert durch den globalen Wert für die Verwaltungsgruppe überschrieben wird.  Setzen Sie stattdessen die Regel außer Kraft, die verwendet wird, um diese Werte zu pushen.  

> [!NOTE] 
> Verwenden Sie diese Konfigurationstechnik, wenn Sie mehrere Log Analytics-Gatewayserver in Ihrer Umgebung zulassen möchten.  So können beispielsweise spezifische Log Analytics-Gatewayserver für einzelne Regionen angegeben werden.
>

So konfigurieren Sie bestimmte Server oder Gruppen für die Verwendung des Log Analytics-Gatewayservers: 

1. Öffnen Sie die Operations Manager-Konsole, und wählen Sie den Arbeitsbereich **Erstellen** aus.  
1. Klicken Sie im Arbeitsbereich „Erstellen“ auf **Regeln**. 
1. Klicken Sie auf der Operations Manager-Symbolleiste auf die Schaltfläche **Bereich**. Sollte diese Schaltfläche nicht verfügbar sein, vergewissern Sie sich, dass im Bereich **Überwachung** kein Ordner, sondern ein Objekt ausgewählt ist. Im Dialogfeld **Management Pack-Objekte in Bereiche einteilen** wird eine Liste mit allgemeinen Zielklassen, Gruppen oder Objekten angezeigt. 
1. Geben Sie in das **Suchfeld** den Suchbegriff **Integritätsdienst** ein, und wählen Sie den Dienst in der Liste aus. Klicken Sie auf **OK**.  
1. Suchen Sie nach **Advisor Proxy Setting Rule** (Proxyeinstellungsregel für Ratgeber). 
1. Wählen Sie auf der Operations Manager-Symbolleiste **Außerkraftsetzungen** aus, und zeigen Sie anschließend auf **Regel außer Kraft setzen\Für ein bestimmtes Objekt der Klasse: Integritätsdienst**, und wählen Sie ein Objekt in der Liste aus.  Sie können auch eine benutzerdefinierte Gruppe mit dem Integritätsdienstobjekt der Server, auf die Sie diese Außerkraftsetzung anwenden möchten, erstellen. Wenden Sie die Außerkraftsetzung anschließend auf Ihre benutzerdefinierte Gruppe an.
1. Fügen Sie im Dialogfeld **Außerkraftsetzungseigenschaften** in der Spalte **Außerkraftsetzung** neben dem Parameter **WebProxyAddress** ein Häkchen hinzu.  Geben Sie im Feld **Außerkraftsetzungswert** die URL des Log Analytics-Gatewayservers ein. Achten Sie darauf, dass Sie mit dem Präfix `http://` beginnen.  

    >[!NOTE]
    > Sie müssen die Regel nicht aktivieren. Sie wird bereits automatisch mit einer Außerkraftsetzung aus dem Microsoft System Center Advisor-Management Pack für die Außerkraftsetzung von sicheren Verweisen verwaltet, das auf die Überwachungsservergruppe von Microsoft System Center Advisor ausgerichtet ist.
    > 

1. Wählen Sie in der Liste **Ziel-Management Pack auswählen** ein Management Pack aus, oder klicken Sie auf **Neu**, um ein neues, nicht versiegeltes Management Pack zu erstellen. 
1. Wenn Sie fertig sind, klicken Sie auf **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Konfigurieren für Automation Hybrid Runbook Worker

Für den Fall, dass Ihre Umgebung Automation Hybrid Runbook Worker enthält, führen Sie die folgenden Schritte aus, um das Gateway für die Unterstützung der Worker zu konfigurieren.

Die URL für jede Region finden Sie in der Dokumentation zu Automation im Abschnitt [Konfigurieren des Netzwerks](../../automation/automation-hybrid-runbook-worker.md#network-planning).

Wenn Ihr Computer automatisch als Hybrid Runbook Worker registriert wird, z.B. wenn die Lösung für die Updateverwaltung für eine oder mehrere VMs aktiviert ist, gehen Sie wie folgt vor:

1. Fügen Sie die Auftragslaufzeit-Datendienst-URLs der Liste zulässiger Hosts auf dem Log Analytics-Gateway hinzu. Beispiel: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Starten Sie den Log Analytics-Gatewaydienst mithilfe des folgenden PowerShell-Cmdlets neu: `Restart-Service OMSGatewayService`

Wenn Ihr Computer mithilfe des Cmdlets für die Hybrid Runbook Worker-Registrierung mit Azure Automation verknüpft ist, gehen Sie wie folgt vor:

1. Fügen Sie die Agent-Dienstregistrierungs-URL der Liste zulässiger Hosts auf dem Log Analytics-Gateway hinzu. Beispiel: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Fügen Sie die Auftragslaufzeit-Datendienst-URLs der Liste zulässiger Hosts auf dem Log Analytics-Gateway hinzu. Beispiel: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Starten Sie den Log Analytics-Gatewaydienst neu.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Nützliche PowerShell-cmdlets

Sie können Cmdlets verwenden, um die Aufgaben zum Aktualisieren der Konfigurationseinstellungen des Log Analytics-Gateways auszuführen. Bevor Sie Cmdlets verwenden, stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind:

1. Installieren Sie das Log Analytics-Gateway (Microsoft Windows Installer).
1. Öffnen Sie ein PowerShell-Konsolenfenster.
1. Importieren Sie das Modul, indem Sie diesen Befehl eingeben: `Import-Module OMSGateway`
1. Wenn im vorherigen Schritt kein Fehler aufgetreten ist, wurde das Modul erfolgreich importiert, und die Cmdlets können verwendet werden. Geben Sie `Get-Module OMSGateway` ein.
1. Nachdem Sie die Cmdlets verwendet haben, um Änderungen vorzunehmen, starten Sie den OMS-Gatewaydienst neu.

Wenn in Schritt 3 ein Fehler auftritt, wurde das Modul nicht importiert. Dieser Fehler kann auftreten, wenn PowerShell das Modul nicht findet. Sie finden das Modul im Installationspfad des OMS-Gateways: *C:\Programme\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parameter** | **Beschreibung** | **Beispiel** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Schlüssel |Ruft die Konfiguration des Diensts ab |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Schlüssel (erforderlich) <br> Wert |Ändert die Konfiguration des Diensts |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Ruft die Adresse des Relays (Upstreamproxy) ab |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adresse<br> Username<br> Kennwort (sichere Zeichenfolge) |Legt die Adresse (und Anmeldeinformationen) des Relays (Upstreamproxy) fest |1. Legen Sie einen Relay-Proxy und Anmeldeinformationen fest:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Legen Sie einen Relay-Proxy fest, der keine Authentifizierung erfordert: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Löschen Sie die Relay-Proxyeinstellung:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Ruft den derzeit zulässigen Host ab (nur lokal konfigurierter zulässiger Host, nicht automatisch heruntergeladene zulässige Hosts) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (erforderlich) |Fügt den Host der Liste zulässiger Hosts hinzu |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (erforderlich) |Entfernt den Host aus der Liste zulässiger Hosts |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Antragsteller (erforderlich) |Fügt den Clientzertifikatantragsteller der Liste zulässiger Hosts hinzu |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Antragsteller (erforderlich) |Entfernt den Clientzertifikatantragsteller aus der Liste zulässiger Hosts |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Ruft die derzeit zulässigen Clientzertifikatantragsteller ab (nur lokal konfigurierte zulässiger Antragsteller, nicht automatisch heruntergeladene zulässige Antragsteller) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Problembehandlung

Um die vom Gateway protokollierten Ereignisse zu erfassen, muss auch der Log Analytics-Agent installiert sein.

![Screenshot der Ereignisanzeigeliste im Log Analytics-Gatewayprotokoll](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Log Analytics-Gatewayereignis-IDs und -Beschreibungen

Die folgende Tabelle enthält die Ereignis-IDs und Beschreibungen für Log Analytics-Gatewayprotokollereignisse.

| **ID** | **Beschreibung** |
| --- | --- |
| 400 |Beliebiger Anwendungsfehler ohne bestimmte ID. |
| 401 |Falsche Konfiguration. Beispiel: listenPort = "text" (anstelle eines Integerwerts). |
| 402 |Ausnahme beim Analysieren von TLS-Handshakenachrichten. |
| 403 |Netzwerkfehler. Beispiel: Mit dem Zielserver kann keine Verbindung hergestellt werden. |
| 100 |Allgemeine Informationen. |
| 101 |Der Dienst wurde gestartet. |
| 102 |Der Dienst wurde beendet. |
| 103 |Ein HTTP CONNECT-Befehl wurde vom Client empfangen. |
| 104 |Kein HTTP CONNECT-Befehl. |
| 105 |Der Zielserver ist nicht in der Liste zulässiger Elemente enthalten, oder der Zielport ist nicht sicher (443). <br> <br> Stellen Sie sicher, dass der MMA-Agent auf dem OMS-Gatewayserver und die Agents, die mit dem OMS-Gateway kommunizieren, mit dem gleichen Log Analytics-Arbeitsbereich verbunden sind. |
| 105 |FEHLER TcpConnection – Ungültiges Clientzertifikat: CN=Gateway. <br><br> Stellen Sie sicher, dass Sie die OMS-Gatewayversion 1.0.395.0 oder höher verwenden. Stellen Sie außerdem sicher, dass der MMA-Agent auf dem OMS-Gatewayserver und die Agents, die mit dem OMS-Gateway kommunizieren, mit dem gleichen Log Analytics-Arbeitsbereich verbunden sind. |
| 106 |Nicht unterstützte TLS/SSL-Protokollversion.<br><br> Das Log Analytics-Gateway unterstützt nur TLS 1.0, TLS 1.1 und TLS 1.2. SSL wird nicht unterstützt.|
| 107 |Die TLS-Sitzung wurde überprüft. |

### <a name="performance-counters-to-collect"></a>Zu erfassende Leistungsindikatoren

In der folgenden Tabelle sind die verfügbaren Leistungsindikatoren für das Log Analytics-Gateway aufgeführt. Verwenden Sie den Systemmonitor, um die Indikatoren hinzuzufügen.

| **Name** | **Beschreibung** |
| --- | --- |
| Log Analytics-Gateway/Aktive Clientverbindung |Anzahl der aktiven Clientnetzwerkverbindungen (TCP) |
| Log Analytics-Gateway/Fehleranzahl |Anzahl von Fehlern |
| Log Analytics-Gateway/Verbundener Client |Anzahl verbundener Clients |
| Log Analytics-Gateway/Ablehnungsanzahl |Anzahl von Ablehnungen aufgrund von TLS-Überprüfungsfehlern |

![Screenshot der Log Analytics-Gatewayschnittstelle mit Leistungsindikatoren](./media/gateway/counters.png)

## <a name="assistance"></a>Unterstützung

Wenn Sie am Azure-Portal angemeldet sind, können Sie Hilfe zum Log Analytics-Gateway oder zu jedem anderen Azure-Dienst oder -Feature erhalten.
Um Hilfe zu erhalten, wählen Sie das Fragezeichensymbol in der oberen rechten Ecke des Portals und anschließend **Neue Supportanfrage** aus. Füllen Sie dann das Formular für eine neue Supportanfrage aus.

![Screenshot einer neuen Supportanfrage](./media/gateway/support.png)

## <a name="next-steps"></a>Nächste Schritte

[Fügen Sie Datenquellen hinzu](./agent-data-sources.md), um Daten aus verbundenen Quellen zu erfassen und die Daten in Ihrem Log Analytics-Arbeitsbereich zu speichern.