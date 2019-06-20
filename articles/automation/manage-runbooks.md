---
title: Verwalten von Runbooks in Azure Automation
description: In diesem Artikel wird beschrieben, wie Sie Runbooks in Azure Automation verwalten.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5bb52e0547ed9bc18d67370ffb9db35942212aab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61300271"
---
# <a name="manage-runbooks-in-azure-automation"></a>Verwalten von Runbooks in Azure Automation

Sie können Azure Automation ein Runbook hinzufügen, indem Sie entweder [ein neues Runbook erstellen](#create-a-runbook) oder ein vorhandenes Runbook aus einer Datei oder aus dem [Runbook-Katalog](automation-runbook-gallery.md) importieren. Dieser Artikel enthält Informationen zum Erstellen und Importieren von Runbooks aus einer Datei.  Einzelheiten zum Zugreifen auf Community-Runbooks und Module erfahren Sie in [Runbook und Modulkataloge für Azure Automation](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Erstellen eines Runbooks

Sie können ein neues Runbook in Azure Automation mithilfe eines der Azure-Portale oder Windows PowerShell erstellen. Sobald das Runbook erstellt wurde, können Sie es mithilfe der Informationen in [Grundlagen des PowerShell-Workflows](automation-powershell-workflow.md) und [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md) bearbeiten.

### <a name="create-a-runbook-in-the-azure-portal"></a>Erstellen eines Runbooks im Azure-Portal

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto.
2. Wählen Sie im Hub **Runbooks** aus, um die Liste der Runbooks zu öffnen.
3. Klicken Sie auf die Schaltfläche **Runbook hinzufügen** und anschließend auf **Neues Runbook erstellen**.
4. Geben Sie einen **Namen** für das Runbook ein, und wählen den [Typ](automation-runbook-types.md)aus. Der Runbook-Name muss mit einem Buchstaben beginnen und kann Buchstaben, Zahlen, Unterstriche und Bindestriche enthalten.
5. Klicken Sie auf **Erstellen** , um das Runbook zu erstellen und den Editor zu öffnen.

### <a name="create-a-runbook-with-powershell"></a>Erstellen eines Runbooks mit PowerShell

Sie können das Cmdlet [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) verwenden, um ein leeres [PowerShell-Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks) zu erstellen. Verwenden Sie den Parameter **Type**, um einen der vier Runbooktypen anzugeben.

Die folgenden Beispiele zeigen, wie Sie ein neues Automation-Anmeldeinformationsobjekt erstellen.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importieren eines Runbooks

Sie können ein neues Runbook in Azure Automation erstellen, indem Sie ein PowerShell-Skript, einen PowerShell-Workflow (.ps1-Erweiterung), ein exportiertes grafisches Runbook (.graphrunbook) oder ein Python 2-Skript (.py-Erweiterung) importieren.  Geben Sie den [Runbook-Typ](automation-runbook-types.md) an, der beim Import erstellt wird. Berücksichtigen Sie dabei die folgenden Aspekte.

* Eine `.graphrunbook`-Datei kann nur in ein neues [grafisches Runbook](automation-runbook-types.md#graphical-runbooks) importiert werden, und grafische Runbooks können nur aus einer `.graphrunbook`-Datei erstellt werden.
* Eine `.ps1`-Datei mit einem PowerShell-Workflow kann nur in ein [PowerShell-Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks) importiert werden.  Enthält die Datei mehrere PowerShell-Workflows, schlägt der Import fehl. Sie müssen jeden Workflow in einer eigenen Datei speichern und einzeln importieren.
* Eine `.ps1`-Datei, die keinen Workflow enthält, kann in ein [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks) oder ein [PowerShell-Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks) importiert werden.  Beim Importieren in ein PowerShell-Workflow-Runbook wird es in einen Workflow konvertiert, und Kommentare zu den vorgenommenen Änderungen werden in das Runbook aufgenommen.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>So importieren Sie ein Runbook aus einer Datei mit dem Azure-Portal

Mit dem folgenden Verfahren können eine Skriptdatei in Azure Automation importieren.  

> [!NOTE]
> Beachten Sie, dass Sie über das Portal nur eine PS1-Datei in ein PowerShell-Workflow-Runbook importieren können.

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto.
2. Wählen Sie im Hub **Runbooks** aus, um die Liste der Runbooks zu öffnen.
3. Klicken Sie auf die Schaltfläche **Runbook hinzufügen** und anschließend auf **Importieren**.
4. Klicken Sie auf **Runbook-Datei** , um die zu importierende Datei auszuwählen.
5. Wenn das Feld **Name** aktiviert ist, haben Sie die Möglichkeit, ihn zu ändern.  Der Runbook-Name muss mit einem Buchstaben beginnen und kann Buchstaben, Zahlen, Unterstriche und Bindestriche enthalten.
6. Der [Runbooktyp](automation-runbook-types.md) wird automatisch ausgewählt, Sie können den Typ jedoch unter Berücksichtigung der geltenden Einschränkungen ändern. 
7. Das neue Runbook wird in der Liste der Runbooks für das Automation-Konto angezeigt.
8. Sie müssen [das Runbook veröffentlichen](#publish-a-runbook) , bevor Sie es ausführen können.

> [!NOTE]
> Nach dem Import eines grafischen Runbooks oder eines grafischen PowerShell-Workflow-Runbooks können Sie diese Runbooks in den anderen Runbooktyp konvertieren. Sie können die Runbooks nicht in ein Textrunbook konvertieren.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>So importieren Sie ein Runbook aus einer Skriptdatei mit Windows PowerShell

Sie können das Cmdlet [Import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) verwenden, um eine Skriptdatei als PowerShell-Workflow-Runbookentwurf zu importieren. Wenn das Runbook bereits vorhanden ist, tritt beim Import ein Fehler auf, sofern Sie nicht den Parameter *-Force* verwenden.

Die folgenden Beispielbefehle zeigen, wie Sie eine Skriptdatei in ein Runbook importieren.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Testen eines Runbooks

Wenn Sie ein Runbook testen, wird die [Entwurfsversion](#publish-a-runbook) ausgeführt, und alle darin ausgeführten Aktionen werden abgeschlossen. Es wird kein Auftragsverlauf erstellt, aber die [Ausgabe](automation-runbook-output-and-messages.md#output-stream)- sowie die [Warnungs- und Fehlerdatenströme](automation-runbook-output-and-messages.md#message-streams) werden im Testausgabebereich angezeigt. Nachrichten an den [ausführlichen Datenstrom](automation-runbook-output-and-messages.md#message-streams) werden im Ausgabebereich nur angezeigt, wenn die Variable [$VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) auf „Continue“ festgelegt ist.

Auch wenn die Entwurfsversion ausgeführt wird, wird das Runbook trotzdem normal ausgeführt, und alle Aktionen werden auf die Ressourcen in der Umgebung angewendet. Aus diesem Grund sollten Sie Runbooks nur auf Ressourcen testen, die sich nicht in der Produktionsumgebung befinden.

Das Verfahren zum Testen der verschiedenen [Runbooktypen](automation-runbook-types.md) ist identisch, und es besteht kein Unterschied zwischen Tests mit dem Text-Editor und dem grafischen Editor im Azure-Portal.  

1. Öffnen Sie die Entwurfsversion des Runbooks entweder im [Text-Editor](automation-edit-textual-runbook.md) oder im [grafischen Editor](automation-graphical-authoring-intro.md).
1. Klicken Sie auf die Schaltfläche **Test**, um die Seite „Test“ zu öffnen.
1. Wenn das Runbook über Parameter verfügt, werden diese im linken Bereich aufgeführt. Hier können Sie die für den Test zu verwendenden Werte angeben.
1. Wenn Sie den Test auf einem [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), ausführen möchten, ändern Sie die **Testlaufeinstellungen** auf **Hybrid Worker**, und wählen Sie den Namen der Zielgruppe aus.  Andernfalls behalten Sie den Standardwert **Azure** bei, um den Test in der Cloud auszuführen.
1. Klicken Sie auf die Schaltfläche **Start** , um den Test zu starten.
1. Wenn das Runbook ein [PowerShell-Workflow](automation-runbook-types.md#powershell-workflow-runbooks) oder [grafisch](automation-runbook-types.md#graphical-runbooks) ist, können Sie es während des Tests mit den Schaltflächen unterhalb des Ausgabebereichs beenden oder anhalten. Wenn Sie das Runbook anhalten, wird die aktuelle Aktivität vor der Unterbrechung abgeschlossen. Nachdem das Runbook angehalten wurde, können Sie es beenden oder erneut starten.
1. Untersuchen Sie die Ausgabe des Runbooks im Ausgabebereich.

## <a name="publish-a-runbook"></a>Veröffentlichen eines Runbooks

Wenn Sie ein neues Runbooks erstellen oder importieren, müssen Sie es veröffentlichen, bevor Sie es ausführen können.  Jedes Runbook in Automation umfasst eine Entwurfsversion und eine veröffentlichte Version. Nur die veröffentlichte Version kann ausgeführt werden, und nur die Entwurfsversion kann bearbeitet werden. Die veröffentlichte Version bleibt von Änderungen an der Entwurfsversion unberührt. Wenn die Entwurfsversion zur Verfügung gestellt werden soll, können Sie sie veröffentlichen und auf diese Weise die veröffentlichte Version mit der Entwurfsversion überschreiben.

### <a name="azure-portal"></a>Azure-Portal

1. Öffnen Sie das Runbook im Azure-Portal.
2. Klicken Sie auf die Schaltfläche **Edit** .
3. Klicken Sie auf **Veröffentlichen**, und wählen Sie bei der Überprüfungsmeldung **Ja** aus.

### <a name="powershell"></a>PowerShell

Sie können das Cmdlet [Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) verwenden, um ein Runbook mit Windows PowerShell zu veröffentlichen. Die folgenden Beispielbefehle zeigen, wie Sie ein neues Beispiel-Runbook veröffentlichen.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Nächste Schritte

* Informationen dazu, wie Sie vom Katalog mit Runbooks und PowerShell-Modulen profitieren, finden Sie unter [Runbook und Modulkataloge für Azure Automation](automation-runbook-gallery.md).
* Weitere Informationen zum Bearbeiten von PowerShell- und PowerShell-Workflow-Runbooks mit einem Text-Editor finden Sie unter [Bearbeiten von Textrunbooks in Azure Automation](automation-edit-textual-runbook.md)
* Weitere Informationen zum Erstellen von grafischen Runbooks finden Sie unter [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md)
