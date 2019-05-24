---
title: Variable Objekte in Azure Automation
description: Variablenobjekte sind Werte, die allen Runbooks und DSC-Konfigurationen in Azure Automation zur Verfügung stehen.  Dieser Artikel stellt eine ausführliche Beschreibung von Variablen bereit und zeigt, wie diese in Textrunbooks und grafischen Runbooks eingesetzt werden.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0ac34f1d1e7fc2a967c7608f31f3b943f9380d01
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786195"
---
# <a name="variable-assets-in-azure-automation"></a>Variable Objekte in Azure Automation

Variablenobjekte sind Werte, die allen Runbooks und DSC-Konfigurationen in Ihrem Automation-Konto zur Verfügung stehen. Sie können über das Azure-Portal, PowerShell, ein Runbook oder eine DSC-Konfiguration verwaltet werden. Automatisierungsvariablen sind in folgenden Szenarien hilfreich:

- Gemeinsame Nutzung eines Werts durch mehrere Runbooks oder DSC-Konfigurationen

- Gemeinsame Nutzung eines Werts durch mehrere Aufträge des gleichen Runbooks oder der gleichen DSC-Konfiguration

- Verwaltung eines Werts über das Portal oder über die von Runbooks oder DSC-Konfigurationen verwendete PowerShell-Befehlszeile, z.B. ein Satz von allgemeinen Konfigurationselementen wie eine bestimmte Liste von VM-Namen, eine bestimmte Ressourcengruppe oder ein AD-Domänenname.  

Da Automation-Variablen persistent gespeichert werden, bleiben sie auch dann verfügbar, wenn die Ausführung eines Runbooks oder einer DSC-Konfiguration misslingt. Durch dieses Verhalten kann ein Wert von einem Runbook festgelegt und anschließend bei der nächsten Ausführung von einem anderen oder gleichen Runbook bzw. von einer anderen oder der gleichen DSC-Konfiguration verwendet werden.

Beim Erstellen einer Variablen können Sie festlegen, dass diese verschlüsselt gespeichert wird. Verschlüsselte Variablen werden sicher in Azure Automation gespeichert. Ihr Wert kann vom Cmdlet [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable), das zum Funktionsumfang des Azure PowerShell-Moduls gehört, nicht abgerufen werden. Ein verschlüsselter Wert kann ausschließlich über die Aktivität **Get-AutomationVariable** in einem Runbook oder einer DSC-Konfiguration abgerufen werden.

>[!NOTE]
>Zu den sicheren Objekten in Azure Automation gehören Anmeldeinformationen, Zertifikate, Verbindungen und verschlüsselte Variablen. Diese Objekte werden mithilfe eines eindeutigen Schlüssels verschlüsselt und in Azure Automation gespeichert, der für jedes Automation-Konto generiert wird. Dieser Schlüssel wird in einem systemseitig verwalteten Schlüsseltresor (Key Vault) gespeichert. Vor dem Speichern eines sicheren Objekts wird der Schlüssel aus Key Vault geladen und dann zum Verschlüsseln des Objekts verwendet. Dieser Prozess wird von Azure Automation verwaltet.

## <a name="variable-types"></a>Variablentypen

Beim Erstellen einer Variablen über das Azure-Portal müssen Sie einen Datentyp aus der Dropdownliste angeben, damit das entsprechende Steuerelement zur Eingabe des Variablenwerts im Portal angezeigt werden kann. Die Variable ist nicht auf diesen Datentyp beschränkt. Sie müssen die Variable in Windows PowerShell festlegen, wenn Sie einen anderen Wertetyp angeben möchten. Wenn Sie **Nicht definiert** angeben, wird der Wert der Variablen auf **$null** festgelegt, sodass Sie den Wert mithilfe des Cmdlets [Set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) oder der Aktivität **Set-AutomationVariable** festlegen müssen. Sie können den Wert eines komplexen Variablentyps nicht im Portal festlegen, jedoch einen Wert beliebigen Typs über Windows PowerShell bereitstellen. Komplexe Typen werden als [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) zurückgegeben.

Sie können mehrere Werte in einer einzigen Variable speichern, indem Sie ein Array oder eine Hashtabelle erstellen und in der Variable speichern.

Im Folgenden finden Sie eine Liste von in Automation verfügbaren Variablentypen:

* Zeichenfolge
* Integer
* Datetime
* Boolean
* Null

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-Cmdlets

Die Cmdlets in der folgenden Tabelle werden für AzureRM zum Erstellen und Verwalten von Automation-Anmeldeinformationsobjekten mit Windows PowerShell verwendet. Sie gehören zum Funktionsumfang des [AzureRM.Automation-Moduls](/powershell/azure/overview), das zur Verwendung in Automation-Runbooks und DSC-Konfigurationen verfügbar ist.

| Cmdlets | BESCHREIBUNG |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Ruft den Wert einer vorhandenen Variable ab.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Erstellt eine neue Variable und legt ihren Wert fest.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Entfernt eine vorhandene Variable.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Legt den Wert für eine vorhandene Variable fest.|

## <a name="activities"></a>Aktivitäten

Die Aktivitäten in der folgenden Tabelle werden für den Zugriff auf Anmeldeinformationen in einem Runbook und DSC-Konfigurationen verwendet.

| Aktivitäten | BESCHREIBUNG |
|:---|:---|
|Get-AutomationVariable|Ruft den Wert einer vorhandenen Variable ab.|
|Set-AutomationVariable|Legt den Wert für eine vorhandene Variable fest.|

> [!NOTE]
> Vermeiden Sie die Verwendung von Variablen im Parameter „–Name“ von **Get-AutomationVariable** in einem Runbook oder einer DSC-Konfiguration, da dies die Ermittlung von Abhängigkeiten zwischen Runbooks bzw. DSC-Konfigurationen und Automation-Variablen zur Entwurfszeit erschweren kann.

Die Funktionen in der folgenden Tabelle werden zum Zugreifen auf und Abrufen von Variablen in einem Python2-Runbook verwendet.

|Python2-Funktionen|BESCHREIBUNG|
|:---|:---|
|automationassets.get_automation_variable|Ruft den Wert einer vorhandenen Variable ab. |
|automationassets.set_automation_variable|Legt den Wert für eine vorhandene Variable fest. |

> [!NOTE]
> Sie müssen das Modul „automationassets“ oben im Python-Runbook importieren, um auf die Assetfunktionen zugreifen zu können.

## <a name="creating-a-new-automation-variable"></a>Erstellen einer neuen Automation-Variablen

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>So erstellen Sie eine neue Variable über das Azure-Portal

1. Klicken Sie in Ihrem Automation-Konto auf die Kachel **Objekte** und dann das Blatt **Objekte**, und wählen Sie **Variablen** aus.
2. Klicken Sie auf dem Blatt **Variablen** auf **Variable hinzufügen**.
3. Gehen Sie die Optionen auf dem Blatt **Neue Variable** durch, und klicken Sie auf **Erstellen**, um die neue Variable zu speichern.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>So erstellen Sie eine neue Variable mit Windows PowerShell

Das Cmdlet [New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) erstellt eine neue Variable und legt ihren anfänglichen Wert fest. Sie können den Wert mithilfe von [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)abrufen. Wenn es sich um einen einfachen Wert handelt, wird der gleiche Typ zurückgegeben. Wenn es sich um einen komplexen Wert handelt, wird ein **PSCustomObject** zurückgegeben.

Die folgenden Beispielbefehle zeigen, wie eine Variable vom Typ "string" erstellt und anschließend der Wert dieser Variablen zurückgegeben wird.

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Die folgenden Beispielbefehle zeigen, wie eine Variable eines komplexen Typs erstellt wird und anschließend die Eigenschaften dieser Variablen zurückgegeben werden. In diesem Fall wird ein Objekt für einen virtuellen Computer von **Get-AzureRmVm** verwendet.

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Verwenden einer Variablen in einem Runbook oder einer DSC-Konfiguration

Verwenden Sie die Aktivität **Set-AutomationVariable**, um den Wert einer Automation-Variablen in einem PowerShell-Runbook oder einer DSC-Konfiguration festzulegen, und die Aktivität **Get-AutomationVariable**, um den Wert abzurufen. Die Verwendung der Cmdlets **Set-AzureRMAutomationVariable** oder **Get-AzureRMAutomationVariable** in einem Runbook oder einer DSC-Konfiguration empfiehlt sich nicht, da sie weniger effizient als die Workflowaktivitäten sind. Außerdem können Sie den Wert sicherer Variablen mit **Get-AzureRMAutomationVariable** nicht abrufen. Die einzige Möglichkeit, eine neue Variable in einem Runbook oder einer DSC-Konfiguration zu erstellen, ist die Verwendung des Cmdlets [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable).

### <a name="textual-runbook-samples"></a>Beispiele für Textrunbooks

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Festlegen und Abrufen eins einfachen Werts aus einer Variablen

Die folgenden Beispielbefehle zeigen, wie Sie eine Variable in einem Textrunbook festlegen und abrufen. In diesem Beispiel wird angenommen, dass die Variablen des Typs „integer“ *NumberOfIterations* und *NumberOfRunnings* sowie die Variable des Typs „string“ *SampleMessage* erstellt wurden.

```powershell
$NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Festlegen und Abrufen einer Variablen in Python2

Der folgende Beispielcode veranschaulicht, wie Sie eine Variable verwenden, eine Variable festlegen und eine Ausnahme für eine nicht vorhandene Variable in einem Python2-Runbook behandeln.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("non-existing variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Beispiel für grafische Runbooks

In einem grafischen Runbook fügen Sie **Get-AutomationVariable** oder **Set-AutomationVariable** hinzu, indem Sie im Bereich „Bibliothek“ des grafischen Editors mit der rechten Maustaste auf die Variable klicken und die gewünschte Aktivität auswählen.

![Hinzufügen einer Variablen zum Zeichenbereich](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Festlegen von Werten in einer Variablen

Die folgende Abbildung zeigt Beispielaktivitäten zum Aktualisieren einer Variablen mit einem einfachen Wert in einem grafischen Runbook. In diesem Beispiel wird mit **Get-AzureRmVM** ein einzelner virtueller Azure-Computer mit abgerufen, und der Name des Computers wird in einer vorhandenen Automation-Variablen des Typs „string“ gespeichert. Es spielt keine Rolle, ob es sich bei der [Verknüpfung um eine Pipeline oder eine Sequenz](../automation-graphical-authoring-intro.md#links-and-workflow) handelt, da in der Ausgabe nur ein einziges Objekt zu erwarten ist.

![Festlegen einer einfachen Variablen](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Verknüpfung von Aktivitäten bei der grafischen Inhaltserstellung finden Sie unter [Links bei der grafischen Erstellung](../automation-graphical-authoring-intro.md#links-and-workflow)
- Informationen über die ersten Schritte mit grafischen Runbooks finden Sie unter [Mein erstes grafisches Runbook](../automation-first-runbook-graphical.md)
