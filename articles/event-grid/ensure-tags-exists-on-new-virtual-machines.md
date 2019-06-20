---
title: Integration von Azure Automation mit Event Grid | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie beim Erstellen eines neuen virtuellen Computers automatisch ein Tag hinzufügen und eine Benachrichtigung an Microsoft Teams senden.
keywords: Automatisierung, Runbook, Teams, Event Grid, virtueller Computer, VM
services: automation
author: eamonoreilly
manager: ''
ms.service: automation
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 05/10/2019
ms.author: eamono
ms.openlocfilehash: 9f99ce5862850c2453e9e72241fff77fe091616f
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521428"
---
# <a name="tutorial-integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Tutorial: Integration von Azure Automation mit Event Grid und Microsoft Teams

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Importieren Sie ein Event Grid-Beispielrunbook.
> * Erstellen Sie einen optionalen Microsoft Teams-Webhook.
> * Erstellen Sie einen Webhook für das Runbook.
> * Erstellen Sie ein Event Grid-Abonnement.
> * Erstellen Sie einen virtuellen Computer, der das Runbook auslöst.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Um dieses Tutorial abzuschließen, ist ein [Azure Automation-Konto](../automation/automation-offering-get-started.md) erforderlich, um das vom Azure Event Grid-Abonnement ausgelöste Runbook aufzunehmen.

* Das `AzureRM.Tags`-Modul muss in Ihr Automation-Konto geladen werden. Informationen zum Importieren von Modulen in Azure Automation finden Sie unter [Importieren von Modulen in Azure Automation](../automation/automation-update-azure-modules.md).

## <a name="import-an-event-grid-sample-runbook"></a>Importieren eines Event Grid-Beispielrunbooks

1. Wählen Sie Ihr Automation-Konto und dann die Seite **Runbooks** aus.

   ![Auswählen von Runbooks](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. Wählen Sie die Schaltfläche **Katalog durchsuchen** aus.

3. Suchen Sie nach **Event Grid** und wählen Sie **Integration von Azure Automation mit Event Grid** aus.

    ![Importieren des Katalogrunbooks](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. Wählen Sie **Importieren** aus und nennen Sie es **Watch-VMWrite**.

5. Nach dem Import wählen Sie **Bearbeiten** aus, um die Runbookquelle anzuzeigen. 
6. Aktualisieren Sie die Zeile 74 im Skript, sodass `Tag` anstelle von `Tags` verwendet wird.

    ```powershell
    Update-AzureRmVM -ResourceGroupName $VMResourceGroup -VM $VM -Tag $Tag | Write-Verbose
    ```
7. Wählen Sie die Schaltfläche **Veröffentlichen** aus.

## <a name="create-an-optional-microsoft-teams-webhook"></a>Erstellen eines optionalen Microsoft Teams-Webhooks

1. Wählen Sie in Microsoft-Teams die Option **Weitere Optionen** neben dem Kanalnamen und dann **Connectors** aus.

    ![Microsoft Teams-Verbindungen](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. Scrollen Sie durch die Liste der Connectors zu **Eingehender Webhook** und wählen Sie **Hinzufügen** aus.

3. Geben Sie **AzureAutomationIntegration** für den Namen ein und wählen Sie **Erstellen** aus.

4. Kopieren Sie die Webhook-URL in die Zwischenablage und speichern Sie sie. Die Webhook-URL wird zum Senden von Informationen an Microsoft Teams verwendet.

5. Wählen Sie **Fertig** aus, um den Webhook zu speichern.

## <a name="create-a-webhook-for-the-runbook"></a>Erstellen eines Webhooks für das Runbook

1. Öffnen Sie das Runbook „Watch-VMWrite“.

2. Wählen Sie **Webhooks** und dann die Schaltfläche **Webhook hinzufügen** aus.

3. Geben Sie **WatchVMEventGrid** für den Namen ein. Kopieren Sie die URL in die Zwischenablage und speichern Sie sie.

    ![Konfigurieren des Webhooknamens](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. Wählen Sie **Parameter konfigurieren und Einstellungen ausführen** aus und geben Sie Microsoft Teams-Webhook-URL für **CHANNELURL** ein. Lassen Sie **WEBHOOKDATA** leer.

    ![Konfigurieren der Webhookparameter](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. Wählen Sie **Erstellen** aus, um den Webhook des Automation-Runbooks zu erstellen.

## <a name="create-an-event-grid-subscription"></a>Erstellen eines Event Grid-Abonnements

1. Wählen Sie auf der Übersichtsseite **Automation-Konto** die Option **Event Grid** aus.

    ![Auswählen von Event Grid](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. Klicken Sie auf **+ Ereignisabonnement**.

3. Konfigurieren Sie das Abonnement mit den folgenden Informationen:
    1. Wählen Sie unter **Thementyp** die Option **Azure-Abonnements** aus.
    2. Deaktivieren Sie das Kontrollkästchen **Alle Ereignistypen abonnieren**.
    3. Geben Sie **AzureAutomation** für den Namen ein.
    4. Deaktivieren Sie in der Dropdownliste **Definierte Ereignistypen** alle Optionen bis auf **Resource Write Success** (Ressourcenschreiberfolg).

        > [!NOTE] 
        > Azure Resource Manager unterscheidet zurzeit nicht zwischen „Erstellen“ und „Aktualisieren“, sodass die Implementierung dieses Tutorials für alle „Microsoft.Resources.ResourceWriteSuccess“-Ereignisse in Ihrem Azure-Abonnement zu einer großen Anzahl von Aufrufen führen kann.
    1. Wählen Sie unter **Endpunkttyp** die Option **Webhook** aus.
    2. Klicken Sie auf **Endpunkt auswählen**. Fügen Sie auf der daraufhin geöffneten Seite **Webhook auswählen** die Webhook-URL ein, die Sie für das Runbook „Watch-VMWrite“ erstellt haben.
    3. Geben Sie unter **FILTER** das Abonnement und die Ressourcengruppe ein, in denen Sie nach den neu erstellten virtuellen Computern suchen möchten. Diese sollte wie folgt aussehen: `/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`.

4. Wählen Sie **Erstellen** aus, um das Event Grid-Abonnement zu speichern.

## <a name="create-a-vm-that-triggers-the-runbook"></a>Erstellen eines virtuellen Computers, der das Runbook auslöst

1. Legen Sie einen neuen virtuellen Computer in der Ressourcengruppe an, die Sie im Event Grid-Abonnementpräfixfilter angegeben haben.

2. Das Runbook „Watch-VMWrite“ sollte aufgerufen und dem virtuellen Computer ein neues Tag hinzugefügt werden.

    ![VM-Tag](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. Eine neue Nachricht wird an den Microsoft Teams-Kanal gesendet.

    ![Microsoft Teams-Benachrichtigung](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial richten Sie die Integration zwischen Event Grid und Automation ein. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Importieren Sie ein Event Grid-Beispielrunbook.
> * Erstellen Sie einen optionalen Microsoft Teams-Webhook.
> * Erstellen Sie einen Webhook für das Runbook.
> * Erstellen Sie ein Event Grid-Abonnement.
> * Erstellen Sie einen virtuellen Computer, der das Runbook auslöst.

> [!div class="nextstepaction"]
> [Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Event Grid](../event-grid/custom-event-quickstart.md)
