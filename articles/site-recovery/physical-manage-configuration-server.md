---
title: Verwalten des Konfigurationsservers für physische Computer in Azure Site Recovery
description: In diesem Artikel wird beschrieben, wie Sie den Azure Site Recovery-Konfigurationsserver für die Notfallwiederherstellung von physischen Servern in Azure verwalten.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: ff612b7c052ead5658ea4bbfafd7aace51ba3c02
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017439"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Verwalten des Konfigurationsservers für die Notfallwiederherstellung von physischen Servern

Sie richten einen lokalen Konfigurationsserver ein, wenn Sie den [Azure Site Recovery](site-recovery-overview.md)-Dienst für die Notfallwiederherstellung von physischen Servern in Azure verwenden. Der Konfigurationsserver koordiniert die Kommunikation zwischen lokalen Computern und Azure und verwaltet die Datenreplikation. In diesem Artikel werden häufige Aufgaben zur Verwaltung des Konfigurationsservers nach dessen Bereitstellung zusammengefasst.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

In der Tabelle werden die erforderlichen Komponenten für die Bereitstellung des lokalen Konfigurationsservercomputers zusammengefasst.

| **Komponente** | **Anforderung** |
| --- |---|
| CPU-Kerne| 8 |
| RAM | 16 GB|
| Anzahl der Datenträger | 3, einschließlich Betriebssystem-Datenträger, Prozessservercache-Datenträger und Aufbewahrungslaufwerk für Failback |
| Freier Speicherplatz (Prozessservercache) | 600 GB
| Freier Speicherplatz (Aufbewahrungslaufwerk) | 600 GB|
| Betriebssystem  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Gebietsschema des Betriebssystems | Englisch (USA)|
| VMware vSphere PowerCLI-Version | Nicht erforderlich|
| Windows Server-Rollen | Aktivieren Sie die folgenden Rollen nicht: <br> - Active Directory Domain Services <br>- Internetinformationsdienste <br> - Hyper-V |
| Gruppenrichtlinien| Aktivieren Sie die folgenden Gruppenrichtlinien nicht: <br> - Zugriff auf Eingabeaufforderung verhindern <br> - Zugriff auf Programme zum Bearbeiten der Registrierung verhindern <br> - Vertrauenslogik für Dateianlagen <br> - Skriptausführung aktivieren <br> [Weitere Informationen](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
| IIS | - Keine bereits vorhandene Standardwebsite <br> - Aktivieren der [anonymen Authentifizierung](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - Aktivieren der Einstellung [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))  <br> - Keine bereits vorhandene Website/Anwendung sollte an Port 443 lauschen<br>|
| NIC-Typ | VMXNET3 (bei Bereitstellung als VMware-VM) |
| Art der IP-Adresse | statischen |
| Zugriff auf das Internet | Der Server benötigt Zugriff auf diese URLs: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - `https://management.azure.com` <br> - *.services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (für horizontal skalierte Prozessserver nicht erforderlich) <br> - time.nist.gov <br> - time.windows.com |
| Ports | 443 (Steuerkanalorchestrierung)<br>9443 (Datentransport)|

## <a name="download-the-latest-installation-file"></a>Herunterladen der aktuellen Installationsdatei

Die aktuelle Version der Konfigurationsserver-Installationsdatei steht im Site Recovery-Portal zur Verfügung. Darüber hinaus kann sie direkt aus dem [Microsoft Download Center](https://aka.ms/unifiedsetup) heruntergeladen werden.

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu Ihrem Recovery Services-Tresor.
2. Navigieren Sie zu **Site Recovery-Infrastruktur** > **Konfigurationsserver** (unter „Für VMware und physische Computer“).
3. Klicken Sie auf die **+Server**-Schaltfläche.
4. Klicken Sie auf der Seite **Server hinzufügen** auf die Downloadschaltfläche, um den Registrierungsschlüssel herunterzuladen. Sie benötigen diesen Schlüssel während der Installation des Konfigurationsservers zur Registrierung beim Azure Site Recovery-Dienst.
5. Klicken Sie auf den Link **Einheitliches Setup von Microsoft Azure Site Recovery herunterladen**, um die neueste Version des Konfigurationsservers herunterzuladen.

   ![Downloadseite](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Installieren und Registrieren des Servers

1. Führen Sie die Installationsdatei für das einheitliche Setup aus.
2. Wählen Sie unter **Vorbereitung** die Option **Install the configuration server and process server** (Konfigurationsserver und Prozessserver installieren) aus.

    ![Vorbereitung](./media/physical-manage-configuration-server/combined-wiz1.png)

3. Klicken Sie unter **Third-Party Software License** (Drittanbietersoftwarelizenz) auf **I Accept** (Ich stimme zu), um MySQL herunterzuladen und zu installieren.
4. Geben Sie unter **Internet Settings** (Interneteinstellungen) an, wie der auf dem Konfigurationsserver ausgeführte Anbieter eine Internetverbindung mit Azure Site Recovery herstellen soll. Stellen Sie sicher, dass Sie die erforderlichen URLs zugelassen haben.

    - Wenn die Verbindung über den derzeit auf dem Computer eingerichteten Proxy hergestellt werden soll, wählen Sie **Unter Verwendung eines Proxyservers mit Azure Site Recovery verbinden**.
    - Wenn der Anbieter eine direkte Verbindung herstellen soll, wählen Sie **Direkt mit Azure Site Recovery verbinden (ohne Proxyserver)** .
    - Falls für den vorhandenen Proxy eine Authentifizierung erforderlich ist oder Sie für die Anbieterverbindung einen benutzerdefinierten Proxy verwenden möchten, müssen Sie **Mit benutzerdefinierten Proxyeinstellungen verbinden** auswählen und Adresse, Port und Anmeldeinformationen angeben.
     ![Firewall](./media/physical-manage-configuration-server/combined-wiz4.png)
6. Bei der **Voraussetzungsüberprüfung** führt das Setup eine Überprüfung durch, um sicherzustellen, dass die Installation ausgeführt werden kann. Falls beim **Überprüfen der Synchronisierung der globalen Zeit** eine Warnung angezeigt wird, stellen Sie sicher, dass die Zeit der Systemuhr (Einstellungen für **Datum und Uhrzeit**) mit der Zeitzone übereinstimmt.

    ![Voraussetzungen](./media/physical-manage-configuration-server/combined-wiz5.png)
7. Erstellen Sie unter **MySQL Configuration** (MySQL-Konfiguration) Anmeldeinformationen für die Anmeldung bei der MySQL-Serverinstanz, die installiert wird.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. Wählen Sie unter **Umgebungsdetails** aus, ob Sie VMware-VMs replizieren werden. Wenn dies der Fall ist, überprüft das Setup, ob PowerCLI 6.0 installiert ist.
9. Wählen Sie unter **Install Location** (Installationsspeicherort) aus, wo die Binärdateien installiert werden sollen und wo der Cache gespeichert werden soll. Auf dem ausgewählten Laufwerk müssen mindestens 5 GB an Speicherplatz verfügbar sind, wir raten Ihnen jedoch zu einem Cachelaufwerk mit mindestens 600 GB freiem Speicherplatz.

    ![Installationsspeicherort](./media/physical-manage-configuration-server/combined-wiz8.png)
10. Wählen Sie in **Netzwerkauswahl** zunächst den Netzwerkadapter aus, den der integrierte Prozessserver für die Ermittlung und die Pushinstallation des Mobilitätsdiensts auf Quellcomputern verwendet. Wählen Sie anschließend den Netzwerkadapter aus, den der Konfigurationsserver zum Herstellen einer Verbindung mit Azure nutzt. Port 9443 ist der Standardport zum Senden und Empfangen von Replikationsdatenverkehr. Sie können diese Portnummer jedoch ändern, um sie an die Anforderungen Ihrer Umgebung anzupassen. Zusätzlich zum Port 9443 öffnen wir auch den Port 443, der von einem Webserver zum Orchestrieren von Replikationsvorgängen verwendet wird. Verwenden Sie den Port 443 nicht, um Replikationsdatenverkehr zu senden oder zu empfangen.

    ![Netzwerkauswahl](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Lesen Sie die unter **Summary** (Zusammenfassung) angezeigten Informationen und klicken Sie auf **Install** (Installieren). Nach Abschluss der Installation wird eine Passphrase generiert. Diese benötigen Sie bei der Aktivierung der Replikation. Kopieren Sie sie deshalb, und bewahren Sie sie an einem sicheren Ort auf.


Nach Abschluss der Registrierung wird der Server auf dem Blatt **Einstellungen** > **Server** im Tresor angezeigt.


## <a name="install-from-the-command-line"></a>Installieren über die Befehlszeile

Führen Sie die Installationsdatei wie folgt aus:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Beispielverwendung
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parameter

|Parametername| type | BESCHREIBUNG| Werte|
|-|-|-|-|
| /ServerMode|Erforderlich|Gibt an, ob die Installation sowohl den Konfigurations- als auch den Prozessserver oder nur den Prozessserver umfassen soll.|CS<br>PS|
|/InstallLocation|Erforderlich|Der Ordner, in dem die Komponenten installiert werden.| Beliebiger Ordner auf dem Computer|
|/MySQLCredsFilePath|Erforderlich|Der Dateipfad, unter dem die MySQL Server-Anmeldeinformationen gespeichert werden.|Die Datei sollte das unten angegebene Format haben.|
|/VaultCredsFilePath|Erforderlich|Pfad der Datei mit Anmeldeinformationen für den Tresor|Gültiger Dateipfad|
|/EnvType|Erforderlich|Typ der zu schützenden Umgebung |VMware<br>NonVMware|
|/PSIP|Erforderlich|IP-Adresse der Netzwerkkarte für die Übertragung von Replikationsdaten| Beliebige gültige IP-Adresse|
|/CSIP|Erforderlich|IP-Adresse der Netzwerkkarte, über die der Konfigurationsserver lauscht| Beliebige gültige IP-Adresse|
|/PassphraseFilePath|Erforderlich|Vollständiger Pfad zum Speicherort der Datei mit der Passphrase|Gültiger Dateipfad|
|/BypassProxy|Optional|Gibt an, dass der Konfigurationsserver die Verbindung mit Azure ohne Proxy herstellt.|Zum Abrufen dieses Werts von Venu|
|/ProxySettingsFilePath|Optional|Proxyeinstellungen (für den standardmäßigen Proxy ist eine Authentifizierung oder ein benutzerdefinierter Proxy erforderlich).|Die Datei sollte das unten angegebene Format haben.|
|DataTransferSecurePort|Optional|Die Portnummer der PSIP, die für Replikationsdaten verwendet werden soll.| Gültige Portnummer (Standardwert ist 9433)|
|/SkipSpaceCheck|Optional|Überspringt die Überprüfung des Speicherplatzes für den Cachedatenträger.| |
|/AcceptThirdpartyEULA|Erforderlich|Das Flag impliziert die Akzeptanz von Drittanbieterlizenzbedingungen.| |
|/ShowThirdpartyEULA|Optional|Zeigt Drittanbieterlizenzbedingungen an. Alle anderen Parameter werden ignoriert, wenn sie als Eingabe bereitgestellt werden.| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Erstellen einer Dateieingabe für „MYSQLCredsFilePath“

Der MySQLCredsFilePath-Parameter nimmt eine Datei als Eingabe an. Erstellen Sie die Datei im folgenden Format, und übergeben Sie sie als Eingabe an den Parameter MySQLCredsFilePath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Erstellen einer Dateieingabe für „ProxySettingsFilePath“
Der ProxySettingsFilePath-Parameter nimmt eine Datei als Eingabe an. Erstellen Sie die Datei im folgenden Format, und übergeben Sie sie als Eingabe an den Parameter ProxySettingsFilePath.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Ändern von Proxyeinstellungen

Sie können Proxyeinstellungen für den Konfigurationsservercomputer wie folgt ändern:

1. Melden Sie sich beim Konfigurationsserver an.
2. Starten Sie die Datei „cspsconfigtool.exe“ über die Verknüpfung auf Ihrem Desktop.
3. Klicken Sie auf die Registerkarte **Tresorregistrierung**.
4. Laden Sie eine neue Tresorregistrierungsdatei aus dem Portal herunter, und geben Sie sie als Eingabe für das Tool an.

   ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Geben Sie die Details des neuen Proxys an, und klicken Sie auf die Schaltfläche **Registrieren**.
6. Öffnen Sie ein PowerShell-Befehlsfenster mit Administratorrechten.
7. Führen Sie den folgenden Befehl aus:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > Wenn Sie zusätzliche Prozessserver mit dem Konfigurationsserver verbunden haben, müssen Sie [die Proxyeinstellungen auf allen Prozessservern für horizontale Skalierung](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) in Ihrer Bereitstellung korrigieren.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Erneutes Registrieren eines Konfigurationsservers im selben Tresor
1. Melden Sie sich am Konfigurationsserver an.
2. Starten Sie die Datei „cspsconfigtool.exe“ über die Verknüpfung auf Ihrem Desktop.
3. Klicken Sie auf die Registerkarte **Tresorregistrierung**.
4. Laden Sie eine neue Registrierungsdatei aus dem Portal herunter, und geben Sie sie als Eingabe für das Tool an.
      ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Geben Sie die Details des Proxyservers an, und klicken Sie auf die Schaltfläche **Registrieren**.  
6. Öffnen Sie ein PowerShell-Befehlsfenster mit Administratorrechten.
7. Führen Sie den folgenden Befehl aus.

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Bei mehreren Prozessservern müssen Sie sie [erneut registrieren](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrieren eines Konfigurationsservers in einem anderen Tresor

> [!WARNING]
> In den nachfolgenden Schritten wird die Zuordnung des Konfigurationsservers zum aktuellen Tresor getrennt und die Replikation aller geschützten virtuellen Computer unter dem Konfigurationsserver beendet.

1. Melden Sie sich beim Konfigurationsserver an.
2. Führen Sie an einer Eingabeaufforderung mit Administratorrechten folgenden Befehl aus:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Starten Sie die Datei „cspsconfigtool.exe“ über die Verknüpfung auf Ihrem Desktop.
4. Klicken Sie auf die Registerkarte **Tresorregistrierung**.
5. Laden Sie eine neue Registrierungsdatei aus dem Portal herunter, und geben Sie sie als Eingabe für das Tool an.
6. Geben Sie die Details des Proxyservers an, und klicken Sie auf die Schaltfläche **Registrieren**.  
7. Öffnen Sie ein PowerShell-Befehlsfenster mit Administratorrechten.
8. Führen Sie den folgenden Befehl aus.
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Aktualisieren eines Konfigurationsservers

Sie führen Updaterollups aus, um den Konfigurationsserver zu aktualisieren. Updates können für maximal N-4 Versionen angewendet werden. Beispiel:

- Wenn Sie Version 9.7, 9.8, 9.9 oder 9.10 ausführen, können Sie direkt auf 9.11 aktualisieren.
- Falls Sie Version 9.6 oder früher ausführen und auf 9.11 aktualisieren möchten, müssen Sie zuerst das Upgrade auf Version 9.7 durchführen, bevor das Upgrade auf 9.11 möglich ist.

Links zu Updaterollups zum Aktualisieren aller Versionen des Konfigurationsservers sind auf der [Wiki-Seite zu den Updates](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) verfügbar.

Aktualisieren Sie den Server wie folgt:

1. Laden Sie die Datei mit dem Update-Installer auf den Konfigurationsserver herunter.
2. Doppelklicken Sie auf die Datei, um das Installationsprogramm auszuführen.
3. Das Installationsprogramm erkennt die aktuelle Version, die auf dem Computer ausgeführt wird.
4. Klicken Sie auf **OK**, um den Vorgang zu bestätigen und das Upgrade durchzuführen. 


## <a name="delete-or-unregister-a-configuration-server"></a>Löschen oder Aufheben der Registrierung eines Konfigurationsservers

> [!WARNING]
> Überprüfen Sie Folgendes vor der Außerbetriebnahme Ihres Konfigurationsservers.
> 1. [Deaktivieren Sie den Schutz](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) für alle virtuellen Computer unter diesem Konfigurationsserver.
> 2. [Heben Sie die Zuordnung von allen Replikationsrichtlinien zum Konfigurationsserver auf](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy), oder [löschen](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) Sie sie.
> 3. [Löschen](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) Sie alle vCenter-Server/vSphere-Hosts, die mit dem Konfigurationsserver verknüpft sind.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Löschen des Konfigurationsservers im Azure-Portal
1. Navigieren Sie im Azure-Portal im Menü „Tresor“ zu **Site Recovery-Infrastruktur** > **Konfigurationsserver**.
2. Klicken Sie auf den Konfigurationsserver, den Sie außer Betrieb nehmen möchten.
3. Klicken Sie auf der Detailseite des Konfigurationsservers auf die Schaltfläche **Löschen**.
4. Klicken Sie auf **Ja**, um das Löschen des Servers zu bestätigen.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Deinstallieren des Konfigurationsservers und seiner Abhängigkeiten
> [!TIP]
>   Wenn Sie den Konfigurationsserver erneut mit Azure Site Recovery verwenden möchten, können Sie direkt mit Schritt 4 fortfahren.

1. Melden Sie sich als Administrator beim Konfigurationsserver an.
2. Öffnen Sie „Systemsteuerung > Programme > Programme deinstallieren“.
3. Deinstallieren Sie die Programme in der folgenden Reihenfolge:
   * Microsoft Azure Recovery Services-Agent
   * Microsoft Azure Site Recovery Mobility Service/Masterzielserver
   * Microsoft Azure Site Recovery-Anbieter
   * Microsoft Azure Site Recovery-Konfigurationsserver/Prozessserver
   * Microsoft Azure Site Recovery-Konfigurationsserverabhängigkeiten
   * MySQL Server 5.5
4. Führen Sie an einer Eingabeaufforderung mit Administratorrechten folgenden Befehl aus:
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Löschen oder Aufheben der Registrierung eines Konfigurationsservers (PowerShell)

1. [Installieren](/powershell/azure/install-Az-ps) Sie das Azure PowerShell-Modul.
2. Melden Sie sich mithilfe des folgenden Befehls bei Ihrem Azure-Konto an:
    
    `Connect-AzAccount`
3. Wählen Sie das Abonnement aus, unter dem der Tresor vorhanden ist.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Richten Sie jetzt den Kontext für Ihren Tresor ein.
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Rufen Sie Ihren ausgewählten Konfigurationsserver ab.

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Löschen Sie den Konfigurationsserver.

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> Die **-Force**-Option im Cmdlet „Remove-AzSiteRecoveryFabric“ kann dazu verwendet werden, das Entfernen bzw. Löschen des Konfigurationsservers zu erzwingen.

## <a name="renew-tlsssl-certificates"></a>Erneuern von TLS-/SSL-Zertifikaten
Der Konfigurationsserver verfügt über einen integrierten Webserver, der die Aktivitäten von Mobility Service, Prozessservern und Masterzielservern, die mit dem Konfigurationsserver verbunden sind, orchestriert. Der Webserver verwendet ein TLS-/SSL-Zertifikat, um Clients zu authentifizieren. Das Zertifikat läuft nach drei Jahren ab und kann jederzeit erneuert werden.

### <a name="check-expiry"></a>Überprüfen des Ablaufs

Für Bereitstellungen von Konfigurationsservern vor dem Mai 2016 wurde die Zertifikatablaufzeit auf ein Jahr festgelegt. Wenn eines Ihrer Zertifikate demnächst abläuft, geschieht Folgendes:

- Wenn das Ablaufdatum höchstens zwei Monate in der Zukunft liegt, sendet der Dienst Benachrichtigungen im Portal und per E-Mail (wenn Sie Azure Site Recovery-Benachrichtigungen abonniert haben).
- Auf der Ressourcenseite des Tresors wird ein Benachrichtigungsbanner angezeigt. Klicken Sie auf das Banner, um weitere Informationen anzuzeigen.
- Wenn die Schaltfläche **Jetzt Upgrade durchführen** angezeigt wird, wurde für einige Komponenten in Ihrer Umgebung noch kein Upgrade auf 9.4.xxxx.x oder höhere Versionen durchgeführt. Aktualisieren Sie die Komponenten, bevor Sie das Zertifikat erneuern. Sie können bei älteren Versionen keine Erneuerung durchführen.

### <a name="renew-the-certificate"></a>Erneuern von Zertifikaten

1. Öffnen Sie im Tresor **Site Recovery-Infrastruktur** > **Konfigurationsserver**, und klicken Sie auf den gewünschten Konfigurationsserver.
2. Das Ablaufdatum wird unter **Integrität des Konfigurationsservers** angezeigt.
3. Klicken Sie auf **Zertifikate erneuern**. 




## <a name="common-issues"></a>Häufige Probleme
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die Tutorials zum Einrichten der Notfallwiederherstellung von [physischen Servern](./physical-azure-disaster-recovery.md) in Azure an.
