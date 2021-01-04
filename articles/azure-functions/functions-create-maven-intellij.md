---
title: Erstellen einer Azure-Funktion mit Java und IntelliJ
description: Erfahren Sie, wie Sie mit Java und IntelliJ eine einfache, durch HTTP ausgelöste serverlose App auf Azure erstellen und veröffentlichen können.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 149d0ae99975628239f8b08f3987947a99e01cbb
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92893232"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Erstellen Ihrer ersten Azure-Funktion mit Java und IntelliJ

In diesem Artikel lernen Sie Folgendes:
- Erstellen eines [serverlosen](https://azure.microsoft.com/overview/serverless-computing/) Funktionsprojekts mit IntelliJ IDEA
- Schritte zum Testen und Debuggen der Funktion in der integrierten Entwicklungsumgebung (IDE) auf Ihrem eigenen Computer
- Anweisungen zum Bereitstellen des Funktionsprojekts in Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung

Installieren Sie die folgende Software, um eine Funktion mit Java und IntelliJ zu entwickeln:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ Ein von [Azure unterstütztes Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-long-term-support) für Java 8
+ Eine installierte Instanz von [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) (Ultimate Edition oder Community Edition)
+ [Maven 3.5.0 oder höher](https://maven.apache.org/download.cgi)
+ Aktuelle Version von [Function Core Tools](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>Installation und Anmeldung

1. Wählen Sie im Dialogfeld mit den Einstellungen/Voreinstellungen von IntelliJ IDEA (STRG+ALT+S) die Option **Plugins** (Plug-Ins) aus. Suchen Sie anschließend unter **Marketplace** nach **Azure Toolkit for IntelliJ** (Azure-Toolkit für IntelliJ), und klicken Sie auf **Install** (Installieren). Klicken Sie nach Abschluss der Installation auf **Restart** (Neu starten), um das Plug-In zu aktivieren. 

    ![Plug-In „Azure-Toolkit für IntelliJ“ im Marketplace][marketplace]

2. Öffnen Sie zur Anmeldung beim Azure-Konto Randleiste **Azure Explorer** , und klicken Sie dann auf die **Azure Sign In** in der Leiste oben auf (oder im Menü der IDEE **Tools/Azure/Azure-Anmeldung** ).
    ![IntelliJ-Befehl für Azure-Anmeldung][intellij-azure-login]

3. Wählen Sie im Fenster für **die Azure-Anmeldung** die Option **Device Login** (Geräteanmeldung) aus, und klicken Sie anschließend auf **Sign in** (Anmelden). Weitere Anmeldeoptionen finden Sie [hier](/azure/developer/java/toolkit-for-intellij/sign-in-instructions).

   ![Fenster für die Azure Anmeldung mit ausgewählter Geräteanmeldung][intellij-azure-popup]

4. Klicken Sie im Dialogfeld **Azure Device Login** (Azure-Geräteanmeldung) auf **Copy&Open** (Kopieren und öffnen).

   ![Dialogfeld für Azure-Anmeldung][intellij-azure-copycode]

5. Fügen Sie im Browser Ihren Gerätecode ein, den Sie im vorherigen Schritt durch Klicken auf **Copy&Open** (Kopieren und öffnen) kopiert haben, und klicken Sie anschließend auf **Next** (Weiter).

   ![Der Browser für die Geräteanmeldung][intellij-azure-link-ms-account]

6. Wenn das Dialogfeld **Select Subscriptions** (Abonnements auswählen) angezeigt wird, wählen Sie die Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **OK**.

   ![Dialogfeld zum Auswählen von Abonnements][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>Erstellen Ihres lokalen Projekts

In diesem Abschnitt wird mithilfe des Azure-Toolkits für IntelliJ ein lokales Azure Functions-Projekt erstellt. Weiter unten in diesem Artikel wird der Funktionscode in Azure veröffentlicht. 

1. Öffnen Sie das Willkommensdialogfeld von IntelliJ, wählen Sie *Neues Projekt erstellen* aus, um den Assistenten zum Erstellen eines neuen Projekts zu öffnen, und wählen Sie dann *Azure Functions* aus.

    ![Erstellen eines Functions-Projekts](media/functions-create-first-java-intellij/create-functions-project.png)

1. Wählen Sie *HTTP-Trigger* aus, klicken Sie auf *Weiter* , und gehen Sie im Assistenten alle Konfigurationen auf den nachfolgenden Seiten durch. Bestätigen Sie den Projektspeicherort, und klicken Sie dann auf *Fertig stellen*. Ihr neues Projekt wird anschließend in Intellj IDEA geöffnet.

    ![Erstellen eines Functions-Projekts: Fertigstellen](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-function-app-locally"></a>Lokales Ausführen der Funktions-App

1. Navigieren Sie zu `src/main/java/org/example/functions/HttpTriggerFunction.java`, um den generierten Code anzuzeigen. Neben Zeile *17* befindet sich die grüne Schaltfläche *Ausführen*. Klicken Sie darauf, und wählen Sie *Run 'azure-function-example'* („azure-function-example“ ausführen) aus. Daraufhin wird Ihre Funktions-App lokal mit einigen Protokollen ausgeführt.

    ![Lokales Ausführen der Funktions-App](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![Lokales Ausführen der Funktions-App: Ausgabe](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. Sie können die Funktion ausprobieren, indem Sie über den Browser (etwa über `http://localhost:7071/api/HttpTrigger-Java?name=Azure`) auf den ausgegebenen Endpunkt zugreifen.

    ![Lokales Ausführen der Funktions-App: Testergebnis](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. Das Protokoll wird ebenfalls in IDEA ausgegeben. Beenden Sie nun die Funktion, indem Sie auf die Schaltfläche zum *Beenden* klicken.

    ![Lokales Ausführen der Funktions-App: Testprotokoll](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-function-app-locally"></a>Lokales Debuggen der Funktions-App

1. Debuggen Sie Ihre Funktions-App nun lokal. Klicken Sie auf der Symbolleiste auf die Schaltfläche *Debuggen*. (Wird die Symbolleiste nicht angezeigt, klicken Sie zum Aktivieren der Symbolleiste auf *Ansicht > Darstellung > Symbolleiste*.)

    ![Schaltfläche zum lokalen Debuggen der Funktions-App](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. Klicken Sie in der Datei `src/main/java/org/example/functions/HttpTriggerFunction.java` auf Zeile *20* , um einen Breakpoint hinzuzufügen, und greifen Sie erneut auf den Endpunkt `http://localhost:7071/api/HttpTrigger-Java?name=Azure` zu. Der Breakpoint wird erreicht, und Sie können weitere Debugfunktionen wie *step* , *watch* oder *evaluation* ausprobieren. Beenden Sie die Debugsitzung, indem Sie auf die Schaltfläche zum Beenden klicken.

    ![Lokales Debuggen der Funktions-App: Breakpoint](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-function-app-to-azure"></a>Bereitstellen der Funktions-App in Azure

1. Klicken Sie im Projektexplorer von IntelliJ mit der rechten Maustaste auf Ihr Projekt, und wählen Sie *Azure > Deploy to Azure Functions* (In Azure Functions bereitstellen) aus.

    ![Bereitstellen von Funktionen in Azure](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. Falls Sie noch keine Funktions-App besitzen, klicken Sie auf *No available function, click to create a new one* (Keine verfügbare Funktion. Zum Erstellen einer neuen Funktion klicken).

    ![Bereitstellen von Funktionen in Azure: Erstellen einer App](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

1. Geben Sie den Namen der Funktions-App ein, und wählen Sie die entsprechenden Angaben für das Abonnement, die Plattform, die Ressourcengruppe und den App Service-Plan aus. Sie können hier auch eine Ressourcengruppe oder einen App Service-Plan erstellen. Übernehmen Sie die App-Einstellungen, klicken Sie auf *OK* , und warten Sie einige Minuten, bis die neue Funktion erstellt wird. Die Statusleiste *Creating New Function App...* (Neue Funktions-App wird erstellt...) wird nicht mehr angezeigt.

    ![Bereitstellen von Funktionen in Azure: Assistent zum Erstellen von Apps](media/functions-create-first-java-intellij/deploy-functions-create-app-wizard.png)

1. Wählen Sie die Funktions-App aus, in der die Bereitstellung vorgenommen werden soll. (Die von Ihnen soeben erstellte Funktions-App ist automatisch ausgewählt.) Klicken Sie zum Bereitstellen der Funktionen auf *Ausführen*.

    ![Screenshot des Dialogfelds zum Bereitstellen von Azure-Funktionen.](media/functions-create-first-java-intellij/deploy-functions-run.png)

    ![Bereitstellen von Funktionen in Azure: Protokoll](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-azure-functions-from-idea"></a>Verwalten von Azure Functions über IDEA

1. Sie können Ihre Funktionen mit *Azure Explorer* in IDEA verwalten. Klicken Sie auf *Funktions-App* , und alle Funktionen werden hier angezeigt.

    ![Anzeigen von Funktionen in Explorer](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. Klicken Sie zum Auswählen einer Funktion, und klicken Sie dann mit der rechten Maustaste darauf. Wählen Sie *Eigenschaften anzeigen* aus, um die Detailseite zu öffnen. 

    ![Anzeigen von Funktionseigenschaften](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. Klicken Sie mit der rechten Maustaste auf die Funktion *HttpTrigger-Java* , und wählen Sie *Triggerfunktion* aus. Der Browser wird mit der Trigger-URL geöffnet.

    ![Screenshot eines Browsers mit der URL.](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Hinzufügen weiterer Funktionen zum Projekt

1. Klicken Sie mit der rechten Maustaste auf das Paket *org.example.functions* , und wählen Sie *Neu > Azure Function Class* (Azure-Funktionsklasse) aus. 

    ![Eintrag zum Hinzufügen von Funktionen zum Projekt](media/functions-create-first-java-intellij/add-functions-entry.png)

1. Geben Sie im Assistenten zum Erstellen von Funktionsklassen den Klassennamen *HttpTest* ein, und wählen Sie *HttpTrigger* aus. Klicken Sie zum Erstellen auf *OK*. Auf diese Weise können Sie nach Bedarf neue Funktionen erstellen.

    ![Screenshot des Dialogfelds zum Erstellen einer Funktionsklasse.](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![Hinzufügen von Funktionen zum Projekt: Ausgabe](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Bereinigen von Functions

1. Löschen von Azure Functions in Azure Explorer
      
      ![Screenshot mit im Kontextmenü ausgewählter Option „Löschen“.](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>Nächste Schritte

Sie haben ein Java-Funktionsprojekt mit einer per HTTP ausgelösten Funktion erstellt, auf Ihrem lokalen Computer ausgeführt und für Azure bereitgestellt. Erweitern Sie nun Ihre Funktion durch...

> [!div class="nextstepaction"]
> [Hinzufügen einer Azure Storage-Warteschlangen-Ausgabebindung](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png