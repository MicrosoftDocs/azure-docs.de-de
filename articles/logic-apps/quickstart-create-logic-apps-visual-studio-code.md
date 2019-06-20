---
title: Erstellen und Verwalten von automatisierten Workflows mit Visual Studio Code – Azure Logic Apps | Microsoft-Dokumentation
description: Schnellstart zum Erstellen und Verwalten von Logik-Apps mit JSON in Visual Studio Code (VS Code)
services: logic-apps
ms.service: logic-apps
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.topic: article
ms.reviewer: klam, deli, LADocs
ms.suite: integration
ms.date: 10/05/2018
ms.openlocfilehash: 0fec590523fa130af2e5670a92914c056df289d1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60512245"
---
# <a name="quickstart-create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>Schnellstart: Erstellen und Verwalten automatisierter Logik-App-Workflows – Visual Studio Code

Mit [Azure Logic Apps](../logic-apps/logic-apps-overview.md) und Visual Studio Code lassen sich Logik-Apps erstellen und verwalten, mit denen Sie Aufgaben, Workflows und Prozessen zum Integrieren von Apps, Daten, Systemen und Diensten in Unternehmen und Organisationen automatisieren können. Dieser Schnellstart zeigt, wie Sie Workflowdefinitionen für Logik-Apps erstellen und bearbeiten, indem Sie in einer codebasierten Umgebung in JavaScript Object Notation (JSON) mit dem Workflow-Definitionsschema arbeiten. Sie können auch vorhandene Logik-Apps bearbeiten, die bereits in <a href="https://docs.microsoft.com/azure/guides/developer/azure-developer-guide" target="_blank">Azure</a> in der Cloud bereitgestellt wurden. 

Obwohl Sie dieselben Aufgaben im <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> und in Visual Studio ausführen können, können Sie in Visual Studio Code schneller starten, wenn Sie bereits mit Logik-App-Definitionen vertraut sind und direkt im Code arbeiten möchten. Beispielsweise können Sie bereits erstellte Logik-Apps deaktivieren, aktivieren, löschen und aktualisieren. Sie können zudem auf jeder Entwicklungsplattform, auf der Visual Studio Code ausgeführt wird – z.B. Linux, Windows und Mach, an Logik-Apps und Integrationskonten arbeiten.

Im Rahmen dieses Artikels können Sie die gleiche Logik-App erstellen wie im [Schnellstart zum Erstellen einer Logik-App im Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), die sich mehr auf die grundlegenden Konzepte konzentriert. In Visual Studio Code sieht die Logik-App wie in diesem Beispiel aus:

![Fertiggestellte Logik-App](./media/create-logic-apps-visual-studio-code/overview.png)

Bevor Sie anfangen, müssen Sie die folgenden Voraussetzungen erfüllen:

* Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>.

* Sie benötigen Grundkenntnisse über die [Workflowdefinitionen von Logik-Apps](../logic-apps/logic-apps-workflow-definition-language.md) und deren Struktur, in der JavaScript Object Notation (JSON) verwendet wird. 

  Wenn Sie noch nicht mit Logik-Apps vertraut sind, probieren Sie den Schnellstart, der Sie durch das [Erstellen Ihrer ersten Logik-App im Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) führt und sich eher mit den grundlegenden Konzepten befasst. 

* Sie benötigen Zugriff auf das Web zum Anmelden bei Azure und in Ihrem Azure-Abonnement.

* Laden Sie diese Tools herunter, und installieren Sie sie, falls sie noch nicht vorhanden sind: 

  * <a href="https://code.visualstudio.com/" target="_blank">Visual Studio Code, Version 1.25.1 oder höher</a> (kostenlos)

  * Visual Studio Code-Erweiterung für Azure Logic Apps

    Sie können diese Erweiterung im [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) oder direkt in Visual Studio Code herunterladen und installieren. 
    Laden Sie Visual Studio Code nach der Installation unbedingt neu. 

    ![Suche nach „Visual Studio Code-Erweiterung für Azure Logic Apps“](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Die Erweiterung wurde ordnungsgemäß installiert, wenn das Azure-Symbol in der Visual Studio Code-Symbolleiste angezeigt wird. 

    ![Erweiterung wurde installiert](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    Weitere Informationen finden Sie im <a href="https://code.visualstudio.com/docs/editor/extension-gallery" target="_blank">Marketplace für Erweiterungen</a>. Unter [Azure Logic Apps-Erweiterung für Visual Studio Code auf GitHub](https://github.com/Microsoft/vscode-azurelogicapps) können Sie Beiträge zur Open Source-Version dieser Erweiterung anzeigen oder selbst einreichen. 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

1. Öffnen Sie Visual Studio Code. Wählen Sie in der Visual Studio Code-Symbolleiste das Azure-Symbol aus. 

   ![Auswahl des Azure-Symbols](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. Wählen Sie im Azure-Fenster unter **Logik-Apps** die Option **Bei Azure anmelden** aus. 

   ![Auswahl von „Bei Azure anmelden“](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   Sie werden nun aufgefordert, sich mit dem bereitgestellten Authentifizierungscode anzumelden. 

1. Kopieren Sie den Authentifizierungscode, und wählen Sie dann **Kopieren und öffnen** aus. Daraufhin wird ein neues Browserfenster geöffnet.

   ![Aufforderung zum Anmelden](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. Geben Sie Ihren Authentifizierungscode ein. Wählen Sie danach **Weiter** aus.

   ![Eingabe des Codes](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. Wählen Sie Ihr Azure-Konto aus. Nach dem Anmelden können Sie den Browser schließen und zu Visual Studio Code zurückkehren.

   Im Azure-Fenster zeigen die Bereiche „Logik-Apps“ und „Integrationskonten“ nun die Azure-Abonnements in Ihrem Konto an. 

   ![Wählen Sie das Abonnement aus.](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Wenn die erwarteten Abonnements nicht angezeigt werden, wählen Sie neben der Bezeichnung **Logik-Apps** **Abonnements auswählen** (Filtersymbol) aus. Suchen Sie nach den gewünschten Abonnements, und wählen Sie sie aus.

1. Um alle vorhandenen Logik-Apps oder Integrationskonten in Ihrem Azure-Abonnement anzuzeigen, erweitern Sie Ihr Abonnement.

   ![Anzeigen von Logik-Apps und Integrationskonten](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Erstellen einer Logik-App

1. Wenn Sie sich nicht über Visual Studio Code in Ihrem Azure-Abonnement angemeldet haben, befolgen Sie die Anleitung in diesem Artikel, um sich [jetzt anzumelden](#sign-in-azure).

1. Wählen Sie im Kontextmenü Ihres Abonnements **Erstellen** aus.

   ![Auswählen von „Erstellen“](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. Wählen Sie aus der Liste mit den Azure-Ressourcengruppen in Ihrem Abonnement eine vorhandene Ressourcengruppe aus, oder **erstellen Sie eine neue Ressourcengruppe**. 

   In diesem Beispiel wird eine neue Ressourcengruppe erstellt:

   ![Erstellen einer neuen Ressourcengruppe](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Geben Sie einen Namen für Ihre Azure-Ressourcengruppe ein, und drücken Sie dann die EINGABETASTE.

   ![Benennen der Ressourcengruppe](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Wählen Sie den Standort des Rechenzentrums zum Speichern der Metadaten Ihrer Logik-App aus.

   ![Auswählen des Standorts](./media/create-logic-apps-visual-studio-code/select-location.png)

1. Geben Sie einen Namen für Ihre Logik-App ein, und drücken Sie dann die EINGABETASTE.

   ![Benennen der Logik-App](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Ihre neue Logik-App wird nun im Azure-Fenster unter Ihrem Azure-Abonnement angezeigt. Nun können Sie die Workflowdefinition Ihrer Logik-App erstellen.

1. Wählen Sie im Kontextmenü der Logik-App **In Editor öffnen** aus. 

   ![Öffnen der Logik-App im Editor](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   Visual Studio Code öffnet eine Vorlage für die Logik-App-Workflowdefinition („.logicapp.json“-Datei), damit Sie mit dem Erstellen des Logik-App-Workflows beginnen können.

   ![Neue Logik-App-Workflowdefinition](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. Beginnen Sie in der Vorlagendatei, die Workflowdefinition Ihrer Logik-App zu erstellen. Weitere Informationen finden Sie in der [Schemareferenz zur Definitionssprache für Workflows in Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   Hier sehen Sie eine beispielhafte Logik-App-Definition. In der Regel werden JSON-Elemente in jedem Abschnitt in alphabetischer Reihenfolge angezeigt. Doch in diesem Beispiel werden diese Elemente in der Reihenfolge angezeigt, in der die Logik-App-Schritte im Designer erscheinen.

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "Title: @{triggerBody()?['title']}\n\nDate published: @{triggerBody()?['publishDate']}\n\nLink: @{triggerBody()?['primaryLink']}",
                  "Subject": "New RSS item: @{triggerBody()?['title']}",
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['outlook']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            }
         }
      },
      "outputs": {}
   }   
   ```

1. Wenn Sie fertig sind, speichern Sie die Definitionsdatei Ihrer Logik-App. Wenn Visual Studio Code Sie auffordert, das Hochladen der Definition Ihrer Logik-App in Ihr Azure-Abonnement zu bestätigen, wählen Sie **Hochladen** aus.

   ![Hochladen der neuen Logik-App](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   Nachdem Visual Studio Code Ihre Logik-App in Azure veröffentlicht hat, ist Ihre App live und wird im Azure-Portal ausgeführt. 

   ![Logik-App wurde im Azure-Portal veröffentlicht](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Bearbeiten einer Logik-App

Um eine vorhandene Logik-App zu bearbeiten, die bereits in Azure bereitgestellt wird, öffnen Sie die Workflowdefinitionsdatei der entsprechenden App in Visual Studio Code.

1. Wenn Sie sich nicht über Visual Studio Code in Ihrem Azure-Abonnement angemeldet haben, befolgen Sie die Anleitung in diesem Artikel, um sich [jetzt anzumelden](#sign-in-azure).

1. Erweitern Sie im Azure-Fenster unter **Logik-Apps** Ihr Azure-Abonnement, und wählen Sie die gewünschte Logik-App aus. 

1. Wählen Sie im Menü der Logik-App **In Editor öffnen** aus. Alternativ können Sie neben dem Namen Ihrer Logik-App das Bearbeitungssymbol auswählen.

   ![Öffnen des Editors für eine vorhandene Logik-App](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code öffnet die Datei „.logicapp.json“ für die Workflowdefinition Ihrer Logik-App.

   ![Geöffnete Logik-App-Workflowdefinition](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Nehmen Sie die gewünschten Änderungen an der Definition Ihrer Logik-App vor.

1. Speichern Sie Ihre Änderungen, wenn Sie fertig sind.

1. Wenn Visual Studio Code Sie auffordert, die Definition Ihrer Logik-App in Ihrem Azure-Abonnement zu aktualisieren, wählen Sie **Hochladen** aus. 

   ![Hochladen Ihrer Änderungen](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps-Forum</a>.
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die <a href="https://aka.ms/logicapps-wish" target="_blank">Website für Logic Apps-Benutzerfeedback</a>.

