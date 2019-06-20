---
title: Ausführen von Runbooks in Azure Automation
description: Beschreibt ausführlich, wie ein Runbook in Azure Automation verarbeitet wird.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f851fd2857650dd00e365abf71ec5f0199db6d57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64711586"
---
# <a name="runbook-execution-in-azure-automation"></a>Ausführen von Runbooks in Azure Automation

Wenn Sie ein Runbook in Azure Automation starten, wird ein Auftrag erstellt. Ein Auftrag ist eine einzelne Ausführungsinstanz eines Runbooks. Für die Ausführung jedes Auftrags wird ein Azure Automation-Worker zugewiesen. Wenngleich Worker von vielen Azure-Konten gemeinsam genutzt werden, sind die Aufträge von verschiedenen Automation-Konten voneinander isoliert. Sie können nicht steuern, welcher Worker die Anforderung für Ihren Auftrag verarbeitet. Für ein einzelnes Runbook können viele Aufträge gleichzeitig ausgeführt werden. Die Ausführungsumgebung für Aufträge aus dem gleichen Automation-Konto kann wiederverwendet werden. Je mehr Aufträge Sie zur gleichen Zeit ausführen, desto häufiger können diese an dieselbe Sandbox weitergeleitet werden. Im selben Sandboxprozess ausgeführte Aufträge können sich gegenseitig beeinflussen. Ein Beispiel ist das Ausführen des Cmdlets `Disconnect-AzureRMAccount`. Durch Ausführen des Cmdlets wird jeder Runbook-Auftrag im gemeinsamen Sandboxprozess getrennt. Wenn Sie die Liste der Runbooks im Azure-Portal anzeigen, wird der Status aller Aufträge aufgelistet, die für jedes Runbook gestartet wurden. Sie können die Liste der Aufträge für jedes Runbook anzeigen, um den Status der einzelnen Aufträge nachzuverfolgen. Auftragsprotokolle werden bis zu 30 Tage lang gespeichert. Eine Beschreibung der verschiedenen Auftragsstatusangaben finden Sie unter [Auftragsstatuswerte](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Im folgenden Diagramm wird der Lebenszyklus eines Runbookauftrags für [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks), [grafische Runbooks](automation-runbook-types.md#graphical-runbooks) und [PowerShell-Workflowrunbooks](automation-runbook-types.md#powershell-workflow-runbooks).

![Auftragsstatus – PowerShell-Workflow](./media/automation-runbook-execution/job-statuses.png)

Ihre Aufträge können auf Ihre Azure-Ressourcen zugreifen, indem sie eine Verbindung mit Ihrem Azure-Abonnement herstellen. Sie besitzen nur Zugriff auf Ressourcen in Ihrem Rechenzentrum, wenn aus der öffentlichen Cloud auf diese Ressourcen zugegriffen werden kann.

## <a name="where-to-run-your-runbooks"></a>Ausführungsort Ihrer Runbooks

Runbooks in Azure Automation können entweder in einer Sandbox in Azure oder auf einem [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) ausgeführt werden. Eine Sandbox ist eine freigegebene Umgebung in Azure, die von mehreren Aufträgen verwendet werden kann. Aufträge, die die gleiche Sandbox verwenden, werden durch die Ressourceneinschränkungen der Sandbox gebunden. Hybrid Runbook Workers führen Runbooks direkt auf dem Computer, der die Rolle hostet, und mit Ressourcen in der Umgebung zur Verwaltung dieser lokalen Ressourcen aus. Runbooks werden in Azure Automation gespeichert und verwaltet und an einzelne oder mehrere zugewiesene Computer übermittelt. Die meisten Runbooks können problemlos in den Azure-Sandboxes ausgeführt werden. Es gibt bestimmte Szenarios, in denen das Auswählen eines Hybrid Runbooks über einer Azure-Sandbox zum Ausführen Ihres Runbooks ratsam sein könnte. In der folgenden Tabelle finden Sie eine Liste von Beispielszenarien:

|Aufgabe|Beste Wahl|Notizen|
|---|---|---|
|Integration in Azure-Ressourcen|Azure-Sandbox|In Azure gehostet ist die Authentifizierung einfacher. Wenn Sie einen Hybrid Runbook Worker auf einer Azure-VM verwenden, können Sie [verwaltete Identitäten für Azure-Ressourcen](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) verwenden.|
|Optimale Leistung zum Verwalten von Azure-Ressourcen|Azure-Sandbox|Skript wird in der gleichen Umgebung ausgeführt, wodurch die Latenz gesenkt wird|
|Betriebskosten minimieren|Azure-Sandbox|Kein Computemehraufwand, kein virtueller Computer erforderlich|
|Zeitintensives Skript|Hybrid Runbook Worker|Für Azure-Sandboxes bestehen [Einschränkungen in Bezug auf Ressourcen](../azure-subscription-service-limits.md#automation-limits)|
|Mit lokalen Diensten interagieren|Hybrid Runbook Worker|Kann direkten Zugriff auf den Hostcomputer haben|
|Software und ausführbare Dateien von Drittanbietern erforderlich|Hybrid Runbook Worker|Sie verwalten das Betriebssystem und können Software installieren|
|Datei oder Ordner mit einem Runbook überwachen|Hybrid Runbook Worker|[Watchertask](automation-watchers-tutorial.md) auf einem Hybrid Runbook Worker verwenden|
|Ressourcenintensives Skript|Hybrid Runbook Worker| Für Azure-Sandboxes bestehen [Einschränkungen in Bezug auf Ressourcen](../azure-subscription-service-limits.md#automation-limits)|
|Verwenden von Modulen mit spezifischen Anforderungen| Hybrid Runbook Worker|Hier einige Beispiele:</br> **WinSCP** – Abhängigkeit von „winscp.exe“ </br> **IISAdministration** – benötigt IIS zur Aktivierung|
|Modul installieren, das Installationsprogramm erfordert|Hybrid Runbook Worker|Module für Sandbox müssen kopiert werden können|
|Verwenden von Runbooks oder Modulen, die eine andere .NET Framework-Version als 4.7.2 erfordern|Hybrid Runbook Worker|Automation-Sandboxes haben .NET Framework 4.7.2, und es gibt keine Möglichkeit zum Upgraden|
|Skripts, für die eine Rechteerweiterung erforderlich ist|Hybrid Runbook Worker|Sandboxes lassen keine Rechteerweiterung zu. Verwenden Sie in einem solchen Fall einen Hybrid Runbook Worker. Dann können Sie die Benutzerkontensteuerung deaktivieren und `Invoke-Command` verwenden, wenn Sie den Befehl ausführen, für den eine Rechteerweiterung erforderlich ist.|
|Skripts, für die Zugriff auf WMI erforderlich ist|Hybrid Runbook Worker|Aufträge, die in Sandboxes in der Cloud ausgeführt werden, [haben keinen Zugriff auf WMI](#device-and-application-characteristics).|

## <a name="runbook-behavior"></a>Runbook-Verhalten

Runbooks werden auf der Grundlage der Logik ausgeführt, die in ihnen definiert ist. Wenn ein Runbook unterbrochen wird, startet das Runbook am Anfang neu. Dieses Verhalten setzt voraus, dass Runbooks so geschrieben werden, dass sie neu gestartet werden können, wenn vorübergehend Probleme aufgetreten sind.

PowerShell-Aufträge, die auf einem Runbook gestartet wurden, das in einer Azure-Sandbox ausgeführt wird, können möglicherweise nicht im vollständigen Sprachmodus ausgeführt werden. Weitere Informationen zu den Sprachmodi in PowerShell finden Sie unter [PowerShell language modes (PowerShell-Sprachmodi)](/powershell/module/microsoft.powershell.core/about/about_language_modes). Weitere Informationen zur Interaktion mit Aufträgen in Azure Automation finden Sie im Abschnitt [Abrufen des Auftragsstatus mithilfe von Windows PowerShell](#retrieving-job-status-using-powershell).

### <a name="creating-resources"></a>Erstellen von Ressourcen

Wenn Ihr Skript Ressourcen erstellt, sollten Sie überprüfen, ob die Ressource bereits vorhanden ist, bevor Sie versuchen, sie erneut zu erstellen. Ein grundlegendes Beispiel sehen Sie im Folgenden:

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependant-scripts"></a>Zeitabhängige Skripte

Dem Erstellen von Runbooks sollten sorgfältige Überlegungen vorausgehen. Wie bereits erwähnt, müssen Runbooks so erstellt werden, dass sie stabil sind und vorübergehenden Fehlern standhalten können, die möglicherweise dazu führen, dass ein Runbook neu startet oder bei ihm ein Fehler auftritt. Wenn bei einem Runbook ein Fehler auftritt, wird es neu gestartet. Wenn ein Runbook innerhalb einer Zeiteinschränkung normal ausgeführt wird, sollte Logik zum Überprüfen der Ausführungszeit im Runbook implementiert werden, um sicherzustellen, dass Vorgänge wie Start, Herunterfahren oder horizontales Hochskalieren nur zu bestimmten Zeiten ausgeführt werden.

> [!NOTE]
> Die lokale Zeit des Azure-Sandbox-Prozesses ist auf UTC-Zeit festgelegt. Berechnungen für Datum und Uhrzeit in Ihren Runbooks müssen dies berücksichtigen.

### <a name="tracking-progress"></a>Nachverfolgung des Verlaufs

Es hat sich bewährt, Runbooks in modularer Form zu erstellen. Dies bedeutet, die Logik des Runbooks so zu strukturieren, dass es ganz einfach wiederverwendet und neu gestartet werden kann. Die Nachverfolgung des Verlaufs in einem Runbook ist eine gute Möglichkeit, sicherzustellen, dass die Logik in einem Runbook ordnungsgemäß ausgeführt wird, falls Probleme aufgetreten sind. Einige Möglichkeiten, den Verlauf des Runbooks nachzuverfolgen, bietet die Verwendung externer Quellen wie Speicherkonten, einer Datenbank oder freigegebener Dateien. Durch externes Nachverfolgen des Status können Sie in Ihrem Runbook Logik erstellen, um zunächst den Status der letzten Aktion zu überprüfen, die das Runbook ausgeführt hat. Dann können Sie auf Grundlage der Ergebnissen bestimmte Aufgaben im Runbook entweder überspringen oder fortsetzen.

### <a name="prevent-concurrent-jobs"></a>Gleichzeitige Aufträge verhindern

Einige Runbooks verhalten sich möglicherweise seltsam, wenn sie gleichzeitig für mehrere Aufträge ausgeführt werden. In diesem Fall ist es wichtig, Logik zu implementieren, die überprüft, ob ein Runbook bereits gerade einen Auftrag ausführt. Im Folgenden sehen Sie anhand eines einfachen Beispiels, wie Sie dies durchführen können:

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>Verwenden mehrerer Abonnements

Beim Erstellen von Runbooks, die mit mehreren Abonnements arbeiten, muss Ihr Runbook das Cmdlet [Disable-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) verwenden, um sicherzustellen, dass der Authentifizierungskontext nicht aus einem anderem Runbook abgerufen wird, das in derselben Sandbox ausgeführt wird. Verwenden Sie anschließend den `-AzureRmContext`-Parameter für Ihre `AzureRM`-Cmdlets, und übergeben Sie diesem den richtigen Kontext.

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Behandeln von Ausnahmen

Beim Erstellen von Skripts ist es wichtig, Ausnahmen und mögliche Fehler währenddessen verarbeiten zu können. Im Folgenden werden einige Methoden zur Behandlung von Ausnahmen und zwischenzeitlich auftretender Fehler mit Ihren Runbooks aufgelistet:

#### <a name="erroractionpreference"></a>$ErrorActionPreference

Die Variable [$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) gibt an, wie PowerShell auf einen Fehler ohne Abbruch reagiert. Fehler mit Abbruch sind nicht von `$ErrorActionPreference` betroffen, da sie nicht permanent vorhanden sind. Wenn Sie `$ErrorActionPreference` verwenden, hält ein Fehler, der normalerweise permanent auftreten würde, z.B `PathNotFound` vom Cmdlet `Get-ChildItem`, das Runbook an. Im folgenden Beispiel wird `$ErrorActionPreference` veranschaulicht: Die letzte Zeile `Write-Output` wird nicht ausgeführt, da das Skript angehalten wurde.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Try Catch Finally

[Try Catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) wird in PowerShell-Skripts verwendet, damit Sie Fehler mit Abbruch besser behandeln können. Wenn Sie „Try Catch“ verwenden, können Sie bestimmte oder allgemeine Ausnahmen abfangen. Die Catch-Anweisung kann zur Fehlernachverfolgung oder -behandlung verwendet werden. Im folgenden Beispiel wird versucht, eine Datei herunterzuladen, die es nicht gibt. Die Ausnahme `System.Net.WebException` wird abgefangen. Wenn es eine weitere Ausnahme gibt, wird der letzte Wert zurückgegeben.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Throw

Mit [Throw](/powershell/module/microsoft.powershell.core/about/about_throw) können Sie einen Fehler mit Abbruch generieren. Das kann nützlich sein, wenn Sie Ihre eigene Logik in einem Runbook erstellen. Wenn eine bestimmte Bedingung erfüllt wird, die zum Anhalten des Skripts führen soll, können Sie das Skript mit `throw` anhalten. Im folgenden Beispiel zeigt der Computer durch `throw` an, dass ein Funktionsparameter erforderlich ist.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Verwenden von ausführbaren Dateien oder Aufrufen von Prozessen

In Azure-Sandboxes ausgeführte Runbooks unterstützen nicht das Aufrufen von Prozessen (z.B. einen EXE- oder Teilprozessaufruf). Dies liegt daran, dass Azure-Sandboxes in Containern ausgeführte freigegebene Prozesse sind, die möglicherweise nicht über Zugriff auf alle zugrunde liegenden APIs verfügen. Für Szenarien, in denen Sie Software von Drittanbietern oder das Aufrufen von Teilprozessen benötigen, sollten Sie das Runbook auf einem [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) ausführen.

### <a name="device-and-application-characteristics"></a>Geräte- und Anwendungsmerkmale

Runbookaufträge, die in Azure-Sandboxes ausgeführt werden, haben keinen Zugriff auf Geräte- oder Anwendungsmerkmale. Die WMI-API wird am häufigsten verwendet, um Leistungsmetriken unter Windows abzufragen. Zu diesen Metriken gehören Arbeitsspeiche- und CPU-Auslastung. Es ist jedoch unerheblich, welche API verwendet wird. Aufträge, die in der Cloud ausgeführt werden, haben keinen Zugriff auf die Microsoft-Implementierung des Web-Based Enterprise Management (WBEM), das auf Common Information Model (CIM) basiert. WBEM und CIM sind Branchenstandards für das Definieren von Geräte- und Anwendungsmerkmalen.

## <a name="job-statuses"></a>Auftragsstatuswerte

Die folgende Tabelle beschreibt die verschiedenen Status, die für einen Auftrag möglich sind. PowerShell unterscheidet zwei Arten von Fehlern: Fehler mit Abbruch und Fehler ohne Abbruch. Fehler mit Abbruch setzen den Runbookstatus auf **Fehler**, wenn sie auftreten. Fehler ohne Abbruch ermöglichen es dem Skript, auch nach ihrem Auftreten fortzufahren. Ein Beispiel für einen Fehler ohne Abbruch ist die Verwendung des Cmdlets `Get-ChildItem` mit einem Pfad, der nicht vorhanden ist. PowerShell erkennt, dass der Pfad nicht vorhanden ist, löst einen Fehler aus und fährt mit dem nächsten Ordner fort. Dieser Fehler würde den Runbookstatus nicht auf **Fehler** setzen und könnte als **Abgeschlossen** markiert werden. Um ein Runbook zu veranlassen, bei einem Fehler ohne Abbruch anzuhalten, können Sie `-ErrorAction Stop` für das Cmdlet verwenden.

| Status | BESCHREIBUNG |
|:--- |:--- |
| Abgeschlossen |Der Auftrag wurde erfolgreich abgeschlossen. |
| Fehler |Bei [grafischen und PowerShell-Workflow-Runbooks](automation-runbook-types.md)ist die Kompilierung des Runbooks fehlgeschlagen. Bei [PowerShell-Skript-Runbooks](automation-runbook-types.md) konnte das Runbook nicht gestartet werden, oder bei dem Auftrag ist eine Ausnahme aufgetreten. |
| Fehler, auf Ressourcen wird gewartet |Beim Auftrag ist ein Fehler aufgetreten, da der Auftrag dreimal den Grenzwert für die [gleichmäßige Verteilung](#fair-share) erreicht hat und jedes Mal vom gleichen Prüfpunkt oder vom Anfang des Runbooks gestartet wurde. |
| In Warteschlange |Der Auftrag wartet darauf, dass Ressourcen für einen Automation-Worker verfügbar werden, damit er gestartet werden kann. |
| Wird gestartet |Der Auftrag wurde einem Worker zugewiesen, und das System startet ihn. |
| Wird fortgesetzt |Das System setzt den Auftrag fort, nachdem er angehalten wurde. |
| Wird ausgeführt |Der Auftrag wird ausgeführt. |
| Wird ausgeführt, auf Ressourcen wird gewartet |Der Auftrag wurde entladen, da er den Grenzwert für die [gleichmäßige Verteilung](#fair-share) erreicht hat. Er wird in Kürze vom letzten Prüfpunkt wiederaufgenommen. |
| Beendet |Der Auftrag wurde vom Benutzer beendet, bevor er abgeschlossen wurde. |
| Wird beendet |Das System beendet den Auftrag. |
| Ausgesetzt |Der Auftrag wurde vom Benutzer, vom System oder von einem Befehl im Runbook angehalten. Wenn ein Runbook keinen Prüfpunkt aufweist, wird der Vorgang am Anfang des Runbooks gestartet. Wenn ein Prüfpunkt vorhanden ist, kann es erneut starten und den Vorgang bei seinem letzten Prüfpunkt fortsetzen. Das Runbook wird nur vom System angehalten, wenn eine Ausnahme auftritt. Standardmäßig ist ErrorActionPreference auf **Continue** festgelegt. Dies bedeutet, dass der Auftrag bei einem Fehler weiterhin ausgeführt wird. Wenn diese Einstellungsvariable auf **Stop** festgelegt wird, wird der Auftrag bei einem Fehler angehalten. Gilt nur für [grafische und PowerShell-Workflow-Runbooks](automation-runbook-types.md) . |
| Wird angehalten |Das System versucht, den Auftrag auf Anforderung des Benutzers anzuhalten. Das Runbook muss den nächsten Prüfpunkt erreichen, bevor es angehalten werden kann. Wenn der letzte Prüfpunkt bereits verstrichen ist, wird das Runbook abgeschlossen, bevor es angehalten werden kann. Gilt nur für [grafische und PowerShell-Workflow-Runbooks](automation-runbook-types.md) . |

## <a name="viewing-job-status-from-the-azure-portal"></a>Anzeigen des Auftragsstatus im Azure-Portal

Sie können einen zusammengefassten Status aller Runbookaufträge anzeigen oder im Azure-Portal weitere Details zu einem bestimmten Runbookauftrag aufführen. Sie können auch die Integration mit Ihrem Log Analytics-Arbeitsbereich konfigurieren, um den Status von Runbookaufträgen und Auftragsdatenströmen weiterzuleiten. Weitere Informationen zur Integration mit Azure Monitor-Protokollen finden Sie unter [Weiterleiten von Auftragsstatus und Auftragsdatenströmen von Automation an Log Analytics](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Automation-Runbookaufträge als Zusammenfassung

Rechts in Ihrem ausgewählten Automation-Konto sehen Sie auf der Kachel **Auftragsstatistik** die Zusammenfassung aller Runbookaufträge.

![Die Kachel „Auftragsstatistik“](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Auf dieser Kachel wird die Anzahl aller ausgeführten Jobs neben einer grafischen Darstellung des Auftragsstatus angezeigt.

Wenn Sie auf die Kachel klicken, wird die Seite **Aufträge** angezeigt, die eine zusammengefasste Liste aller ausgeführten Aufträge enthält. Auf dieser Seite werden Status, Start- und Abschlusszeiten angezeigt.

![Die Seite „Aufträge“ im Automation-Konto](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Sie können die Auftragsliste filtern, indem Sie die Option **Filteraufträge** auswählen. Aufträge lassen sich zum Durchsuchen nach bestimmten Runbooks, dem Auftragsstatus oder, über die Dropdownliste und den Uhrzeitbereich filtern.

![Status von Filteraufträgen](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Alternativ können Sie für ein bestimmtes Runbook eine Übersicht anzeigen, indem Sie in Ihrem Automation-Konto zuerst auf die Seite **Runbooks** das Runbook und dann die Kachel **Aufträge** auswählen. Diese Aktion stellt die Seite **Aufträge** dar. Wenn Sie dort auf einen Auftragsdatensatz klicken, werden Details und die Ausgabe angezeigt.

![Die Seite „Aufträge“ im Automation-Konto](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>API-Zusammenfassung

Sie können eine Liste aller Aufträge, die für ein bestimmtes Runbook erstellt wurden, sowie deren aktuellen Status anzeigen. Sie können diese Liste nach Auftragsstatus und dem Datumsbereich für die letzte Änderung des Auftrags filtern. Um ausführliche Informationen und die Auftragsausgabe anzuzeigen, klicken Sie auf den Namen eines Auftrags. Die Detailansicht des Auftrags enthält die Werte für die Runbookparameter, die für diesen Auftrag bereitgestellt wurden.

Zeigen Sie die Aufträge für ein Runbook mithilfe der folgenden Schritte an.

1. Wählen Sie im Azure-Portal die Option **Automation** aus, und wählen Sie anschließend den Namen eines Automation-Kontos aus.
2. Wählen Sie im Hub die Option **Runbooks** aus, und wählen Sie dann auf der Seite **Runbooks** ein Runbook in der Liste aus.
3. Klicken Sie auf der Seite für das ausgewählte Runbook auf die Kachel **Aufträge**.
4. Wenn Sie auf einen der Aufträge in der Liste klicken, können Sie auf der Seite für die Runbook-Auftragsdetails die Details und die Ausgabe anzeigen.

## <a name="retrieving-job-status-using-powershell"></a>Abrufen des Auftragsstatus mithilfe von Windows PowerShell

Sie können [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) verwenden, um die für ein Runbook erstellten Aufträge und die Details zu einem bestimmten Auftrag anzuzeigen. Wenn Sie ein Runbook über PowerShell mithilfe des Befehls [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) starten, wird der resultierende Auftrag zurückgegeben. Verwenden Sie [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput), um die Ausgabe eines Auftrags abzurufen.

Die folgenden Beispielbefehle rufen den letzten Auftrag für ein Beispielrunbook ab und zeigen seinen Status, die für die Runbookparameter bereitgestellten Werte und die Ausgabe des Auftrags an.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Das folgende Beispiel ruft die Ausgabe für einen bestimmten Auftrag ab und gibt die einzelnen Datensätze zurück. Sollte für einen der Datensätze eine Ausnahme aufgetreten sein, wird anstelle des Werts die Ausnahme ausgegeben. Dieses Verhalten ist hilfreich, da Ausnahmen zusätzliche Informationen liefern können, die bei der Ausgabe normalerweise nicht protokolliert werden.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>Abrufen von Details aus Aktivitätsprotokoll

Weitere Details wie die Person oder das Konto, das das Runbook gestartet hat, können aus dem Aktivitätsprotokoll des Automation-Kontos abgerufen werden. Das folgende PowerShell-Beispiel gibt den letzten Benutzer an, um das betreffende Runbook auszuführen:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    { 
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>gleichmäßige Verteilung

Damit Ressourcen von allen Runbooks in der Cloud verwendet werden können, beendet oder entlädt Azure Automation jeden Auftrag vorübergehend, der seit mehr als drei Stunden ausgeführt wird. Aufträge für [PowerShell-basierte Runbooks](automation-runbook-types.md#powershell-runbooks) und [Python-Runbooks](automation-runbook-types.md#python-runbooks) werden beendet und nicht neu gestartet, und der Auftragsstatus zeigt „Beendet“.

Für zeitintensive Aufgaben sollten Sie einen [Hybrid Runbook Worker](automation-hrw-run-runbooks.md#job-behavior) verwenden. Hybrid Runbook Worker unterliegen nicht der gleichmäßigen Verteilung und keiner Einschränkung hinsichtlich der Ausführungszeit eines Runbooks. Die anderen [Grenzwerte](../azure-subscription-service-limits.md#automation-limits) für Aufträge gelten sowohl für Azure-Sandboxes als auch für Hybrid Runbook Workers. Während Hybrid Runbook Workers nicht durch die dreistündige Begrenzung für die gleichmäßige Verteilung eingeschränkt sind, sollten Runbooks, die auf Hybrid Runbook Workers ausgeführt werden, so entwickelt werden, dass sie Neustartverhalten bei unerwarteten lokalen Infrastrukturproblemen unterstützen.

Eine weitere Möglichkeit ist das Optimieren des Runbooks durch die Verwendung von untergeordneten Runbooks. Wenn Ihr Runbook für mehrere Ressourcen eine Schleife durch die gleiche Funktion durchführt, z.B. für einen Datenbankvorgang in mehreren Datenbanken, können Sie diese Funktion in ein [untergeordnetes Runbook](automation-child-runbooks.md) verschieben und mit dem Cmdlet [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) aufrufen. Jedes dieser untergeordneten Runbooks wird parallel in separaten Prozessen ausgeführt. Dieses Verhalten führt dazu, dass die Gesamtdauer der Verarbeitung für das übergeordnete Runbook verringert wird. Mit dem Cmdlet [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) in Ihrem Runbook können Sie den Auftragsstatus für jedes untergeordnete Element überprüfen, wenn Vorgänge vorhanden sind, die nach Abschluss des untergeordneten Runbooks durchgeführt werden.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den verschiedenen Methoden, die zum Starten eines Runbooks in Azure Automation verwendet werden können, finden Sie unter [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md).

