---
title: Bereitstellen eines Hybrid Runbook Workers unter Windows in Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie einen Hybrid Runbook Worker bereitstellen, mit dem Sie Runbooks auf Windows-basierten Computern in Ihrem lokalen Rechenzentrum oder einer Cloudumgebung ausführen können.
services: automation
ms.subservice: process-automation
ms.date: 11/24/2020
ms.topic: conceptual
ms.openlocfilehash: 09c86e23379cf9ccecf77d88a99ad6b3a7019a87
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100006988"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Bereitstellen eines Windows Hybrid Runbook Workers

Sie können die Benutzerfunktion Hybrid Runbook Worker von Azure Automation verwenden, um Runbooks direkt auf dem Azure- oder Nicht-Azure-Computer auszuführen, einschließlich der Server, die bei [Servern mit Azure Arc-Aktivierung](../azure-arc/servers/overview.md) registriert sind. Von dem Computer oder Server aus, der die Rolle hostet, können Sie Runbooks direkt auf dem Computer ausführen, und mit Ressourcen in der Umgebung, um diese lokalen Ressourcen zu verwalten.

Azure Automation speichert und verwaltet Runbooks und übermittelt sie dann an dafür festgelegte Computer. Dieser Artikel beschreibt, wie Sie einen Benutzer-Hybrid Runbook Worker auf einem Windows-Computer bereitstellen, den Worker entfernen und eine Hybrid Runbook Worker-Gruppe entfernen.

Lesen Sie nach dem erfolgreichen Bereitstellen eines Runbook Workers [Running runbooks on a Hybrid Runbook Worker (Ausführen von Runbooks auf einem Hybrid Runbook Worker)](automation-hrw-run-runbooks.md), um zu erfahren, wie Sie Ihre Runbooks für die Automatisierung von Prozessen in Ihrem lokalen Datencenter oder in einer anderen Cloudumgebung konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zunächst sicher, dass Sie über Folgendes verfügen.

### <a name="a-log-analytics-workspace"></a>Einen Log Analytics-Arbeitsbereich.

Die „Hybrid Runbook Worker“-Rolle ist hinsichtlich ihrer Installation und Konfiguration von einem Azure Monitor Log Analytics-Arbeitsbereich abhängig. Sie können ihn über den [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace) erstellen, mithilfe der [PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json) oder im [Azure-Portal](../azure-monitor/learn/quick-create-workspace.md).

Wenn Sie über keinen Azure Monitor Log Analytics-Arbeitsbereich verfügen, lesen Sie zuerst die [Entwurfsanleitung für Azure Monitor-Protokolle](../azure-monitor/platform/design-logs-deployment.md), bevor Sie einen Arbeitsbereich erstellen.

### <a name="log-analytics-agent"></a>Log Analytics-Agent

Die „Hybrid Runbook Worker“-Rolle erfordert den [Log Analytics-Agent](../azure-monitor/platform/log-analytics-agent.md) für das unterstützte Windows-Betriebssystem. Für Server oder Computer, die außerhalb von Azure gehostet werden, können Sie den Log Analytics-Agent mit [Servern mit Azure Arc-Aktivierung](../azure-arc/servers/overview.md) installieren.

### <a name="supported-windows-operating-system"></a>Unterstützte Windows-Betriebssysteme

Die Hybrid Runbook Worker-Funktion unterstützt die folgenden Betriebssysteme:

* Windows Server 2019 (einschließlich Server Core)
* Windows Server 2016 Versionen 1709 und 1803 (ohne Server Core)
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (einschließlich Multisession) und Pro
* Windows 8 Enterprise und Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Mindestanforderungen

Die Mindestanforderungen für einen System- und Benutzer-Hybrid Runbook Worker unter Windows sind:

* Windows PowerShell 5.1 ([WMF 5.1 herunterladen](https://www.microsoft.com/download/details.aspx?id=54616)). PowerShell Core wird nicht unterstützt.
* .NET Framework 4.6.2 oder höher
* Zwei Kerne
* 4 GB RAM
* Port 443 (ausgehend)

### <a name="network-configuration"></a>Netzwerkkonfiguration

Netzwerkanforderungen für den Hybrid Runbook Worker finden Sie unter [Konfigurieren des Netzwerks](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Hinzufügen eines Computers zu einer Hybrid Runbook Worker-Gruppe

Sie können den Workercomputer einer Hybrid Runbook Worker-Gruppe einem Ihrer Automation-Konten hinzufügen. Computer, auf denen der von der Updateverwaltung verwaltete System-Hybrid Runbook Worker gehostet wird, können einer Hybrid Runbook Worker-Gruppe hinzugefügt werden. Sie müssen aber für die Updateverwaltung und die Mitgliedschaft in der Hybrid Runbook Worker-Gruppe dasselbe Automation-Konto verwenden.

>[!NOTE]
>Die [Updateverwaltung](./update-management/overview.md) von Azure Automation installiert automatisch den System-Hybrid Runbook Worker auf einem Azure- oder Nicht-Azure-Computer, der für die Updateverwaltung aktiviert ist. Es erfolgt jedoch keine Registrierung des Workers für Hybrid Runbook Worker-Gruppen in Ihrem Automation-Konto. Zum Ausführen der Runbooks auf diesen Computern müssen Sie sie einer Hybrid Runbook Worker-Gruppe hinzufügen. Führen Sie Schritt 6 im Abschnitt [Manuelle Bereitstellung](#manual-deployment) aus, um ihn einer Gruppe hinzuzufügen.

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Aktivieren für die Verwaltung mit Azure Automation State Configuration

Informationen zum Aktivieren von Computern für die Verwaltung mit Azure Automation State Configuration finden Sie unter [Aktivieren von Computern für die Verwaltung mit Azure Automation State Configuration](automation-dsc-onboarding.md).

> [!NOTE]
> Um die Konfiguration der Computer zu verwalten, die die Hybrid Runbook Worker-Rolle mit DSC (Desired State Configuration) unterstützen, müssen Sie die Computer als DSC-Knoten hinzufügen.

## <a name="installation-options"></a>Installationsoptionen

Zum Installieren und Konfigurieren eines Benutzer-Hybrid Runbook Workers unter Windows können Sie eine der folgenden Methoden verwenden.

* Verwenden Sie ein bereitgestelltes PowerShell-Skript, um den Prozess des Konfigurierens eines oder mehrerer Windows-Computer vollständig zu [automatisieren](#automated-deployment). Dies ist die empfohlene Methode für Computer in Ihrem Rechenzentrum oder einer anderen Cloudumgebung.
* Importieren Sie die Automation-Lösung manuell, installieren Sie den Log Analytics-Agent für Windows, und konfigurieren Sie die Workerrolle auf dem Computer.

## <a name="automated-deployment"></a>Automatisierte Bereitstellung

Bei der automatisierten Bereitstellungsmethode wird das PowerShell-Skript **New-OnPremiseHybridWorker.ps1** verwendet, um die Hybrid Runbook Worker-Rolle unter Windows zu automatisieren und zu konfigurieren. Sie führt Folgendes aus:

* Installation der erforderlichen Module
* Anmelden mit Ihrem Azure-Konto
* Überprüfen des Vorhandenseins der angegebenen Ressourcengruppe und des Automation-Kontos
* Erstellen von Verweisen auf Automation-Kontoattribute
* Erstellen eines Azure Monitor Log Analytics-Arbeitsbereichs, falls nicht angegeben
* Aktivieren der Azure Automation-Lösung im Arbeitsbereich
* Herunterladen und Installieren des Log Analytics-Agents für Windows
* Registrieren des Computers als Hybrid Runbook Worker

Führen Sie die folgenden Schritte aus, um die Rolle auf Ihrem Windows-Computer mithilfe des Skripts zu installieren.

1. Laden Sie das Skript **New-OnPremiseHybridWorker.ps1** aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) herunter. Nachdem Sie das Skript heruntergeladen haben, kopieren Sie es auf den Zielcomputer, oder führen Sie es darauf aus. Das Skript **New-OnPremiseHybridWorker.ps1** verwendet während der Ausführung die folgenden Parameter.

    | Parameter | Status | BESCHREIBUNG |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | Obligatorisch. | Der Name der Ressourcengruppe, die Ihrem Automation-Konto zugeordnet ist. |
    | `AutomationAccountName` | Obligatorisch. | Der Name Ihres Automation-Kontos.
    | `Credential` | Optional | Die Anmeldeinformationen, die beim Anmelden bei der Azure-Umgebung verwendet werden sollen. |
    | `HybridGroupName` | Obligatorisch. | Der Name einer Hybrid Runbook Worker-Gruppe, die Sie als Ziel für die Runbooks angeben, die dieses Szenario unterstützen. |
    | `OMSResourceGroupName` | Optional | Der Name der Ressourcengruppe für den Log Analytics-Arbeitsbereich. Wenn diese Ressourcengruppe nicht angegeben ist, wird der Wert von `AAResourceGroupName` verwendet. |
    | `SubscriptionID` | Obligatorisch. | Der Bezeichner des Azure-Abonnements, das Ihrem Automation-Konto zugeordnet ist. |
    | `TenantID` | Optional | Der Bezeichner der Mandantenorganisation, die Ihrem Automation-Konto zugeordnet ist. |
    | `WorkspaceName` | Optional | Name des Log Analytics-Arbeitsbereichs. Falls kein Log Analytics-Arbeitsbereich vorhanden ist, wird dieser durch das Skript erstellt und konfiguriert. |

2. Öffnen Sie eine 64-Bit-PowerShell-Eingabeaufforderung mit erhöhten Rechten.

3. Navigieren Sie in der PowerShell-Eingabeaufforderung zu dem Ordner, der das Skript enthält, das Sie heruntergeladen haben. Ändern Sie die Werte der Parameter `AutomationAccountName`, `AAResourceGroupName`, `OMSResourceGroupName`, `HybridGroupName`, `SubscriptionID` und `WorkspaceName`. Führen Sie das Skript dann aus.

    Nach dem Ausführen des Skripts werden Sie aufgefordert, sich bei Azure zu authentifizieren. Sie müssen sich mit einem Konto anmelden, das Mitglied der Rolle **Abonnement-Administratoren** sowie Co-Administrator des Abonnements ist.

    ```powershell-interactive
    $NewOnPremiseHybridWorkerParameters = @{
      AutomationAccountName = <nameOfAutomationAccount>
      AAResourceGroupName   = <nameOfResourceGroup>
      OMSResourceGroupName  = <nameOfResourceGroup>
      HybridGroupName       = <nameOfHRWGroup>
      SubscriptionID        = <subscriptionId>
      WorkspaceName         = <nameOfLogAnalyticsWorkspace>
    }
    .\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
    ```

4. Sie werden aufgefordert, der Installation von NuGet zuzustimmen und sich mit Ihren Azure-Anmeldeinformationen zu authentifizieren. Wenn Sie nicht über die neueste Version von NuGet verfügen, können Sie sie über [Verfügbare NuGet-Distributionsversionen](https://www.nuget.org/downloads) herunterladen.

5. Überprüfen Sie die Bereitstellung, nachdem das Skript abgeschlossen wurde. Auf der Seite **Hybrid-Runbook-Workergruppen** in Ihrem Automation-Konto werden unter der Registerkarte **Benutzer-Hybrid Runbook Worker-Gruppe** die neue Gruppe sowie die Anzahl der Mitglieder angezeigt. Wenn die Gruppe bereits vorhanden ist, wird die Anzahl der Mitglieder erhöht. Sie können die Gruppe in der Liste auf der Seite auswählen. Wählen Sie im Menü auf der linken Seite **Hybrid Worker** aus. Auf der Seite **Hybrid Worker** werden die einzelnen Mitglieder der Gruppe aufgelistet.

## <a name="manual-deployment"></a>Manuelle Bereitstellung

Zum Installieren und Konfigurieren eines Windows Hybrid Runbook Workers führen Sie die folgenden Schritte durch.

1. Aktivieren Sie die Azure Automation-Lösung in Ihrem Log Analytics-Arbeitsbereich, indem Sie den folgenden Befehl an einer PowerShell-Eingabeaufforderung mit erhöhten Rechten oder in der Cloud Shell im [Azure-Portal](https://portal.azure.com) ausführen.

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Stellen Sie den Log Analytics-Agent auf dem Zielcomputer bereit.

    * Für Azure-VMs installieren Sie den Log Analytics-Agent für Windows mithilfe der [VM-Erweiterung für Windows](../virtual-machines/extensions/oms-windows.md). Die Erweiterung installiert den Log Analytics-Agent auf virtuellen Azure-Computern und registriert virtuelle Computer in einem vorhandenen Log Analytics-Arbeitsbereich. Sie können eine Azure Resource Manager-Vorlage, PowerShell oder Azure Policy verwenden, um die integrierte Richtlinie [Log Analytics-Agent für *Linux*- oder *Windows*-VMs bereitstellen](../governance/policy/samples/built-in-policies.md#monitoring) zuzuweisen. Nachdem der Agent installiert wurde, kann der Computer einer Hybrid Runbook Worker-Gruppe in Ihrem Automation-Konto hinzugefügt werden.
    
    * Für Nicht-Azure-Computer können Sie den Log Analytics-Agent mit [Servern mit Azure Arc-Aktivierung](../azure-arc/servers/overview.md) installieren. Server mit Arc-Unterstützung erlauben die Bereitstellung des Log Analytics-Agents mithilfe der folgenden Methoden:
    
        - Verwenden des VM-Erweiterungen-Frameworks.
        
            Mit dieser Funktion in Servern mit Azure Arc-Unterstützung können Sie die VM-Erweiterung für den Log Analytics-Agent auf einem Nicht-Azure-Windows- und/oder Linux-Server bereitstellen. VM-Erweiterungen können mit den folgenden Methoden auf Ihren hybriden Computern oder Servern verwaltet werden, die von Azure Arc-fähigen Servern verwaltet werden:
        
            - Das [Azure-Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - Die [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - [Azure Resource Manager-Vorlagen](../azure-arc/servers/manage-vm-extensions-template.md)
        
        - Verwenden von Azure Policy.
        
            Bei dieser Vorgehensweise verwenden Sie die integrierte Azure Policy-Richtlinie zum [Bereitstellen des Log Analytics Agent auf Linux- oder Windows-Azure Arc-Computern](../governance/policy/samples/built-in-policies.md#monitoring), um zu überwachen, ob der Log Analytics-Agent auf dem Server mit Arc-Unterstützung installiert ist. Wenn der Agent nicht installiert ist, wird er automatisch über einen Wartungstask bereitgestellt. Wenn Sie alternativ die Überwachung der Computer mit Azure Monitor für VMs planen, verwenden Sie stattdessen die Methode [Azure Monitor für VMs aktivieren](../governance/policy/samples/built-in-initiatives.md#monitoring), um den Log Analytics-Agent zu installieren und zu konfigurieren.

    Es wird empfohlen, den Log Analytics-Agent für Windows oder Linux mit Azure Policy zu installieren.

3. Überprüfen, ob der Agent an den Arbeitsbereich meldet

    Der Log Analytics-Agent für Windows verbindet Computer mit einem Azure Monitor Log Analytics-Arbeitsbereich. Wenn Sie den Agent auf Ihrem Computer installieren und mit Ihrem Arbeitsbereich verbinden, werden automatisch die erforderlichen Komponenten für Hybrid Runbook Worker heruntergeladen.

    Nachdem der Agent nach einigen Minuten eine Verbindung mit dem Log Analytics-Arbeitsbereich hergestellt hat, können Sie über die folgende Abfrage überprüfen, ob er Heartbeatdaten an den Arbeitsbereich sendet.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    In den Suchergebnissen sollten Heartbeat-Datensätze für den Computer angezeigt werden, die angeben, dass dieser verbunden ist und Berichte an den Dienst übermittelt. Standardmäßig leitet jeder Agent einen Heartbeat-Datensatz an seinen zugewiesenen Arbeitsbereich weiter. Führen Sie die folgenden Schritte aus, um Installation und Einrichtung des Agents abzuschließen.

4. Überprüfen Sie die Version des Hybrid Runbook Workers auf dem Computer, auf dem der Log Analytics-Agent gehostet wird, und wechseln Sie zu `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\`, und sehen Sie im Unterordner **version** nach. Dieser Ordner wird auf dem Computer einige Minuten nach der Aktivierung der Lösung im Arbeitsbereich angezeigt.

5. Installieren der Runbookumgebung und Verbindungsherstellung mit Azure Automation. Wenn Sie einen Agent für die Berichterstattung an einen Log Analytics-Arbeitsbereich konfigurieren und die **Automation**-Lösung importieren, pusht die Lösung das PowerShell-Modul `HybridRegistration` herunter. Dieses Modul enthält das Cmdlet `Add-HybridRunbookWorker`. Verwenden Sie dieses Cmdlet zum Installieren der Runbookumgebung auf dem Computer und zu deren Registrierung bei Azure Automation.

    Öffnen Sie eine PowerShell-Sitzung im Administratormodus, und führen Sie die folgenden Befehle zum Importieren des Moduls aus.

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

6. Führen Sie das Cmdlet `Add-HybridRunbookWorker` aus, und geben Sie dabei die Werte für die Parameter `Url`, `Key` und `GroupName` an.

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    Sie finden die für die Parameter `Url` und `Key` erforderlichen Informationen in Ihrem Automation-Konto auf der Seite **Schlüssel**. Wählen Sie im linken Bereich der Seite im Abschnitt **Kontoeinstellungen** die Option **Schlüssel** aus.

    ![Seite „Schlüssel verwalten“](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Kopieren Sie für den Parameter `Url` den Wert für **URL**.

    * Kopieren Sie für den Parameter `Key` den Wert für **PRIMÄRER ZUGRIFFSSCHLÜSSEL**.

    * Verwenden Sie für den Parameter `GroupName` den Namen der Hybrid Runbook Worker-Gruppe. Wenn diese Gruppe bereits im Automation-Konto vorhanden ist, wird ihr der aktuelle Computer hinzugefügt. Wenn diese Gruppe nicht vorhanden ist, wird sie hinzugefügt.

    * Legen Sie bei Bedarf den Parameter `Verbose`fest, um Details zur Installation zu erhalten.

7. Überprüfen Sie die Bereitstellung, nachdem der Befehl abgeschlossen wurde. Auf der Seite **Hybrid-Runbook-Workergruppen** in Ihrem Automation-Konto werden unter der Registerkarte **Benutzer-Hybrid Runbook Worker-Gruppe** die neue oder vorhandene Gruppe sowie die Anzahl der Mitglieder angezeigt. Wenn die Gruppe bereits vorhanden ist, wird die Anzahl der Mitglieder erhöht. Sie können die Gruppe in der Liste auf der Seite auswählen. Wählen Sie im Menü auf der linken Seite **Hybrid Worker** aus. Auf der Seite **Hybrid Worker** werden die einzelnen Mitglieder der Gruppe aufgelistet.

## <a name="install-powershell-modules"></a>Installieren von PowerShell-Modulen

Runbooks können beliebige Aktivitäten und Cmdlets der Module verwenden, die Sie in Ihrer Azure Automation-Umgebung installiert haben. Da diese Module nicht automatisch auf den lokalen Computern bereitgestellt werden, müssen Sie sie manuell installieren. Die Ausnahme ist das Azure-Modul. Dieses Modul wird standardmäßig installiert und bietet Zugriff auf Cmdlets für alle Azure-Dienste und -Aktivitäten für Azure Automation.

Da der primäre Zweck des Hybrid Runbook Workers in der Verwaltung lokaler Ressourcen besteht, müssen Sie sehr wahrscheinlich die Module zur Unterstützung dieser Ressourcen installieren, insbesondere das Modul `PowerShellGet`. Informationen zum Installieren von Windows PowerShell-Modulen finden Sie unter [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

Installierte Module müssen sich an einem Speicherort befinden, auf den von der Umgebungsvariablen `PSModulePath` verwiesen wird, damit sie vom Hybrid Worker automatisch importiert werden. Weitere Informationen finden Sie unter [Installieren von Modulen in PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module).

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>Entfernen des Hybrid Runbook Workers

1. Navigieren Sie im Azure-Portal zu Ihrem Automation-Konto.

2. Wählen Sie unter **Kontoeinstellungen** die Option **Schlüssel** aus, und notieren Sie sich die Werte für **URL** und **Primärer Zugriffsschlüssel**.

3. Öffnen Sie eine PowerShell-Sitzung im Administratormodus, und führen Sie den folgenden Befehl aus. Verwenden Sie dabei Ihre Werte für URL und primären Zugriffsschlüssel. Verwenden Sie den Parameter `Verbose`, um ein ausführliches Protokoll zum Entfernungsvorgang zu erhalten. Verwenden Sie zum Entfernen veralteter Computer aus Ihrer Hybrid Worker-Gruppe den optionalen Parameter `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Entfernen einer Hybrid Worker-Gruppe

Um eine Hybrid Runbook Worker-Gruppe zu entfernen, müssen Sie zunächst den Hybrid Runbook Worker von allen Computern entfernen, die Mitglied der Gruppe sind. Führen Sie dann die folgenden Schritte aus, um die Gruppe zu entfernen:

1. Wählen Sie im Azure-Portal das Automation-Konto aus.

2. Wählen Sie unter **Prozessautomatisierung** die Option **Hybrid Worker-Gruppen** aus. Wählen Sie die Gruppe aus, die Sie löschen möchten. Die Seite „Eigenschaften“ für diese Gruppe wird angezeigt.

   ![Eigenschaftenseite](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Wählen Sie auf der Seite „Eigenschaften“ der ausgewählten Gruppe die Option **Löschen** aus. Eine Meldung fordert Sie zur Bestätigung dieser Aktion auf. Klicken Sie auf **Ja**, wenn Sie den Vorgang wirklich fortsetzen möchten.

   ![Bestätigungsmeldung](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Der Abschluss dieses Vorgangs kann mehrere Sekunden in Anspruch nehmen. Sie können den Fortschritt im Menü unter **Benachrichtigungen** nachverfolgen.

## <a name="next-steps"></a>Nächste Schritte

* Um zu erfahren, wie Sie Ihre Runbooks für die Automatisierung von Prozessen in Ihrem lokalen Rechenzentrum oder in einer anderen Cloudumgebung konfigurieren, lesen Sie sich [Ausführen von Runbooks auf einem Hybrid Runbook Worker](automation-hrw-run-runbooks.md) durch.

* Informationen zum Behandeln von Problemen mit Ihren Hybrid Runbook Workern finden Sie unter [Problembehandlung von Hybrid Runbook Workern](troubleshoot/hybrid-runbook-worker.md#general).