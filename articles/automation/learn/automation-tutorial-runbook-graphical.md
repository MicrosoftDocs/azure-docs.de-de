---
title: Erstellen eines grafischen Runbooks in Azure Automation
description: Dieser Artikel vermittelt Ihnen, wie Sie ein einfaches grafisches Runbook in Azure Automation erstellen, testen und veröffentlichen.
keywords: Runbooks, Runbookvorlage, Runbookautomatisierung, Azure-Runbook
services: automation
ms.subservice: process-automation
ms.date: 09/15/2020
ms.topic: tutorial
ms.openlocfilehash: 1b6c02778b0ee790d81c713283e653058c29c153
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92899807"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Tutorial: Erstellen eines grafischen Runbooks

In diesem Tutorial werden die Schritte zur Erstellung eines [grafischen Runbooks](../automation-runbook-types.md#graphical-runbooks) in Azure Automation beschrieben. Sie können grafische Runbooks und grafische PowerShell-Workflow-Runbooks im grafischen Editor im Azure-Portal erstellen und bearbeiten. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines einfachen grafischen Runbooks
> * Testen und Veröffentlichen des Runbooks
> * Ausführen des Runbookauftrags und Nachverfolgen seines Status
> * Aktualisieren des Runbooks zum Starten eines virtuellen Azure-Computers mit Runbookparametern und bedingten Links

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free) registrieren.
* [Automation-Konto](../index.yml) dient zur Aufbewahrung des Runbooks und zur Authentifizierung gegenüber Azure-Ressourcen. Dieses Konto muss über die Berechtigung zum Starten und Beenden des virtuellen Computers verfügen.
* Einen virtuellen Azure-Computer. Da Sie diesen Computer starten und beenden, sollte es sich nicht um eine Produktions-VM handeln.
* Abhängig von den verwendeten Cmdlets können Sie bei Bedarf [Azure-Module importieren](../shared-resources/modules.md) oder [Module aktualisieren](../automation-update-azure-modules.md).

## <a name="step-1---create-runbook"></a>Schritt 1: Erstellen eines Runbooks

Erstellen Sie zunächst ein einfaches Runbook, das den Text `Hello World` ausgibt.

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto.

    Die Seite des Automation-Kontos bietet einen kurzen Überblick über die Ressourcen des Kontos. Sie sollten bereits über einige Objekte verfügen. Bei den meisten Objekten handelt es sich um die in einem neuen Automation-Konto automatisch enthaltenen Module. Sie sollten auch über das Anmeldeobjekt verfügen, das Ihrem Abonnement zugeordnet ist.

2. Wählen Sie unter **Prozessautomatisierung** die Option **Runbooks** aus, um die Liste der Runbooks zu öffnen.

3. Erstellen Sie ein neues Runbook, indem Sie **Runbook erstellen** auswählen.

4. Nennen Sie das Runbook **MyFirstRunbook-Graphical** .

5. In diesem Fall erstellen Sie ein [grafisches Runbook](../automation-graphical-authoring-intro.md). Wählen Sie **Grafisch** für den **Runbooktyp** aus.

    ![Neues Runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)

6. Klicken Sie auf **Erstellen** , um das Runbook zu erstellen und den grafischen Editor zu öffnen.

## <a name="step-2---add-activities"></a>Schritt 2: Hinzufügen von Aktivitäten

Mithilfe des Bibliotheksteuerelements auf der linken Seite des Editors können Sie Aktivitäten auswählen und Ihrem Runbook hinzufügen. Sie fügen ein `Write-Output`-Cmdlet hinzu, um Text aus dem Runbook auszugeben.

1. Klicken Sie im Bibliothekssteuerelement in das Suchfeld, und geben Sie `write-output` ein. Die folgende Abbildung zeigt die Suchergebnisse.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. Führen Sie einen Bildlauf zum Ende der Liste durch. Klicken Sie mit der rechten Maustaste auf **Write-Output** , und wählen Sie **Zur Canvas hinzufügen** aus. Alternativ können Sie auf die Auslassungspunkte (...) neben dem Namen des Cmdlets klicken und dann **Zur Canvas hinzufügen** auswählen.

3. Klicken Sie in der Canvas auf die Aktivität **Write-Output** . Über diese Aktion wird die Seite für die Konfigurationssteuerung geöffnet, auf der Sie die Aktivität konfigurieren können.

4. Das Feld **Bezeichnung** ist standardmäßig auf den Namen des Cmdlets festgelegt, kann aber in einen aussagekräftigeren Wert geändert werden. Ändern Sie diesen Wert in `Write Hello World to output`.

5. Klicken Sie auf **Parameter** , um Parameterwerte für das Cmdlet anzugeben.

   Einige Cmdlets verfügen über mehrere Parametersätze, und Sie müssen auswählen, welche verwendet werden. `Write-Output` besitzt in diesem Fall lediglich einen einzelnen Parametersatz.

6. Wählen Sie den Parameter `InputObject` aus. Dies ist der Parameter, mit dem Sie den Text angeben, der an den Ausgabedatenstrom gesendet werden soll.

7. Das Dropdownmenü **Datenquelle** enthält Quellen, die Sie zum Auffüllen eines Parameterwerts verwenden können. Wählen Sie in diesem Menü **PowerShell-Ausdruck** aus.

   Sie können Ausgaben von solchen Quellen (etwa eine andere Aktivität, ein Automation-Objekt oder ein PowerShell-Ausdruck) verwenden. In diesem Fall soll lediglich „`Hello World`“ ausgegeben werden. Hierzu können Sie einen PowerShell-Ausdruck verwenden und eine Zeichenfolge angeben.

8. Geben Sie in das Feld **Ausdruck** „`"Hello World"`“ ein, und klicken Sie dann zweimal auf **OK** , um zur Canvas zurückzukehren.

9. Klicken Sie auf **Speichern** , um das Runbook zu speichern.

## <a name="step-3---test-the-runbook"></a>Schritt 3: Testen des Runbooks

Bevor Sie das Runbook für die Verwendung in der Produktionsumgebung veröffentlichen, sollten Sie es testen, um sicherzustellen, dass es ordnungsgemäß funktioniert. Beim Testen eines Runbooks führen Sie die Entwurfsversion aus und sehen sich interaktiv die Ausgabe an.

1. Klicken Sie auf **Testbereich** , um den Bereich „Test“ zu öffnen.

2. Klicken Sie auf **Starten** , um den Test zu starten. Andere Optionen dürften nicht zur Verfügung stehen.

3. Beachten Sie, dass ein [Runbookauftrag](../automation-runbook-execution.md) erstellt und der zugehörige Status im Bereich angezeigt wird.

   Der Auftrag weist zunächst den Status `Queued` auf, der angibt, dass der Auftrag darauf wartet, dass in der Cloud ein Runbook Worker verfügbar wird. Der Status wird in `Starting` geändert, wenn ein Worker den Auftrag beansprucht. Schließlich ändert sich der Status in `Running`, wenn die Ausführung des Runbooks beginnt.

4. Nach Abschluss des Runbookauftrags wird die Ausgabe im Bereich „Test“ angezeigt. In diesem Fall wird `Hello World` angezeigt.

    ![Ausgabe des Runbooks „Hallo Welt“](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. Schließen Sie den Testbereich, um zum Zeichenbereich zurückzukehren.

## <a name="step-4---publish-and-start-the-runbook"></a>Schritt 4: Veröffentlichen und Starten des Runbooks

Das erstellte Runbook befindet sich immer noch im Entwurfsmodus. Es muss veröffentlicht werden, damit es in der Produktionsumgebung ausgeführt werden kann. Beim Veröffentlichen eines Runbooks wird die vorhandene veröffentlichte Version durch die Entwurfsversion überschrieben. In diesem Fall ist noch keine veröffentlichte Version vorhanden, da Sie das Runbook gerade erst erstellt haben.

1. Klicken Sie auf **Veröffentlichen** , um das Runbook zu veröffentlichen, und bestätigen Sie den Vorgang mit **Ja** .

2. Scrollen Sie nach links, um das Runbook auf der Seite „Runbooks“ anzuzeigen, und beachten Sie, dass der Wert **Erstellungsstatus** als **Veröffentlicht** angezeigt wird.

3. Scrollen Sie zurück nach rechts, um die Seite für **MyFirstRunbook-Graphical** anzuzeigen.

   Mit den Optionen im oberen Bereich können Sie das Runbook jetzt starten, den Start für einen späteren Zeitpunkt planen oder einen [Webhook](../automation-webhooks.md) erstellen, um das Runbook über einen HTTP-Aufruf starten zu können.

4. Klicken Sie zum Starten des Runbooks auf **Starten** und anschließend auf **Ja** .

5. Ein Auftragsbereich für den soeben erstellten Runbookauftrag wird angezeigt. Vergewissern Sie sich, dass im Feld **Auftragsstatus** der Wert **Abgeschlossen** angezeigt wird.

6. Klicken Sie auf **Ausgabe** , um die Ausgabeseite zu öffnen, auf der „`Hello World`“ angezeigt wird.

7. Schließen Sie die Seite „Ausgabe“.

8. Klicken Sie auf **Alle Protokolle** , um den Bereich „Datenströme“ für den Runbookauftrag zu öffnen. Im Ausgabestream sollte nur `Hello World` angezeigt werden.

    Beachten Sie, dass im Bereich „Streams“ auch andere Streams für einen Runbookauftrag, etwa ausführliche Streams und Fehlerstreams, angezeigt werden können, sofern das Runbook in diese schreibt.

9. Schließen Sie den Datenstrom- und den Auftragsbereich, um zum Bereich „MyFirstRunbook-Graphical“ zurückzukehren.

10. Wählen Sie **Aufträge** unter **Ressourcen** aus, um alle Aufträge für das Runbook anzuzeigen. Auf der Seite „Aufträge“ werden alle von Ihrem Runbook erstellten Aufträge aufgelistet. Es sollte nur ein Auftrag aufgeführt sein, da Sie den Auftrag bislang erst einmal ausgeführt haben.

11. Klicken Sie auf den Namen des Auftrags, um den Bereich „Auftrag“ zu öffnen, der beim Starten des Runbooks angezeigt wurde. In diesem Bereich können Sie die Details zu jedem Auftrag anzeigen, der für das Runbook erstellt wurde.

## <a name="step-5---create-variable-assets"></a>Schritt 5: Erstellen variabler Ressourcen

Sie haben Ihr Runbook inzwischen zwar getestet und veröffentlicht, bislang ist es aber zum Verwalten von Azure-Ressourcen noch nicht sonderlich hilfreich. Bevor Sie das Runbook für die Authentifizierung konfigurieren, müssen Sie eine Variable erstellen, die die Abonnement-ID enthält, eine Aktivität zur Authentifizierung einrichten und dann auf die Variable verweisen. Das Einfügen eines Verweises in den Abonnementkontext ermöglicht Ihnen die einfache Nutzung mehrerer Abonnements.

1. Kopieren Sie Ihre Abonnement-ID aus der Option **Abonnements** im Navigationsbereich.

2. Klicken Sie auf der Seite „Automation-Konten“ unter **Freigegebene Ressourcen** auf **Variablen** .

3. Klicken Sie auf **Variable hinzufügen** .

4. Nehmen Sie auf der Seite „Neue Variable“ die folgenden Einstellungen in den dafür vorgesehenen Feldern vor.

    * **Name** : Geben Sie `AzureSubscriptionId` ein.
    * **Wert** : Geben Sie Ihre Abonnement-ID ein.
    * **Typ** : Lassen Sie die Zeichenfolge ausgewählt.
    * **Verschlüsselung** : Verwenden Sie den Standardwert.

5. Klicken Sie auf **Erstellen** , um die Variable zu erstellen.

## <a name="step-6---add-authentication"></a>Schritt 6: Hinzufügen der Authentifizierung

Nachdem Sie nun über eine Variable für Ihre Abonnement-ID verfügen, können Sie das Runbook so konfigurieren, dass die Authentifizierung mit den Anmeldeinformationen für das ausführende Konto für Ihr Abonnement erfolgt. Fügen Sie dazu die Verbindung für das ausführende Azure-Konto als Objekt hinzu. Sie müssen auch die Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) und [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) zur Canvas hinzufügen.

>[!NOTE]
>Für PowerShell-Runbooks sind `Add-AzAccount` und `Add-AzureRMAccount` Aliase für `Connect-AzAccount`. Beachten Sie, dass diese Aliase für Ihre grafischen Runbooks nicht verfügbar sind. Ein grafisches Runbook kann nur `Connect-AzAccount` selbst verwenden.

1. Navigieren Sie zu Ihrem Runbook, und klicken Sie auf der Seite „MyFirstRunbook-Graphical“ auf **Bearbeiten** .

2. Der Eintrag „`Write Hello World to output`“ ist nicht mehr erforderlich. Klicken Sie einfach auf die Auslassungspunkte, und wählen Sie **Löschen** aus.

3. Erweitern Sie im Bibliothekssteuerelement **OBJEKTE** und dann **Verbindungen** . Fügen Sie `AzureRunAsConnection` zur Canvas hinzu, indem Sie auf **Zur Canvas hinzufügen** klicken.

4. Benennen Sie `AzureRunAsConnection` in `Get Run As Connection` um.

5. Geben Sie im Bibliotheksteuerelement in das Suchfeld `Connect-AzAccount` ein.

6. Fügen Sie der Canvas `Connect-AzAccount` hinzu.

7. Zeigen Sie auf `Get Run As Connection`, bis am unteren Rand der Form ein Kreis erscheint. Klicken Sie auf den Kreis, und ziehen Sie den Pfeil zu `Connect-AzAccount`, um eine Verknüpfung herzustellen. Das Runbook wird mit `Get Run As Connection` gestartet und führt dann `Connect-AzAccount` aus.

    ![Verknüpfung zwischen Aktivitäten erstellen](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. Klicken Sie in der Canvas auf `Connect-AzAccount`. Geben Sie im Konfigurationssteuerungsbereich **Anmelden bei Azure** in das Feld **Bezeichnung** ein.

9. Klicken Sie auf **Parameter** , um die Seite „Konfiguration der Aktivitätsparameter“ anzuzeigen.

10. Das Cmdlet `Connect-AzAccount` verfügt über mehrere Parametersätze, und Sie müssen einen auswählen, bevor Sie Parameterwerte bereitstellen können. Klicken Sie auf **Parametersatz** , und wählen Sie dann **ServicePrincipalCertificateWithSubscriptionId** aus.

11. Die Parameter für diesen Parametersatz werden auf der Seite „Konfiguration der Aktivitätsparameter“ angezeigt. Klicken Sie auf **APPLICATIONID** .

    ![Azure-Kontoparameter hinzufügen](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. Nehmen Sie auf der Seite „Parameterwert“ die folgenden Einstellungen vor, und klicken Sie dann auf **OK** .

   * **Datenquelle** : Wählen Sie **Aktivitätsausgabe** aus.
   * Datenquellenliste: Klicken Sie auf **Get Automation Connection** (Automatisierungsverbindung abrufen).
   * **Feldpfad** : Geben Sie `ApplicationId` ein. Sie geben den Namen der Eigenschaft für den Feldpfad an, da die Aktivität ein Objekt mit mehreren Eigenschaften ausgibt.

13. Klicken Sie auf **CERTIFICATETHUMBPRINT** , nehmen Sie auf der Seite „Parameterwert“ die folgenden Einstellungen vor, und klicken Sie dann auf **OK** .

    * **Datenquelle** : Wählen Sie **Aktivitätsausgabe** aus.
    * Datenquellenliste: Klicken Sie auf **Get Automation Connection** (Automatisierungsverbindung abrufen).
    * **Feldpfad** : Geben Sie `CertificateThumbprint` ein.

14. Klicken Sie auf **SERVICEPRINCIPAL** , und wählen Sie auf der Seite „Parameterwert“ für das Feld **Datenquelle** die Option **ConstantValue** aus. Klicken Sie auf die Option **True** und dann auf **OK** .

15. Klicken Sie auf **TENANTID** , und nehmen Sie auf der Seite „Parameterwert“ die folgenden Einstellungen vor. Wenn Sie fertig sind, klicken Sie zweimal auf **OK** .

    * **Datenquelle** : Wählen Sie **Aktivitätsausgabe** aus.
    * Datenquellenliste: Klicken Sie auf **Get Automation Connection** (Automatisierungsverbindung abrufen).
    * **Feldpfad** : Geben Sie `TenantId` ein.

16. Geben Sie im Bibliotheksteuerelement in das Suchfeld `Set-AzContext` ein.

17. Fügen Sie der Canvas `Set-AzContext` hinzu.

18. Klicken Sie in der Canvas auf `Set-AzContext`. Geben Sie im Konfigurationssteuerungsbereich `Specify Subscription Id` in das Feld **Bezeichnung** ein.

19. Klicken Sie auf **Parameter** , um die Seite „Konfiguration der Aktivitätsparameter“ anzuzeigen.

20. Das Cmdlet `Set-AzContext` verfügt über mehrere Parametersätze, und Sie müssen einen auswählen, bevor Sie Parameterwerte bereitstellen können. Klicken Sie zunächst auf **Parametersatz** und dann auf **SubscriptionId** .

21. Die Parameter für diesen Parametersatz werden auf der Seite „Konfiguration der Aktivitätsparameter“ angezeigt. Klicken Sie auf **SubscriptionID** .

22. Wählen Sie auf der Seite „Parameterwert“ für das Feld **Datenquelle** die Option **Variablenasset** und in der Liste die Option **AzureSubscriptionId** aus. Wenn Sie fertig sind, klicken Sie zweimal auf **OK** .

23. Zeigen Sie auf `Login to Azure`, bis am unteren Rand der Form ein Kreis erscheint. Klicken Sie auf den Kreis, und ziehen Sie den Pfeil zu `Specify Subscription Id`. Ihr Runbook sollte nun wie folgt aussehen:

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="Screenshot: Runbook nach dem Ziehen des Pfeils zu „Specify Subscription ID“":::

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Schritt 7: Hinzufügen einer Aktivität zum Starten eines virtuellen Computers

Jetzt müssen Sie eine Aktivität vom Typ `Start-AzVM` hinzufügen, um eine VM zu starten. Sie können einen beliebigen virtuellen Computer in Ihrem Azure-Abonnement auswählen. Sie werden seinen Namen vorerst im Cmdlet [Start-AzVM](/powershell/module/az.compute/start-azvm) hartcodieren.

1. Geben Sie im Bibliotheksteuerelement in das Suchfeld `Start-Az` ein.

2. Fügen Sie der Canvas `Start-AzVM` hinzu, klicken Sie darauf und ziehen Sie es unter `Specify Subscription Id`.

3. Zeigen Sie auf `Specify Subscription Id`, bis am unteren Rand der Form ein Kreis erscheint. Klicken Sie auf den Kreis, und ziehen Sie den Pfeil zu `Start-AzVM`.

4. Wählen Sie `Start-AzVM`aus. Klicken Sie auf **Parameter** und anschließend auf **Parametersatz** , um die Sätze für die Aktivität anzuzeigen.

5. Klicken Sie auf den Parametersatz **ResourceGroupNameParameterSetName** . Die Felder **ResourceGroupName** und **Name** verfügen über nebenstehende Ausrufezeichen, um anzuzeigen, dass es sich um erforderliche Parameter handelt. Beachten Sie, dass beide Felder Zeichenfolgenwerte erwarten.

6. Wählen Sie **Name** . Wählen Sie **PowerShell-Ausdruck** für das Feld **Datenquelle** aus. Geben Sie für den virtuellen Computer, mit dem Sie dieses Runbook starten, den Computernamen in doppelten Anführungszeichen ein. Klicken Sie auf **OK** .

7. Wählen Sie **ResourceGroupName** aus. Verwenden Sie den Wert **PowerShell-Ausdruck** für das Feld **Datenquelle** , und geben Sie den Namen der Ressourcengruppe in doppelten Anführungszeichen ein. Klicken Sie auf **OK** .

8. Klicken Sie auf den **Testbereich** , um das Runbook zu testen.

9. Klicken Sie auf **Starten** , um den Test zu starten. Vergewissern Sie sich nach Abschluss, dass die VM gestartet wurde. Ihr Runbook sollte nun wie folgt aussehen:

    ![Runbook-Ausgabe für „Start-AzVM“](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Schritt 8: Hinzufügen zusätzlicher Eingabeparameter

Ihr Runbook startet die VM derzeit in der Ressourcengruppe, die Sie im Cmdlet `Start-AzVM` angegeben haben. Das Runbook wird hilfreicher sein, wenn Sie beim Starten des Runbooks sowohl den Namen als auch die Ressourcengruppe angeben. Zu diesem Zweck fügen wir dem Runbook Eingabeparameter hinzu.

1. Öffnen Sie den grafischen Editor, indem Sie auf der Seite „MyFirstRunbook-Graphical“ auf **Bearbeiten** klicken.

2. Klicken Sie auf **Eingabe und Ausgabe** und anschließend auf **Eingabe hinzufügen** , um den Eingabeparameterbereich für das Runbook zu öffnen.

3. Nehmen Sie die folgenden Einstellungen in den vorgesehenen Feldern vor, und klicken Sie dann auf **OK** .
   * **Name** : Geben Sie `VMName` an.
   * **Typ** : Behalten Sie die Einstellung „String“ (Zeichenfolge) bei.
   * **Obligatorisch** : Ändern Sie den Wert in **Yes** (Ja).

4. Erstellen Sie einen zweiten obligatorischen Eingabeparameter namens `ResourceGroupName`, und klicken Sie anschließend auf **OK** , um den Bereich „Eingabe und Ausgabe“ zu schließen.

    ![Runbook-Eingabeparameter](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. Klicken Sie auf die Aktivität `Start-AzVM` und anschließend auf **Parameter** .

6. Ändern Sie das Feld **Datenquelle** für **Name** in **Runbookeingabe** . Wählen Sie dann **VMName** aus.

7. Ändern Sie das Feld **Datenquelle** für **ResourceGroupName** in **Runbookeingabe** , und wählen Sie anschließend **ResourceGroupName** aus.

    ![Start-AzVM-Parameter](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Speichern Sie das Runbook, und öffnen Sie den Testbereich. Nun können Sie Werte für die beiden Eingabevariablen angeben, die im Test verwendet werden.

9. Schließen Sie den Testbereich.

10. Klicken Sie auf **Veröffentlichen** , um die neue Version des Runbooks zu veröffentlichen.

11. Beenden Sie den virtuellen Computer, den Sie zuvor gestartet haben.

12. Klicken Sie auf **Starten** , um das Runbook zu starten. Geben Sie die Werte `VMName` und `ResourceGroupName` für die VM ein, die Sie starten möchten.

13. Wenn das Runbook abgeschlossen ist, vergewissern Sie sich, dass die VM gestartet wurde.

## <a name="step-9---create-a-conditional-link"></a>Schritt 9: Erstellen einer bedingten Verknüpfung

In diesem Schritt können Sie nun das Runbook so anpassen, dass nur dann versucht wird, den virtuellen Computer zu starten, wenn dieser noch nicht gestartet ist. Fügen Sie dazu das Cmdlet [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) hinzu, das den Status der VM auf Instanzebene abruft. Anschließend können Sie ein PowerShell-Workflow-Codemodul namens `Get Status` mit einem PowerShell-Codeausschnitt hinzufügen, mit dem ermittelt wird, ob die VM ausgeführt wird oder beendet ist. Eine bedingte Verknüpfung des Moduls `Get Status` führt `Start-AzVM` nur dann aus, wenn der aktuelle Ausführungszustand angibt, dass die VM beendet ist. Am Ende dieses Vorgangs gibt Ihr Runbook mit dem Cmdlet `Write-Output` eine Meldung aus, die Sie darüber informiert, ob die VM erfolgreich gestartet wurde.

1. Öffnen Sie **MyFirstRunbook-Graphical** im grafischen Editor.

2. Entfernen Sie die Verknüpfung zwischen `Specify Subscription Id` und `Start-AzVM`, indem Sie darauf klicken und dann auf **Löschen** klicken.

3. Geben Sie im Bibliotheksteuerelement in das Suchfeld `Get-Az` ein.

4. Fügen Sie der Canvas `Get-AzVM` hinzu.

5. Klicken Sie zunächst auf `Get-AzVM` und anschließend auf **Parametersatz** , um die Sätze für das Cmdlet anzuzeigen.

6. Wählen Sie den Parametersatz **GetVirtualMachineInResourceGroupNameParamSet** aus. Die Felder **ResourceGroupName** und **Name** verfügen über nebenstehende Ausrufezeichen, um anzuzeigen, dass sie erforderliche Parameter angeben. Beachten Sie, dass beide Felder Zeichenfolgenwerte erwarten.

7. Wählen Sie unter **Datenquelle** für **Name** die Option **Runbookeingabe** und anschließend **VMName** aus. Klicken Sie auf **OK** .

8. Wählen Sie unter **Datenquelle** für **ResourceGroupName** die Option **Runbookeingabe** und anschließend **ResourceGroupName** aus. Klicken Sie auf **OK** .

9. Wählen Sie unter **Datenquelle** als **Status** die Option **Konstanter Wert** und anschließend **True** aus. Klicken Sie auf **OK** .

10. Erstellen Sie eine Verknüpfung von `Specify Subscription Id` zu `Get-AzVM`.

11. Erweitern Sie im Bibliotheksteuerelement den Bereich **Runbooksteuerung** , und fügen Sie der Canvas **Code** hinzu.  

12. Erstellen Sie eine Verknüpfung von `Get-AzVM` zu `Code`.  

13. Klicken Sie auf `Code`, und ändern Sie im Konfigurationsbereich die Bezeichnung in **Get Status** (Status abrufen).

14. Klicken Sie auf `Code`, um die Seite „Code-Editor“ anzuzeigen.  

15. Fügen Sie den folgenden Codeausschnitt auf der Editorseite ein.

    ```powershell
    $Statuses = $ActivityOutput['Get-AzVM'].Statuses
    $StatusOut = ""
    foreach ($Status in $Statuses) {
      if($Status.Code -eq "Powerstate/running")
        {$StatusOut = "running"}
      elseif ($Status.Code -eq "Powerstate/deallocated")
        {$StatusOut = "stopped"}
    }
    $StatusOut
    ```

16. Erstellen Sie eine Verknüpfung von `Get Status` zu `Start-AzVM`.

    ![Runbook mit Codemodul](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. Wählen Sie die Verknüpfung aus, und legen Sie **Bedingung anwenden** im Konfigurationsbereich auf **Yes** (Ja) fest. Beachten Sie, dass die Verknüpfung nun als gestrichelte Linie dargestellt wird. Das bedeutet, dass die Aktivität nur ausgeführt wird, wenn die Bedingung erfüllt ist.  

18. Geben Sie für **Bedingungsausdruck** den Wert `$ActivityOutput['Get Status'] -eq "Stopped"` ein. `Start-AzVM` wird jetzt nur noch ausgeführt, wenn die VM beendet wird.

19. Erweitern Sie im Bibliotheksteuerelement den Knoten **Cmdlets** und anschließend **Microsoft.PowerShell.Utility** .

20. Fügen Sie der Canvas zweimal `Write-Output` hinzu.

21. Klicken Sie für das erste Steuerelement vom Typ `Write-Output` auf **Parameter** , und legen Sie die **Bezeichnung** auf **Notify VM Started** (Benachrichtigen, dass VM gestartet wurde) fest.

22. Legen Sie die **Datenquelle** für **InputObject** auf **PowerShell-Ausdruck** fest, und geben Sie den Ausdruck `$VMName successfully started.` ein.

23. Klicken Sie im zweiten Steuerelement vom Typ `Write-Output` auf **Parameter** , und legen Sie die **Bezeichnung** auf **Notify VM Start Failed** (Benachrichtigen, dass Start der VM fehlgeschlagen ist) fest.

24. Legen Sie die **Datenquelle** für **InputObject** auf **PowerShell-Ausdruck** fest, und geben Sie den Ausdruck `$VMName could not start` ein.

25. Erstellen Sie Verknüpfungen von `Start-AzVM` zu `Notify VM Started` und `Notify VM Start Failed`.

26. Klicken Sie auf die Verknüpfung mit `Notify VM Started`, und ändern Sie **Bedingung anwenden** in „true“.

27. Geben Sie für den **Bedingungsausdruck** den Wert `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` ein. Dieses `Write-Output`-Steuerelement wird jetzt nur ausgeführt, wenn die VM erfolgreich gestartet wurde.

28. Klicken Sie auf die Verknüpfung mit `Notify VM Start Failed`, und ändern Sie **Bedingung anwenden** in „true“.

29. Geben Sie für das Feld **Bedingungsausdruck** den Wert `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` ein. Dieses `Write-Output`-Steuerelement wird jetzt nur ausgeführt, wenn die VM nicht erfolgreich gestartet wurde. Ihr Runbook sollte nun wie in der folgenden Abbildung aussehen:

    ![Runbook mit Write-Output](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Speichern Sie das Runbook, und öffnen Sie den Testbereich.

31. Wenn Sie nun das Runbook starten, während der virtuellen Computer beendet ist, sollte der Computer gestartet werden.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur grafischen Erstellung finden Sie unter [Erstellen eines grafischen Runbooks in Azure Automation](../automation-graphical-authoring-intro.md).
* Informationen zu den ersten Schritten mit PowerShell-Runbooks finden Sie unter [Erstellen eines PowerShell-Runbooks](automation-tutorial-runbook-textual-powershell.md).
* Informationen zu den ersten Schritten mit PowerShell-Workflow-Runbooks finden Sie unter [Erstellen eines PowerShell-Workflow-Runbooks](automation-tutorial-runbook-textual.md).
* Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation).
