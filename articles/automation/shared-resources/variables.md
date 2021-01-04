---
title: Verwalten von Variablen in Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie Variablen in Runbooks und DSC-Konfigurationen verwenden.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/01/2020
ms.topic: conceptual
ms.openlocfilehash: 5be0d45843eed8c7c0d7d9b6dc4655de01e914c3
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461451"
---
# <a name="manage-variables-in-azure-automation"></a>Verwalten von Variablen in Azure Automation

Variablenobjekte sind Werte, die allen Runbooks und DSC-Konfigurationen in Ihrem Automation-Konto zur Verfügung stehen. Sie können sie über das Azure-Portal, mit PowerShell, in einem Runbook oder in einer DSC-Konfiguration verwalten.

Automatisierungsvariablen sind in folgenden Szenarien hilfreich:

- Gemeinsame Nutzung eines Werts durch mehrere Runbooks oder DSC-Konfigurationen

- Gemeinsame Nutzung eines Werts durch mehrere Aufträge desselben Runbooks oder derselben DSC-Konfiguration

- Verwalten eines von Runbooks oder DSC-Konfigurationen verwendeten Werts über das Portal oder über die PowerShell-Befehlszeile Ein Beispiel hierfür ist ein Satz von gemeinsamen Konfigurationselementen, z. B. eine bestimmte Liste von VM-Namen, eine bestimmte Ressourcengruppe, ein AD-Domänenname u. v. m.  

Azure Automation speichert Variablen persistent und macht sie auch dann verfügbar, wenn die Ausführung eines Runbooks oder einer DSC-Konfiguration zu einem Fehler führt. Durch dieses Verhalten kann ein Runbook oder eine DSC-Konfiguration einen Wert festlegen, der dann bei der nächsten Ausführung von einem anderen Runbook bzw. vom selben Runbook oder derselben DSC-Konfiguration genutzt werden kann.

Azure Automation speichert jede verschlüsselte Variable sicher. Beim Erstellen einer Variablen können Sie die Verschlüsselung und den Speicher durch Azure Automation als sichere Ressource angeben. Nachdem Sie die Variable erstellt haben, können Sie ihren Verschlüsselungsstatus nicht ändern, ohne die Variable neu erstellen zu müssen. Wenn Sie über Automation-Kontovariablen verfügen, in denen vertrauliche Daten gespeichert werden und die noch nicht verschlüsselt sind, müssen Sie sie löschen und als verschlüsselte Variablen neu erstellen. Eine Azure Security Center-Empfehlung ist, alle Azure Automation-Variablen wie unter [Automation-Kontovariablen sollten verschlüsselt werden](../../security-center/recommendations-reference.md#recs-computeapp) beschrieben zu verschlüsseln. Wenn Sie über unverschlüsselte Variablen verfügen, die aus dieser Sicherheitsempfehlung ausgeschlossen werden sollen, erfahren Sie unter [Ausschließen einer Ressource aus Empfehlungen und der Sicherheitsbewertung](../../security-center/exempt-resource.md), wie Sie eine Ausnahmeregel erstellen.

>[!NOTE]
>Zu den sicheren Objekten in Azure Automation gehören Anmeldeinformationen, Zertifikate, Verbindungen und verschlüsselte Variablen. Diese Objekte werden mithilfe eines eindeutigen Schlüssels verschlüsselt und in Azure Automation gespeichert, der für jedes Automation-Konto generiert wird. Azure Automation speichert den Schlüssel im vom System verwalteten Schlüsseltresor. Vor dem Speichern einer sicheren Ressource lädt Azure Automation den Schlüssel aus dem Schlüsseltresor und verwendet ihn dann zum Verschlüsseln der Ressource.

## <a name="variable-types"></a>Variablentypen

Beim Erstellen einer Variable über das Azure-Portal müssen Sie einen Datentyp aus der Dropdownliste angeben, damit das entsprechende Steuerelement zum Eingeben des Variablenwerts im Portal angezeigt werden kann. Im Folgenden finden Sie die in Azure Automation verfügbaren Variablentypen:

* String
* Integer
* Datetime
* Boolean
* Null

Die Variable ist nicht auf den festgelegten Datentyp beschränkt. Sie müssen die Variable in Windows PowerShell festlegen, wenn Sie einen anderen Wertetyp angeben möchten. Wenn Sie `Not defined` angeben, wird der Wert der Variable auf NULL festgelegt. Sie müssen den Wert mit dem Cmdlet [Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable) oder dem internen Cmdlet `Set-AutomationVariable` festlegen.

Sie können den Wert für einen komplexen Variablentyp nicht im Azure-Portal erstellen oder ändern. Sie können jedoch einen Wert eines beliebigen Typs mithilfe von Windows PowerShell bereitstellen. Komplexe Typen werden als [Newtonsoft.Json.Linq.JProperty](https://www.newtonsoft.com/json/help/html/N_Newtonsoft_Json_Linq.htm) für einen komplexen Objekttyp anstelle eines [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) vom Typ „PSObject“ abgerufen.

Sie können mehrere Werte in einer einzigen Variable speichern, indem Sie ein Array oder eine Hashtabelle erstellen und in der Variable speichern.

>[!NOTE]
>Variablen für VM-Namen dürfen maximal 80 Zeichen lang sein. Variablen für Ressourcengruppen dürfen maximal 90 Zeichen lang sein. Weitere Informationen finden Sie unter [Benennungsregeln und -einschränkungen für Azure-Ressourcen](../../azure-resource-manager/management/resource-name-rules.md).

## <a name="powershell-cmdlets-to-access-variables"></a>PowerShell-Cmdlets für den Zugriff auf Variablen

Über die Cmdlets in der folgenden Tabelle können Sie Variablen für Automation mit PowerShell erstellen und verwalten. Diese sind im Lieferumfang der [Az-Module](modules.md#az-modules) enthalten.

| Cmdlet | BESCHREIBUNG |
|:---|:---|
|[Get-AzAutomationVariable](/powershell/module/az.automation/get-azautomationvariable) | Ruft den Wert einer vorhandenen Variable ab. Wenn es sich um einen einfachen Werttyp handelt, wird der gleiche Typ abgerufen. Bei einem komplexen Typ wird der Typ `PSCustomObject` abgerufen. <br>**Hinweis:** Sie können dieses Cmdlet nicht verwenden, um den Wert einer verschlüsselten Variable abzurufen. Dies ist nur mit dem internen Cmdlet `Get-AutomationVariable` in einem Runbook oder einer DSC-Konfiguration möglich. Weitere Informationen finden Sie unter [Interne Cmdlets für den Zugriff auf Variablen](#internal-cmdlets-to-access-variables). |
|[New-AzAutomationVariable](/powershell/module/az.automation/new-azautomationvariable) | Erstellt eine neue Variable und legt ihren Wert fest.|
|[Remove-AzAutomationVariable](/powershell/module/az.automation/remove-azautomationvariable)| Entfernt eine vorhandene Variable.|
|[Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable)| Legt den Wert für eine vorhandene Variable fest. |

## <a name="internal-cmdlets-to-access-variables"></a>Interne Cmdlets für den Zugriff auf Variablen

Die internen Cmdlets in der folgenden Tabelle werden für den Zugriff auf Variablen in Runbooks und DSC-Konfigurationen verwendet. Diese Cmdlets sind im globalen Modul `Orchestrator.AssetManagement.Cmdlets` enthalten. Weitere Informationen finden Sie unter [Interne Cmdlets](modules.md#internal-cmdlets).

| Internes Cmdlet | BESCHREIBUNG |
|:---|:---|
|`Get-AutomationVariable`|Ruft den Wert einer vorhandenen Variable ab.|
|`Set-AutomationVariable`|Legt den Wert für eine vorhandene Variable fest.|

> [!NOTE]
> Vermeiden Sie die Verwendung von Variablen im Parameter `Name` von `Get-AutomationVariable` in einem Runbook oder einer DSC-Konfiguration. Durch die Verwendung dieser Variablen kann die Ermittlung von Abhängigkeiten zwischen Runbooks und Automation-Variablen zur Entwurfszeit erschwert werden.

`Get-AutomationVariable` funktioniert nicht in PowerShell, sondern nur in einem Runbook oder einer DSC-Konfiguration. Wenn Sie z. B. den Wert einer verschlüsselten Variable anzeigen möchten, können Sie ein Runbook erstellen, um die Variable abzurufen und dann in den Ausgabestream zu schreiben:

```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="python-2-functions-to-access-variables"></a>Python 2-Funktionen für den Zugriff auf Variablen

Die Funktionen in der folgenden Tabelle werden zum Zugreifen auf Variablen in einem Python 2-Runbook verwendet.

|Python 2-Funktionen|BESCHREIBUNG|
|:---|:---|
|`automationassets.get_automation_variable`|Ruft den Wert einer vorhandenen Variable ab. |
|`automationassets.set_automation_variable`|Legt den Wert für eine vorhandene Variable fest. |

> [!NOTE]
> Sie müssen das Modul `automationassets` oben im Python-Runbook importieren, um auf die Ressourcenfunktionen zugreifen zu können.

## <a name="create-and-get-a-variable"></a>Erstellen und Abrufen einer Variable

>[!NOTE]
>Wenn Sie die Verschlüsselung für eine Variable entfernen möchten, müssen Sie die Variable löschen und als unverschlüsselt neu erstellen.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Erstellen und Abrufen einer Variable über das Azure-Portal

1. Wählen Sie in Ihrem Automation-Konto im linken Bereich unter **Freigegebene Ressourcen** die Option **Variablen** aus.
2. Wählen Sie auf der Seite **Variablen** die Option **Variable hinzufügen** aus.
3. Füllen Sie die Optionen auf dem Blatt **Neue Variable** aus, und wählen Sie anschließend **Erstellen** aus, um die neue Variable zu speichern.

> [!NOTE]
> Nachdem Sie eine verschlüsselte Variable gespeichert haben, kann sie nicht mehr im Portal angezeigt werden. Sie kann lediglich aktualisiert werden.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Erstellen und Abrufen einer Variable in Windows PowerShell

Ihr Runbook oder Ihre DSC-Konfiguration verwendet das Cmdlet `New-AzAutomationVariable`, um eine neue Variable zu erstellen und ihren Anfangswert festzulegen. Wenn die Variable verschlüsselt ist, sollte beim Aufruf der Parameter `Encrypted` verwendet werden. Ihr Skript kann den Wert der Variable mithilfe von `Get-AzAutomationVariable` abrufen.

>[!NOTE]
>Mit einem PowerShell-Skript kann kein verschlüsselter Wert abgerufen werden. Dies ist nur über die Verwendung des internen Cmdlets `Get-AutomationVariable` möglich.

Im folgenden Beispiel wird gezeigt, wie eine Zeichenfolgenvariable erstellt und dann ihr Wert zurückgegeben wird.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Das folgende Beispiel zeigt, wie eine Variable mit einem komplexen Typ erstellt wird und dann ihre Eigenschaften abgerufen werden. In diesem Fall wird ein Objekt für einen virtuellen Computer von [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) verwendet, wobei eine Teilmenge der Eigenschaften angegeben wird.

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01" | Select Name, Location, Extensions
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable"

$vmName = $vmValue.Name
$vmExtensions = $vmValue.Extensions
```
## <a name="textual-runbook-examples"></a>Beispiele für Textrunbooks

### <a name="retrieve-and-set-a-simple-value-from-a-variable"></a>Abrufen und Festlegen eines einfachen Werts einer Variable

Im folgenden Beispiel wird gezeigt, wie Sie eine Variable in einem Textrunbook festlegen und abrufen. In diesem Beispiel wird davon ausgegangen, dass die Integervariablen `NumberOfIterations` und `NumberOfRunnings` sowie eine Zeichenfolgenvariable mit dem Namen `SampleMessage` erstellt werden.

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

### <a name="retrieve-and-set-a-variable-in-a-python-2-runbook"></a>Abrufen und Festlegen einer Variable in einem Python 2-Runbook

Im folgenden Beispiel wird veranschaulicht, wie Sie in einem Python 2-Runbook eine Variable abrufen, eine Variable festlegen und eine Ausnahme für eine nicht vorhandene Variable behandeln.

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
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

## <a name="graphical-runbook-examples"></a>Beispiele für grafische Runbooks

In einem grafischen Runbook können Sie Aktivitäten für die internen Cmdlets `Get-AutomationVariable` oder `Set-AutomationVariable` hinzufügen. Klicken Sie einfach mit der rechten Maustaste im Bibliotheksbereich des grafischen Editors auf die Variable, und wählen Sie die gewünschte Aktivität aus.

![Hinzufügen einer Variablen zum Zeichenbereich](../media/variables/runbook-variable-add-canvas.png)

In der folgenden Abbildung werden Beispielaktivitäten zum Aktualisieren einer Variablen mit einem einfachen Wert in einem grafischen Runbook veranschaulicht. In diesem Beispiel ruft die Aktivität für `Get-AzVM` eine einzelne Azure-VM ab und speichert den Computernamen in einer vorhandenen Automation-Zeichenfolgenvariablen. Es spielt keine Rolle, ob es sich bei der [Verknüpfung um eine Pipeline oder eine Sequenz handelt](../automation-graphical-authoring-intro.md#use-links-for-workflow), da der Code in der Ausgabe nur ein einziges Objekt erwartet.

![Festlegen einer einfachen Variablen](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den Cmdlets, die für den Zugriff auf Variablen verwendet werden, finden Sie unter [Verwalten von Modulen in Azure Automation](modules.md).
* Allgemeine Informationen zu Runbooks finden Sie unter [Ausführen von Runbooks in Azure Automation](../automation-runbook-execution.md).
* Ausführliche Informationen zu DSC-Konfigurationen finden Sie in der [Übersicht über die Zustandskonfiguration](../automation-dsc-overview.md).
