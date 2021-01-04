---
title: Problembehandlung des Azure Site Recovery-Prozessservers
description: Dieser Artikel beschreibt die Behandlung von Problemen mit dem Azure Site Recovery-Prozessserver.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: ad1bec66edaa3fcc6049f4911684f6e6d6c3e366
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999184"
---
# <a name="troubleshoot-the-process-server"></a>Problembehandlung des Prozessservers

Der [Site Recovery](site-recovery-overview.md)-Prozessserver wird verwendet, wenn Sie die Notfallwiederherstellung für Azure für lokale VMware-VMs und physische Server einrichten. Dieser Artikel beschreibt die Behandlung von Problemen mit dem Prozessserver (einschließlich Replikation und Verbindungsprobleme).

[Weitere Informationen](vmware-physical-azure-config-process-server-overview.md) zum Prozessserver.

## <a name="before-you-start"></a>Vorbereitung

Bevor Sie mit der Problembehandlung beginnen:

1. Stellen Sie sicher, dass Sie verstehen, wie [Prozessserver überwacht werden](vmware-physical-azure-monitor-process-server.md).
2. Machen Sie sich mit den unten beschriebenen bewährten Methoden vertraut.
3. Stellen Sie sicher, dass Sie [Kapazitätsüberlegungen](site-recovery-plan-capacity-vmware.md#capacity-considerations) berücksichtigen, und verwenden Sie Größenanleitungen für den [Konfigurationsserver](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) oder [eigenständige Prozessserver](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Bewährte Methoden für die Bereitstellung von Prozessservern

Für eine optimale Leistung von Prozessservern haben wir eine Reihe von allgemeinen bewährten Methoden zusammengefasst.

**Bewährte Methode** | **Details**
--- |---
**Verwendung** | Stellen Sie sicher, dass der Konfigurationsserver/der eigenständige Prozessserver nur für den vorgesehenen Zweck verwendet wird. Führen Sie keine weiteren Prozesse auf dem Computer aus.
**IP-Adresse** | Stellen Sie sicher, dass der Prozessserver eine statische IPv4-Adresse besitzt und keine NAT konfiguriert ist.
**Steuern der Arbeitsspeicher-/CPU-Verwendung** |Halten Sie die CPU- und Arbeitsspeicherauslastung unter 70 %.
**Sicherstellen von freiem Speicherplatz** | „Freier Speicherplatz“ bezieht sich auf den Cachespeicherplatz auf dem Prozessserver. Replikationsdaten werden im Cache gespeichert, bevor sie in Azure hochgeladen werden.<br/><br/> Halten Sie den freien Speicherplatz über 25 %. Wenn er unter 20 % fällt, wird die Replikation für replizierte Computer gedrosselt, die mit dem Prozessserver verknüpft sind.

## <a name="check-process-server-health"></a>Überprüfen der Integrität des Prozessservers

Der erste Schritt bei der Problembehandlung besteht im Überprüfen der Integrität und des Status des Prozessservers. Überprüfen Sie dazu alle Warnmeldungen, überprüfen Sie, ob die erforderlichen Dienste ausgeführt werden, und vergewissern Sie sich, dass ein Heartbeat vom Prozessserver vorhanden ist. Diese Schritte werden in der folgenden Abbildung zusammengefasst, gefolgt von Verfahren zum Ausführen der einzelnen Schritte.

![Problembehandlung der Integrität des Prozessservers](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Schritt 1: Problembehandlung der Integritätswarnungen des Prozessservers

Die Prozessserver generiert zahlreiche Integritätswarnungen. Diese Warnungen (und die empfohlenen Aktionen) werden in der folgenden Tabelle zusammengefasst.

**Warnungstyp** | **Fehler** | **Problembehandlung**
--- | --- | --- 
![Healthy][green] | Keine  | Der Prozessserver ist verbunden und fehlerfrei.
![Warnung][yellow] | Die angegebenen Dienste werden nicht ausgeführt. | 1. Überprüfen Sie, ob die Dienste ausgeführt werden.<br/> 2. Wenn die Dienste wie erwartet ausgeführt werden, befolgen Sie die Anweisungen unten, um [Konnektivitäts- und Replikationsprobleme zu behandeln](#check-connectivity-and-replication).
![Warnung][yellow]  | CPU-Auslastung > 80 % während der letzten 15 Minuten. | 1. Fügen Sie keine neuen Computern hinzu.<br/>2. Überprüfen Sie, ob die Anzahl der VMs, die den Prozessserver verwenden, den [definierten Grenzwerten](site-recovery-plan-capacity-vmware.md#capacity-considerations) entspricht, und ziehen Sie die Einrichtung eines [zusätzlichen Prozessservers](vmware-azure-set-up-process-server-scale.md) in Betracht.<br/>3. Befolgen Sie die Anweisungen unten, um [Konnektivitäts- und Replikationsprobleme zu beheben](#check-connectivity-and-replication).
![Kritisch][red] |  CPU-Auslastung > 95 % während der letzten 15 Minuten. | 1. Fügen Sie keine neuen Computern hinzu.<br/>2. Überprüfen Sie, ob die Anzahl der VMs, die den Prozessserver verwenden, den [definierten Grenzwerten](site-recovery-plan-capacity-vmware.md#capacity-considerations) entspricht, und ziehen Sie die Einrichtung eines [zusätzlichen Prozessservers](vmware-azure-set-up-process-server-scale.md) in Betracht.<br/>3. Befolgen Sie die Anweisungen unten, um [Konnektivitäts- und Replikationsprobleme zu beheben](#check-connectivity-and-replication).<br/> 4. Wenn das Problem weiterhin besteht, führen Sie den [Bereitstellungsplaner](./site-recovery-deployment-planner.md) für die Replikation von VMware- bzw. physischern Servern aus.
![Warnung][yellow] | Speicherauslastung > 80 % während der letzten 15 Minuten. |  1. Fügen Sie keine neuen Computern hinzu.<br/>2. Überprüfen Sie, ob die Anzahl der VMs, die den Prozessserver verwenden, den [definierten Grenzwerten](site-recovery-plan-capacity-vmware.md#capacity-considerations) entspricht, und ziehen Sie die Einrichtung eines [zusätzlichen Prozessservers](vmware-azure-set-up-process-server-scale.md) in Betracht.<br/>3. Befolgen Sie die Anweisungen, die der Warnung zugeordnet sind.<br/> 4. Wenn das Problem weiterhin besteht, befolgen Sie die Anweisungen unten, um [Konnektivitäts- und Replikationsprobleme zu beheben](#check-connectivity-and-replication).
![Kritisch][red] | Speicherauslastung > 95 % während der letzten 15 Minuten. | 1. Fügen Sie keine neuen Computern hinzu, und ziehen Sie die Einrichtung eines [zusätzlichen Prozessservers](vmware-azure-set-up-process-server-scale.md) in Betracht.<br/> 2. Befolgen Sie die Anweisungen, die der Warnung zugeordnet sind.<br/> 3. 4. Wenn das Problem weiterhin besteht, befolgen Sie die Anweisungen unten, um [Konnektivitäts- und Replikationsprobleme zu beheben](#check-connectivity-and-replication).<br/> 4. Wenn das Problem weiterhin besteht, führen Sie den [Bereitstellungsplaner](./site-recovery-deployment-planner.md) für Replikationsprobleme von VMware- bzw. physischern Servern aus.
![Warnung][yellow] | Freier Speicherplatz des Cacheordners < 30 % während der letzten 15 Minuten. | 1. Fügen Sie keine neuen Computern hinzu, und ziehen Sie die Einrichtung eines [zusätzlichen Prozessservers](vmware-azure-set-up-process-server-scale.md) in Betracht.<br/>2. Überprüfen Sie, ob die Anzahl der VMs, die den Prozessserver verwenden, den [Richtlinien](site-recovery-plan-capacity-vmware.md#capacity-considerations) entspricht.<br/> 3. Befolgen Sie die Anweisungen unten, um [Konnektivitäts- und Replikationsprobleme zu beheben](#check-connectivity-and-replication).
![Kritisch][red] |  Freier Speicherplatz < 25 % während der letzten 15 Minuten. | 1. Befolgen Sie die Anweisungen, die der Warnung für dieses Problem zugeordnet sind.<br/> 2. 3. Befolgen Sie die Anweisungen unten, um [Konnektivitäts- und Replikationsprobleme zu beheben](#check-connectivity-and-replication).<br/> 3. Wenn das Problem weiterhin besteht, führen Sie den [Bereitstellungsplaner](./site-recovery-deployment-planner.md) für die Replikation von VMware- bzw. physischern Servern aus.
![Kritisch][red] | 15 Minuten oder länger kein Heartbeat vom Prozessserver. Der tmansvs-Dienst kommuniziert nicht mit dem Konfigurationsserver. | 1) Überprüfen Sie, ob der Prozessserver aktiv ist und ausgeführt wird.<br/> 2. Überprüfen Sie, ob tmassvc auf dem Prozessserver ausgeführt wird.<br/> 3. Befolgen Sie die Anweisungen unten, um [Konnektivitäts- und Replikationsprobleme zu beheben](#check-connectivity-and-replication).


![Tabellenschlüssel](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Schritt 2: Überprüfen der Prozessserverdienste

Dienste, die auf dem Prozessserver ausgeführt werden sollten, werden in der folgenden Tabelle zusammengefasst. Es gibt jedoch abhängig davon, wie der Prozessserver bereitgestellt wird, geringfügige Unterschiede bezüglich der Dienste. 

Überprüfen Sie, ob für alle Dienste mit Ausnahme des Microsoft Azure Recovery Services-Agents (obengine) StartType auf **Automatisch** oder **Automatisch (Verzögerter Start)** festgelegt ist.
 
**Bereitstellung** | **Ausgeführte Dienste**
--- | ---
**Prozessserver auf dem Konfigurationsserver** | ProcessServer, ProcessServerMonitor, cxprocessserver, InMage PushInstall, Protokolluploaddienst (LogUpload), InMage Scout-Anwendungsdienst, Microsoft Azure Recovery Services-Agent (obengine), InMage Scout VX Agent-Sentinel/Outpost (svagents), tmansvc, World Wide Web Publishing Service (W3SVC), MySQL, Microsoft Azure Site Recovery Service (dra)
**Prozessserver als eigenständiger Server** | ProcessServer, ProcessServerMonitor, cxprocessserver, InMage PushInstall, Protokolluploaddienst (LogUpload), InMage Scout-Anwendungsdienst, Microsoft Azure Recovery Services-Agent (obengine), InMage Scout VX Agent-Sentinel/Outpost (svagents), tmansvc.
**In Azure für Failback bereitgestellter Prozessserver** | ProcessServer, ProcessServerMonitor, cxprocessserver, InMage PushInstall, Protokolluploaddienst (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Schritt 3: Überprüfen des Heartbeats des Prozessservers

Wenn kein Heartbeat vom Prozessserver (Fehlercode 806) vorhanden ist, führen Sie die folgenden Schritte aus:

1. Stellen Sie sicher, dass die Prozessserver-VM aktiv ist und ausgeführt wird.
2. Überprüfen Sie die folgenden Protokolle auf Fehler.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log, C\ProgramData\ASR\home\svsystems\monitor_protection*.log.

## <a name="check-connectivity-and-replication"></a>Überprüfen der Konnektivität und Replikation

 Fehler bei der ersten und fortlaufenden Replikation werden häufig von Konnektivitätsproblemen zwischen Quellcomputern und dem Prozessserver oder zwischen dem Prozessserver und Azure verursacht. Diese Schritte werden in der folgenden Abbildung zusammengefasst, gefolgt von Verfahren zum Ausführen der einzelnen Schritte.

![Flussdiagramm: Schritte zur Problembehandlung der Konnektivität und Replikation](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Schritt 4: Überprüfen der Zeitsynchronisierung auf dem Quellcomputer

Stellen Sie sicher, dass das Systemdatum und die -uhrzeit für den replizierten Computer synchron sind. [Weitere Informationen](/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Schritt 5: Überprüfen der Antivirensoftware auf dem Quellcomputer

Stellen Sie sicher, dass keine Antivirensoftware auf dem replizierten Computer Site Recovery blockiert. Wenn Sie Site Recovery von Antivirenprogrammen ausschließen möchten, lesen Sie [diesen Artikel](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Schritt 6: Überprüfen der Konnektivität vom Quellcomputer


1. Installieren Sie den [Telnet-Client](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771275(v=ws.10)) auf dem Quellcomputer, wenn dies noch nicht geschehen ist. Verwenden Sie nicht Ping.
2. Pingen Sie den Prozessserver vom Quellcomputer am HTTPS-Port mit Telnet. Standardmäßig ist 9443 der HTTPS-Port für Replikationsdatenverkehr.

    `telnet <process server IP address> <port>`

3. Überprüfen Sie, ob die Verbindung erfolgreich ist.


**Konnektivität** | **Details** | **Aktion**
--- | --- | ---
**Erfolgreich** | Telnet zeigt einen leeren Bildschirm an, und der Prozessserver ist erreichbar. | Es ist keine weitere Aktion erforderlich.
**Nicht erfolgreich** | Keine Verbindung möglich | Stellen Sie sicher, dass Port 9443 für eingehenden Datenverkehr auf dem Prozessserver zulässig ist. Beispielsweise, wenn Sie ein Umkreisnetzwerk oder ein überwachtes Subnetz verwenden. Überprüfen Sie die Verbindung erneut.
**Teilweise erfolgreich** | Sie können eine Verbindung herstellen, aber der Quellcomputer meldet, dass der Prozessserver nicht erreichbar ist. | Fahren Sie mit dem nächsten Verfahren für die Problembehandlung fort.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Schritt 7: Problembehandlung für einen nicht erreichbaren Prozessserver

Wenn der Prozessserver vom Quellcomputer nicht erreicht werden kann, wird Fehler 78186 angezeigt. Wenn dieses Problem nicht behoben wird, führt dies dazu, dass sowohl anwendungs- als auch crashkonsistente Wiederherstellungspunkte nicht wie erwartet generiert werden.

Beheben Sie das Problem, indem Sie überprüfen, ob der Quellcomputer die IP-Adresse des Prozessservers erreichen kann, und führen Sie das Tool cxpsclient auf dem Quellcomputer aus, um die End-to-End-Verbindung zu überprüfen.


### <a name="check-the-ip-connection-on-the-process-server"></a>Überprüfen der IP-Verbindung auf dem Prozessserver

Wenn Telnet erfolgreich ist, der Quellcomputer aber meldet, dass der Prozessserver nicht erreichbar ist, überprüfen Sie, ob Sie die IP-Adresse des Prozessservers erreichen können.

1. Versuchen Sie in einem Webbrowser, die IP-Adresse https://<PS_IP>:<PS_Data_Port>/ zu erreichen.
2. Es ist normal, wenn diese Überprüfung einen HTTPS-Zertifikatfehler anzeigt. Wenn Sie den Fehler ignorieren, sollte ein Fehler 400 – Bad Request (ungültige Anforderung) angezeigt werden. Dies bedeutet, dass der Server die Browseranforderung nicht verarbeiten kann und dass die HTTPS-Standardverbindung ordnungsgemäß funktioniert.
3. Wenn diese Überprüfung nicht funktioniert, beachten Sie die Fehlermeldung des Browsers. Ein Fehler vom Typ 407 zeigt beispielsweise ein Problem mit der Proxyauthentifizierung an.

### <a name="check-the-connection-with-cxpsclient"></a>Überprüfen der Verbindung mit cxpsclient

Darüber hinaus können Sie das Tool cxpsclient ausführen, um die End-to-End-Verbindung zu überprüfen.

1. Führen Sie das Tool wie folgt aus:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. Überprüfen Sie auf dem Prozessserver die generierten Protokolle in diesen Ordnern:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err, C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Überprüfen der VM-Quellprotokolle auf Uploadfehler (Fehler 78028)

Probleme mit Datenuploads, die von den Quellcomputern zum Prozessdienst blockiert werden, können dazu führen, dass sowohl crashkonsistente als auch anwendungskonsistente Wiederherstellungspunkte nicht generiert werden. 

1. Um Netzwerkuploadfehler zu beheben, können Sie in diesem Protokoll nach Fehlern suchen:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. Die Verwendung der restlichen Verfahren in diesem Artikel kann Ihnen helfen, Probleme beim Datenupload zu lösen.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Schritt 8: Überprüfen, ob der Prozessserver Daten pusht

Überprüfen Sie, ob der Prozessserver aktiv Daten in Azure pusht.

  1. Öffnen Sie auf dem Prozessserver den Task-Manager (drücken Sie STRG+UMSCHALT+ESC).
  2. Klicken Sie auf der Registerkarte **Leistung** auf **Ressourcenmonitor öffnen**.
  3. Wählen Sie auf der Seite **Ressourcenmonitor** die Registerkarte **Netzwerk** aus. Überprüfen Sie unter **Prozess mit Netzwerkaktivität**, ob „cbengine.exe“ aktiv große Datenvolumen sendet.

       ![Screenshot: Hoher Wert für Volumen unter „Prozesse mit Netzwerkaktivität“](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Wenn „cbengine.exe“ keine großen Datenmengen sendet, führen Sie die Schritte in den folgenden Abschnitten aus.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Schritt 9: Überprüfen der Prozessserververbindung mit Azure Blob Storage

1. Wählen Sie im Ressourcenmonitor **cbengine.exe** aus.
2. Überprüfen Sie unter **TCP-Verbindungen**, ob eine Verbindung vom Prozessserver mit dem Azure-Speicher besteht.

  ![Screenshot: Konnektivität zwischen „cbengine.exe“ und der URL von Azure Blob Storage](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Dienstüberprüfung

Wenn keine Verbindung vom Prozessserver mit der URL von Azure Blob Storage besteht, überprüfen Sie, ob die Dienste ausgeführt werden.

1. Wählen Sie in der Systemsteuerung **Dienste** aus.
2. Stellen Sie sicher, dass die folgenden Dienste ausgeführt werden:

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Microsoft Azure Recovery Services-Agent
    - Microsoft Azure Site Recovery-Dienst
    - tmansvc

3. Starten Sie jeden der Dienste, der nicht ausgeführt wird, bzw. starten Sie ihn neu.
4. Stellen Sie sicher, dass der Prozessserver verbunden und erreichbar ist. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Schritt 10: Überprüfen der Prozessserververbindung mit der öffentlichen IP-Adresse

1. Öffnen Sie auf dem Prozessserver in **%programfiles%\Microsoft Azure Recovery Services Agent\Temp** die neueste Datei „CBEngineCurr.errlog“.
2. Suchen Sie in der Datei nach **443** oder nach der Zeichenfolge **connection attempt failed** (Fehler beim Verbindungsversuch).

  ![Fehlerprotokolle im Ordner „Temp“](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Wenn Probleme angezeigt werden, ermitteln Sie Ihre öffentliche Azure-IP-Adresse in der Datei „CBEngineCurr.currLog“ mithilfe von Port 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Verwenden Sie in der Befehlszeile auf dem Prozessserver Telnet zum Pingen Ihrer öffentlichen Azure-IP-Adresse.
6. Wenn Sie keine Verbindung herstellen können, führen Sie das nächste Verfahren aus.

## <a name="step-11-check-process-server-firewall-settings"></a>Schritt 11: Überprüfen der Firewalleinstellungen des Prozessservers 

Überprüfen Sie, ob die auf der IP-Adresse basierende Firewall auf dem Prozessserver den Zugriff blockiert.

1. Für auf der IP-Adresse basierende Firewallregeln:

    a) Laden Sie die vollständige Liste der [IP-Bereiche der Microsoft Azure-Datencenter](https://www.microsoft.com/download/details.aspx?id=41653) herunter.

    b) Fügen Sie die IP-Adressbereiche Ihrer Firewallkonfiguration hinzu, um sicherzustellen, dass die Firewall die Kommunikation in Azure (und mit dem HTTPS-Standardport 443) zulässt.

    c) Lassen Sie IP-Adressbereiche für die Azure-Region Ihres Abonnements und für die Azure-Region „USA, Westen“ (die für die Zugriffsteuerung und Identitätsverwaltung verwendet werden) zu.

2. Wenn Sie URL-basierte Firewalls verwenden, fügen Sie die in der folgenden Tabelle aufgeführten URLs der Firewallkonfiguration hinzu.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Schritt 12: Überprüfen der Prozessserver-Proxyeinstellungen 

1. Wenn Sie einen Proxyserver verwenden, stellen Sie sicher, dass der Proxyservername durch den DNS-Server aufgelöst wird. Überprüfen Sie den Wert, den Sie beim Einrichten des Konfigurationsservers im Registrierungsschlüssel **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings** angegeben haben.
2. Stellen Sie sicher, dass dieselben Einstellungen vom Azure Site Recovery-Agent zum Senden von Daten verwendet werden.

    a) Suchen Sie nach **Microsoft Azure Backup**.

    b) Öffnen Sie **Microsoft Azure Backup**, und wählen Sie dann **Aktion** > **Eigenschaften ändern** aus.

    c) Auf der Registerkarte **Proxykonfiguration** sollte die Proxyadresse mit der Proxyadresse übereinstimmen, die in den Registrierungseinstellungen angezeigt wird. Wenn dies nicht der Fall ist, ändern Sie diese in dieselbe Adresse.

## <a name="step-13-check-bandwidth"></a>Schritt 13: Überprüfen der Bandbreite

Erhöhen Sie die Bandbreite zwischen dem Prozessserver und Azure, und überprüfen Sie dann, ob das Problem weiterhin auftritt.


## <a name="next-steps"></a>Nächste Schritte

Wenn Sie weitere Hilfe benötigen, können Sie Ihre Frage auf der [Microsoft F&A-Seite für Azure Site Recovery](/answers/topics/azure-site-recovery.html) veröffentlichen. 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png