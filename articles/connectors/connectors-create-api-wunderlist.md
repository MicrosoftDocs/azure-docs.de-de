---
title: Herstellen einer Verbindung mit Wunderlist über Azure Logic Apps | Microsoft-Dokumentation
description: Automatisieren von Aufgaben und Workflows, die Listen, Aufgaben, Erinnerungen und vieles mehr in Ihrem Wunderlist-Konto mithilfe von Azure Logic Apps überwachen und verwalten
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: e3570ab1227ca388ac62bffdc74bb68b1ddc41d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105665"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Überwachen und Verwalten von Wunderlist mithilfe von Azure Logic Apps

Mit Azure Logic Apps und dem Wunderlist-Connector können Sie automatisierte Aufgaben und Workflows erstellen, die Todo-Listen, Aufgaben, Erinnerungen und vieles mehr in Ihrem Wunderlist-Konto überwachen und verwalten. Darüber hinaus können z.B. folgende Aktionen ausgeführt werden:

* Sie können die Vorgänge überwachen, wenn neue Aufgaben erstellt werden, wenn Aufgaben fällig sind oder wenn Erinnerungen übermittelt werden.
* Sie können Listen, Notizen, Aufgaben, Unteraufgaben und vieles mehr erstellen und verwalten.
* Sie können Erinnerungen einrichten.
* Sie können Listen, Aufgaben, Unteraufgaben, Erinnerungen, Dateien, Notizen, Kommentare und vieles mehr abrufen.

[Wunderlist](https://www.wunderlist.com/) ist ein Dienst, der Sie dabei unterstützt, Ihre Projekte, Todo-Listen und Aufgaben zu planen, zu verwalten und abzuschließen – auf jedem beliebigen Gerät und überall. Sie können Trigger verwenden, die Antworten von Ihrem Wunderlist-Konto erhalten und die Ausgabe für andere Aktionen verfügbar machen. Sie können Aktionen verwenden, die Aufgaben mit Ihrem Wunderlist-Konto ausführen. Sie können die Ausgaben von Wunderlist-Aktionen auch in anderen Aktionen verwenden. Sie können z.B. Nachrichten mit dem Slack-Connector senden, wenn neue Aufgaben fällig werden. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>. 

* Ihr Wunderlist-Konto und Benutzeranmeldeinformationen.

   Ihre Anmeldeinformationen autorisieren Ihre Logik-App zur Erstellung einer Verbindung mit Ihrem Wunderlist-Konto sowie zum Zugriff auf das Konto.

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf Ihr Yammer-Konto zugreifen möchten. Um mit einem Wunderlist-Trigger zu beginnen, [erstellen Sie eine leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Um eine Wunderlist-Aktion zu verwenden, starten Sie Ihre Logik-App mit einem anderen Trigger, z.B. dem **Wiederholungstrigger**.

## <a name="connect-to-wunderlist"></a>Herstellen einer Verbindung mit Wunderlist

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Wählen Sie einen Pfad aus: 

   * Geben Sie im Fall einer leeren Logik-App im Suchfeld die Zeichenfolge „wunderlist“ als Filter ein. 
   Wählen Sie in der Triggerliste den gewünschten Trigger aus. 

     Oder

   * Für vorhandene Logik-Apps: 
   
     * Wählen Sie im letzten Schritt zum Hinzufügen einer Aktion **Neuer Schritt** aus. 

       Oder

     * Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. 
     Wählen Sie das daraufhin angezeigte Pluszeichen ( **+** ) und dann **Aktion hinzufügen** aus.
     
       Geben Sie im Suchfeld den Begriff „wunderlist“ als Filter ein. 
       Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

1. Wenn Sie zur Anmeldung bei Wunderlist aufgefordert werden, melden Sie sich jetzt an, sodass Sie den Zugriff zulassen können.

1. Geben Sie die erforderlichen Details für Ihren ausgewählten Trigger oder Ihre ausgewählte Aktion an, und fahren Sie mit dem Erstellen Ihres Logik-App-Workflows fort.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu Triggern, Aktionen und Beschränkungen aus der OpenAPI-Beschreibung (ehemals Swagger) des Connectors finden Sie auf der [Referenzseite](/connectors/wunderlist/) des Connectors.

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)