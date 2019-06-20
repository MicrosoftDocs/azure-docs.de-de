---
title: Herstellen einer Verbindung mit Box – Azure Logic Apps | Microsoft-Dokumentation
description: Erstellen und Verwalten von Dateien mit Box-REST-APIs und Azure Logic Apps
author: ecfan
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 971d38fa0fbd47f0deb815577033bbe684aac32f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60312575"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Erstellen und Verwalten von Dateien in Box mit Azure Logic Apps

In diesem Artikel wird gezeigt, wie Sie Ihre Dateien in Box innerhalb einer Logik-App mit dem Box-Connector erstellen und verwalten. Auf diese Weise können Sie Logik-Apps erstellen, mit denen Aufgaben und Workflows für das Verwalten Ihrer Dateien und anderer Aktionen automatisiert werden, wie z.B.:

* Erstellen eines Geschäftsworkflows basierend auf den Daten, die aus Box abgerufen werden.

* Auslösen automatisierter Aufgaben und Workflows beim Erstellen oder Aktualisieren einer Datei.

* Ausführen von Aktionen, um eine Datei zu kopieren, zu löschen usw.

  Wenn diese Aktionen eine Antwort erhalten, stellen sie anschließend die Ausgabe anderen Aktionen zur Verfügung. 
  Wenn z. B. in Box eine Datei geändert wird, können Sie diese Datei mithilfe von Office 365 per E-Mail senden.

## <a name="prerequisites"></a>Voraussetzungen

* Ein [Box-Konto](https://www.box.com/home).

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>. 

* Die Logik-App, in der Sie auf Ihr Box-Konto zugreifen möchten. Um Ihre Logik-App mit einem Box-Trigger starten zu können, benötigen Sie eine [leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Grundlegende Kenntnisse über das [Erstellen von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Wenn Sie mit Logik-Apps noch nicht vertraut sind, lesen Sie [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Connector-Referenz

Technische Details wie Trigger, Aktionen und Limits, wie sie in der OpenAPI-Datei (ehemals Swagger) des Connectors beschrieben werden, finden Sie auf der [Referenzseite des Connectors](/connectors/box/).

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)