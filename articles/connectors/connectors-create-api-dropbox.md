---
title: Herstellen einer Verbindung mit Dropbox – Azure Logic Apps
description: Hochladen und Verwalten von Dateien mit Dropbox-REST-APIs und Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 5a1bfe8ca38fc23f09b13195fb8ca5bd443a4afd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60312538"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Hochladen und Verwalten von Dateien in Dropbox mithilfe von Azure Logic Apps

Mit dem Dropbox-Connector und Azure Logic Apps können Sie automatisierte Workflows erstellen, die Dateien in Ihr Dropbox-Konto hochladen und sie verwalten. 

In diesem Artikel wird gezeigt, wie Sie aus Ihrer Logik-App eine Verbindung mit Dropbox herstellen und dann den Dropbox-Trigger **Wenn eine Datei erstellt wird** hinzufügen sowie die Dropbox-Aktion **Dateiinhalt anhand des Pfads abrufen**.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>.

* Ein [Dropbox-Konto](https://www.dropbox.com/), für das Sie sich kostenlos registrieren können. Ihre Kontoanmeldeinformationen sind zum Herstellen einer Verbindung zwischen Ihrer Logik-App und Ihrem Dropbox-Konto erforderlich.

* Grundlegende Kenntnisse über das [Erstellen von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Für dieses Beispiel benötigen Sie eine leere Logik-App.

## <a name="add-trigger"></a>Hinzufügen des Triggers

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Wählen Sie unter dem Suchfeld **Alle** aus. Geben Sie im Suchfeld den Begriff „dropbox“ als Filter ein.
Wählen Sie in der Triggerliste den folgenden Trigger aus: **Wenn eine Datei erstellt wird**

   ![Auswählen des Dropbox-Triggers](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Melden Sie sich mit den Anmeldeinformationen Ihres Dropbox-Kontos an, und autorisieren Sie den Zugriff auf Ihre Dropbox-Daten durch Azure Logic Apps.

1. Geben Sie die erforderlichen Informationen für Ihren Trigger an. 

   Wählen Sie in diesem Beispiel den Ordner aus, in dem Sie die Dateierstellung nachverfolgen möchten. Um Ihre Ordner zu durchsuchen,wählen Sie neben dem Feld **Ordner** das Ordnersymbol aus.

## <a name="add-action"></a>Hinzufügen einer Aktion

Fügen Sie jetzt eine Aktion hinzu, die den Inhalt aus jeder neuen Datei abruft.

1. Klicken Sie unter dem Trigger auf **Nächster Schritt**. 

1. Wählen Sie unter dem Suchfeld **Alle** aus. Geben Sie im Suchfeld den Begriff „dropbox“ als Filter ein.
Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Dateiinhalt anhand des Pfads abrufen**

1. Wenn Sie Azure Logic Apps noch nicht für den Zugriff auf Dropbox autorisiert haben, autorisieren Sie den Zugriff jetzt.

1. Um zum Dateipfad zu wechseln, den Sie verwenden möchten, wählen Sie neben dem Feld **Dateipfad** die Auslassungspunkte ( **...** ) aus. 

   Sie können auch in das Feld **Dateipfad** klicken und aus der dynamischen Inhaltsliste **Dateipfad**auswählen, dessen Wert als Ausgabe des Triggers verfügbar ist, den Sie im vorherigen Abschnitt hinzugefügt haben.

1. Wenn Sie fertig sind, speichern Sie Ihre Logik-App.

1. Um Ihre Logik-App auszulösen, erstellen Sie eine neue Datei in Dropbox.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details wie Trigger, Aktionen und Limits, wie sie in der OpenAPI-Datei (ehemals Swagger) des Connectors beschrieben werden, finden Sie auf der [Referenzseite des Connectors](/connectors/dropbox/).

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
