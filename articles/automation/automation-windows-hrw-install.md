---
title: Azure Automation Hybrid Runbook Worker (Windows)
description: Dieser Artikel enthält Informationen zum Installieren eines Azure Automation-Hybrid Runbook Workers, mit dem Sie Runbooks auf Windows-basierten Computern in Ihrem lokalen Datencenter oder einer Cloudumgebung ausführen können.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/21/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cc3307a4f32d77b9b8d259ac846c4db1c1ae4a99
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002508"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Bereitstellen eines Windows Hybrid Runbook Workers

Sie können mit dem Azure Automation-Feature „Hybrid Runbook Worker“ Runbooks direkt auf dem Computer ausführen, der die Rolle hostet, und außerhalb für Ressourcen in der Umgebung zur Verwaltung dieser lokalen Ressourcen. Runbooks werden in Azure Automation gespeichert und verwaltet und an einen oder mehrere dafür vorgesehene Computer übermittelt. Dieser Artikel beschreibt, wie Sie den Hybrid Runbook Worker auf einem Windows-Computer installieren.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Installieren der Windows Hybrid Runbook Worker

Zum Installieren und Konfigurieren eines Windows Hybrid Runbook Workers können Sie zwei verschiedene Methoden verwenden. Die empfohlene Methode besteht darin, ein Automation-Runbook zu verwenden, um den Prozess zum Konfigurieren eines Windows-Computers vollständig zu automatisieren. Bei der zweiten Methode wird die Rolle Schritt für Schritt manuell installiert und konfiguriert.

> [!NOTE]
> Um die Konfiguration der Server zu verwalten, die die Hybrid Runbook Worker-Rolle mit DSC (Desired State Configuration) unterstützen, müssen Sie die Server als DSC-Knoten hinzufügen.

Die folgenden Mindestanforderungen gelten für einen Windows Hybrid Runbook Worker:

* Windows Server 2012 oder höher
* Windows PowerShell 5.1 oder höher ([WMF 5.1 herunterladen](https://www.microsoft.com/download/details.aspx?id=54616)).
* .NET Framework 4.6.2 oder höher
* Zwei Kerne.
* 4 GB RAM.
* Port 443 (ausgehend).

Weitere Netzwerkanforderungen für den Hybrid Runbook Worker finden Sie unter [Konfigurieren des Netzwerks](automation-hybrid-runbook-worker.md#network-planning).

Weitere Informationen zur Integration der Server für die Verwaltung durch DSC finden Sie unter [Integrieren von Computern für die Verwaltung durch Azure Automation DSC](automation-dsc-onboarding.md).
Wenn Sie die [Lösung für die Updateverwaltung](../operations-management-suite/oms-solution-update-management.md) aktivieren, werden alle mit dem Azure Log Analytics-Arbeitsbereich verbundenen Windows-Computer automatisch als Hybrid Runbook Worker konfiguriert, um Runbooks zu unterstützen, die in dieser Lösung enthalten sind. Es wird aber keine Registrierung für Hybrid Worker-Gruppen durchgeführt, die in Ihrem Automation-Konto bereits definiert wurden. 

Der Computer kann einer Hybrid Runbook Worker-Gruppe in Ihrem Automation-Konto hinzugefügt werden, um Automation-Runbooks zu unterstützen, solange Sie sowohl für die Lösung als auch die Mitgliedschaft in der Hybrid Runbook Worker-Gruppe dasselbe Konto verwenden. Diese Funktionalität wurde Version 7.2.12024.0 des Hybrid Runbook Worker hinzugefügt.

Lesen Sie nach dem erfolgreichen Bereitstellen eines Runbook Workers [Running runbooks on a Hybrid Runbook Worker (Ausführen von Runbooks auf einem Hybrid Runbook Worker)](automation-hrw-run-runbooks.md), um zu erfahren, wie Sie Ihre Runbooks für die Automatisierung von Prozessen in Ihrem lokalen Datencenter oder in einer anderen Cloudumgebung konfigurieren.

### <a name="automated-deployment"></a>Automatisierte Bereitstellung

Führen Sie die folgenden Schritte aus, um die Installation und Konfiguration der Windows Hybrid Worker-Rolle zu automatisieren:

1. Laden Sie das Skript „New-OnPremiseHybridWorker.ps1“ aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) direkt über den Computer herunter, auf dem die Hybrid Runbook Worker-Rolle ausgeführt wird, oder laden Sie es über einen anderen Computer in Ihrer Umgebung herunter. Kopieren Sie das Skript auf den Worker.

   Das Skript „New-OnPremiseHybridWorker.ps1“ erfordert während der Ausführung die folgenden Parameter:

   * *AutomationAccountName* (erforderlich): Der Name Ihres Automation-Kontos.
   * *AAResourceGroupName* (erforderlich): Der Name der Ressourcengruppe, die Ihrem Automation-Konto zugeordnet ist.
   * *OMSResourceGroupName* (optional): Der Name der Ressourcengruppe für den Log Analytics-Arbeitsbereich. Wenn diese Ressourcengruppe nicht angegeben wird, wird *AAResourceGroupName* verwendet.
   * *HybridGroupName* (erforderlich): Der Name einer Hybrid Runbook Worker-Gruppe, die Sie als Ziel für die Runbooks angeben, die dieses Szenario unterstützen.
   * *SubscriptionID* (erforderlich): Die Azure-Abonnement-ID Ihres Automation-Kontos.
   * *WorkspaceName* (optional): Name des Log Analytics-Arbeitsbereichs. Falls kein Log Analytics-Arbeitsbereich vorhanden ist, wird dieser durch das Skript erstellt und konfiguriert.

   > [!NOTE]
   > Wenn Sie Lösungen aktivieren, werden nur bestimmte Regionen zum Verknüpfen mit einem Log Analytics-Arbeitsbereich und einem Automation-Konto unterstützt.
   >
   > Eine Liste der unterstützten Zuordnungspaare finden Sie unter [Regionszuordnung für Automation-Konto und Log Analytics-Arbeitsbereich](how-to/region-mappings.md).

2. Öffnen Sie auf Ihrem Computer **Windows PowerShell** über den **Startbildschirm** im Administratormodus.
3. Navigieren Sie in der PowerShell-Befehlszeilenshell zu dem Ordner, der das Skript enthält, das Sie heruntergeladen haben. Ändern Sie die Werte für die Parameter *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* und *-WorkspaceName*. Führen Sie das Skript dann aus.

     > [!NOTE]
     > Nach dem Ausführen des Skripts werden Sie aufgefordert, sich bei Azure zu authentifizieren. Sie *müssen* sich mit einem Konto anmelden, das Mitglied der Rolle „Abonnement-Administratoren“ und Co-Administrator des Abonnements ist.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Sie werden aufgefordert, der Installation von NuGet zuzustimmen und sich mit Ihren Azure-Anmeldeinformationen zu authentifizieren.

5. Nachdem das Skript abgeschlossen wurde, werden auf der Seite **Hybrid Worker-Gruppen** die neue Gruppe und die Anzahl der Mitglieder angezeigt. Wenn die Gruppe bereits vorhanden ist, wird die Anzahl der Mitglieder erhöht. Sie können die Gruppe aus der Liste auf der Seite **Hybrid Worker-Gruppen** auswählen und dann auf die Kachel **Hybrid Worker** klicken. Auf der Seite **Hybrid Worker** werden die einzelnen Mitglieder der Gruppe aufgelistet.

### <a name="manual-deployment"></a>Manuelle Bereitstellung

Führen Sie die ersten beiden Schritte ein Mal für Ihre Automation-Umgebung aus, und wiederholen Sie dann die verbleibenden Schritte für jeden Workercomputer.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

#### <a name="1-create-a-log-analytics-workspace"></a>1. Erstellen eines Log Analytics-Arbeitsbereichs

Sofern Sie noch nicht über einen Log Analytics-Arbeitsbereich verfügen, erstellen Sie diesen mithilfe der Anweisungen unter [Verwalten von Arbeitsbereichen](../azure-monitor/platform/manage-access.md). Wenn Sie bereits über einen Arbeitsbereich verfügen, können Sie diesen verwenden.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Hinzufügen der Automation-Lösung zum Log Analytics-Arbeitsbereich

Die Automation Azure Monitor-Protokolllösung fügt Azure Automation Funktionalität hinzu, einschließlich Unterstützung für Hybrid Runbook Worker. Wenn Sie die Lösung Ihrem Arbeitsbereich hinzufügen, werden Workerkomponenten automatisch auf den Agent-Computer gepusht, den Sie im nächsten Schritt installieren.

Um die **Automation** Azure Monitor-Protokolllösung Ihrem Arbeitsbereich hinzuzufügen, führen Sie den folgenden PowerShell-Befehl aus.

```powershell-interactive
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Installieren von Microsoft Monitoring Agent

Microsoft Monitoring Agent stellt die Verbindung von Computern mit Azure Monitor-Protokolle her. Wenn Sie den Agent auf dem lokalen Computer installieren und mit Ihrem Arbeitsbereich verbinden, werden automatisch die erforderlichen Komponenten für Hybrid Runbook Worker heruntergeladen.

Befolgen Sie die Anweisungen unter [Verbinden von Windows-Computern mit Azure Monitor-Protokolle](../log-analytics/log-analytics-windows-agent.md), um den Agent auf dem lokalen Computer zu installieren. Sie können diesen Vorgang für mehrere Computer wiederholen, um Ihrer Umgebung mehrere Worker hinzuzufügen.

Wenn der Agent erfolgreich eine Verbindung mit Azure Monitor-Protokolle hergestellt hat, wird er auf der Log Analytics-Seite **Einstellungen** auf der Registerkarte **Verbundene Datenquellen** aufgeführt. Sie können sicherstellen, dass der Agent die Automation-Lösung ordnungsgemäß heruntergeladen hat, indem Sie prüfen, ob unter „C:\Programme\Microsoft Monitoring Agent\Agent“ ein Ordner namens **AzureAutomationFiles** vorhanden ist. Unter „C:\Programme\Microsoft Monitoring Agent\Agent\AzureAutomation\“ im Unterordner \\*version* können Sie die Hybrid Runbook Worker-Version überprüfen.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installieren der Runbookumgebung und Verbindungsherstellung mit Azure Automation

Wenn Sie Azure Monitor-Protokolle einen Agent hinzufügen, lädt die Automation-Lösung per Push das PowerShell-Modul **HybridRegistration** herunter, in dem das Cmdlet **Add-HybridRunbookWorker** enthalten ist. Sie verwenden dieses Cmdlet zum Installieren der Runbookumgebung auf dem Computer und registrieren diesen bei Azure Automation.

Öffnen Sie eine PowerShell-Sitzung im Administratormodus, und führen Sie die folgenden Befehle zum Importieren des Moduls aus:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Führen Sie dann das Cmdlet **Add-HybridRunbookWorker** mit der folgenden Syntax aus:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Sie finden die für dieses Cmdlet erforderlichen Informationen im Azure-Portal auf der Seite **Schlüssel verwalten**. Öffnen Sie diese Seite, indem Sie in Ihrem Automation-Konto auf der Seite **Einstellungen** die Option **Schlüssel** auswählen.

![Seite „Schlüssel verwalten“](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** ist der Name der Hybrid Runbook Worker-Gruppe. Wenn die Gruppe bereits im Automation-Konto vorhanden ist, wird ihr der aktuelle Computer hinzugefügt. Wenn diese Gruppe nicht vorhanden ist, wird sie hinzugefügt.
* **EndPoint** ist der **URL**-Eintrag auf der Seite **Schlüssel verwalten**.
* **Token** ist der Eintrag **PRIMÄRER ZUGRIFFSSCHLÜSSEL** auf der Seite **Schlüssel verwalten**.

Verwenden Sie den Schalter **-Verbose** mit **Add-HybridRunbookWorker**, um detaillierte Informationen zur Installation zu erhalten.

#### <a name="5-install-powershell-modules"></a>5. Installieren von PowerShell-Modulen

Runbooks können beliebige Aktivitäten und Cmdlets der Module verwenden, die Sie in Ihrer Azure Automation-Umgebung installiert haben. Diese Module werden nicht automatisch auf den lokalen Computern bereitgestellt. Sie müssen sie daher manuell installieren. Eine Ausnahme stellt das standardmäßig installierte Azure-Modul dar. Es bietet Zugriff auf Cmdlets für alle Azure-Dienste und -Aktivitäten für Azure Automation.

Da der primäre Zweck des Hybrid Runbook Worker-Features darin besteht, lokale Ressourcen zu verwalten, müssen Sie sehr wahrscheinlich die Module zur Unterstützung dieser Ressourcen installieren. Unter [Installieren von Modulen](/powershell/developer/windows-powershell) finden Sie Informationen zum Installieren von PowerShell-Modulen. 

Installierte Module müssen sich an einem Speicherort befinden, auf den von der **PSModulePath**-Umgebungsvariablen verwiesen wird, damit sie vom Hybrid Worker automatisch importiert werden. Weitere Informationen finden Sie unter [Modifying the PSModulePath Installation Path](/powershell/developer/windows-powershell) (Ändern des PSModulePath-Installationspfads).

## <a name="next-steps"></a>Nächste Schritte

* Um zu erfahren, wie Sie Ihre Runbooks für die Automatisierung von Prozessen in Ihrem lokalen Rechenzentrum oder in einer anderen Cloudumgebung konfigurieren, lesen Sie sich [Ausführen von Runbooks auf einem Hybrid Runbook Worker](automation-hrw-run-runbooks.md) durch.
* Anweisungen zum Entfernen von Hybrid Runbook Workern finden Sie unter [Entfernen von Azure Automation-Hybrid-Runbook Workern](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Informationen zur Problembehandlung Ihrer Hybrid Runbook Worker finden Sie unter [Problembehandlung von Windows Hybrid Runbook Workers](troubleshoot/hybrid-runbook-worker.md#windows).
* Zusätzliche Schritte zum Beheben von Problemen mit der Updateverwaltung finden Sie unter [Updateverwaltung: Problembehandlung](troubleshoot/update-management.md).
