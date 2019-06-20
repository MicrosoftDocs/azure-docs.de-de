---
title: Herstellen einer Verbindung mit Salesforce über Azure Logic Apps | Microsoft-Dokumentation
description: Automatisieren von Aufgaben und Workflows, die Salesforce-Datensätze und -Aufträge mithilfe von Azure Logic Apps überwachen, erstellen und verwalten
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 292d517f2c99974f4674a4c94472a0a320320ce4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106014"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Überwachen, Erstellen und Verwalten von Salesforce-Ressourcen mithilfe von Azure Logic Apps

Mit Azure Logic Apps und dem Salesforce-Connector können Sie automatisierte Aufgaben und Workflows für Ihre Salesforce-Ressourcen wie Datensätze, Aufträge und Objekte erstellen, z.B.:

* Überwachen, wann Datensätze erstellt oder geändert werden 
* Erstellen, Abrufen und Verwalten von Aufträgen und Datensätzen, einschließlich Einfüge-, Aktualisierungs- und Löschaktionen

Sie können Salesforce-Trigger verwenden, die Antworten von Salesforce erhalten und die Ausgabe für andere Aktionen verfügbar machen. Sie können Aktionen in Ihren Logik-Apps zum Ausführen von Aufgaben mit Salesforce-Ressourcen verwenden. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>. 

* Ein [Salesforce](https://salesforce.com/)-Konto

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf Ihr Salesforce-Konto zugreifen möchten. Wenn Sie mit einem Salesforce-Trigger beginnen möchten, [erstellen Sie eine leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Um eine Salesforce-Aktion zu verwenden, starten Sie Ihre Logik-App mit einem anderen Trigger, z.B. dem **Wiederholungstrigger**.

## <a name="connect-to-salesforce"></a>Herstellen einer Verbindung mit Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Auswählen eines Pfads: 

   * Geben Sie für leere Logik-Apps im Suchfeld die Zeichenfolge „salesforce“ als Filter ein. 
   Wählen Sie in der Triggerliste den gewünschten Trigger aus. 

     Oder

   * Für vorhandene Logik-App wählen Sie im Schritt zum Hinzufügen einer Aktion **Neuer Schritt** aus. Geben Sie im Suchfeld „salesforce“ als Filter ein. Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

1. Wenn Sie zur Anmeldung bei Salesforce aufgefordert werden, melden Sie sich jetzt an und lassen den Zugriff zu.

   Ihre Anmeldeinformationen autorisieren Ihre Logik-App zur Erstellung einer Verbindung mit Salesforce sowie zum Zugriff auf Ihre Daten.

1. Geben Sie die erforderlichen Details für Ihren ausgewählten Trigger oder Ihre ausgewählte Aktion an, und fahren Sie mit dem Erstellen Ihres Logik-App-Workflows fort.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu Triggern, Aktionen und Beschränkungen aus der OpenAPI-Beschreibung (ehemals Swagger) des Connectors finden Sie auf der [Referenzseite](/connectors/salesforce/) des Connectors.

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)