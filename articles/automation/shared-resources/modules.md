---
title: Verwalten von Modulen in Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie PowerShell-Module verwenden, um Cmdlets in Runbooks und DSC-Ressourcen in DSC-Konfigurationen zu aktivieren.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/01/2021
ms.topic: conceptual
ms.openlocfilehash: a784127cfd6019629f1c2714d0f36850406c3b9d
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99548769"
---
# <a name="manage-modules-in-azure-automation"></a>Verwalten von Modulen in Azure Automation

Azure Automation nutzt eine Reihe von PowerShell-Modulen, um Cmdlets in Runbooks und DSC-Ressourcen in DSC-Konfigurationen zu aktivieren. Zu den unterstützten Modulen gehören:

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az)
* [Azure PowerShell AzureRM.Automation](/powershell/module/azurerm.automation/)
* Weitere PowerShell-Module
* Internes `Orchestrator.AssetManagement.Cmdlets`-Modul
* Python 2-Module
* Selbst erstellte benutzerdefinierte Module

Beim Erstellen eines Automation-Kontos importiert Azure Automation standardmäßig einige Module. Weitere Informationen finden Sie unter [Standardmodule](#default-modules).

## <a name="sandboxes"></a>Sandboxes

Wenn Automation Runbook- und DSC-Kompilierungsaufträge ausführt, werden die Module in Sandboxes geladen, in denen die Runbooks ausgeführt und die DSC-Konfigurationen kompiliert werden können. DSC-Ressourcen in Modulen werden ebenfalls automatisch auf dem DSC-Pullserver platziert. Computer können die Ressourcen beim Anwenden von DSC-Konfigurationen per Pull abrufen.

>[!NOTE]
>Importieren Sie nur Module, die Ihre Runbooks und DSC-Konfigurationen wirklich benötigen. Es wird nicht empfohlen, das Az-Stammmodul zu importieren. Es enthält viele andere Module, die Sie möglicherweise nicht benötigen, die aber zu Leistungsproblemen führen können. Importieren Sie stattdessen einzelne Module, z. B. Az.Compute.

Eine Cloudsandbox unterstützt maximal 48 Systemaufrufe und schränkt alle anderen Aufrufe aus Sicherheitsgründen ein. Andere Funktionen, etwa die Verwaltung von Anmeldeinformationen und einige Netzwerkfunktionen, werden in einer Cloudsandbox nicht unterstützt.

Aufgrund der Anzahl der enthaltenen Module und Cmdlets ist es schwierig, vorab zu wissen, welche der Cmdlets nicht unterstützte Aufrufe durchführen. Im Allgemeinen treten Probleme bei Cmdlets auf, für die erhöhte Zugriffsrechte erforderlich sind oder die Anmeldeinformationen als Parameter erfordern, oder bei Cmdlets für Netzwerkfunktionen. Cmdlets, die umfassende Netzwerkvorgänge ausführen, werden in der Sandbox nicht unterstützt. Dies schließt auch [Connect-AipService](/powershell/module/aipservice/connect-aipservice) aus dem PowerShell-Modul AIPService und [Resolve-DnsName](/powershell/module/dnsclient/resolve-dnsname) aus dem Modul DNSClient ein.

Dabei handelt es sich um bekannte Einschränkungen bei einer Sandbox. Zur Umgehung dieses Problems wird empfohlen, einen [Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md) bereitzustellen oder [Azure Functions](../../azure-functions/functions-overview.md) zu verwenden.

## <a name="default-modules"></a>Standardmodule

In der folgenden Tabelle sind Module aufgeführt, die Azure Automation standardmäßig importiert, wenn Sie Ihr Automation-Konto erstellen. Automation kann neuere Versionen dieser Module importieren. Sie können jedoch die ursprüngliche Version nicht aus Ihrem Automation-Konto entfernen, selbst wenn Sie eine neuere Version löschen. Beachten Sie, dass diese Standardmodule mehrere AzureRM-Module enthalten.

Die Standardmodule werden auch als globale Module bezeichnet. Im Azure-Portal ist die Eigenschaft **Globales Modul** **true**, wenn Sie ein Modul anzeigen, das beim Erstellen des Kontos importiert wurde.

![Screenshot der Eigenschaft „Globales Modul“ im Azure-Portal.](../media/modules/automation-global-modules.png)

Automation importiert das Az-Stammmodul nicht automatisch in neue oder vorhandene Automation-Konten. Weitere Informationen zum Arbeiten mit diesen Modulen finden Sie unter [Migrieren zu Az-Modulen](#migrate-to-az-modules).

> [!NOTE]
> Es wird nicht empfohlen, Module und Runbooks in Automation-Konten zu ändern, die für die Bereitstellung des Features [VMs außerhalb der Geschäftszeiten starten/beenden](../automation-solution-vm-management.md) verwendet werden.

|Modulname|Version|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>Az-Module

Für Az.Automation weist die Mehrzahl der Cmdlets denselben Namen auf wie bei den AzureRM-Modulen, mit der Ausnahme, dass das Präfix `AzureRM` in `Az` geändert wurde. Eine Liste der Az-Module, die nicht dieser Namenskonvention folgen, finden Sie in der [Liste der Ausnahmen](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Interne Cmdlets

Azure Automation unterstützt das interne `Orchestrator.AssetManagement.Cmdlets`-Modul für den Log Analytics-Agent für Windows, das standardmäßig installiert wird. In der folgenden Tabelle werden die internen Cmdlets definiert. Diese Cmdlets sind dafür konzipiert, anstelle von Azure PowerShell-Cmdlets verwendet zu werden, um mit freigegebenen Ressourcen zu interagieren. Sie können Geheimnisse aus verschlüsselten Variablen, Anmeldeinformationen und verschlüsselten Verbindungen abrufen.

>[!NOTE]
>Die internen Cmdlets sind nur verfügbar, wenn Sie Runbooks in der Sandboxumgebung von Azure oder auf einem Hybrid Runbook Worker unter Windows ausführen. 

|Name|BESCHREIBUNG|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Beachten Sie, dass sich die internen Cmdlets bei der Benennung von den Az- und AzureRM-Cmdlets unterscheiden. Namen interner Cmdlets enthalten keine Wörter wie `Azure` oder `Az` als Substantive, verwenden aber das Wort `Automation`. Es wird empfohlen, diese anstelle von Az- oder AzureRM-Cmdlets während der Runbookausführung in einer Azure-Sandbox oder auf einem Hybrid Runbook Worker unter Windows zu verwenden. Sie benötigen weniger Parameter und werden im Kontext Ihres bereits ausgeführten Auftrags ausgeführt.

Verwenden Sie Az- oder AzureRM-Cmdlets zum Bearbeiten von Automation-Ressourcen außerhalb des Kontexts eines Runbooks. 

## <a name="python-modules"></a>Python-Module

Sie können Python 2-Runbooks in Azure Automation erstellen. Informationen zu Python-Modulen finden Sie unter [Verwalten von Python 2-Paketen in Azure Automation](../python-packages.md).

## <a name="custom-modules"></a>Benutzerdefinierte Module

Azure Automation unterstützt benutzerdefinierte PowerShell-Module, die Sie zur Verwendung mit Ihren Runbooks und DSC-Konfigurationen verwenden können. Eine Art benutzerdefiniertes Modul ist ein Integrationsmodul, das optional eine Datei mit Metadaten enthalten kann, um die benutzerdefinierte Funktionalität für die Cmdlets des Moduls zu definieren. Ein Beispiel für die Verwendung eines Integrationsmoduls wird unter [Hinzufügen eines Verbindungstyps](../automation-connections.md#add-a-connection-type) veranschaulicht.

Azure Automation kann ein benutzerdefiniertes Modul importieren, um Cmdlets zur Verfügung zu stellen. Im Hintergrund wird das Modul gespeichert und wie andere Module in Azure-Sandboxes verwendet.

## <a name="migrate-to-az-modules"></a>Migrieren zu Az-Modulen

In diesem Abschnitt erfahren Sie, wie Sie zu den Az-Modulen in Automation migrieren. Weitere Informationen finden Sie unter [Migrieren von Azure PowerShell von AzureRM zum Az-Modul](/powershell/azure/migrate-from-azurerm-to-az).

Die Ausführung von AzureRM-Modulen und Az-Modulen im selben Automation-Konto wird nicht empfohlen. Wenn Sie sicher sind, dass Sie von AzureRM zu Az migrieren möchten, ist es am besten, sich in vollem Umfang zu einer vollständige Migration zu entschließen. Automation-Sandboxes werden häufig im Automation-Konto wiederverwendet, um die Startzeiten zu verkürzen. Wenn Sie keine vollständige Modulmigration durchführen, starten Sie möglicherweise einen Auftrag, der ausschließlich AzureRM-Module nutzt, und dann einen anderen Auftrag nur mit Az-Modulen. Die Sandbox stürzt dann bald ab, und Sie erhalten einen schwerwiegenden Fehler, da die Module nicht kompatibel sind. Diese Situation führt zu zufällig auftretenden Abstürzen bei beliebigen Runbooks oder Konfigurationen.

>[!NOTE]
>Beim Erstellen eines neuen Automation-Kontos, installiert Automation selbst nach der Migration zu Az-Modulen standardmäßig die AzureRM-Module. Sie können die Runbooks von Tutorials weiterhin mit den AzureRM-Cmdlets aktualisieren. Sie sollten diese Runbooks jedoch nicht ausführen.

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Testen Ihrer Runbooks und DSC-Konfigurationen vor der Modulmigration

Stellen Sie sicher, dass Sie alle Runbooks und DSC-Konfigurationen sorgfältig in einem separaten Automation-Konto testen, bevor Sie zu den Az-Modulen migrieren. 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Anhalten und Aufheben des Zeitplans aller Runbooks, die AzureRM-Module verwenden

Um sicherzustellen, dass Sie keine bestehenden Runbooks oder DSC-Konfigurationen ausführen, die AzureRM-Module verwenden, müssen Sie alle betroffenen Runbooks und Konfigurationen beenden und ihre Zeitplanung aufheben. Stellen Sie zunächst sicher, dass Sie jedes Runbook oder jede DSC-Konfiguration und deren zugehörige Zeitpläne gesondert überprüfen, um zu gewährleisten, dass Sie das Element ggf. in Zukunft neu planen können.

Wenn Sie bereit sind, Ihre Zeitpläne zu entfernen, können Sie entweder das Azure-Portal oder das Cmdlet [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) verwenden. Weitere Informationen finden Sie unter [Entfernen eines Zeitplans](schedules.md#remove-a-schedule).

### <a name="remove-azurerm-modules"></a>Entfernen der AzureRM-Module

Es ist möglich, die AzureRM-Module zu entfernen, bevor Sie die Az-Module importieren. Dadurch kann jedoch die Synchronisierung der Quellcodeverwaltung unterbrochen werden, und es kann dazu führen, dass alle noch geplanten Skripts mit Fehlern beendet werden. Wenn Sie sich zum Entfernen der Module entschließen, finden Sie Informationen hierzu unter [Deinstallieren von AzureRM](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm).

### <a name="import-az-modules"></a>Importieren von Az-Modulen

Beim Importieren eines Az-Moduls in Ihr Automation-Konto wird das Modul nicht automatisch in die PowerShell-Sitzung importiert, die von Runbooks verwendet wird. Module werden in den folgenden Situationen in die PowerShell-Sitzung importiert:

* Wenn ein Runbook ein Cmdlet in einem Modul aufruft
* Wenn ein Runbook das Modul explizit mit dem Cmdlet [Import-Module](/powershell/module/microsoft.powershell.core/import-module) importiert
* Wenn ein Runbook ein anderes abhängiges Modul importiert

Sie können die Az-Module im Azure-Portal importieren. Denken Sie daran, nicht das gesamte Az.Automation-Modul, sondern nur die benötigten Az-Module zu importieren. Da [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) eine Abhängigkeit für die anderen Az-Module darstellt, muss dieses Modul vor den anderen Modulen importiert werden.

1. Wählen Sie in Ihrem Automation-Konto unter **Freigegebene Ressourcen** die Option **Module** aus.
2. Wählen Sie **Katalog durchsuchen** aus.  
3. Geben Sie in die Suchleiste den Namen des Moduls ein (z. B. `Az.Accounts`).
4. Wählen Sie auf der Seite „PowerShell-Module“ die Option **Importieren** aus, um das Modul in Ihr Automation-Konto zu importieren.

    ![Screenshot: Importieren von Modulen in Ihr Automation-Konto](../media/modules/import-module.png)

Sie können diesen Importvorgang auch über den [PowerShell-Katalog](https://www.powershellgallery.com) ausführen, indem Sie nach dem zu importierenden Modul suchen. Wenn Sie das Modul gefunden haben, wählen Sie es aus, und wählen Sie dann die Registerkarte **Azure Automation** aus. Wählen Sie **Deploy to Azure Automation** (In Azure Automation bereitstellen) aus.

![Screenshot: direktes Importieren von Modulen aus dem PowerShell-Katalog](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testen Ihres Runbooks

Nachdem Sie die Az-Module in das Automation-Konto importiert haben, können Sie beginnen, Ihre Runbooks und DSC-Konfigurationen so zu bearbeiten, dass sie die neuen Module verwenden. Eine Möglichkeit zum Testen der Änderung eines Runbooks zur Verwendung der neuen Cmdlets besteht darin, den Befehl `Enable-AzureRmAlias -Scope Process` am Anfang des Runbooks anzugeben. Wenn Sie diesen Befehl Ihrem Runbook hinzufügen, kann das Skript ohne Änderungen ausgeführt werden.

## <a name="author-modules"></a>Erstellen von Modulen

Es wird empfohlen, dass Sie die Empfehlungen in diesem Abschnitt befolgen, wenn Sie ein benutzerdefiniertes PowerShell-Modul zur Verwendung in Azure Automation erstellen. Sie müssen zumindest eine PSD1-, PSM1- oder PowerShell-Modul-**DLL**-Datei mit dem gleichen Namen wie der Modulordner erstellen, um Ihr Modul für den Importvorgang vorzubereiten. Anschließend packen Sie den Modulordner, damit Azure Automation ihn als einzelne Datei importieren kann. Das **ZIP**-Paket sollte den gleichen Namen wie der enthaltene Modulordner aufweisen.

Weitere Informationen zum Erstellen eines PowerShell-Moduls finden Sie unter [Schreiben eines PowerShell-Skriptmoduls](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

### <a name="version-folder"></a>Versionsordner

Mit der parallelen PowerShell-Modulversionsverwaltung können Sie mehr als eine Version eines Moduls innerhalb von PowerShell verwenden. Dies kann hilfreich sein, wenn Sie ältere Skripts haben, die getestet wurden und nur mit einer bestimmten Version eines PowerShell-Moduls funktionieren, während andere Skripts aber eine neuere Version desselben PowerShell-Moduls benötigen.

Um PowerShell-Module zu erstellen, sodass Sie mehrere Versionen enthalten, erstellen Sie den Modulordner, und erstellen Sie dann einen Ordner in diesem Modulordner für jede Version des Moduls, das verwendbar sein soll. Im folgenden Beispiel stellt ein Modul namens *TestModule* zwei Versionen bereit, 1.0.0 und 2.0.0.

```dos
TestModule
   1.0.0
   2.0.0
```

Kopieren Sie in jedem der Versionsordner Ihre PowerShell-PSM1-, PSD1- oder PowerShell-Modul-**DLL**-Dateien, aus denen ein Modul besteht, in den jeweiligen Versionsordner. Packen Sie den Modulordner (als ZIP), damit Azure Automation ihn als einzelne ZIP-Datei importieren kann. Automation zeigt zwar nur die höchste Version des importierten Moduls an, doch wenn Modulpaket parallele Versionen des Moduls enthält, sind diese für die Verwendung in Ihren Runbooks oder DSC-Konfigurationen verfügbar.  

Automation unterstützt Module, die parallele Versionen innerhalb desselben Pakets enthalten, es unterstützt aber nicht die Verwendung mehrerer Versionen eines Moduls über Modulpaketimporte hinweg. Angenommen, Sie importieren beispielsweise **Modul A**, das die Versionen 1 und 2 enthält, in Ihr Automation-Konto. Später aktualisieren Sie das **Modul A**, um die Versionen 3 und 4 aufzunehmen. Wenn Sie es nun in Ihr Automation-Konto importieren, können nur die Versionen 3 und 4 in allen Runbooks oder DSC-Konfigurationen verwendet werden. Wenn alle Versionen (1, 2, 3 und 4) verfügbar sein müssen, sollte die ZIP-Datei, die Sie importieren, die Versionen 1, 2, 3 und 4 enthalten.

Wenn Sie unterschiedliche Versionen desselben Moduls zwischen Runbooks verwenden möchten, sollten Sie immer die Version deklarieren, die Sie in Ihrem Runbook verwenden möchten, indem Sie das Cmdlet `Import-Module` verwenden und den Parameter `-RequiredVersion <version>` einschließen. Selbst wenn es sich bei der zu verwendenden Version um die neueste Version handelt. Dies liegt daran, dass Runbookaufträge in derselben Sandbox ausgeführt werden können. Wenn die Sandbox bereits explizit ein Modul mit einer bestimmten Versionsnummer geladen hat, weil ein vorheriger Auftrag in dieser Sandbox dies angewiesen hatte, laden zukünftige Aufträge in dieser Sandbox nicht automatisch die neueste Version dieses Moduls. Dies liegt daran, dass eine Version davon bereits in die Sandbox geladen wurde.

Verwenden Sie für eine DSC-Ressource den folgenden Befehl, um eine bestimmte Version anzugeben:

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>Hilfeinformationen

Fügen Sie eine Zusammenfassung, eine Beschreibung und einen Hilfe-URI für jedes Cmdlet in Ihrem Modul ein. In PowerShell können Sie mithilfe des Cmdlets `Get-Help` Hilfeinformationen für Cmdlets definieren. Im folgenden Beispiel sehen Sie, wie Sie eine Zusammenfassung und einen Hilfe-URI für eine Moduldatei vom Typ **.psm1** erstellen.

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Bei Angabe dieser Informationen wird Hilfetext über das Cmdlet `Get-Help` in der PowerShell-Konsole angezeigt. Dieser Text wird auch im Azure-Portal angezeigt.

  ![Screenshot: Hilfe zu Integrationsmodulen](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Verbindungstyp

Wenn das Modul eine Verbindung mit einem externen Dienst herstellt, definieren Sie einen Verbindungstyp mithilfe eines [benutzerdefinierten Integrationsmoduls](#custom-modules). Jedes Cmdlet im Modul muss eine Instanz dieses Verbindungstyps (ein Verbindungsobjekt) als Parameter akzeptieren. Benutzer ordnen Parameter des Verbindungsobjekts bei jedem Aufruf eines Cmdlets den entsprechenden Parametern des Cmdlets zu.

![Verwenden einer benutzerdefinierten Verbindung im Azure-Portal](../media/modules/connection-create-new.png)

Im folgenden Runbookbeispiel wird eine Contoso-Verbindungsressource namens `ContosoConnection` verwenden, um auf Contoso-Ressourcen zuzugreifen und Daten vom externen Dienst zurückzugeben. In diesem Beispiel werden die Felder den Eigenschaften `UserName` und `Password` eines Objekts vom Typ `PSCredential` zugeordnet und dann an das Cmdlet übergeben.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

Eine einfachere und bessere Herangehensweise für dieses Verhalten besteht jedoch darin, das Verbindungsobjekt direkt an das Cmdlet zu übergeben:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

Sie können ein solches Verhalten für Ihre Cmdlets aktivieren, indem Sie zulassen, dass ein Verbindungsobjekt direkt als Parameter akzeptiert wird, und nicht nur Verbindungsfelder für Parameter. In der Regel empfiehlt sich jeweils ein Parametersatz, damit ein Benutzer, der Automation nicht verwendet, Ihre Cmdlets aufrufen kann, ohne eine Hashtabelle erstellen zu müssen, die als Verbindungsobjekt dient. Mit dem Parametersatz `UserAccount` werden die Eigenschaften des Verbindungsfelds übergeben. Mit `ConnectionObject` können Sie die Verbindung direkt übergeben.

### <a name="output-type"></a>Ausgabetyp

Definieren Sie den Ausgabetyp für alle Cmdlets in Ihrem Modul. Wenn Sie einen Ausgabetyp für ein Cmdlet definieren, können Sie bei der Erstellung mithilfe von IntelliSense die Ausgabeeigenschaften des Cmdlets bestimmen. Diese Vorgehensweise ist insbesondere bei der Erstellung von grafischen Runbooks hilfreich, da Kenntnisse zum Zeitpunkt des Entwurfs für die Benutzerfreundlichkeit Ihres Moduls von zentraler Bedeutung sind.

Fügen Sie `[OutputType([<MyOutputType>])]` hinzu, wobei `MyOutputType` ein gültiger Typ ist. Weitere Informationen zu `OutputType` finden Sie unter [About Functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute) (Informationen zu Functions: OutputTypeAttribute). Der folgende Code ist ein Beispiel, wie Sie `OutputType` einem Cmdlet hinzufügen:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Screenshot: Ausgabetyp für grafische Runbooks](../media/modules/runbook-graphical-module-output-type.png)

  Dieses Verhalten ähnelt der Textvervollständigung der Ausgabe eines Cmdlets in der PowerShell ISE (Integrationsdienstumgebung), ohne dass diese ausgeführt werden muss.

  ![Screenshot: IntelliSense in PowerShell](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Cmdlet-Status

Legen Sie alle Cmdlets in Ihrem Modul als statusfrei fest. Mehrere Runbookaufträge können gleichzeitig in derselben `AppDomain`, im gleichen Prozess und in der gleichen Sandbox ausgeführt werden. Wenn diese Ebenen den gleichen Status verwenden, können die Aufträge sich gegenseitig beeinflussen. Dieses Verhalten kann zu vorübergehenden und schwierig zu diagnostizierenden Problemen führen. Hier sehen Sie ein Beispiel für eine falsche Vorgehensweise:

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>Modulabhängigkeit

Stellen Sie sicher, dass das Modul vollständig in einem Paket enthalten ist, das mithilfe von xcopy kopiert werden kann. Automation-Module werden an die Automation-Sandboxes verteilt, wenn Runbooks ausgeführt werden. Die Module müssen unabhängig vom Host funktionieren, auf dem Sie ausgeführt werden.

Es muss daher möglich sein, ein Modulpaket zu komprimieren, zu verschieben und nach dem Import in die PowerShell-Umgebung eines anderen Hosts ganz normal zu verwenden. Stellen Sie dazu sicher, dass das Modul nicht von Dateien außerhalb des Modulordners abhängt, der beim Importieren des Moduls in Automation komprimiert wird.

Das Modul darf nicht von konkreten Registrierungseinstellungen auf einem Host abhängen. Beispiele hierfür sind die Einstellungen, die beim Installieren eines Produkts festgelegt werden.

### <a name="module-file-paths"></a>Moduldateipfade

Stellen Sie sicher, dass alle Dateien im Modul Pfade mit einer Länge von weniger als 140 Zeichen aufweisen. Bei Pfaden mit mehr als 140 Zeichen treten Probleme auf, wenn Runbooks importiert werden. Automation kann keine Datei mit einer Pfadlänge über 140 Zeichen in die PowerShell-Sitzung mit `Import-Module` importieren.

## <a name="import-modules"></a>Importieren von Modulen

In diesem Abschnitt sind verschiedene Methoden beschrieben, um ein Modul in Ihr Automation-Konto zu importieren.

### <a name="import-modules-in-the-azure-portal"></a>Importieren von Modulen im Azure-Portal

So importieren Sie Module im Azure-Portal:

1. Navigieren Sie zu Ihrem Automation-Konto.
2. Wählen Sie unter **Freigegebene Ressourcen** die Option **Module** aus.
3. Wählen Sie **Modul hinzufügen** aus.
4. Wählen Sie die **ZIP**-Datei aus, die Ihr Modul enthält.
5. Wählen Sie **OK** aus, um den Importvorgang zu starten.

### <a name="import-modules-by-using-powershell"></a>Importieren von Modulen mithilfe von PowerShell

Sie können das Cmdlet [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) zum Importieren von Modulen in Ihr Automation-Konto verwenden. Das Cmdlet benötigt eine URL für ein Modul-ZIP-Paket.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Sie können das gleiche Cmdlet verwenden, um ein Modul direkt aus dem PowerShell-Katalog zu importieren. Rufen Sie `ModuleName` und `ModuleVersion` aus dem [PowerShell-Katalog](https://www.powershellgallery.com) ab.

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Importieren von Modulen aus dem PowerShell-Katalog

Sie können Module im [PowerShell-Katalog](https://www.powershellgallery.com) entweder direkt aus dem Katalog oder aus Ihrem Automation-Konto importieren.

So importieren Sie ein Modul direkt aus dem PowerShell-Katalog:

1. Wechseln Sie zu https://www.powershellgallery.com, und suchen Sie nach dem zu importierenden Modul.
2. Wählen Sie auf der Registerkarte **Azure Automation** unter **Installationsoptionen** die Option **Deploy to Azure Automation** (In Azure Automation bereitstellen) aus. Das Azure-Portal wird geöffnet. 
3. Wählen Sie auf der Seite „Importieren“ Ihr Automation-Konto und dann **OK** aus.

![Screenshot: Importieren von Modulen aus dem PowerShell-Katalog](../media/modules/powershell-gallery.png)

So importieren Sie ein Modul im PowerShell-Katalog direkt aus Ihrem Automation-Konto:

1. Wählen Sie unter **Freigegebene Ressourcen** die Option **Module** aus. 
2. Wählen Sie **Katalog durchsuchen** aus, und durchsuchen Sie dann den Katalog nach einem Modul. 
3. Wählen Sie das zu importierende Modul und dann **Importieren** aus. 
4. Wählen Sie **OK** aus, um den Importvorgang zu starten.

![Screenshot: Importieren eines Moduls aus dem PowerShell-Katalog über das Azure-Portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Löschen von Modulen

Wenn Sie Probleme mit einem Modul haben oder ein Rollback auf eine frühere Version eines Moduls ausführen müssen, können Sie es aus Ihrem Automation-Konto löschen. Die ursprünglichen Versionen von [Standardmodulen](#default-modules), die beim Erstellen eines Automation-Kontos importiert werden, können nicht gelöscht werden. Wenn das zu löschende Modul eine neuere Version als die [Standardmodule](#default-modules) aufweist, wird ein Rollback auf die Version durchgeführt, die mit Ihrem Automation-Konto installiert wurde. Andernfalls werden alle Module, die Sie aus Ihrem Automation-Konto löschen, entfernt.

### <a name="delete-modules-in-the-azure-portal"></a>Löschen von Modulen im Azure-Portal

So entfernen Sie ein Modul im Azure-Portal:

1. Navigieren Sie zu Ihrem Automation-Konto. Wählen Sie unter **Freigegebene Ressourcen** die Option **Module** aus.
2. Wählen Sie das Modul aus, das Sie entfernen möchten.
3. Wählen Sie auf der Seite „Modul“ die Option **Löschen** aus. Handelt es sich bei dem Modul um eines der [Standardmodule](#default-modules), wird ein Rollback auf die Version durchgeführt, die beim Erstellen des Automation-Kontos vorhanden war.

### <a name="delete-modules-by-using-powershell"></a>Löschen von Modulen mithilfe von PowerShell

Führen Sie zum Entfernen eines Moduls mithilfe von PowerShell den folgenden Befehl aus:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Verwendung von Azure PowerShell-Modulen finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).

* Weitere Informationen zum Erstellen von PowerShell-Modulen finden Sie unter [Schreiben eines Windows PowerShell-Moduls](/powershell/scripting/developer/module/writing-a-windows-powershell-module).
