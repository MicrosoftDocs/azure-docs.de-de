---
title: Verbinden mit Project Online über Azure Logic Apps | Microsoft-Dokumentation
description: Automatisieren von Workflows, die Project Online-Projekte, Aufgaben und Ressourcen mithilfe von Azure Logic Apps überwachen, erstellen und verwalten
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 663363d05c1875d22a0ecc0478abcf7e0ec89c99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105629"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Verwalten von Project Online-Projekten, -Aufgaben und -Ressourcen mithilfe von Azure Logic Apps

Mit Azure Logic Apps und dem Project Online-Connector können Sie über Office 365 automatisierte Aufgaben und Workflows für Ihre Projekte, Aufgaben und Ressourcen in Project Online erstellen. Ihre Workflows können diese und weitere Aktionen ausführen, z.B.:

* Überwachen, wann neue Projekte, Aufgaben und Ressourcen erstellt werden Oder Überwachen, wann neue Projekte veröffentlicht werden
* Erstellen von neuen Projekten, Aufgaben und Ressourcen
* Auflisten vorhandener Projekte oder Aufgaben
* Auschecken, Einchecken oder Veröffentlichen von Projekten

Project Online unterstützt Sie durch die Bereitstellung von leistungsstarken Projektmanagementfunktionen beim Planen, Priorisieren und Verwalten von Projekten und Investitionen in Projektportfolios an fast jedem Ort auf fast jedem Gerät. Sie können Project Online-Trigger verwenden, die Antworten von Project Online erhalten und die Ausgabe für andere Aktionen verfügbar machen. Sie können Aktionen in Ihren Logik-Apps zum Ausführen verschiedener Aufgaben in Project Online verwenden. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>. 

* Project Online, verfügbar über ein [Office 365-Konto](https://www.office.com/) 

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf Ihre Project Online-Daten zugreifen möchten. Wenn Sie mit einem Project Online-Trigger beginnen möchten, [erstellen Sie eine leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Um Project Online-Aktionen zu verwenden, starten Sie Ihre Logik-App mit einem anderen Trigger, z.B. dem **Wiederholungstrigger**.

## <a name="connect-to-project-online"></a>Herstellen einer Verbindung mit Project Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Auswählen eines Pfads: 

   * Geben Sie für leere Logik-Apps im Suchfeld „Project Online“ als Filter ein. 
   Wählen Sie in der Triggerliste den gewünschten Trigger aus. 

     Oder

   * Für vorhandene Logik-App wählen Sie im Schritt zum Hinzufügen einer Aktion **Neuer Schritt** aus. Geben Sie im Suchfeld „Project Online“ als Filter ein. Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

1. Wenn Sie zur Anmeldung bei Project Online aufgefordert werden, melden Sie sich jetzt an.

   Ihre Anmeldeinformationen autorisieren Ihre Logik-App zur Erstellung einer Verbindung mit Project Online sowie zum Zugriff auf Ihre Daten.

1. Geben Sie die erforderlichen Details für Ihren ausgewählten Trigger oder Ihre ausgewählte Aktion an, und fahren Sie mit dem Erstellen Ihres Logik-App-Workflows fort.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu Triggern, Aktionen und Beschränkungen aus der OpenAPI-Beschreibung (ehemals Swagger) des Connectors finden Sie auf der [Referenzseite](/connectors/projectonline/) des Connectors.

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)