---
title: Erstellen genehmigungsbasierter automatisierter Workflows
description: 'Tutorial: Erstellen eines genehmigungsbasierten automatisierten Workflows, mit dem Adressenlistenabonnements mithilfe von Azure Logic Apps verarbeitet werden'
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/07/2020
ms.openlocfilehash: 1690b8d143b86e5caa691f5f8f479f715f57f0c8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054652"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Tutorial: Erstellen automatisierter genehmigungsbasierter Workflows mithilfe von Azure Logic Apps

In diesem Tutorial erfahren Sie, wie Sie eine exemplarische [Logik-App](../logic-apps/logic-apps-overview.md) erstellen, die einen genehmigungsbasierten Workflow automatisiert. Diese exemplarische Logik-App verarbeitet gezielt Abonnementanforderungen für eine Adressenliste, die durch den Dienst [MailChimp](https://mailchimp.com/) verwaltet wird. Diese Logik-App umfasst mehrere Schritte: Sie überwacht ein E-Mail-Konto auf Anforderungen, sendet diese Anforderungen zur Genehmigung, überprüft, ob die Anforderungen genehmigt werden, fügt genehmigte Mitglieder der Adressenliste hinzu und überprüft, ob neue Mitglieder der Liste hinzugefügt werden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen einer leeren Logik-App
> * Hinzufügen eines Triggers, der E-Mails auf Abonnementanforderungen überwacht
> * Hinzufügen einer Aktion, die E-Mails zur Genehmigung/Ablehnung dieser Anforderungen sendet
> * Hinzufügen einer Bedingung, die die Genehmigungsantwort überprüft
> * Hinzufügen einer Aktion, die genehmigte Mitglieder der Adressenliste hinzufügt
> * Hinzufügen einer Bedingung, die überprüft, ob die Mitglieder der Liste hinzugefügt wurden
> * Hinzufügen einer Aktion zum Senden von E-Mails, die darüber informieren, ob die Mitglieder der Liste hinzugefügt wurden

Am Ende entspricht Ihre Logik-App grob dem folgenden Workflow:

![Allgemeine Übersicht über die fertige Logik-App](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ein MailChimp-Konto mit einer Liste namens „test-members-ML“, der Ihre Logik-App E-Mail-Adressen für genehmigte Mitglieder hinzufügen kann. Falls Sie über kein Konto verfügen, [registrieren Sie sich für ein kostenloses Konto](https://login.mailchimp.com/signup/), und informieren Sie sich dann darüber, [wie Sie eine MailChimp-Liste erstellen](https://us17.admin.mailchimp.com/lists/#).

* Ein E-Mail-Konto eines von Logic Apps unterstützten E-Mail-Anbieters (beispielsweise Office 365 Outlook, Outlook.com oder Gmail). Informationen zu Connectors für andere Anbieter finden Sie in [dieser Liste](/connectors/). In dieser Schnellstartanleitung wird Office 365 Outlook mit einem Geschäfts-, Schul- oder Unikonto verwendet. Bei Verwendung eines anderen E-Mail-Kontos bleiben die allgemeinen Schritte zwar gleich, die Benutzeroberfläche unterscheidet sich aber ggf. etwas.

* Ein E-Mail-Konto bei Office 365 Outlook oder Outlook.com, das Genehmigungsworkflows unterstützt. Dieses Tutorial verwendet Office 365 Outlook. Bei Verwendung eines anderen E-Mail-Kontos bleiben die allgemeinen Schritte zwar gleich, die Benutzeroberfläche sieht aber unter Umständen etwas anders aus.

* Falls Ihre Logik-App über eine Firewall kommunizieren muss, mit der der Datenverkehr auf bestimmte IP-Adressen beschränkt wird, muss Folgendes sichergestellt sein: In der Firewall muss der Zugriff für IP-Adressen, die vom Logic Apps-Dienst oder der Runtime in der Azure-Region Ihrer Logik-App genutzt werden, in [eingehender](logic-apps-limits-and-config.md#inbound) *und* [ausgehender](logic-apps-limits-and-config.md#outbound) Richtung zugelassen sein. Falls für Ihre Logik-App auch [verwaltete Connectors](../connectors/apis-list.md#managed-api-connectors) (Office 365 Outlook-Connector oder SQL-Connector) oder [benutzerdefinierte Connectors](/connectors/custom-connectors/) verwendet werden, muss in der Firewall in der Azure-Region Ihrer Logik-App zusätzlich der Zugriff für *alle* [ausgehenden IP-Adressen der verwalteten Connectors](logic-apps-limits-and-config.md#outbound) zulässig sein.

## <a name="create-your-logic-app"></a>Erstellen Ihrer Logik-App

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie auf der Azure-Startseite **Ressource erstellen** aus.

1. Wählen Sie im Azure Marketplace-Menü **Integration** > **Logik-App** aus.

   ![Screenshot: Azure Marketplace-Menü mit den ausgewählten Optionen „Integration“ und „Logik-App“](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. Geben Sie im Bereich **Logik-App** die hier beschriebenen Informationen zu der Logik-App an, die Sie erstellen möchten.

   ![Screenshot: Bereich für die Logik-App-Erstellung sowie die Informationen, die für die neue Logik-App angegeben werden müssen](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Eigenschaft | Wert | BESCHREIBUNG |
   |----------|-------|-------------|
   | **Abonnement** | <*Name des Azure-Abonnements*> | Den Namen Ihres Azure-Abonnements In diesem Beispiel wird `Pay-As-You-Go` verwendet. |
   | **Ressourcengruppe** | LA-MailingList-RG | Der Name der [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md), die zum Organisieren verwandter Ressourcen verwendet wird. In diesem Beispiel wird eine neue Ressourcengruppe namens `LA-MailingList-RG` erstellt. |
   | **Name** | LA-MailingList | Der Name Ihrer Logik-App. Er darf nur Buchstaben, Ziffern, Bindestriche (`-`), Unterstriche (`_`), Klammern (`(`, `)`) und Punkte (`.`) enthalten. In diesem Beispiel wird `LA-MailingList` verwendet. |
   | **Location** | USA (Westen) | Die Region, in der die Informationen zu Ihrer Logik-App gespeichert werden sollen. In diesem Beispiel wird `West US` verwendet. |
   | **Log Analytics** | Aus | Behalten Sie die Einstellung **Aus** für die Diagnoseprotokollierung bei. |
   ||||

1. Wählen Sie abschließend **Überprüfen + erstellen** aus. Warten Sie, bis Azure die Informationen zu Ihrer Logik-App überprüft hat, und wählen Sie anschließend **Erstellen** aus.

1. Warten Sie, bis Azure Ihre App bereitgestellt hat, und wählen Sie anschließend **Zu Ressource wechseln** aus.

   Azure öffnet den Bereich zum Auswählen einer Logic Apps-Vorlage. Hier finden Sie ein Einführungsvideo, häufig verwendete Trigger sowie Logik-App-Vorlagenmuster.

1. Scrollen Sie am Video und an den gängigen Triggern vorbei nach unten zum Abschnitt **Vorlagen**, und wählen Sie **Leere Logik-App** aus.

   ![Screenshot: Bereich zum Auswählen einer Logic Apps-Vorlage mit ausgewählter Option „Leere Logik-App“](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Fügen Sie als Nächstes einen Outlook-[Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) hinzu, der auf eingehende E-Mails mit Abonnementanforderungen lauscht. Jede Logik-App beginnt mit einem Trigger, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder neue Daten eine bestimmte Bedingung erfüllen. Weitere Informationen finden Sie unter [Erstellen Ihrer ersten Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Hinzufügen eines Triggers zum Überwachen von E-Mails

1. Geben Sie im Suchfeld des Designers für Logik-Apps den Suchtext `when email arrives` ein, und wählen Sie den Trigger **Wenn eine neue E-Mail empfangen wird** aus.

   * Wählen Sie für Geschäfts-, Schul- oder Unikonten für Azure die Option **Office 365 Outlook**.
   * Wählen Sie für persönliche Microsoft-Konten die Option **Outlook.com**.

   In diesem Beispiel wird Office 365 Outlook ausgewählt.

   ![Screenshot: Suchfeld des Designers für Logik-Apps mit dem Suchbegriff „wenn E-Mail empfangen wird“ und ausgewählter Trigger „Wenn eine neue E-Mail empfangen wird“](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. Sollte noch keine Verbindung bestehen, melden Sie sich an, und authentifizieren Sie den Zugriff auf Ihr E-Mail-Konto, wenn Sie dazu aufgefordert werden.

   Von Azure Logic Apps wird eine Verbindung mit Ihrem E-Mail-Konto erstellt.

1. Geben Sie im Trigger die Kriterien für die Überprüfung neuer E-Mails an.

   1. Geben Sie den Ordner für die E-Mail-Überprüfung an, und behalten Sie für die anderen Eigenschaften die Standardwerte bei.

      ![Screenshot: Designer mit der Aktion „Wenn eine neue E-Mail empfangen wird“ und dem Wert „Posteingang“ für „Ordner“](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. Fügen Sie die Triggereigenschaft **Betrefffilter** hinzu, damit E-Mails auf der Grundlage der Betreffzeile gefiltert werden können. Wählen Sie in der Liste **Neuen Parameter hinzufügen** die Option **Betrefffilter** aus.

      ![Screenshot: Geöffnete Liste „Neuen Parameter hinzufügen“ mit ausgewählter Option „Betrefffilter“](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Weitere Informationen zu den Eigenschaften dieses Triggers finden Sie in der [Referenz zum Office 365 Outlook-Connector](/connectors/office365/) oder in der [Referenz zum Outlook.com-Connector](/connectors/outlook/).

   1. Wenn die Eigenschaft im Trigger angezeigt wird, geben Sie den folgenden Text ein: `subscribe-test-members-ML`.

      ![Screenshot: Eigenschaft „Betrefffilter“ mit dem Text „subscribe-test-members-ML“](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Blenden Sie die Triggerdetails vorerst aus, indem Sie auf die Titelleiste des Bereichs klicken, um ihn zu reduzieren.

   ![Screenshot: Reduzierter Triggerbereich](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Speichern Sie Ihre Logik-App. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

Ihre Logik-App befindet sich jetzt im Livemodus, überprüft bislang aber lediglich Ihre eingehenden E-Mails. Fügen Sie daher eine Aktion hinzu, die reagiert, wenn der Trigger ausgelöst wird.

## <a name="send-approval-email"></a>Genehmigungs-E-Mail senden

Nachdem Sie nun über einen Trigger verfügen, fügen Sie als Nächstes eine [Aktion](../logic-apps/logic-apps-overview.md#logic-app-concepts) hinzu, die eine E-Mail zur Genehmigung oder Ablehnung der Anforderung sendet.

1. Wählen Sie im Designer für Logik-Apps unter dem Trigger **Wenn eine neue E-Mail empfangen wird** die Option **Neuer Schritt** aus.

1. Geben Sie unter **Vorgang auswählen** den Text `send approval` in das Suchfeld ein, und wählen Sie die Aktion **Genehmigungs-E-Mail senden** aus.

   ![Screenshot: Nach Genehmigungsaktionen gefilterte Liste „Vorgang auswählen“ mit ausgewählter Aktion „Genehmigungs-E-Mail senden“](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Geben Sie als Nächstes die Werte für die angegebenen Eigenschaften wie hier gezeigt und beschrieben ein. Behalten Sie für alle anderen Optionen die Standardwerte bei. Weitere Informationen zu diesen Eigenschaften finden Sie in der [Referenz zum Office 365 Outlook-Connector](/connectors/office365/) oder in der [Referenz zum Outlook.com-Connector](/connectors/outlook/).

   ![Screenshot: Eigenschaften für „Genehmigungs-E-Mail senden“](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Eigenschaft | Wert | BESCHREIBUNG |
   |----------|-------|-------------|
   | **An** | <*Adresse für Genehmigungs-E-Mail*> | Die E-Mail-Adresse der genehmigenden Person. Zu Testzwecken können Sie hier Ihre eigene Adresse angeben. In diesem Beispiel wird die fiktive E-Mail-Adresse `sophiaowen@fabrikam.com` verwendet. |
   | **Subject** | `Approve member request for test-members-ML` | Ein aussagekräftiger E-Mail-Betreff. |
   | **Benutzeroptionen** | `Approve, Reject` | Diese Eigenschaft muss die auswählbaren Antwortoptionen für die genehmigende Person angeben. Standardmäßig sind das **Genehmigen** und **Ablehnen**. |
   ||||

   > [!NOTE]
   > Wenn Sie auf einige der Bearbeitungsfelder klicken, wird die Liste mit dynamischen Inhalten angezeigt. Diese können Sie vorerst ignorieren. In der Liste mit den dynamischen Inhalten werden die Ausgaben vorheriger Aktionen angezeigt, die als Eingaben für nachfolgende Aktionen in Ihrem Workflow ausgewählt werden können.
 
1. Speichern Sie Ihre Logik-App.

Fügen Sie als Nächstes eine Bedingung hinzu, die Antwortauswahl der genehmigenden Person überprüft.

## <a name="check-approval-response"></a>Überprüfen der Genehmigungsantwort

1. Wählen Sie unter der Aktion **Genehmigungs-E-Mail senden** die Option **Neuer Schritt** aus.

1. Wählen Sie unter **Vorgang auswählen** die Option **Integriert** aus. Geben Sie im Suchfeld den Suchbegriff `condition` ein, und wählen Sie die Aktion **Bedingung** aus.

   ![Screenshot: Suchfeld „Vorgang auswählen“ mit ausgewählter Option „Integriert“, dem Suchbegriff „Bedingung“ und der ausgewählten Aktion „Bedingung“](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Wählen Sie auf der Titelleiste **Bedingung** die Schaltfläche mit den **Auslassungspunkten** ( **...** ) und anschließend **Umbenennen** aus. Nennen Sie die Bedingung wie folgt: `If request approved`

   ![Screenshot: Ausgewählte Schaltfläche mit Auslassungspunkten, geöffnete Einstellungsliste und ausgewählter Umbenennungsbefehl](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. Erstellen Sie eine Bedingung, die überprüft, ob die genehmigende Person **Genehmigen** ausgewählt hat.

   1. Klicken Sie auf der linken Seite der Bedingung auf das Feld **Wert auswählen**.

   1. Wählen Sie in der angezeigten Liste mit dynamischen Inhalten unter **Genehmigungs-E-Mail senden** die Eigenschaft **SelectedOption** aus.

      ![Screenshot: Liste mit dynamischen Inhalten und ausgewählter Ausgabe „SelectedOption“ im Abschnitt „Genehmigungs-E-Mail senden“](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. Wählen Sie im mittleren Vergleichsfeld den Operator **Ist gleich** aus.

   1. Geben Sie auf der rechten Seite der Bedingung im Feld **Wert auswählen** den Text `Approve` ein.

      Danach sieht die Bedingung wie im folgenden Beispiel aus:

      ![Screenshot: Fertige Bedingung des Beispiels für eine genehmigte Anforderung](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Speichern Sie Ihre Logik-App.

Geben Sie als Nächstes die Aktion an, die Ihre Logik-App ausführen soll, wenn der Prüfer die Anforderung genehmigt. 

## <a name="add-member-to-mailchimp-list"></a>Hinzufügen eines Mitglieds zur MailChimp-Liste

Fügen Sie nun eine Aktion hinzu, die das genehmigte Mitglied Ihrer Adressenliste hinzufügt.

1. Wählen Sie in der Bedingungsverzweigung **True** die Option **Aktion hinzufügen** aus.

1. Wählen Sie unter dem Suchfeld **Vorgang auswählen** die Option **Alle** aus. Geben Sie im Suchfeld den Suchbegriff `mailchimp` ein, und wählen Sie die Aktion **Mitglied zu Liste hinzufügen** aus.

   ![Screenshot: Feld „Vorgang auswählen“ mit dem Suchbegriff „mailchimp“ und der ausgewählten Aktion „Mitglied zu Liste hinzufügen“](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Falls noch keine Verbindung mit Ihrem MailChimp-Konto besteht, werden Sie aufgefordert, sich anzumelden.

1. Geben Sie in der Aktion **Mitglied zu Liste hinzufügen** die Informationen wie hier gezeigt und beschrieben an:

   ![Screenshot: Informationen der Aktion „Mitglied zu Liste hinzufügen“](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Listen-ID** | Ja | <*Name der Adressenliste*> | Wählen Sie den Namen für Ihre MailChimp-Adressenliste aus. In diesem Beispiel wird `test-members-ML` verwendet. |
   | **E-Mail-Adresse** | Ja | <*E-Mail-Adresse des neuen Mitglieds*> | Wählen Sie in der angezeigten Liste mit dynamischen Inhalten im Abschnitt **Wenn eine neue E-Mail empfangen wird** die Option **Von** aus. Dies ist die Ausgabe des Triggers und gibt die E-Mail-Adresse für das neue Mitglied an. |
   | **Status** | Ja | <*Abonnementstatus des Mitglieds*> | Wählen Sie den Abonnementstatus aus, der für das neue Mitglied festgelegt werden soll. In diesem Beispiel wird `subscribed` ausgewählt. <p>Weitere Informationen finden Sie unter [Manage Subscribers with the MailChimp API](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/) (Verwalten von Abonnenten mit der MailChimp-API). |
   |||||

   Weitere Informationen zu den Eigenschaften der Aktion **Mitglied zu Liste hinzufügen** finden Sie in der [Referenz zum MailChimp-Connector](/connectors/mailchimp/).

1. Speichern Sie Ihre Logik-App.

Fügen Sie als Nächstes eine Bedingung hinzu, um zu prüfen, ob das neue Mitglied der Adressenliste hinzugefügt wurde. Dadurch kann Ihre Logik-App Sie darüber informieren, ob der Vorgang erfolgreich war.

## <a name="check-for-success-or-failure"></a>Überprüfen, ob der Vorgang erfolgreich war

1. Wählen Sie in der Verzweigung **True** unter der Aktion **Mitglied zu Liste hinzufügen** die Option **Aktion hinzufügen** aus.

1. Wählen Sie unter **Vorgang auswählen** die Option **Integriert** aus. Geben Sie im Suchfeld den Suchbegriff `condition` ein, und wählen Sie die Aktion **Bedingung** aus.

1. Nennen Sie die Bedingung wie folgt: `If add member succeeded`

1. Erstellen Sie eine Bedingung, die überprüft, ob das genehmigte Mitglied der Adressenliste hinzugefügt wurde:

   1. Klicken Sie auf der linken Seite der Bedingung auf das Feld **Wert auswählen**. Wählen Sie in der angezeigten Liste mit dynamischen Inhalten im Abschnitt **Mitglied zu Liste hinzufügen** die Eigenschaft **Status** aus.

      Ihre Bedingung sieht etwa wie im folgendem Beispiel aus:

      ![Screenshot: Linke Seite der Bedingung mit dem Feld „Wert auswählen“ und der Eingabe „Status“](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. Wählen Sie im mittleren Vergleichsfeld den Operator **Ist gleich** aus.

   1. Geben Sie auf der rechten Seite der Bedingung im Feld **Wert auswählen** den folgenden Text ein: `subscribed`

      Danach sieht die Bedingung wie im folgenden Beispiel aus:

      ![Screenshot: Fertige Bedingung zur Überprüfung des Abonnementerfolgsstatus](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Richten Sie als Nächstes die E-Mails ein, die gesendet werden sollen, wenn das genehmigte Mitglied erfolgreich der Adressenliste hinzugefügt wurde oder wenn das Hinzufügen nicht erfolgreich war.

## <a name="send-email-if-member-added"></a>Senden einer E-Mail, wenn das Mitglied hinzugefügt wurde

1. Wählen Sie in der Verzweigung **True** für die Bedingung **If add member succeeded** (Bei erfolgreichem Hinzufügen des Mitglieds) die Option **Aktion hinzufügen** aus.

   ![Screenshot: Verzweigung „True“ der Bedingung „If add member succeeded“ (Bei erfolgreichem Hinzufügen des Mitglieds) mit ausgewählter Option „Aktion hinzufügen“](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. Geben Sie im Suchfeld **Vorgang auswählen** den Text `outlook send email` ein, und wählen Sie die Aktion **E-Mail senden** aus.

   ![Screenshot: Suchfeld „Vorgang auswählen“ mit Eingabe „Outlook E-Mail senden“ und ausgewählter Aktion „E-Mail senden“ für die Benachrichtigung](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Nennen Sie die Aktion wie folgt: `Send email on success`

1. Geben Sie in der Aktion **Send email on success** (E-Mail senden bei Erfolg) die Informationen wie hier gezeigt und beschrieben an:

   ![Screenshot: Aktion „Send email on success“ (E-Mail senden bei Erfolg) und Angaben für die Erfolgs-E-Mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Text** | Ja | <*Text der Erfolgs-E-Mail*> | Der Inhalt der Erfolgs-E-Mail. Führen Sie für dieses Tutorial die folgenden Schritte aus: <p>1. Geben Sie den folgenden Text mit einem nachgestellten Leerzeichen ein: `New member has joined "test-members-ML":` <p>2. Wählen Sie in der angezeigten Liste mit dynamischen Inhalten die Eigenschaft **E-Mail-Adresse** aus. <p>**Hinweis**: Sollte diese Eigenschaft nicht angezeigt werden, wählen Sie neben der Überschrift des Abschnitts **Mitglied zu Liste hinzufügen** die Option **Mehr anzeigen** aus. <p>3. Geben Sie in der nächsten Zeile den folgenden Text mit einem nachgestellten Leerzeichen ein: `Member opt-in status: ` <p>4. Wählen Sie in der Liste mit den dynamischen Inhalten unter **Mitglied zu Liste hinzufügen** die Eigenschaft **Status** aus. |
   | **Subject** | Ja | <*Betreff der Erfolgs-E-Mail*> | Der Betreff der Erfolgs-E-Mail. Führen Sie für dieses Tutorial die folgenden Schritte aus: <p>1. Geben Sie den folgenden Text mit einem nachgestellten Leerzeichen ein: `Success! Member added to "test-members-ML": ` <p>2. Wählen Sie in der Liste mit den dynamischen Inhalten unter **Mitglied zu Liste hinzufügen** die Eigenschaft **E-Mail-Adresse** aus. |
   | **An** | Ja | <*Ihre E-Mail-Adresse*> | Die E-Mail-Adresse, an die die Erfolgs-E-Mail gesendet werden soll. Zu Testzwecken können Sie hier Ihre eigene E-Mail-Adresse angeben. |
   |||||

1. Speichern Sie Ihre Logik-App.

## <a name="send-email-if-member-not-added"></a>Senden einer E-Mail, wenn das Mitglied nicht hinzugefügt wurde

1. Wählen Sie in der Verzweigung **False** für die Bedingung **If add member succeeded** (Bei erfolgreichem Hinzufügen des Mitglieds) die Option **Aktion hinzufügen** aus.

   ![Screenshot: Verzweigung „False“ der Bedingung „If add member succeeded“ (Bei erfolgreichem Hinzufügen des Mitglieds) mit ausgewählter Option „Aktion hinzufügen“](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. Geben Sie im Suchfeld **Vorgang auswählen** den Text `outlook send email` ein, und wählen Sie die Aktion **E-Mail senden** aus.

   ![Screenshot: Suchfeld „Vorgang auswählen“ mit Eingabe „Outlook E-Mail senden“ und ausgewählter Aktion „E-Mail senden“](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Nennen Sie die Aktion wie folgt: `Send email on failure`

1. Geben Sie Informationen zu dieser Aktion wie hier dargestellt und beschrieben an:

   ![Screenshot: Aktion „Send email on failure“ (E-Mail senden bei Fehler) und Angaben für die Fehler-E-Mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Text** | Ja | <*Text für Fehler-E-Mail*> | Der Inhalt der Fehler-E-Mail. Geben Sie für dieses Tutorial den folgenden Text ein: <p>`Member might already exist. Check your MailChimp account.` |
   | **Subject** | Ja | <*Betreff für Fehler-E-Mail*> | Der Betreff der Fehler-E-Mail. Führen Sie für dieses Tutorial die folgenden Schritte aus: <p>1. Geben Sie den folgenden Text mit einem nachgestellten Leerzeichen ein: `Failed, member not added to "test-members-ML": ` <p>2. Wählen Sie in der Liste mit den dynamischen Inhalten unter **Mitglied zu Liste hinzufügen** die Eigenschaft **E-Mail-Adresse** aus. |
   | **An** | Ja | <*Ihre E-Mail-Adresse*> | Die E-Mail-Adresse, an die die Fehler-E-Mail gesendet werden soll. Zu Testzwecken können Sie hier Ihre eigene E-Mail-Adresse angeben. |
   |||||

1. Speichern Sie Ihre Logik-App. 

Testen Sie als Nächstes Ihre Logik-App, die nun in etwa wie folgt aussieht:

![Screenshot: Fertiger Workflow der exemplarischen Logik-App](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Ausführen Ihrer Logik-App

1. Senden Sie sich selbst eine E-Mail mit der Anforderung, in Ihre Adressenliste aufgenommen zu werden. Warten Sie, bis die Anforderung in Ihrem Posteingang eingeht.

1. Wählen Sie auf der Symbolleiste des Designers die Option **Ausführen** aus, um Ihre Logik-App manuell zu starten. 

   Wenn der Betreff Ihrer E-Mail dem Betrefffilter des Triggers entspricht, sendet Ihre Logik-App Ihnen eine E-Mail zur Genehmigung der Abonnementanforderung.

1. Wählen Sie in der erhaltenen Genehmigungs-E-Mail die Option **Genehmigen** aus.

1. Wenn die E-Mail-Adresse des Abonnenten in Ihrer Adressenliste nicht vorhanden ist, fügt Ihre Logik-App die entsprechende E-Mail-Adresse hinzu und sendet Ihnen eine E-Mail. Beispiel:

   ![Screenshot: Beispiel-E-Mail für ein erfolgreiches Abonnement](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Falls Ihre Logik-App den Abonnenten nicht hinzufügen kann, erhalten Sie eine E-Mail wie die folgende:

   ![Screenshot: Beispiel-E-Mail für ein nicht erfolgreiches Abonnement](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > Sollten Sie keine E-Mails erhalten, überprüfen Sie Ihren Ordner für Junk-E-Mails. E-Mails dieser Art werden unter Umständen durch Ihren Junk-E-Mail-Filter umgeleitet. Wenn Sie unsicher sind, ob Ihre Logik-App richtig ausgeführt wurde, helfen Ihnen die Informationen unter [Diagnostizieren von Fehlern bei Logik-Apps](../logic-apps/logic-apps-diagnosing-failures.md) weiter.

Geschafft! Sie haben eine Logik-App erstellt und ausgeführt, die Informationen über Azure, Microsoft-Dienste und andere SaaS-Apps hinweg integriert.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Ihre Logik-App wird weiter ausgeführt, bis Sie die App deaktivieren oder löschen. Wenn Sie diese exemplarische Logik-App nicht mehr benötigen, löschen Sie die Ressourcengruppe, die Ihre Logik-App und die zugehörigen Ressourcen enthält.

1. Geben Sie im Azure-Portal den Namen der von Ihnen erstellten Ressourcengruppe in das Suchfeld ein. Wählen Sie in den Ergebnissen unter **Ressourcengruppen** die Ressourcengruppe aus.

   In diesem Beispiel wurde die Ressourcengruppe `LA-MailingList-RG` erstellt.

   ![Screenshot: Azure-Suchfeld mit der Eingabe „la-mailinglist-rg“ und der ausgewählten Ressourcengruppe **LA-MailingList-RG**](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > Falls die Ressourcengruppe auf der Azure-Startseite unter **Kürzlich verwendete Ressourcen** angezeigt wird, können Sie sie auf der Startseite auswählen.

1. Vergewissern Sie sich im Ressourcengruppenmenü, dass **Übersicht** ausgewählt ist. Wählen Sie auf der Symbolleiste des Bereichs **Übersicht** die Option **Ressourcengruppe löschen** aus.

   ![Screenshot: Bereich „Übersicht“ der Ressourcengruppe und ausgewählte Option „Ressourcengruppe löschen“ auf der Symbolleiste des Bereichs](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Geben Sie im angezeigten Bestätigungsbereich den Ressourcengruppennamen ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Logik-App zur Behandlung von Genehmigungen für Adressenlistenanforderungen erstellt. Erfahren Sie als Nächstes, wie Sie eine Logik-App erstellen, die E-Mail-Anlagen verarbeitet und speichert und Azure-Dienste wie Azure Storage und Azure Functions integriert.

> [!div class="nextstepaction"]
> [Verarbeiten von E-Mail-Anlagen](../logic-apps/tutorial-process-email-attachments-workflow.md)
