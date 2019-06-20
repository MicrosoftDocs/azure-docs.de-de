---
title: Grafische Erstellung in Azure Automation
description: Die grafische Erstellung ermöglicht Ihnen das Erstellen von Runbooks für Azure Automation, ohne mit Code zu arbeiten. Dieser Artikel bietet eine Einführung in die grafische Erstellung und alle Informationen, die Sie für die Erstellung eines grafischen Runbooks benötigen.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ae732ab5c73dbec4a2aef6521b9edb490079112e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60740671"
---
# <a name="graphical-authoring-in-azure-automation"></a>Grafische Erstellung in Azure Automation

Mithilfe der grafischen Erstellung können Sie ohne die Komplexität des zugrunde liegenden Windows PowerShell- oder PowerShell-Workflow-Codes Runbooks für Azure Automation erstellen. Sie können der Canvas Aktivitäten aus einer Bibliothek mit Cmdlets und Runbooks hinzufügen, Verknüpfungen erstellen und die Elemente konfigurieren, um einen Workflow zu erhalten. Wenn Sie schon einmal mit System Center Orchestrator oder Service Management Automation (SMA) gearbeitet haben, dürfte Ihnen das bekannt vorkommen.

Dieser Artikel bietet eine Einführung in die grafische Erstellung und die Konzepte, die Sie für den Einstieg in die Erstellung eines grafischen Runbooks benötigen.

## <a name="graphical-runbooks"></a>Grafische Runbooks

Alle Runbooks in Azure Automation sind Windows PowerShell-Workflows. Grafische Runbooks und grafische PowerShell-Workflow-Runbooks generieren PowerShell-Code, der von den Automation-Workern ausgeführt wird. Sie können diesen Code jedoch weder anzeigen noch direkt ändern. Ein grafisches Runbook kann in ein grafisches PowerShell-Workflow-Runbook konvertiert werden (und umgekehrt), eine Konvertierung in ein Textrunbook ist jedoch nicht möglich. Bereits vorhandene Textrunbooks können nicht in den Grafik-Editor importiert werden.

## <a name="overview-of-graphical-editor"></a>Übersicht über den grafischen Editor

Sie können den grafischen Editor im Azure-Portal öffnen, indem Sie ein grafisches Runbook erstellen oder bearbeiten.

![Grafischer Arbeitsbereich](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

Die folgenden Abschnitte beschreiben die Steuerelemente im grafischen Editor.

### <a name="canvas"></a>Canvas

Die Canvas ist der Bereich, in dem Sie Ihr Runbook entwerfen. Sie fügen über die Knoten im Steuerelement "Bibliothek" Aktivitäten zum Runbook hinzu und verbinden diese mit Verknüpfungen zur Definition der Logik des Runbooks.

Über die Steuerelemente am unteren Rand der Canvas können Sie die Ansicht vergrößern und verkleinern.

### <a name="library-control"></a>Steuerelement "Bibliothek"

Über das Steuerelement "Bibliothek" wählen Sie die [Aktivitäten](#activities) aus, die Sie Ihrem Runbook hinzufügen möchten. Sie fügen diese zur Canvas hinzu, wenn Sie sie mit anderen Aktivitäten verbinden. Das Steuerelement enthält vier Abschnitte, die in der folgenden Tabelle beschrieben werden:

| `Section` | BESCHREIBUNG |
|:--- |:--- |
| Cmdlets |Enthält alle Cmdlets, die in Ihrem Runbook verwendet werden können. Die Cmdlets sind nach Modul angeordnet. Es sind alle Module verfügbar, die Sie in Ihrem Automation-Konto installiert haben. |
| Runbooks |Umfasst die Runbooks in Ihrem Automation-Konto. Diese Runbooks können der Canvas hinzugefügt und als untergeordnete Runbooks verwendet werden. Es werden nur Runbooks angezeigt, die den gleichen Kerntyp besitzen wie das gerade bearbeitete Runbook: Für grafische Runbooks werden nur PowerShell-basierte Runbooks angezeigt, für grafische PowerShell-Workflow-Runbooks nur PowerShell-Workflow-basierte Runbooks. |
| Objekte |Enthalten die [Automation-Objekte](/previous-versions/azure/dn939988(v=azure.100)) in Ihrem Automation-Konto, die im Runbook verwendet werden können. Wenn Sie ein Objekt in ein Runbook einfügen, wird eine Workflowaktivität zum Abrufen des ausgewählten Objekts hinzugefügt. Im Fall von Variablenobjekten können Sie auswählen, ob Sie eine Aktivität zum Abrufen der Variable oder eine Aktivität zum Festlegen der Variable hinzufügen möchten. |
| Steuerelement "Runbook" |Enthält die Runbookaktivitäten, die im aktuellen Runbook verwendet werden können. Eine *Verbindung* akzeptiert mehrere Eingaben und wartet, bis sämtliche dieser Eingaben abgeschlossen wurden, bevor der Workflow fortgesetzt wird. Eine Aktivität vom Typ *Code* führt abhängig von der Art des grafischen Runbooks mindestens eine PowerShell- oder PowerShell-Workflow-Codezeile aus. Sie können diese Aktivität für benutzerdefinierten Code oder für Funktionen nutzen, die sich mit anderen Aktivitäten nur schwer umsetzen lassen. |

### <a name="configuration-control"></a>Steuerelement "Konfiguration"

Das Steuerelement „Konfiguration“ dient zum Angeben von Details für ein auf der Canvas ausgewähltes Objekt. Welche Eigenschaften in diesem Steuerelement zur Verfügung stehen, hängt von der Art des ausgewählten Objekts ab. Wenn Sie im Steuerelement „Konfiguration“ eine Option auswählen, werden zusätzliche Blätter zum Angeben weiterer Informationen geöffnet.

### <a name="test-control"></a>Steuerelement "Test"

Das Steuerelement "Test" wird beim ersten Start des grafischen Editors nicht angezeigt. Es wird geöffnet, wenn Sie ein [grafisches Runbook interaktiv testen](#graphical-runbook-procedures).

## <a name="graphical-runbook-procedures"></a>Grafische Runbooks – Verfahren

### <a name="exporting-and-importing-a-graphical-runbook"></a>Exportieren und Importieren eines grafischen Runbooks

Sie können nur die veröffentlichte Version eines grafisch Runbooks exportieren. Wenn das Runbook noch nicht veröffentlicht wurde, ist die Schaltfläche **Exportieren** deaktiviert. Beim Klicken auf die Schaltfläche **Exportieren** wird das Runbook auf Ihren lokalen Computer heruntergeladen. Der Name der Datei entspricht dem Namen des Runbooks mit der Erweiterung *graphrunbook* .

Sie können eine Datei für ein grafisches Runbook oder für ein grafisches PowerShell-Workflow-Runbook importieren, indem Sie beim Hinzufügen eines Runbooks die Option **Importieren** auswählen. Wenn Sie die zu importierende Datei auswählen, können Sie den **Namen** beibehalten oder einen neuen vergeben. Das Feld für den Runbooktyp gibt nach der Prüfung der ausgewählten Datei Aufschluss über die Art des Runbooks. Beim Versuch, einen anderen (falschen) Typ auszuwählen, wird in einer Meldung auf mögliche Konflikte hingewiesen, und bei der Konvertierung treten unter Umständen Syntaxfehler auf.

![Runbook importieren](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Testen eines grafischen Runbooks

Sie können die Entwurfsversion eines Runbooks im Azure-Portal testen und dabei die veröffentlichte Version des Runbooks unverändert lassen, oder Sie testen ein neues Runbook, bevor Sie dieses veröffentlichen. Auf diese Weise stellen Sie sicher, dass das Runbook ordnungsgemäß arbeitet, bevor Sie die veröffentlichte Version ersetzen. Wenn Sie ein Runbook testen, wird die Entwurfsversion des Runbooks ausgeführt, und alle darin ausgeführten Aktionen werden abgeschlossen. Es wird kein Auftragsverlauf erstellt, aber im Fensterbereich "Testausgabe" wird die Ausgabe angezeigt.

Sie öffnen das Steuerelement "Test" für ein Runbook, indem Sie das Runbook zur Bearbeitung öffnen und dann auf die Schaltfläche **Testbereich** klicken.

Das Steuerelement „Test“ fordert alle benötigten Eingabeparameter an, und Sie können das Runbook starten, indem Sie auf die Schaltfläche **Start** klicken.

### <a name="publishing-a-graphical-runbook"></a>Veröffentlichen eines grafischen Runbooks

Jedes Runbook in Azure Automation umfasst eine Entwurfsversion und eine veröffentlichte Version. Nur die veröffentlichte Version kann ausgeführt werden, und nur die Entwurfsversion kann bearbeitet werden. Die veröffentlichte Version bleibt von Änderungen an der Entwurfsversion unberührt. Wenn die Entwurfsversion zur Verfügung gestellt werden kann, können Sie sie veröffentlichen und auf diese Weise die veröffentlichte Version mit der Entwurfsversion überschreiben.

Sie können ein grafisches Runbook veröffentlichen, indem Sie das Runbook zur Bearbeitung öffnen und dann auf die Schaltfläche **Veröffentlichen** klicken.

Wenn ein Runbook noch nicht veröffentlicht wurde, weist es den Status **Neu**auf. Nach dem Veröffentlichen erhält es den Status **Veröffentlicht**. Wenn Sie das Runbook nach der Veröffentlichung bearbeiten und sich die Entwurfsversion und die veröffentlichte Version unterscheiden, weist das Runbook den Status **In Bearbeitung**auf.

![Statuswerte für ein Runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Sie haben außerdem die Möglichkeit, ein Runbook auf die veröffentlichte Version zurückzusetzen. Auf diese Weise werden alle Änderungen seit der letzten Veröffentlichung verworfen, und die Entwurfsversion des Runbooks wird durch die veröffentlichte Version ersetzt.

## <a name="activities"></a>activities

Aktivitäten sind die Bausteine eines Runbooks. Bei einer Aktivität kann es sich um ein PowerShell-Cmdlet, um ein untergeordnetes Runbook oder um eine Workflowaktivität handeln. Sie fügen dem Runbook eine Aktivität hinzu, indem Sie mit der rechten Maustaste im Steuerelement „Bibliothek“ auf das Runbook klicken und die Option **Zur Canvas hinzufügen**auswählen. Anschließend können Sie die Aktivität per Drag & Drop an einer beliebigen Stelle auf der Canvas platzieren. Die Position der Aktivität auf der Canvas hat keinerlei Einfluss auf den Vorgang im Runbook. Sie können das Layout Ihres Runbooks so gestalten, wie es Ihnen zur visuellen Darstellung der Vorgänge am geeignetsten erscheint.

![Zum Zeichenbereich hinzufügen](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Wählen Sie die Aktivität auf der Canvas, um ihre Eigenschaften und Parameter im Blatt "Konfigurieren" zu konfigurieren. Sie können die **Bezeichnung** der Aktivität in eine für Sie aussagekräftige Beschreibung ändern. Das ursprüngliche Cmdlet wird weiterhin ausgeführt, Sie ändern lediglich den Anzeigenamen, der im grafischen Editor verwendet wird. Die Bezeichnung muss innerhalb des Runbooks eindeutig sein.

### <a name="parameter-sets"></a>Parametersätze

Ein Parametersatz definiert die erforderlichen und optionalen Parameter, die Werte für ein bestimmtes Cmdlet akzeptieren. Alle Cmdlets müssen über mindestens einen Parametersatz verfügen, einige enthalten mehrere. Wenn ein Cmdlet mehrere Parametersätze aufweist, können Sie die Parameter erst konfigurieren, wenn Sie den zu verwendenden Parametersatz ausgewählt haben. Die Parameter, die Sie konfigurieren können, richten sich nach dem ausgewählten Parametersatz. Sie ändern den durch eine Aktivität verwendeten Parametersatz, indem Sie **Parametersatz** auswählen und einen anderen Satz angeben. In diesem Fall gehen alle bereits konfigurierten Parameterwerte verloren.

Im folgenden Beispiel besitzt das Cmdlet „Get-AzureRmVM“ drei Parametersätze. Sie können erst dann Parameterwerte konfigurieren, wenn Sie einen der Parametersätze ausgewählt haben. Der ListVirtualMachineInResourceGroupParamSet-Parametersatz dient zum Zurückgeben aller virtuellen Computer in einer Ressourcengruppe und besitzt einen einzelnen optionalen Parameter. Der **GetVirtualMachineInResourceGroupParamSet**-Parametersatz dient zum Angeben des virtuellen Computers, den Sie zurückgeben möchten, und weist zwei erforderliche und einen optionalen Parameter auf.

![Parametersatz](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Parameterwerte

Wenn Sie einen Wert für einen Parameter angeben, wählen Sie eine Datenquelle aus, um festzulegen, wie der Wert angegeben wird. Die für einen bestimmten Parameter verfügbaren Datenquellen richten sich nach den gültigen Werten für diesen Parameter. Beispielsweise ist NULL keine verfügbare Option für einen Parameter, der keine NULL-Werte zulässt.

| Data source | BESCHREIBUNG |
|:--- |:--- |
| Konstanter Wert |Geben Sie einen Wert für den Parameter ein. Dies ist nur für die folgenden Datentypen verfügbar: Int32, Int64, String, Boolean, DateTime, Switch. |
| Aktivitätsausgabe |Ausgabe einer Aktivität, die der aktuellen Aktivität im Workflow vorausgeht. Alle gültigen Aktivitäten werden aufgelistet. Wählen Sie einfach die Aktivität, um ihre Ausgabe für den Parameterwert zu verwenden. Wenn es sich bei der Aktivitätsausgabe um ein Objekt mit mehreren Eigenschaften handelt, können Sie nach Auswahl der Aktivität den Namen der Eigenschaft eingeben. |
| Runbookeingabe |Wählen Sie einen Runbook-Eingabeparameter als Eingabe für den Aktivitätsparameter aus. |
| Variablenobjekt |Wählen Sie eine Automation-Variable als Eingabe aus. |
| Anmeldeinformationsobjekt |Wählen Sie ein Automation-Anmeldeinformationsobjekt als Eingabe aus. |
| Zertifikatobjekt |Wählen Sie ein Automation-Zertifikatobjekt als Eingabe aus. |
| Verbindungsobjekt |Wählen Sie ein Automation-Verbindungsobjekt als Eingabe aus. |
| PowerShell-Ausdruck |Geben Sie einen einfachen [PowerShell-Ausdruck](#powershell-expressions)an. Der Ausdruck wird vor der Aktivität ausgewertet, und das Ergebnis wird für den Parameterwert verwendet. Sie können Variablen verwenden, um auf die Ausgabe einer Aktivität oder einen Eingabeparameter für ein Runbook zu verweisen. |
| Nicht konfiguriert |Löscht einen Wert, der zuvor konfiguriert wurde. |

#### <a name="optional-additional-parameters"></a>Optionale zusätzliche Parameter

Alle Cmdlets bieten die Möglichkeit, zusätzliche Parameter festzulegen. Hierbei handelt es sich um allgemeine PowerShell-Parameter oder andere benutzerdefinierte Parameter. Es wird Ihnen ein Textfeld angezeigt, in dem Sie unter Verwendung der PowerShell-Syntax Parameter bereitstellen können. Um beispielsweise den allgemeinen Parameter **Verbose** zu verwenden, geben Sie **"-Verbose:$True"** an.

### <a name="retry-activity"></a>Wiederholen der Aktivität

Das **Wiederholungsverhalten** ermöglicht die mehrmalige Ausführung einer Aktivität, bis eine bestimmte Bedingung erfüllt ist (ähnlich wie bei einer Schleife). Sie können dieses Feature für Aktivitäten nutzen, die mehrmals ausgeführt werden müssen oder fehleranfällig sind und möglicherweise mehrere Versuche für eine erfolgreiche Ausführung. Sie können mit diesem Feature auch die Gültigkeit der ausgegebenen Informationen überprüfen.

Wenn Sie eine Wiederholung für eine Aktivität aktivieren, können Sie eine Verzögerung und eine Bedingung festlegen. Die Verzögerung ist die Zeit (gemessen in Sekunden oder Minuten), die das Runbook wartet, bevor die Aktivität erneut ausgeführt wird. Wird keine Verzögerung angegeben, wird die Aktivität sofort nach Abschluss erneut ausgeführt.

![Wiederholungsverzögerung für Aktivität](media/automation-graphical-authoring-intro/retry-delay.png)

Die Wiederholungsbedingung ist ein PowerShell-Ausdruck, der nach jeder Ausführung der Aktivität ausgewertet wird. Wenn der Ausdruck „True“ ergibt, wird die Aktivität erneut ausgeführt. Wenn der Ausdruck in „False“ resultiert, wird die Aktivität nicht erneut ausgeführt, und das Runbook fährt mit der nächsten Aktivität fort.

![Wiederholungsverzögerung für Aktivität](media/automation-graphical-authoring-intro/retry-condition.png)

Die Wiederholungsbedingung kann eine Variable namens „$RetryData“ enthalten, die Zugriff auf Informationen zu den Aktivitätswiederholungen bereitstellt. Diese Variable weist die in der folgenden Tabelle aufgeführten Eigenschaften auf:

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| NumberOfAttempts |Häufigkeit, mit der die Aktivität ausgeführt wurde. |
| Output |Die Ausgabe nach der letzten Ausführung der Aktivität. |
| TotalDuration |Vergangene Zeit seit dem ersten Start der Aktivität. |
| StartedAt |Uhrzeit im UTC-Format, zu der die Aktivität zuerst gestartet wurde. |

Es folgen Beispiele von Bedingungen für die Wiederholung von Aktivitäten.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

Wenn Sie eine Wiederholungsbedingung für eine Aktivität konfiguriert haben, verfügt die Aktivität über zwei optische Hinweise, um Sie daran zu erinnern. Einer wird in der Aktivität angezeigt, der andere beim Überprüfen der Aktivitätskonfiguration.

![Visuelle Indikatoren für Aktivitätswiederholung](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Steuerelement "Workflowskript"

Bei einer Codekontrolle handelt es sich um eine spezielle Aktivität, die je nach Art des zu erstellenden grafischen Runbooks ein PowerShell- oder PowerShell-Workflowskript akzeptiert, um Funktionen zur Verfügung zu stellen, die andernfalls möglicherweise nicht verfügbar wären. Es kann keine Parameter akzeptieren, kann jedoch Variablen für die Aktivitätsausgabe und Runbookeingabeparameter verwenden. Die Ausgabe der Aktivität wird dem Datenbus hinzugefügt, sofern eine ausgehende Verknüpfung vorhanden ist. Andernfalls wird die Ausgabe zur Runbookausgabe hinzugefügt.

Der folgende Code beispielsweise führt Datumsberechnungen unter Verwendung einer Runbookeingabevariable namens „$NumberOfDays“ durch. Anschließend wird ein berechneter DateTime-Wert als Ausgabe an nachfolgende Aktivitäten im Runbook gesendet.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Verknüpfungen und Workflow

Eine **Verknüpfung** in einem grafischen Runbook verbindet zwei Aktivitäten miteinander. Sie wird auf der Canvas als Pfeil dargestellt, der von der Quellaktivität zur Zielaktivität zeigt. Die Aktivitäten werden in Richtung des Pfeils ausgeführt, wobei die Zielaktivität gestartet wird, sobald die Quellaktivität abgeschlossen wurde.

### <a name="create-a-link"></a>Erstellen einer Verknüpfung

Sie erstellen eine Verknüpfung zwischen zwei Aktivitäten, indem Sie die Quellaktivität auswählen und auf den Kreis am unteren Ende der Form klicken. Ziehen Sie den Pfeil bis zur Zielaktivität, und lassen Sie die Maustaste los.

![Erstellen einer Verknüpfung](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Wählen Sie die Verknüpfung aus, um ihre Eigenschaften im Blatt "Konfiguration" zu konfigurieren. Hierzu gehört beispielsweise der Verknüpfungstyp, der in der folgenden Tabelle beschrieben wird:

| Verknüpfungstyp | BESCHREIBUNG |
|:--- |:--- |
| Pipeline |Die Zielaktivität wird einmal für jede Objektausgabe der Quellaktivität ausgeführt. Die Zielaktivität wird nicht ausgeführt, wenn die Quellaktivität zu keiner Ausgabe führt. Die Ausgabe der Quellaktivität steht als Objekt zur Verfügung. |
| Sequenz |Die Zielaktivität wird nur einmal ausgeführt. Sie empfängt ein Array aus Objekten von der Quellaktivität. Die Ausgabe der Quellaktivität steht als Array aus Objekten zur Verfügung. |

### <a name="starting-activity"></a>Startaktivität

Ein grafisches Runbook wird mit allen Aktivitäten gestartet, die keine eingehende Verknüpfung besitzen. Dies ist oft nur eine Aktivität, die als Startaktivität für das Runbook dient. Wenn mehrere Aktivitäten keine eingehende Verknüpfung aufweisen, wird das Runbook durch eine parallele Ausführung dieser Aktivitäten gestartet. Die Verknüpfungen werden durchlaufen, um nach jeder Beendigung einer Aktivität weitere Aktivitäten auszuführen.

### <a name="conditions"></a>Bedingungen

Wenn Sie für eine Verknüpfung eine Bedingung angeben, wird die Zielaktivität nur ausgeführt, wenn die Bedingung als wahr ausgewertet wird. Sie verwenden üblicherweise eine $ActivityOutput-Variable in einer Bedingung, um die Ausgabe der Quellaktivität abzurufen.

Bei einer Pipelineverknüpfung geben Sie eine Bedingung für ein einzelnes Objekt an, und die Bedingung wird für jedes ausgegebene Objekt der Quellaktivität ausgewertet. Die Zielaktivität wird anschließend für jedes Objekt ausgeführt, das die Bedingung erfüllt. Wenn Sie beispielsweise „Get-AzureRmVm“ als Quellaktivität verwenden, kann die folgende Syntax für eine bedingte Pipelineverknüpfung verwendet werden, um nur virtuelle Computer aus der Ressourcengruppe *Group1*abzurufen.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Bei einer Sequenzverknüpfung wird die Bedingung nur einmal ausgewertet, da ein einzelnes Array mit sämtlichen Objekten zurückgegeben wird, die von der Quellaktivität ausgegeben werden. Aus diesem Grund kann eine Sequenzverknüpfung nicht wie eine Pipelineverknüpfung zur Filterung verwendet werden, sondern ermittelt einfach nur, ob die nächste Aktivität ausgeführt wird oder nicht. Betrachten Sie beispielsweise den folgenden Satz von Aktivitäten aus unserem Runbook zum Starten des virtuellen Computers:

![Bedingte Verknüpfung mit Sequenzen](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Hier sehen Sie drei unterschiedliche Sequenzverknüpfungen, die überprüfen, ob Werte für zwei Runbookeingabeparameter (Name des virtuellen Computers und Name der Ressourcengruppe) angegeben wurden, um zu bestimmen, welche Aktion durchgeführt werden soll: Starten eines einzelnen virtuellen Computers, Starten aller virtuellen Computer in der Ressourcengruppe oder Starten aller virtuellen Computer eines Abonnements. Die Bedingungslogik für die Sequenzverknüpfung zwischen „Connect to Azure“ (Verbindung mit Azure herstellen) und „Get single VM“ (Einzelnen virtuellen Computer abrufen“) sieht wie folgt aus:

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

Wenn Sie eine bedingte Verknüpfung verwenden, werden die verfügbaren Daten aus der Quellaktivität für andere Aktivitäten in dieser Verzweigung durch die Bedingung gefiltert. Wenn eine Aktivität die Quelle für mehrere Verknüpfungen ist, richten sich die für Aktivitäten verfügbaren Daten in jeder Verzweigung nach der Bedingung in der Verknüpfung, die mit dieser Verzweigung verbunden ist.

So startet beispielsweise die Aktivität **Start-AzureRmVm** im nachstehenden Runbook alle virtuellen Computer. Sie verfügt über zwei bedingte Verknüpfungen: Die erste bedingte Verknüpfung verwendet den Ausdruck *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*, um danach zu filtern, ob die Aktivität „Start-AzureRmVm“ erfolgreich abgeschlossen wurde. Die zweite verwendet den Ausdruck *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*, um danach zu filtern, ob der virtuelle Computer mit der Aktivität „Start-AzureRmVm“ nicht erfolgreich gestartet werden konnte.

![Beispiel einer bedingten Verknüpfung](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Alle Aktivitäten, die auf die erste Verknüpfung folgen und die Aktivitätsausgabe von "Get-AzureVM" verwenden, empfangen nur die virtuellen Computer, die zum Zeitpunkt der Ausführung von "Get-AzureVM" gestartet waren. Alle Aktivitäten, die der zweiten Verknüpfung folgen, rufen nur die virtuellen Computer ab, die zum Zeitpunkt der Ausführung von „Get-AzureVM“ beendet waren. Alle Aktivitäten, die der dritten Verknüpfung folgen, rufen alle virtuellen Computer ab, unabhängig von deren Ausführungsstatus.

### <a name="junctions"></a>Verbindungen

Eine Verbindung ist eine spezielle Aktivität, die wartet, bis alle eingehenden Verzweigungen abgeschlossen wurden. Auf diese Weise können mehrere Aktivitäten parallel ausgeführt werden, und vor dem Fortfahren sichergestellt, dass alle Aktivitäten abgeschlossen wurden.

Wenngleich eine Verbindung eine unbegrenzte Anzahl von eingehenden Verknüpfungen aufweisen kann, darf nur eine dieser Verknüpfungen eine Pipeline sein. Für die Anzahl der eingehenden Sequenzverknüpfungen gilt keine Beschränkung. Sie können die Verbindung mit mehreren eingehenden Pipelineverknüpfungen erstellen und das Runbook speichern, bei der Ausführung tritt jedoch ein Fehler auf.

Das nachstehende Beispiel ist ein Teil eines Runbooks, das einen Satz virtueller Computer startet, während gleichzeitig Patches heruntergeladen werden, die auf diese Computer angewendet werden sollen. Eine Verbindung stellt sicher, dass beide Prozesse abgeschlossen sind, bevor das Runbook fortgesetzt wird.

![Verbindung](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Zyklen

Wenn eine Zielaktivität eine Verknüpfung zurück auf die Quellaktivität oder eine andere Aktivität umfasst, die letztlich eine Verknüpfung mit der zugehörigen Quellaktivität aufweist, wird dies als Zyklus bezeichnet. Zyklen werden in der grafischen Erstellung zurzeit nicht unterstützt. Wenn Ihr Runbook einen Zyklus aufweist, wird dieser ordnungsgemäß gespeichert, empfängt bei der Ausführung jedoch einen Fehler.

![Zyklus](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Freigeben von Daten zwischen Aktivitäten

Sämtliche Daten, die von einer Aktivität mit ausgehender Verknüpfung ausgegeben werden, werden in den *Datenbus* für das Runbook geschrieben. Alle Aktivitäten im Runbook können die Daten im Datenbus zum Auffüllen von Parameterwerten oder zur Nutzung in Skriptcode verwenden. Eine Aktivität kann auf die Ausgabe einer beliebigen vorherigen Aktivität im Workflow zugreifen.

Wie die Daten in den Datenbus geschrieben werden, richtet sich nach dem Typ der Verknüpfung der Aktivität. Bei einer **Pipeline**werden die Daten als mehrere Objekte ausgegeben. Bei einer **Sequenz** werden die Daten als Array ausgegeben. Wenn nur ein Wert vorliegt, wird dieser als ein einzelnes Elementarray ausgegeben.

Sie können über eine von zwei Methoden auf die Daten im Datenbus zugreifen. Die erste Methode besteht darin, über eine Datenquelle vom Typ **Aktivitätsausgabe** einen Parameter einer anderen Aktivität aufzufüllen. Wenn es sich bei der Ausgabe um ein Objekt handelt, können Sie eine einzelne Eigenschaft festlegen.

![Aktivitätsausgabe](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Sie können außerdem die Ausgabe einer Aktivität in einer Datenquelle vom Typ **PowerShell-Ausdruck** oder mit einer ActivityOutput-Variablen aus einer Aktivität vom Typ **Workflowskript** abrufen. Wenn es sich bei der Ausgabe um ein Objekt handelt, können Sie eine einzelne Eigenschaft festlegen. ActivityOutput-Variablen verwenden die folgende Syntax.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Prüfpunkte

Sie können [Prüfpunkte](automation-powershell-workflow.md#checkpoints) in einem grafischen PowerShell-Workflow-Runbook festlegen, indem Sie für eine beliebige Aktivität die Option *Prüfpunkt für Runbook* auswählen. Dadurch wird ein Prüfpunkt festgelegt, nachdem die Aktivität ausgeführt wurde.

![Prüfpunkt](media/automation-graphical-authoring-intro/set-checkpoint.png)

Prüfpunkte stehen nur in grafischen PowerShell-Workflow-Runbooks zur Verfügung, nicht in grafischen Runbooks. Wenn das Runbook Azure-Cmdlets enthält, sollten Sie alle Aktivitäten mit Prüfpunkt mit „Connect-AzureRmAccount“ verfolgen, falls das Runbook angehalten und an diesem Prüfpunkt auf einem anderen Worker neu gestartet wird.

## <a name="authenticating-to-azure-resources"></a>Authentifizierung bei Azure-Ressourcen

Zur Verwaltung von Azure-Ressourcen verwendete Runbooks in Azure Automation erfordern eine Authentifizierung bei Azure. Standardmäßig wird das [ausführende Konto](automation-create-runas-account.md) (auch als Dienstprinzipal bezeichnet) verwendet, um in Ihrem Abonnement mit Automation-Runbooks auf Azure Resource Manager-Ressourcen zuzugreifen. Diese Funktion können Sie einem grafischen Runbook hinzufügen, indem Sie der Canvas das Verbindungsobjekt **AzureRunAsConnection** (verwendet das PowerShell-Cmdlet [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx)) und das Cmdlet [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) hinzufügen. Dies wird im folgenden Beispiel veranschaulicht:

![Authentifizierungsaktivitäten für „Ausführen als“](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

Die Aktivität „Get Run As Connection“ (also „Get-AutomationConnection“) ist mit einer Datenquelle mit konstantem Wert konfiguriert: AzureRunAsConnection.

![Verbindungskonfiguration für „Ausführen als“](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Die nächste Aktivität (Connect-AzureRmAccount) fügt das authentifizierte ausführende Konto hinzu, damit es im Runbook verwendet werden kann.

![Parametersatz „Connect-AzureRmAccount“](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

> [!IMPORTANT]
> **Add-AzureRmAccount** ist jetzt ein Alias für **Connect-AzureRMAccount**. Sollte **Connect-AzureRMAccount** beim Durchsuchen der Bibliothekselemente nicht angezeigt werden, können Sie **Add-AzureRmAccount** verwenden oder die Module in Ihrem Automation-Konto aktualisieren.

Für die Parameter **APPLICATIONID**, **CERTIFICATETHUMBPRINT** und **TENANTID** muss der Name der Eigenschaft für den Feldpfad angegeben werden, da die Aktivität ein Objekt mit mehreren Eigenschaften ausgibt. Andernfalls kann beim Ausführen des Runbooks keine Authentifizierung erfolgen. Dies sind die Mindestanforderungen, die für die Authentifizierung Ihres Runbooks mit dem ausführenden Konto erfüllt sein müssen.

Um die Abwärtskompatibilität für Abonnenten zu gewährleisten, die ein Automation-Konto mit einem [Azure AD-Benutzerkonto](automation-create-aduser-account.md) erstellt haben, um klassische Azure-Bereitstellungen oder Azure Resource Manager-Ressourcen zu verwalten, wird zur Authentifizierung das Cmdlet „Add-AzureAccount“ mit einem [Anmeldeinformationsobjekt](automation-credentials.md) verwendet, das einen Active Directory-Benutzer mit Zugriff auf das Azure-Konto darstellt.

Sie können diese Funktionalität zu einem grafischen Runbook hinzufügen, indem Sie der Canvas ein Anmeldeinformationsobjekt gefolgt von einer Add-AzureAccount-Aktivität hinzufügen. "Add-AzureAccount" verwendet die Anmeldeinformationen aus der Aktivität als Eingabe. Dies wird im folgenden Beispiel veranschaulicht:

![Authentifizierungsaktivitäten](media/automation-graphical-authoring-intro/authentication-activities.png)

Sie müssen beim Start des Runbooks und nach jedem Prüfpunkt eine Authentifizierung durchführen. Dies bedeutet, dass Sie nach jeder Checkpoint-Workflow-Aktivität eine Add-AzureAccount-Aktivität einfügen. Sie müssen keine zusätzliche Aktivität für die Anmeldung hinzufügen, da dieselbe Aktivität verwendet werden kann.

![Aktivitätsausgabe](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Eingabe und Ausgabe in Runbooks

### <a name="runbook-input"></a>Runbookeingabe

Ein Runbook erfordert möglicherweise Eingaben durch den Benutzer, wenn es über das Azure-Portal oder (wenn das aktuelle Runbook als untergeordnetes Runbook verwendet wird) aus einem anderen Runbook gestartet wird.
Wenn Sie beispielsweise über ein Runbook verfügen, das einen virtuellen Computer erstellt, müssen Sie bei jedem Start des Runbooks möglicherweise Informationen wie den Namen des virtuellen Computers und andere Eigenschaften bereitstellen.

Sie akzeptieren eine Eingabe für ein Runbook, indem Sie einen oder mehrere Eingabeparameter definieren. Die Werte für diese Parameter werden bei jedem Start des Runbooks bereitgestellt. Wenn Sie ein Runbook im Azure-Portal starten, werden Sie aufgefordert, Werte für jeden Eingabeparameter des Runbooks einzugeben.

Sie können auf die Eingabeparameter für ein Runbook zugreifen, indem Sie auf die Schaltfläche **Eingabe und Ausgabe** auf der Symbolleiste des Runbooks klicken.

Daraufhin wird das Steuerelement **Eingabe und Ausgabe** geöffnet, in dem Sie einen vorhandenen Eingabeparameter bearbeiten oder durch Klicken auf **Eingabe hinzufügen** einen neuen Eingabeparameter erstellen können.

![Eingabe hinzufügen](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Jeder Eingabeparameter wird durch die Eigenschaften in der folgenden Tabelle definiert:

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| NAME |Der eindeutige Name des Parameters. Dieser darf nur aus alphanumerischen Zeichen bestehen und darf keine Leerzeichen enthalten. |
| BESCHREIBUNG |Eine optionale Beschreibung für den Eingabeparameter. |
| Type |Der für den Parameterwert erwartete Datentyp. Das Azure-Portal bietet ein geeignetes Steuerelement zum Eingeben des jeweiligen Datentyps für jeden Parameter. |
| Erforderlich |Gibt an, ob ein Wert für den Parameter bereitgestellt werden muss. Das Runbook kann nicht gestartet werden, wenn der Wert für einen erforderlichen Parameter fehlt, für den kein Standardwert definiert wurde. |
| Standardwert |Gibt an, welcher Wert für den Parameter verwendet wird, wenn kein Wert bereitgestellt wird. Dieser Wert kann entweder "Null" lauten oder einen spezifischen Wert angeben. |

### <a name="runbook-output"></a>Runbookausgabe

Daten, die von einer Aktivität ohne ausgehende Verknüpfung erstellt werden, werden in der [Ausgabe des Runbooks](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages) gespeichert. Die Ausgabe wird mit dem Runbookauftrag gespeichert und steht einem übergeordneten Runbook zur Verfügung, wenn das Runbook als untergeordnetes Runbook verwendet wird.

## <a name="powershell-expressions"></a>PowerShell-Ausdrücke

Einer der Vorteile der grafischen Erstellung ist, dass Sie auch dann ein Runbook erstellen können, wenn Sie nur über wenig PowerShell-Kenntnisse verfügen. Derzeit benötigen Sie aber einige PowerShell-Kenntnisse, um bestimmte [Parameterwerte](#activities) einzufügen und [Verknüpfungsbedingungen](#links-and-workflow) festzulegen. Dieser Abschnitt enthält eine kurze Einführung in PowerShell-Ausdrücke für Benutzer, die damit unter Umständen noch nicht vertraut sind. Alle Details von PowerShell sind unter [Skripterstellung mit Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx)verfügbar.

### <a name="powershell-expression-data-source"></a>PowerShell-Ausdruck als Datenquelle
Sie können einen PowerShell-Ausdruck als Datenquelle verwenden, um den Wert eines [Aktivitätsparameters](#activities) mit den Ergebnissen von PowerShell-Code zu füllen. Hierbei kann es sich um eine einzelne Codezeile handeln, mit der eine einfache Funktion durchgeführt wird, oder um mehrere Zeilen, mit denen die Schritte einer komplexen Logik ausgeführt werden. Alle Ausgaben eines Befehls, der keiner Variablen zugewiesen ist, werden an den Parameterwert ausgegeben.

Mit dem folgenden Befehl wird beispielsweise das aktuelle Datum ausgegeben.

Mit dem folgenden Befehl wird beispielsweise das aktuelle Datum ausgegeben.

```powershell-interactive
Get-Date
```

Mit den folgenden Befehlen wird aus dem aktuellen Datum eine Zeichenfolge erstellt und einer Variablen zugewiesen. Die Inhalte der Variablen werden dann an die Ausgabe gesendet.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Mit den folgenden Befehlen wird das aktuelle Datum ausgewertet, und es wird eine Zeichenfolge zurückgegeben, die angibt, ob der aktuelle Tag ein Tag am Wochenende oder ein Wochentag ist.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Aktivitätsausgabe

Zum Verwenden der Ausgabe einer vorherigen Aktivität im Runbook nutzen Sie die $ActivityOutput-Variable mit der unten angegebenen Syntax.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Ein Beispiel: Sie verfügen über eine Aktivität mit einer Eigenschaft, für die der Name eines virtuellen Computers erforderlich ist. Hierfür können Sie den folgenden Ausdruck verwenden:

```powershell-interactive
$ActivityOutput['Get-AzureVm'].Name
```

Falls für die Eigenschaft anstelle einer einfachen Eigenschaft das Objekt des virtuellen Computers erforderlich ist, würden Sie das gesamte Objekt mit der folgenden Syntax zurückgeben:

```powershell-interactive
$ActivityOutput['Get-AzureVm']
```

Sie können die Ausgabe einer Aktivität auch in einem komplexeren Ausdruck verwenden, z. B. wie im folgenden Beispiel, in dem Text mit dem Namen des virtuellen Computers verkettet wird.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVm'].Name
```

### <a name="conditions"></a>Bedingungen

Verwenden Sie [Vergleichsoperatoren](https://technet.microsoft.com/library/hh847759.aspx) , um Werte zu vergleichen, oder bestimmen Sie, ob ein Wert mit einem bestimmten Muster übereinstimmt. Bei einem Vergleich wird entweder der Wert „$true“ oder „$false“ zurückgegeben.

Mit der folgenden Bedingung wird beispielsweise bestimmt, ob sich der virtuelle Computer einer Aktivität mit dem Namen *Get-AzureVM* derzeit im Zustand *Beendet* befindet.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

Mit der folgenden Bedingung wird überprüft, ob sich derselbe virtuelle Computer in einem anderen Zustand als *Beendet*befindet.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Sie können mehrere Bedingungen mit einem [logischen Operator](https://technet.microsoft.com/library/hh847789.aspx) wie **-and** oder **-or** verknüpfen. Mit der folgenden Bedingung wird beispielsweise überprüft, ob sich derselbe virtuelle Computer aus dem vorherigen Beispiel im Zustand *Beendet* oder *Wird beendet* befindet.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Hashtabellen

[Hashtabellen](https://technet.microsoft.com/library/hh847780.aspx) sind Name-Wert-Paare, die hilfreich zum Zurückgeben einer Gruppe von Werten sind. Eigenschaften für bestimmte Aktivitäten erwarten unter Umständen anstelle eines einfachen Werts eine Hashtabelle. Es kann auch vorkommen, dass eine Hashtabelle als Wörterbuch bezeichnet wird.

Sie erstellen eine Hashtabelle mit der folgenden Syntax. Eine Hashtabelle kann eine beliebige Anzahl von Einträgen enthalten, die aber jeweils durch einen Namen und einen Wert definiert sind.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Mit dem folgenden Ausdruck wird beispielsweise eine Hashtabelle erstellt, die in der Datenquelle für einen Aktivitätsparameter verwendet wird, der eine Hashtabelle mit Werten für eine Internetsuche erwartet.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

Im folgenden Beispiel wird die Ausgabe eine Aktivität mit dem Namen *Get Twitter Connection* (Twitter-Verbindung abrufen) zum Auffüllen einer Hashtabelle verwendet.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="next-steps"></a>Nächste Schritte

* Die ersten Schritte mit PowerShell-Workflow-Runbooks sind unter [Mein erstes PowerShell-Workflow-Runbook](automation-first-runbook-textual.md)
* Informationen zu den ersten Schritten mit grafischen Runbooks finden Sie unter [Mein erstes grafisches Runbook](automation-first-runbook-graphical.md)
* Weitere Informationen zu den verschiedenen Runbooktypen sowie zu ihren Vorteilen und Einschränkungen finden Sie unter [Azure Automation-Runbooktypen](automation-runbook-types.md)
* Informationen zur Authentifizierung mithilfe des ausführenden Automation-Kontos finden Sie unter [Authentifizieren von Runbooks mit der Azure-Option „Ausführendes Konto“](automation-sec-configure-azure-runas-account.md)
