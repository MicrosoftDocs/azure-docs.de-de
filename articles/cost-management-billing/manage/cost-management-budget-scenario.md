---
title: Budgetszenario zur Abrechnungs- und Kostenverwaltung in Azure
description: Erfahren Sie, wie Sie mit der Azure-Automatisierung virtuelle Computer basierend auf bestimmten Budgetschwellenwerten herunterfahren.
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 1706fd69f58e4dee4a7ed963fb22cb63aec026fa
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593394"
---
# <a name="manage-costs-with-azure-budgets"></a>Verwalten von Kosten mit Azure-Budgets

Kostenkontrolle ist eine wichtige Komponente zur Maximierung des Nutzens Ihrer Investition in der Cloud. Es gibt mehrere Szenarien, in denen Kostensichtbarkeit, Berichterstellung und kostenbasierte Orchestrierung für fortgesetzte Geschäftsvorgänge entscheidend sind. Zur Unterstützung dieser einzelnen Szenarien stehen die [Azure Cost Management-APIs](/rest/api/consumption/) zur Verfügung. Die-APIs bieten Nutzungsdetails, mit deren Hilfe Sie die Kosten präzise auf Instanzebene anzeigen können.

Budgets werden häufig als Teil der Kostenkontrolle verwendet. Budgets können in Azure festgelegt werden. Beispielsweise können Sie Ihre Budgetansicht auf Grundlage von Abonnement, Ressourcengruppen oder einer Sammlung von Ressourcen konzentrieren. Sie können jedoch nicht nur die Budget-API verwenden, um sich per E-Mail benachrichtigen zu lassen, wenn ein Budgetschwellenwert erreicht wird, sondern auch die [Azure Monitor-Aktionsgruppen](../../azure-monitor/alerts/action-groups.md) nutzen, um einen orchestrierten Satz von Aktionen auszulösen, der aus einem Budgetereignis resultiert.

In einem häufigen Budgetszenario eines Kunden, der eine nicht kritische Workload ausführt, möchte der Kunde die Verwaltung an einem Budget orientieren und auch die Kosten der monatlichen Rechnung prognostizieren. Dieses Szenario erfordert eine kostenbasierte Orchestrierung von Ressourcen, die Teil der Azure-Umgebung sind. In diesem Szenario wird ein Monatsbudget von 1.000 USD für das Abonnement festgelegt. Außerdem werden Benachrichtigungsschwellenwerte festgelegt, um einige Orchestrierungen auszulösen. Dieses Szenario beginnt mit einem Kostenschwellenwert von 80%, bei dem alle virtuellen Computer in der Ressourcengruppe beendet werden **Optional**. Dann werden bei einem Kostenschwellenwert von 100% alle VM-Instanzen beendet.

Um dieses Szenario zu konfigurieren, führen Sie die folgenden Aktionen mithilfe der Schritte in den einzelnen Abschnitten dieses Tutorials durch.

Die in diesem Tutorial enthaltenen Aktionen ermöglichen Ihnen Folgendes:

- Erstellen eines Azure Automation-Runbooks, um VMs mithilfe von Webhooks zu beenden.
- Erstellen einer Azure-Logik-App, die basierend auf dem Budgetschwellenwert ausgelöst wird, und Aufrufen des Runbooks mit den entsprechenden Parametern.
- Erstellen einer Azure Monitor-Aktionsgruppe, die konfiguriert wird, um bei Erreichen des Budgetschwellenwerts die Azure-Logik-App auszulösen.
- Erstellen des Azure-Budgets mit den gewünschten Schwellenwerten und dessen Verknüpfung mit der Aktionsgruppe

## <a name="create-an-azure-automation-runbook"></a>Erstellen eines Azure Automation-Runbooks

[Azure Automation](../../automation/automation-intro.md) ist ein Dienst, mit dem Sie die meisten Ihrer Ressourcenverwaltungsaufgaben in ein Skript schreiben und diese Aufgaben entweder geplant oder bedarfsgesteuert ausführen können. Im Rahmen dieses Szenarios erstellen Sie ein [Azure Automation-Runbook](../../automation/automation-runbook-types.md) zum Beenden von VMs. Sie verwenden zum Erstellen dieses Szenarios das grafische Runbook [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) aus dem [Katalog](../../automation/automation-runbook-gallery.md). Wenn Sie dieses Runbook in Ihr Azure-Konto importieren und veröffentlichen, können Sie VMs beenden, wenn ein Budgetschwellenwert erreicht wird.

### <a name="create-an-azure-automation-account"></a>Erstellen eines Azure Automation-Kontos

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie in der linken oberen Ecke von Azure die Schaltfläche **Ressource erstellen** aus.
3. Wählen Sie **Verwaltungstools** > **Automatisierung**.
   > [!NOTE]
   > Falls Sie kein Azure-Konto besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.
4. Geben Sie Ihre Kontoinformationen ein. Wählen Sie für **Ausführendes Azure-Konto erstellen** die Option **Ja**, um automatisch die Einstellungen zu aktivieren, die zum Vereinfachen der Authentifizierung für Azure erforderlich sind.
5. Wählen Sie nach Abschluss des Vorgangs **Erstellen** aus, um die Bereitstellung des Automation-Kontos zu starten.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Importieren des „Stop Azure V2 VMs“-Runbooks

Importieren Sie mit einem [Azure Automation-Runbook](../../automation/automation-runbook-types.md) das grafische Runbook [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) aus dem Katalog.

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com/) an.
1. Öffnen Sie Ihr Automation-Konto durch Auswahl von **Alle Dienste** > **Automation-Kontos**. Wählen Sie dann Ihr Automation-Konto aus.
1. Wählen Sie im Abschnitt **Prozessautomatisierung** die Option **Runbookkatalog** aus.
1. Legen Sie für die **Katalogquelle** den Wert **Script Center** fest, und wählen Sie **OK** aus.
1. Wählen Sie das Katalogelement [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) im Azure-Portal aus.
1. Wählen Sie **Importieren** aus, um den Bereich **Importieren** anzuzeigen, und wählen Sie **OK** aus. Der Runbookübersichtsbereich wird angezeigt.
1. Sobald das Runbook den Importvorgang abgeschlossen hat, wählen Sie **Bearbeiten**, um den grafischen Runbook-Editor und die Veröffentlichungsoption anzuzeigen.  
    ![Azure – Bearbeiten von grafischen Runbooks](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
1. Wählen Sie **Veröffentlichen** aus, um das Runbook zu veröffentlichen, und bestätigen Sie den Vorgang mit **Ja**. Beim Veröffentlichen eines Runbooks wird die vorhandene veröffentlichte Version durch die Entwurfsversion überschrieben. In diesem Fall ist noch keine veröffentlichte Version vorhanden, da Sie das Runbook gerade erst erstellt haben.
    Weitere Informationen zum Veröffentlichen eines Runbooks finden Sie unter [Mein erstes grafisches Runbook](../../automation/learn/automation-tutorial-runbook-graphical.md).

## <a name="create-webhooks-for-the-runbook"></a>Erstellen von Webhooks für das Runbook

Mithilfe des grafischen Runbooks [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) erstellen Sie zwei Webhooks, um das Runbook in Azure Automation über eine einzelne HTTP-Anforderung zu starten. Der erste Webhook ruft das Runbook bei einem Budgetschwellenwert von 80 Prozent mit dem Namen der Ressourcengruppe als Parameter auf und ermöglicht das Beenden der optionalen virtuellen Computer. Dann ruft der zweite Webhook das Runbook (bei 100 Prozent) ohne Parameter auf, wodurch alle verbleibenden VM-Instanzen beendet werden.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Seite **Runbooks** das Runbook **StopAzureV2Vm** aus, das den Runbookübersichtsbereich anzeigt.
1. Wählen Sie oben auf der Seite **Webhook** aus, um den Bereich **Webhook hinzufügen** zu öffnen.
1. Wählen Sie **Neuen Webhook erstellen** aus, um den Bereich **Neuen Webhook erstellen** zu öffnen.
1. Legen Sie als **Name** des Webhooks **Optional** fest. Die Eigenschaft **Aktiviert** muss auf **Ja** festgelegt sein. Sie müssen den Wert für **Gültig bis** nicht ändern. Weitere Informationen zu Webhook-Eigenschaften finden Sie unter [Details zu einem Webhook](../../automation/automation-webhooks.md#webhook-properties).
1. Wählen Sie neben der URL das Symbol zum Kopieren aus, um die URL des Webhooks zu kopieren.
   > [!IMPORTANT]
   > Speichern Sie die URL des Webhooks mit dem Namen **Optional** an einem sicheren Ort. Sie verwenden die URL später in diesem Tutorial. Nachdem Sie den Webhook erstellt haben, können Sie die URL aus Sicherheitsgründen weder anzeigen noch erneut abrufen.
1. Wählen Sie **OK** aus, um den neuen Webhook zu erstellen.
1. Wählen Sie **Parameter und Ausführungseinstellungen konfigurieren** aus, um Parameterwerte für das Runbook anzuzeigen.
   > [!NOTE]
   > Wenn das Runbook über obligatorische Parameter verfügt, können Sie den Webhook nur erstellen, wenn die entsprechenden Werte bereitgestellt wurden.
1. Wählen Sie **OK** aus, um die Webhookparameterwerte zu akzeptieren.
1. Wählen Sie **Erstellen** aus, um den Webhook zu erstellen.
1. Erstellen Sie als Nächstes mit den obigen Schritten einen zweiten Webhook namens **Complete**.
    > [!IMPORTANT]
    > Achten Sie darauf, dass Sie beide Webhook-URLs zur späteren Verwendung in diesem Tutorial speichern. Nachdem Sie den Webhook erstellt haben, können Sie die URL aus Sicherheitsgründen weder anzeigen noch erneut abrufen.

Sie sollten nun über zwei konfigurierte Webhooks verfügen, die jeweils mit den URLs verfügbar sind, die Sie gespeichert haben.

![Webhooks – „Optional“ und „Complete“](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Das Azure Automation-Setup ist nun abgeschlossen. Sie können die Webhooks mit einem einfachen Postman-Test testen, um sicherzustellen, dass sie funktionieren. Als Nächstes müssen Sie die Logik-App für die Orchestrierung erstellen.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Erstellen einer Azure-Logik-App für die Orchestrierung

Logic Apps dient zum Erstellen, Planen und Automatisieren von Prozessen in Form von Workflows, um Apps, Daten, Systeme und Dienste für Unternehmen oder Organisationen zu integrieren. In diesem Szenario macht die [Logik-App](../../logic-apps/index.yml), die Sie erstellen, ein wenig mehr, als nur den Automatisierungswebhook aufzurufen, den Sie erstellt haben.

Budgets können so eingerichtet werden, dass sie eine Benachrichtigung auslösen, wenn ein angegebener Schwellenwert erreicht wird. Sie können mehrere Schwellenwerte einrichten, bei denen Sie benachrichtigt werden, und die Logik-App zeigt Ihnen die Möglichkeit, basierend auf dem jeweils erreichten Schwellenwert unterschiedliche Aktionen auszuführen. In diesem Beispiel richten Sie ein Szenario ein, in dem Sie eine Reihe von Benachrichtigungen erhalten. Die erste Benachrichtigung erfolgt, wenn 80 Prozent des Budgets erreicht sind, und die zweite Benachrichtigung, wenn 100 Prozent des Budgets erreicht sind. Die Logik-App dient zum Herunterfahren aller virtuellen Computer in der Ressourcengruppe. Zuerst wird der Schwellenwert **Optional** bei 80% erreicht, und dann wird der zweite Schwellenwert erreicht, bei dem alle virtuellen Computer im Abonnement heruntergefahren werden.

Mit Logik-Apps können Sie ein Beispielschema für den HTTP-Trigger bereitstellen, jedoch müssen Sie den **Content-Type**-Header festlegen. Da die Aktionsgruppe keine benutzerdefinierten Header für den Webhook aufweist, müssen Sie die Nutzlast in einem separaten Schritt analysieren. Hierfür verwenden Sie die **Parse**-Aktion mit einer Beispielnutzlast.

### <a name="create-the-logic-app"></a>Erstellen der Logik-App

Die Logik-App führt mehrere Aktionen aus. Die folgende Liste bietet einen allgemeinen Satz von Aktionen, die die Logik-App ausführt:

- Erkennen, wenn eine HTTP-Anforderung empfangen wird
- Analysieren der übergebenen JSON-Daten, um den erreichten Schwellenwert zu bestimmen
- Überprüfen Sie mit einer Bedingungsanweisung, ob der Schwellenwert von mindestens 80% des Budgets erreicht wurde, aber nicht 100% oder mehr.
  - Wenn dieser Schwellenwert erreicht wurde, senden Sie einen HTTP-POST unter Verwendung des Webhooks mit dem Namen **Optional**. Diese Aktion fährt die virtuellen Computer in der Gruppe „Optional“ herunter.
- Überprüfen Sie mit einer Bedingungsanweisung, ob der Schwellenwert 100% des Budgets erreicht oder überschritten hat.
  - Wenn der Schwellenwert erreicht wurde, senden Sie einen HTTP-POST unter Verwendung des Webhooks mit dem Namen **Complete**. Diese Aktion fährt alle übrigen virtuellen Computer herunter.

Die folgenden Schritte sind erforderlich, um die Logik-App zu erstellen, die die oben genannten Schritte ausführt:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) die Option **Ressource erstellen** > **Integration** > **Logik-App** aus.  
    ![Azure – Auswählen der Logik-App-Ressource](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
1. Stellen Sie im Blatt **Logik-App erstellen** die erforderlichen Details zum Erstellen Ihrer Logik-App bereit, und wählen Sie **An Dashboard anheften** und dann **Erstellen** aus.  
    ![Azure – Erstellen einer Logik-App](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Nachdem Ihre Logik-App von Azure bereitgestellt wurde, wird der **Designer für Logik-Apps** geöffnet, und es wird ein Bereich mit einem Einführungsvideo und häufig verwendeten Triggern angezeigt.

### <a name="add-a-trigger"></a>Hinzufügen eines Triggers

Jede Logik-App muss mit einem Trigger beginnen, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder eine bestimmte Bedingung erfüllt wird. Bei jeder Auslösung des Triggers erstellt die Logic Apps-Engine eine Logik-App-Instanz, mit der Ihr Workflow gestartet und ausgeführt wird. Als Aktionen werden alle Schritte bezeichnet, die nach dem Trigger ausgeführt werden.

1. Wählen Sie unter **Vorlagen** des Bereichs **Designer für Logik-Apps** die Option **Leere Logik-App** aus.
1. Fügen Sie einen [Trigger](../../logic-apps/logic-apps-overview.md#logic-app-concepts) durch Eingabe von „http request“ im **Designer für Logik-Apps**-Suchfeld zum Suchen und Auswählen des Triggers mit dem Namen **Anforderung – wenn eine HTTP-Anforderung empfangen wird** hinzu.  
    ![Azure – Logik-App – HTTP-Trigger](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
1. Wählen Sie **Nächster Schritt** > **Aktion hinzufügen** aus.  
    ![Azure – Neuer Schritt – Aktion hinzufügen](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
1. Suchen Sie im Suchfeld von **Designer für Logik-Apps** nach „parse JSON“, um die [Aktion](../../logic-apps/logic-apps-overview.md#logic-app-concepts) **Datenvorgänge – JSON analysieren** zu suchen, und wählen Sie diese aus.  
    ![Azure – Logik-App – Aktion „JSON analysieren“ hinzufügen](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
1. Geben Sie „Payload“ als Name für das Analysieren der JSON-Nutzlast ins Feld **Inhalt** ein, oder verwenden Sie das Body-Tag aus dem dynamischen Inhalt.
1. Wählen Sie die Option **Beispielnutzlast zum Generieren eines Schemas verwenden** im Feld **JSON analysieren** aus.  
    ![Azure – Logik-App – Beispiel-JSON-Daten zum Generieren eines Schemas verwenden](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
1. Fügen Sie die folgende JSON-Beispielnutzlast in das Textfeld ein: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`
   Das Textfeld wird wie folgt angezeigt:  
    ![Azure – Logik-App – Beispiel-JSON-Nutzlast](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
1. Wählen Sie **Fertig** aus.

### <a name="add-the-first-conditional-action"></a>Hinzufügen der ersten bedingten Aktion

Überprüfen Sie mit einer Bedingungsanweisung, ob der Schwellenwert von mindestens 80% des Budgets erreicht wurde, aber nicht 100% oder mehr. Wenn dieser Schwellenwert erreicht wurde, senden Sie einen HTTP-POST unter Verwendung des Webhooks mit dem Namen **Optional**. Diese Aktion fährt die virtuellen Computer in der Gruppe **Optional** herunter.

1. Wählen Sie **Nächster Schritt** > **Bedingung hinzufügen** aus.  
    ![Azure – Logik-App – Bedingung hinzufügen](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
1. Wählen Sie im Feld **Bedingung** das Textfeld mit dem Eintrag `Choose a value` aus, um eine Liste der verfügbaren Werte anzuzeigen.  
    ![Azure – Logik-App – Feld „Bedingung“](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)
1. Wählen Sie am oberen Rand der Liste **Ausdruck** aus, und geben Sie den folgenden Ausdruck im Ausdrucks-Editor ein: `float()`.  
    ![Azure – Logik-App – Floatausdruck](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)
1. Wählen Sie **Dynamischer Inhalt** aus, platzieren Sie den Cursor innerhalb der Klammern (), und wählen Sie **NotificationThresholdAmount** in der Liste aus, um den vollständigen Ausdruck einzutragen.
   Der Ausdruck lautet folgendermaßen:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
1. Wählen Sie **OK** aus, um den Ausdruck festzulegen.
1. Wählen Sie **ist größer als oder gleich** im Dropdownlistenfeld der **Bedingung** aus.
1. Geben Sie im Feld **Wert auswählen** der Bedingung `.8` ein.  
    ![Screenshot des Dialogfelds „Bedingung“ mit ausgewählten Werten](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)
1. Wählen Sie im Feld „Bedingung“ die Optionen **Hinzufügen** > **Zeile hinzufügen** aus, um einen zusätzlichen Teil der Bedingung hinzuzufügen.
1. Wählen Sie im Feld **Bedingung** das Textfeld mit dem Eintrag `Choose a value` aus.
1. Wählen Sie am oberen Rand der Liste **Ausdruck** aus, und geben Sie den folgenden Ausdruck im Ausdrucks-Editor ein: `float()`.
1. Wählen Sie **Dynamischer Inhalt** aus, platzieren Sie den Cursor innerhalb der Klammern (), und wählen Sie **NotificationThresholdAmount** in der Liste aus, um den vollständigen Ausdruck einzutragen.
1. Wählen Sie **OK** aus, um den Ausdruck festzulegen.
1. Wählen Sie **ist kleiner als** im Dropdownlistenfeld **Bedingung** aus.
1. Geben Sie im Feld **Wert auswählen** der Bedingung `1` ein.  
    ![Screenshot des Dialogfelds „Bedingung“ mit zwei Bedingungen](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)
1. Wählen Sie im Feld **Bei TRUE** die Option **Aktion hinzufügen** aus. Sie fügen eine HTTP-POST-Aktion hinzu, die optionale virtuelle Computer herunterfährt.  
    ![Azure – Logik-App – Hinzufügen einer Aktion](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)
1. Geben Sie **HTTP** ein, um nach der HTTP-Aktion zu suchen, und wählen Sie die **HTTP – HTTP**-Aktion aus.  
    ![Azure – Logik-App – Hinzufügen einer HTTP-Aktion](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)
1. Wählen Sie **post** als Wert für **Methode** aus.
1. Geben Sie die URL für den Webhook mit dem Namen **Optional** ein, den Sie zuvor in diesem Tutorial mit dem Wert **Uri** erstellt haben.  
    ![Azure – Logik-App – HTTP-Aktions-URI](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)
1. Wählen Sie im Feld **Bei TRUE** die Option **Aktion hinzufügen** aus. Sie fügen eine E-Mail-Aktion hinzu, die eine E-Mail-Benachrichtigung mit dem Hinweis an den Empfänger sendet, dass die optionalen virtuellen Computer heruntergefahren wurden.
1. Suchen Sie nach „send email“, und wählen Sie eine *E-Mail*-Aktion auf Basis des von Ihnen verwendeten E-Mail-Diensts aus.  
    ![Azure – Logik-App – E-Mail-Sendeaktion](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    Wählen Sie für persönliche Microsoft-Konten die Option **Outlook.com**. Wählen Sie für Geschäfts-, Schul- oder Unikonten für Azure die Option **Office 365 Outlook**. Besteht noch keine Verbindung, werden Sie zur Anmeldung bei Ihrem E-Mail-Konto aufgefordert. Logic Apps erstellt eine Verbindung mit Ihrem E-Mail-Konto.
   Sie müssen der Logik-App den Zugriff auf Ihre E-Mail-Informationen ermöglichen.  
    ![Azure – Logik-App – Zugriffshinweis](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)
1. Fügen Sie den Text für **An**, **Betreff** und **Text** für die E-Mail hinzu, die dem Empfänger mitteilt, dass die optionalen VMs heruntergefahren wurden. Verwenden Sie **BudgetName** und den dynamischen Inhalt **NotificationThresholdAmount** zum Auffüllen der Felder „Betreff“ und „Text“. 
    ![Azure – Logik-App – E-Mail-Details](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>Hinzufügen der zweiten bedingten Aktion

Überprüfen Sie mit einer Bedingungsanweisung, ob der Schwellenwert 100% des Budgets erreicht oder überschritten hat. Wenn der Schwellenwert erreicht wurde, senden Sie einen HTTP-POST unter Verwendung des Webhooks mit dem Namen **Complete**. Diese Aktion fährt alle übrigen virtuellen Computer herunter.

1. Wählen Sie **Nächster Schritt** > **Bedingung hinzufügen** aus.  
    ![Screenshot des Dialogfelds „Bei TRUE“, in dem „Aktion hinzufügen“ hervorgehoben ist](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)
1. Wählen Sie im Feld **Bedingung** das Textfeld mit dem Eintrag `Choose a value` aus, um eine Liste der verfügbaren Werte anzuzeigen.
1. Wählen Sie am oberen Rand der Liste **Ausdruck** aus, und geben Sie den folgenden Ausdruck im Ausdrucks-Editor ein: `float()`.
1. Wählen Sie **Dynamischer Inhalt** aus, platzieren Sie den Cursor innerhalb der Klammern (), und wählen Sie **NotificationThresholdAmount** in der Liste aus, um den vollständigen Ausdruck einzutragen.
   Der Ausdruck lautet in etwa wie folgt:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
1. Wählen Sie **OK** aus, um den Ausdruck festzulegen.
1. Wählen Sie **ist größer als oder gleich** im Dropdownlistenfeld der **Bedingung** aus.
1. Geben Sie im Feld **Wert auswählen** für die Bedingung `1` ein.  
    ![Azure – Logik-App – Bedingungswert festlegen](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)
1. Wählen Sie im Feld **Bei TRUE** die Option **Aktion hinzufügen** aus. Sie fügen eine HTTP-POST-Aktion hinzu, die alle verbleibenden virtuellen Computer herunterfährt.  
    ![Screenshot des Dialogfelds „Bei TRUE“, in dem Sie eine H T T P-POST-Aktion hinzufügen können](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)
1. Geben Sie **HTTP** ein, um nach der HTTP-Aktion zu suchen, und wählen Sie die **HTTP – HTTP**-Aktion aus.
1. Wählen Sie **post** als Wert für **Methode** aus.
1. Geben Sie die URL für den Webhook mit dem Namen **Complete** ein, den Sie zuvor in diesem Tutorial mit dem Wert **Uri** erstellt haben.  
    ![Screenshot des Dialogfelds „H T T P“, in dem Sie den U R L-Wert eingeben können](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)
1. Wählen Sie im Feld **Bei TRUE** die Option **Aktion hinzufügen** aus. Sie fügen eine E-Mail-Aktion hinzu, die eine E-Mail-Benachrichtigung mit dem Hinweis an den Empfänger sendet, dass die verbleibenden virtuellen Computer heruntergefahren wurden.
1. Suchen Sie nach „send email“, und wählen Sie eine *E-Mail*-Aktion auf Basis des von Ihnen verwendeten E-Mail-Diensts aus.
1. Fügen Sie den Text für **An**, **Betreff** und **Text** für die E-Mail hinzu, die dem Empfänger mitteilt, dass die optionalen VMs heruntergefahren wurden. Verwenden Sie **BudgetName** und den dynamischen Inhalt **NotificationThresholdAmount** zum Auffüllen der Felder „Betreff“ und „Text“.  
    ![Azure – Logik-App – Details zum E-Mail-Senden](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)
1. Wählen Sie am oberen Rand des Blatts **Logik-App-Designer** die Option **Speichern** aus.

### <a name="logic-app-summary"></a>Logik-App-Übersicht

So sieht Ihre Logik-App aus, wenn Sie fertig sind. In den grundlegendsten Szenarien, in denen Sie keine schwellenwertbasierte Orchestrierung benötigen, können Sie das Automatisierungsskript direkt von **Monitor** aus aufrufen und den **Logik-App**-Schritt überspringen.

![Azure – Logik-App – Vollständige Ansicht](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

Wenn Sie Ihre Logik-App gespeichert haben, wird eine URL generiert, die Sie aufrufen können. Sie verwenden diese URL im nächsten Abschnitt dieses Tutorials.

## <a name="create-an-azure-monitor-action-group"></a>Erstellen einer Azure Monitor-Aktionsgruppe

Eine Aktionsgruppe ist eine Sammlung von Benachrichtigungseinstellungen, die Sie definieren. Wenn eine Warnung ausgelöst wird, kann eine bestimmte Aktionsgruppe die Warnung mittels einer Benachrichtigung empfangen. Eine Azure-Warnung löst proaktiv basierend auf bestimmten Bedingungen eine Benachrichtigung aus und bietet die Möglichkeit, Maßnahmen zu ergreifen. Eine Warnung kann Daten aus mehreren Quellen einschließlich Metriken und Protokollen nutzen.

Aktionsgruppen sind der einzige Endpunkt, den Sie in Ihr Budget integrieren. Sie können Benachrichtigungen in einer Vielzahl von Kanälen einrichten, aber für dieses Szenario konzentrieren Sie sich auf die Logik-App, die Sie zuvor in diesem Tutorial erstellt haben.

### <a name="create-an-action-group-in-azure-monitor"></a>Erstellen einer Aktionsgruppe in Azure Monitor

Bei der Erstellung der Aktionsgruppe verweisen Sie auf die Logik-App, die Sie zuvor in diesem Tutorial erstellt haben.

1. Wenn Sie noch nicht beim [Azure-Portal](https://portal.azure.com/) angemeldet sind, melden Sie sich an, und wählen Sie **Alle Dienste** > **Monitor** aus.
1. Wählen Sie **Warnungen** und dann **Aktionen verwalten** aus.
1. Wählen Sie im Bereich **Aktionsgruppen** die Option **Hinzufügen einer Aktionsgruppe** aus.
1. Fügen Sie folgende Elemente hinzu, und überprüfen Sie sie:
    - Aktionsgruppenname
    - Kurzname
    - Subscription
    - Resource group  
    ![Azure – Logik-App – Hinzufügen einer Aktionsgruppe](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)
1. Fügen Sie im Bereich **Aktionsgruppe hinzufügen** eine Logik-App-Aktion hinzu. Benennen Sie die Aktion **Budget-BudgetLA**. Wählen Sie im Bereich **Logik-App** das **Abonnement** und die **Ressourcengruppe** aus. Wählen Sie dann die **Logik-App** aus, die Sie zuvor in diesem Tutorial erstellt haben.
1. Wählen Sie **OK** aus, um die Logik-App festzulegen. Wählen Sie dann **OK** im Bereich **Aktionsgruppe hinzufügen** aus, um die Aktionsgruppe zu erstellen.

Sie verfügen nun über alle unterstützenden Komponenten, die erforderlich sind, um Ihr Budget effektiv zu orchestrieren. Jetzt müssen Sie lediglich noch das Budget erstellen und für die Verwendung der von Ihnen erstellten Aktionsgruppe konfigurieren.

## <a name="create-the-azure-budget"></a>Erstellen des Azure-Budgets

Im Azure-Portal können Sie mithilfe des [Budgetfeatures](../costs/tutorial-acm-create-budgets.md) in Cost Management ein Budget erstellen. Alternativ dazu können Sie ein Budget auch mithilfe von REST-APIs, PowerShell-Cmdlets oder der Befehlszeilenschnittstelle erstellen. Im folgenden Verfahren wird die REST-API verwendet. Vor dem Aufrufen der REST-API benötigen Sie ein Autorisierungstoken. Um ein Autorisierungstoken zu erstellen, können Sie das [ARMClient](https://github.com/projectkudu/ARMClient)-Projekt verwenden. Mit dem **ARMClient** können Sie sich beim Azure Resource Manager authentifizieren und ein Token zum Aufrufen der APIs erhalten.

### <a name="create-an-authentication-token"></a>Erstellen eines Authentifizierungstokens

1. Navigieren Sie auf GitHub zum [ARMClient](https://github.com/projectkudu/ARMClient)-Projekt.
1. Klonen Sie das Repository, um eine lokale Kopie zu erhalten.
1. Öffnen Sie das Projekt in Visual Studio, und erstellen Sie es.
1. Nachdem der Build erfolgreich erstellt wurde, muss die ausführbare Datei sich im *\bin\debug*-Ordner befinden.
1. Führen Sie den ARMClient aus. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie im Stammverzeichnis des Projekts zum *\bin\debug*-Ordner.
1. Um sich anzumelden und zu authentifizieren, geben Sie an der Eingabeaufforderung den folgenden Befehl ein:<br>
    `ARMClient login prod`
1. Kopieren Sie die **Abonnement-GUID** aus der Ausgabe.
1. Um ein Autorisierungstoken in die Zwischenablage zu kopieren, geben Sie den folgenden Befehl an der Eingabeaufforderung ein, aber verwenden Sie unbedingt die kopierte Abonnement-ID aus dem vorherigen Schritt: <br>
    `ARMClient token <subscription GUID from previous step>`

    Sobald Sie die vorherigen Schritte abgeschlossen haben, sehen Sie Folgendes:<br>
    **Token copied to clipboard successfully. (Token wurde erfolgreich in die Zwischenablage kopiert.)**
1. Speichern Sie das Token zur Verwendung für Schritte im nächsten Abschnitt dieses Tutorials.

### <a name="create-the-budget"></a>Erstellen des Budgets

Als Nächstes konfigurieren Sie **Postman** zum Erstellen eines Budgets durch Aufrufen der Azure-Nutzungs-REST-APIs. Postman ist eine API-Entwicklungsumgebung. Sie importieren Umgebungs- und Sammlungsdateien in Postman. Die Sammlung enthält gruppierte Definitionen von HTTP-Anforderungen, die Azure Consumption-REST-APIs aufrufen. Die Umgebungsdatei enthält Variablen, die von der Sammlung verwendet werden.

1. Laden Sie den [Postman-REST-Client](https://www.getpostman.com/) herunter, und öffnen Sie ihn, um die REST-APIs auszuführen.
1. Erstellen Sie in Postman eine neue Anforderung.  
    ![Postman – Erstellen einer neuen Anforderung](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)
1. Speichern Sie die neue Anforderung als Sammlung, sodass die neue Anforderung keinen Inhalt hat.  
    ![Postman – Speichern der neuen Anforderung](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)
1. Ändern Sie die Anforderung von einer `Get`- in eine `Put`-Aktion.
1. Ändern Sie die folgende URL, indem Sie `{subscriptionId}` mit der **Abonnement-ID** ersetzen, die Sie im vorherigen Abschnitt dieses Tutorials verwendet haben. Ändern Sie die URL außerdem so, dass „SampleBudget“ als Wert für `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31` einbezogen wird.
1. Wählen Sie in Postman die Registerkarte **Headers** aus.
1. Fügen Sie einen neuen **Key (Schlüssel)** mit dem Namen „Authorization“ (Autorisierung) hinzu.
1. Legen Sie für **Value (Wert)** ein Token fest, das am Ende des letzten Abschnitts mit dem ArmClient erstellt wurde.
1. Wählen Sie in Postman die Registerkarte **Body (Text)** aus.
1. Wählen Sie die Schaltflächenoption **raw (unformatiert)** aus.
1. Fügen Sie die unten stehende Beispielbudgetdefinition in das Textfeld ein. Allerdings müssen Sie die Parameter `subscriptionID`, `resourcegroupname` und `actiongroupname`durch Ihre Abonnement-ID, einen eindeutigen Namen für Ihre Ressourcengruppe und den Namen der Aktionsgruppe ersetzen, die Sie in URL und Anforderungstext erstellt haben:

    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {},
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [],
                    "contactRoles": [],
                    "contactGroups": [
                        "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                },
               "Actual_EqualTo_100_Percent": {
                       "operator": "EqualTo",
                       "threshold": 100,
                       "contactGroups": [
                           "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                       ]
                   }
               }
            }
        }
    ```
1. Wählen Sie **Send (Senden)** zum Senden der Anforderung aus.

Sie haben jetzt alle Komponenten, die Sie zum Aufrufen der [Budgets-API](/rest/api/consumption/budgets) benötigen. Die Budget-API-Referenz enthält zusätzliche Details zu spezifischen Anforderungen, einschließlich der folgenden:

- **budgetName**: Mehrere Budgets werden unterstützt.  Budgetnamen müssen eindeutig sein.
- **category**: Entweder **Kosten** oder **Nutzung**. Die API unterstützt sowohl Kosten- als auch Nutzungsbudgets.
- **timeGrain**: Ein Monats-, Quartals- oder Jahresbudget. Der Betrag wird am Ende des Zeitraums zurückgesetzt.
- **filters**: Mit Filtern können Sie das Budget auf einen bestimmten Satz von Ressourcen im ausgewählten Bereich eingrenzen. Ein Filter kann beispielsweise eine Sammlung von Ressourcengruppen für ein Budget auf Abonnementebene sein.
- **notifications**: Legt die Benachrichtigungsdetails und Schwellenwerte fest. Sie können mehrere Schwellenwerte einrichten und eine E-Mail-Adresse oder Aktionsgruppe angeben, um eine Benachrichtigung zu erhalten.

## <a name="summary"></a>Zusammenfassung

In diesem Tutorial wurde Folgendes beschrieben:

- Wie Sie ein Azure Automation-Runbook zum Beenden von VMs erstellen.
- Wie Sie eine Azure-Logik-App erstellen, die basierend auf den Budgetschwellenwerten ausgelöst wird, und das zugehörige Runbook mit den entsprechenden Parametern aufrufen.
- Wie Sie eine Azure Monitor-Aktionsgruppe erstellen, die konfiguriert wurde, um bei Erreichen des Budgetschwellenwerts die Azure-Logik-App auszulösen
- Wie Sie das Azure-Budget mit den gewünschten Schwellenwerten erstellen und es mit der Aktionsgruppe verknüpfen.

Sie verfügen nun über ein voll funktionsfähiges Budget für Ihr Abonnement, das Ihre VMs beendet, wenn Sie die konfigurierten Budgetschwellenwerte erreichen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure-Abrechnungsszenarien finden Sie unter [Automatisierungsszenarien zur Abrechnungs- und Kostenverwaltung](cost-management-automation-scenarios.md).