---
title: Herstellen einer Verbindung mit Twilio aus Azure Logic Apps | Microsoft-Dokumentation
description: Automatisieren Sie Aufgaben und Workflows zur Verwaltung globaler SMS-, MMS- und IP-Nachrichten über Ihr Twilio-Konto mithilfe von Azure Logic Apps.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: fab52236c701f10c8e8e23ac398362ca4583ea06
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104900"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Verwalten von Nachrichten in Twilio mit Azure Logic Apps

Mit Azure Logic Apps und dem Twilio-Connector können Sie automatisierte Aufgaben und Workflows erstellen, die Nachrichten in Twilio abrufen, senden und auflisten. Dazu gehören globale SMS-, MMS- und IP-Nachrichten. Sie können diese Aktionen verwenden, um Aufgaben mit Ihrem Twilio-Konto auszuführen. Sie können die Ausgaben von Twilio-Aktionen auch in anderen Aktionen verwenden. Wenn z.B. eine neue Nachricht eingeht, können Sie den Nachrichteninhalt mit dem Slack-Connector senden. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>. 

* In [Twilio](https://www.twilio.com/): 

  * Ihre Twilio-Konto-ID und das [Authentifizierungstoken](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), die Sie in Ihrem Twilio-Dashboard finden

    Ihre Anmeldeinformationen autorisieren Ihre Logik-App zur Herstellung einer Verbindung mit Ihrem Twilio-Konto sowie zum Zugriff auf das Konto aus Ihrer Logik-App. 
    Wenn Sie ein Twilio-Testkonto verwenden, können Sie SMS-Nachrichten nur an *verifizierte* Telefonnummern senden.

  * Eine verifizierte Twilio-Telefonnummer, die SMS senden kann

  * Eine verifizierte Twilio-Telefonnummer, die SMS empfangen kann

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf Ihr Twilio-Konto zugreifen möchten. Um eine Twilio-Aktion zu verwenden, starten Sie Ihre Logik-App mit einem anderen Trigger, z.B. dem **Wiederholungstrigger**.

## <a name="connect-to-twilio"></a>Verbinden mit Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Auswählen eines Pfads: 

     * Wählen Sie im letzten Schritt zum Hinzufügen einer Aktion **Neuer Schritt** aus. 

       Oder

     * Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. 
     Wählen Sie das daraufhin angezeigte Pluszeichen ( **+** ) und dann **Aktion hinzufügen** aus.
     
       Geben Sie im Suchfeld den Begriff „Twilio“ als Filter ein. 
       Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

1. Geben Sie die erforderlichen Informationen zu Ihrer Verbindung ein, und wählen Sie dann **Erstellen** aus:

   * Der Name, der für Ihre Verbindung verwendet werden soll
   * Ihre Twilio-Konto-ID 
   * Ihr Twilio-Zugriffstoken (Authentifizierung)

1. Geben Sie die erforderlichen Informationen für die ausgewählte Aktion ein, und fahren Sie mit dem Erstellen Ihres Logik-App-Workflows fort.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu Triggern, Aktionen und Beschränkungen aus der OpenAPI-Beschreibung (ehemals Swagger) des Connectors finden Sie auf der [Referenzseite](/connectors/twilio/) des Connectors.

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)