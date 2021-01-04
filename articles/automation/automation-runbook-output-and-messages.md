---
title: Konfigurieren der Ausgabe und Meldungsdatenströme von Runbooks
description: In diesem Artikel werden Ausgabe- und Meldungsdatenströme in Azure Automation-Runbooks beschrieben, und es wird erläutert, wie Sie Logik zur Fehlerbehandlung implementieren.
services: automation
ms.subservice: process-automation
ms.date: 11/03/2020
ms.topic: conceptual
ms.openlocfilehash: beed3ec50d0c7990168ee75976c732796cdbe246
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324424"
---
# <a name="configure-runbook-output-and-message-streams"></a>Konfigurieren der Ausgabe und Meldungsdatenströme von Runbooks

Die meisten Azure Automation-Runbooks besitzen eine Form von Ausgabe. Diese Ausgabe ist möglicherweise eine Fehlermeldung für den Benutzer oder ein komplexes Objekt, das für die Verwendung mit einem anderen Runbook gedacht ist. Windows PowerShell bietet [mehrere Datenströme](/powershell/module/microsoft.powershell.core/about/about_redirection) zum Senden der Ausgabe eines Skripts oder Workflows. Azure Automation verwendet jeden dieser Datenströme anders. Sie sollten beim Erstellen eines Runbooks die bewährten Methoden für die Verwendung der Datenströme befolgen.

In der folgenden Tabelle werden die einzelnen Datenströme kurz beschrieben und ihr Verhalten im Azure-Portal für veröffentlichte Runbooks sowie beim [Testen eines Runbooks](./manage-runbooks.md) erläutert. Der Ausgabedatenstrom ist der Hauptdatenstrom, der für die Kommunikation zwischen Runbooks verwendet wird. Die anderen Datenströme sind als Nachrichtendatenströme klassifiziert und für die Übermittlung von Informationen an den Benutzer gedacht.

| STREAM | BESCHREIBUNG | Veröffentlicht | Test |
|:--- |:--- |:--- |:--- |
| Fehler |Für den Benutzer vorgesehene Fehlermeldung. Anders als bei einer Ausnahme wird das Runbook nach einer Fehlermeldung standardmäßig fortgesetzt. |Wird in den Auftragsverlauf geschrieben. |Wird im Testausgabebereich angezeigt. |
| Debuggen |Für einen interaktiven Benutzer vorgesehene Meldungen. Sollte nicht in Runbooks verwendet werden. |Wird nicht in den Auftragsverlauf geschrieben. |Wird nicht im Testausgabebereich angezeigt. |
| Output |Objekte, die von anderen Runbooks genutzt werden. |Wird in den Auftragsverlauf geschrieben. |Wird im Testausgabebereich angezeigt. |
| Status |Datensätze, die automatisch vor und nach jeder Aktivität im Runbook generiert werden. Das Runbook sollte nicht versuchen, eigene Statusdatensätze zu erstellen, da diese für einen interaktiven Benutzer vorgesehen sind. |Wird nur in den Auftragsverlauf geschrieben, wenn die Statusprotokollierung für das Runbook aktiviert ist. |Wird nicht im Testausgabebereich angezeigt. |
| Ausführlich |Meldungen mit allgemeinen Informationen oder Debuginformationen. |Wird nur in den Auftragsverlauf geschrieben, wenn die ausführliche Protokollierung für das Runbook aktiviert ist. |Wird nur im Testausgabebereich angezeigt, wenn die Variable `VerbosePreference` im Runbook auf „Continue“ festgelegt ist. |
| Warnung |Für den Benutzer vorgesehene Warnmeldung. |Wird in den Auftragsverlauf geschrieben. |Wird im Testausgabebereich angezeigt. |

## <a name="use-the-output-stream"></a>Verwenden des Ausgabedatenstroms

Der Ausgabedatenstrom wird zur Ausgabe von Objekten verwendet, die bei korrekter Ausführung von einem Skript oder Workflow erstellt werden. Azure Automation verwendet diesen Datenstrom hauptsächlich für Objekte, die von übergeordneten Runbooks, die das [aktuelle Runbook](automation-child-runbooks.md) aufrufen, genutzt werden sollen. Wenn ein übergeordnetes Runbook [ein Runbook inline aufruft](automation-child-runbooks.md#invoke-a-child-runbook-using-inline-execution), gibt das untergeordnete Runbook Daten aus dem Ausgabedatenstrom an das übergeordnete Runbook zurück.

Ihr Runbook verwendet den Ausgabedatenstrom nur dann zum Übermitteln allgemeiner Informationen an den Client, wenn es nie von einem anderen Runbook aufgerufen wird. In der Regel empfiehlt es sich jedoch als bewährte Methode, dass Ihre Runbooks den [ausführlichen Datenstrom](#write-output-to-verbose-stream) verwenden, um allgemeine Informationen für den Benutzer bereitzustellen.

Lassen Sie Ihr Runbook mithilfe von [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) Daten in den Ausgabedatenstrom schreiben. Alternativ können Sie das Objekt in eine eigene Zeile im Skript einfügen.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handle-output-from-a-function"></a>Verarbeiten der Ausgabe einer Funktion

Wenn eine Runbookfunktion in den Ausgabedatenstrom schreibt, wird die Ausgabe an das Runbook zurückgegeben. Weist das Runbook diese Ausgabe einer Variablen zu, wird die Ausgabe nicht in den Ausgabedatenstrom geschrieben. Wenn Sie innerhalb der Funktion in andere Datenströme schreiben, wird die Ausgabe in den entsprechenden Datenstrom für das Runbook geschrieben. Sehen Sie sich folgendes Beispielrunbook mit einem PowerShell-Workflow an.

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

Der Ausgabedatenstrom für den Runbookauftrag ist:

```output
Output inside of function
Output outside of function
```

Der ausführliche Datenstrom für den Runbookauftrag ist:

```output
Verbose outside of function
Verbose inside of function
```

Nach dem Veröffentlichen und vor dem Starten des Runbooks müssen Sie die ausführliche Protokollierung in den Runbookeinstellungen aktivieren, um die ausführliche Datenstromausgabe abzurufen.

### <a name="declare-output-data-type"></a>Deklarieren des Ausgabedatentyps

Nachfolgend finden Sie Beispiele für Ausgabedatentypen:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Deklarieren des Ausgabedatentyps in einem Workflow

Ein Workflow gibt den Datentyp seiner Ausgabe mit dem [OutputType-Attribut](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute) an. Dieses Attribut hat zur Laufzeit keinerlei Auswirkung, gibt Ihnen zur Entwurfszeit aber einen Hinweis auf die erwartete Ausgabe des Runbooks. Mit der Weiterentwicklung des Toolsets für Runbooks wird die Deklaration der Ausgabedatentypen zur Entwurfszeit zunehmend an Bedeutung gewinnen. Daher empfiehlt es sich, diese Deklaration in alle von Ihnen erstellten Runbooks einzuschließen.

Das folgende Beispielrunbook gibt ein Zeichenfolgenobjekt aus und enthält eine Deklaration des Ausgabetyps. Wenn Ihr Runbook ein Array eines bestimmten Typs ausgibt, sollten Sie trotzdem den Typ angeben und kein Array des Typs.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Deklarieren des Ausgabedatentyps in einem grafischen Runbook

Um einen Ausgabetyp in einem grafischen Runbook oder einem grafischen PowerShell-Workflow-Runbook zu deklarieren, können Sie die Menüoption **Ein- und Ausgabe** auswählen und Ausgabetyp eingeben. Es wird empfohlen, den vollständigen .NET-Klassennamen zu verwenden, um den Typ bei Verweisen von einem übergeordneten Runbook darauf einfach identifizieren zu können. Durch die Verwendung des vollständigen Namens werden alle Eigenschaften der Klasse dem Datenbus im Runbook verfügbar gemacht, und die Flexibilität steigt, wenn die Eigenschaften für bedingte Logik, Protokollierung und Verweise als Werte für andere Runbookaktivitäten verwendet werden.<br> ![Option für Runbookeingabe und -ausgabe](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Nachdem Sie im Bereich „Eingabe- und Ausgabeeigenschaften“ in das Feld **Ausgabetyp** einen Wert eingegeben haben, stellen Sie sicher, dass Sie außerhalb des Steuerelements klicken, damit dieses Ihre Eingabe erkennt.

Im folgenden Beispiel werden zwei grafische Runbooks gezeigt, um die Eingabe- und Ausgabefunktion zu demonstrieren. Wenn Sie das modulare Entwurfsmodell für Runbooks verwenden, verfügen Sie über ein Runbook als Authentifizierungsrunbook-Vorlage, das die Authentifizierung bei Azure unter Verwendung des ausführenden Kontos verwaltet. Das zweite Runbook, das in der Regel die Kernlogik zum Automatisieren eines bestimmten Szenarios ausführt, führt in diesem Fall die Authentifizierungsrunbook-Vorlage aus. Die Ergebnisse werden im Testausgabebereich angezeigt. Unter normalen Umständen würde dieses Runbook eine Aktion in einer Ressource ausführen und dabei die Ausgabe des untergeordneten Runbooks nutzen.

Hier sehen Sie die grundlegende Logik des Runbooks **AuthenticateTo-Azure**.<br> ![Beispiel für Authentifizierung einer Runbook-Vorlage](media/automation-runbook-output-and-messages/runbook-authentication-template.png).

Das Runbook enthält den Ausgabetyp `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`, der die Eigenschaften des Authentifizierungsprofils zurückgibt.<br> ![Beispiel für Runbook-Ausgabetyp](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Bei diesem eher unkomplizierten Runbook muss dennoch ein Konfigurationselement aufgerufen werden. Die letzte Aktivität führt das `Write-Output`-Cmdlet aus, um mithilfe eines PowerShell-Ausdrucks für den `Inputobject`-Parameter Profildaten in eine Variable zu schreiben. Dieser Parameter ist für `Write-Output` erforderlich.

Das zweite Runbook in diesem Beispiel namens **Test-ChildOutputType** definiert einfach nur zwei Aktivitäten.<br> ![Beispiel für Runbook vom Typ „untergeordnete Ausgabe“](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Die erste Aktivität ruft das **AuthenticateTo-Azure** -Runbook auf. Die zweite Aktivität führt das Cmdlet `Write-Verbose` mit der auf **Aktivitätsausgabe** festgelegten **Datenquelle** aus. Außerdem wird **Feldpfad** auf **Context.Subscription.SubscriptionName** festgelegt, die Kontextausgabe aus dem **AuthenticateTo-Azure** -Runbook.<br> ![Parameter „Datenquelle“ des Write-Verbose-Cmdlets](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Die entstandene Ausgabe ist der Name des Abonnements.<br> ![Test-ChildOutputType- Runbookergebnisse](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="working-with-message-streams"></a>Arbeiten mit Meldungsdatenströmen

Im Gegensatz zum Ausgabedatenstrom übermitteln Meldungsdatenströme Informationen an den Benutzer. Es gibt mehrere Nachrichtendatenströme für verschiedene Arten von Informationen, und jeder dieser Nachrichtendatenströme wird von Azure Automation anders behandelt.

### <a name="write-output-to-warning-and-error-streams"></a>Schreiben einer Ausgabe an Warnungs- und Fehlerdatenströme

Die Warnungs- und Fehlerdatenströme protokollieren Probleme, die in einem Runbook auftreten. Azure Automation schreibt diese Datenströme in den Auftragsverlauf, wenn ein Runbook ausgeführt wird. Automation nimmt die Datenströme in den Testausgabebereich im Azure-Portal auf, wenn ein Runbook getestet wird.

Standardmäßig wird die Ausführung eines Runbooks nach einer Warnung oder einem Fehler fortgesetzt. Sie können angeben, dass Ihr Runbook bei einer Warnung oder einem Fehler angehalten werden soll, indem Sie vor dem Erstellen der Meldung das Runbook eine [Einstellungsvariable](#work-with-preference-variables) festlegen lassen. Soll beispielsweise ein Runbook beim Auftreten eines Fehlers wie bei einer Ausnahme angehalten werden, legen Sie die Variable `ErrorActionPreference` auf „Stop“ fest.

Erstellen Sie mit dem Cmdlet [Write-Warning](/powershell/module/microsoft.powershell.utility/write-warning) bzw. [Write-Error](/powershell/module/microsoft.powershell.utility/write-error) eine Warn- oder Fehlermeldung. Aktivitäten können auch in die Warnungs- und Fehlerdatenströme schreiben.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="write-output-to-debug-stream"></a>Schreiben einer Ausgabe an den Debugdatenstrom

Azure Automation verwendet den Debugmeldungsdatenstrom für interaktive Benutzer. Standardmäßig erfasst Azure Automation keine Daten aus Debugdatenströmen, sondern nur Ausgabe-, Fehler- und Warnungsdaten sowie ausführliche Daten, wenn das Runbook für deren Erfassung konfiguriert ist.

Um Daten aus Debugdatenströmen zu erfassen, müssen Sie in Ihren Runbooks zwei Aktionen ausführen:

1. Legen Sie die Variable `$GLOBAL:DebugPreference="Continue"` fest, die PowerShell anweist, beim Auftreten einer Debugmeldung fortzufahren.  Der Teil **$GLOBAL:** weist PowerShell an, dass dies im globalen Bereich erfolgen soll, nicht in dem lokalen Bereich, in dem sich das Skript zum Zeitpunkt der Ausführung der Anweisung befindet.

1. Leiten Sie den nicht erfassten Debugdatenstrom in einen Datenstrom um, der erfasst wird, beispielsweise eine *Ausgabe*. Sie erreichen dies durch eine PowerShell-Umleitung für die auszuführende Anweisung. Weitere Informationen zur Umleitung in PowerShell finden Sie unter [Informationen zur Umleitung](/powershell/module/microsoft.powershell.core/about/about_redirection).

#### <a name="examples"></a>Beispiele

In diesem Beispiel ist das Runbook mit den Cmdlets `Write-Output` und `Write-Debug` sowie der Absicht konfiguriert, zwei verschiedene Datenströme auszugeben.

```powershell
Write-Output "This is an output message." 
Write-Debug "This is a debug message."
```

Wenn dieses Runbook unverändert ausgeführt wird, wird die folgende Ausgabe in den Ausgabebereich für den Runbookauftrag gestreamt:

```output
This is an output message.
```

In diesem Beispiel ist das Runbook ähnlich wie im vorherigen Beispiel konfiguriert, außer dass die Anweisung `$GLOBAL:DebugPreference="Continue"` mit `5>&1` am Ende der Anweisung `Write-Debug` hinzugefügt wurde.

```powershell
Write-Output "This is an output message." 
$GLOBAL:DebugPreference="Continue" 
Write-Debug "This is a debug message." 5>&1
```

Bei Ausführung dieses Runbooks wird die folgende Ausgabe in den Ausgabebereich für den Runbookauftrag gestreamt:

```output
This is an output message.
This is a debug message.
```

Dies liegt an Folgendem: Die Anweisung `$GLOBAL:DebugPreference="Continue"` informiert PowerShell darüber, dass Debugmeldungen angezeigt werden sollen, und die Ergänzung `5>&1` am Ende der Anweisung `Write-Debug` informiert PowerShell darüber, dass der Datenstrom 5 (Debuggen) in den Datenstrom 1 (Ausgabe) umgeleitet werden soll.

### <a name="write-output-to-verbose-stream"></a>Schreiben der Ausgabe in einen ausführlichen Datenstrom

Der ausführliche Nachrichtendatenstrom unterstützt allgemeine Informationen zu Runbookvorgängen. Da der Debugdatenstrom für ein Runbook nicht verfügbar ist, sollte Ihr Runbook ausführliche Meldungen für Debuginformationen verwenden.

Standardmäßig speichert der Auftragsverlauf aus Leistungsgründen keine ausführlichen Meldungen von veröffentlichten Runbooks. Um ausführliche Meldungen zu speichern, verwenden Sie die Registerkarte **Konfigurieren** im Azure-Portal mit der Einstellung **Ausführliche Datensätze protokollieren** , um Ihre veröffentlichten Runbooks für die Protokollierung von ausführlichen Meldungen zu konfigurieren. Aktivieren Sie diese Option nur zum Beheben oder Debuggen eines Runbooks. In den meisten Fällen sollte die Standardeinstellung, also keine Protokollierung ausführlicher Datensätze, für Runbooks beibehalten werden.

Beim [Testen eines Runbooks](./manage-runbooks.md)werden auch dann keine ausführlichen Meldungen angezeigt, wenn das Runbook zum Protokollieren ausführlicher Datensätze konfiguriert ist. Um beim [Testen eines Runbooks](./manage-runbooks.md) ausführliche Meldungen anzuzeigen, müssen Sie die Variable `VerbosePreference` auf „Continue“ festlegen. Ist diese Variable festgelegt, werden im Testausgabebereich des Azure-Portals ausführliche Meldungen angezeigt.

Der folgende Code erstellt eine ausführliche Meldung mit dem Cmdlet [Write-Verbose](/powershell/module/microsoft.powershell.utility/write-verbose).

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="handle-progress-records"></a>Verarbeiten von Statusdatensätzen

Mithilfe der Registerkarte **Konfigurieren** im Azure-Portal können Sie ein Runbook für die Protokollierung von Statusdatensätzen konfigurieren. Die Standardeinstellung ist, dass die Datensätze nicht protokolliert werden, um die Leistung zu maximieren. In den meisten Fällen sollten Sie die Standardeinstellung beibehalten. Aktivieren Sie diese Option nur zum Beheben oder Debuggen eines Runbooks.

Wenn Sie die Protokollierung von Statusdatensätzen aktivieren, schreibt ihr Runbook vor und nach der Ausführung jeder Aktivität einen Datensatz in den Auftragsverlauf. Beim Testen eines Runbooks werden auch dann keine Statusmeldungen angezeigt, wenn das Runbook zum Protokollieren von Statusdatensätzen konfiguriert ist.

>[!NOTE]
>Das Cmdlet [Write-Progress](/powershell/module/microsoft.powershell.utility/write-progress) ist in einem Runbook ungültig, da es zur Verwendung mit einem interaktiven Benutzer vorgesehen ist.

## <a name="work-with-preference-variables"></a>Verwenden von Einstellungsvariablen

Sie können bestimmte Windows PowerShell-[Einstellungsvariablen](/powershell/module/microsoft.powershell.core/about/about_preference_variables) in Ihren Runbooks festlegen, um die Antwort auf Daten zu kontrollieren, die an verschiedene Ausgabedatenströme gesendet werden. In der folgenden Tabelle sind die Einstellungsvariablen, die in Runbooks verwendet werden können, mit ihren Standardwerten und gültigen Werten aufgeführt. Bei der Verwendung in Windows PowerShell außerhalb von Azure Automation sind weitere Werte für die Einstellungsvariablen zulässig.

| Variable | Standardwert | Gültige Werte |
|:--- |:--- |:--- |
| `WarningPreference` |Continue |Beenden<br>Continue<br>SilentlyContinue |
| `ErrorActionPreference` |Continue |Beenden<br>Continue<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Beenden<br>Continue<br>SilentlyContinue |

In der nächsten Tabelle wird das Verhalten für die in Runbooks zulässigen Einstellungsvariablenwerte beschrieben.

| Wert | Verhalten |
|:--- |:--- |
| Continue |Protokolliert die Meldung und setzt die Ausführung des Runbooks fort. |
| SilentlyContinue |Setzt die Ausführung des Runbooks ohne Protokollierung der Meldung fort. Dieser Wert hat den Effekt, dass die Meldung ignoriert wird. |
| Beenden |Protokolliert die Meldung und hält das Runbook an. |

## <a name="retrieve-runbook-output-and-messages"></a><a name="runbook-output"></a>Abrufen der Runbookausgabe und -meldungen

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Abrufen von Runbookausgabe und -meldungen im Azure-Portal

Sie können die Details eines Runbookauftrags im Azure-Portal mithilfe der Registerkarte **Aufträge** für das jeweilige Runbook anzeigen. Die Auftragszusammenfassung zeigt die Eingabeparameter und den [Ausgabedatenstrom](#use-the-output-stream) sowie allgemeine Informationen zum Auftrag und alle aufgetretenen Ausnahmen an. Der Auftragsverlauf beinhaltet Meldungen aus den Ausgabe-, [Warnungs- und Fehlerdatenströmen](#write-output-to-warning-and-error-streams). Er enthält außerdem Meldungen aus dem [ausführlichen Datenstrom](#write-output-to-verbose-stream) und [Statusdatensätze](#handle-progress-records), wenn das Runbook zum Protokollieren von ausführlichen Meldungen und Statusdatensätzen konfiguriert ist.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Abrufen von Runbookausgabe und -meldungen in Windows PowerShell

In Windows PowerShell können Sie Ausgaben und Meldungen mithilfe des Cmdlets [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) aus einem Runbook abrufen. Dieses Cmdlet erfordert die ID des Auftrags und verfügt über einen Parameter namens `Stream`, mit dem der abzurufende Datenstrom angegeben wird. Sie können den Wert „Any“ für diesen Parameter angeben, um alle Datenströme für den Auftrag abzurufen.

Im folgenden Beispiel wird ein Beispielrunbook gestartet und anschließend auf seinen Abschluss gewartet. Sobald die Ausführung des Runbooks abgeschlossen ist, erfasst das Skript den Runbook-Ausgabedatenstrom aus dem Auftrag.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Abrufen von Runbookausgabe und -meldungen in grafischen Runbooks

Für grafische Runbooks steht eine zusätzliche Protokollierung der Ausgabe und Meldungen in Form einer Ablaufverfolgung auf Aktivitätsebene zur Verfügung. Es gibt zwei Stufen der Ablaufverfolgung: „Standard“ und „Ausführlich“. Eine Ablaufverfolgung der Stufe „Standard“ zeigt die Start- und Endzeit jeder Aktivität im Runbook sowie Informationen zu allen Aktivitätswiederholungen an. Beispiele sind die Anzahl von Versuchen und die Startzeit der Aktivität. Eine Ablaufverfolgung der Stufe „Ausführlich“ umfasst grundlegende Ablaufverfolgungsfunktionen, zuzüglich der Protokollierung von Ein- und Ausgabedaten für jede Aktivität. 

Derzeit schreibt die Ablaufverfolgung auf Aktivitätsebene Datensätze mithilfe des ausführlichen Datenstroms. Daher müssen Sie die ausführliche Protokollierung aktivieren, wenn Sie die Ablaufverfolgung aktivieren. Bei grafischen Runbooks mit aktivierter Ablaufverfolgung ist es nicht erforderlich, Statusdatensätze zu protokollieren. Einfache Ablaufverfolgung dient demselben Zweck und ist informativer.

![Ansicht der Auftragsdatenströme bei der grafischen Inhaltserstellung](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

In der Abbildung können Sie sehen, dass bei aktivierter ausführlicher Protokollierung und Ablaufverfolgung für grafische Runbooks in der Produktionsansicht der **Auftragsdatenströme** deutlich mehr Informationen zur Verfügung stehen. Diese zusätzlichen Informationen können für die Behandlung von Produktionsproblemen mit einem Runbook von wesentlicher Bedeutung sein. 

Sofern Sie diese Informationen also nicht zur Nachverfolgung des Fortschritts eines Runbooks im Rahmen der Problembehandlung benötigen, sollten Sie die Ablaufverfolgung generell deaktiviert lassen. Die Anzahl von Ablaufverfolgungsdatensätzen kann erheblich sein. Bei der Ablaufverfolgung für grafische Runbooks können Sie zwei bis vier Datensätze pro Aktivität erhalten – abhängig davon, ob Sie die Ablaufverfolgung der Stufe „Standard“ oder „Ausführlich“ konfiguriert haben.

**So aktivieren Sie die Ablaufverfolgung auf Aktivitätsebene**

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto.
2. Wählen Sie unter **Prozessautomatisierung** die Option **Runbooks** aus, um die Liste der Runbooks zu öffnen.
3. Wählen Sie auf der Seite „Runbooks“ ein grafisches Runbook in der Liste der Runbooks aus.
4. Klicken Sie unter **Einstellungen** auf **Protokollierung und Ablaufverfolgung**.
5. Klicken Sie auf der Seite „Protokollierung und Ablaufverfolgung“ unter **Ausführliche Datensätze protokollieren** auf **Ein** , um die ausführliche Protokollierung zu aktivieren.
6. Ändern Sie unterhalb von **Ablaufverfolgung auf Aktivitätsebene** die Stufe der Ablaufverfolgung gemäß Ihren Anforderungen in **Standard** oder **Ausführlich**.<br>

   ![Seite „Protokollierung und Ablaufverfolgung“ bei der grafischen Inhaltserstellung](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Abrufen von Runbookausgabe und -meldungen in Microsoft Azure Monitor-Protokollen

Azure Automation kann Runbookauftragsstatus und Auftragsdatenströme an Ihren Log Analytics-Arbeitsbereich senden. Azure Monitor unterstützt Protokolle, die Ihnen Folgendes ermöglichen:

* Gewinnen Sie Einblicke in Ihre Automation-Aufträge.
* Auslösen einer E-Mail oder einer Benachrichtigung, basierend auf Ihrem Runbookauftragsstatus, z. B. „Fehler“ oder „Angehalten“.
* Schreiben erweiterter Abfragen an Auftragsdatenströme.
* Korrelieren Sie Aufträge über Automation-Konten hinweg.
* Visualisieren des Auftragsverlaufs.

Weitere Informationen zum Konfigurieren der Integration in Azure Monitor-Protokolle, um Auftragsdaten zu sammeln, zu korrelieren und entsprechende Maßnahmen zu ergreifen, finden Sie unter [Weiterleiten von Auftragsstatus und Auftragsdatenströmen von Automation an Azure Monitor-Protokolle](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zur Arbeit mit Runbooks finden Sie unter [Verwalten eines Runbooks in Azure Automation](manage-runbooks.md).
* Wenn Sie mit PowerShell-Skripts noch nicht vertraut sind, finden Sie weitere Informationen in der Dokumentation zu [PowerShell](/powershell/scripting/overview).
* Eine Referenz zu den PowerShell-Cmdlets für Azure Automation finden Sie unter [Az.Automation](/powershell/module/az.automation).
