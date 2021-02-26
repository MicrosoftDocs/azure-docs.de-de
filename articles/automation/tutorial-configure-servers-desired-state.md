---
title: Konfigurieren von Computern mit einem gewünschten Zustand in Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie Computer in einem gewünschten Zustand mit Azure Automation State Configuration konfigurieren.
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: f16db3f55ebd0f09e4d7b75750fa319daf03977e
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053566"
---
# <a name="configure-machines-to-a-desired-state"></a>Konfigurieren von Computern mit einem gewünschten Status

Mit der Azure Automation-Zustandskonfiguration können Sie Konfigurationen für Ihre Server festlegen und sicherstellen, dass sich diese Server im Verlauf der Zeit im angegebenen Zustand befinden.

> [!div class="checklist"]
> - Integrieren eines virtuellen Computers in die Azure Automation DSC-Verwaltung
> - Hochladen einer Konfiguration in Azure Automation
> - Kompilieren einer Konfiguration in eine Knotenkonfiguration
> - Zuweisen einer Knotenkonfiguration zu einem verwalteten Knoten
> - Überprüfen des Konformitätsstatus eines verwalteten Knotens

Für dieses Tutorial verwenden wir eine einfache [DSC-Konfiguration](/powershell/scripting/dsc/configurations/configurations), die sicherstellt, dass IIS auf der VM installiert ist.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure Automation-Konto. Weitere Informationen zu Automation-Konten und den zugehörigen Anforderungen finden Sie unter [Übersicht über die Automation-Kontoauthentifizierung](./automation-security-overview.md).
- Eine Azure Resource Manager-VM (nicht klassisch) unter Windows Server 2008 R2 oder höher. Eine Anleitung zum Erstellen einer VM finden Sie unter [Erstellen Ihres ersten virtuellen Windows-Computers im Azure-Portal](../virtual-machines/windows/quick-create-portal.md).
- Azure PowerShell-Modul, Version 3.6 oder höher. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/azurerm/install-azurerm-ps) Informationen dazu.
- Erfahrung mit DSC (Desired State Configuration, Konfiguration des gewünschten Zustands). Weitere Informationen zu DSC finden Sie unter [Windows PowerShell DSC – Übersicht](/powershell/scripting/dsc/overview/overview).

## <a name="support-for-partial-configurations"></a>Unterstützung für Teilkonfigurationen

Azure Automation State Configuration unterstützt die Verwendung von [Teilkonfigurationen](/powershell/scripting/dsc/pull-server/partialconfigs). In diesem Szenario ist DSC so konfiguriert, dass mehrere Konfigurationen unabhängig voneinander verwaltet werden können, wobei jede Konfiguration von Azure Automation abgerufen wird. Einem Knoten kann aber nur eine Konfiguration mittels Automation-Konto zugewiesen werden. Dies bedeutet, dass Sie bei Verwendung von zwei Konfigurationen für einen Knoten zwei Automation-Kontos benötigen.

Ausführliche Informationen zum Registrieren einer Teilkonfiguration aus einem Pulldienst finden Sie in der Dokumentation zu [Teilkonfigurationen](/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Weitere Informationen dazu, wie Teams zusammenarbeiten können, um Server gemeinsam mithilfe von Konfiguration als Code zu verwalten, finden Sie unter [Grundlegendes zu DSCs Rolle in einer CI/CD-Pipeline](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich mit dem Cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Erstellen und Hochladen einer Konfiguration in Azure Automation

Geben Sie in einem Texteditor Folgendes ein, und speichern Sie die Datei lokal als **TestConfig.ps1**.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> In komplexeren Szenarien, in denen mehrere Module importiert werden müssen, die DSC-Ressourcen bereitstellen, müssen Sie sicherstellen, dass jedes Modul über eine eindeutige Zeile vom Typ `Import-DscResource` in Ihrer Konfiguration verfügt.

Rufen Sie das Cmdlet [Import-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration) auf, um die Konfiguration in Ihr Automation-Konto hochzuladen.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilieren einer Konfiguration in eine Knotenkonfiguration

Eine DSC-Konfiguration muss in eine Knotenkonfiguration kompiliert werden, bevor sie einem Knoten zugewiesen werden kann. Weitere Informationen finden Sie unter [DSC-Konfigurationen](/powershell/scripting/dsc/configurations/configurations).

Rufen Sie das Cmdlet [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) auf, um die `TestConfig`-Konfiguration in eine Knotenkonfiguration namens `TestConfig.WebServer` in Ihrem Automation-Konto zu kompilieren.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrieren eines virtuellen Computers für die Verwaltung durch die Zustandskonfiguration

Mit Azure Automation State Configuration können Sie virtuelle Azure-Computer (mit dem klassischen Modell oder dem Resource Manager-Modell), lokale virtuelle Computer, Linux-Computer, virtuelle AWS-Computer und lokale physische Computer verwalten. In diesem Thema wird das Registrieren von ausschließlich Azure Resource Manager-VMs behandelt. Informationen zum Registrieren anderer Computertypen finden Sie unter [Integrieren von Computern für die Verwaltung durch die Azure Automation-Zustandskonfiguration](automation-dsc-onboarding.md).

Rufen Sie das Cmdlet [Register-AzAutomationDscNode](/powershell/module/Az.Automation/Register-AzAutomationDscNode) auf, um Ihren virtuellen Computer bei der Azure Automation State Configuration als verwalteten Knoten zu registrieren. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Angeben der Konfigurationsmoduseinstellungen

Verwenden Sie das Cmdlet [Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode), um einen virtuellen Computer als verwalteten Knoten zu registrieren und Konfigurationseigenschaften anzugeben. Sie können z. B. festlegen, dass der Zustand des Computers nur einmalig angewendet werden soll, indem Sie `ApplyOnly` als Wert der `ConfigurationMode`-Eigenschaft angeben. State Configuration versucht nach der anfänglichen Überprüfung nicht, die Konfiguration anzuwenden.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Sie können auch angeben, wie oft DSC den Konfigurationszustand überprüft, indem Sie die `ConfigurationModeFrequencyMins`-Eigenschaft verwenden. Weitere Informationen zu DSC-Konfigurationseinstellungen finden Sie unter [Konfigurieren des lokalen Konfigurations-Managers](/powershell/scripting/dsc/managing-nodes/metaConfig).

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Zuweisen einer Knotenkonfiguration zu einem verwalteten Knoten

Nun können wir die kompilierte Knotenkonfiguration der zu konfigurierenden VM zuordnen.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Damit wird dem registrierten DSC-Knoten `DscVm` die Knotenkonfiguration namens `TestConfig.WebServer` zugeordnet. Standardmäßig wird der DSC-Knoten alle 30 Minuten auf Konformität mit der Knotenkonfiguration geprüft. Informationen zum Ändern des Intervalls für die Konformitätsprüfung finden Sie unter [Konfigurieren des lokalen Konfigurations-Managers](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Überprüfen des Konformitätsstatus eines verwalteten Knotens

Sie können Berichte zum Compliancestatus eines verwalteten Knotens abrufen, indem Sie das Cmdlet [Get-AzAutomationDscNodeReport](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport) verwenden.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Entfernen von Knoten aus dem Dienst

Wenn Sie Azure Automation State Configuration einen Knoten hinzufügen, werden die Einstellungen im lokalen Konfigurations-Manager so festgelegt, dass eine Registrierung beim Dienst erfolgt und Konfigurationen sowie erforderliche Module zum Konfigurieren des Computers abgerufen werden.
Wenn Sie den Knoten aus dem Dienst entfernen möchten, können Sie dazu entweder das Azure-Portal oder die Az-Cmdlets verwenden.

> [!NOTE]
> Durch das Aufheben der Registrierung eines Knotens beim Dienst werden die Einstellungen des lokalen Konfigurations-Managers lediglich so festgelegt, dass der Knoten keine Verbindung mehr mit dem Dienst herstellt.
> Dies hat keine Auswirkung auf die Konfiguration, die derzeit auf den Knoten angewendet wird.
> Zum Entfernen der aktuellen Konfiguration verwenden Sie [PowerShell](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument), oder löschen Sie die lokale Konfigurationsdatei (dies ist für Linux-Knoten die einzige Option).

### <a name="azure-portal"></a>Azure-Portal

Klicken Sie in Azure Automation im Inhaltsverzeichnis auf **Zustandskonfiguration (DSC)** .
Klicken Sie dann auf **Knoten**, um die Liste der Knoten anzuzeigen, die beim Dienst registriert sind.
Klicken Sie auf den Namen des Knotens, den Sie entfernen möchten.
Klicken Sie in der daraufhin geöffneten Knotenansicht auf **Registrierung aufheben**.

### <a name="powershell"></a>PowerShell

Zum Aufheben der Registrierung eines Knotens beim Azure Automation State Configuration-Dienst mithilfe von PowerShell befolgen Sie die Anweisungen in der Dokumentation für das Cmdlet [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode).

## <a name="next-steps"></a>Nächste Schritte

- Eine Einführung finden Sie unter [Erste Schritte mit Azure Automation State Configuration](automation-dsc-getting-started.md).
- Informationen zum Aktivieren von Knoten finden Sie unter [Aktivieren von Azure Automation State Configuration](automation-dsc-onboarding.md).
- Wie Sie DSC-Konfigurationen kompilieren und sie anschließend Zielknoten zuweisen, erfahren Sie unter [Kompilieren von DSC-Konfigurationen in Azure Automation State Configuration](automation-dsc-compile.md).
- Ein Anwendungsbeispiel für Azure Automation State Configuration in einer Continuous Deployment-Pipeline finden Sie unter [Einrichten von Continuous Deployment mit Chocolatey](automation-dsc-cd-chocolatey.md).
- Eine Preisübersicht finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).
- Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation).
