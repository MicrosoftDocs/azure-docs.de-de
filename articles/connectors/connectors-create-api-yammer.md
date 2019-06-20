---
title: Herstellen einer Verbindung mit Yammer über Azure Logic Apps | Microsoft-Dokumentation
description: Automatisieren von Aufgaben und Workflows, die Nachrichten, Feeds und mehr in Yammer mithilfe von Azure Logic Apps überwachen und verwalten
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: ca2d28f3438fd166fa282488206662c95777bf3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104730"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Überwachen und Verwalten Ihres Yammer-Kontos mithilfe von Azure Logic Apps

Mit Azure Logic Apps und dem Yammer-Connector können Sie automatisierte Aufgaben und Workflows erstellen, die Nachrichten, Feeds und vieles mehr in Ihrem Yammer-Konto überwachen und verwalten. Darüber hinaus können z.B. folgende Aktionen ausgeführt werden:

* Überwachen, wenn neue Nachrichten in Feeds und Gruppen angezeigt werden, denen Sie folgen.
* Erhalten von Nachrichten, Gruppen, Netzwerke, Benutzerdetails und mehr.
* Veröffentlichen und Nachrichten mit "Gefällt mir" markieren.

Sie können Trigger verwenden, die Antworten von Ihrem Yammer-Konto erhalten und die Ausgabe für andere Aktionen verfügbar machen. Sie können Aktionen verwenden, die Aufgaben mit Ihrem Yammer-Konto ausführen. Sie können die Ausgaben von Yammer-Aktionen auch in anderen Aktionen verwenden. Wenn beispielsweise neue Nachrichten in Feeds oder Gruppen erscheinen, können Sie diese Nachrichten mit dem Slack-Connector teilen. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>. 

* Ihr Yammer-Konto und Benutzeranmeldeinformationen

   Ihre Anmeldeinformationen autorisieren Ihre Logik-App zur Erstellung einer Verbindung mit Ihrem Yammer-Konto sowie zum Zugriff auf das Konto.

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf Ihr Yammer-Konto zugreifen möchten. Wenn Sie mit einem Yammer-Trigger beginnen möchten, [erstellen Sie eine leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Um eine Yammer-Aktion zu verwenden, starten Sie Ihre Logik-App mit einem anderen Trigger, z.B. dem **Wiederholungstrigger**.

## <a name="connect-to-yammer"></a>Herstellen einer Verbindung zu Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Auswählen eines Pfads: 

   * Geben Sie für leere Logik-Apps im Suchfeld die Zeichenfolge „yammer“ als Filter ein. 
   Wählen Sie in der Triggerliste den gewünschten Trigger aus. 

     Oder

   * Für vorhandene Logik-Apps: 
   
     * Wählen Sie im letzten Schritt zum Hinzufügen einer Aktion **Neuer Schritt** aus. 

       Oder

     * Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. 
     Wählen Sie das daraufhin angezeigte Pluszeichen ( **+** ) und dann **Aktion hinzufügen** aus.
     
       Geben Sie im Suchfeld den Begriff „yammer“ als Filter ein. 
       Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

1. Wenn Sie zur Anmeldung bei Yammer aufgefordert werden, melden Sie sich jetzt an, sodass Sie den Zugriff zulassen können.

1. Geben Sie die erforderlichen Details für Ihren ausgewählten Trigger oder Ihre ausgewählte Aktion an, und fahren Sie mit dem Erstellen Ihres Logik-App-Workflows fort.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu Triggern, Aktionen und Beschränkungen aus der OpenAPI-Beschreibung (ehemals Swagger) des Connectors finden Sie auf der [Referenzseite](/connectors/yammer/) des Connectors.

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)