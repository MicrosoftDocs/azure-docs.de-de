---
title: Konfigurieren von Node.js-Apps
description: Erfahren Sie, wie Sie eine Node.js-App in den nativen Windows-Instanzen oder in einem vordefinierten Linux-Container in Azure App Service konfigurieren. In diesem Artikel werden die gängigsten Konfigurationsaufgaben vorgestellt.
ms.custom: devx-track-js, devx-track-azurecli
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 8bdf637ab773e90a5eac42bcaa443cf6741db636
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696012"
---
# <a name="configure-a-nodejs-app-for-azure-app-service"></a>Konfigurieren einer Node.js-App für Azure App Service

Node.js-Apps müssen mit allen erforderlichen NPM-Abhängigkeiten bereitgestellt werden. Die App Service-Bereitstellungs-Engine führt automatisch `npm install --production` für Sie aus, wenn Sie ein [Git-Repository](deploy-local-git.md) oder ein [ZIP-Paket](deploy-zip.md) mit aktivierter Buildautomatisierung bereitstellen. Wenn Sie Ihre Dateien jedoch mit [FTP/S](deploy-ftp.md) bereitstellen, müssen Sie die erforderlichen Pakete manuell hochladen.

Diese Anleitung enthält die wichtigsten Konzepte und Anweisungen für Node.js-Entwickler, die in App Service bereitstellen. Wenn Sie Azure App Service noch nie verwendet haben, befolgen Sie zunächst den [Node.js-Schnellstart](quickstart-nodejs.md) und das Tutorial [Node.js mit dem MongoDB](tutorial-nodejs-mongodb-app.md).

## <a name="show-nodejs-version"></a>Anzeigen der Node.js-Version

::: zone pivot="platform-windows"  

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um die aktuelle Node.js-Version anzuzeigen:

```azurecli-interactive
az webapp config appsettings list --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um alle unterstützten Node.js-Versionen anzuzeigen:

```azurecli-interactive
az webapp list-runtimes | grep node
```

::: zone-end

::: zone pivot="platform-linux"

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um die aktuelle Node.js-Version anzuzeigen:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um alle unterstützten Node.js-Versionen anzuzeigen:

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

::: zone-end

## <a name="set-nodejs-version"></a>Festlegen der Node.js-Version

::: zone pivot="platform-windows"  

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um `WEBSITE_NODE_DEFAULT_VERSION` auf eine unterstützte Version und für Ihre App eine [unterstützte Node.js-Version](#show-nodejs-version) festzulegen:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Diese Einstellung legt die zu verwendende Version von Node.js fest, sowohl zur Laufzeit als auch während der automatischen Paketwiederherstellung bei der App Service-Buildautomatisierung.

> [!NOTE]
> Sie sollten die Node.js-Version in der `package.json` des Projekts festlegen. Die Bereitstellungs-Engine wird in einem separaten Prozess ausgeführt, der alle unterstützten Node.js-Versionen enthält.

::: zone-end

::: zone pivot="platform-linux"

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um für Ihre App eine [unterstützte Node.js-Version](#show-nodejs-version) festzulegen:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Diese Einstellung legt die zu verwendende Version von Node.js fest, sowohl zur Laufzeit als auch während der automatischen Paketwiederherstellung in Kudu.

> [!NOTE]
> Sie sollten die Node.js-Version in der `package.json` des Projekts festlegen. Die Bereitstellungs-Engine wird in einem separaten Container ausgeführt, der alle unterstützten Node.js-Versionen enthält.

::: zone-end

## <a name="get-port-number"></a>Abrufen der Portnummer

Ihre Node.js-App muss am richtigen Port lauschen, um eingehende Anforderungen empfangen zu können.

::: zone pivot="platform-windows"  

In App Service unter Windows werden Node.js-Apps mit [IISNode](https://github.com/Azure/iisnode) gehostet, und Ihre Node.js-App sollte an dem Port lauschen, der in der Variablen `process.env.PORT` angegeben ist. Im folgenden Beispiel wird veranschaulicht, wie Sie hierbei für eine einfache Express-App vorgehen:

::: zone-end

::: zone pivot="platform-linux"  

App Service legt die Umgebungsvariable `PORT` im Node.js-Container fest und leitet die eingehenden Anforderungen unter dieser Portnummer an Ihren Container weiter. Um die Anforderungen erhalten zu können, sollte Ihre App mit `process.env.PORT` an diesem Port lauschen. Im folgenden Beispiel wird veranschaulicht, wie Sie hierbei für eine einfache Express-App vorgehen:

::: zone-end

```javascript
const express = require('express')
const app = express()
const port = process.env.PORT || 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
})
```

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Anpassen der Buildautomatisierung

Wenn Sie Ihre App mithilfe von Git- oder ZIP-Paketen mit aktivierter Buildautomatisierung bereitstellen, durchläuft die App Service-Buildautomatisierung die Schritte der folgenden Sequenz:

1. Ausführen eines benutzerdefinierten Skripts, falls mittels `PRE_BUILD_SCRIPT_PATH` angegeben.
1. Ausführen von `npm install` ohne Flags, was npm `preinstall` und `postinstall`-Skripts einschließt und auch `devDependencies` installiert.
1. Ausführen von `npm run build`, wenn ein Buildskript in Ihrer *package.json* angegeben ist.
1. Ausführen von `npm run build:azure`, wenn ein build:azure-Skript in Ihrer *package.json* angegeben ist.
1. Ausführen eines benutzerdefinierten Skripts, falls mittels `POST_BUILD_SCRIPT_PATH` angegeben.

> [!NOTE]
> Wie in der [npm-Dokumentation](https://docs.npmjs.com/misc/scripts) beschrieben, werden Skripts namens `prebuild` und `postbuild` vor bzw. nach `build` ausgeführt, falls angegeben. `preinstall` und `postinstall` werden vor bzw. nach `install` ausgeführt.

`PRE_BUILD_COMMAND` und `POST_BUILD_COMMAND` sind Umgebungsvariablen, die standardmäßig leer sind. Um Präbuildbefehle auszuführen, definieren Sie `PRE_BUILD_COMMAND`. Um Postbuildbefehle auszuführen, definieren Sie `POST_BUILD_COMMAND`.

Im folgenden Beispiel werden die beiden Variablen für eine Reihe von Befehlen angegeben, die durch Kommas getrennt sind.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Weitere Umgebungsvariablen zum Anpassen der Buildautomatisierung finden Sie unter [Oryx-Konfiguration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Weitere Informationen, wie App Service Node.js-Apps in Linux ausführt und erstellt, finden Sie unter [Oryx-Dokumentation: Erkennen und Erstellen von Node.js-Apps](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md).

## <a name="configure-nodejs-server"></a>Konfigurieren des Node.js-Servers

Die Node.js-Container werden mit [PM2](https://pm2.keymetrics.io/), einem Manager für den Produktionsprozess, ausgeliefert. Sie können Ihre App so konfigurieren, dass sie mit PM2, mit NPM oder mit einem benutzerdefinierten Befehl startet.

- [Benutzerdefinierten Befehl ausführen](#run-custom-command)
- [NPM-Start ausführen](#run-npm-start)
- [Mit PM2 ausführen](#run-with-pm2)

### <a name="run-custom-command"></a>Benutzerdefinierten Befehl ausführen

App Service kann Ihre App mit einem benutzerdefinierten Befehl starten, z. B. mit einer ausführbaren Datei wie *run.sh*. Führen Sie z. B. zum Ausführen von `npm run start:prod` den folgenden Befehl in der [Cloud Shell](https://shell.azure.com) aus:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>NPM-Start ausführen

Um Ihre App mit `npm start` zu starten, stellen Sie einfach sicher, dass sich ein `start`-Skript in der Datei *package.json* befindet. Beispiel:

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Führen Sie den folgenden Befehl in der [Cloud Shell](https://shell.azure.com) aus, um eine benutzerdefinierte *package.json*-Datei in Ihrem Projekt zu verwenden:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Mit PM2 ausführen

Der Container startet Ihre App automatisch mit PM2, wenn sich eine der üblichen Node.js-Dateien in Ihrem Projekt befindet:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Eine der folgenden [PM2-Dateien](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json* und *ecosystem.config.js*

Sie können auch eine benutzerdefinierte Startdatei mit den folgenden Erweiterungen konfigurieren:

- Eine *.js*-Datei
- Eine [PM2-Datei](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) mit der Erweiterung *.json*, *.config.js*, *.yaml* oder *.yml*.

Führen Sie zum Hinzufügen einer benutzerdefinierten Startdatei den folgenden Befehl in der [Cloud Shell](https://shell.azure.com) aus:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Remote Debuggen

> [!NOTE]
> Das Remotedebuggen befindet sich derzeit in der Vorschauversion.

Sie können Ihre Node.js-App remote in [Visual Studio Code](https://code.visualstudio.com/) debuggen, wenn Sie sie so konfigurieren, dass sie [mit PM2 ausgeführt](#run-with-pm2) wird, außer Sie führen sie mit einer *.config.js, *.yml oder *.yaml* aus.

In den meisten Fällen ist keine zusätzliche Konfiguration für Ihre App erforderlich. Wenn Ihre App mit einer *process.json*-Datei (Standard oder benutzerdefiniert) ausgeführt wird, muss sie über eine `script`-Eigenschaft im JSON-Stammverzeichnis verfügen. Beispiel:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Um Visual Studio Code für das Remotedebuggen einzurichten, installieren Sie die [App Service-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Folgen Sie den Anweisungen auf der Erweiterungsseite, und melden Sie sich in Visual Studio Code bei Azure an.

Suchen Sie im Azure Explorer die App, die Sie debuggen möchten, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Remotedebuggen starten** aus. Klicken Sie auf **Ja**, um es für Ihre App zu aktivieren. App Service startet für Sie einen Tunnelproxy und fügt den Debugger an. Sie können dann Anforderungen an die App stellen und sehen, wie der Debugger an Haltepunkten anhält.

Nachdem Sie mit dem Debuggen fertig sind, stoppen Sie den Debugger, indem Sie **Trennen** auswählen. Wenn Sie dazu aufgefordert werden, sollten Sie auf **Ja** klicken, um das Remotedebuggen zu deaktivieren. Um es später zu deaktivieren, klicken Sie mit der rechten Maustaste erneut auf Ihre App im Azure Explorer, und wählen Sie **Remotedebuggen deaktivieren** aus.

::: zone-end

## <a name="access-environment-variables"></a>Zugreifen auf Umgebungsvariablen

In App Service können Sie [App-Einstellungen außerhalb Ihres App-Codes festlegen](configure-common.md). Anschließend können Sie darauf unter Verwendung des Node.js-Standardmusters zugreifen. Verwenden Sie beispielsweise den folgenden Code, um auf eine App-Einstellung namens `NODE_ENV` zuzugreifen:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Run Grunt/Bower/Gulp

Standardmäßig führt die App Service-Buildautomatisierung `npm install --production` aus, wenn sie erkennt, dass eine Node.js-App über Git bereitgestellt wird (oder über eine ZIP-Bereitstellung mit aktivierter Buildautomatisierung). Wenn Ihre App eines der beliebten Automationstools wie Grunt, Bower oder Gulp benötigt, müssen Sie ein [benutzerdefiniertes Bereitstellungsskript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) bereitstellen, um es auszuführen.

Damit Ihr Repository diese Tools ausführen kann, müssen Sie sie zu den Abhängigkeiten in *package.json* hinzufügen. Beispiel:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Wechseln Sie von einem lokalen Terminalfenster aus in das Stammverzeichnis Ihres Repositorys, und führen Sie die folgenden Befehle aus:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Das Stammverzeichnis Ihres Repositorys enthält jetzt zwei zusätzliche Dateien: *.deployment* und *deploy.sh*.

Öffnen Sie *deploy.sh* und suchen Sie den Abschnitt `Deployment`, der wie folgt aussieht:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Dieser Abschnitt endet mit dem Ausführen von `npm install --production`. Fügen Sie den Codeabschnitt, den Sie zum Ausführen des erforderlichen Tools benötigen, *am Ende* des Abschnitts `Deployment` hinzu:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Weitere Informationen finden Sie in einem [Beispiel im der MEAN.js-Beispiel](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), in dem das Bereitstellungsskript auch einen benutzerdefinierten `npm install`-Befehl ausführt.

### <a name="bower"></a>Bower

Dieser Codeausschnitt führt `bower install` aus.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Dieser Codeausschnitt führt `gulp imagemin` aus.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Dieser Codeausschnitt führt `grunt` aus.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Erkennen einer HTTPS-Sitzung

In App Service erfolgt die [SSL-Terminierung](https://wikipedia.org/wiki/TLS_termination_proxy) in den Modulen für den Netzwerklastenausgleich, sodass alle HTTPS-Anforderungen Ihre App als unverschlüsselte HTTP-Anforderungen erreichen. Wenn Ihre App-Logik überprüfen muss, ob Benutzeranforderungen verschlüsselt sind, können Sie dazu den Header `X-Forwarded-Proto` untersuchen.

Gängige Webframeworks ermöglichen den Zugriff auf die Information `X-Forwarded-*` in Ihrem App-Standardmuster. In [Express](https://expressjs.com/) können Sie [trust proxies](https://expressjs.com/guide/behind-proxies.html) (Proxys vertrauen) verwenden. Beispiel:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Zugreifen auf Diagnoseprotokolle

::: zone pivot="platform-windows"  

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>Problembehandlung

Wenn sich eine funktionierende Node.js-App in App Service anders verhält oder Fehler aufweist, versuchen Sie Folgendes:

- [Greifen Sie auf den Protokolldatenstrom zu](#access-diagnostic-logs).
- Testen Sie die App lokal im Produktionsmodus. App Service führt Ihre Node.js-Apps im Produktionsmodus aus, daher müssen Sie sicherstellen, dass Ihr Projekt lokal wie erwartet im Produktionsmodus funktioniert. Beispiel:
    - Abhängig von Ihrer *package.json* können verschiedene Pakete für den Produktionsmodus installiert sein (`dependencies` oder `devDependencies`).
    - Bestimmte Webframeworks können statische Dateien im Produktionsmodus unterschiedlich bereitstellen.
    - Bestimmte Webframeworks können benutzerdefinierte Startskripts verwenden, wenn sie im Produktionsmodus ausgeführt werden.
- Führen Sie Ihre App in App Service im Entwicklungsmodus aus. In [MEAN.js](https://meanjs.org/) können Sie Ihre App z. B. zur Laufzeit in den Entwicklungsmodus versetzen, indem Sie die [App-Einstellung `NODE_ENV` festlegen](configure-common.md).

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Node.js-App mit MongoDB](tutorial-nodejs-mongodb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [Häufig gestellte Fragen (FAQ) zu Azure App Service unter Linux](faq-app-service-linux.md)

::: zone-end

