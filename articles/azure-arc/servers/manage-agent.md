---
title: Verwalten des Agents für Azure Arc-fähige Server
description: In diesem Artikel werden die verschiedenen Verwaltungsaufgaben beschrieben, die Sie typischerweise während des Lebenszyklus des Connected Machine-Agents für Azure Arc-fähige Server ausführen.
ms.date: 10/30/2020
ms.topic: conceptual
ms.openlocfilehash: 63db1177b193cad66208964ec377fab0779f23ba
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130968"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Verwalten des Connected Machine-Agent

Nach der erstmaligen Bereitstellung des Connected Machine-Agents für Server mit Azure Arc-Unterstützung für Windows oder Linux müssen Sie den Agent u. U. neu konfigurieren, aktualisieren oder vom Computer entfernen. Sie können diese Routinewartungsaufgaben einfach manuell oder durch Automatisierung bewältigen, wodurch sowohl Betriebsfehler als auch Kosten reduziert werden.

## <a name="before-uninstalling-agent"></a>Vor dem Deinstallieren des Agents

Bevor Sie den Connected Machine-Agent von Ihrem Azure Arc-fähigen Server entfernen, sollten Sie Folgendes berücksichtigen, um unerwartete Probleme oder Kosten auf Ihrer Azure-Rechnung zu vermeiden:

* Wenn Sie Azure-VM-Erweiterungen für einen aktivierten Server bereitgestellt haben und den Connected Machine-Agent entfernen oder die Ressource löschen, die den Azure Arc-fähigen Server in der Ressourcengruppe darstellt, werden diese Erweiterungen weiterhin normal ausgeführt.

* Wenn Sie die Ressource löschen, die den Azure Arc-fähigen Server in der Ressourcengruppe darstellt, ohne die VM-Erweiterungen zu deinstallieren, können Sie die installierten VM-Erweiterungen nicht verwalten, wenn Sie den Computer neu registrieren.

Für Server oder Computer, die nicht mehr über Azure Arc-fähige Server verwaltet werden sollen, müssen Sie folgende Schritte ausführen, um die Verwaltung erfolgreich zu beenden:

1. Entfernen Sie die VM-Erweiterungen von dem Computer oder dem Server. Ausführlichere Informationen hierzu finden Sie weiter unten.

2. Trennen Sie die Verbindung zwischen dem Computer und Azure Arc über eine der folgenden Methoden:

    * Ausführen des Befehls `azcmagent disconnect` auf dem Computer oder dem Server

    * Durch Auswählen des registrierten Servers im Azure-Portal und Klicken auf **Löschen** in der oberen Leiste

    * Mithilfe der [Azure CLI](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-cli#delete-resource) oder mithilfe von [Azure PowerShell](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell#delete-resource) Verwenden Sie `Microsoft.HybridCompute/machines` für den Parameter `ResourceType`.

3. Deinstallieren Sie den Agent auf dem Computer oder Server. Führen Sie die folgenden Schritte aus.

## <a name="upgrading-agent"></a>Aktualisierung des Agent

Der Azure Connected Machine-Agent wird regelmäßig aktualisiert, um Fehlerbehebungen, Stabilitätsverbesserungen und neue Funktionen bereitzustellen. [Azure Advisor](../../advisor/advisor-overview.md) identifiziert Ressourcen, die nicht die aktuelle Version des Machine-Agents verwenden, und empfiehlt ein Upgrade auf die neueste Version. Wenn Sie den Server mit Arc-Unterstützung auswählen, werden Sie über ein Banner auf der Seite **Übersicht** oder beim Zugriff auf Advisor über das Azure-Portal benachrichtigt.

Der Azure Connected Machine-Agent für Windows und Linux kann abhängig von Ihren Anforderungen manuell oder automatisch auf das neueste Release aktualisiert werden.

In der folgenden Tabelle werden die Methoden beschrieben, die für das Agent-Upgrade unterstützt werden.

| Betriebssystem | Aktualisierungsmethode |
|------------------|----------------|
| Windows | Manuell<br> Windows-Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) |

### <a name="windows-agent"></a>Windows-Agent

Das Updatepaket für den Connected Machine-Agent für Windows kann über folgende Quellen bezogen werden:

* Microsoft Update

* [Microsoft Update-Katalog](https://www.catalog.update.microsoft.com/Home.aspx)

* [Windows Installer-Paket für den Windows-Agent](https://aka.ms/AzureConnectedMachineAgent) im Microsoft Download Center

Der Agent kann auf verschiedene Arten aktualisiert werden, um Ihren Verwaltungsprozess für Softwareupdates zu unterstützen. Er kann nicht nur über Microsoft Update bezogen, sondern auch über die Eingabeaufforderung, ein Skript oder eine andere Automatisierungslösung oder über den Benutzeroberflächen-Assistenten heruntergeladen und ausgeführt werden, indem Sie `AzureConnectedMachine.msi` ausführen.

> [!NOTE]
> * Zum Upgraden des Agent sind *Administratorberechtigungen* erforderlich.
> * Laden Sie für das manuelle Upgrade zunächst das Installer-Paket herunter, und kopieren Sie es in einen Ordner auf dem Zielserver, oder verwenden Sie einen freigegebenen Netzwerkordner. 

Wenn Sie mit den Befehlszeilenoptionen für Windows Installer-Pakete nicht vertraut sind, lesen Sie die Artikel [Standardmäßige Installer-Befehlszeilenoptionen](/windows/win32/msi/standard-installer-command-line-options) und [Befehlszeilenoptionen](/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>So aktualisieren Sie mit dem Setup-Assistenten

1. Melden Sie sich auf dem Computer mit einem Konto an, das über Administratorrechte verfügt.

2. Führen Sie **AzureConnectedMachineAgent.msi** aus, um den Setup-Assistenten zu starten.

Der Setup-Assistent ermittelt, ob eine frühere Version vorhanden ist, und führt dann automatisch ein Upgrade des Agent aus. Wenn das Upgrade abgeschlossen ist, wird der Setup-Assistent automatisch geschlossen.

#### <a name="to-upgrade-from-the-command-line"></a>So aktualisieren Sie über die Befehlszeile

1. Melden Sie sich auf dem Computer mit einem Konto an, das über Administratorrechte verfügt.

2. Führen Sie den folgenden Befehl aus, um den Agent im Hintergrund zu aktualisieren und eine Setupprotokolldatei im Ordner `C:\Support\Logs` zu erstellen.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux-Agent

Mit zwei Befehlen wird der Agent auf einem Linux-Computer auf die neueste Version aktualisiert. Ein Befehl aktualisiert den lokalen Paketindex mit der Liste der neuesten verfügbaren Pakete aus den Repositorys und ein weiterer Befehl führt ein Upgrade des lokalen Pakets durch.

Das aktuelle Agent-Paket können Sie über das [Microsoft-Paketrepository](https://packages.microsoft.com/) herunterladen.

> [!NOTE]
> Um ein Upgrade des Agents vorzunehmen, benötigen Sie *root* -Zugriffsberechtigungen oder ein Konto mit erhöhten Rechten, das Sudo verwendet.

#### <a name="upgrade-ubuntu"></a>Ubuntu-Upgrade

1. Führen Sie den folgenden Befehl aus, um den lokalen Paketindex mit den neuesten Änderungen in den Repositorys zu aktualisieren:

    ```bash
    apt update
    ```

2. Führen Sie den folgenden Befehl aus, um Ihr System upzugraden:

    ```bash
    apt upgrade
    ```

Aktionen des Befehls [apt](https://help.ubuntu.com/lts/serverguide/apt.html) wie Installation und Entfernung von Paketen werden in der `/var/log/dpkg.log`-Protokolldatei protokolliert.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Upgraden von Red Hat/CentOS/Amazon Linux

1. Führen Sie den folgenden Befehl aus, um den lokalen Paketindex mit den neuesten Änderungen in den Repositorys zu aktualisieren:

    ```bash
    yum check-update
    ```

2. Führen Sie den folgenden Befehl aus, um Ihr System upzugraden:

    ```bash
    yum update
    ```

Aktionen des Befehls [yum](https://access.redhat.com/articles/yum-cheat-sheet) wie Installation und Entfernung von Paketen werden in der `/var/log/yum.log`-Protokolldatei protokolliert. 

#### <a name="upgrade-suse-linux-enterprise"></a>Upgrade von SUSE Linux Enterprise

1. Führen Sie den folgenden Befehl aus, um den lokalen Paketindex mit den neuesten Änderungen in den Repositorys zu aktualisieren:

    ```bash
    zypper refresh
    ```

2. Führen Sie den folgenden Befehl aus, um Ihr System upzugraden:

    ```bash
    zypper update
    ```

Aktionen des Befehls [zypper](https://en.opensuse.org/Portal:Zypper) wie Installation und Entfernung von Paketen werden in der `/var/log/zypper.log`-Protokolldatei protokolliert.

## <a name="about-the-azcmagent-tool"></a>Informationen zum Azcmagent-Tool

Das Azcmagent-Tool (Azcmagent.exe) wird verwendet, um den Connected Machine-Agent für Azure Arc-fähige Server während der Installation zu konfigurieren oder die Erstkonfiguration des Agents nach der Installation zu ändern. Azcmagent.exe bietet Befehlszeilenparameter zum Anpassen des Agents und zum Anzeigen seines Status:

* **Connect** : Verbindet den Computer mit Azure Arc.

* **Disconnect** : Trennt den Computer von Azure Arc.

* **Show** : Zeigt den Agent-Status und seine Konfigurationseigenschaften an (Name der Ressourcengruppe, Abonnement-ID, Version usw.), die bei der Behandlung eines Problems mit dem Agent helfen können. Verwenden Sie den Parameter `-j`, um die Ergebnisse im JSON-Format auszugeben.

* **Logs:** Hiermit wird eine ZIP-Datei im aktuellen Verzeichnis erstellt, die Protokolle enthält, die Sie bei der Problembehandlung unterstützen.

* **Version:** Hiermit wird die Version des Connected Machine-Agents angezeigt.

* **-h oder -help** : Zeigt verfügbare Befehlszeilenparameter an.

    Um beispielsweise ausführliche Hilfeinformationen für den Parameter **Reconnect** anzuzeigen, geben Sie `azcmagent reconnect -h` ein. 

* **-v oder -verbose** : Aktiviert die ausführliche Protokollierung.

Sie können einen **Connect** - und **Disconnect** -Vorgang manuell ausführen, während Sie interaktiv angemeldet sind, oder Sie können mithilfe desselben Dienstprinzipals automatisieren, den Sie für das Onboarding mehrerer Agents verwendet haben, oder mithilfe eines [Zugriffstokens](../../active-directory/develop/access-tokens.md) der Microsoft Identity-Plattform. Wenn Sie keinen Dienstprinzipal zum Registrieren des Computers bei Azure Arc-fähigen Servern verwendet haben, finden Sie im folgenden [Artikel](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) Informationen zum Erstellen eines Dienstprinzipals.

>[!NOTE]
>Sie müssen auf Linux-Computern über *Stamm* zugriffsberechtigungen verfügen, um **azcmagent** ausführen zu können.

### <a name="connect"></a>Verbinden

Dieser Parameter gibt eine Ressource in Azure Resource Manager an, die den in Azure erstellten Computer darstellt. Die Ressource befindet sich im angegebenen Abonnement und der angegebenen Ressourcengruppe, und die Daten zu dem Computer werden in der durch den `--location`-Parameter angegebenen Azure-Region gespeichert. Der Standardressourcenname ist der Hostname des Computers, wenn nichts angegeben wird.

Ein Zertifikat, das der systemseitig zugewiesenen Identität dieses Computers entspricht, wird dann heruntergeladen und lokal gespeichert. Sobald dieser Schritt abgeschlossen ist, beginnen Azure Connected Machine Metadata Service und der Gastkonfigurations-Agent die Synchronisierung mit Azure Arc-fähigen Servern.

Führen Sie den folgenden Befehl aus, um eine Verbindung mithilfe des Dienstprinzipals herzustellen:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Um mithilfe eines Zugriffstokens eine Verbindung herzustellen, führen Sie den folgenden Befehl aus:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Führen Sie den folgenden Befehl aus, um eine Verbindung mit Ihren Anmeldeinformationen mit erhöhten Rechten (interaktiv), mit denen Sie angemeldet sind, herzustellen:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Trennen

Dieser Parameter gibt eine Ressource in Azure Resource Manager an, die den in Azure gelöschten Computer darstellt. Der Agent wird nicht vom Computer gelöscht, dies muss als separater Schritt erfolgen. Nachdem die Verbindung des Computers getrennt wurde und Sie ihn neu bei Azure Arc-fähigen Servern registrieren möchten, führen Sie `azcmagent connect` aus, um eine neue Ressource in Azure zu erstellen.

> [!NOTE]
> Wenn Sie mindestens eine Azure-VM-Erweiterung für Ihren Azure-Arc-fähigen Server bereitgestellt haben und die VM-Registrierung in Azure löschen, sind die Erweiterungen weiterhin installiert. Es ist wichtig zu verstehen, dass der Computer je nach installierter Erweiterung seine Funktion aktiv ausführt. Auf Computern, die außer Betrieb genommen oder nicht mehr von Azure Arc-fähigen Servern verwaltet werden sollen, müssen zunächst die Erweiterungen entfernt werden, bevor die Registrierung aus Azure entfernt wird.

Führen Sie den folgenden Befehl aus, um die Verbindung mithilfe des Dienstprinzipals zu trennen:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Um mithilfe eines Zugriffstokens die Verbindung zu trennen, führen Sie den folgenden Befehl aus:

`azcmagent disconnect --access-token <accessToken>`

Führen Sie den folgenden Befehl aus, um die Verbindung mit Ihren Anmeldeinformationen mit erhöhten Rechten (interaktiv), mit denen Sie angemeldet sind, zu trennen:

`azcmagent disconnect`

## <a name="remove-the-agent"></a>Entfernen des Agents

Führen Sie eine der folgenden Methoden aus, um den Conncected Machine-Agent für Windows oder Linux von dem Computer zu deinstallieren. Durch das Entfernen des Agents wird weder die Registrierung des Computers bei Azure Arc-fähigen Servern aufgehoben noch werden die installierten Azure-VM-Erweiterungen entfernt. Sie müssen diese Schritte separat durchführen, wenn Sie den Computer in Azure nicht mehr verwalten müssen. Außerdem sollten die Schritte vor der Deinstallation des Agents abgeschlossen werden.

### <a name="windows-agent"></a>Windows-Agent

Beider der folgenden Methoden entfernen den Agent, aber sie entfernen nicht den Ordner *C:\Programme\AzureConnectedMachineAgent* auf dem Computer.

#### <a name="uninstall-from-control-panel"></a>Deinstallieren über die Systemsteuerung

1. Gehen Sie zum Deinstallieren des Windows-Agents auf dem Computer wie folgt vor:

    a. Melden Sie sich bei dem Computer mit einem Konto an, das über Administratorberechtigungen verfügt.  
    b. Wählen Sie in der **Systemsteuerung** die Option **Programme und Features** aus.  
    c. Wählen Sie unter **Programme und Features** Folgendes aus: **Azure Connected Machine-Agent**  > **Deinstallieren**  > **Ja**.  

    >[!NOTE]
    > Sie können auch auf das Installer-Paket **AzureConnectedMachineAgent.msi** doppelklicken, um den Setup-Assistenten für den Agent auszuführen.

#### <a name="uninstall-from-the-command-line"></a>Deinstallieren über die Befehlszeile

Um den Agent manuell über die Eingabeaufforderung zu deinstallieren oder eine automatisierte Methode wie z. B. ein Skript zu verwenden, können Sie das folgende Beispiel nutzen. Zuerst müssen Sie den Produktcode, d. h. eine GUID, die der Prinzipalbezeichner des Anwendungspakets ist, vom Betriebssystem abrufen. Die Deinstallation erfolgt mithilfe der Msiexec.exe-Befehlszeile – `msiexec /x {Product Code}`.

1. Öffnen Sie den Registrierungs-Editor.

2. Suchen Sie unter dem Registrierungsschlüssel `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` nach der Produktcode-GUID, und kopieren Sie sie.

3. Anschließend können Sie den Agent mithilfe von Msiexec gemäß der folgenden Beispiele deinstallieren:

   * Eingabe über die Befehlszeile:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Sie können die gleichen Schritte auch mithilfe von PowerShell ausführen:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux-Agent

> [!NOTE]
> Um den Agent zu deinstallieren, benötigen Sie *root* -Zugriffsberechtigungen oder ein Konto mit erhöhten Rechten, das Sudo verwendet.

Beim Deinstallieren des Linux-Agents ist der zu verwendende Befehl vom Linux-Betriebssystem abhängig.

- Führen Sie für Ubuntu den folgenden Befehl aus:

    ```bash
    sudo apt purge azcmagent
    ```

- Führen Sie für RHEL, CentOS und Amazon Linux den folgenden Befehl aus:

    ```bash
    sudo yum remove azcmagent
    ```

- Führen Sie für SLES den folgenden Befehl aus:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Aufheben der Registrierung eines Computers

Wenn Sie beabsichtigen, die Verwaltung des Computers mit unterstützenden Diensten in Azure zu beenden, führen Sie die folgenden Schritte aus, um die Registrierung des Computers bei Azure Arc-fähigen Servern aufzuheben. Sie können diese Schritte entweder vor oder nach dem Entfernen des Connected Machine-Agents von dem Computer ausführen.

1. Öffnen Sie die Azure Arc-fähigen Server, indem Sie zum [Azure-Portal](https://aka.ms/hybridmachineportal) navigieren.

2. Wählen Sie den Computer in der Liste aus. Wählen Sie dann die Auslassungspunkte ( **...** ) und anschließend **Löschen** aus.

## <a name="update-or-remove-proxy-settings"></a>Aktualisieren oder Entfernen von Proxyeinstellungen

Um den Agent für die Kommunikation mit dem Dienst über einen Proxyserver zu konfigurieren, entfernen Sie diese Konfiguration nach der Bereitstellung, oder führen Sie eine der folgenden Methoden aus, um diese Aufgabe durchzuführen.

### <a name="windows"></a>Windows

Führen Sie den folgenden Befehl aus, um die Umgebungsvariable für den Proxyserver festzulegen:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

Um den Agent so zu konfigurieren, dass er die Kommunikation über einen Proxyserver einstellt, führen Sie den folgenden Befehl aus, um die Umgebungsvariable für den Proxyserver zu entfernen und den Agent-Dienst neu zu starten:

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

Um den Proxyserver festzulegen, führen Sie den folgenden Befehl aus dem Verzeichnis aus, in das Sie das Agent-Installationspaket heruntergeladen haben:

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

Um den Agent so zu konfigurieren, dass er die Kommunikation über einen Proxyserver einstellt, führen Sie den folgenden Befehl aus, um die Proxykonfiguration zu entfernen:

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zur Problembehandlung finden Sie im [Handbuch zur Problembehandlung des Connected Machine-Agents](troubleshoot-agent-onboard.md).

* Erfahren Sie, wie Sie Ihren Computer mithilfe von [Azure Policy](../../governance/policy/overview.md) verwalten, wie z. B. bei der VM-[Gastkonfiguration](../../governance/policy/concepts/guest-configuration.md), dem Überprüfen, ob der Computer dem erwarteten Log Analytics-Arbeitsbereich Bericht erstattet, beim Aktivieren der Überwachung mit [Azure Monitor mit VMs](../../azure-monitor/insights/vminsights-enable-policy.md) und vieles mehr.

* Weitere Informationen zum [Log Analytics-Agent](../../azure-monitor/platform/log-analytics-agent.md). Der Log Analytics-Agent für Windows und Linux ist erforderlich, wenn Sie Daten zur Betriebssystem- und Workloadüberwachung erfassen, diese mithilfe von Automation Runbooks oder Funktionen wie Updateverwaltung oder andere Azure-Dienste wie [Azure Security Center](../../security-center/security-center-introduction.md) nutzen möchten.