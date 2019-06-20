---
title: Budgetszenario zur Abrechnungs- und Kostenverwaltung in Azure| Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit der Azure-Automatisierung virtuelle Computer basierend auf bestimmten Budgetschwellenwerten herunterfahren.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: db93f546-6b56-4b51-960d-1a5bf0274fc8
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 03/13/2019
ms.author: erikre
ms.openlocfilehash: 4bf76ac0bdd59764815f18a40a3e243d7cf9d920
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60617385"
---
# <a name="manage-costs-with-azure-budgets"></a>Verwalten von Kosten mit Azure-Budgets

Kostenkontrolle ist eine wichtige Komponente zur Maximierung des Nutzens Ihrer Investition in der Cloud. Es gibt mehrere Szenarien, in denen Kostensichtbarkeit, Berichterstellung und kostenbasierte Orchestrierung für fortgesetzte Geschäftsvorgänge entscheidend sind. Zur Unterstützung dieser einzelnen Szenarien stehen die [Azure Cost Management-APIs](https://docs.microsoft.com/rest/api/consumption/) zur Verfügung. Die-APIs bieten Nutzungsdetails, mit deren Hilfe Sie die Kosten präzise auf Instanzebene anzeigen können.

Budgets werden häufig als Teil der Kostenkontrolle verwendet. Budgets können in Azure festgelegt werden. Beispielsweise können Sie Ihre Budgetansicht auf Grundlage von Abonnement, Ressourcengruppen oder einer Sammlung von Ressourcen konzentrieren. Sie können jedoch nicht nur die Budget-API verwenden, um sich per E-Mail benachrichtigen zu lassen, wenn ein Budgetschwellenwert erreicht wird, sondern auch die [Azure Monitor-Aktionsgruppen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) verwenden, um einen orchestrierten Satz von Aktionen als Ergebnis eines Budgetereignisses auszulösen.

In einem häufigen Budgetszenario eines Kunden, der eine nicht kritische Workload ausführt, möchte der Kunde die Verwaltung an einem Budget orientieren und auch die Kosten der monatlichen Rechnung prognostizieren. Dieses Szenario erfordert eine kostenbasierte Orchestrierung von Ressourcen, die Teil der Azure-Umgebung sind. In diesem Szenario wird ein Monatsbudget von 1.000 USD für das Abonnement festgelegt. Außerdem werden Benachrichtigungsschwellenwerte festgelegt, um einige Orchestrierungen auszulösen. Dieses Szenario beginnt mit einem Kostenschwellenwert von 80%, bei dem alle virtuellen Computer in der Ressourcengruppe beendet werden **Optional**. Dann werden bei einem Kostenschwellenwert von 100% alle VM-Instanzen beendet.
Um dieses Szenario zu konfigurieren, führen Sie die folgenden Aktionen mithilfe der Schritte in den einzelnen Abschnitten dieses Tutorials durch.

Die in diesem Tutorial enthaltenen Aktionen ermöglichen Ihnen Folgendes:

- Erstellen eines Azure Automation-Runbooks, um VMs mithilfe von Webhooks zu beenden.
- Erstellen einer Azure-Logik-App, die basierend auf dem Budgetschwellenwert ausgelöst wird, und Aufrufen des Runbooks mit den entsprechenden Parametern.
- Erstellen einer Azure Monitor-Aktionsgruppe, die konfiguriert wird, um bei Erreichen des Budgetschwellenwerts die Azure-Logik-App auszulösen.
- Erstellen des Azure-Budgets mit den gewünschten Schwellenwerten und dessen Verknüpfung mit der Aktionsgruppe.

## <a name="create-an-azure-automation-runbook"></a>Erstellen eines Azure Automation-Runbooks

[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) ist ein Dienst, mit dem Sie die meisten Ihrer Ressourcenverwaltungsaufgaben in ein Skript schreiben und diese Aufgaben entweder geplant oder bedarfsgesteuert ausführen können. Im Rahmen dieses Szenarien erstellen Sie ein [Azure Automation-Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types) zum Beenden von VMs. Um dieses Szenario zu erstellen, verwenden Sie das grafische Runbook [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) aus dem [Katalog](https://docs.microsoft.com/azure/automation/automation-runbook-gallery). Wenn Sie dieses Runbook in Ihr Azure-Konto importieren und veröffentlichen, können Sie VMs beenden, wenn ein Budgetschwellenwert erreicht wird.

### <a name="create-an-azure-automation-account"></a>Erstellen eines Azure Automation-Kontos

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie in der linken oberen Ecke von Azure auf die Schaltfläche **Ressource erstellen**.
3. Wählen Sie **Verwaltungstools** > **Automatisierung**.
   > [!NOTE]
   > Falls Sie kein Azure-Konto besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.
4. Geben Sie Ihre Kontoinformationen ein. Wählen Sie für **Ausführendes Azure-Konto erstellen** die Option **Ja**, um automatisch die Einstellungen zu aktivieren, die zum Vereinfachen der Authentifizierung für Azure erforderlich sind.
5. Klicken Sie nach Abschluss des Vorgangs auf **Erstellen**, um die Bereitstellung des Automation-Kontos zu starten.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Importieren des „Stop Azure V2 VMs“-Runbooks

Importieren Sie mit einem [Azure Automation-Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types) das grafische Runbook [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) aus dem Katalog.

1.  Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com/) an.
2.  Öffnen Sie Ihr Automation-Konto durch Auswahl von **Alle Dienste** > **Automation-Kontos**. Wählen Sie dann Ihr Automation-Konto aus.
3.  Klicken Sie im Abschnitt **Prozessautomatisierung** auf **Runbookkatalog**.
4.  Legen Sie für die **Katalogquelle** den Wert **Script Center** fest, und wählen Sie **OK** aus.
5.  Wählen Sie das Katalogelement [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) im Azure-Portal aus.
6.  Klicken Sie auf die Schaltfläche **Importieren**, um das Blatt **Importieren** anzuzeigen, und wählen Sie **OK** aus. Das Runbookübersichtsblatt wird angezeigt.
7.  Sobald das Runbook den Importvorgang abgeschlossen hat, wählen Sie **Bearbeiten**, um den grafischen Runbook-Editor und die Veröffentlichungsoption anzuzeigen.

    ![Azure – Bearbeiten von grafischen Runbooks](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  Klicken Sie auf die Schaltfläche **Veröffentlichen**, um das Runbook zu veröffentlichen, und wählen Sie dann nach Aufforderung **Ja** aus. Beim Veröffentlichen eines Runbooks wird die vorhandene veröffentlichte Version durch die Entwurfsversion überschrieben. In diesem Fall ist noch keine veröffentlichte Version vorhanden, da Sie das Runbook gerade erst erstellt haben.

    Weitere Informationen zum Veröffentlichen eines Runbooks finden Sie unter [Mein erstes grafisches Runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical).

## <a name="create-webhooks-for-the-runbook"></a>Erstellen von Webhooks für das Runbook

Mithilfe des grafischen Runbooks [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) erstellen Sie zwei Webhooks, um das Runbook in Azure Automation über eine einzelne HTTP-Anforderung zu starten. Der erste Webhook ruft das Runbook bei einem Budgetschwellenwert von 80% mit dem Namen der Ressourcengruppe als Parameter auf und ermöglicht, die optionalen virtuellen Computer zu beenden. Dann ruft der zweite Webhook das Runbook (bei 100 %) ohne Parameter auf, wodurch alle verbleibenden VM-Instanzen beendet werden.

1. Klicken Sie auf der Seite **Runbooks** im [Azure-Portal](https://portal.azure.com/) auf das Runbook **StopAzureV2Vm**, das das Runbookübersichtsblatt anzeigt.
2. Klicken Sie oben auf der Seite auf **Webhook**, um das Blatt **Webhook hinzufügen** zu öffnen.
3. Klicken Sie auf **Neuen Webhook erstellen**, um das Blatt **Neuen Webhook erstellen** zu öffnen.
4. Legen Sie als **Name** des Webhooks **Optional** fest. Die Eigenschaft **Aktiviert** muss auf **Ja** festgelegt sein. Der Wert **Läuft ab** muss nicht geändert werden. Weitere Informationen zu Webhook-Eigenschaften finden Sie unter [Details zu einem Webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook).
5. Klicken Sie neben der URL auf das Symbol zum Kopieren, um die URL des Webhooks zu kopieren.
   > [!IMPORTANT]
   > Speichern Sie die URL des Webhooks mit dem Namen **Optional** an einem sicheren Ort. Sie werden die URL später in diesem Tutorial verwenden. Nachdem Sie den Webhook erstellt haben, können Sie die URL aus Sicherheitsgründen weder anzeigen noch erneut abrufen.
6. Klicken Sie auf **OK**, um den neuen Webhook zu erstellen.
7. Klicken Sie auf **Parameter und Ausführungseinstellungen konfigurieren**, um Parameterwerte für das Runbook anzuzeigen.
   > [!NOTE]
   > Wenn das Runbook über obligatorische Parameter verfügt, können Sie den Webhook nur erstellen, wenn die entsprechenden Werte bereitgestellt wurden.
8. Klicken Sie auf **OK**, um die Webhookparameterwerte zu akzeptieren.
9. Klicken Sie auf **Erstellen** , um das Projekt zu erstellen.
10. Erstellen Sie als Nächstes mit den obigen Schritten einen zweiten Webhook namens **Complete**.
    > [!IMPORTANT]
    > Achten Sie darauf, dass Sie beide Webhook-URLs zur späteren Verwendung in diesem Tutorial speichern. Nachdem Sie den Webhook erstellt haben, können Sie die URL aus Sicherheitsgründen weder anzeigen noch erneut abrufen.

Sie sollten nun über zwei konfigurierte Webhooks verfügen, die jeweils mit den URLs verfügbar sind, die Sie gespeichert haben.

![Webhooks – „Optional“ und „Complete“](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Das Azure Automation-Setup ist nun fertig. Sie können die Webhooks mit einem einfachen Postman-Test testen, um sicherzustellen, dass sie funktionieren. Als Nächstes müssen Sie die Logik-App für die Orchestrierung erstellen.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Erstellen einer Azure-Logik-App für die Orchestrierung

Logik-Apps dienen zum Erstellen, Planen und Automatisieren von Prozessen in Form von Workflows, um Apps, Daten, Systeme und Dienste für Unternehmen und Organisationen zu integrieren. In diesem Szenario macht die [Logik-App](https://docs.microsoft.com/azure/logic-apps/), die Sie erstellen, ein wenig mehr, als nur den Automatisierungswebhook aufzurufen, den Sie erstellt haben.

Budgets können so eingerichtet werden, dass sie eine Benachrichtigung auslösen, wenn ein angegebener Schwellenwert erreicht wird. Sie können mehrere Schwellenwerte einrichten, bei denen Sie benachrichtigt werden, und die Logik-App zeigt Ihnen die Möglichkeit, basierend auf dem jeweils erreichten Schwellenwert unterschiedliche Aktionen auszuführen. In diesem Beispiel richten Sie ein Szenario ein, in dem Sie eine Reihe von Benachrichtigungen erhalten. Die erste Benachrichtigung erfolgt, wenn 80% des Budgets erreicht sind, und die zweite Benachrichtigung, wenn 100% des Budgets erreicht sind. Die Logik-App dient zum Herunterfahren aller virtuellen Computer in der Ressourcengruppe. Zuerst wird der Schwellenwert **Optional** bei 80% erreicht, und dann wird der zweite Schwellenwert erreicht, bei dem alle virtuellen Computer im Abonnement heruntergefahren werden.

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

1.  Wählen Sie im [Azure-Portal](https://portal.azure.com/) die Option **Ressource erstellen** > **Integration** > **Logik-App** aus.

    ![Azure – Auswählen der Logik-App-Ressource](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  Stellen Sie im Blatt **Logik-App erstellen** die erforderlichen Details zum Erstellen Ihrer Logik-App bereit, wählen Sie **An Dashboard anheften** aus, und klicken Sie auf **Erstellen**.

    ![Azure – Erstellen einer Logik-App](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Nachdem Ihre Logik-App von Azure bereitgestellt wurde, wird der **Designer für Logik-Apps** geöffnet, und es wird ein Blatt mit einem Einführungsvideo und häufig verwendeten Triggern angezeigt.

### <a name="add-a-trigger"></a>Hinzufügen eines Triggers

Jede Logik-App muss mit einem Trigger beginnen, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder eine bestimmte Bedingung erfüllt wird. Bei jeder Auslösung des Triggers erstellt die Logic Apps-Engine eine Logik-App-Instanz, mit der Ihr Workflow gestartet und ausgeführt wird. Als Aktionen werden alle Schritte bezeichnet, die nach dem Trigger ausgeführt werden.

1.  Wählen Sie unter **Vorlagen** des Blatts **Designer für Logik-Apps** die Option **Leere Logik-App** aus.
2.  Fügen Sie einen [Trigger](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) durch Eingabe von „http request“ im **Designer für Logik-Apps**-Suchfeld zum Suchen und Auswählen des Triggers mit dem Namen **Anforderung – wenn eine HTTP-Anforderung empfangen wird** hinzu.

    ![Azure – Logik-App – HTTP-Trigger](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
3.  Wählen Sie **Nächster Schritt** > **Aktion hinzufügen** aus.

    ![Azure – Neuer Schritt – Aktion hinzufügen](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
4.  Suchen Sie im **Designer für Logik-Apps**-Suchfeld zum Suchen und Auswählen der [Aktion](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) **Datenvorgänge – JSON analysieren** nach „parse JSON“.

    ![Azure – Logik-App – Aktion „JSON analysieren“ hinzufügen](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
5.  Geben Sie „Payload“ als Name für das Analysieren der JSON-Nutzlast ins Feld **Inhalt** ein, oder verwenden Sie das Body-Tag aus dem dynamischen Inhalt.
6.  Wählen Sie die Option **Beispielnutzlast zum Generieren eines Schemas verwenden** im Feld **JSON analysieren** aus.

    ![Azure – Logik-App – Beispiel-JSON-Daten zum Generieren eines Schemas verwenden](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
7.  Fügen Sie die folgende JSON-Beispielnutzlast in das Textfeld ein: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`

    Das Textfeld wird wie folgt angezeigt:

    ![Azure – Logik-App – Beispiel-JSON-Nutzlast](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
8.  Klicken Sie auf **Fertig**.

### <a name="add-the-first-conditional-action"></a>Hinzufügen der ersten bedingten Aktion

Überprüfen Sie mit einer Bedingungsanweisung, ob der Schwellenwert von mindestens 80% des Budgets erreicht wurde, aber nicht 100% oder mehr. Wenn dieser Schwellenwert erreicht wurde, senden Sie einen HTTP-POST unter Verwendung des Webhooks mit dem Namen **Optional**. Diese Aktion fährt die virtuellen Computer in der Gruppe **Optional** herunter.

1.  Wählen Sie **Nächster Schritt** > **Bedingung hinzufügen** aus.

    ![Azure – Logik-App – Bedingung hinzufügen](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
2.  Klicken Sie im Feld **Bedingung** auf das Textfeld mit dem Eintrag **Wert auswählen**, um eine Liste der verfügbaren Werte anzuzeigen.

    ![Azure – Logik-App – Feld „Bedingung“](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)

3.  Klicken Sie am oberen Rand der Liste auf **Ausdruck**, und geben Sie den folgenden Ausdruck im Ausdrucks-Editor ein: `float()`

    ![Azure – Logik-App – Floatausdruck](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)

4.  Wählen Sie **Dynamischer Inhalt** aus, platzieren Sie den Cursor innerhalb der Klammern (), und wählen Sie **NotificationThresholdAmount** in der Liste aus, um den vollständigen Ausdruck einzutragen.

    Der Ausdruck lautet folgendermaßen:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Wählen Sie **OK** aus, um den Ausdruck festzulegen.
6.  Wählen Sie **ist größer als oder gleich** im Dropdownlistenfeld der **Bedingung** aus.
7.  Geben Sie im Feld **Wert auswählen** der Bedingung `.8` ein.

    ![Azure – Logik-App – Floatausdruck mit einem Wert](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)

8.  Klicken Sie im Feld „Bedingung“ auf **Hinzufügen** > **Zeile hinzufügen**, um einen zusätzlichen Teil der Bedingung hinzuzufügen.
9.  Klicken Sie im Feld **Bedingung** auf das Textfeld mit dem Eintrag **Wert auswählen**.
10. Klicken Sie am oberen Rand der Liste auf **Ausdruck**, und geben Sie den folgenden Ausdruck im Ausdrucks-Editor ein: `float()`
11. Wählen Sie **Dynamischer Inhalt** aus, platzieren Sie den Cursor innerhalb der Klammern (), und wählen Sie **NotificationThresholdAmount** in der Liste aus, um den vollständigen Ausdruck einzutragen.
12. Wählen Sie **OK** aus, um den Ausdruck festzulegen.
13. Wählen Sie **ist kleiner als** im Dropdownlistenfeld **Bedingung** aus.
14. Geben Sie im Feld **Wert auswählen** der Bedingung `1` ein.

    ![Azure – Logik-App – Floatausdruck mit einem Wert](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)

15. Wählen Sie im Feld **Bei TRUE** die Option **Aktion hinzufügen** aus. Sie fügen eine HTTP-POST-Aktion hinzu, die optionale virtuelle Computer herunterfährt.

    ![Azure – Logik-App – Hinzufügen einer Aktion](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)

16. Geben Sie **HTTP** ein, um nach der HTTP-Aktion zu suchen, und wählen Sie die **HTTP – HTTP**-Aktion aus.

    ![Azure – Logik-App – Hinzufügen einer HTTP-Aktion](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)

17. Wählen Sie **post** als Wert für **Methode** aus.
18. Geben Sie die URL für den Webhook mit dem Namen **Optional** ein, den Sie zuvor in diesem Tutorial mit dem Wert **Uri** erstellt haben.

    ![Azure – Logik-App – HTTP-Aktions-URI](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)

19. Wählen Sie im Feld **Bei TRUE** die Option **Aktion hinzufügen** aus. Sie fügen eine E-Mail-Aktion hinzu, die eine E-Mail-Benachrichtigung sendet, die dem Empfänger mitteilt, dass die optionalen virtuellen Computer heruntergefahren wurden.
20. Suchen Sie nach „send email“, und wählen Sie eine *E-Mail*-Aktion auf Basis des von Ihnen verwendeten E-Mail-Diensts aus.

    ![Azure – Logik-App – E-Mail-Sendeaktion](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    Wählen Sie für persönliche Microsoft-Konten die Option **Outlook.com**. Wählen Sie für Geschäfts-, Schul- oder Unikonten für Azure die Option **Office 365 Outlook**. Besteht noch keine Verbindung, werden Sie zur Anmeldung bei Ihrem E-Mail-Konto aufgefordert. Logic Apps erstellt eine Verbindung mit Ihrem E-Mail-Konto.

    Sie müssen der Logik-App den Zugriff auf Ihre E-Mail-Informationen ermöglichen.

    ![Azure – Logik-App – Zugriffshinweis](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)

21. Fügen Sie den Text für **An**, **Betreff** und **Text** für die E-Mail hinzu, die dem Empfänger mitteilt, dass die optionalen VMs heruntergefahren wurden. Verwenden Sie **BudgetName** und den dynamischen Inhalt **NotificationThresholdAmount** zum Auffüllen der Felder „Betreff“ und „Text“.

    ![Azure – Logik-App – E-Mail-Details](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>Hinzufügen der zweiten bedingten Aktion

Überprüfen Sie mit einer Bedingungsanweisung, ob der Schwellenwert 100% des Budgets erreicht oder überschritten hat. Wenn der Schwellenwert erreicht wurde, senden Sie einen HTTP-POST unter Verwendung des Webhooks mit dem Namen **Complete**. Diese Aktion fährt alle übrigen virtuellen Computer herunter.

1.  Wählen Sie **Nächster Schritt** > **Bedingung hinzufügen** aus.

    ![Azure – Logik-App – Aktion hinzufügen](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)

2.  Klicken Sie im Feld **Bedingung** auf das Textfeld mit dem Eintrag **Wert auswählen**, um eine Liste der verfügbaren Werte anzuzeigen.
3.  Klicken Sie am oberen Rand der Liste auf **Ausdruck**, und geben Sie den folgenden Ausdruck im Ausdrucks-Editor ein: `float()`
4.  Wählen Sie **Dynamischer Inhalt** aus, platzieren Sie den Cursor innerhalb der Klammern (), und wählen Sie **NotificationThresholdAmount** in der Liste aus, um den vollständigen Ausdruck einzutragen.

    Der Ausdruck lautet folgendermaßen:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Wählen Sie **OK** aus, um den Ausdruck festzulegen.
6.  Wählen Sie **ist größer als oder gleich** im Dropdownlistenfeld der **Bedingung** aus.
7.  Geben Sie im Feld **Wert auswählen** der Bedingung `1` ein.

    ![Azure – Logik-App – Bedingungswert festlegen](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)

8.  Wählen Sie im Feld **Bei TRUE** die Option **Aktion hinzufügen** aus. Sie fügen eine HTTP-POST-Aktion hinzu, die alle verbleibenden virtuellen Computer herunterfährt.

    ![Azure – Logik-App – Hinzufügen einer Aktion](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)

9.  Geben Sie **HTTP** ein, um nach der HTTP-Aktion zu suchen, und wählen Sie die **HTTP – HTTP**-Aktion aus.
10. Wählen Sie **post** als Wert für **Methode** aus.
11. Geben Sie die URL für den Webhook mit dem Namen **Complete** ein, den Sie zuvor in diesem Tutorial mit dem Wert **Uri** erstellt haben.

    ![Azure – Logik-App – Hinzufügen einer Aktion](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)

12. Wählen Sie im Feld **Bei TRUE** die Option **Aktion hinzufügen** aus. Sie fügen eine E-Mail-Aktion hinzu, die eine E-Mail-Benachrichtigung sendet, die dem Empfänger mitteilt, dass die verbleibenden virtuellen Computer heruntergefahren wurden.
13. Suchen Sie nach „send email“, und wählen Sie eine *E-Mail*-Aktion auf Basis des von Ihnen verwendeten E-Mail-Diensts aus.
14. Fügen Sie den Text für **An**, **Betreff** und **Text** für die E-Mail hinzu, die dem Empfänger mitteilt, dass die optionalen VMs heruntergefahren wurden. Verwenden Sie **BudgetName** und den dynamischen Inhalt **NotificationThresholdAmount** zum Auffüllen der Felder „Betreff“ und „Text“.

    ![Azure – Logik-App – Details zum E-Mail-Senden](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)

15. Klicken Sie am oberen Rand des Blatts **Logik-App-Designer** auf **Speichern**.

### <a name="logic-app-summary"></a>Logik-App-Übersicht

So sieht Ihre Logik-App aus, wenn Sie fertig sind. In den grundlegendsten Szenarien, in denen Sie keine schwellenwertbasierte Orchestrierung benötigen, können Sie das Automatisierungsskript direkt von **Monitor** aus aufrufen und den **Logik-App**-Schritt überspringen.

   ![Azure – Logik-App – Vollständige Ansicht](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

Wenn Sie Ihre Logik-App gespeichert haben, wird eine URL generiert, die Sie aufrufen können. Sie verwenden diese URL im nächsten Abschnitt dieses Tutorials.

## <a name="create-an-azure-monitor-action-group"></a>Erstellen einer Azure Monitor-Aktionsgruppe

Eine Aktionsgruppe ist eine Sammlung von Benachrichtigungseinstellungen, die Sie definieren. Wenn eine Warnung ausgelöst wird, kann eine bestimmte Aktionsgruppe die Warnung mittels einer Benachrichtigung empfangen. Eine Azure-Warnung löst proaktiv basierend auf bestimmten Bedingungen eine Benachrichtigung aus und bietet die Möglichkeit, Maßnahmen zu ergreifen. Eine Warnung kann Daten aus mehreren Quellen einschließlich Metriken und Protokollen nutzen.

Aktionsgruppen sind der einzige Endpunkt, den Sie in Ihr Budget integrieren. Sie können Benachrichtigungen in einer Vielzahl von Kanälen einrichten, aber für dieses Szenario konzentrieren Sie sich auf die Logik-App, die Sie zuvor in diesem Tutorial erstellt haben.

### <a name="create-an-action-group-in-azure-monitor"></a>Erstellen einer Aktionsgruppe in Azure Monitor

Bei der Erstellung der Aktionsgruppe verweisen Sie auf die Logik-App, die Sie zuvor in diesem Tutorial erstellt haben.

1.  Wenn Sie noch nicht beim [Azure-Portal](https://portal.azure.com/) angemeldet sind, melden Sie sich an, und wählen Sie **Alle Dienste** > **Monitor** aus.
2.  Wählen Sie **Aktionsgruppen** im Abschnitt **Einstellung** aus.
3.  Wählen Sie **Hinzufügen einer Aktionsgruppe** im Blatt **Aktionsgruppen** aus.
4.  Fügen Sie folgende Elemente hinzu, und überprüfen Sie sie:
    - Aktionsgruppenname
    - Kurzname
    - Abonnement
    - Ressourcengruppe

    ![Azure – Logik-App – Hinzufügen einer Aktionsgruppe](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)

5.  Fügen Sie im Bereich **Aktionsgruppe hinzufügen** eine Logik-App-Aktion hinzu. Benennen Sie die Aktion **Budget-BudgetLA**. Wählen Sie im Bereich **Logik-App** das **Abonnement** und die **Ressourcengruppe** aus. Wählen Sie dann die **Logik-App** aus, die Sie zuvor in diesem Tutorial erstellt haben.
6.  Klicken Sie auf **OK**, um die Logik-App festzulegen. Wählen Sie dann **OK** im Bereich **Aktionsgruppe hinzufügen** aus, um die Aktionsgruppe zu erstellen.

Sie verfügen nun über alle unterstützenden Komponenten, die erforderlich sind, um Ihr Budget effektiv zu orchestrieren. Jetzt müssen Sie lediglich noch das Budget erstellen und für die Verwendung der von Ihnen erstellten Aktionsgruppe konfigurieren.

## <a name="create-the-azure-budget"></a>Erstellen des Azure-Budgets

Im Azure-Portal können Sie mithilfe des [Budgetfeatures](../cost-management/tutorial-acm-create-budgets.md) in Cost Management ein Budget erstellen. Alternativ dazu können Sie ein Budget auch mithilfe von REST-APIs, PowerShell-Cmdlets oder der Befehlszeilenschnittstelle erstellen. Im folgenden Verfahren wird die REST-API verwendet. Vor dem Aufrufen der REST-API benötigen Sie ein Autorisierungstoken. Um ein Autorisierungstoken zu erstellen, können Sie das [ARMClient](https://github.com/projectkudu/ARMClient)-Projekt verwenden. Mit dem **ARMClient** können Sie sich beim Azure Resource Manager authentifizieren und ein Token zum Aufrufen der APIs erhalten.

### <a name="create-an-authentication-token"></a>Erstellen eines Authentifizierungstokens

1.  Navigieren Sie auf GitHub zum [ARMClient](https://github.com/projectkudu/ARMClient)-Projekt.
2.  Klonen Sie das Repository, um eine lokale Kopie zu erhalten.
3.  Öffnen Sie das Projekt in Visual Studio, und erstellen Sie es.
4.  Nachdem der Build erfolgreich erstellt wurde, muss die ausführbare Datei sich im *\bin\debug*-Ordner befinden.
5.  Führen Sie den ARMClient aus. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie im Stammverzeichnis des Projekts zum *\bin\debug*-Ordner.
6.  Um sich anzumelden und zu authentifizieren, geben Sie an der Eingabeaufforderung den folgenden Befehl ein:<br>
    `ARMClient login prod`
7.  Kopieren Sie die **Abonnement-GUID** aus der Ausgabe.
8.  Um ein Autorisierungstoken in die Zwischenablage zu kopieren, geben Sie den folgenden Befehl an der Eingabeaufforderung ein, aber verwenden Sie unbedingt die kopierte Abonnement-ID aus dem vorherigen Schritt: <br>
    `ARMClient token <subscription GUID from previous step>`

    Sobald Sie die vorherigen Schritte abgeschlossen haben, sehen Sie Folgendes:<br>
    **Token copied to clipboard successfully. (Token wurde erfolgreich in die Zwischenablage kopiert.)**
9.  Speichern Sie das Token zur Verwendung für Schritte im nächsten Abschnitt dieses Tutorials.

### <a name="create-the-budget"></a>Erstellen des Budgets

Als Nächstes konfigurieren Sie **Postman** zum Erstellen eines Budgets durch Aufrufen der Azure Consumption-REST-APIs. Postman ist eine API-Entwicklungsumgebung. Sie werden Umgebungs- und Sammlungsdateien in Postman importieren. Die Sammlung enthält gruppierte Definitionen von HTTP-Anforderungen, die Azure Consumption-REST-APIs aufrufen. Die Umgebungsdatei enthält Variablen, die von der Sammlung verwendet werden.

1.  Laden Sie den [Postman-REST-Client](https://www.getpostman.com/) herunter, und öffnen Sie ihn, um die REST-APIs auszuführen.
2.  Erstellen Sie in Postman eine neue Anforderung.

    ![Postman – Erstellen einer neuen Anforderung](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)

3.  Speichern Sie die neue Anforderung als Sammlung, sodass die neue Anforderung keinen Inhalt hat.

    ![Postman – Speichern der neuen Anforderung](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)

4.  Ändern Sie die Anforderung von einer `Get`- in eine `Put`-Aktion.
5.  Ändern Sie die folgende URL, indem Sie `{subscriptionId}` mit der **Abonnement-ID** ersetzen, die Sie im vorherigen Abschnitt dieses Tutorials verwendet haben. Ändern Sie die URL außerdem so, dass „SampleBudget“ als Wert für `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31` einbezogen wird.
6.  Wählen Sie in Postman die Registerkarte **Headers** aus.
7.  Fügen Sie einen neuen **Key (Schlüssel)** mit dem Namen „Authorization“ (Autorisierung) hinzu.
8.  Legen Sie für **Value (Wert)** ein Token fest, das am Ende des letzten Abschnitts mit dem ArmClient erstellt wurde.
9.  Wählen Sie in Postman die Registerkarte **Body (Text)** aus.
10. Wählen Sie die Schaltflächenoption **raw (unformatiert)** aus.
11. Fügen Sie die unten stehende Beispielbudgetdefinition in das Textfeld ein. Allerdings müssen Sie die Parameter **subscriptionid**, **budgetname** und **actiongroupname** durch Ihre Abonnement-ID, einen eindeutigen Namen für Ihr Budget und den Namen der Aktionsgruppe ersetzen, die Sie in URL und Anforderungstext erstellt haben:

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
                "filters": {
                },
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [
                    ],
                    "contactRoles": [
                    ],
                    "contactGroups": [
                    "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}
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
    ```
12. Wählen Sie **Send (Senden)** zum Senden der Anforderung aus.

Sie haben jetzt alle Komponenten, die Sie zum Aufrufen der [Budgets-API](https://docs.microsoft.com/rest/api/consumption/budgets) benötigen. Die Budgets-API-Referenz verfügt über zusätzliche Details der spezifischen Anforderungen, einschließlich der folgenden:
    - **budgetName**: Mehrere Budgets werden unterstützt.  Budgetnamen müssen eindeutig sein.
    - **category**: Entweder **Kosten** oder **Nutzung**. Die API unterstützt sowohl Kosten- als auch Nutzungsbudgets.
    - **timeGrain**: Ein Monats-, Quartals- oder Jahresbudget. Der Betrag wird am Ende des Zeitraums zurückgesetzt.
    - **filters**: Mit Filtern können Sie das Budget auf einen bestimmten Satz von Ressourcen im ausgewählten Bereich eingrenzen. Ein Filter kann beispielsweise eine Sammlung von Ressourcengruppen für ein Budget auf Abonnementebene sein.
    - **notifications**: Legt die Benachrichtigungsdetails und Schwellenwerte fest. Sie können mehrere Schwellenwerte einrichten und eine E-Mail-Adresse oder Aktionsgruppe angeben, um eine Benachrichtigung zu erhalten.

## <a name="summary"></a>Zusammenfassung

In diesem Tutorial haben Sie Folgendes gelernt:
- Wie Sie ein Azure Automation-Runbook zum Beenden von VMs erstellen.
- Wie Sie eine Azure-Logik-App erstellen, die basierend auf den Budgetschwellenwerten ausgelöst wird, und das zugehörige Runbook mit den entsprechenden Parametern aufrufen.
- Wie Sie eine Azure Monitor-Aktionsgruppe erstellen, die konfiguriert wurde, um bei Erreichen des Budgetschwellenwerts die Azure-Logik-App auszulösen.
- Wie Sie das Azure-Budget mit den gewünschten Schwellenwerten erstellen und es mit der Aktionsgruppe verknüpfen.

Sie verfügen nun über ein voll funktionsfähiges Budget für Ihr Abonnement, das Ihre VMs beendet, wenn Sie die konfigurierten Budgetschwellenwerte erreichen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure-Abrechnungsszenarien finden Sie unter [Automatisierungsszenarien zur Abrechnungs- und Kostenverwaltung](billing-cost-management-automation-scenarios.md).
