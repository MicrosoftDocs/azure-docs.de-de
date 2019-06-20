---
title: Überwachen von Änderungen an einem virtuellen Computer – Azure Event Grid und Logic Apps | Microsoft-Dokumentation
description: Überprüfen auf Konfigurationsänderungen an virtuellen Computern (VMs) mit Azure Event Grid und Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 33634773b436114f4a5f2942028710ae50e0e703
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65801083"
---
# <a name="tutorial-monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Tutorial: Überwachen von Änderungen an einem virtuellen Computer mit Azure Event Grid und Logic Apps

Wenn bestimmte Ereignisse in Azure- oder Drittanbieterressourcen auftreten, können Sie einen automatisierten [Logik-App-Workflow](../logic-apps/logic-apps-overview.md) starten. Diese Ressourcen können derartige Ereignisse in einem [Azure-Ereignisraster](../event-grid/overview.md) veröffentlichen. Das Ereignisraster überträgt diese Ereignisse wiederum mithilfe von Push an Abonnenten, die als Endpunkte Warteschlangen, Webhooks, oder [Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) aufweisen. Als Abonnent kann Ihre Logik-App auf diese Ereignisse vom Ereignisraster warten, bevor automatisierte Workflows zur Durchführung von Aufgaben ausgeführt werden – ohne dass Sie entsprechenden Code schreiben müssen.

Im Folgenden werden einige Beispiele für Ereignisse aufgeführt, die Herausgeber mit dem Azure Event Grid-Dienst an Abonnenten senden können:

* Eine Ressource wird erstellt, gelesen, aktualisiert oder gelöscht. Beispielsweise können Sie Änderungen überwachen, bei denen möglicherweise Gebühren für Ihr Azure-Abonnement anfallen und die sich auf Ihre Abrechnung auswirken könnten. 
* Eine Person wird zu einem Azure-Abonnement hinzugefügt oder aus dieser entfernt.
* Ihre App führt eine bestimmte Aktion durch.
* Eine neue Meldung wird in einer Warteschlange angezeigt.

In diesem Tutorial wird eine Logik-App erstellt, die Änderungen an einer VM überwacht und E-Mails über diese Änderungen sendet. Wenn Sie eine Logik-App mit einem Ereignisabonnement für eine Azure-Ressource erstellen, werden Ereignisse aus dieser Ressource über ein Ereignisraster an die Logik-App weitergeleitet. Das Tutorial führt Sie durch das Erstellen dieser Logik-App:

![Übersicht – Überwachen von virtuellen Computern mit Event Grid und Logic Apps](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen Sie eine Logik-App zur Überwachung von Ereignissen über ein Ereignisraster.
> * Fügen Sie eine Bedingung zur Prüfung auf Änderungen an einem virtuellen Computer hinzu.
> * Senden Sie bei Änderungen an Ihrem virtuellen Computer E-Mails.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Ein E-Mail-Konto bei einem von Logic Apps unterstützten E-Mail-Anbieter wie Office 365 Outlook, Outlook.com oder Gmail zum Senden von Benachrichtigungen. Informationen zu Connectors für andere Anbieter finden Sie in [dieser Liste](/connectors/). 

  Dieses Tutorial verwendet ein Office 365 Outlook-Konto. Bei Verwendung eines anderen E-Mail-Kontos bleiben die allgemeinen Schritte zwar gleich, die Benutzeroberfläche sieht aber unter Umständen etwas anders aus.

* Ein [virtueller Computer](https://azure.microsoft.com/services/virtual-machines). Erstellen Sie eine VM mithilfe des [Tutorials zum Erstellen eines virtuellen Computers](../virtual-machines/windows/quick-create-portal.md), wenn Sie dies nicht bereits getan haben. Zur Veröffentlichung von Ereignissen auf dem virtuellen Computer [müssen Sie keine weiteren Aktionen durchführen](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Erstellen einer leeren Logik-App

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com) an. 

1. Klicken Sie im Hauptmenü von Azure auf **Ressource erstellen** > **Integration** > **Logik-App**.

   ![Erstellen einer Logik-App](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. Geben Sie unter **Logik-App** Informationen zu Ihrer Logik-App ein. Wenn Sie fertig sind, wählen Sie **Erstellen** aus.

   ![Angeben von Details zur Logik-App](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Eigenschaft | Empfohlener Wert | Beschreibung |
   | -------- | --------------- | ----------- |
   | **Name** | <*logic-app-name*> | Geben Sie einen eindeutigen Namen für Ihre Logik-App an. |
   | **Abonnement** | <*Name des Azure-Abonnements*> | Wählen Sie für alle Dienste in diesem Tutorial das gleiche Azure-Abonnement. |
   | **Ressourcengruppe** | <*Azure-resource-group*> | Wählen Sie für alle Dienste in diesem Tutorial die gleiche Azure-Ressourcengruppe. |
   | **Location** | <*Azure-Datencenterregion*> | Wählen Sie für alle Dienste in diesem Tutorial die gleiche Azure-Region. |
   |||

   Sie haben nun eine Azure-Ressource für Ihre Logik-App erstellt. 

1. Nachdem Ihre Logik-App von Azure bereitgestellt wurde, zeigt der Designer für Logik-Apps eine Seite mit einem Einführungsvideo und häufig verwendeten Triggern an. Scrollen Sie nach unten, bis der Bereich unter dem Video und den Triggern angezeigt wird. 

1. Wählen Sie unter **Vorlagen** die Option **Leere Logik-App**.

   ![Auswählen der Logik-App-Vorlage](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Der Designer für Logik-Apps zeigt Ihnen jetzt [*Trigger*](../logic-apps/logic-apps-overview.md#logic-app-concepts) an, die Sie verwenden können, um Ihre Logik-App zu starten. Jede Logik-App muss mit einem Trigger beginnen, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder eine bestimmte Bedingung erfüllt wird. 
   Bei jeder Auslösung des Triggers erstellt Azure Logic Apps eine Workflowinstanz, die Ihre Logik-App ausführt.

## <a name="add-event-grid-trigger"></a>Hinzufügen eines Event Grid-Triggers 

Fügen Sie nun einen Event Grid-Trigger hinzu, der die Ressourcengruppe für Ihren virtuellen Computer überwacht. 

1. Geben Sie im Suchfeld des Designers „Event Grid“ als Filter ein. Wählen Sie in der Triggerliste den folgenden Trigger aus: **Wenn ein Ressourcenereignis eintritt – Azure Event Grid**

   ![Wählen Sie den folgenden Trigger aus: „Bei einem Ressourcenereignis“](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Wenn Sie aufgefordert werden, melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos bei Azure Event Grid an. Überprüfen Sie in der Liste **Mandant**, in der der Azure Active Directory-Mandant angezeigt wird, der Ihrem Azure-Abonnement zugeordnet ist, dass der richtige Mandant angezeigt wird.

   ![Anmelden mit Ihren Azure-Anmeldeinformationen](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Wenn Sie mit einem persönlichen Microsoft-Konto wie @outlook.com oder @hotmail.com angemeldet sind, wird der Event Grid-Trigger möglicherweise nicht ordnungsgemäß angezeigt. Um dieses Problem zu umgehen, wählen Sie [Verbindung über Dienstprinzipal herstellen](../active-directory/develop/howto-create-service-principal-portal.md), oder authentifizieren Sie sich als Mitglied des Azure Active Directory, dass Ihrem Azure-Abonnement zugeordnet ist, z.B. *Benutzername*@emailoutlook.onmicrosoft.com.

1. Abonnieren Sie jetzt für Ihre Logik-App Ereignisse eines Herausgebers. Legen Sie die Details für Ihr Ereignisabonnement wie in der folgenden Tabelle angegeben fest:

   ![Angeben von Details für das Ereignisabonnement](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Eigenschaft | Erforderlich | Value | BESCHREIBUNG |
   | -------- | -------- | ----- | ----------- |
   | **Abonnement** | Ja | <*event-publisher-Azure-subscription-name*> | Wählen Sie den Namen für das Azure-Abonnement aus, das dem Ereignisherausgeber zugeordnet ist. Wählen Sie für dieses Tutorial den Namen des Azure-Abonnements für Ihren virtuellen Computer aus. |
   | **Ressourcentyp** | Ja | <*event-publisher-Azure-resource-type*> | Wählen Sie den Azure-Ressourcentyp für den Ereignisherausgeber aus. Wählen Sie für dieses Tutorial diesen Wert aus, um Azure-Ressourcengruppen zu überwachen: <p><p>**Microsoft.Resources.ResourceGroups** |
   | **Ressourcenname** |  Ja | <*event-publisher-Azure-resource-name*> | Wählen Sie den Namen der Azure-Ressource für den Ereignisherausgeber aus. Diese Liste variiert, basierend auf dem von Ihnen ausgewählten Ressourcentyp. Wählen Sie für dieses Tutorial den Namen der Azure-Ressourcengruppe für Ihren virtuellen Computer aus. |
   | **Ereignistypelement** |  Nein  | <*event-types*> | Wählen Sie mindestens einen spezifischen Ereignistyp aus, um zu filtern und an Ihr Event Grid zu senden. Beispielsweise können Sie optional diese Ereignistypen hinzufügen, um zu erkennen, wenn Ressourcen geändert oder gelöscht werden: <p><p>- **Microsoft.Resources.ResourceActionSuccess** <br>- **Microsoft.Resources.ResourceDeleteSuccess** <br>- **Microsoft.Resources.ResourceWriteSuccess** <p>Weitere Informationen finden Sie in den folgenden Themen: <p><p>- [Grundlegendes zur Ereignisfilterung](../event-grid/event-filtering.md) <br>- [Filtern von Ereignissen für das Event Grid](../event-grid/how-to-filter-events.md) <br>- [Azure Event Grid-Ereignisschema für Ressourcengruppen](../event-grid/event-schema-resource-groups.md) |
   | **Abonnementname** | Nein  | <*event-subscription-name*> | Geben Sie einen eindeutigen Namen für Ihr Ereignisabonnement an. |
   | Wählen Sie für optionale Einstellungen **Neuen Parameter hinzufügen** aus. | Nein  | {siehe Beschreibungen} | * **Präfixfilter:** Lassen Sie diese Eigenschaft für dieses Tutorial leer. Das Standardverhalten entspricht allen Werten. Allerdings können Sie als Filter eine Präfixzeichenfolge wie einen Pfad und einen Parameter für eine bestimmte Ressource angeben. <p>* **Suffixfilter:** Lassen Sie diese Eigenschaft für dieses Tutorial leer. Das Standardverhalten entspricht allen Werten. Wenn nur bestimmte Dateitypen angezeigt werden sollen, können Sie als Filter jedoch eine Suffixzeichenfolge wie eine Dateinamenerweiterung angeben. |
   |||

   Anschließend sieht Ihr Event Grid-Trigger etwa wie im folgenden Beispiel aus:

   ![Details eines Event Grid-Beispieltriggers](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

1. Speichern Sie Ihre Logik-App. Wählen Sie auf der Symbolleiste des Designers **Speichern**. Um die Details einer Aktion in Ihrer Logik-App zu reduzieren und auszublenden, wählen Sie die Titelleiste der Aktion aus.

   ![Speichern Ihrer Logik-App](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Wenn Sie Ihre Logik-App mit einen Ereignisrastertrigger speichern, erstellt Azure automatisch ein Ereignisabonnement für Ihre Logik-App in Ihrer ausgewählten Ressource. Veröffentlicht die Ressource nun ein Ereignis im Ereignisraster, überträgt dieses Ereignisraster das Ereignis mithilfe von Push automatisch an Ihre Logik-App. Dieses Ereignis löst Ihre Logik-App aus, erstellt dann eine Instanz des in den folgenden Schritten definierten Workflows und startet deren Ausführung.

Ihre Logik-App ist nun live geschaltet und lauscht auf Ereignisse im Ereignisraster, führt allerdings erst Aktionen durch, wenn Sie diese dem Workflow hinzufügen. 

## <a name="add-condition"></a>Bedingung hinzufügen

Um den Workflow Ihrer Logik-App nur bei einem bestimmten Ereignis auszuführen, fügen Sie eine Bedingung hinzu, die auf Schreibvorgänge des virtuellen Computers überprüft. Wenn diese Bedingung erfüllt ist, sendet Ihre Logik-App Ihnen per E-Mail Details zum aktualisierten virtuellen Computer.

1. Wählen Sie im Designer für Logik-Apps unter dem Event Grid-Trigger **Neuer Schritt** aus.

   ![„Neuer Schritt“ auswählen](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. Geben Sie im Suchfeld den Begriff „Bedingung“ als Filter ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Condition**

   ![Bedingung hinzufügen](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Der Designer für Logik-Apps fügt Ihrem Workflow eine leere Bedingung einschließlich der abhängig davon, ob die Bedingung „true“ oder „false“ ist, zu befolgenden Aktionspfade hinzu.

   ![Leere Bedingung](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Benennen Sie den Titel der Bedingung in `If a virtual machine in your resource group has changed` um. Wählen Sie in der Titelleiste der Bedingung die Schaltfläche mit den Auslassungspunkten ( **...** ) aus, und wählen Sie anschließend **Umbenennen** aus.

   ![Umbenennen der Bedingung](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Erstellen Sie eine Bedingung, die das `body`-Ereignis auf ein `data`-Objekt überprüft, bei dem die Eigenschaft `operationName` dem Vorgang `Microsoft.Compute/virtualMachines/write` entspricht. Weitere Informationen finden Sie unter [Event Grid-Ereignisschema](../event-grid/event-schema.md).

   1. Klicken Sie in der ersten Zeile unter **Und** innerhalb des linken Felds. Wählen Sie in der Liste mit dynamischem Inhalt, die angezeigt wird, **Ausdruck** aus.

      ![„Ausdruck“ auswählen](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. Geben Sie im Ausdrucks-Editor diesen Ausdruck ein, und wählen Sie **OK** aus: 

      `triggerBody()?['data']['operationName']`

      Beispiel: 

      ![„Ausdruck“ auswählen](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. Behalten Sie im mittleren Feld den Operator **gleich** bei.

   1. Geben Sie in das rechte Feld diesen Wert ein:

      `Microsoft.Compute/virtualMachines/write`

   Ihre fertig gestellte Bedingung sieht nun wie im folgenden Beispiel aus:

   ![Fertige Bedingung](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Wenn Sie von der Entwurfsansicht zur Programmieransicht und wieder zurück in die Entwurfsansicht wechseln, wird der Ausdruck, den Sie in der Bedingung angegeben haben, in das Token **data.operationName** aufgelöst:

   ![Aufgelöste Bedingung](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Speichern Sie Ihre Logik-App.

## <a name="send-email-notifications"></a>Senden von E-Mail-Benachrichtigungen

Fügen Sie nun eine [*Aktion*](../logic-apps/logic-apps-overview.md#logic-app-concepts) hinzu, um eine E-Mail zu erhalten, wenn die angegebene Bedingung erfüllt ist.

1. Wählen Sie im Feld **Falls erfüllt** der Bedingung die Option **Aktion hinzufügen**.

   ![Hinzufügen einer Aktion bei Erfüllung der Bedingung](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. Geben Sie in das Suchfeld „E-Mail senden“ als Filter ein. Suchen Sie abhängig von Ihrem E-Mail-Anbieter nach dem entsprechenden Connector, und wählen Sie diesen aus. Wählen Sie anschließend die Aktion „E-Mail senden“ für Ihren Connector aus. Beispiel:  

   * Wählen Sie für ein Geschäfts-, Schul- oder Unikonto in Azure den Office 365 Outlook-Connector aus. 

   * Wählen Sie für persönliche Microsoft-Konten den Outlook.com-Connector aus. 

   * Wählen Sie für Gmail-Konten den Gmail-Connector aus. 

   Dieses Tutorial fährt mit dem Office 365 Outlook-Connector fort. 
   Wenn Sie einen anderen Anbieter verwenden, werden dieselben Schritte durchgeführt, allerdings kann das Erscheinungsbild der Benutzeroberfläche eventuell etwas abweichen. 

   ![Auswählen der Aktion „E-Mail senden“](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Wenn noch keine Verbindung für Ihren E-Mail-Anbieter besteht, melden Sie sich nach Aufforderung zur Authentifizierung bei Ihrem E-Mail-Konto an.

1. Benennen Sie den Titel „E-Mail senden“ in diesen Titel um: `Send email when virtual machine updated`

1. Legen Sie Details für die E-Mail entsprechend den Angaben in der folgenden Tabelle fest:

   ![Leere E-Mail-Aktion](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Um aus Ergebnissen aus vorherigen Schritten in Ihrem Workflow auszuwählen, klicken Sie in ein Bearbeitungsfeld, sodass die Liste mit dynamischen Inhalten angezeigt wird, oder wählen Sie **Dynamischen Inhalt hinzufügen** aus. Um weitere Ergebnisse anzuzeigen, wählen Sie für jeden Abschnitt in der Liste **Mehr anzeigen** aus. Um die Liste mit dynamischen Inhalten zu schließen, wählen Sie erneut **Dynamischen Inhalt hinzufügen** aus.

   | Eigenschaft | Erforderlich | Value | BESCHREIBUNG |
   | -------- | -------- | ----- | ----------- |
   | **An** | Ja | <*Empfänger\@Domäne*> | Geben Sie die E-Mail-Adresse des Empfängers ein. Zu Testzwecken können Sie hier Ihre eigene E-Mail-Adresse angeben. |
   | **Betreff** | Ja | Ressource aktualisiert: **Betreff** | Geben Sie den Inhalt des Betreffs der E-Mail ein. Geben Sie für dieses Tutorial den angegebenen Text ein, und wählen Sie das Feld **Betreff** des Ereignisses aus. Hier enthält Ihr E-Mail-Betreff den Namen für die aktualisierte Ressource (virtueller Computer). |
   | **Text** | Ja | Ressource: **Thema** <p>Ereignistyp: **Ereignistyp**<p>Ereignis-ID: **ID**<p>Zeit: **Ereigniszeit** | Geben Sie den Inhalt des Texts der E-Mail ein. Geben Sie für dieses Tutorial den angegebenen Text ein, und wählen Sie die Felder **Thema**, **Ereignistyp**, **ID** und **Ereigniszeit** des Ereignisses, damit Ihre E-Mail die Ressource enthält, die das Ereignis ausgelöst hat, den Ereignistyp, den Ereigniszeitstempel sowie die Ereignis-ID für das Update. Für dieses Tutorial ist die Ressource die im Trigger ausgewählte Azure-Ressourcengruppe. <p>Um Ihrem Inhalt leere Zeilen hinzuzufügen, drücken Sie UMSCHALT + EINGABETASTE. |
   ||||

   > [!NOTE]
   > Wenn Sie ein Feld auswählen, das ein Array darstellt, fügt der Designer automatisch eine **ForEach**-Schleife für die Aktion hinzu, die auf das Array verweist. Auf diese Weise führt Ihre Logik-App diese Aktion für jedes Arrayelement durch.

   Ihre E-Mail-Aktion könnte nun folgendem Beispiel entsprechen:

   ![Auswählen der in einer E-Mail einzuschließenden Ausgaben](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Anschließend sieht Ihre Logik-App in etwa wie im folgenden Beispiel aus:

   ![Fertiggestellte Logik-App](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Speichern Sie Ihre Logik-App. Um Details zu den einzelnen Aktionen in Ihrer Logik-App zu reduzieren und auszublenden, wählen Sie die Titelleiste der Aktion aus.

   Die Logik-App ist nun live geschaltet, wartet jedoch vor der Durchführung von Aktionen auf Änderungen an Ihrem virtuellen Computer. 
   Um nun Ihre Logik-App zu testen, fahren Sie mit dem nächsten Abschnitt fort.

## <a name="test-your-logic-app-workflow"></a>Testen Ihres Logik-App-Workflows

1. Um zu überprüfen, ob Ihre Logik-App die angegebenen Ereignisse abruft, aktualisieren Sie Ihren virtuellen Computer.

   Beispielsweise können Sie die Größe Ihrer VM im Azure-Portal oder [mit Azure PowerShell ändern](../virtual-machines/windows/resize-vm.md).

   Nach nur wenigen Minuten sollten Sie eine E-Mail erhalten. Beispiel: 

   ![E-Mail zum Update eines virtuellen Computers](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Um den Verlauf der Ausführungen und Trigger für Ihre Logik-App zu überprüfen, wählen Sie im Menü Ihrer Logik-App **Übersicht** aus. Um weitere Details zu einer Ausführung anzuzeigen, wählen Sie die Zeile dieser Ausführung aus.

   ![Verlauf von Logik-App-Ausführungen](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. Um die Eingaben und Ausgaben für die einzelnen Schritte anzuzeigen, erweitern Sie den Schritt, den Sie überprüfen möchten. Diese Informationen helfen Ihnen bei der Diagnose und beim Debuggen von Problemen in Ihrer Logik-App.

   ![Details des Verlaufs von Logik-App-Ausführungen](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Herzlichen Glückwunsch! Sie haben eine Logik-App erstellt und ausgeführt, die Ressourcenereignisse durch einen Ereignisraster überwacht und Ihnen eine E-Mail sendet, wenn derartige Ereignisse auftreten sollten. Außerdem haben Sie erfahren, wie einfach Sie Workflows zur Automatisierung von Prozessen und Integration von Systemen sowie Clouddiensten erstellen können.

Sie können andere Konfigurationsänderungen mit Ereignisrastern und Logik-Apps überwachen wie etwas Folgende:

* Ein virtueller Computer erhält rollenbasierte Zugriffssteuerungsrechte (Role-Based Access Control, RBAC).
* Es werden Änderungen an einer Netzwerksicherheitsgruppe (Network Security Group, NSG) in einer Netzwerkschnittstelle (Network Interface, NIC) vorgenommen.
* Datenträger für einen virtuellen Computer werden hinzugefügt oder entfernt.
* Der NIC eines virtuellen Computers wird eine öffentliche IP-Adresse zugewiesen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Tutorial werden Ressourcen verwendet und Aktionen durchgeführt, für die Gebühren in Ihrem Azure-Abonnement anfallen. Wenn Sie mit der Durchführung des Tutorials und dem Testen fertig sind, deaktivieren oder löschen Sie unbedingt alle Ressourcen, für deren Gebühren Sie nicht aufkommen möchten.

* Deaktivieren Sie Ihre Logik-App, um die Ausführung zu beenden, ohne Ihre Arbeit zu löschen. Wählen Sie in Ihrem Logik-App-Menü **Übersicht** aus. Wählen Sie auf der Symbolleiste **Deaktivieren** aus.

  ![Deaktivieren der Logik-App](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Falls das Logik-App-Menü nicht angezeigt wird, können Sie zum Azure-Dashboard zurückkehren und Ihre Logik-App erneut öffnen.

* Wählen Sie im Menü der Logik-App **Übersicht** aus, um Ihre Logik-App endgültig zu löschen. Wählen Sie in der Symbolleiste die Option **Löschen** aus. Bestätigen Sie, dass Sie Ihre Logik-App löschen möchten, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Event Grid](../event-grid/custom-event-quickstart.md)
