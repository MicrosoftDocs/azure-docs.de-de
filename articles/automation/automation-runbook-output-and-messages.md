---
title: Runbookausgabe und -meldungen in Azure Automation
description: Beschreibt, wie Ausgaben und Fehlermeldungen von Runbooks in Azure Automation erstellt und abgerufen werden.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1cbf91af4e91f41fff30a7edfa869d07a21b881e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61226962"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Runbookausgabe und -meldungen in Azure Automation
Die meisten Azure Automation-Runbooks besitzen eine Form von Ausgabe. Diese Ausgabe ist möglicherweise eine Fehlermeldung für den Benutzer oder ein komplexes Objekt, das Sie mit einem anderen Runbook verwenden möchten. Windows PowerShell bietet [mehrere Datenströme](/powershell/module/microsoft.powershell.core/about/about_redirection) zum Senden der Ausgabe eines Skripts oder Workflows. Azure Automation verwendet jeden dieser Datenströme anders. Sie sollten beim Erstellen eines Runbooks die bewährten Methoden für die Verwendung der einzelnen Datenströme befolgen.

Die folgende Tabelle enthält eine kurze Beschreibung der einzelnen Datenströme und erläutert ihr Verhalten im Azure-Portal für veröffentlichte Runbooks und beim [Testen von Runbooks](automation-testing-runbook.md). Ausführlichere Informationen zu den verschiedenen Datenströmen finden Sie in nachfolgenden Abschnitten.

| Datenstrom | BESCHREIBUNG | Veröffentlicht | Test |
|:--- |:--- |:--- |:--- |
| Output |Objekte, die von anderen Runbooks genutzt werden. |Wird in den Auftragsverlauf geschrieben. |Wird im Testausgabebereich angezeigt. |
| Warnung |Für den Benutzer vorgesehene Warnmeldung. |Wird in den Auftragsverlauf geschrieben. |Wird im Testausgabebereich angezeigt. |
| Error |Für den Benutzer vorgesehene Fehlermeldung. Anders als bei einer Ausnahme wird das Runbook nach einer Fehlermeldung standardmäßig fortgesetzt. |Wird in den Auftragsverlauf geschrieben. |Wird im Testausgabebereich angezeigt. |
| Ausführlich |Meldungen mit allgemeinen Informationen oder Debuginformationen. |Wird nur in den Auftragsverlauf geschrieben, wenn die ausführliche Protokollierung für das Runbook aktiviert ist. |Wird nur im Testausgabebereich angezeigt, wenn „$VerbosePreference“ im Runbook auf „Continue“ festgelegt ist. |
| Status |Datensätze, die automatisch vor und nach jeder Aktivität im Runbook generiert werden. Das Runbook sollte nicht versuchen, eigene Statusdatensätze zu erstellen, da diese für einen interaktiven Benutzer vorgesehen sind. |Wird nur in den Auftragsverlauf geschrieben, wenn die Statusprotokollierung für das Runbook aktiviert ist. |Wird nicht im Testausgabebereich angezeigt. |
| Debuggen |Für einen interaktiven Benutzer vorgesehene Meldungen. Sollte nicht in Runbooks verwendet werden. |Wird nicht in den Auftragsverlauf geschrieben. |Wird nicht im Testausgabebereich angezeigt. |

## <a name="output-stream"></a>Ausgabedatenstrom
Der Ausgabedatenstrom dient zur Ausgabe von Objekten, die bei korrekter Ausführung von einem Skript oder Workflow erstellt werden. In Azure Automation wird dieser Datenstrom hauptsächlich für Objekte verwendet, die von [übergeordneten Runbooks, die das aktuelle Runbook aufrufen](automation-child-runbooks.md), genutzt werden sollen. Wenn Sie ein Runbook über ein übergeordnetes Runbook [inline aufrufen](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution), werden Daten aus dem Ausgabedatenstrom an das übergeordnete Runbook zurückgegeben. Verwenden Sie den Ausgabedatenstrom nur dann zum Übermitteln allgemeiner Informationen an den Benutzer, wenn Sie wissen, dass das Runbook nie von einem anderen Runbook aufgerufen wird. In der Regel empfiehlt es sich jedoch, den [ausführlichen Datenstrom](#verbose-stream) zu verwenden, um allgemeine Informationen für den Benutzer bereitzustellen.

Sie können Daten in den Ausgabedatenstrom schreiben, indem Sie [Write-Output](https://technet.microsoft.com/library/hh849921.aspx) verwenden oder das Objekt im Runbook in einer eigenen Zeile platzieren.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Ausgabe einer Funktion
Wenn Sie in einer in Ihrem Runbook enthaltenen Funktion in den Ausgabedatenstrom schreiben, wird die Ausgabe an das Runbook zurückgegeben. Weist das Runbook diese Ausgabe einer Variablen zu, wird sie nicht in den Ausgabedatenstrom geschrieben. Wenn Sie innerhalb der Funktion in andere Datenströme schreiben, wird die Ausgabe in den entsprechenden Datenstrom für das Runbook geschrieben.

Sehen Sie sich folgendes Beispielrunbook an:

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Der Ausgabedatenstrom für den Runbookauftrag würde wie folgt aussehen:

```output
Output inside of function
Output outside of function
```

Der ausführliche Datenstrom für den Runbookauftrag würde wie folgt aussehen:

```output
Verbose outside of function
Verbose inside of function
```

Nach dem Veröffentlichen und vor dem Starten des Runbooks müssen Sie die ausführliche Protokollierung in den Runbookeinstellungen aktivieren, um die ausführliche Datenstromausgabe abzurufen.

### <a name="declaring-output-data-type"></a>Deklarieren des Ausgabedatentyps
Ein Workflow kann den Datentyp seiner Ausgabe mit dem [OutputType-Attribut](https://technet.microsoft.com/library/hh847785.aspx)angeben. Dieses Attribut hat zur Laufzeit keinerlei Auswirkung, gibt dem Runbookautor zur Entwurfszeit aber Auskunft über die erwartete Ausgabe des Runbooks. Mit der Weiterentwicklung des Toolsets für Runbooks wird die Deklaration der Ausgabedatentypen zur Entwurfszeit zunehmend an Bedeutung gewinnen. Daher empfiehlt es sich, diese Deklaration in alle von Ihnen erstellten Runbooks einzuschließen.

Hier sehen Sie einige Beispiele für Ausgabetypen:

* System.String
* System. Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

Das folgende Beispielrunbook gibt ein Zeichenfolgenobjekt aus und enthält eine Deklaration des Ausgabetyps. Wenn Ihr Runbook ein Array eines bestimmten Typs ausgibt, sollten Sie trotzdem den Typ angeben und kein Array des Typs.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

Um einen Ausgabetyp in grafischen oder grafischen PowerShell-Workflow-Runbooks zu deklarieren, wählen Sie die Menüoption **Ein- und Ausgabe** und geben Sie den Namen des Ausgabetyps ein. Es wird empfohlen, den vollständigen .NET-Klassennamen zu verwenden, um diesen bei Verweisen von einem übergeordneten Runbook einfach identifizieren zu können. So sind alle Eigenschaften dieser Klasse im Datenbus im Runbook verfügbar, und Sie profitieren von einem hohen Maß an Flexibilität, wenn Sie die Eigenschaften für bedingte Logik, Protokollierung und Verweise als Werte für andere Aktivitäten im Runbook verwenden.<br> ![Option für Runbookeingabe und -ausgabe](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

Im folgenden Beispiel demonstrieren zwei grafische Runbooks diese Funktion. Wenn Sie das modulare Entwurfsmodell für Runbooks verwenden, verfügen Sie über ein Runbook, das als *Vorlage für Authentifizierungsrunbooks* fungiert, mit denen die Authentifizierung bei Azure unter Verwendung des ausführenden Kontos verwaltet wird. Unser zweites Runbook, das in der Regel die grundlegende Logik zum Automatisieren eines bestimmten Szenarios ausführen würde, führt in diesem Fall die *Authentifizierungs-Runbook-Vorlage* aus und zeigt die Ergebnisse in Ihrem **Test** -Ausgabebereich an. Unter normalen Umständen würde dieses Runbook eine Aktion in einer Ressource ausführen und dabei die Ausgabe des untergeordneten Runbooks nutzen.

Hier sehen Sie die grundlegende Logik des Runbooks **AuthenticateTo-Azure**.<br> ![Beispiel für Authentifizierung einer Runbook-Vorlage](media/automation-runbook-output-and-messages/runbook-authentication-template.png).  

Es enthält den Ausgabetyp *Microsoft.Azure.Commands.Profile.Models.PSAzureContext*, der die Eigenschaften des Authentifizierungsprofils zurückgibt.<br> ![Beispiel für Runbook-Ausgabetyp](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

Trotz dieses wenig komplexen Runbooks muss ein Konfigurationselement aufgerufen werden. Die letzte Aktivität führt das Cmdlet **Write-Output** aus und schreibt die Profildaten auf eine Variable „$_“ mit einem PowerShell-Ausdruck für den Parameter **Inputobject**, der für das Cmdlet erforderlich ist.  

Das zweite Runbook in diesem Beispiel namens *Test-ChildOutputType* verfügt nur über zwei Aktivitäten.<br> ![Beispiel für Runbook vom Typ „untergeordnete Ausgabe“](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

Die erste Aktivität ruft das Runbook **AuthenticateTo-Azure** auf und die zweite Aktivität führt das Cmdlet **Write-Verbose** mit der **Datenquelle** der **Aktivitätsausgabe** aus. Der Wert für **Feldpfad** lautet **Context.Subscription.SubscriptionName**, was die Kontextausgabe des **AuthenticateTo-Azure**-Runbooks festlegt.<br> ![Write-Verbose-Cmdlet-Parameter-Datenquelle](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

Die entstandene Ausgabe ist der Name des Abonnements.<br> ![Test-ChildOutputType- Runbookergebnisse](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

Ein Hinweis über das Verhalten des Steuerelements „Ausgabetyp“: Wenn Sie einen Wert im Feld „Ausgabetyp“ im Blatt mit den Eigenschaften für Ein- und Ausgabe eingeben, müssen Sie nach dem Eingeben auf die Fläche außerhalb des Steuerelements klicken, damit die Eingabe vom Steuerelement erkannt werden kann.  

## <a name="message-streams"></a>Nachrichtendatenströme
Im Gegensatz zum Ausgabedatenstrom dienen Nachrichtendatenströme zum Übermitteln von Informationen an den Benutzer. Es gibt mehrere Nachrichtendatenströme für verschiedene Arten von Informationen, und jeder dieser Nachrichtendatenströme wird von Azure Automation anders behandelt.

### <a name="warning-and-error-streams"></a>Warnungs- und Fehlerdatenströme
Die Warnungs- und Fehlerdatenströme dienen zum Protokollieren von Problemen, die in einem Runbook auftreten. Sie werden in den Auftragsverlauf geschrieben, wenn ein Runbook ausgeführt wird, und im Testausgabebereich im Azure-Portal angezeigt, wenn ein Runbook getestet wird. Standardmäßig wird das Runbook nach einer Warnung oder einem Fehler fortgesetzt. Sie können angeben, dass das Runbook bei einer Warnung oder einem Fehler angehalten werden soll, indem Sie vor dem Erstellen der Meldung eine [Einstellungsvariable](#preference-variables) im Runbook festlegen. Soll ein Runbook beim Auftreten eines Fehlers wie bei einer Ausnahme angehalten werden, legen Sie z.B. **$ErrorActionPreference** auf „Stop“ fest.

Erstellen Sie mit dem Cmdlet [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) bzw. [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) eine Warn- oder Fehlermeldung. Aktivitäten können ebenfalls in diese Datenströme schreiben.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Ausführlicher Datenstrom
Der ausführliche Nachrichtendatenstrom dient für allgemeine Informationen zu Runbookvorgängen. Da der [Debugdatenstrom](#debug-stream) in einem Runbook nicht verfügbar ist, sollten ausführliche Meldungen für Debuginformationen verwendet werden. Standardmäßig werden ausführliche Meldungen von veröffentlichten Runbooks nicht im Auftragsverlauf gespeichert. Um ausführliche Meldungen zu speichern, konfigurieren Sie für veröffentlichte Runbooks im Azure-Portal auf der Registerkarte „Konfigurieren“ des Runbooks die Einstellung „Ausführliche Datensätze protokollieren“. In den meisten Fällen sollte aus Leistungsgründen die Standardeinstellung (keine Protokollierung ausführlicher Datensätze) für Runbooks beibehalten werden. Aktivieren Sie diese Option nur zum Beheben oder Debuggen eines Runbooks.

Beim [Testen eines Runbooks](automation-testing-runbook.md)werden auch dann keine ausführlichen Meldungen angezeigt, wenn das Runbook zum Protokollieren ausführlicher Datensätze konfiguriert ist. Um beim [Testen eines Runbooks](automation-testing-runbook.md)ausführliche Meldungen anzuzeigen, müssen Sie die $VerbosePreference-Variable auf „Continue“ festlegen. Ist diese Variable festgelegt, werden im Testausgabebereich des Azure-Portals ausführliche Meldungen angezeigt.

Erstellen Sie eine ausführliche Meldung mit dem Cmdlet [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) .

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Debugdatenstrom
Der Debugdatenstrom dient zur Verwendung mit einem interaktiven Benutzer und sollte nicht in Runbooks benutzt werden.

## <a name="progress-records"></a>Statusdatensätze
Wenn Sie ein Runbook zum Protokollieren von Statusdatensätzen konfigurieren (auf der Registerkarte „Konfigurieren“ des Runbooks im Azure-Portal), wird vor und nach der Ausführung jeder Aktivität ein Datensatz in den Auftragsverlauf geschrieben. In den meisten Fällen sollte zum Maximieren der Leistung die Standardeinstellung (keine Protokollierung von Statusdatensätzen) für Runbooks beibehalten werden. Aktivieren Sie diese Option nur zum Beheben oder Debuggen eines Runbooks. Beim Testen eines Runbooks werden auch dann keine Statusmeldungen angezeigt, wenn das Runbook zum Protokollieren von Statusdatensätzen konfiguriert ist.

Das Cmdlet [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) ist in einem Runbook ungültig, da es zur Verwendung mit einem interaktiven Benutzer vorgesehen ist.

## <a name="preference-variables"></a>Einstellungsvariablen
Windows PowerShell ermittelt anhand von [Einstellungsvariablen](https://technet.microsoft.com/library/hh847796.aspx) , wie auf Daten reagiert werden soll, die an verschiedene Ausgabedatenströme gesendet werden. Sie können diese Variablen in einem Runbook festlegen, um zu steuern, wie es auf die in unterschiedliche Datenströme gesendeten Daten reagiert.

In der folgenden Tabelle sind die Einstellungsvariablen, die in Runbooks verwendet werden können, mit ihren gültigen Werten und Standardwerten aufgeführt. Diese Tabelle enthält nur die Werte, die in einem Runbook gültig sind. Bei der Verwendung in Windows PowerShell außerhalb von Azure Automation sind weitere Werte für die Einstellungsvariablen zulässig.

| Variable | Standardwert | Gültige Werte |
|:--- |:--- |:--- |
| WarningPreference |Weiter |Beenden<br>Weiter<br>SilentlyContinue |
| ErrorActionPreference |Weiter |Beenden<br>Weiter<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Beenden<br>Weiter<br>SilentlyContinue |

In der folgenden Tabelle wird das Verhalten für die in Runbooks zulässigen Einstellungsvariablenwerte beschrieben.

| Wert | Verhalten |
|:--- |:--- |
| Weiter |Protokolliert die Meldung und setzt die Ausführung des Runbooks fort. |
| SilentlyContinue |Setzt die Ausführung des Runbooks ohne Protokollierung der Meldung fort. Dieser Wert hat den Effekt, dass die Meldung ignoriert wird. |
| Beenden |Protokolliert die Meldung und hält das Runbook an. |

## <a name="runbook-output"></a>Abrufen der Runbookausgabe und -meldungen
### <a name="azure-portal"></a>Azure-Portal
Sie können die Details eines Runbookauftrags im Azure-Portal auf der Registerkarte „Aufträge“ eines Runbooks anzeigen. Die Zusammenfassung des Auftrags zeigt die Eingabeparameter und den [Ausgabestream](#output-stream) sowie allgemeine Informationen zum Auftrag und alle aufgetretenen Ausnahmen. Der Verlauf enthält Meldungen aus dem [Ausgabestream](#output-stream), den [Warnungs- und Fehlerdatenströmen](#warning-and-error-streams) sowie dem [ausführlichen Datenstrom](#verbose-stream) und den [Statusdatensätzen](#progress-records), wenn das Runbook zum Protokollieren von ausführlichen Meldungen und Statusdatensätzen konfiguriert ist.

### <a name="windows-powershell"></a>Windows PowerShell
In Windows PowerShell können Sie Ausgaben und Meldungen mithilfe des Cmdlets [Get-AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) aus einem Runbook abrufen. Dieses Cmdlet erfordert die ID des Auftrags und verfügt über einen Stream-Parameter, mit dem der zurückzugebende Datenstrom angegeben wird. Wenn Sie **Any** angeben, werde alle Datenströme für den Auftrag zurückgegeben.

Im folgenden Beispiel wird ein Beispielrunbook gestartet und anschließend auf seinen Abschluss gewartet. Danach wird der Ausgabedatenstrom aus dem Auftrag abgerufen.

```powershell
$job = Start-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzureRmAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzureRmAutomationJobOutput to Get-AzureRmAutomationJobOutputRecord
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzureRmAutomationJobOutputRecord
``` 

### <a name="graphical-authoring"></a>Grafische Inhaltserstellung
Für grafische Runbooks steht eine zusätzliche Protokollierung in Form einer Ablaufverfolgung auf Aktivitätsebene zur Verfügung. Es gibt zwei Stufen der Ablaufverfolgung: „Standard“ und „Ausführlich“. Bei einer Ablaufverfolgung der Stufe „Standard“ können Sie die Start- und Endzeit jeder Aktivität im Runbook sowie Informationen zu Aktivitätswiederholungen sehen. Beispiele sind die Anzahl von Versuchen und die Startzeit der Aktivität. Bei einer Ablaufverfolgung der Stufe „Ausführlich“ erhalten Sie alle Informationen der Stufe „Standard“ plus Ein- und Ausgabedaten für jede Aktivität. Zurzeit werden die Ablaufverfolgungsdatensätze mithilfe des ausführlichen Datenstroms geschrieben. Daher müssen Sie beim Aktivieren der Ablaufverfolgung die ausführliche Protokollierung aktivieren. Bei grafischen Runbooks mit aktivierter Ablaufverfolgung ist es nicht erforderlich, Statusdatensätze zu protokollieren. Einfache Ablaufverfolgung dient demselben Zweck und ist informativer.

![Ansicht der Auftragsdatenströme bei der grafischen Inhaltserstellung](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Im Screenshot oben sehen Sie, dass bei aktivierter ausführlicher Protokollierung und Ablaufverfolgung für grafische Runbooks in der Ansicht der Datenströme für die Produktionsaufträge deutlich mehr Informationen zur Verfügung stehen. Diese zusätzlichen Informationen können für die Behandlung von Produktionsproblemen mit einem Runbook von wesentlicher Bedeutung sein. Deshalb sollten sie nur zu diesem Zweck und nicht als allgemeine Maßnahme aktiviert werden. Die Anzahl von Ablaufverfolgungsdatensätzen kann erheblich sein. Bei der Ablaufverfolgung für grafische Runbooks können Sie zwei bis vier Datensätze pro Aktivität erhalten – abhängig davon, ob Sie die Ablaufverfolgung der Stufe „Standard“ oder „Ausführlich“ konfiguriert haben. Sofern Sie diese Informationen nicht zur Nachverfolgung des Fortschritts eines Runbooks im Rahmen der Problembehandlung benötigen, sollten Sie die Ablaufverfolgung deaktiviert lassen.

**Führen Sie die folgenden Schritte aus, um die Ablaufverfolgung auf Aktivitätsebene zu aktivieren:**

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto.
2. Klicken Sie unter **Prozessautomatisierung** auf **Runbooks**, um die Liste mit den Runbooks zu öffnen.
3. Klicken Sie auf der Seite „Runbooks“ auf ein grafisches Runbook in der Liste, um dieses auszuwählen.
4. Klicken Sie unter **Einstellungen** auf **Protokollierung und Ablaufverfolgung**.
5. Klicken Sie auf der Seite „Protokollierung und Ablaufverfolgung“ unter „Ausführliche Datensätze protokollieren“ auf **Ein**, um die ausführliche Protokollierung zu aktivieren. Ändern Sie unterhalb von „Ablaufverfolgung auf Aktivitätsebene“ die Stufe der Ablaufverfolgung Ihren Anforderungen entsprechend zu **Standard** oder **Ausführlich**.<br>
   
   ![Seite „Protokollierung und Ablaufverfolgung“ bei der grafischen Inhaltserstellung](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure Monitor-Protokolle
Automation kann Runbookauftragsstatus und Auftragsdatenströme an Ihren Log Analytics-Arbeitsbereich senden. Mit Azure Monitor-Protokolle können Sie Folgendes:

* Gewinnen Sie Einblicke in Ihre Automation-Aufträge 
* Lösen Sie basierend auf Ihrem Runbookauftragsstatus (beispielsweise „Fehler“ oder „Angehalten“) das Senden einer E-Mail oder einer Warnung aus. 
* Schreiben Sie erweiterte Abfragen für Ihre Auftragsdatenströme 
* Korrelieren Sie Aufträge über Automation-Konten hinweg 
* Visualisieren Sie Ihren Auftragsverlauf    

Weitere Informationen zum Konfigurieren der Integration in Azure Monitor-Protokolle, um Auftragsdaten zu sammeln, zu korrelieren und entsprechende Maßnahmen zu ergreifen, finden Sie unter [Weiterleiten von Auftragsstatus und Auftragsdatenströmen von Automation an Azure Monitor-Protokolle](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Ausführen von Runbooks, zum Überwachen von Runbookaufträgen sowie andere technische Details finden Sie unter [Verfolgen eines Runbookauftrags](automation-runbook-execution.md)
* Informationen zum Entwerfen und Verwenden von untergeordneten Runbooks finden Sie unter [Untergeordnete Runbooks in Azure Automation](automation-child-runbooks.md)


