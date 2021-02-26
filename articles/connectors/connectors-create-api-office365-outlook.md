---
title: Integration in Office 365 Outlook
description: Automatisieren von Aufgaben und Workflows, die E-Mails, Kontakte und Kalender in Office 365 Outlook mithilfe von Azure Logic Apps verwalten
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/13/2020
tags: connectors
ms.openlocfilehash: 790879894c3b268fcd55aafc96507319b29fe1e5
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055075"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Verwalten von E-Mails, Kontakten und Kalendern in Office 365 Outlook mithilfe von Azure Logic Apps

Mit [Azure Logic Apps](../logic-apps/logic-apps-overview.md) und dem [Office 365 Outlook-Connector](/connectors/office365connector/) können Sie durch die Erstellung von Logik-Apps automatisierte Aufgaben und Workflows zur erstellen, die Ihr Geschäfts-, Schul- oder Unikonto verwalten. Sie können beispielsweise folgende Aufgaben automatisieren:

* Abrufen, Senden, und Beantworten von E-Mails.
* Planen von Besprechungen in Ihrem Kalender.
* Hinzufügen und Bearbeiten von Kontakten

Sie können einen beliebigen Trigger verwenden, um Ihren Workflow zu starten – etwa den Eingang einer neuen E-Mail, die Aktualisierung eines Kalenderelements oder das Eintreten eines Ereignisses in einem anderen Dienst, z. B. Salesforce. Auf das Triggerereignis kann dann mit Aktionen wie dem Senden einer E-Mail oder dem Erstellen eines neuen Kalenderereignisses reagiert werden.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Outlook-Konto, bei dem Sie sich mit einem [Geschäfts-, Schul- oder Unikonto](https://www.office.com/) anmelden. Wenn Sie ein @outlook.com- oder @hotmail.com-Konto besitzen, verwenden Sie stattdessen den [Outlook.com-Connector](../connectors/connectors-create-api-outlook.md). Informationen zum Herstellen einer Verbindung mit Outlook mit einem anderen Benutzerkonto, z. B. einem Dienstkonto, finden Sie unter [Herstellen einer Verbindung mit anderen Konten](#connect-using-other-accounts).

* Ein Azure-Konto und ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Die Logik-App, in der Sie auf Ihr Outlook-Konto zugreifen möchten. Um Ihren Workflow mit einem Office 365 Outlook-Trigger starten zu können, benötigen Sie eine [leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Wenn Sie Ihrem Workflow eine Office 365 Outlook-Aktion hinzufügen möchten, muss Ihre Logik-App bereits über einen-Trigger verfügen.

## <a name="add-a-trigger"></a>Hinzufügen eines Triggers

Ein [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) ist ein Ereignis, das den Workflow in Ihrer Logik-App startet. In dieser exemplarischen Logik-App wird ein Abfragetrigger verwendet, um Ihr E-Mail-Konto im angegebenen Intervall und mit der angegebenen Frequenz auf aktualisierte Kalenderereignisse zu überprüfen.

1. Öffnen Sie Ihre leere Logik-App im [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

1. Geben Sie im Suchfeld den Begriff `office 365 outlook` als Filter ein. In diesem Beispiel wird **Wenn ein anstehender Termin in Kürze startet** ausgewählt.
   
   ![Auswählen des Triggers zum Starten Ihrer Logik-App](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Wenn keine aktive Verbindung mit Ihrem Outlook-Konto vorhanden ist, werden Sie aufgefordert, sich anzumelden und diese Verbindung zu erstellen. Informationen zum Herstellen einer Verbindung mit Outlook mit einem anderen Benutzerkonto, z. B. einem Dienstkonto, finden Sie unter [Herstellen einer Verbindung mit anderen Konten](#connect-using-other-accounts). Geben Sie andernfalls die Informationen für die Eigenschaften des Triggers ein.

   > [!NOTE]
   > Ihre Verbindung läuft erst ab, wenn sie widerrufen wird. Sie läuft selbst dann nicht ab, wenn Sie die Anmeldeinformationen ändern. Weitere Informationen finden Sie unter [Konfigurierbare Tokengültigkeitsdauern in Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   In diesem Beispiel wird der Kalender ausgewählt, den der Trigger überprüft, beispielsweise:

   ![Konfigurieren der Eigenschaften des Triggers](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. Legen Sie im Trigger die Werte für **Häufigkeit** und **Intervall** fest. Um weitere verfügbare Trigger wie **Zeitzone** hinzuzufügen, wählen Sie diese Eigenschaften aus der Liste **Neuen Parameter hinzufügen** aus.

   Wenn also der Trigger beispielsweise den Kalender alle 15 Minuten überprüfen soll, legen Sie **Häufigkeit** auf **Minute** und **Intervall** auf `15` fest. 

   ![Festlegen von Häufigkeit und Intervall für den Trigger](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

Fügen Sie nun eine Aktion hinzu, die ausgeführt wird, nachdem der Trigger ausgelöst wurde. Beispielsweise können Sie die Twilio-Aktion **Nachricht senden** hinzufügen, die einen Text sendet, wenn ein Kalenderereignis in 15 Minuten beginnt.

## <a name="add-an-action"></a>Hinzufügen einer Aktion

Eine [Aktion](../logic-apps/logic-apps-overview.md#logic-app-concepts) ist ein Vorgang, der durch den Workflow in Ihrer Logik-App ausgeführt wird. Diese Beispiel-Logik-App erstellt einen neuen Kontakt in Office 365 Outlook. Für die Erstellung des Kontakts können Sie die Ausgabe eines anderen Triggers oder einer Aktion verwenden. Ein Beispiel: Angenommen, Ihre Logik-App verwendet den Dynamics 365-Trigger **Wenn ein Datensatz erstellt wird**. Sie können die Office 365 Outlook-Aktion **Kontakt erstellen** hinzufügen und die Ausgaben des SalesForce-Triggers verwenden, um den neuen Kontakt zu erstellen.

1. Öffnen Sie Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

1. Wenn Sie eine Aktion als letzten Schritt in Ihrem Workflow hinzufügen möchten, wählen Sie **Neuer Schritt** aus. 

   Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen diesen Schritten. Wählen Sie das angezeigte Pluszeichen ( **+** ) aus, und wählen Sie dann **Aktion hinzufügen** aus.

1. Geben Sie im Suchfeld den Begriff `office 365 outlook` als Filter ein. In diesem Beispiel wird **Kontakt erstellen** ausgewählt.

   ![Auswählen der Aktion, die in ihrer Logik-App ausgeführt werden soll](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Wenn keine aktive Verbindung mit Ihrem Outlook-Konto vorhanden ist, werden Sie aufgefordert, sich anzumelden und diese Verbindung zu erstellen. Informationen zum Herstellen einer Verbindung mit Outlook mit einem anderen Benutzerkonto, z. B. einem Dienstkonto, finden Sie unter [Herstellen einer Verbindung mit anderen Konten](#connect-using-other-accounts). Geben Sie andernfalls die Informationen für die Eigenschaften der Aktion ein.

   > [!NOTE]
   > Ihre Verbindung läuft erst ab, wenn sie widerrufen wird. Sie läuft selbst dann nicht ab, wenn Sie die Anmeldeinformationen ändern. Weitere Informationen finden Sie unter [Konfigurierbare Tokengültigkeitsdauern in Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   In diesem Beispiel wird der Ordner „Kontakte“ ausgewählt, in dem die Aktion den neuen Kontakt erstellt, z. B.:

   ![Konfigurieren der Eigenschaften der Aktion](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Um weitere verfügbare Aktionseigenschaften hinzuzufügen, wählen Sie diese Eigenschaften aus der Liste **Neuen Parameter hinzufügen** aus.

1. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

<a name="connect-using-other-accounts"></a>

## <a name="connect-using-other-accounts"></a>Herstellen einer Verbindung mit anderen Konten

Wenn Sie versuchen, eine Verbindung mit Outlook herzustellen, indem Sie ein anderes Konto als das derzeit bei Azure angemeldete verwenden, erhalten Sie möglicherweise Fehlermeldungen bezüglich des [einmaligen Anmeldens](../active-directory/manage-apps/what-is-single-sign-on.md). Dieses Problem tritt auf, wenn Sie sich beim Azure-Portal mit einem Konto anmelden, aber ein anderes Konto verwenden, um die Verbindung herzustellen. Der Designer erwartet, dass Sie das beim Azure-Portal angemeldete Konto verwenden. Sie haben folgende Möglichkeiten, um dieses Problem zu beheben:

* Richten Sie das andere Konto mit der Rolle **Mitwirkender** in der Ressourcengruppe Ihrer Logik-App ein.

  1. Wählen Sie im Ressourcengruppenmenü Ihrer Logik-App **Zugriffssteuerung (IAM)** aus. Richten Sie das andere Konto mit der Rolle **Mitwirkender** ein. 
  
     Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Rollenzuweisungen mithilfe des Azure-Portals](../role-based-access-control/role-assignments-portal.md).

  1. Nachdem Sie diese Rolle eingerichtet haben, melden Sie sich beim Azure-Portal mit dem Konto an, das nun über Berechtigungen für Mitwirkende verfügt. Sie können jetzt mit diesem Konto die Verbindung mit Outlook herstellen.

* Richten Sie das andere Konto so ein, dass Ihr Geschäfts-, Schul- oder Unikonto über „Senden als“-Berechtigungen verfügt.

   Wenn Sie über Administratorberechtigungen verfügen, richten Sie im Postfach des Dienstkontos Ihr Geschäfts-, Schul- oder Unikonto entweder mit **Senden als**- oder mit **Senden im Auftrag von**-Berechtigungen ein. Weitere Informationen finden Sie unter [Erteilen von Postfachberechtigungen für einen anderen Benutzer – Administratorhilfe](/microsoft-365/admin/add-users/give-mailbox-permissions-to-another-user). Anschließend können Sie die Verbindung mit Ihrem Geschäfts-, Schul- oder Unikonto herstellen. In Triggern oder Aktionen, bei denen Sie den Absender angeben können, können Sie jetzt die E-Mail-Adresse des Dienstkontos verwenden.

   Beispielsweise verfügt die Aktion **E-Mail senden** über einen optionalen Parameter **Von (Senden als)** , den Sie der Aktion hinzufügen und die E-Mail-Adresse Ihres Dienstkontos als Absender verwenden können. Um diesen Parameter hinzuzufügen, führen Sie die folgenden Schritte aus:

   1. Öffnen Sie in der Aktion **E-Mail senden** die Liste **Einen Parameter hinzufügen**, und wählen Sie den Parameter **Von (Senden als)** aus.

   1. Sobald der Parameter in der Aktion angezeigt wird, geben Sie die E-Mail-Adresse des Dienstkontos ein.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu diesem Connector, z. B. Trigger, Aktionen und Grenzwerte, wie sie in der Swagger-Datei des Connectors beschrieben werden, finden Sie auf der [Referenzseite des Connectors](/connectors/office365/). 

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
