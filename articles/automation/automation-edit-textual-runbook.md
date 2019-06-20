---
title: Bearbeiten von Textrunbooks in Azure Automation
description: Dieser Artikel enthält verschiedene Verfahren zum Arbeiten mit PowerShell und PowerShell-Workflow-Runbooks in Azure Automation unter Verwendung des Text-Editors.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9c684e9d1bf6cec12024cedfb5360d10e400e139
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61071092"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Bearbeiten von Textrunbooks in Azure Automation

Mit dem Text-Editor in Azure Automation können Sie [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks) und [PowerShell-Workflow-Runbooks](automation-runbook-types.md#powershell-workflow-runbooks) bearbeiten. Dieser Editor bietet neben den üblichen Features anderer Code-Editoren wie Intellisense und Farbcodierung zusätzliche spezielle Features, um den Zugriff auf für Runbooks typische Ressourcen zu ermöglichen. Dieser Artikel enthält detaillierte Schritte zum Ausführen verschiedener Funktionen mit diesem Editor.

Der Text-Editor enthält ein Feature zum Einfügen von Code für Cmdlets, Objekte und untergeordnete Runbooks in ein Runbook. Sie müssen den Code nicht selbst eingeben, sondern können eine Auswahl aus einer Liste verfügbarer Ressourcen treffen und den entsprechenden Code in das Runbook einfügen.

Jedes Runbook in Azure Automation umfasst zwei Versionen: eine Entwurfsversion und eine veröffentlichte Version. Sie bearbeiten die Entwurfsversion des Runbooks und veröffentlichen sie anschließend, damit sie ausgeführt werden kann. Die veröffentlichte Version kann nicht bearbeitet werden. Weitere Informationen finden Sie unter [Veröffentlichen eines Runbooks](manage-runbooks.md#publish-a-runbook).

Informationen zum Arbeiten mit [grafischen Runbooks](automation-runbook-types.md#graphical-runbooks) finden Sie unter [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>So bearbeiten Sie ein Runbook mit dem Azure-Portal

Gehen Sie wie folgt vor, um ein Runbook zur Bearbeitung im Text-Editor zu öffnen.

1. Wählen Sie im Azure-Portal Ihr Automation-Konto aus.
2. Klicken Sie unter **PROZESSAUTOMATISIERUNG** auf **Runbooks**, um die Liste mit den Runbooks zu öffnen.
3. Wählen Sie das Runbook aus, das Sie bearbeiten möchten, und klicken Sie dann auf die Schaltfläche **Bearbeiten**.
4. Bearbeiten Sie das Runbook.
5. Klicken Sie auf **Speichern** , wenn die Bearbeitung abgeschlossen ist.
6. Klicken Sie auf **Veröffentlichen** , wenn die aktuelle Entwurfsversion des Runbooks veröffentlicht werden soll.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>So fügen Sie ein Cmdlet in ein Runbook ein

1. Platzieren Sie den Cursor im Zeichenbereich des Text-Editors an der Position, an der Sie das Cmdlet einfügen möchten.
2. Erweitern Sie im Bibliotheksteuerelement den Knoten **Cmdlets** .
3. Erweitern Sie das Modul mit dem Cmdlet, das Sie verwenden möchten.
4. Klicken Sie mit der rechten Maustaste auf das einzufügende Cmdlet, und wählen Sie **Add to canvas** (Zur Canvas hinzufügen). Wenn für das Cmdlet mehrere Parameter festgelegt sind, wird der Standardsatz hinzugefügt. Sie können das Cmdlet auch erweitern, um einen anderen Parametersatz auszuwählen.
5. Der Code für das Cmdlet wird mit der gesamten Liste der Parameter eingefügt.
6. Geben Sie für alle erforderlichen Parameter anstelle des Datentyps in spitzen Klammern (<>) einen zulässigen Wert ein. Entfernen Sie alle nicht benötigten Parameter.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>So fügen Sie Code für ein untergeordnetes Runbook in ein Runbook ein

1. Platzieren Sie den Cursor im Zeichenbereich des Text-Editors an der Position, an der Sie den Code für das [untergeordnete Runbook](automation-child-runbooks.md)einfügen möchten.
2. Erweitern Sie im Bibliotheksteuerelement den Knoten **Runbooks** .
3. Klicken Sie mit der rechten Maustaste auf das einzufügende Runbook, und wählen Sie **Add to canvas** (Zur Canvas hinzufügen).
4. Der Code für das untergeordnete Runbook wird mit allen Platzhaltern für alle Runbook-Parameter eingefügt.
5. Ersetzen Sie die Platzhalter durch entsprechende Werte für jeden Parameter.

### <a name="to-insert-an-asset-into-a-runbook"></a>So fügen Sie ein Objekt in ein Runbook ein

1. Platzieren Sie den Cursor im Zeichenbereich des Text-Editors an der Position, an der Sie den Code für das untergeordnete Runbook einfügen möchten.
2. Erweitern Sie im Bibliotheksteuerelement den Knoten **Objekte** .
3. Erweitern Sie den Knoten für den gewünschten Objekttyp.
4. Klicken Sie mit der rechten Maustaste auf das einzufügende Objekt, und wählen Sie **Add to canvas** (Zur Canvas hinzufügen). Wählen Sie für [Variablenobjekte](automation-variables.md) entweder **„Variable abrufen“ zum Zeichenbereich hinzufügen** oder **„Variable festlegen“ zum Zeichenbereich hinzufügen** aus. Dies ist abhängig davon, ob Sie die Variable abrufen oder festlegen möchten.
5. Der Code für das Objekt wird in das Runbook eingefügt.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>So bearbeiten Sie ein Azure Automation-Runbook mit Windows PowerShell

Zum Bearbeiten eines Runbooks mit Windows PowerShell verwenden Sie einen Editor Ihrer Wahl und speichern es in einer Datei vom Typ `.ps1`. Mit dem Cmdlet [Export-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Export-AzureRmAutomationRunbook) können Sie den Inhalt des Runbooks abrufen, und mit dem Cmdlet [Import-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/import-azurermautomationrunbook) können Sie das vorhandene Entwurfsrunbook durch das geänderte Runbook ersetzen.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>So rufen Sie den Inhalt eines Runbooks mit WindowsPowerShell ab

Die folgenden Beispielbefehle zeigen, wie das Skript für ein Runbook abgerufen und in einer Skriptdatei gespeichert wird. In diesem Beispiel wird die Entwurfsversion abgerufen. Sie können auch die veröffentlichte Version des Runbooks abrufen, auch wenn diese Version nicht geändert werden kann.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>So ändern Sie den Inhalt eines Runbooks mit WindowsPowerShell

Die folgenden Beispielbefehle zeigen, wie der vorhandene Inhalt eines Runbooks durch den Inhalt einer Skriptdatei ersetzt wird. Hierbei handelt es sich um das gleiche Beispielverfahren wie unter [So importieren Sie ein Runbook aus einer Skriptdatei mit Windows PowerShell](manage-runbooks.md#import-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzureRmAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Verwandte Artikel

* [Verwalten von Runbooks in Azure Automation](manage-runbooks.md)
* [Grundlagen des PowerShell-Workflows](automation-powershell-workflow.md)
* [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md)
* [Certificates](automation-certificates.md)
* [Verbindungen](automation-connections.md)
* [Anmeldeinformationen](automation-credentials.md)
* [Zeitpläne](automation-schedules.md)
* [Variablen](automation-variables.md)

