---
title: Beheben von Fehlern bei freigegebenen Azure Automation-Ressourcen
description: Erfahren Sie, wie Sie Probleme mit freigegebenen Azure Automation-Ressourcen beheben.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 66165a196c8b934df948f1d88b09a5859d3e792f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60401475"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Beheben von Fehlern bei freigegebenen Ressourcen

In diesem Artikel werden Lösungen zum Beheben von Problemen beschrieben, die bei Verwendung der freigegebenen Ressourcen in Azure Automation auftreten können.

## <a name="modules"></a>Module

### <a name="module-stuck-importing"></a>Szenario: Ein Modul bleibt beim Import hängen

#### <a name="issue"></a>Problem

Ein Modul bleibt beim Importieren oder Aktualisieren in Azure Automation im Zustand **Importieren** hängen.

#### <a name="cause"></a>Ursache

Das Importieren von PowerShell-Modulen ist ein komplexer Vorgang mit mehreren Schritten. Bei diesen Vorgang besteht die Möglichkeit, dass ein Modul nicht ordnungsgemäß importiert wird. In diesem Fall kann das Modul, das Sie importieren, in einem Übergangszustand hängen bleiben. Weitere Informationen zu diesem Vorgang finden Sie unter [Importieren eines PowerShell-Moduls]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Lösung

Um dieses Problem zu beheben, müssen Sie das Modul, das im Zustand **Importieren** hängen geblieben ist, mithilfe des Cmdlets [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) entfernen. Sie können dann das Importieren des Moduls erneut versuchen.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>Szenario: AzureRM-Module sind beim Versuch der Aktualisierung während des Importierens hängen geblieben.

#### <a name="issue"></a>Problem

Ein Banner mit der folgenden Meldung wird weiterhin in Ihrem Konto angezeigt, nachdem Sie versucht haben, Ihre AzureRM-Module zu aktualisieren:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Ursache

Beim Aktualisieren der AzureRM-Module in einem Automation-Konto tritt in Ressourcengruppen mit einem mit 0 beginnenden numerischen Namen ein bekanntes Problem auf.

#### <a name="resolution"></a>Lösung

Um die Azure-Module in Ihrem Automation-Konto zu aktualisieren, müssen sich die Module in einer Ressourcengruppe mit einem alphanumerischen Namen befinden. Ressourcengruppen, deren numerischer Name mit 0 beginnt, können zurzeit keine AzureRM-Module aktualisieren.

### <a name="module-fails-to-import"></a>Szenario: Fehler beim Modulimport oder Ausführung von Cmdlets nach Import nicht möglich

#### <a name="issue"></a>Problem

Ein Modul kann nicht importiert werden, oder sein Import ist erfolgreich, aber es werden keine Cmdlets extrahiert.

#### <a name="cause"></a>Ursache

Es folgen häufige Ursachen, warum ein Modul nicht erfolgreich in Azure Automation importiert wird:

* Die Struktur stimmt nicht mit der Struktur überein, die für Automation erforderlich ist.
* Das Modul hängt von einem anderen Modul ab, das für Ihr Automation-Konto nicht bereitgestellt wurde.
* Für das Modul fehlen die Abhängigkeiten im Ordner.
* Das Cmdlet `New-AzureRmAutomationModule` wird zum Hochladen des Moduls verwendet, und Sie haben nicht den vollständigen Speicherpfad angegeben oder das Modul nicht mit einer öffentlich zugänglichen URL geladen.

#### <a name="resolution"></a>Lösung

Sie können dieses Problem mit jeder der folgenden Lösungen beheben:

* Stellen Sie sicher, dass das Modul das folgende Format beachtet: Modulname.Zip **->** Modulname oder Versionsnummer **->** (Modulname.psm1, Modulname.psd1)
* Öffnen Sie die PSD1-Datei, und prüfen Sie, ob für das Modul Abhängigkeiten bestehen. Wenn ja, laden Sie diese Module in das Automation-Konto hoch.
* Stellen Sie sicher, dass alle referenzierten DLLs im Modulordner vorhanden sind.

### <a name="all-modules-suspended"></a>Szenario: Update-AzureModule.ps1 wird beim Aktualisieren der Module angehalten.

#### <a name="issue"></a>Problem

Bei Verwendung des [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1)-Runbooks zum Aktualisieren Ihrer Azure-Module wird der Updateprozess angehalten.

#### <a name="cause"></a>Ursache

Die Standardeinstellung, um zu bestimmen, wie viele Module gleichzeitig aktualisiert werden, liegt bei Verwendung des `Update-AzureModule.ps1`-Skripts bei 10. Der Updateprozess ist fehleranfällig, wenn zu viele Module gleichzeitig aktualisiert werden.

#### <a name="resolution"></a>Lösung

Es ist nicht üblich, dass alle AzureRM-Module im selben Automation-Konto erforderlich sind. Es wird empfohlen, dass Sie nur die AzureRM-Module importieren, die Sie benötigen.

> [!NOTE]
> Vermeiden Sie es, das **AzureRM**-Modul zu importieren. Der Import des **AzureRM**-Moduls führt dazu, dass alle **AzureRM.\*** -Module importiert werden. Dies wird nicht empfohlen.

Wenn der Updatevorgang angehalten wird, müssen Sie den `SimultaneousModuleImportJobCount`-Parameter zum `Update-AzureModules.ps1`-Skript hinzufügen und einen niedrigeren Wert als den Standardwert 10 angeben. Beginnen Sie beim Implementieren dieser Logik mit dem Wert 3 oder 5. `SimultaneousModuleImportJobCount` ist ein Parameter des `Update-AutomationAzureModulesForAccount`-Systemrunbooks, das zum Aktualisieren der Azure-Module verwendet wird. Durch diese Änderung dauert die Ausführung des Prozesses länger, sie steigert jedoch die Wahrscheinlichkeit, dass er abgeschlossen wird. Das folgende Beispiel zeigt den Parameter und seine Position im Runbook:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Ausführende Konten

### <a name="unable-create-update"></a>Szenario: Ausführendes Konto kann nicht erstellt oder aktualisiert werden

#### <a name="issue"></a>Problem

Beim Versuch, ein ausführendes Konto zu erstellen oder zu aktualisieren, erhalten Sie eine ähnliche Fehlermeldung wie die folgende:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Ursache

Sie haben keine Berechtigungen zum Erstellen oder Aktualisieren des ausführenden Kontos, oder die Ressource ist auf Ressourcengruppenebene gesperrt.

#### <a name="resolution"></a>Lösung

Um ein ausführendes Konto zu erstellen oder zu aktualisieren, müssen Sie über geeignete Berechtigungen für die verschiedenen Ressourcen verfügen, die vom ausführenden Konto verwendet werden. Informationen zu den benötigten Berechtigungen zum Erstellen oder Aktualisieren eines ausführenden Kontos finden Sie unter [Berechtigungen zum Konfigurieren von ausführenden Konten](../manage-runas-account.md#permissions).

Wenn das Problem durch eine Sperre verursacht wird, überprüfen Sie, ob die Sperre entfernt werden kann. Navigieren Sie zur gesperrten Ressource, klicken Sie mit der rechten Maustaste auf das Schlosssymbol, und klicken Sie auf **Löschen**, um die Sperre zu entfernen.

### <a name="iphelper"></a>Szenario: Sie erhalten beim Ausführen eines Runbooks den Fehler „Der Einstiegspunkt 'GetPerAdapterInfo' wurde nicht in der DLL 'iplpapi.dll' gefunden“.

#### <a name="issue"></a>Problem

Sie erhalten beim Ausführen eines Runbooks die folgende Ausnahme:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Ursache

Dieser Fehler wird wahrscheinlich durch ein falsch konfiguriertes [ausführendes Konto](../manage-runas-account.md) verursacht.

#### <a name="resolution"></a>Lösung

Stellen Sie sicher, dass Ihr [ausführendes Konto](../manage-runas-account.md) ordnungsgemäß konfiguriert ist. Nachdem es ordnungsgemäß konfiguriert ist, vergewissern Sie sich, dass der Code in Ihrem Runbook für die Authentifizierung mit Azure korrekt ist. Das folgende Beispiel zeigt einen Codeausschnitt in einem Runbook zum Authentifizieren bei Azure mithilfe eines ausführenden Kontos.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Mit [@AzureSupport](https://twitter.com/azuresupport) verbinden – das offizielle Microsoft Azure-Konto zur Verbesserung der Benutzerfreundlichkeit durch Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.
* Wenn Sie weitere Hilfe benötigen, können Sie einen Azure-Supportvorgang anlegen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten**aus.
