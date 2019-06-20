---
title: Behandeln von Problemen mit der Azure Automation-Konfiguration des gewünschten Zustands (Desired State Configuration, DSC)
description: Dieser Artikel enthält Informationen zur Behandlung von Problemen mit der Konfiguration des gewünschten Zustands (Desired State Configuration, DSC).
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7cb0d77a266dbe8afd331782965e7e9a44663671
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514465"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Behandeln von Problemen mit Konfiguration des gewünschten Zustands (Desired State Configuration, DSC)

Dieser Artikel enthält Informationen zur Behandlung von Problemen mit der Konfiguration des gewünschten Zustands (Desired State Configuration, DSC).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Häufige Fehler beim Verwenden der Konfiguration des gewünschten Zustands (Desired State Configuration, DSC)

### <a name="unsupported-characters"></a>Szenario: Eine Konfiguration mit speziellen Zeichen kann nicht aus dem Portal gelöscht werden

#### <a name="issue"></a>Problem

Beim Versuch, eine DSC-Konfiguration aus dem Portal zu löschen, wird der folgende Fehler angezeigt:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Ursache

Der Fehler ist ein vorübergehendes Problem, das gelöst werden soll.

#### <a name="resolution"></a>Lösung

* Verwenden Sie das Azure-Cmdlet „Remove-AzAutomationDscConfiguration“, um die Konfiguration zu löschen.
* Die Dokumentation für dieses Cmdlet wurde noch nicht aktualisiert.  Nutzen Sie bis dahin bitte die Dokumentation für das AzureRM-Modul.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Szenario: Fehler beim Registrieren des DSC-Agents

#### <a name="issue"></a>Problem

Bei der Ausführung von `Set-DscLocalConfigurationManager` oder eines anderen DSC-Cmdlets tritt der folgende Fehler auf:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

#### <a name="cause"></a>Ursache

Dieser Fehler wird normalerweise durch eine Firewall, durch einen Computer hinter einem Proxyserver oder durch andere Netzwerkfehler verursacht.

#### <a name="resolution"></a>Lösung

Stellen Sie sicher, dass Ihr Computer Zugriff auf die richtigen Endpunkte für Azure Automation DSC hat, und wiederholen Sie den Vorgang. Eine Liste der erforderlichen Ports und Adressen finden Sie unter [Übersicht über Azure Automation State Configuration](../automation-dsc-overview.md#network-planning).

### <a name="failed-not-found"></a>Szenario: Knoten hat den Fehlerstatus „Nicht gefunden“

#### <a name="issue"></a>Problem

Der Knoten hat den Status **Fehler** mit der folgenden Meldung zurückgegeben:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Ursache

Dieser Fehler tritt normalerweise auf, wenn der Knoten einem Konfigurationsnamen (z.B. ABC) anstatt einem Knotenkonfigurationsnamen (z.B. ABC.WebServer) zugewiesen ist.

#### <a name="resolution"></a>Lösung

* Stellen Sie sicher, dass Sie den Knoten mit dem „Knotenkonfigurationsnamen“ und nicht mit dem „Konfigurationsnamen“ zuweisen.
* Einem Knoten können Sie über das Azure-Portal oder mit einem PowerShell-Cmdlet eine Knotenkonfiguration zuweisen.

  * Um über das Azure-Portal einem Knoten eine Knotenkonfiguration zuzuweisen, öffnen Sie die Seite **DSC-Knoten**, wählen Sie dann einen Knoten aus, und klicken Sie auf die Schaltfläche **Knotenkonfiguration zuweisen**.  
  * Um einem Knoten mit einem PowerShell-Cmdlet eine Knotenkonfiguration zuzuweisen, verwenden Sie das Cmdlet **Set-AzureRmAutomationDscNode**.

### <a name="no-mof-files"></a>Szenario: Bei der Kompilierung einer Konfiguration wurden keine Knotenkonfigurationen (MOF-Dateien) erstellt

#### <a name="issue"></a>Problem

Ihr DSC-Kompilierungsauftrag wird mit folgendem Fehler abgebrochen:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Ursache

Wenn der Ausdruck nach dem Schlüsselwort **Node** in der DSC-Konfiguration mit `$null` ausgewertet wird, werden keine Knotenkonfigurationen erstellt.

#### <a name="resolution"></a>Lösung

Sie können dieses Problem mit jeder der folgenden Lösungen beheben:

* Stellen Sie sicher, dass der Ausdruck neben dem Schlüsselwort **Node** in der Konfigurationsdefinition nicht mit „$null“ ausgewertet wird.
* Wenn Sie bei der Kompilierung der Konfiguration ConfigurationData übergeben, stellen Sie sicher, dass Sie die erwarteten Werte übergeben, die für die Konfiguration aus [ConfigurationData](../automation-dsc-compile.md#configurationdata)erforderlich sind.

### <a name="dsc-in-progress"></a>Szenario: Der DSC-Knotenbericht bleibt mit dem Status „In Bearbeitung“ hängen

#### <a name="issue"></a>Problem

Der DSC-Agent gibt Folgendes aus:

```error
No instance found with given property values
```

#### <a name="cause"></a>Ursache

Sie haben Ihre WMF-Version aktualisiert und WMI beschädigt.

#### <a name="resolution"></a>Lösung

Befolgen Sie zum Beheben des Problems die Anweisungen im Artikel [Bekannte Probleme und Einschränkungen bei DSC](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc).

### <a name="issue-using-credential"></a>Szenario: In einer DSC-Konfiguration können keine Anmeldeinformationen verwendet werden

#### <a name="issue"></a>Problem

Ihr DSC-Kompilierungsauftrag wurde mit folgendem Fehler abgebrochen:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Ursache

Sie haben Anmeldeinformationen in einer Konfiguration verwendet, aber keine ordnungsgemäßen **ConfigurationData** angegeben, über die **PSDscAllowPlainTextPassword** für jede Knotenkonfiguration auf TRUE festgelegt wird.

#### <a name="resolution"></a>Lösung

* Stellen Sie sicher, dass Sie die ordnungsgemäßen **ConfigurationData** übergeben, über die **PSDscAllowPlainTextPassword** für jede Knotenkonfiguration auf „true“ festgelegt wird. Weitere Informationen finden Sie unter [Assets in Azure Automation DSC](../automation-dsc-compile.md#assets).

### <a name="failure-processing-extension"></a>Szenario: Onboarding über DSC-Erweiterung, Fehler beim Verarbeiten der Erweiterung

#### <a name="issue"></a>Problem

Beim Onboarding mithilfe der DSC-Erweiterung tritt der folgende Fehler auf:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Ursache

Dieser Fehler tritt in der Regel auf, wenn dem Knoten ein Knotenkonfigurationsname zugewiesen wurde, der im Dienst nicht vorhanden ist.

#### <a name="resolution"></a>Lösung

* Stellen Sie sicher, dass Sie dem Knoten einen Knotenkonfigurationsnamen zuweisen, der genau mit dem im Dienst vorhandenen Namen übereinstimmt.
* Sie können festlegen, dass kein Knotenkonfigurationsname angegeben werden soll. In dem Fall erfolgt ein Onboarding des Knotens, aber keine Zuweisung einer Knotenkonfiguration.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Mit [@AzureSupport](https://twitter.com/azuresupport) verbinden – das offizielle Microsoft Azure-Konto zur Verbesserung der Benutzerfreundlichkeit durch Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.
* Wenn Sie weitere Hilfe benötigen, können Sie einen Azure-Supportvorgang anlegen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten**aus.
