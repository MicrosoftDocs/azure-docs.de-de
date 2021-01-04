---
title: Hinzufügen einer API zu Azure Static Web Apps mit Azure Functions
description: Steigen Sie in Azure Static Web Apps ein, indem Sie Ihrer statischen Web-App mit Azure Functions eine serverlose API hinzufügen.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/29/2020
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: 53be2ee9b6b3ab46f081747aec58b9c99fc54f0d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349955"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Hinzufügen einer API zu Azure Static Web Apps (Vorschau) mit Azure Functions

Sie können Azure Static Web Apps serverlose APIs per Integration in Azure Functions hinzufügen. In diesem Artikel wird veranschaulicht, wie Sie eine API für eine Azure Static Web Apps-Website hinzufügen und bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Konto mit einem aktiven Abonnement.
  - Falls Sie kein Konto besitzen, können Sie [kostenlos eines erstellen](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure Functions-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) für Visual Studio Code
- [Live Server-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) für Visual Studio Code
- [Node.js](https://nodejs.org/download/) zum lokalen Ausführen der API-App

## <a name="create-a-git-repository"></a>Erstellen eines Git-Repositorys

Die folgenden Schritte veranschaulichen, wie Sie ein neues Repository erstellen und die Dateien auf Ihrem Computer klonen.

1. Stellen Sie sicher, dass Sie bei GitHub angemeldet sind, und navigieren Sie zu [https://github.com/staticwebdev/vanilla-basic/generate](https://github.com/login?return_to=/staticwebdev/vanilla-basic/generate), um ein neues Repository zu erstellen.
1. Geben Sie im Feld _Repositoryname_ den Namen **my-vanilla-api** ein.
1. Klicken Sie auf **Create repository from template** (Repository aus Vorlage erstellen).

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Erstellen eines neuen Repositorys aus „vanilla-basic“":::

Sobald Ihr Projekt erstellt ist, kopieren Sie die URL in Ihrem Browser für das neue Repository. Sie verwenden diese URL in Visual Studio Code, um das Git-Repository zu klonen.

1. Drücken Sie **F1**, um den Befehl in der Befehlspalette zu öffnen.
1. Fügen Sie die URL in die Aufforderung von _Git: Clone_ ein, und drücken Sie die **EINGABETASTE**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Klonen eines GitHub-Projekts mit Visual Studio Code":::

    Folgen Sie den Eingabeaufforderungen, um einen Speicherort für das Repository zum Klonen des Projekts auszuwählen.

## <a name="create-the-api"></a>Erstellen der API

Als nächstes erstellen Sie ein Azure Functions-Projekt als API der Anwendung. 

1. Erstellen Sie im Projekt _my-vanilla-api_ einen Unterordner mit dem Namen **api**.
1. Drücken Sie **F1**, um die Befehlspalette zu öffnen.
1. Geben Sie **Azure Functions: Neues Projekt erstellen...** ein.
1. Drücken Sie die **EINGABETASTE**.
1. Wählen Sie **Durchsuchen** aus.
1. Wählen Sie den Ordner **api** als Verzeichnis für Ihren Projektarbeitsbereich aus.
1. Wählen Sie **Auswählen** aus.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Screenshot: API-Ordner und Schaltfläche „Auswählen“":::

1. Geben Sie nach entsprechender Aufforderung Folgendes ein:

    - _Sprache auswählen_: Wählen Sie **JavaScript** aus.
    - _Select a template for your project's first function_ (Wählen Sie die Vorlage für die erste Funktion Ihres Projekts aus.): Wählen Sie **HTTP-Trigger** aus.
    - _Provide a function name_ (Geben Sie einen Funktionsnamen an.): Geben Sie **GetMessage** ein.
    - _Autorisierungsstufe:_ Wählen Sie **Anonym** aus, damit Ihr Funktionsendpunkt von jedem Benutzer aufgerufen werden kann.
        - Weitere Informationen zu Autorisierungsstufen finden Sie unter [Autorisierungsschlüssel](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys).

Visual Studio Code generiert ein Azure Functions-Projekt mit einer über HTTP ausgelösten Funktion.

Ihre App besitzt jetzt eine Projektstruktur, die dem folgenden Beispiel ähnelt.

```files
├── api
│   ├── GetMessage
│   │   ├── function.json
│   │   ├── index.js
│   │   └── sample.dat
│   ├── host.json
│   ├── local.settings.json
│   ├── package.json
│   └── proxies.json
├── index.html
├── readme.md
└── styles.css
```

Als nächstes ändern Sie die Funktion `GetMessage`, um eine Nachricht an das Front-End zurückzugeben.

1. Aktualisieren Sie die Funktion `GetMessage` unter _api/GetMessage/index.js_ mit dem folgenden Code.

    ```javascript
    module.exports = async function (context, req) {
      context.res = {
        body: {
          text: "Hello from the API"
        }
      };
    };
    ```

1. Aktualisieren Sie die Konfiguration für `GetMessage` unter `api/GetMessage/function.json` mit den folgenden Einstellungen.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```

Mit den obigen Einstellungen gilt für den API-Endpunkt Folgendes:

- Wird ausgelöst, wenn eine HTTP-Anforderung an die Funktion gesendet wird
- Verfügbar für alle Anforderungen, unabhängig vom Authentifizierungsstatus
- Wird über die Route _/api/message_ verfügbar gemacht

## <a name="run-the-api-locally"></a>Lokales Ausführen der API

Dank der Integration zwischen Visual Studio Code und [Azure Functions Core Tools](../azure-functions/functions-run-local.md) können Sie dieses Projekt vor der Veröffentlichung in Azure zunächst auf Ihrem lokalen Entwicklungscomputer ausführen.

> [!TIP]
> Stellen Sie sicher, dass Sie alle im Abschnitt [Voraussetzungen](#prerequisites) aufgeführten Ressourcen installiert haben, bevor Sie fortfahren.

1. Führen Sie die Funktion aus, indem Sie **F5** drücken, um die Functions-App zu starten.

1. Falls Azure Functions Core Tools noch nicht installiert ist, müssen Sie in der Aufforderung die Option **Installieren** auswählen.

    Die Core Tools zeigen die Ausgabe der aktiven Anwendung im Bereich _Terminal_ an. In der Ausgabe wird der lokal ausgeführte URL-Endpunkt Ihrer per HTTP ausgelösten Funktion angezeigt.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Screenshot: Registerkarte „TERMINAL“ mit angezeigter URL":::

1. Wenn die Core Tools ausgeführt werden, navigieren Sie zu der folgenden URL, um zu überprüfen, ob die API ordnungsgemäß ausgeführt wird: `http://localhost:7071/api/message`.

   Die Antwort im Browser sollte ähnlich wie im folgenden Beispiel aussehen:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Screenshot: Textantwort in einem Browser":::

1. Drücken Sie **UMSCHALT+F5**, um die Debugsitzung zu beenden.

### <a name="call-the-api-from-the-application"></a>Aufrufen der API aus der Anwendung

Bei der Bereitstellung in Azure werden Anforderungen an die API automatisch an die Functions-App für Anforderungen weitergeleitet, die an die `api`-Route gesendet werden. Wenn Sie lokal arbeiten, müssen Sie die Anwendungseinstellungen für Proxyanforderungen an die lokale API konfigurieren.

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

#### <a name="update-html-files-to-access-the-api"></a>Aktualisieren von HTML-Dateien für den Zugriff auf die API

1. Aktualisieren Sie anschließend den Inhalt der Datei _index.html_ mit dem folgenden Code, um den Text aus der API-Funktion abzurufen und auf dem Bildschirm anzuzeigen:

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading content from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

1. Drücken Sie **F5**, um das API-Projekt zu starten.

1. Drücken Sie **F1**, und wählen Sie **Live Server: Mit Live-Server öffnen** aus.

    Die API-Meldung sollte jetzt auf der Webseite angezeigt werden.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Screenshot: API-Meldung in einem Browser":::

   > [!NOTE]
   > Sie können auch andere HTTP-Server oder -Proxys verwenden, um die Datei `index.html` bereitzustellen. Der Zugriff auf `index.html` über `file:///` funktioniert nicht.

1. Drücken Sie **UMSCHALT+F5**, um das API-Projekt zu beenden.

### <a name="commit-and-push-your-changes-to-github"></a>Committen und Pushen Ihrer Änderungen für GitHub

Verwenden Sie Visual Studio Code, um für Ihre Änderungen das Committen und Pushen zum Git-Remoterepository durchzuführen.

1. Drücken Sie **F1**, um die Befehlspalette zu öffnen.
1. Geben Sie **Git: Commit für alle** ein.
1. Fügen Sie eine Commitnachricht hinzu, und drücken Sie die **EINGABETASTE**.
1. Drücken Sie **F1**.
1. Geben Sie **Git: push** ein, und drücken Sie die **EINGABETASTE**.

## <a name="create-a-static-web-app"></a>Erstellen einer statischen Web-App

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Klicken Sie auf **Ressource erstellen**.
1. Suchen Sie nach **Static Web App**.
1. Klicken Sie auf **Static Web App (Vorschau)** .
1. Klicken Sie auf **Erstellen**

Fügen Sie als nächstes die App-spezifischen Einstellungen hinzu.

1. Wählen Sie Ihr _Azure-Abonnement_ aus.
1. Wählen Sie eine neue _Ressourcengruppe_ aus, oder erstellen Sie sie.
1. Geben Sie der App den Namen **my-vanilla-api**.
1. Wählen Sie eine _Region_ aus, die in Ihrer Nähe liegt.
1. Wählen Sie für _SKU_ die Option **Free** aus.
1. Klicken Sie auf die Schaltfläche **Mit GitHub anmelden**, und führen Sie die Authentifizierung mit GitHub durch.
1. Wählen Sie Ihre bevorzugte _Organisation_ aus.
1. Wählen Sie in der Dropdownliste _Repository_ die Option **my-vanilla-api** aus.
1. Wählen Sie in der Dropdownliste _Branch_ den Eintrag **master** aus.
1. Wählen Sie das Framework für die Buildkonfiguration aus der Dropdownliste _Buildvoreinstellungen_ aus.

 > Diese Felder entsprechen der Standardprojektstruktur des App-Typs. Ändern Sie die Werte so, dass Sie Ihrer App entsprechen.

Fügen Sie als Nächstes die folgenden Builddetails hinzu.

1. Geben Sie **/** für den _App-Speicherort_ ein.
1. Geben Sie im Feld _API-Speicherort_ den Speicherort **api** ein.
1. Löschen Sie den Standardwert aus _Speicherort für App-Artefakte_, und lassen Sie das Feld leer.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf die Schaltfläche **Erstellen**.

    Sobald Sie auf die Schaltfläche _Erstellen_ klicken, führt Azure zwei Schritte aus. Zunächst werden die zugrunde liegenden Clouddienste erstellt, um die App zu unterstützen. Anschließend beginnt ein Hintergrundprozess mit der Erstellung und Bereitstellung der Anwendung.

1. Klicken Sie auf die Schaltfläche **Zu Ressource wechseln**, um zur Seite _Übersicht_ der Web-App zu gelangen.

    Während die App im Hintergrund erstellt wird, können Sie auf das Banner mit dem Link klicken, um den Buildstatus anzuzeigen.

    :::image type="content" source="media/add-api/github-action-flag.png" alt-text="GitHub-Workflow":::

1. Sobald die Bereitstellung abgeschlossen ist, können Sie zu der Web-App navigieren, indem Sie auf den _URL_-Link klicken, der auf der Seite _Übersicht_ angezeigt wird.

    :::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Zugreifen auf die URL der statischen App über das Azure-Portal":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht zur weiteren Verwendung behalten möchten, können Sie die folgenden Schritte ausführen, um die Azure Static Web App-Anwendung und die zugehörigen Ressourcen zu löschen.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Geben Sie in der oberen Suchleiste **Ressourcengruppen** ein.
1. Klicken Sie auf **Ressourcengruppen**.
1. Wählen Sie **myResourceGroup** aus.
1. Stellen Sie auf der Seite _myResourceGroup_ sicher, dass die Ressourcen aufgelistet sind, die Sie löschen möchten.
1. Wählen Sie **Löschen** aus.
1. Geben Sie **myResourceGroup** in das Textfeld ein.
1. Klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konfigurieren von App-Einstellungen](./application-settings.md)
