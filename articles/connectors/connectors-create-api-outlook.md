---
title: Herstellen einer Verbindung mit Outlook.com – Azure Logic Apps | Microsoft-Dokumentation
description: Verwalten von E-Mails, Kalendern und Kontakten mit Outlook.com-REST-APIs und Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.topic: article
ms.date: 08/18/2016
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: fd6836451a73551487b8f97903594154a2efc894
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105801"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Verwalten von E-Mails, Kalendern und Kontakten in Outlook.com mit Azure Logic Apps

In diesem Artikel wird gezeigt, wie Sie Ihr Outlook.com-Konto innerhalb einer Logik-App mit dem Box-Connector erstellen und verwalten. Auf diese Weise können Sie Logik-Apps erstellen, mit denen Aufgaben und Workflows für das Verwalten für Ihr Outlook.com-Konto automatisiert werden, wie z.B.:

* Senden von E-Mails. 
* Planen von Besprechungen.
* Hinzufügen von Kontakten. 

Wenn Sie mit Logik-Apps noch nicht vertraut sind, lesen Sie [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein [Outlook.com-Konto](https://outlook.live.com/owa/)

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>. 

* Die Logik-App, in der Sie auf Ihr Outlook.com-Konto zugreifen möchten. Um Ihre Logik-App mit einem Outlook-Trigger starten zu können, benötigen Sie eine [leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Grundlegende Kenntnisse über das [Erstellen von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-outlookcom"></a>Verbinden mit Outlook.com

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Connector-Referenz

Technische Details, z.B. Trigger, Aktionen und Grenzwerte, wie sie in der Swagger-Datei des Connectors beschrieben werden, finden Sie auf der [Referenzseite des Connectors](/connectors/outlook/). 

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)