---
title: Herstellen einer Verbindung mit RSS-Feeds über Azure Logic Apps | Microsoft-Dokumentation
description: Automatisieren von Aufgaben und Workflows, die RSS-Feeds mithilfe von Azure Logic Apps überwachen und verwalten
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 01573871700bbeeb653ce3efdbf6c6aca88fd454
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104883"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Verwalten von RSS-Feeds mithilfe von Azure Logic Apps

Mit Azure Logic Apps und dem RSS-Connector können Sie automatisierte Aufgaben und Workflows für jeden beliebigen RSS-Feed erstellen, z.B.:

* Überwachen, wann RSS-Feedelemente veröffentlicht werden
* Auflisten aller RSS-Feedelemente

RSS (Rich Site Summary), auch als „Really Simple Syndication“ bezeichnet, ist ein beliebtes Format für die Websyndikation und wird für die Veröffentlichung von häufig aktualisierten Inhalten wie Blogbeiträge und Schlagzeilen verwendet. Viele Inhaltsherausgeber bieten einen RSS-Feed an, damit Benutzer den Inhalt abonnieren können. 

Sie können einen RSS-Trigger verwenden, der Antworten von einem RSS-Feed erhält und die Ausgabe für andere Aktionen verfügbar macht. Sie können eine RSS-Aktion in Ihren Logik-Apps zum Ausführen einer Aufgabe mit dem RSS-Feed verwenden. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>. 

* Die URL für einen RSS-Feed

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf einen RSS-Feed zugreifen möchten. Wenn Sie mit einem RSS-Trigger beginnen möchten, [erstellen Sie eine leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Um eine RSS-Aktion zu verwenden, starten Sie Ihre Logik-App mit einem anderen Trigger, z.B. dem **Wiederholungstrigger**.

## <a name="connect-to-an-rss-feed"></a>Herstellen einer Verbindung mit einem RSS-Feed

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Auswählen eines Pfads: 

   * Geben Sie für leere Logik-Apps im Suchfeld die Zeichenfolge „rss“ als Filter ein. Wählen Sie in der Triggerliste den gewünschten Trigger aus. 

     Oder

   * Für vorhandene Logik-App wählen Sie im Schritt zum Hinzufügen einer Aktion **Neuer Schritt** aus. Geben Sie im Suchfeld den Begriff „rss“ als Filter ein. Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

1. Geben Sie die erforderlichen Details für Ihren ausgewählten Trigger oder Ihre ausgewählte Aktion an, und fahren Sie mit dem Erstellen Ihres Logik-App-Workflows fort.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu Triggern, Aktionen und Beschränkungen aus der OpenAPI-Beschreibung (ehemals Swagger) des Connectors finden Sie auf der [Referenzseite](/connectors/rss/) des Connectors.

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)