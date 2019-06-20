---
title: Mein erstes grafisches Runbook in Azure Automation
description: Tutorial, in dem Sie sich mit dem Erstellen, Testen und Veröffentlichen eines einfachen grafischen Runbooks vertraut machen können.
keywords: Runbooks, Runbookvorlage, Runbookautomatisierung, Azure-Runbook
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/13/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: be811d0dc2ce2eca0b20ca12165eaf0799bd6b5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61077911"
---
# <a name="my-first-graphical-runbook"></a>Mein erstes grafisches Runbook

> [!div class="op_single_selector"]
> * [Grafisch](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-Workflow](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

In diesem Tutorial werden die Schritte zur Erstellung eines [grafischen Runbooks](automation-runbook-types.md#graphical-runbooks) in Azure Automation beschrieben. Wir beginnen mit einem einfachen Runbook, das getestet und veröffentlicht wird. Dabei erfahren Sie, wie Sie den Status des Runbookauftrags nachverfolgen. Anschließend wird das Runbook geändert, um damit tatsächlich Azure-Ressourcen zu verwalten. Im vorliegenden Fall soll ein virtueller Azure-Computer gestartet werden. Zum Abschluss des Tutorials werden Runbookparameter und bedingte Verknüpfungen hinzugefügt, um die Stabilität des Runbooks zu erhöhen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) registrieren.
* [Automation-Konto](automation-offering-get-started.md) dient zur Aufbewahrung des Runbooks und zur Authentifizierung gegenüber Azure-Ressourcen. Dieses Konto muss über die Berechtigung zum Starten und Beenden des virtuellen Computers verfügen.
* Einen virtuellen Azure-Computer. Da dieser Computer gestartet und beendet wird, sollte es sich nicht um einen virtuellen Computer in der Produktionsumgebung handeln.

## <a name="create-runbook"></a>Runbook erstellen

Erstellen Sie zunächst ein einfaches Runbook, das den Text *Hello World* ausgibt.

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto.

   Die Seite des Automation-Kontos bietet einen kurzen Überblick über die Ressourcen des Kontos. Sie sollten bereits über einige Objekte verfügen. Bei den meisten Objekten handelt es sich um die Module, die automatisch in einem neuen Automation-Konto enthalten sind. Darunter müsste sich auch das in den [Voraussetzungen](#prerequisites)erwähnte Anmeldeinformationsobjekt befinden.

2. Klicken Sie unter **PROZESSVERWALTUNG** auf **Runbooks**, um die Liste mit den Runbooks zu öffnen.
3. Erstellen Sie ein neues Runbook, indem Sie auf die Schaltfläche **+ Runbook hinzufügen** und anschließend auf **Neues Runbook erstellen** klicken.
4. Nennen Sie das Runbook *MyFirstRunbook-Graphical*.
5. Da Sie in diesem Fall ein [grafisches Runbook](automation-graphical-authoring-intro.md) erstellen, legen Sie den **Runbooktyp** auf **Grafisch** fest.<br> ![Neues Runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Klicken Sie auf **Erstellen** , um das Runbook zu erstellen und den grafischen Editor zu öffnen.

## <a name="add-activities"></a>Aktivitäten hinzufügen

Mithilfe des Bibliotheksteuerelements auf der linken Seite des Editors können Sie Aktivitäten auswählen und Ihrem Runbook hinzufügen. Sie fügen ein **Write-Output**-Cmdlet hinzu, um Text aus dem Runbook auszugeben.

1. Klicken Sie im Bibliotheksteuerelement auf das Textfeld für die Suche, und geben Sie **Write-Output**ein. Die folgende Abbildung zeigt die Suchergebnisse: <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Führen Sie einen Bildlauf zum Ende der Liste durch. Sie können entweder mit der rechten Maustaste auf **Write-Output** klicken und **Zur Canvas hinzufügen** auswählen oder auf die Auslassungspunkte neben dem Cmdlet klicken und **Zur Canvas hinzufügen** auswählen.
1. Klicken Sie in der Canvas auf die Aktivität **Write-Output**. Über diese Aktion wird die Seite für die Konfigurationssteuerung geöffnet, auf der Sie die Aktivität konfigurieren können.
1. Die **Bezeichnung** ist standardmäßig auf den Namen des Cmdlets festgelegt, kann aber in einen aussagekräftigeren Wert geändert werden. Legen Sie ihn auf *Write Hello World to output*fest.
1. Klicken Sie auf **Parameter** , um Parameterwerte für das Cmdlet anzugeben.

   Einige Cmdlets verfügen über mehrere Parametersätze, und Sie müssen auswählen, welchen Sie verwenden möchten. **Write-Output** besitzt in diesem Fall lediglich einen einzelnen Parametersatz, sodass Sie keinen auswählen müssen.

1. Wählen Sie den **InputObject** -Parameter aus. Dieser Parameter dient zum Angeben des Texts, der an den Ausgabestream gesendet werden soll.
1. Wählen Sie im Dropdownfeld **Datenquelle** die Option **PowerShell-Ausdruck** aus. Das Dropdownfeld **Datenquelle** enthält verschiedene Quellen, die Sie zum Auffüllen eines Parameterwerts verwenden.

   Sie können Ausgaben von solchen Quellen (etwa eine andere Aktivität, ein Automation-Objekt oder ein PowerShell-Ausdruck) verwenden. In diesem Fall soll lediglich *Hello World* ausgegeben werden. Hierzu können Sie einen PowerShell-Ausdruck verwenden und eine Zeichenfolge angeben.<br>

1. Geben Sie im Feld **Ausdruck** die Zeichenfolge *"Hello World"* ein, und klicken Sie dann zweimal auf **OK**, um zum Zeichenbereich zurückzukehren.
1. Klicken Sie auf **Speichern**, um das Runbook zu speichern.

## <a name="test-the-runbook"></a>Testen des Runbooks

Bevor Sie das Runbook für die Verwendung in der Produktionsumgebung veröffentlichen, sollten Sie sich vergewissern, dass es ordnungsgemäß funktioniert. Beim Testen eines Runbooks führen Sie die **Entwurfsversion** des Runbooks aus und sehen sich interaktiv die Ausgabe an.

1. Klicken Sie auf **Testbereich**, um die Seite „Test“ zu öffnen.
1. Klicken Sie auf **Starten** , um den Test zu starten. Andere Optionen dürften nicht zur Verfügung stehen.
1. Ein [Runbookauftrag](automation-runbook-execution.md) wird erstellt, und der dazugehörige Status wird angezeigt.

   Der Auftrag besitzt zunächst den Status *In der Warteschlange*. Hiermit wird angegeben, dass der Auftrag darauf wartet, dass in der Cloud ein Runbook Worker verfügbar wird. Wird der Auftrag von einem Worker übernommen, wechselt der Status zu *Wird gestartet* und anschließend zu *Wird ausgeführt*, wenn die Ausführung des Runbooks tatsächlich gestartet wurde.

1. Nach Abschluss des Runbookauftrags wird die Ausgabe angezeigt. In diesem Fall wird *Hello World* angezeigt.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Schließen Sie die Seite „Test“, um zum Zeichenbereich zurückzukehren.

## <a name="publish-and-start-the-runbook"></a>Veröffentlichen und Starten des Runbooks

Das erstellte Runbook befindet sich immer noch im Entwurfsmodus. Es muss veröffentlicht werden, damit es in der Produktionsumgebung ausgeführt werden kann. Beim Veröffentlichen eines Runbooks wird die vorhandene veröffentlichte Version durch die Entwurfsversion überschrieben. In diesem Fall ist noch keine veröffentlichte Version vorhanden, da Sie das Runbook gerade erst erstellt haben.

1. Klicken Sie auf **Veröffentlichen**, um das Runbook zu veröffentlichen, und bestätigen Sie den Vorgang mit **Ja**.
1. Wenn Sie nach links scrollen, um das Runbook auf der Seite **Runbooks** anzuzeigen, wird für das Runbook **Veröffentlicht** als **Erstellungsstatus** angezeigt.
1. Scrollen Sie zurück nach rechts, um die Seite für **MyFirstRunbook-Graphical** anzuzeigen.

   Mit den Optionen am oberen Rand können wir das Runbook starten, den Start für einen späteren Zeitpunkt planen oder einen [Webhook](automation-webhooks.md) erstellen, um den Start über einen HTTP-Aufruf zu ermöglichen.

1. Klicken Sie zum Starten des Runbooks auf **Starten** und anschließend auf **Ja**.
1. Für den erstellten Runbookauftrag wird eine Auftragsseite geöffnet. Vergewissern Sie sich, dass der **Auftragsstatus** den Wert **Abgeschlossen** hat.
1. Wenn der Runbookstatus *Abgeschlossen*lautet, klicken Sie auf **Ausgabe**. Die Seite **Ausgabe** wird geöffnet, und der Text *Hello World* wird in dem Bereich angezeigt.
1. Schließen Sie die Seite „Ausgabe“.
1. Klicken Sie auf **Alle Protokolle**, um den Bereich „Datenströme“ für den Runbookauftrag zu öffnen. Im Ausgabedatenstrom sollte nur *Hello World* angezeigt werden. Hier können aber auch andere Datenströme für einen Runbookauftrag (wie etwa „Ausführlich“ und „Fehler“) angezeigt werden, sofern das Runbook Schreibvorgänge dafür durchführt.
1. Schließen Sie die Seiten „Alle Protokolle“ und „Auftrag“, um zur Seite „MyFirstRunbook-Graphical“ zurückzukehren.
1. Wenn Sie alle Aufträge für das Runbook anzeigen möchten, schließen Sie die Seite **Auftrag**, und klicken Sie unter **RESSOURCEN** auf **Aufträge**. Dadurch werden alle von diesem Runbook erstellten Aufträge aufgeführt. Hier ist nur ein einzelner Auftrag aufgeführt, da Sie den Auftrag bislang erst einmal ausgeführt haben.
1. Wenn Sie auf diesen Auftrag klicken, wird wieder der Auftragsbereich geöffnet, den Sie sich beim Starten des Runbooks angesehen haben. So können Sie bereits ausgeführte Aufträge öffnen und Details zu jedem Auftrag anzeigen, der für ein bestimmtes Runbook erstellt wurde.

## <a name="create-variable-assets"></a>Erstellen variabler Ressourcen

Sie haben Ihr Runbook inzwischen zwar getestet und veröffentlicht, bislang ist es aber noch nicht sonderlich hilfreich. Sie möchten damit ja eigentlich Azure-Ressourcen verwalten. Bevor Sie das Runbook für die Authentifizierung konfigurieren, erstellen Sie eine Variable zum Speichern der Abonnement-ID, auf die Sie nach dem Einrichten der Authentifizierungsaktivität in Schritt 6 verweisen können. Das Einfügen eines Verweises in den Abonnementkontext ermöglicht Ihnen die einfache Nutzung mehrerer Abonnements. Kopieren Sie Ihre Abonnement-ID aus der Option „Abonnements“ im Navigationsbereich, bevor Sie fortfahren.

1. Klicken Sie auf der Seite „Automation-Konten“ unter **FREIGEGEBENE RESSOURCEN** auf **Variablen**.
1. Klicken Sie auf **Variable hinzufügen**.
1. Geben Sie auf der Seite „Neue Variable“ im Feld **Name** die Zeichenfolge **AzureSubscriptionId** und im Feld **Wert** Ihre Abonnement-ID ein. Behalten Sie unter **Typ** die Option *Zeichenfolge* sowie den Standardwert für **Verschlüsselung** bei.
1. Klicken Sie auf **Erstellen** , um die Variable zu erstellen.

## <a name="add-authentication"></a>Hinzufügen von Authentifizierung

Nachdem Sie nun über eine Variable für Ihre Abonnement-ID verfügen, können Sie Ihr Runbook für die Authentifizierung mit den Anmeldeinformationen für das ausführende Konto konfigurieren, die unter [Voraussetzungen](#prerequisites) angegeben sind. Fügen Sie hierzu der Canvas das **Objekt** für die Verbindung mit dem ausführenden Azure-Konto und das Cmdlet **Connect-AzureRmAccount** hinzu.

1. Navigieren Sie wieder zu Ihrem Runbook, und klicken Sie auf der Seite „MyFirstRunbook-Graphical“ auf **Bearbeiten**.
1. **Write Hello World to output** wird nicht mehr benötigt. Klicken Sie daher auf die Auslassungspunkte (...) und anschließend auf **Löschen**.
1. Erweitern Sie im Bibliotheksteuerelement die Optionen **OBJEKTE** und **Verbindungen**, und fügen Sie der Canvas das Element **AzureRunAsConnection** hinzu, indem Sie auf **Zur Canvas hinzufügen** klicken.
1. Geben Sie im Bibliotheksteuerelement in das Textfeld für die Suche die Zeichenfolge **Connect-AzureRmAccount** ein.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** ist jetzt ein Alias für **Connect-AzureRMAccount**. Sollte **Connect-AzureRMAccount** beim Durchsuchen der Bibliothekselemente nicht angezeigt werden, können Sie **Add-AzureRmAccount** verwenden oder die Module in Ihrem Automation-Konto aktualisieren.

1. Fügen Sie **Connect-AzureRmAccount** der Canvas hinzu.
1. Zeigen Sie auf **Get Run As Connection** , bis am unteren Rand der Form ein Kreis erscheint. Klicken Sie auf den Kreis, und ziehen Sie den Pfeil zu **Connect-AzureRmAccount**. Bei dem soeben erstellten Pfeil handelt es sich um eine *Verknüpfung*. Das Runbook wird mit **Get Run As Connection** gestartet und führt dann **Connect-AzureRmAccount** aus.<br> ![Verknüpfung zwischen Aktivitäten erstellen](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Wählen Sie in der Canvas **Connect-AzureRmAccount** aus, und geben Sie im Konfigurationssteuerungsbereich im Textfeld **Bezeichnung** den Text **Login to Azure** ein.
1. Klicken Sie auf **Parameter**, um die Seite „Konfiguration der Aktivitätsparameter“ anzuzeigen.
1. Da **Connect-AzureRmAccount** über mehrere Parametersätze verfügt, müssen Sie einen davon auswählen, um Parameterwerte angeben zu können. Klicken Sie auf **Parametersatz**, und wählen Sie dann den Parametersatz **ServicePrincipalCertificate** aus.
1. Nachdem Sie den Parametersatz ausgewählt haben, werden die Parameter auf der Seite „Konfiguration der Aktivitätsparameter“ angezeigt. Klicken Sie auf **APPLICATIONID**.<br> ![Azure RM-Kontoparameter hinzufügen](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Wählen Sie auf der Seite „Parameterwert“ für die **Datenquelle** die Option **Aktivitätsausgabe** und dann in der Liste **Get Run As Connection** aus. Geben Sie im Textfeld **Feldpfad** den Text **ApplicationId** ein, und klicken Sie auf **OK**. Geben Sie den Namen der Eigenschaft für den Feldpfad an, da die Aktivität ein Objekt mit mehreren Eigenschaften ausgibt.
1. Klicken Sie auf **CERTIFICATETHUMBPRINT**, und wählen Sie auf der Seite „Parameterwert“ für die **Datenquelle** die Option **Aktivitätsausgabe**. Wählen Sie in der Liste die Option **Get Run As Connection** aus, und geben Sie im Textfeld **Feldpfad** den Text **CertificateThumbprint** ein. Klicken Sie anschließend auf **OK**.
1. Klicken Sie auf **SERVICEPRINCIPAL**, und wählen Sie auf der Seite „Parameterwert“ für die **Datenquelle** die Option **ConstantValue** aus. Klicken Sie auf die Option **True** und dann auf **OK**.
1. Klicken Sie auf **TENANTID**, und wählen Sie auf der Seite „Parameterwert“ für die **Datenquelle** die **Aktivitätsausgabe**. Wählen Sie in der Liste die Option **Get Run As Connection** aus, und geben Sie im Textfeld **Feldpfad** den Text **TenantId** ein. Klicken Sie anschließend zweimal auf **OK**.
1. Geben Sie im Bibliotheksteuerelement in das Textfeld für die Suche die Zeichenfolge **Set-AzureRmContext** ein.
1. Fügen Sie **Set-AzureRmContext** dem Zeichenbereich hinzu.
1. Wählen Sie im Zeichenbereich die Option **Set-AzureRmContext** aus, und geben Sie im Konfigurationssteuerungsbereich im Feld **Bezeichnung** die Zeichenfolge **Specify Subscription Id** ein.
1. Klicken Sie auf **Parameter**, um die Seite „Konfiguration der Aktivitätsparameter“ anzuzeigen.
1. Da **Set-AzureRmContext** über mehrere Parametersätze verfügt, müssen Sie einen davon auswählen, um Parameterwerte angeben zu können. Klicken Sie auf **Parametersatz**, und wählen Sie anschließend den Parametersatz **SubscriptionId** aus.
1. Nachdem Sie den Parametersatz ausgewählt haben, werden die Parameter auf der Seite „Konfiguration der Aktivitätsparameter“ angezeigt. Klicken Sie auf **SubscriptionID**
1. Wählen Sie auf der Seite „Parameterwert“ für die **Datenquelle** die Option **Variablenasset** und in der Liste die Option **AzureSubscriptionId** aus. Klicken Sie anschließend zweimal auf **OK**.
1. Zeigen Sie auf **Login to Azure** , bis am unteren Rand der Form ein Kreis erscheint. Klicken Sie auf den Kreis, und ziehen Sie den Pfeil auf **Specify Subscription Id**.

Ihr Runbook sollte nun wie folgt aussehen: <br>![Konfiguration der Runbook-Authentifizierung](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="add-activity-to-start-a-vm"></a>Hinzufügen einer Aktivität zum Starten eines virtuellen Computers

In diesem Schritt fügen Sie eine Aktivität vom Typ **Start-AzureRmVM** hinzu, um einen virtuellen Computer zu starten. Sie können einen beliebigen virtuellen Computer in Ihrem Azure-Abonnement auswählen. Sie werden den Namen vorerst innerhalb des Cmdlets hartcodieren.

1. Geben Sie im Bibliotheksteuerelement in das Textfeld für die Suche die Zeichenfolge **Start-AzureRm** ein.
2. Fügen Sie **Start-AzureRmVM** dem Zeichenbereich hinzu, und ziehen Sie das Element anschließend unter **Specify Subscription Id**.
1. Zeigen Sie auf **Specify Subscription Id** , bis am unteren Rand der Form ein Kreis erscheint. Klicken Sie auf den Kreis, und ziehen Sie den Pfeil zu **Start-AzureRmVM**.
1. Wählen Sie **Start-AzureRmVM**aus. Klicken Sie auf **Parameter** und anschließend auf **Parametersatz**, um die Sätze für **Start-AzureRmVM** anzuzeigen. Wählen Sie den Parametersatz **ResourceGroupNameParameterSetName** aus. Neben **ResourceGroupName** und **Name** wird jeweils ein Ausrufezeichen angezeigt. Das bedeutet, dass es sich dabei um erforderliche Parameter handelt. Beachten Sie außerdem, dass für beide ein Zeichenfolgenwert erwartet wird.
1. Wählen Sie **Name**. Wählen Sie **PowerShell-Ausdruck** als **Datenquelle** aus, und geben Sie den Namen des virtuellen Computers (in doppelten Anführungszeichen) ein, der mit diesem Runbook gestartet werden soll. Klicken Sie auf **OK**.
1. Wählen Sie **ResourceGroupName**aus. Wählen Sie unter **PowerShell-Ausdruck** die Zeichenfolge **Datenquelle** aus, und geben Sie den Namen der Ressourcengruppe in doppelten Anführungszeichen ein. Klicken Sie auf **OK**.
1. Klicken Sie auf den Testbereich, um das Runbook zu testen.
1. Klicken Sie auf **Starten** , um den Test zu starten. Vergewissern Sie sich nach Abschluss des Tests, dass der virtuelle Computer gestartet wurde.

Ihr Runbook sollte nun wie folgt aussehen: <br>![Konfiguration der Runbook-Authentifizierung](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="add-additional-input-parameters"></a>Hinzufügen zusätzlicher Eingabeparameter

Das Runbook startet den virtuellen Computer derzeit in der Ressourcengruppe, die Sie im Cmdlet **Start-AzureRmVM** angegeben haben. Es wäre aber hilfreich, wenn wir beides beim Starten des Runbooks angeben könnten. Zu diesem Zweck werden dem Runbook als Nächstes Eingabeparameter hinzugefügt.

1. Öffnen Sie den grafischen Editor, indem Sie im Bereich **MyFirstRunbook-Graphical** auf **Bearbeiten** klicken.
1. Klicken Sie auf **Eingabe und Ausgabe** und anschließend auf **Eingabe hinzufügen**, um den Eingabeparameterbereich für das Runbook zu öffnen.
1. Geben Sie als *Name* die Zeichenfolge **VMName**an. Behalten Sie für **Typ** die Einstellung *string* bei, aber ändern Sie **Obligatorisch** in *Ja*. Klicken Sie auf **OK**.
1. Erstellen Sie einen zweiten obligatorischen Eingabeparameter namens *ResourceGroupName*, und klicken Sie anschließend auf **OK**, um den Bereich **Eingabe und Ausgabe** zu schließen.<br> ![Runbook-Eingabeparameter](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Wählen Sie die Aktivität **Start-AzureRmVM** aus, und klicken Sie anschließend auf **Parameter**.
1. Ändern Sie die **Datenquelle** für **Name** in **Runbookeingabe**, und wählen Sie anschließend **VMName** aus.
1. Ändern Sie die **Datenquelle** für **ResourceGroupName** in **Runbookeingabe**, und wählen Sie anschließend **ResourceGroupName** aus.<br> ![Start-AzureVM – Parameter](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Speichern Sie das Runbook, und öffnen Sie den Testbereich. Nun können Sie Werte für die beiden Eingabevariablen angeben, die im Test verwendet werden.
1. Schließen Sie den Testbereich.
1. Klicken Sie auf **Veröffentlichen** , um die neue Version des Runbooks zu veröffentlichen.
1. Beenden Sie den virtuellen Computer, den Sie im vorherigen Schritt gestartet haben.
1. Klicken Sie auf **Starten** , um das Runbook zu starten. Geben Sie **VMName** und **ResourceGroupName** für den virtuellen Computer ein, den Sie starten möchten.
1. Vergewissern Sie sich nach Abschluss des Runbooks, dass der virtuelle Computer gestartet wurde.

## <a name="create-a-conditional-link"></a>Erstellen einer bedingten Verknüpfung

In diesem Schritt passen Sie das Runbook so an, dass nur dann versucht wird, den virtuellen Computer zu starten, wenn dieser noch nicht gestartet ist. Zu diesem Zweck fügen Sie dem Runbook ein **Get-AzureRmVM**-Cmdlet hinzu, das den Instanzebenenstatus des virtuellen Computers abruft. Anschließend fügen Sie ein PowerShell-Workflow-Codemodul namens **Get Status** mit einem PowerShell-Codeausschnitt hinzu, mit dem ermittelt wird, ob der virtuelle Computer ausgeführt wird oder beendet ist. Eine bedingte Verknüpfung des Moduls **Get Status** führt **Start-AzureRmVM** nur dann aus, wenn der aktuelle Ausführungszustand angibt, dass der virtuelle Computer beendet ist. Schließlich geben Sie mithilfe des PowerShell-Cmdlets „Write-Output“ noch eine Nachricht aus, die darüber informiert, ob der virtuelle Computer erfolgreich gestartet wurde.

1. Öffnen Sie **MyFirstRunbook-Graphical** im grafischen Editor.
1. Entfernen Sie die Verknüpfung zwischen **Specify Subscription Id** und **Start-AzureRmVM**, indem Sie auf die Verknüpfung klicken und die *ENTF-TASTE* drücken.
1. Geben Sie im Bibliotheksteuerelement in das Textfeld für die Suche die Zeichenfolge **Get-AzureRm** ein.
1. Fügen Sie dem Zeichenbereich **Get-AzureRmVM** hinzu.
1. Wählen Sie **Get-AzureRmVM** und anschließend **Parametersatz** aus, um die Sätze für **Get-AzureRmVM** anzuzeigen. Wählen Sie den Parametersatz **GetVirtualMachineInResourceGroupNameParamSet** aus. Neben **ResourceGroupName** und **Name** wird jeweils ein Ausrufezeichen angezeigt. Das bedeutet, dass es sich dabei um erforderliche Parameter handelt. Beachten Sie außerdem, dass für beide ein Zeichenfolgenwert erwartet wird.
1. Wählen Sie unter **Datenquelle** für **Name** die Option **Runbookeingabe** und anschließend **VMName** aus. Klicken Sie auf **OK**.
1. Wählen Sie unter **Datenquelle** für **ResourceGroupName** die Option **Runbookeingabe** und anschließend **ResourceGroupName** aus. Klicken Sie auf **OK**.
1. Wählen Sie unter **Datenquelle** als **Status** die Option **Konstanter Wert** aus, und klicken Sie anschließend auf **True**. Klicken Sie auf **OK**.
1. Erstellen Sie eine Verknüpfung zwischen **Specify Subscription Id** und **Get-AzureRmVM**.
1. Erweitern Sie im Bibliotheksteuerelement den Bereich **Runbooksteuerung**, und fügen Sie dem Zeichenbereich **Code** hinzu.  
1. Erstellen Sie eine Verknüpfung zwischen **Get-AzureRmVM** und **Code**.  
1. Klicken Sie auf **Code**, und ändern Sie im Konfigurationsbereich die Bezeichnung in **Get Status**.
1. Wählen Sie den Parameter **Code** aus, um die Seite **Code-Editor** anzuzeigen.  
1. Fügen Sie im Code-Editor den folgenden Codeausschnitt ein:

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Erstellen Sie eine Verknüpfung zwischen **Get Status** und **Start-AzureRmVM**.<br> ![Runbook mit Codemodul](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Wählen Sie die Verknüpfung aus, und legen Sie **Bedingung anwenden** im Konfigurationsbereich auf **Ja** fest. Beachten Sie, dass die Verknüpfung nun als gestrichelte Linie dargestellt wird. Das bedeutet, dass die Aktivität nur ausgeführt wird, wenn die Bedingung erfüllt ist.  
1. Geben Sie für den **Bedingungsausdruck** Folgendes ein: *$ActivityOutput['Get Status'] -eq "Stopped"* . Nun wird **Start-AzureRmVM** nur dann ausgeführt, wenn der virtuelle Computer beendet ist.
1. Erweitern Sie im Bibliotheksteuerelement den Knoten **Cmdlets** und anschließend **Microsoft.PowerShell.Utility**.
1. Fügen Sie dem Zeichenbereich zweimal **Write-Output** hinzu.
1. Klicken Sie im ersten Steuerelement vom Typ **Write-Output** auf **Parameter**, und legen Sie die **Bezeichnung** auf *Notify VM Started* fest.
1. Legen Sie die **Datenquelle** für **InputObject** auf **PowerShell-Ausdruck** fest, und geben Sie den Ausdruck *"$VMName successfully started."* ein.
1. Klicken Sie im zweiten Steuerelement vom Typ **Write-Output** auf **Parameter**, und legen Sie die **Bezeichnung** auf *Notify VM Start Failed* fest.
1. Legen Sie die **Datenquelle** für **InputObject** auf **PowerShell-Ausdruck** fest, und geben Sie den Ausdruck *"$VMName could not start."* ein.
1. Verknüpfen Sie **Start AzureRmVM** mit **Notify VM Started** und **Notify VM Start Failed**.
1. Wählen Sie die Verknüpfung mit **Notify VM Started** aus, und legen Sie **Bedingung anwenden** auf **True** fest.
1. Geben Sie als **Bedingungsausdruck** Folgendes ein: *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*. Dieses Write-Output-Steuerelement wird nur ausgeführt, wenn der virtuelle Computer erfolgreich gestartet wurde.
1. Wählen Sie die Verknüpfung mit **Notify VM Start Failed** aus, und legen Sie **Bedingung anwenden** auf **True** fest.
1. Geben Sie als **Bedingungsausdruck** Folgendes ein: *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*. Dieses Write-Output-Steuerelement wird nur ausgeführt, wenn der virtuelle Computer nicht erfolgreich gestartet wurde. Ihr Runbook sollte nun wie in der folgenden Abbildung aussehen: <br> ![Runbook mit Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Speichern Sie das Runbook, und öffnen Sie den Testbereich.
1. Wenn Sie nun das Runbook starten, während der virtuellen Computer beendet ist, wird der virtuelle Computer gestartet.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur grafischen Inhaltserstellung finden Sie unter [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md)
* Erste Schritte mit PowerShell-Runbooks werden in [Mein erstes PowerShell-Runbook](automation-first-runbook-textual-powershell.md)
* Die ersten Schritte mit PowerShell-Workflow-Runbooks sind unter [Mein erstes PowerShell-Workflow-Runbook](automation-first-runbook-textual.md)


