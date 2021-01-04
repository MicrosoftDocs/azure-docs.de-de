---
title: 'Schnellstart: Erstellen einer Node.js-Web-App'
description: Stellen Sie in wenigen Minuten Ihre erste Node.js-App vom Typ „Hallo Welt“ in Azure App Service bereit.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
ms.openlocfilehash: b42abfda863ed38eb3c29004630f905edef075c4
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359101"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Erstellen einer Node.js-Web-App in Azure

::: zone pivot="platform-windows"  

Beginnen Sie mit der Verwendung von Azure App Service, indem Sie lokal eine Node.js-/Express-App mit Visual Studio Code erstellen und die App anschließend in der Cloud bereitstellen. Da Sie einen kostenlosen App Service-Tarif verwenden, fallen bei dieser Schnellstartanleitung keine Kosten an.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">Installation von Git</a>
- [Node.js und npm](https://nodejs.org). Führen Sie den Befehl `node --version` aus, um sich zu vergewissern, dass Node.js installiert ist.
- [Visual Studio Code](https://code.visualstudio.com/)
- Die [Azure App Service-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) für Visual Studio Code.

## <a name="clone-and-run-a-local-nodejs-application"></a>Klonen und Ausführen einer lokalen Node.js-Anwendung

1. Öffnen Sie auf Ihrem lokalen Computer ein Terminal, und klonen Sie das Beispielrepository:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navigieren Sie zu dem neuen App-Ordner:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Starten Sie die App, um sie lokal zu testen:

    ```bash
    npm start
    ```
    
1. Navigieren Sie in Ihrem Browser zu `http://localhost:1337`. Im Browser sollte „Hello World!“ angezeigt werden.

1. Drücken Sie im Terminal **STRG**+**C** , um den Server zu beenden.

> [!div class="nextstepaction"]
> [Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Bereitstellen der Anwendung in Azure

In diesem Abschnitt wird Ihre Node.js-App mithilfe von VS Code und der Azure App Service-Erweiterung in Azure bereitgestellt.

1. Vergewissern Sie sich im Terminal, dass Sie sich im Ordner *nodejs-docs-hello-world* befinden, und starten Sie Visual Studio Code mit dem folgenden Befehl:

    ```bash
    code .
    ```

1. Wählen Sie auf der Aktivitätsleiste von VS Code das Azure-Logo aus, um den **AZURE APP SERVICE-Explorer** anzuzeigen. Wählen Sie **Bei Azure anmelden...** aus, und befolgen Sie die Anweisungen. (Sollte bei Ihnen ein Fehler auftreten, lesen Sie [Behandeln von Problemen bei der Azure-Anmeldung](#troubleshooting-azure-sign-in) weiter unten.) Nach der Anmeldung sollte im Explorer der Name Ihres Azure-Abonnements angezeigt werden.

    ![Anmelden bei Azure](media/quickstart-nodejs/sign-in.png)

1. Wählen Sie im **AZURE APP SERVICE-Explorer** von VS Code den blauen Aufwärtspfeil aus, um Ihre App in Azure bereitzustellen. (Der gleiche Befehl kann auch über die **Befehlspalette** ( **STRG**+**UMSCHALT**+**P** ) aufgerufen werden. Geben Sie hierzu „In Web-App bereitstellen“ ein, und wählen Sie den Befehl **Azure App Service: In Web-App bereitstellen** aus.)

    :::image type="content" source="media/quickstart-nodejs/deploy.png" alt-text="Screenshot: Azure-App-Dienst in VS Code mit ausgewähltem blauem Pfeil":::
        
1. Wählen Sie den Ordner *nodejs-docs-hello-world* aus.

1. Wählen Sie eine Erstellungsoption basierend auf dem Betriebssystem aus, auf dem die Bereitstellung erfolgen soll:

    - Linux: Wählen Sie **Neue Web-App erstellen** aus.
    - Windows: Wählen Sie **Neue Web-App erstellen... Erweitert** aus.

1. Geben Sie einen global eindeutigen Namen für Ihre Web-App ein, und drücken Sie die **EINGABETASTE**. Der Name muss innerhalb von Azure eindeutig sein und darf nur alphanumerische Zeichen („A–Z“, „a–z“ und „0–9“) und Bindestriche („-“) enthalten.

1. Wenn Linux Ihre Zielplattform ist, wählen Sie eine Node.js-Version aus, wenn Sie dazu aufgefordert werden. Eine **LTS** -Version wird empfohlen.

1. Wenn Sie Windows als Zielplattform verwenden, führen Sie die folgenden Zusatzschritte aus:
    1. Wählen Sie **Neue Ressourcengruppe erstellen** aus, und geben Sie einen Namen für die Ressourcengruppe ein (beispielsweise `AppServiceQS-rg`).
    1. Wählen Sie als Betriebssystem **Windows** aus.
    1. Wählen Sie **Neuen App Services-Plan erstellen** aus, geben Sie einen Namen für den Plan ein (beispielsweise `AppServiceQS-plan`), und wählen Sie anschließend den Tarif **F1 Free** aus.
    1. Wählen Sie **Vorerst überspringen** aus, wenn eine Eingabeaufforderung zu Application Insights sehen.
    1. Wählen Sie eine Region in Ihrer Nähe oder in der Nähe von Ressourcen aus, auf die Sie zugreifen möchten.

1. Wenn Sie auf alle Eingabeaufforderungen reagiert haben, werden in VS Code in einem Benachrichtigungspopup die Azure-Ressourcen angezeigt, die für Ihre App erstellt werden.

    Wählen Sie bei der Bereitstellung unter Linux **Ja** aus, wenn Sie aufgefordert werden, Ihre Konfiguration für die Ausführung von `npm install` auf dem Linux-Zielserver zu aktualisieren.

    ![Aufforderung zum Aktualisieren der Konfiguration auf dem Linux-Zielserver](media/quickstart-nodejs/server-build.png)

1. Wählen Sie **Ja** aus, wenn **Always deploy the workspace „nodejs-docs-hello-world“ to (app name)** (Arbeitsbereich „nodejs-docs-hello-world“ immer in (App-Name) bereitstellen) angezeigt wird. Durch Auswählen von **Ja** wird VS Code angewiesen, für nachfolgende Bereitstellungen automatisch die gleiche App Service-Web-App zu verwenden.

1. Wählen Sie bei einer Bereitstellung für Linux in der Aufforderung die Option **Website durchsuchen** aus, um nach Abschluss der Bereitstellung Ihre neu bereitgestellte Web-App anzuzeigen. Im Browser sollte „Hello World!“ angezeigt werden.

1. Legen Sie bei einer Bereitstellung für Windows zunächst die Node.js-Versionsnummer für die Web-App fest:

    1. Erweitern Sie in VS Code den Knoten für den neuen App-Dienst, klicken Sie mit der rechten Maustaste auf **Anwendungseinstellungen** , und wählen Sie anschließend **Neue Einstellung hinzufügen...** aus:

        ![Hinzufügen eines Befehls für eine App-Einstellung](media/quickstart-nodejs/add-setting.png)

    1. Geben Sie `WEBSITE_NODE_DEFAULT_VERSION` für den Einstellungsschlüssel ein.
    1. Geben Sie `10.15.2` für den Einstellungswert ein.
    1. Klicken Sie mit der rechten Maustaste auf den Knoten für den App-Dienst, und wählen Sie **Neu starten** aus.

        ![Befehl zum Neustarten der App](media/quickstart-nodejs/restart.png)

    1. Klicken Sie noch einmal mit der rechten Maustaste auf den Knoten für den App-Dienst, und wählen Sie **Website durchsuchen** aus.

> [!div class="nextstepaction"]
> [Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Behandeln von Problemen bei der Azure-Anmeldung

Sollte bei der Azure-Anmeldung der Fehler **Abonnement mit dem Namen [Abonnement-ID] nicht gefunden** angezeigt werden, befinden Sie sich möglicherweise hinter einem Proxy und können die Azure-API nicht erreichen. Konfigurieren Sie die Umgebungsvariablen `HTTP_PROXY` und `HTTPS_PROXY` mithilfe von `export` in Ihrem Terminal mit Ihren Proxyinformationen.

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Wenn sich das Problem durch Festlegen der Umgebungsvariablen nicht beheben lässt, wenden Sie sich an uns, indem Sie oben die Schaltfläche **Ich bin auf ein Problem gestoßen** auswählen.

### <a name="update-the-app"></a>Aktualisieren der App

Sie können Änderungen für diese App bereitstellen, indem Sie in VS Code Änderungen vornehmen, Ihre Dateien speichern und anschließend den gleichen Prozess verwenden wie vorhin, dabei aber keine neue App erstellen, sondern die vorhandene App auswählen.

## <a name="viewing-logs"></a>Anzeigen von Protokollen

Sie können sich die Protokollausgabe (Aufrufe von `console.log`) der App direkt im VS Code-Ausgabefenster ansehen.

1. Klicken Sie im **AZURE APP SERVICE-Explorer** mit der rechten Maustaste auf den App-Knoten, und wählen Sie **Start Streaming Logs** (Streamen der Protokolle starten) aus.

    ![„Start Streaming Logs“ (Streamen der Protokolle starten)](media/quickstart-nodejs/view-logs.png)

1. Wenn Sie dazu aufgefordert werden, aktivieren Sie die Protokollierung, und starten Sie die Anwendung neu. Sobald die App neu gestartet wurde, wird das VS Code-Ausgabefenster mit einer Verbindung mit dem Protokollstream geöffnet. 

    :::image type="content" source="media/quickstart-nodejs/enable-restart.png" alt-text="Screenshot: Visual Studio Code-Aufforderung zum Aktivieren der Protokollierung und zum Neustarten der Anwendung mit ausgewählter Schaltfläche „Ja“":::

1. Nach einigen Sekunden wird im Ausgabefenster eine Meldung mit dem Hinweis angezeigt, dass eine Verbindung mit dem Protokollstreamingdienst hergestellt wurde. Sie können weitere Ausgabeaktivitäten generieren, indem Sie die Seite im Browser aktualisieren.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

> [!div class="nextstepaction"]
> [Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Nächste Schritte

Herzlichen Glückwunsch! Sie haben diese Schnellstartanleitung erfolgreich abgeschlossen!

> [!div class="nextstepaction"]
> [Tutorial: Node.js-App mit MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Konfigurieren der Node.js-App](configure-language-nodejs.md)

Sehen Sie sich die anderen Azure-Erweiterungen an.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure-Funktionen](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-Tools](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Sie können auch alle Erweiterungen auf einmal erhalten, indem Sie das [Node Pack for Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) installieren.
::: zone-end

::: zone pivot="platform-linux"  
## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Konto besitzen, [registrieren Sie sich jetzt](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) für ein kostenloses Konto mit 200 US-Dollar Azure-Guthaben, um verschiedene Dienstkombinationen auszuprobieren.

[Visual Studio Code](https://code.visualstudio.com/) sowie [Node.js und npm](https://nodejs.org/en/download) – der Node.js-Paket-Manager – müssen installiert sein.

Sie müssen auch die [Azure App Service-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) installieren, mit der Sie Linux-Web-Apps im Azure-PaaS-Dienst (Platform-as-a-Service) erstellen, verwalten und bereitstellen können.

### <a name="sign-in"></a>Anmelden

Melden Sie sich bei Ihrem Azure-Konto an, sobald die Erweiterung installiert ist. Wählen Sie in der Aktivitätsleiste das Azure-Logo aus, um den **AZURE APP SERVICE** -Explorer anzuzeigen. Wählen Sie **Bei Azure anmelden...** aus, und befolgen Sie die Anweisungen.

![Anmelden bei Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Problembehandlung

Wenn der Fehler **Abonnement mit dem Namen [Abonnement-ID] nicht gefunden** angezeigt wird, kann dies daran liegen, dass Ihr Computer sich hinter einem Proxy befindet und die Azure-API nicht erreichen kann. Konfigurieren Sie die Umgebungsvariablen `HTTP_PROXY` und `HTTPS_PROXY` mithilfe von `export` in Ihrem Terminal mit Ihren Proxyinformationen.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Wenn sich das Problem durch Festlegen der Umgebungsvariable nicht beheben lässt, wenden Sie sich an uns, indem Sie unten die Schaltfläche **Ich bin auf ein Problem gestoßen** auswählen.

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

Bevor Sie fortfahren, stellen Sie sicher, dass alle erforderlichen Komponenten installiert und konfiguriert sind.

In VS Code sehen Sie Ihre Azure-E-Mail-Adresse in der Statusleiste und Ihr Abonnement im **AZURE APP SERVICE-Explorer**.

> [!div class="nextstepaction"]
> [Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Erstellen der Node.js-Anwendung

Als Nächstes erstellen Sie eine Node.js-Anwendung, die in der Cloud bereitgestellt werden kann. In dieser Schnellstartanleitung wird ein Anwendungsgenerator verwendet, um in einem Terminal schnell ein Gerüst für die Anwendung zu erstellen.

> [!TIP]
> Wenn Sie das [Node.js-Tutorial](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial) bereits abgeschlossen haben, können Sie mit dem Schritt [Bereitstellen in Azure](#deploy-to-azure) fortfahren.

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Gerüst einer neuen Anwendung mit dem Express-Generator

[Express](https://www.expressjs.com) ist ein beliebtes Framework für die Erstellung und Ausführung von Node.js-Anwendungen. Sie können eine neue Express-Anwendung (bzw. ihr Gerüst) mithilfe des Tools [Express-Generator](https://expressjs.com/en/starter/generator.html) erstellen. Der Express-Generator wird als npm-Modul ausgeliefert und kann direkt (ohne Installation) mithilfe des npm-Befehlszeilentools `npx` ausgeführt werden.

```bash
npx express-generator myExpressApp --view pug --git
```

Die `--view pug --git`-Parameter weisen den Generator an, die Vorlagen-Engine [pug](https://pugjs.org/api/getting-started.html) zu verwenden (früher als `jade` bezeichnet) und eine `.gitignore`-Datei zu erstellen.

Um alle Abhängigkeiten der Anwendung zu installieren, wechseln Sie zum neuen Ordner und führen `npm install` aus.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Ausführen der Anwendung

Als Nächstes stellen Sie sicher, dass die Anwendung ausgeführt wird. Starten Sie die Anwendung über das Terminal, indem Sie den Befehl `npm start` zum Starten des Servers verwenden.

```bash
npm start
```

Öffnen Sie jetzt Ihren Browser, und navigieren Sie zu `http://localhost:3000`. Dort sollte ein Bildschirm wie der folgende angezeigt werden:

![Ausführen der Express-Anwendung](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Bereitstellen in Azure

In diesem Abschnitt stellen Sie Ihre Node.js-App mithilfe von VS Code und der Azure App Service-Erweiterung bereit. Diese Schnellstartanleitung verwendet das einfachste Bereitstellungsmodell, bei dem Ihre App gezippt und unter Linux in einer Azure-Web-App bereitgestellt wird.

### <a name="deploy-using-azure-app-service"></a>Bereitstellen mit Azure App Service

Öffnen Sie zunächst Ihren Anwendungsordner in VS Code.

```bash
code .
```

Wählen Sie im **AZURE APP SERVICE-Explorer** den blauen, nach oben weisenden Pfeil aus, um Ihre App in Azure bereitzustellen.

:::image type="content" source="./media/quickstart-nodejs/deploy.png" alt-text="Screenshot: Azure App Service-Menü in Visual Studio Code mit ausgewähltem blauem Bereitstellungspfeil":::

> [!TIP]
> Sie können die Anwendung auch über die **Befehlspalette** (STRG+UMSCHALT+P) bereitstellen, indem Sie „deploy to web app“ eingeben und den Befehl **Azure App Service: Bereitstellen in Web-App** ausführen.

1. Wählen Sie das Verzeichnis aus, das Sie zurzeit geöffnet haben: `myExpressApp`.

1. Wählen Sie **Eine neue Web-App erstellen** aus, was standardmäßig eine Bereitstellung auf App Service für Linux durchführt.

1. Geben Sie einen global eindeutigen Namen für Ihre Web-App ein, und drücken Sie die EINGABETASTE. Gültige Zeichen für den Namen einer App sind die Buchstaben a-z, die Ziffern 0-9 und der Bindestrich (-).

1. Wählen Sie Ihre **Node.js-Version** aus. Empfohlen wird LTS.

    Der Benachrichtigungskanal zeigt die Azure-Ressourcen an, die für Ihre App erstellt werden.

1. Wählen Sie **Ja** aus, wenn Sie aufgefordert werden, Ihre Konfiguration für die Ausführung von `npm install` auf dem Zielserver zu aktualisieren. Ihre App wird anschließend bereitgestellt.

    :::image type="content" source="./media/quickstart-nodejs/server-build.png" alt-text="Screenshot: Aufforderung zum Aktualisieren der Konfiguration auf dem Zielserver mit ausgewählter Schaltfläche „Ja“":::

1. Wenn die Bereitstellung startet, werden Sie aufgefordert, Ihren Arbeitsbereich zu aktualisieren, sodass spätere Bereitstellungen automatisch die gleiche App Service-Web-App als Ziel verwenden. Klicken Sie auf **Ja** , um sicherzustellen, dass Ihre Änderungen für die richtige App bereitgestellt werden.

    :::image type="content" source="./media/quickstart-nodejs/save-configuration.png" alt-text="Screenshot: Aufforderung zum Aktualisieren des Arbeitsbereichs mit ausgewählter Schaltfläche „Ja“":::

> [!TIP]
> Stellen Sie sicher, dass Ihre Anwendung an dem Port lauscht, der von der PORT-Umgebungsvariable `process.env.PORT` bereitgestellt wird.

### <a name="browse-the-app-in-azure"></a>Durchsuchen der App in Azure

Wählen Sie nach Abschluss der Bereitstellung an der Eingabeaufforderung **Website durchsuchen** aus, um Ihre neu bereitgestellte Web-App anzuzeigen.

### <a name="troubleshooting"></a>Problembehandlung

Wenn der Fehler **Sie sind nicht berechtigt, dieses Verzeichnis oder diese Seite anzuzeigen.** angezeigt wird, wurde die Anwendung wahrscheinlich nicht ordnungsgemäß gestartet. Fahren Sie mit dem nächsten Abschnitt fort, und sehen Sie sich die Protokollausgabe an, um den Fehler zu suchen und zu beheben. Wenn sich das Problem nicht beheben lässt, wenden Sie sich an uns, indem Sie unten die Schaltfläche **Ich bin auf ein Problem gestoßen** auswählen. Wir helfen Ihnen gerne weiter.

> [!div class="nextstepaction"]
> [Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Aktualisieren der App

Sie können Änderungen an dieser App bereitstellen, indem Sie den gleichen Prozess ausführen und die vorhandene App auswählen anstatt eine neue zu erstellen.

## <a name="viewing-logs"></a>Anzeigen von Protokollen

In diesem Abschnitt erfahren Sie, wie Sie die Protokolle der ausgeführten App Service-App anzeigen (bzw. verfolgen). Alle Aufrufe von `console.log` in der App werden in Visual Studio Code im Ausgabefenster angezeigt.

Suchen Sie die App im **AZURE APP SERVICE-Explorer** , klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Streamingprotokolle anzeigen** aus.

Das VS Code-Ausgabefenster wird mit einer Verbindung mit dem Protokollstream geöffnet.

![Anzeigen von Streamingprotokollen](./media/quickstart-nodejs/view-logs.png)

:::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Screenshot: Visual Studio Code-Aufforderung zum Aktivieren der Dateiprotokollierung und zum Neustarten der Web-App mit ausgewählter Schaltfläche „Ja“":::

Nach einigen Sekunden sehen Sie eine Meldung, die Sie darüber informiert, dass eine Verbindung mit dem Protokollstreamingdienst hergestellt wurde. Aktualisieren Sie die Seite mehrmals, um weitere Aktivitäten anzuzeigen.

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Nächste Schritte

Herzlichen Glückwunsch! Sie haben diese Schnellstartanleitung erfolgreich abgeschlossen!

Sehen Sie sich als Nächstes die anderen Azure-Erweiterungen an.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure-Funktionen](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-Tools](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Sie können auch alle Erweiterungen auf einmal erhalten, indem Sie das [Node Pack for Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) installieren.


::: zone-end
