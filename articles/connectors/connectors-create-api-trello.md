---
title: Herstellen einer Verbindung mit Trello über Azure Logic Apps | Microsoft-Dokumentation
description: Automatisieren von Aufgaben und Workflows, die Listen, Boards und Karten in Ihren Trello-Projekten mithilfe von Azure Logic Apps überwachen und verwalten
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: fe7a4377-5c24-4f72-ab1a-6d9d23e8d895
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 31357fa313cfa31c0f8a90c0f7722f627e4394d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106166"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Überwachen und Verwalten von Trello mit Azure Logic Apps

Mit Azure Logic Apps und dem Trello-Connector können Sie automatisierte Aufgaben und Workflows erstellen, die Ihre Trello-Listen, -Karten, -Boards, -Teammitglieder usw. überwachen und verwalten, z.B.:

* Überwachen, wann neue Karten zu Boards und Listen hinzugefügt werden 
* Erstellen, Abrufen und Verwalten von Boards, Karten und Listen
* Hinzufügen von Kommentare und Mitgliedern zu Karten
* Auflisten von Boards, Boardbezeichnungen, Karten auf Boards, Kartenkommentaren, Kartenmitgliedern, Teammitgliedern und Teams, in denen Sie Mitglied sind 
* Abrufen von Teams

Sie können Trigger verwenden, die Antworten von Ihrem Trello-Konto erhalten und die Ausgabe für andere Aktionen verfügbar machen. Sie können Aktionen verwenden, die Aufgaben mit Ihrem Trello-Konto ausführen. Sie können die Ausgaben von Trello-Aktionen auch in anderen Aktionen verwenden. Beispiel: Wenn einem Board oder einer Liste eine neue Karte hinzugefügt wird, können Sie Nachrichten mit dem Slack-Connector senden. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>. 

* Ihr Trello-Konto und Benutzeranmeldeinformationen

  Ihre Anmeldeinformationen autorisieren Ihre Logik-App zur Erstellung einer Verbindung mit Ihrem Trello-Konto sowie zum Zugriff auf das Konto.

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf Ihr Trello-Konto zugreifen möchten. Wenn Sie mit einem Trello-Trigger beginnen möchten, [erstellen Sie eine leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Um eine Trello-Aktion zu verwenden, starten Sie Ihre Logik-App mit einem Trigger, z.B. dem **Wiederholungstrigger**.

## <a name="connect-to-trello"></a>Herstellen einer Verbindung mit Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Geben Sie für leere Logik-Apps im Suchfeld die Zeichenfolge „trello“ als Filter ein. Wählen Sie in der Triggerliste den gewünschten Trigger aus. 

   Oder

   Wählen Sie für vorhandene Logik-Apps im letzten Schritt zum Hinzufügen einer Aktion die Option **Neuer Schritt** aus. 
   Geben Sie im Suchfeld den Begriff „trello“ als Filter ein. 
   Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

   Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. 
   Wählen Sie das daraufhin angezeigte Pluszeichen ( **+** ) und dann **Aktion hinzufügen** aus.

1. Wenn Sie zur Anmeldung bei Trello aufgefordert werden, autorisieren Sie den Zugriff für Ihre Logik-App, und melden Sie sich an.

1. Geben Sie die erforderlichen Details für Ihren ausgewählten Trigger oder Ihre ausgewählte Aktion an, und fahren Sie mit dem Erstellen Ihres Logik-App-Workflows fort.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu Triggern, Aktionen und Beschränkungen aus der OpenAPI-Beschreibung (ehemals Swagger) des Connectors finden Sie auf der [Referenzseite](/connectors/trello/) des Connectors.

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)