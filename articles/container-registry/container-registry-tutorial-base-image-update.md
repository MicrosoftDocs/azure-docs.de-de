---
title: 'Tutorial: Auslösen der Imageerstellung bei der Aktualisierung eines Basisimages'
description: In diesem Tutorial erfahren Sie, wie Sie eine Azure Container Registry-Aufgabe so konfigurieren, dass automatisch Buildvorgänge für Containerimages in der Cloud ausgelöst werden, wenn ein Basisimage in der gleichen Registrierung aktualisiert wird.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 342f7fc37edb3a1623b72c57e8766b1a484d0e8d
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559149"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Tutorial: Automatisieren von Buildvorgängen für Containerimages nach der Aktualisierung eines Basisimages in einer Azure-Containerregistrierung 

[ACR Tasks](container-registry-tasks-overview.md) unterstützt automatisierte Containerimage-Buildvorgänge, wenn das [Basisimage eines Containers aktualisiert wird](container-registry-tasks-base-images.md) (also beispielsweise, wenn Sie das Betriebssystem oder das Anwendungsframework in einem Ihrer Basisimages patchen). 

In diesem Tutorial erfahren Sie, wie Sie eine ACR-Aufgabe erstellen, die einen Buildvorgang in der Cloud auslöst, wenn das Basisimage eines Containers in die gleiche Registrierung gepusht wird. Es steht auch ein Tutorial zur Verfügung, in dem eine ACR-Aufgabe erstellt wird, die einen Buildvorgang auslöst, wenn ein Basisimage in eine [andere Azure Container Registry-Instanz](container-registry-tutorial-private-base-image-update.md) gepusht wird. 

Dieses Tutorial umfasst folgende Punkte:

> [!div class="checklist"]
> * Erstellen des Basisimages
> * Erstellen eines Anwendungsimages in der gleichen Registrierung, um das Basisimage nachzuverfolgen 
> * Aktualisieren des Basisimages, um eine Aufgabe für das Anwendungsimage auszulösen
> * Anzeigen der ausgelösten Aufgabe
> * Überprüfen des aktualisierten Anwendungsimages

## <a name="prerequisites"></a>Voraussetzungen

### <a name="complete-the-previous-tutorials"></a>Arbeiten Sie die vorhergehenden Tutorials durch.

In diesem Tutorial wird vorausgesetzt, dass Sie bereits Ihre Umgebung konfiguriert und die Schritte aus den ersten beiden Tutorials der Reihe ausgeführt haben:

- Erstellen einer Azure-Containerregistrierung
- Forken des Beispielrepositorys
- Klonen des Beispielrepositorys
- Erstellen eines persönlichen GitHub-Zugriffstokens

Absolvieren Sie bei Bedarf die folgenden Tutorials, bevor Sie mit diesem Tutorial fortfahren:

[Erstellen von Containerimages in der Cloud mit Azure Container Registry Tasks](container-registry-tutorial-quick-task.md)

[Automatisieren von Buildvorgängen für Containerimages mit Azure Container Registry Tasks](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>Konfigurieren der Umgebung

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- Für diesen Artikel ist mindestens Version 2.0.46 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

Geben Sie für die folgenden Shell-Umgebungsvariablen geeignete Werte für Ihre Umgebung an. Dieser Schritt ist zwar nicht zwingend erforderlich, vereinfacht aber das Ausführen der mehrzeiligen Azure CLI-Befehle in diesem Tutorial. Wenn Sie diese Umgebungsvariablen nicht angeben, müssen Sie sie später jedes Mal die einzelnen Werte manuell ersetzen, wenn sie in einem der Beispielbefehle vorkommen.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```


### <a name="base-image-update-scenario"></a>Aktualisierungsszenario für das Basisimage

In diesem Tutorial wird ein Aktualisierungsszenario für das Basisimage behandelt, bei dem ein Basis- und ein Anwendungsimage in einer einzelnen Registrierung verwaltet werden. 

Das [Codebeispiel][code-sample] enthält zwei Dockerfiles: ein Anwendungsimage und ein als Basis angegebenes Image. In den folgenden Abschnitten erstellen Sie eine ACR-Aufgabe, die automatisch einen Buildvorgang für das Anwendungsimage auslöst, wenn eine neue Version des Basisimages an die gleiche Containerregistrierung gepusht wird.

* [Dockerfile-app:][dockerfile-app] Eine kleine Node.js-Webanwendung, die eine statische Webseite rendert, auf der die zugrunde liegende Node.js-Version angezeigt wird. Die Versionszeichenfolge wird simuliert: Sie zeigt den Inhalt der im Basisimage definierten Umgebungsvariablen `NODE_VERSION` an.

* [Dockerfile-base:][dockerfile-base] Das Image, das von `Dockerfile-app` als Basis angegeben wird. Es basiert auf einem [Node][base-node]-Image und enthält die Umgebungsvariable `NODE_VERSION`.

In den folgenden Abschnitten erstellen Sie eine Aufgabe, aktualisieren den Wert `NODE_VERSION` in der Dockerfile des Basisimages und erstellen anschließend das Basisimage mithilfe von ACR Tasks. Wenn die ACR-Aufgabe das neue Basisimage an Ihre Registrierung pusht, wird automatisch ein Buildvorgang für das Anwendungsimage ausgelöst. Außerdem können Sie das Containerimage optional lokal ausführen, um sich die verschiedenen Versionszeichenfolgen in den erstellten Images anzusehen.

In diesem Tutorial erstellt und pusht die ACR-Aufgabe ein Anwendungscontainerimage, das in einem Dockerfile angegeben ist. ACR Tasks kann mithilfe einer YAML-Datei, in der Schritte zum Erstellen, Pushen und optional zum Testen mehrerer Container definiert werden, auch [Aufgaben mit mehreren Schritten](container-registry-tasks-multi-step.md) ausführen.

## <a name="build-the-base-image"></a>Erstellen des Basisimages

Erstellen Sie zunächst das Basisimage mit einer *Schnellaufgabe* von ACR Tasks (unter Verwendung von [az acr build][az-acr-build]). Wie im [ersten Tutorial](container-registry-tutorial-quick-task.md) der Reihe bereits erläutert, wird dabei das Image erstellt und an Ihre Containerregistrierung gepusht, sofern der Buildvorgang erfolgreich war.

```azurecli
az acr build --registry $ACR_NAME --image baseimages/node:15-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Erstellt eine Aufgabe.

Erstellen Sie als Nächstes mit [az acr task create][az-acr-task-create] eine Aufgabe:

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name baseexample1 \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT
```

Diese Aufgabe ähnelt der Aufgabe, die Sie im [vorherigen Tutorial](container-registry-tutorial-build-task.md) erstellt haben. Sie weist ACR Tasks an, einen Buildvorgang für ein Image auszulösen, wenn Commits an das durch `--context` angegebene Repository gepusht werden. Während das im vorherigen Tutorial zum Erstellen des Images verwendete Dockerfile ein öffentliches Basisimage angibt (`FROM node:15-alpine`), gibt das Dockerfile [Dockerfile-app][dockerfile-app] in dieser Aufgabe ein Basisimage in der gleichen Registrierung an:

```dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
```

Mit dieser Konfiguration ist es einfach, weiter unten in diesem Tutorial einen Frameworkpatch im Basisimage zu simulieren.

## <a name="build-the-application-container"></a>Erstellen des Anwendungscontainers

Verwenden Sie [az acr task run][az-acr-task-run], um die Aufgabe manuell auszulösen und das Anwendungsimage zu erstellen. Dieser Schritt ist erforderlich, damit die Aufgabe die Abhängigkeit des Anwendungsimages vom Basisimage nachverfolgt.

```azurecli
az acr task run --registry $ACR_NAME --name baseexample1
```

Notieren Sie sich nach Abschluss der Aufgabe die **Ausführungs-ID** (beispielsweise „da6“), falls Sie den folgenden optionalen Schritt ausführen möchten.

### <a name="optional-run-application-container-locally"></a>Optional: Lokales Ausführen des Anwendungscontainers

Wenn Sie lokal (also nicht in Cloud Shell) arbeiten und Docker installiert ist, können Sie vor der Neuerstellung des Basisimages den Container ausführen, um die Anwendung in einem Webbrowser anzuzeigen. Bei Verwendung von Cloud Shell können Sie diesen Abschnitt überspringen. (Cloud-Shell unterstützt weder `az acr login` noch `docker run`.)

Authentifizieren Sie sich zunächst mit [az acr login][az-acr-login] bei der Containerregistrierung:

```azurecli
az acr login --name $ACR_NAME
```

Verwenden Sie nun `docker run`, um den Container lokal auszuführen. Ersetzen Sie **\<run-id\>** durch die Ausführungs-ID aus der Ausgabe des vorherigen Schritts (beispielsweise „da6“). Dieses Beispiel legt `myapp` als Namen für den Container fest und fügt den Parameter `--rm` ein, damit der Container entfernt wird, wenn Sie ihn beenden.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Navigieren Sie in Ihrem Browser zu `http://localhost:8080`. Daraufhin sollte eine Webseite mit der Node.js-Versionsnummer angezeigt werden, wie im folgenden Beispiel zu sehen. In einem späteren Schritt passen Sie die Version an, indem Sie der Versionszeichenfolge ein „a“ hinzufügen.

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-01.png" alt-text="Screenshot: Beispielanwendung im Browser":::

Führen Sie den folgenden Befehl aus, um den Container zu beenden und zu entfernen:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Auflisten der Builds

Führen Sie als Nächstes den Befehl [az acr task list-runs][az-acr-task-list-runs] aus, um eine Liste der Aufgabenausführungen anzuzeigen, die ACR Tasks für Ihre Registrierung abgeschlossen hat:

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

Wenn Sie das vorherige Tutorial absolviert (und die Registrierung nicht gelöscht) haben, sollte die Ausgabe in etwa wie im folgenden Beispiel aussehen. Notieren Sie sich die Anzahl der Aufgabenausführungen und die letzte Ausführungs-ID, um die Angaben nach der Aktualisierung des Basisimages im nächsten Abschnitt vergleichen zu können.

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
cax       baseexample1    linux       Succeeded  Manual     2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:29
cav       example2        linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:55
cau       example1        linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:40
cat       taskhelloworld  linux       Succeeded  Manual     2020-11-20T23:07:29Z  00:00:27
```

## <a name="update-the-base-image"></a>Aktualisieren der Basisimages

Hier simulieren Sie einen Frameworkpatch im Basisimage. Bearbeiten Sie **Dockerfile-base**, indem Sie die in `NODE_VERSION` definierte Versionsnummer um ein „a“ ergänzen:

```dockerfile
ENV NODE_VERSION 15.2.1a
```

Führen Sie eine Schnellaufgabe aus, um das geänderte Basisimage zu erstellen. Notieren Sie sich die **Ausführungs-ID** aus der Ausgabe.

```azurecli
az acr build --registry $ACR_NAME --image baseimages/node:15-alpine --file Dockerfile-base .
```

Sobald die ACR-Aufgabe das neue Basisimage nach Abschluss des Buildvorgangs an Ihre Registrierung gepusht hat, wird ein Buildvorgang für das Anwendungsimage ausgelöst. Es dauert möglicherweise einen Moment, bis die zuvor erstellte Aufgabe den Buildvorgang für das Anwendungsimage auslöst, da das neue und gepushte Basisimage erkannt werden muss.

## <a name="list-updated-build"></a>Auflisten des aktualisierten Builds

Listen Sie nach der Aktualisierung des Basisimages erneut Ihre Aufgabenausführungen auf, und vergleichen Sie sie mit der vorherigen Liste. Sollte sich die Ausgabe nicht unterscheiden, führen Sie den Befehl in regelmäßigen Abständen erneut aus, bis die neue Aufgabenausführung in der Liste angezeigt wird.

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

Die Ausgabe sieht in etwa wie folgt aus. Als „TRIGGER“ (Auslöser) für den zuletzt ausgeführten Build sollte „Image Update“ (Imageaktualisierung) angegeben sein, was bedeutet, dass die Aufgabe durch die Schnellaufgabe des Basisimages ausgelöst wurde.

```output
Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
cav       example2        linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:55
cau       example1        linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:40
cat       taskhelloworld  linux       Succeeded  Manual        2020-11-20T23:07:29Z  00:00:27
```

Wenn Sie im folgenden optionalen Schritt den neu erstellten Container ausführen möchten, um die aktualisierte Versionsnummer anzuzeigen, notieren Sie sich den Wert unter **RUN ID** für den durch die Imageaktualisierung ausgelösten Buildvorgang („ca11“ in der obigen Ausgabe).

### <a name="optional-run-newly-built-image"></a>Optional: Ausführen des neu erstellten Images

Wenn Sie lokal (also nicht in Cloud Shell) arbeiten und Docker installiert ist, können Sie nach Abschluss des Buildvorgangs das neue Anwendungsimage ausführen. Ersetzen Sie `<run-id>` durch die Ausführungs-ID aus dem vorherigen Schritt. Bei Verwendung von Cloud Shell können Sie diesen Abschnitt überspringen. (Cloud-Shell unterstützt `docker run` nicht.)

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Navigieren Sie in Ihrem Browser zu http://localhost:8081. Daraufhin sollte die Webseite mit der aktualisierten Node.js-Versionsnummer (mit hinzugefügtem „a“) angezeigt werden:

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-02.png" alt-text="Screenshot: Aktualisierte Beispielanwendung im Browser":::


Beachten Sie, dass Sie Ihr **Basisimage** mit einer neuen Versionsnummer aktualisiert haben und das zuletzt erstellte **Anwendungsimage** die neue Version anzeigt. ACR Tasks hat die Änderung aus dem Basisimage übernommen und Ihr Anwendungsimage automatisch neu erstellt.

Führen Sie den folgenden Befehl aus, um den Container zu beenden und zu entfernen:

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie mithilfe einer Aufgabe automatisch Buildvorgänge für Containerimages auslösen, wenn das Basisimage des Images aktualisiert wurde. Im nächsten Tutorial erfahren Sie, wie Sie Aufgaben nach einem definierten Zeitplan ausführen.

> [!div class="nextstepaction"]
> [Ausführen eines ACR Tasks nach einem definierten Zeitplan](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-node]: https://hub.docker.com/_/node/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr
