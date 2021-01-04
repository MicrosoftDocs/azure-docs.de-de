---
title: Verwalten des Azure Log Analytics-Agents
description: In diesem Artikel werden die verschiedenen Verwaltungsaufgaben beschrieben, die Sie typischerweise während des Lebenszyklus des Windows- oder Linux-Agents für Log Analytics ausführen, der auf einem Computer bereitgestellt wird.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: 4d0ceacd37748e9761903d02fd7e052d70b10e15
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011879"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Verwalten und Warten des Log Analytics-Agents für Windows und Linux

Nach der erstmaligen Bereitstellung des Windows- oder Linux-Agents für Log Analytics in Azure Monitor müssen Sie den Agent neu konfigurieren, ihn aktualisieren oder ihn vom Computer entfernen, wenn er in seinem Lebenszyklus die Deaktivierungsphase erreicht hat. Sie können diese Routinewartungsaufgaben einfach manuell oder durch Automatisierung bewältigen, wodurch sowohl Betriebsfehler als auch Kosten reduziert werden.

## <a name="upgrading-agent"></a>Aktualisierung des Agent

Der Log Analytics-Agent für Windows und Linux kann manuell oder automatisch auf die neueste Version aktualisiert werden, je nach Bereitstellungsszenario und Umgebung, in der die VM ausgeführt wird. Die folgenden Methoden können zum Aktualisieren des Agents verwendet werden.

| Environment | Installationsmethode | Aktualisierungsmethode |
|--------|----------|-------------|
| Azure VM | VM-Erweiterung für den Log Analytics-Agent für Windows/Linux | Der Agent wird standardmäßig automatisch aktualisiert, es sei denn, Sie haben Ihre Azure Resource Manager-Vorlage so konfiguriert, dass sie sich abmeldet, indem Sie die Eigenschaft *autoUpgradeMinorVersion* auf **false** setzen. |
| Benutzerdefinierte Azure VM-Images | Manuelles Installieren des Log Analytics-Agent fürs Windows oder Linux | Das Aktualisieren von VMs auf die neueste Version des Agents muss über die Befehlszeile mit dem Windows-Installationspaket oder dem selbstextrahierenden und installierbaren Shellskript-Bundle von Linux erfolgen.|
| Nicht-Azure-VMs | Manuelles Installieren des Log Analytics-Agent fürs Windows oder Linux | Das Aktualisieren von VMs auf die neueste Version des Agents muss über die Befehlszeile mit dem Windows-Installationspaket oder dem selbstextrahierenden und installierbaren Shellskript-Bundle von Linux erfolgen. |

### <a name="upgrade-windows-agent"></a>Aktualisieren des Windows-Agents 

Um den Agent auf einem virtuellen Windows-Computer auf die neueste Version zu aktualisieren, die nicht über die VM-Erweiterung für Log Analytics installiert wurde, erfolgt die Ausführung entweder über die Eingabeaufforderung, das Skript oder eine andere Automatisierungslösung oder über den msi-Setup-Assistenten für MMASetup-\<platform\>.  

Sie können die neueste Version des Windows-Agents aus Ihrem Log Analytics-Arbeitsbereich herunterladen, indem Sie die folgenden Schritte ausführen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie im Azure-Portal auf **Alle Dienste**. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics-Arbeitsbereiche** aus.

3. Wählen Sie in der Liste der Log Analytics-Arbeitsbereiche den Arbeitsbereich aus.

4. Wählen Sie in Ihrem Log Analytics-Arbeitsbereich **Erweiterte Einstellungen**, dann **Verbundene Quellen** und schließlich **Windows-Server**.

5. Wählen Sie auf der Seite **Windows-Server** die entsprechende Version für **Windows-Agent herunterladen** aus, um in Abhängigkeit von der Prozessorarchitektur des Windows-Betriebssystems herunterzuladen.

>[!NOTE]
>Während des Upgrades des Log Analytics-Agents für Windows wird die Konfiguration oder Rekonfiguration eines Arbeitsbereichs, an den berichtet werden soll, nicht unterstützt. Um den Agenten zu konfigurieren, müssen Sie eine der unter [Hinzufügen oder Entfernen eines Arbeitsbereichs](#adding-or-removing-a-workspace) aufgeführten unterstützten Methoden ausführen.
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>So aktualisieren Sie mit dem Setup-Assistenten

1. Melden Sie sich auf dem Computer mit einem Konto an, das über Administratorrechte verfügt.

2. Führen Sie **MMASetup-\<platform\>.exe** aus, um den Setup-Assistenten zu starten.

3. Klicken Sie auf der ersten Seite des Setup-Assistenten auf **Weiter**.

4. Klicken Sie im Dialogfeld **Microsoft Monitoring Agent-Setup** auf **Ich stimme zu**, um die Lizenzvereinbarung anzunehmen.

5. Klicken Sie im Dialogfeld **Microsoft Monitoring Agent-Setup** auf **Upgrade**. Die Statusseite zeigt den Fortschritt des Upgrades an.

6. Wenn die Seite **Die Microsoft Monitoring Agent-Konfiguration wurde erfolgreich abgeschlossen.** angezeigt wird, klicken Sie auf **Fertig stellen**.

#### <a name="to-upgrade-from-the-command-line"></a>So aktualisieren Sie über die Befehlszeile

1. Melden Sie sich auf dem Computer mit einem Konto an, das über Administratorrechte verfügt.

2. Um die Installationsdateien des Agent zu extrahieren, führen Sie `MMASetup-<platform>.exe /c` von einer Eingabeaufforderung mit erhöhten Rechten aus. Sie werden dann nach dem Pfad gefragt, in den die Dateien extrahiert werden sollen. Alternativ können Sie den Pfad angeben, indem Sie die Argumente `MMASetup-<platform>.exe /c /t:<Full Path>` übergeben.

3. Führen Sie den folgenden Befehl aus, wobei D:\ der Speicherort für die Upgradeprotokolldatei ist.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Aktualisieren des Linux-Agents 

Das Upgrade von früheren Versionen (> 1.0.0-47) wird unterstützt. Beim Ausführen der Installation mit dem Befehl `--upgrade` werden alle Komponenten des Agents auf die neueste Version aktualisiert.

Führen Sie den folgenden Befehl aus, um den Agent zu aktualisieren.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Hinzufügen oder Entfernen von Arbeitsbereichen

### <a name="windows-agent"></a>Windows-Agent
Die Schritte in diesem Abschnitt sind notwendig, wenn Sie den Windows-Agent nicht nur neu konfigurieren möchten, um an einen anderen Arbeitsbereich zu berichten oder einen Arbeitsbereich aus seiner Konfiguration zu entfernen, sondern auch, wenn Sie den Agent konfigurieren möchten, um an mehr als einen Arbeitsbereich zu berichten (allgemein als Multihosting bezeichnet). Die Konfiguration des Windows-Agents für die Berichterstattung an mehrere Arbeitsbereiche kann nur nach der anfänglichen Einrichtung des Agents und mit den nachfolgend beschriebenen Methoden durchgeführt werden.    

#### <a name="update-settings-from-control-panel"></a>Aktualisieren von Einstellungen über die Systemsteuerung

1. Melden Sie sich auf dem Computer mit einem Konto an, das über Administratorrechte verfügt.

2. Öffnen Sie die **Systemsteuerung**.

3. Wählen Sie **Microsoft Monitoring Agent** aus, und klicken Sie dann auf die Registerkarte **Azure Log Analytics**.

4. Wenn Sie einen Arbeitsbereich entfernen, wählen Sie ihn aus, und klicken Sie dann auf **Entfernen**. Wiederholen Sie diesen Schritt für alle anderen Arbeitsbereiche, für die der Agent keine Berichte mehr erstellen soll.

5. Wenn Sie einen Arbeitsbereich hinzufügen, klicken Sie auf **Hinzufügen**. Fügen Sie dann im Dialogfeld **Log Analytics-Arbeitsbereich hinzufügen** die Arbeitsbereichs-ID und den Arbeitsbereichsschlüssel (Primärschlüssel) ein. Wenn der Computer in einen Log Analytics-Arbeitsbereich in Azure Government Cloud melden soll, wählen Sie „Azure US-Regierung“ aus der Dropdownliste „Azure Cloud“ aus.

6. Klicken Sie auf **OK** , um die Änderungen zu speichern.

#### <a name="remove-a-workspace-using-powershell"></a>Entfernen eines Arbeitsbereichs mithilfe von PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Hinzufügen eines Arbeitsbereichs in Azure Commercial mithilfe von PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Hinzufügen eines Arbeitsbereichs in Azure für US-Regierungsbehörden mithilfe von PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Wenn Sie bereits die Befehlszeile oder Skripts zum Installieren oder Konfigurieren des Agents verwendet haben, sollten Sie wissen, dass `EnableAzureOperationalInsights` durch `AddCloudWorkspace` und `RemoveCloudWorkspace` ersetzt wurde.
>

### <a name="linux-agent"></a>Linux-Agent
Die folgenden Schritte veranschaulichen, wie Sie den Linux-Agent neu konfigurieren, wenn Sie sich entscheiden, ihn mit einem anderen Arbeitsbereich zu registrieren, oder einen Arbeitsbereich aus seiner Konfiguration entfernen möchten.

1. Um sicherzustellen, dass er bei einem Arbeitsbereich registriert ist, führen Sie den folgenden Befehl aus:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Es sollte ungefähr folgender Status zurückgegeben werden:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Es ist wichtig, dass der Status auch zeigt, dass der Agent ausgeführt wird, da andernfalls die folgenden Schritte zum Neukonfigurieren des Agents nicht erfolgreich abgeschlossen werden.

2. Wenn er bereits mit einem Arbeitsbereich registriert ist, entfernen Sie den registrierten Arbeitsbereich mit dem folgenden Befehl. Falls er noch nicht registriert ist, fahren Sie mit dem nächsten Schritt fort.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Führen Sie zur Registrierung bei einem anderen Arbeitsbereich den folgenden Befehl aus:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Führen Sie den folgenden Befehl aus, um sicherzustellen, dass Ihre Änderungen wirksam sind:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Es sollte ungefähr folgender Status zurückgegeben werden:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Der Agent-Dienst muss nicht neu gestartet werden, damit die Änderungen wirksam werden.

## <a name="update-proxy-settings"></a>Aktualisieren der Proxyeinstellungen
Verwenden Sie eine der folgenden Methoden, um den Agent so zu konfigurieren, dass er mit dem Dienst nach der Bereitstellung über einen Proxyserver oder ein [Log Analytics-Gateway](gateway.md) kommuniziert.

### <a name="windows-agent"></a>Windows-Agent

#### <a name="update-settings-using-control-panel"></a>Aktualisieren von Einstellungen über die Systemsteuerung

1. Melden Sie sich auf dem Computer mit einem Konto an, das über Administratorrechte verfügt.

2. Öffnen Sie die **Systemsteuerung**.

3. Wählen Sie **Microsoft Monitoring Agent** aus, und klicken Sie dann auf die Registerkarte **Proxyeinstellungen**.

4. Klicken Sie auf **Proxyserver verwenden**, und geben Sie die URL und die Portnummer des Proxyservers oder Gateways an. Wenn der Proxyserver oder das Log Analytics-Gateway eine Authentifizierung erfordert, geben Sie den Benutzernamen und das Kennwort für die Authentifizierung ein, und klicken Sie dann auf **OK**.

#### <a name="update-settings-using-powershell"></a>Aktualisieren von Einstellungen mithilfe von PowerShell

Kopieren Sie den folgenden PowerShell-Beispielcode, aktualisieren Sie ihn mit umgebungsspezifischen Informationen, und speichern Sie ihn mit einer PS1-Erweiterung. Führen Sie das Skript auf jedem Computer aus, der sich direkt mit dem Log Analytics-Arbeitsbereich in Azure Monitor verbindet.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

### <a name="linux-agent"></a>Linux-Agent
Führen Sie die folgenden Schritte aus, wenn Ihre Linux-Computer über einen Proxyserver oder ein Log Analytics-Gateway kommunizieren müssen. Der Wert für die Proxykonfiguration weist die folgende Syntax auf: `[protocol://][user:password@]proxyhost[:port]`. Für die *proxyhost*-Eigenschaft kann ein vollqualifizierter Domänenname oder eine IP-Adresse des Proxyservers verwendet werden.

1. Bearbeiten Sie die Datei `/etc/opt/microsoft/omsagent/proxy.conf`, indem Sie die folgenden Befehle ausführen. Ändern Sie die Werte, damit Sie Ihren spezifischen Einstellungen entsprechen.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
    ```

2. Starten Sie den Agent neu, indem Sie den folgenden Befehl ausführen:

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ```

## <a name="uninstall-agent"></a>Uninstall agent
Verwenden Sie eines der folgenden Verfahren, um den Windows- oder Linux-Agent über die Befehlszeile oder mithilfe des Setup-Assistenten zu deinstallieren.

### <a name="windows-agent"></a>Windows-Agent

#### <a name="uninstall-from-control-panel"></a>Deinstallieren über die Systemsteuerung
1. Melden Sie sich auf dem Computer mit einem Konto an, das über Administratorrechte verfügt.

2. Klicken Sie in der **Systemsteuerung** auf **Programs und Features**.

3. Klicken Sie in **Programme und Features** auf die Option **Microsoft Monitoring Agent**, dann auf **Deinstallieren** und anschließend auf **Ja**.

>[!NOTE]
>Der Agent-Setup-Assistent kann auch durch Doppelklicken auf die Datei **MMASetup-\<platform\>.exe** ausgeführt werden, die von einem Arbeitsbereich im Azure-Portal heruntergeladen werden kann.

#### <a name="uninstall-from-the-command-line"></a>Deinstallieren über die Befehlszeile
Die heruntergeladene Datei für den Agent ist ein eigenständiges Installationspaket, das mit IExpress erstellt wurde. Das Setup-Programm für den Agent und die unterstützenden Dateien sind im Paket enthalten und müssen extrahiert werden, um eine ordnungsgemäße Deinstallation mit der im folgenden Beispiel gezeigten Befehlszeile durchführen zu können.

1. Melden Sie sich auf dem Computer mit einem Konto an, das über Administratorrechte verfügt.

2. Um die Installationsdateien des Agent zu extrahieren, führen Sie `extract MMASetup-<platform>.exe` von einer Eingabeaufforderung mit erhöhten Rechten aus. Sie werden dann nach dem Pfad gefragt, in den die Dateien extrahiert werden sollen. Alternativ können Sie den Pfad angeben, indem Sie die Argumente `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>` übergeben. Weitere Informationen zu den von IExpress unterstützten Befehlszeilenschaltern finden Sie unter [Befehlszeilenoptionen für IExpress-Softwareupdatepakete](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages). Sie können anschließend das Beispiel aktualisieren, um es an Ihre Bedürfnisse anzupassen.

3. Geben Sie an der Eingabeaufforderung `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb` ein.

### <a name="linux-agent"></a>Linux-Agent
Führen Sie den folgenden Befehl auf dem Linux-Computer aus, um den Agent zu entfernen. Mit dem Argument *--purge* werden der Agent und die dazugehörige Konfiguration vollständig entfernt.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Konfigurieren von Agents zum Berichten an eine Operations Manager-Verwaltungsgruppe

### <a name="windows-agent"></a>Windows-Agent
Führen Sie die folgenden Schritte aus, um den Log Analytics-Agent für Windows so zu konfigurieren, dass Meldungen an eine System Center Operations Manager-Verwaltungsgruppe gesendet werden.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Melden Sie sich auf dem Computer mit einem Konto an, das über Administratorrechte verfügt.

2. Öffnen Sie die **Systemsteuerung**.

3. Klicken Sie auf **Microsoft Monitoring Agent** und dann auf die Registerkarte **Operations Manager**.

4. Wenn Ihre Operations Manager-Server über eine Integration mit Active Directory verfügen, klicken Sie auf **Verwaltungsgruppenzuweisungen aus AD DS automatisch aktualisieren**.

5. Klicken Sie auf **Hinzufügen**, um das Dialogfeld **Verwaltungsgruppe hinzufügen** zu öffnen.

6. Geben Sie im Feld **Verwaltungsgruppenname** den Namen Ihrer Verwaltungsgruppe ein.

7. Geben Sie im Feld **Primärer Verwaltungsserver** den Computernamen des primären Verwaltungsservers ein.

8. Geben Sie im Feld **Verwaltungsserverport** die TCP-Portnummer ein.

9. Wählen Sie unter **Agentaktionskonto** das lokale Systemkonto oder ein lokales Domänenkonto aus.

10. Klicken Sie zum Schließen des Dialogfelds **Verwaltungsgruppe hinzufügen** auf **OK**. Klicken Sie anschließend zum Schließen des Dialogfelds **Eigenschaften von Microsoft Monitoring Agent** auf **OK**.

### <a name="linux-agent"></a>Linux-Agent
Führen Sie die folgenden Schritte aus, um den Log Analytics-Agent für Linux so zu konfigurieren, dass Meldungen an eine System Center Operations Manager-Verwaltungsgruppe gesendet werden.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Bearbeiten Sie die Datei `/etc/opt/omi/conf/omiserver.conf`

2. Stellen Sie sicher, dass die Zeile, die mit `httpsport=` beginnt, den Port 1270 definiert. Beispiel: `httpsport=1270`

3. Starten Sie den OMI-Server neu: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie [Problembehandlung für den Linux-Agent](agent-linux-troubleshoot.md), wenn bei der Installation oder Verwaltung des Linux-Agent Probleme auftreten.

- Lesen Sie [Problembehandlung für den Windows-Agent](agent-windows-troubleshoot.md), wenn bei der Installation oder Verwaltung des Windows-Agent Probleme auftreten.
