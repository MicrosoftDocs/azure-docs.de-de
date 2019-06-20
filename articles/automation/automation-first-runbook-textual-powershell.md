---
title: Mein erstes PowerShell-Runbook in Azure Automation
description: Tutorial, in dem Sie sich mit dem Erstellen, Testen und Veröffentlichen eines einfachen PowerShell-Runbooks vertraut machen können.
keywords: Azure PowerShell, Tutorial zu PowerShell-Skripts, PowerShell-Automation
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b08e1489cf337360e838a3b5d5531fa2d4c0073b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60694259"
---
# <a name="my-first-powershell-runbook"></a>Mein erstes PowerShell-Runbook

> [!div class="op_single_selector"]
> * [Grafisch](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-Workflow](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Dieses Tutorial führt Sie durch die Erstellung eines [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks) in Azure Automation. Sie beginnen mit einem einfachen Runbook, das Sie testen und veröffentlichen. Dabei wird erläutert, wie Sie den Status des Runbookauftrags nachverfolgen. Anschließend wird das Runbook geändert, um damit tatsächlich Azure-Ressourcen zu verwalten. Im vorliegenden Fall soll ein virtueller Azure-Computer gestartet werden. Abschließend fügen Sie Runbookparameter hinzu, um die Stabilität des Runbooks zu erhöhen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchführen dieses Tutorials benötigen Sie Folgendes:

* Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) registrieren.
* [Automation-Konto](automation-quickstart-create-account.md) dient zur Aufbewahrung des Runbooks und zur Authentifizierung gegenüber Azure-Ressourcen. Dieses Konto muss über die Berechtigung zum Starten und Beenden des virtuellen Computers verfügen.
* Einen virtuellen Azure-Computer. Da dieser Computer gestartet und beendet wird, sollte es sich nicht um einen virtuellen Computer in der Produktionsumgebung handeln.

## <a name="create-new-runbook"></a>Erstellen eines neuen Runbooks

Erstellen Sie zunächst ein einfaches Runbook, das den Text *Hello World* ausgibt.

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto.
2. Klicken Sie unter **Prozessautomatisierung** auf **Runbooks**, um die Liste mit den Runbooks zu öffnen.
3. Erstellen Sie ein neues Runbook, indem Sie auf die Schaltfläche **+ Runbook hinzufügen** und anschließend auf **Neues Runbook erstellen** klicken.
4. Nennen Sie das Runbook *MyFirstRunbook-PowerShell*.
5. Da Sie hier ein [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks) erstellen, wählen Sie als **Runbooktyp** die Option **PowerShell** aus.
6. Klicken Sie auf **Erstellen** , um das Runbook zu erstellen und den Text-Editor zu öffnen.

## <a name="add-code-to-the-runbook"></a>Hinzufügen von Code zum Runbook

Sie können entweder direkt Code in das Runbook eingeben, oder Sie wählen Cmdlets, Runbooks und Objekte aus dem Bibliotheksteuerelement aus und fügen diese mit entsprechenden Parametern zum Runbook hinzu. In dieser exemplarischen Vorgehensweise geben Sie den Code direkt in das Runbook ein.

1. Das Runbook ist derzeit leer. Geben Sie *Write-Output "Hello World."* im Textbereich des Skripts ein.

   ![Hallo Welt](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  

2. Klicken Sie auf **Speichern**, um das Runbook zu speichern.

## <a name="step-3---test-the-runbook"> </a> Testen des Runbooks

Bevor Sie das Runbook für die Verwendung in der Produktionsumgebung veröffentlichen, sollten Sie sich vergewissern, dass es ordnungsgemäß funktioniert. Beim Testen eines Runbooks führen Sie die **Entwurfsversion** des Runbooks aus und sehen sich interaktiv die Ausgabe an.

1. Klicken Sie auf **Testbereich** , um den Testbereich zu öffnen.
2. Klicken Sie auf **Starten** , um den Test zu starten. Andere Optionen dürften nicht zur Verfügung stehen.
3. Ein [Runbookauftrag](automation-runbook-execution.md) wird erstellt, und der dazugehörige Status wird angezeigt.

   Der Auftrag besitzt zunächst den Status *In der Warteschlange*. Hiermit wird angegeben, dass der Auftrag darauf wartet, dass in der Cloud ein Runbook Worker verfügbar wird. Wird der Auftrag von einem Worker übernommen, wechselt der Status zu *Wird gestartet* und anschließend zu *Wird ausgeführt*, wenn die Ausführung des Runbooks tatsächlich gestartet wurde.  

4. Nach Abschluss des Runbookauftrags wird die Ausgabe angezeigt. In Ihrem Fall sollte *Hello World* angezeigt werden.

   ![Ausgabe des Testbereichs](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  

5. Schließen Sie den Testbereich, um zum Zeichenbereich zurückzukehren.

## <a name="publish-and-start-the-runbook"></a>Veröffentlichen und Starten des Runbooks

Das erstellte Runbook befindet sich immer noch im Entwurfsmodus. Es muss veröffentlicht werden, damit es in der Produktionsumgebung ausgeführt werden kann.  Beim Veröffentlichen eines Runbooks wird die vorhandene veröffentlichte Version durch die Entwurfsversion überschrieben. In diesem Fall ist noch keine veröffentlichte Version vorhanden, da Sie das Runbook gerade erst erstellt haben.

1. Klicken Sie auf **Veröffentlichen**, um das Runbook zu veröffentlichen, und bestätigen Sie den Vorgang mit **Ja**.
1. Wenn Sie nun nach links scrollen, um das Runbook im Bereich **Runbooks** anzuzeigen, wird der **Erstellungsstatus** des Runbooks als **Veröffentlicht** angezeigt.
1. Führen Sie wieder einen Bildlauf nach rechts durch, um den Bereich für **MyFirstRunbook-PowerShell**anzuzeigen.  
   Mit den Optionen am oberen Rand können wir das Runbook starten, das Runbook anzeigen, den Start für einen späteren Zeitpunkt planen oder einen [Webhook](automation-webhooks.md) erstellen, um den Start über einen HTTP-Aufruf zu ermöglichen.
1. Im nächsten Schritt wird das Runbook gestartet. Klicken Sie hierzu auf **Start** und dann auf der Seite „Runbook starten“ auf **OK**.
1. Eine Auftragsseite für den soeben erstellten Runbookauftrag wird angezeigt. Dieser Bereich kann zwar geschlossen werden, lassen Sie ihn in diesem Fall aber geöffnet, um den Status des Auftrags verfolgen zu können.
1. Der Auftragsstatus wird unter **Auftragszusammenfassung** angezeigt und entspricht den Statusoptionen, die Sie bereits beim Testen des Runbooks gesehen haben.

   ![API-Zusammenfassung](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Wenn der Runbookstatus *Abgeschlossen* lautet, klicken Sie unter **Übersicht** auf **Ausgabe**. Der Ausgabebereich wird geöffnet, und der Text *Hello World* wird angezeigt.

   ![Auftragsausgabe](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Schließen Sie die Seite „Ausgabe“.
1. Klicken Sie auf **Alle Protokolle**, um den Bereich „Datenströme“ für den Runbookauftrag zu öffnen. Im Ausgabedatenstrom sollte nur *Hallo Welt* angezeigt werden. Hier können aber auch andere Datenströme für einen Runbookauftrag (wie etwa „Ausführlich“ und „Fehler“) angezeigt werden, sofern das Runbook Schreibvorgänge dafür durchführt.

   ![Alle Protokolle](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Schließen Sie die Datenstrom- und die Auftragsseite, um zur Seite „MyFirstRunbook-PowerShell“ zurückzukehren.
1. Klicken Sie unter **Details** auf **Aufträge**, um den Auftragsbereich für dieses Runbook zu öffnen. Diese Seite enthält alle von diesem Runbook erstellten Aufträge. Hier ist nur ein einzelner Auftrag aufgeführt, da Sie den Auftrag bislang erst einmal ausgeführt haben.

   ![Auftragsliste](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  

1. Wenn Sie auf diesen Auftrag klicken, wird wieder der Auftragsbereich geöffnet, den Sie sich beim Starten des Runbooks angesehen haben. Mit dieser Aktion können Sie bereits ausgeführte Aufträge öffnen und Details zu jedem Auftrag anzeigen, der für ein bestimmtes Runbook erstellt wurde.

## <a name="add-authentication-to-manage-azure-resources"></a>Hinzufügen von Authentifizierungsfunktionen für die Verwaltung von Azure-Ressourcen

Sie haben Ihr Runbook inzwischen zwar getestet und veröffentlicht, bislang ist es aber noch nicht sonderlich hilfreich. Sie möchten damit ja eigentlich Azure-Ressourcen verwalten. Dies ist jedoch nicht möglich, es sei denn, Sie haben eine Authentifizierung über eine ausführende Verbindung, die automatisch erstellt wird, wenn Sie Ihr Automatisierungskonto erstellen. Sie verwenden die ausführende Verbindung mit dem **Connect-AzureRmAccount**-Cmdlet. Wenn Sie Ressourcen über mehrere Abonnements verwalten, müssen Sie den Parameter **-AzureRmContext** zusammen mit [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext) verwenden.

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process
   
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. Öffnen Sie den Text-Editor, indem Sie auf der Seite „MyFirstRunbook-PowerShell“ auf **Bearbeiten** klicken.
1. Die Zeile **Write-Output** wird nicht mehr benötigt, also löschen Sie sie.
1. Geben Sie den folgenden Code ein (oder fügen Sie ihn ein). Dieser Code ist für die Authentifizierung bei Ihrem ausführenden Konto für Automation zuständig:

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** und **Login-AzureRmAccount** sind nun Aliase für **Connect-AzureRMAccount**. Wenn das Cmdlet **Connect-AzureRMAccount** nicht vorhanden ist, können Sie **Add-AzureRmAccount** oder **Login-AzureRmAccount** verwenden, oder Sie können Ihre Module in Ihrem Automation-Konto auf die neuesten Versionen aktualisieren.

1. Klicken Sie auf den **Testbereich**, um das Runbook zu testen.
1. Klicken Sie auf **Starten** , um den Test zu starten. Nach Abschluss des Tests erhalten Sie in der Regel eine Ausgabe wie in der Abbildung unten mit allgemeinen Informationen aus Ihrem Konto. Diese Ausgabe bestätigt, dass das ausführende Konto gültig ist.

   ![Authentifizieren](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Hinzufügen von Code zum Starten eines virtuellen Computers

Nachdem das Runbook jetzt in Ihrem Azure-Abonnement authentifiziert ist, können Sie Ressourcen verwalten. Sie fügen einen Befehl zum Starten eines virtuellen Computers hinzu. Sie können einen beliebigen virtuellen Computer in Ihrem Azure-Abonnement auswählen. Sie werden den Namen vorerst im Runbook hartcodieren.

1. Geben Sie nach *Connect-AzureRmAccount* den Code *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'* ein, und geben Sie den Namen und den Ressourcengruppennamen des virtuellen Computers an, der gestartet werden soll.  

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Speichern Sie das Runbook, und klicken Sie dann auf den **Testbereich**, um es zu testen.
1. Klicken Sie auf **Starten** , um den Test zu starten. Vergewissern Sie sich nach Abschluss des Tests, dass der virtuelle Computer gestartet wurde.

## <a name="add-an-input-parameter"></a>Eingabeparameter hinzufügen

Ihr Runbook startet zwar jetzt den virtuellen Computer, den Sie im Runbook hartcodiert haben, es wäre aber praktischer, wenn Sie den virtuellen Computer beim Start des Runbooks angeben. Zu diesem Zweck fügen Sie dem Runbook Eingabeparameter hinzu.

1. Fügen Sie dem Runbook Parameter für *VMName* und *ResourceGroupName* hinzu, und verwenden Sie diese Variablen mit dem Cmdlet **Start-AzureRmVM**, wie im folgenden Beispiel gezeigt:

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Speichern Sie das Runbook, und öffnen Sie den Testbereich. Sie können nun Werte für die beiden Eingabevariablen angeben, die im Test verwendet werden.
1. Schließen Sie den Testbereich.
1. Klicken Sie auf **Veröffentlichen** , um die neue Version des Runbooks zu veröffentlichen.
1. Beenden Sie den virtuellen Computer, den Sie im vorherigen Schritt gestartet haben.
1. Klicken Sie auf **OK**, um das Runbook zu starten. Geben Sie **VMName** und **ResourceGroupName** für den virtuellen Computer ein, den Sie starten möchten.<br><br> ![Parameter übergeben](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
1. Vergewissern Sie sich nach Abschluss des Runbooks, dass der virtuelle Computer gestartet wurde.

## <a name="differences-from-powershell-workflow"></a>Unterschiede zu PowerShell-Workflow

PowerShell-Runbooks verfügen über den gleichen Lebenszyklus, die gleichen Funktionen und die gleiche Verwaltung wie PowerShell-Workflow-Runbooks, aber es gibt auch einige Unterschiede und Einschränkungen:

1. PowerShell-Runbooks werden im Vergleich zu PowerShell-Workflow-Runbooks schneller ausgeführt, weil sie über keinen Kompilierungsschritt verfügen.
2. PowerShell-Workflow-Runbooks unterstützen Prüfpunkte. Mithilfe von Prüfpunkten lassen sich PowerShell-Workflow-Runbooks von einem beliebigen Punkt aus fortsetzen. PowerShell-Runbooks können nur vom Anfang fortgesetzt werden.
3. PowerShell-Workflow-Runbooks unterstützen parallele und serielle Ausführung. PowerShell-Runbooks können Befehle nur seriell ausführen.
4. In einem PowerShell-Workflow-Runbook können eine Aktivität, ein Befehl oder ein Skriptblock einen eigenen Runspace haben. In einem PowerShell-Runbook wird der gesamte Inhalt eines Skripts in einem einzigen Runspace ausgeführt. Außerdem gibt es auch einige [syntaktische Unterschiede](https://technet.microsoft.com/magazine/dn151046.aspx) zwischen einem nativen PowerShell-Runbook und einem PowerShell-Workflow-Runbook.

## <a name="next-steps"></a>Nächste Schritte

* Informationen über die ersten Schritte mit grafischen Runbooks finden Sie unter [Mein erstes grafisches Runbook](automation-first-runbook-graphical.md)
* Die ersten Schritte mit PowerShell-Workflow-Runbooks sind unter [Mein erstes PowerShell-Workflow-Runbook](automation-first-runbook-textual.md)
* Informationen über die verschiedenen Runbooktypen, ihre Vorteile und Einschränkungen finden Sie unter [Azure Automation-Runbooktypen](automation-runbook-types.md)
* Weitere Informationen zur PowerShell-Skriptunterstützung finden Sie unter [Native PowerShell Script Support in Azure Automation (Native PowerShell-Skriptunterstützung in Azure Automation)](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
