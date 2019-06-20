---
title: Anmeldeinformationsobjekte in Azure Automation
description: Anmeldeinformationsobjekte in Azure Automation enthalten Sicherheitsanmeldeinformationen, die zur Authentifizierung von Ressourcen verwendet werden können, auf die über das Runbook oder die DSC-Konfiguration zugegriffen wird. Dieser Artikel beschreibt, wie Sie Anmeldeinformationsobjekte erstellen und in einem Runbook oder einer DSC-Konfiguration verwenden.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bb34c3f04302e6a2b5cc307b98bafe93e09fcf2f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734708"
---
# <a name="credential-assets-in-azure-automation"></a>Anmeldeinformationsobjekte in Azure Automation

Ein Automation-Anmeldeinformationsobjekt enthält ein Objekt, das Sicherheitsanmeldeinformationen wie beispielsweise einen Benutzernamen und ein Kennwort umfasst. Runbooks und DSC-Konfigurationen können Cmdlets verwenden, die zur Authentifizierung ein PSCredential-Objekt akzeptieren, oder sie extrahieren den Benutzernamen und das Kennwort aus dem PSCredential-Objekt, um diese Informationen für eine Anwendung oder einen Dienst bereitzustellen, die bzw. der eine Authentifizierung erfordert. Die Eigenschaften für ein Anmeldeinformationsobjekt werden sicher in Azure Automation gespeichert und können im Runbook oder in der DSC-Konfiguration über die Aktivität [Get-AutomationPSCredential](#activities) abgerufen werden.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Zu den sicheren Objekten in Azure Automation gehören Anmeldeinformationen, Zertifikate, Verbindungen und verschlüsselte Variablen. Diese Objekte werden mithilfe eines eindeutigen Schlüssels verschlüsselt und in Azure Automation gespeichert, der für jedes Automation-Konto generiert wird. Dieser Schlüssel wird in Key Vault gespeichert. Vor dem Speichern eines sicheren Objekts wird der Schlüssel aus Key Vault geladen und dann zum Verschlüsseln des Objekts verwendet.

## <a name="azure-classic-powershell-cmdlets"></a>Klassische Azure PowerShell-Cmdlets

Die Cmdlets in der folgenden Tabelle werden zum Erstellen und Verwalten von Automation-Anmeldeinformationsobjekten mit Windows PowerShell verwendet.  Sie gehören zum Lieferumfang des [Azure PowerShell-Moduls](/powershell/azure/overview), das zur Verwendung in Automation-Runbooks und DSC-Konfigurationen verfügbar ist.

| Cmdlets | BESCHREIBUNG |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/servicemanagement/azure/get-azureautomationcredential) |Ruft Informationen zu einem Anmeldeinformationsobjekt ab. Sie können mithilfe der Aktivität **Get-AutomationPSCredential** nur das Anmeldeinformationsobjekt selbst abrufen. |
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Erstellt ein neues Anmeldeinformationsobjekt. |
| [Remove-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Entfernt ein Anmeldeinformationsobjekt. |
| [Set-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Legt die Eigenschaften für ein vorhandenes Anmeldeinformationsobjekt fest. |

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-Cmdlets

Die Cmdlets in der folgenden Tabelle werden für AzureRM zum Erstellen und Verwalten von Automation-Anmeldeinformationsobjekten mit Windows PowerShell verwendet.  Sie gehören zum Funktionsumfang des [AzureRM.Automation-Moduls](/powershell/azure/overview), das zur Verwendung in Automation-Runbooks und DSC-Konfigurationen verfügbar ist.

| Cmdlets | BESCHREIBUNG |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential) |Ruft Informationen zu einem Anmeldeinformationsobjekt ab. Dies gibt kein PSCredential-Objekt zurück.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential) |Erstellt ein neues Anmeldeinformationsobjekt. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential) |Entfernt ein Anmeldeinformationsobjekt. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential) |Legt die Eigenschaften für ein vorhandenes Anmeldeinformationsobjekt fest. |

## <a name="activities"></a>activities

Die Aktivitäten in der folgenden Tabelle werden für den Zugriff auf Anmeldeinformationen in einem Runbook und DSC-Konfigurationen verwendet.

| activities | BESCHREIBUNG |
|:--- |:--- |
| Get-AutomationPSCredential |Ruft Anmeldeinformationen zur Verwendung in einem Runbook oder einer DSC-Konfiguration ab. Gibt ein [System.Management.Automation.PSCredential](/dotnet/api/system.management.automation.pscredential) -Objekt zurück. |

> [!NOTE]
> Vermeiden Sie die Verwendung von Variablen im Parameter „–Name“ von „Get-AutomationPSCredential“ in einem Runbook, da dies die Ermittlung von Abhängigkeiten zwischen Runbooks oder DSC-Konfigurationen und Verbindungsobjekten zur Entwurfszeit erschweren kann.

## <a name="python2-functions"></a>Python2-Funktionen

Mit der Funktion in der folgenden Tabelle wird auf Anmeldeinformationen in einem Python2-Runbook zugegriffen.

| Funktion | BESCHREIBUNG |
|:---|:---|
| automationassets.get_automation_credential | Ruft Informationen zu einem Anmeldeinformationsobjekt ab. |

> [!NOTE]
> Sie müssen das Modul „automationassets“ oben im Python-Runbook importieren, um auf die Assetfunktionen zugreifen zu können.

## <a name="creating-a-new-credential-asset"></a>Erstellen eines neuen Anmeldeinformationsobjekts

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>So erstellen Sie ein neues Anmeldeinformationsobjekt über das Azure-Portal

1. Wählen Sie in Ihrem Automation-Konto unter **Freigegebene Ressourcen** die Option **Anmeldeinformationen** aus.
1. Klicken Sie auf **+ Anmeldeinformationen hinzufügen**.
1. Geben Sie die erforderlichen Daten ein, und klicken Sie auf **Erstellen** , um die neuen Anmeldeinformationen zu speichern.

> [!NOTE]
> Benutzerkonten, die mehrstufige Authentifizierung verwenden, werden in Azure Automation nicht unterstützt.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>So erstellen Sie ein neues Anmeldeinformationsobjekt mithilfe von Windows PowerShell

Die folgenden Beispielbefehle zeigen, wie Sie ein neues Automation-Anmeldeinformationsobjekt erstellen. Es wird zunächst ein PSCredential-Objekt mit Name und Kennwort erstellt, anschließend werden diese Informationen zum Erstellen des Anmeldeinformationsobjekts verwendet. Alternativ können Sie das Cmdlet **Get-Credential** verwenden, um zur Eingabe von Name und Kennwort aufgefordert zu werden.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Verwenden von PowerShell-Anmeldeinformationen

In einem Runbook oder einer DSC-Konfiguration rufen Sie ein Anmeldeinformationsobjekt mit der Aktivität **Get-AutomationPSCredential** ab. Diese Aktivität gibt ein [PSCredential-Objekt](/dotnet/api/system.management.automation.pscredential) zurück, das Sie mit einer Aktivität oder einem Cmdlet verwenden können, die oder das einen PSCredential-Parameter erfordert. Sie können auch die Eigenschaften des Anmeldeinformationsobjekts abrufen, um diese einzeln zu verwenden. Das Objekt besitzt eine Eigenschaft für den Benutzernamen und das sichere Kennwort. Oder Sie verwenden die Methode **GetNetworkCredential**, um ein [NetworkCredential](/dotnet/api/system.net.networkcredential)-Objekt zurückzugeben, das eine unsichere Version des Kennworts bereitstellt.

> [!NOTE]
> **Get-AzureRmAutomationCredential** gibt kein **PSCredential** zurück, das für die Authentifizierung verwendet werden kann. Es bietet lediglich Informationen zu den Anmeldeinformationen. Wenn Sie Anmeldeinformationen in einem Runbook verwenden müssen, müssen Sie **Get-AutomationPSCredential** verwenden, um das **PSCredential**-Objekt abzurufen.

### <a name="textual-runbook-sample"></a>Beispiel für ein Textrunbook

Die folgenden Beispielbefehle zeigen, wie Sie ein PowerShell-Anmeldeinformationsobjekt in einem Runbook verwenden. In diesem Beispiel wird das Anmeldeinformationsobjekt abgerufen, und der darin enthaltene Benutzername sowie das Kennwort werden Variablen zugewiesen.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Sie können Anmeldeinformationen auch zum Authentifizieren mit [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) in Azure verwenden. In den meisten Fällen sollten Sie ein [ausführendes Konto](../manage-runas-account.md) verwenden und mit [Get-AutomationConnection](../automation-connections.md) abrufen.

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Beispiel für ein grafisches Runbook

Sie können einem grafischen Runbook eine **Get-AutomationPSCredential**-Aktivität hinzufügen, indem Sie im Bibliotheksbereich des grafischen Editors mit der rechten Maustaste auf die Anmeldeinformationen klicken und **Zum Zeichenbereich hinzufügen** auswählen.

![Anmeldeinformationen zum Zeichenbereich hinzufügen](../media/credentials/credential-add-canvas.png)

Die folgende Abbildung zeigt ein Beispiel für die Verwendung eines Anmeldeinformationsobjekts in einem grafischen Runbook.  In diesem Fall wird das Objekt zur Bereitstellung von Authentifizierungsinformationen für ein Runbook in Azure-Ressourcen verwendet, wie unter [Authentifizieren von Runbooks mit Azure AD-Benutzerkonto](../automation-create-aduser-account.md) beschrieben.  Die erste Aktivität ruft die Anmeldeinformationen ab, die Zugriff auf das Azure-Abonnement haben.  Die Aktivität **Add-AzureAccount** verwendet diese Anmeldeinformationen anschließend, um eine Authentifizierung für alle nachfolgenden Aktivitäten bereitzustellen.  Es wird eine [Pipelineverknüpfung](../automation-graphical-authoring-intro.md#links-and-workflow) verwendet, da **Get-AutomationPSCredential** ein einzelnes Objekt erwartet.  

![Anmeldeinformationen zum Zeichenbereich hinzufügen](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Verwenden von PowerShell-Anmeldeinformationen in DSC

DSC-Konfigurationen in Azure Automation können mithilfe von **Get-AutomationPSCredential** auf Anmeldeinformationen verweisen. Diese Informationen können aber bei Bedarf auch über Parameter übergeben werden. Weitere Informationen finden Sie unter [Kompilieren von Konfigurationen in Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Verwenden von Anmeldeinformationen in Python2

Im folgenden Beispiel wird der Zugriff auf Anmeldeinformationen in Python2-Runbooks veranschaulicht.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Links bei der grafischen Inhaltserstellung finden Sie unter [Links bei der grafischen Erstellung](../automation-graphical-authoring-intro.md#links-and-workflow)
* Informationen zu den verschiedenen Authentifizierungsmethoden bei Automation finden Sie unter [Azure Automation-Sicherheit](../automation-security-overview.md)
* Informationen zu den ersten Schritten mit grafischen Runbooks finden Sie unter [Mein erstes grafisches Runbook](../automation-first-runbook-graphical.md)
* Die ersten Schritte mit PowerShell-Workflow-Runbooks sind unter [Mein erstes PowerShell-Workflow-Runbook](../automation-first-runbook-textual.md)
* Informationen zu den ersten Schritten mit Python2-Runbooks finden Sie unter [My first Python2 runbook](../automation-first-runbook-textual-python2.md) (Mein erstes Python2-Runbook). 
