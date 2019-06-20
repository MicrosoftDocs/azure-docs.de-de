---
title: Herstellen einer Verbindung mit SharePoint über Azure Logic Apps | Microsoft-Dokumentation
description: Automatisieren von Aufgaben und Workflows, die mithilfe von Azure Logic Apps Ressourcen in SharePoint Online oder SharePoint Server lokal überwachen und verwalten
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: e636b2bb08477e6c56c6ae41f08983fc5bfa2a9b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60450742"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Überwachen und Verwalten von SharePoint-Ressourcen mit Azure Logic Apps

Mit Azure Logic Apps und dem SharePoint-Connector können Sie automatisierte Aufgaben und Workflows erstellen, die Ressourcen wie Dateien, Ordner, Listen, Elemente, Personen usw. in SharePoint Online oder auf einer SharePoint Server-Instanz lokal überwachen und verwalten. Beispiele:

* Sie können die Vorgänge überwachen, wenn Dateien oder Elemente erstellt, geändert oder gelöscht werden.
* Sie können Elemente erstellen, abrufen, aktualisieren oder löschen.
* Sie können Anlagen hinzufügen, abrufen oder löschen. Sie können die Inhalte von Anlagen abrufen.
* Sie können Dateien erstellen, kopieren, aktualisieren oder löschen. 
* Sie können Dateieigenschaften aktualisieren. Sie können die Inhalte, Metadaten oder Eigenschaften einer Datei abrufen.
* Sie können Ordner auflisten oder extrahieren.
* Sie können Listen abrufen oder Ansichten auflisten.
* Sie können den Inhaltsgenehmigungsstatus festlegen.
* Sie können Personen auflösen.
* Sie können HTTP-Anforderungen an SharePoint senden.
* Sie können Entitätswerte abrufen.

Sie können Trigger verwenden, die Antworten von SharePoint erhalten und die Ausgabe für andere Aktionen verfügbar machen. Sie können Aktionen in Ihren Logik-Apps verwenden, um Aufgaben in SharePoint auszuführen. Sie können die Ausgaben von SharePoint-Aktionen auch in anderen Aktionen verwenden. Wenn Sie z.B. regelmäßig Dateien aus SharePoint abrufen, können Sie über den Slack-Connector Nachrichten an Ihr Team senden.
Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>. 

* Die Adresse Ihrer SharePoint-Website und Benutzeranmeldeinformationen.

  Ihre Anmeldeinformationen autorisieren Ihre Logik-App zur Erstellung einer Verbindung mit Ihrem SharePoint-Konto sowie zum Zugriff auf das Konto. 

* Bevor Sie eine Verbindung zwischen Logik-Apps und lokalen Systemen wie SharePoint Server herstellen können, müssen Sie [ein lokales Datengateway installieren und einrichten](../logic-apps/logic-apps-gateway-install.md). So können Sie beim Erstellen der SharePoint-Verbindung für Ihre Logik-App angeben, dass die Gatewayinstallation verwendet werden soll.

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf Ihr SharePoint-Konto zugreifen möchten. Um mit einem SharePoint-Trigger zu beginnen, [erstellen Sie eine leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Um eine SharePoint-Aktion zu verwenden, starten Sie Ihre Logik-App mit einem Trigger – z.B. einem Salesforce-Trigger, falls Sie ein Salesforce-Konto besitzen.

  Sie können Ihre Logik-App beispielsweise mit dem Salesforce-Trigger **Wenn ein Datensatz erstellt wird** starten. 
  Dieser Trigger wird jedes Mal ausgelöst, wenn in Salesforce ein neuer Datensatz erstellt wird, z.B. ein neuer Lead. 
  Auf diesen Trigger können Sie die SharePoint-Aktion **Datei erstellen** folgen lassen. Wenn ein neuer Datensatz erstellt wird, erstellt Ihre Logik-App in SharePoint eine Datei mit Informationen zu diesem neuen Datensatz.

## <a name="connect-to-sharepoint"></a>Herstellen einer Verbindung mit SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Geben Sie im Fall einer leeren Logik-App im Suchfeld die Zeichenfolge „sharepoint“ als Filter ein. Wählen Sie in der Triggerliste den gewünschten Trigger aus. 

   Oder

   Wählen Sie für vorhandene Logik-Apps im letzten Schritt zum Hinzufügen einer SharePoint-Aktion die Option **Neuer Schritt** aus. 
   Geben Sie im Suchfeld „sharepoint“ als Filter ein. 
   Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

   Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. 
   Wählen Sie das daraufhin angezeigte Pluszeichen ( **+** ) und dann **Aktion hinzufügen** aus.

1. Wenn Sie aufgefordert werden, sich anzumelden, geben Sie die erforderlichen Verbindungsinformationen ein. Wenn Sie SharePoint Server verwenden, stellen Sie sicher, dass Sie **Über lokales Datengateway verbinden** auswählen. Wenn Sie fertig sind, wählen Sie **Erstellen** aus.

1. Geben Sie die erforderlichen Details für Ihren ausgewählten Trigger oder Ihre ausgewählte Aktion an, und fahren Sie mit dem Erstellen Ihres Logik-App-Workflows fort.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu Triggern, Aktionen und Beschränkungen aus der OpenAPI-Beschreibung (ehemals Swagger) des Connectors finden Sie auf der [Referenzseite](/connectors/sharepoint/) des Connectors.

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
