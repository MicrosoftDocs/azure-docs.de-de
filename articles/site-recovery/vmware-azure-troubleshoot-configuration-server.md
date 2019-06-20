---
title: Beheben von Problemen mit dem Konfigurationsserver bei der Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure mit Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zur Problembehandlung für die Bereitstellung des Konfigurationsserver bei der Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure mit Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: f08d7bb2087ef4f30b325b3796a13e387ccdea22
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60725566"
---
# <a name="troubleshoot-configuration-server-issues"></a>Behandeln von Problemen mit dem Konfigurationsserver

Dieser Artikel hilft Ihnen bei der Behandlung von Problemen bei der Bereitstellung und Verwaltung des [Azure Site Recovery](site-recovery-overview.md)-Konfigurationsservers. Der Konfigurationsserver fungiert als Verwaltungsserver. Verwenden Sie den Konfigurationsserver zum Einrichten der Notfallwiederherstellung von lokalen virtuellen VMware-Computern und physischen Servern in Azure mithilfe von Site Recovery. In den folgenden Abschnitten werden die häufigsten Fehler erörtert, die beim Hinzufügen eines neuen Konfigurationsserver und beim Verwalten von Konfigurationsservern auftreten können.

## <a name="registration-failures"></a>Fehler bei der Registrierung

Der Quellcomputer registriert sich während der Installation des Mobilitäts-Agents beim Konfigurationsserver. Sie können alle während dieses Setups auftretenden Fehler durch Befolgen der folgenden Anleitungen debuggen:

1. Öffnen Sie die Datei „C:\Programme\ASR\home\svsystems\var\configurator_register_host_static_info.log“. (Der Ordner „ProgramData“ ist normalerweise ein versteckter Ordner. Wenn der Ordner „ProgramData“ im Datei-Explorer nicht angezeigt wird, aktivieren Sie auf der Registerkarte **Ansicht** im Bereich **Ein-/ausblenden** das Kontrollkästchen **Ausgeblendete Elemente**.) Fehler können durch mehrere Probleme verursacht werden.

2. Suchen Sie nach der Zeichenfolge **No Valid IP Address found** (Keine gültige IP-Adresse gefunden). Wenn die Zeichenfolge gefunden wird:
   1. Überprüfen Sie, ob die angeforderte Host-ID mit der Host-ID des Quellcomputers übereinstimmt.
   2. Überprüfen Sie, ob der Quellcomputer über mindestens eine der physischen Netzwerkkarte zugewiesene IP-Adresse verfügt. Damit der Agent erfolgreich beim Konfigurationsserver registriert werden kann, muss der Quellcomputer über mindestens eine der physischen Netzwerkkarte zugewiesene IP v4-Adresse verfügen.
   3. Führen Sie einen der folgenden Befehle auf dem Quellcomputer aus, um alle IP-Adressen des Quellcomputers abzurufen:
      - Für Windows: `> ipconfig /all`
      - Für Linux: `# ifconfig -a`

3. Wenn die Zeichenfolge **No Valid IP Address found** (Keine gültige IP-Adresse gefunden) nicht gefunden wird, suchen Sie nach der Zeichenfolge **Reason=>NULL**. Dieser Fehler tritt auf, wenn der Quellcomputer einen leeren Host für die Registrierung beim Konfigurationsserver verwendet. Wenn die Zeichenfolge gefunden wird:
    - Nachdem Sie die Probleme behoben haben, befolgen Sie die Anleitungen unter [Registrieren des Quellcomputers beim Konfigurationsserver](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server), um die Registrierung manuell zu wiederholen.

4. Wenn die Zeichenfolge **Reason=>NULL** nicht gefunden wird, öffnen Sie auf dem Quellcomputer die Datei „C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log“. (Der Ordner „ProgramData“ ist normalerweise ein versteckter Ordner. Wenn der Ordner „ProgramData“ im Datei-Explorer nicht angezeigt wird, aktivieren Sie auf der Registerkarte **Ansicht** im Bereich **Ein-/ausblenden** das Kontrollkästchen **Ausgeblendete Elemente**.) Fehler können durch mehrere Probleme verursacht werden. 

5. Suchen Sie nach der Zeichenfolge **post request: (7) – Couldn't connect to server** (Veröffentlichungsanforderung: (7) – Serververbindung konnte nicht hergestellt werden). Wenn die Zeichenfolge gefunden wird:
    1. Beheben Sie die Netzwerkprobleme zwischen dem Quellcomputer und dem Konfigurationsserver. Überprüfen Sie, ob der Konfigurationsserver vom Quellcomputer aus erreichbar ist, mithilfe von Netzwerktools wie Ping, Traceroute oder einem Webbrowser. Stellen Sie sicher, dass der Quellcomputer den Konfigurationsserver über Port 443 erreichen kann.
    2. Überprüfen Sie, ob eine der Firewallregeln auf dem Quellcomputer die Verbindung zwischen Quellcomputer und Konfigurationsserver blockiert. Arbeiten Sie gemeinsam mit Ihrem Netzwerkadministrator daran, jegliche Verbindungsprobleme zu beheben.
    3. Stellen Sie sicher, dass die unter [Site Recovery-Ordnerausschlüsse von Antivirenprogrammen](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) aufgeführten Ordner in der Antivirensoftware ausgeschlossen sind.
    4. Nachdem die Probleme behoben wurden, versuchen Sie die Registrierung erneut, indem Sie die Anleitungen unter [Registrieren des Quellcomputers beim Konfigurationsserver](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) befolgen.

6. Wenn die Zeichenfolge **post request: (7) – Couldn't connect to server** (Veröffentlichungsanforderung: (7) – Serververbindung konnte nicht hergestellt werden) nicht gefunden wird, suchen Sie in derselben Protokolldatei nach der Zeichenfolge **request: (60) – Peer certificate cannot be authenticated with given CA certificates** (Anforderung: (60) – Das Peerzertifikat kann mit den vorhandenen Zertifizierungsstellenzertifikaten nicht authentifiziert werden). Dieser Fehler kann auftreten, weil das Zertifikat des Konfigurationsservers abgelaufen ist oder der Quellcomputer weder TLS 1.0 noch höhere SSL-Protokolle unterstützt. Er kann auch auftreten, wenn eine Firewall die SSL-Kommunikation zwischen dem Quellcomputer und dem Konfigurationsserver blockiert. Wenn die Zeichenfolge gefunden wird: 
    1. Stellen Sie zur Behebung eine Verbindung mit der IP-Adresse des Konfigurationsservers mithilfe eines Webbrowsers auf dem Quellcomputer her. Verwenden Sie den URI „https:\/\/<IP-Adresse des Konfigurationsservers\>:443/“. Stellen Sie sicher, dass der Quellcomputer den Konfigurationsserver über Port 443 erreichen kann.
    2. Überprüfen Sie, ob eine der Firewallregeln auf dem Quellcomputer hinzugefügt oder entfernt werden muss, damit der Quellcomputer mit dem Konfigurationsserver kommunizieren kann. Aufgrund der Vielfalt von Firewallsoftware, die im Einsatz sein kann, können wir nicht alle erforderlichen Firewallkonfigurationen auflisten. Arbeiten Sie gemeinsam mit Ihrem Netzwerkadministrator daran, jegliche Verbindungsprobleme zu beheben.
    3. Stellen Sie sicher, dass die unter [Site Recovery-Ordnerausschlüsse von Antivirenprogrammen](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) aufgeführten Ordner in der Antivirensoftware ausgeschlossen sind.  
    4. Nachdem Sie die Probleme behoben haben, versuchen Sie die Registrierung erneut, indem Sie die Anleitungen unter [Registrieren des Quellcomputers beim Konfigurationsserver](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) befolgen.

7. Wenn unter Linux der Wert für die Plattform in „<INSTALLATIONSVERZ\>/etc/drscout.conf“ beschädigt ist, schlägt die Registrierung fehl. Um dieses Problem zu identifizieren, öffnen Sie die Datei „/var/log/ua_install.log“. Suchen Sie nach der Zeichenfolge **Aborting configuration as VM_PLATFORM value is either null or it is not VmWare/Azure** (Die Konfiguration wird abgebrochen, da der Wert von VM_PLATFORM entweder NULL oder nicht VmWare/Azure ist). Die Plattform sollte entweder auf **VmWare** oder **Azure** festgelegt sein. Wenn die Datei „drscout.conf“ beschädigt ist, empfehlen wird, dass Sie [den Mobilitäts-Agent deinstallieren](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) und diesen dann erneut installieren. Schlägt die Deinstallation fehl, führen Sie die folgenden Schritte aus: a. Öffnen Sie die Datei „Installation_Directory/uninstall.sh“, und kommentieren Sie den Aufruf der Funktion **StopServices** aus.
    b. Öffnen Sie die Datei „Installation_Directory/Vx/bin/uninstall.sh“, und kommentieren Sie den Aufruf der Funktion **stop_services** aus.
    c. Öffnen Sie die Datei „Installation_Directory/Fx/uninstall.sh“, und kommentieren Sie den gesamten Abschnitt aus, der versucht, den Fx-Dienst zu beenden.
    d. [Deinstallieren](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) Sie den Mobilitäts-Agent. Starten Sie nach der erfolgreichen Deinstallation das System neu, und versuchen Sie, den Mobilitäts-Agent erneut zu installieren.

## <a name="installation-failure-failed-to-load-accounts"></a>Installationsfehler: Fehler beim Laden von Konten.

Dieser Fehler tritt auf, wenn der Dienst beim Installieren des Mobilitäts-Agents und seiner Registrierung beim Konfigurationsserver keine Daten aus der Transportverbindung lesen kann. Stellen Sie zur Behebung des Problems sicher, dass TLS 1.0 auf Ihrem Quellcomputer aktiviert ist.

## <a name="vcenter-discovery-failures"></a>vCenter-Ermittlungsfehler

Um vCenter-Ermittlungsfehler zu beheben, fügen Sie den vCenter-Server zu den Proxy-Einstellungen der byPass-Liste hinzu. 

- Laden Sie das Tool PsExec [hier](https://aka.ms/PsExec) herunter, um auf Systembenutzerinhalte zuzugreifen.
- Öffnen Sie Internet Explorer im Systembenutzerinhalt, indem Sie die folgende Befehlszeile ausführen: psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Fügen Sie Proxyeinstellungen in Internet Explorer hinzu, und starten Sie den Dienst „tmanssvc“ neu.
- Führen Sie zum Konfigurieren der DRA-Proxyeinstellungen „cd C:\Program Files\Microsoft Azure Site Recovery Provider“ aus.
- Führen Sie anschließend „DRCONFIGURATOR.EXE /configure /AddBypassUrls“ aus [Hinzufügen der IP-Adresse/des FQDN von vCenter Server, die bzw. der im Schritt **Konfigurieren von vCenter Server/des vSphere ESXi-Servers** unter [Bereitstellen eines Konfigurationsservers](vmware-azure-deploy-configuration-server.md#configure-settings) angegeben wurde].

## <a name="change-the-ip-address-of-the-configuration-server"></a>Ändern der IP-Adresse des Konfigurationsservers

Wir empfehlen dringend, dass Sie die IP-Adresse eines Konfigurationsservers nicht ändern. Stellen Sie sicher, dass alle IP-Adressen, die dem Konfigurationsserver zugewiesen sind, statische IP-Adressen sind. Verwenden Sie keine DHCP-IP-Adressen.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: Das SAML-Token ist ungültig

Um diesen Fehler zu vermeiden, vergewissern Sie sich, dass die Uhrzeit Ihrer Systemuhr nicht um mehr als 15 Minuten von der Ortszeit abweicht. Führen Sie das Installationsprogramm erneut aus, um die Registrierung durchzuführen.

## <a name="failed-to-create-a-certificate"></a>Fehler beim Erstellen eines Zertifikats

Ein für die Authentifizierung von Site Recovery erforderliches Zertifikat kann nicht erstellt werden. Führen Sie Setup erneut aus, nachdem Sie sichergestellt haben, dass Sie Setup als lokaler Administrator ausführen.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Fehler beim Aktivieren von Windows-Lizenz von Server Standard EVALUATION zu Server Standard

1. Im Rahmen der Konfigurationsserver-Bereitstellung durch OVF wird eine Evaluierungslizenz verwendet, die 180 Tage lang gültig ist. Sie müssen diese Lizenz aktivieren, bevor sie abgelaufen ist. Andernfalls kann dies zu einem häufigen Herunterfahren des Konfigurationsservers und damit zu einer Behinderung der Replikationsaktivitäten führen.
2. Wenn Sie die Windows-Lizenz nicht aktivieren können, wenden Sie sich an das [Windows-Supportteam](https://aka.ms/Windows_Support), um das Problem zu lösen.

## <a name="register-source-machine-with-configuration-server"></a>Registrieren des Quellcomputers beim Konfigurationsserver

### <a name="if-the-source-machine-runs-windows"></a>Wenn der Quellcomputer Windows ausführt

Führen Sie den folgenden Befehl auf dem Quellcomputer aus:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Einstellung | Details
--- | ---
Verwendung | UnifiedAgentConfigurator.exe  /CSEndPoint <IP-Adresse des Konfigurationsservers\> /PassphraseFilePath <Dateipfad der Passphrase\>
Agent-Konfigurationsprotokolle | Befindet sich unter „%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log“.
/CSEndPoint | Obligatorischer Parameter. Gibt die IP-Adresse des Konfigurationsservers an. Verwenden Sie eine beliebige gültige IP-Adresse.
/PassphraseFilePath |  Obligatorisch. Der Speicherort der Passphrase. Verwenden Sie einen beliebiger UNC- oder lokalen Dateipfad.

### <a name="if-the-source-machine-runs-linux"></a>Wenn der Quellcomputer Linux ausführt

Führen Sie den folgenden Befehl auf dem Quellcomputer aus:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Einstellung | Details
--- | ---
Verwendung | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <IP-Adresse des Konfigurationsservers\> -P <Dateipfad der Passphrase\>
-i | Obligatorischer Parameter. Gibt die IP-Adresse des Konfigurationsservers an. Verwenden Sie eine beliebige gültige IP-Adresse.
-P |  Obligatorisch. Der vollständige Dateipfad der Datei, in der die Passphrase gespeichert ist. Verwenden Sie einen beliebigen gültigen Ordner.

## <a name="unable-to-configure-the-configuration-server"></a>Konfigurieren des Konfigurationsservers nicht möglich

Wenn Sie andere Anwendungen als den Konfigurationsserver auf dem virtuellen Computer installieren, können Sie das Masterziel möglicherweise nicht konfigurieren. 

Der Konfigurationsserver ist nur für seinen jeweiligen Zweck vorgesehen. Der Einsatz als gemeinsam genutzter Server wird nicht unterstützt. 

Weitere Informationen finden Sie in den häufig gestellten Fragen zur Konfiguration unter [Bereitstellen eines Konfigurationsservers](vmware-azure-deploy-configuration-server.md#faq). 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>Entfernen der veralteten Einträge für geschützte Elemente aus der Datenbank des Konfigurationsservers 

Um einen veralteten geschützten Computer auf dem Konfigurationsserver zu entfernen, führen Sie die folgenden Schritte aus. 
 
1. So bestimmen Sie den Quellcomputer und die IP-Adresse des veralteten Eintrags: 

    1. Öffnen Sie die MYSQL-Befehlszeile im Administratormodus. 
    2. Führen Sie die folgenden Befehle aus. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Dadurch wird eine Liste der registrierten Geräte mit ihren IP-Adressen und dem letzten Heartbeat ausgegeben. Suchen Sie den Host mit veralteten Replikationspaaren.

2. Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten, und navigieren Sie zu C:\ProgramData\ASR\home\svsystems\bin. 
4. Um die Details der registrierten Hosts und die Informationen zum veralteten Eintrag vom Konfigurationsserver zu entfernen, führen Sie den folgenden Befehl mit dem Quellcomputer und der IP-Adresse des veralteten Eintrags aus. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Wenn Sie einen Quellservereintrag von „OnPrem-VM01“ mit der IP-Adresse 10.0.0.0.4 haben, verwenden Sie stattdessen den folgenden Befehl.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Starten Sie die folgenden Dienste auf dem Quellcomputer neu, um sich erneut beim Konfigurationsserver zu registrieren. 
 
    - InMage Scout-Anwendungsdienst
    - InMage Scout VX Agent – Sentinel/Outpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>Upgrade schlägt fehl, wenn die Dienste nicht beenden werden

Das Upgrade des Konfigurationsservers schlägt fehl, wenn bestimmte Dienste nicht beendet werden. 

Um das Problem zu identifizieren, navigieren auf dem Konfigurationsserver zu C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile. Wenn Sie folgende Fehler finden, verwenden Sie zum Beheben des Problems die folgenden Schritte aus: 

    2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
    2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
    2018-06-28 14:28:12.944   Stopping svagents service.
    2018-06-28 14:31:32.949   Unable to stop svagents service.
    2018-06-28 14:31:32.949   Stopping svagents service.
    2018-06-28 14:34:52.960   Unable to stop svagents service.
    2018-06-28 14:34:52.960   Stopping svagents service.
    2018-06-28 14:38:12.971   Unable to stop svagents service.
    2018-06-28 14:38:12.971   Rolling back the install changes.
    2018-06-28 14:38:12.971   Upgrade has failed.

So lösen Sie das Problem:

Beenden Sie die folgenden Dienste manuell:

- cxprocessserver
- InMage Scout VX Agent – Sentinel/Outpost, 
- Microsoft Azure Recovery Services-Agent, 
- Microsoft Azure Site Recovery-Dienst, 
- tmansvc
  
Um den Konfigurationsserver zu aktualisieren, führen Sie das [vereinheitlichte Setup](service-updates-how-to.md#links-to-currently-supported-update-rollups) erneut aus.

## <a name="azure-active-directory-application-creation-failure"></a>Fehler beim Erstellen der Azure Active Directory-Anwendung

Sie haben keine ausreichenden Berechtigungen, um eine Anwendung in Azure Active Directory (AAD) mit der Vorlage [Open Virtualization Application (OVA)](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template
) zu erstellen.

Um das Problem zu beheben, melden Sie sich beim Azure-Portal an, und führen Sie einen der folgenden Schritte aus:

- Fordern Sie die Rolle „Anwendungsentwickler“ in AAD an. Weitere Informationen zur Rolle des Anwendungsentwicklers finden Sie unter [Berechtigungen der Administratorrolle in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).
- Überprüfen Sie, ob das Flag **Benutzer kann die Anwendung erstellen** in AAD auf *true* gesetzt ist. Weitere Informationen finden Sie unter [Gewusst wie: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>Prozessserver/Masterziel können nicht mit dem Konfigurationsserver kommunizieren 

Die Module „Prozessserver“ (PS) und „Masterziel“ (MT) können nicht mit dem Konfigurationsserver (CS) kommunizieren und ihr Status wird als „nicht verbunden“ im Azure-Portal angezeigt.

In der Regel liegt hier ein Fehler an Port 443 vor. Führen Sie die folgenden Schritte aus, um die Sperre des Ports aufzuheben und die Kommunikation mit dem CS wieder zu aktivieren.

**Stellen Sie sicher, dass der MARS-Agent vom Masterziel-Agent aufgerufen wird.**

Um zu überprüfen, ob der Masterziel-Agent eine TCP-Sitzung für die IP des Konfigurationsservers erstellen kann, suchen Sie in den Masterziel-Agent-Protokollen nach einer Ablaufverfolgung ähnlich der folgenden:

TCP \<hier IP durch CS-IP ersetzen>: 52739 \<hier IP durch CS-IP ersetzen>: 443 SYN_SENT 

TCP    192.168.1.40:52739     192.168.1.40:443      SYN_SENT  // Ersetzen Sie IP hier mit CS IP

Wenn Sie in den MT-Agent-Protokollen ähnliche Ablaufverfolgungen wie die folgenden finden, meldet der MT-Agent Fehler auf Port 443:

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
Dieser Fehler kann auftreten, wenn andere Anwendungen ebenfalls Port 443 verwenden oder wenn eine Firewalleinstellung den Port blockiert.

So lösen Sie das Problem:

- Stellen Sie sicher, dass Port 443 nicht durch Ihre Firewall blockiert wird.
- Wenn der Port aufgrund einer anderen Anwendung, die diesen Port verwendet, nicht erreichbar ist, beenden und deinstallieren Sie die App.
  - Wenn das Beenden der App nicht möglich ist, richten Sie einen neue CS ein.
- Staren Sie den Konfigurationsserver neu.
- Starten Sie den IIS-Dienst neu.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Konfigurationsserver ist aufgrund von falschen UUID-Einträgen nicht verbunden

Dieser Fehler kann auftreten, wenn mehrere UUID-Einträge der Konfigurationsserver-Instanz (CS) in der Datenbank vorhanden sind. Das Problem tritt häufig auf, wenn Sie die VM des Konfigurationsservers klonen.

So lösen Sie das Problem:

1. Entfernen Sie die veraltete CS-VM aus vCenter. Weitere Informationen finden Sie unter [Entfernen von Servern und Deaktivieren des Schutzes](site-recovery-manage-registration-and-protection.md).
2. Melden Sie sich bei der Konfigurationsserver-VM an und verbinden Sie sich mit der MySQL svsdb1-Datenbank. 
3. Führen Sie die folgenden Abfrage aus:

    > [!IMPORTANT]
    >
    > Stellen Sie sicher, dass Sie die UUID-Details des geklonten Konfigurationsservers oder den veralteten Eintrag des Konfigurationsservers eingeben, der nicht mehr zum Schutz virtueller Computer verwendet wird. Die Eingabe einer falschen UUID führt zum Verlust der Informationen für alle vorhandenen geschützten Elemente.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Aktualisieren Sie die Portalseite.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>Bei der Eingabe Ihrer Anmeldeinformationen tritt eine unendliche Anmeldeschleife auf

Nachdem Sie den richtigen Benutzernamen und das richtige Kennwort auf dem Konfigurationsserver-OVF eingegeben haben, werden Sie bei der Azure-Anmeldung weiterhin nach den richtigen Anmeldeinformationen gefragt.

Dieses Problem kann auftreten, wenn die Systemzeit falsch ist.

So lösen Sie das Problem:

Stellen Sie die richtige Zeit am Computer ein, und versuchen Sie erneut, sich anzumelden. 
 