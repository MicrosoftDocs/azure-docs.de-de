---
title: Herstellen einer Verbindung mit Slack über Azure Logic Apps | Microsoft-Dokumentation
description: Automatisieren von Aufgaben und Workflows, die Dateien überwachen und Kanäle, Gruppen und Nachrichten in Ihrem Slack-Konto mithilfe von Azure Logic Apps überwachen und verwalten
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 675e37120b06af3add58b564495f22875647a0fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105648"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Überwachen und Verwalten von Slack mit Azure Logic Apps

Mit Azure Logic Apps und dem Slack-Connector können Sie automatisierte Aufgaben und Workflows erstellen, die Ihre Slack-Dateien überwachen und Ihre Slack-Kanäle, -Nachrichten, -Gruppen usw. verwalten. Beispiele:

* Sie können Vorgänge überwachen, wenn neue Dateien erstellt werden.
* Sie können Kanäle erstellen, auflisten und verknüpfen. 
* Sie können Nachrichten senden.
* Sie können Gruppen erstellen und die Einstellung „Nicht stören“ festlegen.

Sie können Trigger verwenden, die Antworten von Ihrem Slack-Konto erhalten und die Ausgabe für andere Aktionen verfügbar machen. Sie können Aktionen verwenden, die Aufgaben mit Ihrem Slack-Konto ausführen. Sie können die Ausgaben von Slack-Aktionen auch in anderen Aktionen verwenden. Sie können z.B. mit dem Office 365 Outlook-Connector eine E-Mail senden, wenn eine neue Datei erstellt wird. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>. 

* Ihr [Slack](https://slack.com/)-Konto und Benutzeranmeldeinformationen.

  Ihre Anmeldeinformationen autorisieren Ihre Logik-App zur Erstellung einer Verbindung mit Ihrem Slack-Konto sowie zum Zugriff auf das Konto.

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf Ihr Slack-Konto zugreifen möchten. Um mit einem Slack-Trigger zu beginnen, [erstellen Sie eine leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Um eine Slack-Aktion zu verwenden, starten Sie Ihre Logik-App mit einem Slack-Trigger oder dem **Wiederholungstrigger**.

## <a name="connect-to-slack"></a>Herstellen einer Verbindung mit Slack

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Geben Sie im Fall einer leeren Logik-App im Suchfeld die Zeichenfolge „slack“ als Filter ein. Wählen Sie in der Triggerliste den gewünschten Trigger aus. 

   Oder

   Wählen Sie für vorhandene Logik-Apps im letzten Schritt zum Hinzufügen einer Aktion die Option **Neuer Schritt** aus. 
   Geben Sie im Suchfeld den Begriff „slack“ als Filter ein. 
   Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

   Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. 
   Wählen Sie das daraufhin angezeigte Pluszeichen ( **+** ) und dann **Aktion hinzufügen** aus.

1. Wenn Sie aufgefordert werden, sich bei Slack anzumelden, melden Sie sich bei Ihrem Slack-Arbeitsbereich an. 

   ![Anmelden beim Slack-Arbeitsbereich](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Autorisieren Sie den Zugriff für Ihre Logik-App.

   ![Autorisieren des Zugriffs auf Slack](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Geben Sie die erforderlichen Informationen zu Ihrem ausgewählten Trigger oder Ihrer ausgewählten Aktion ein. Fügen Sie weitere Aktionen hinzu, um mit dem Erstellen des Workflows für Ihre Logik-App fortzufahren.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu Triggern, Aktionen und Beschränkungen aus der OpenAPI-Beschreibung (ehemals Swagger) des Connectors finden Sie auf der [Referenzseite](/connectors/slack/) des Connectors.

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
