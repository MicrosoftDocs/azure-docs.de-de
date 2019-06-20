---
title: 'Tutorial: Automatisieren von Buildvorgängen für Containerimages – Azure Container Registry Tasks'
description: In diesem Tutorial erfahren Sie, wie Sie einen Azure Container Registry Task so konfigurieren, dass automatisch Buildvorgänge für Containerimages in der Cloud ausgelöst werden, wenn Sie Quellcode in einem Git-Repository committen.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/04/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 7a9a1e3d3c92f43d19a75e7cd0e10b3fd395a9b5
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544993"
---
# <a name="tutorial-automate-container-image-builds-in-the-cloud-when-you-commit-source-code"></a>Tutorial: Automatisieren von Buildvorgängen für Containerimages in der Cloud beim Committen von Quellcode

Neben einer [Schnellaufgabe](container-registry-tutorial-quick-task.md) unterstützt ACR Tasks automatisierte Docker-Containerimagebuilds in der Cloud, wenn Sie Quellcode in ein Git-Repository committen.

In diesem Tutorial erstellt und pusht Ihre ACR-Aufgabe ein einzelnes Containerimage, das in einer Dockerfile-Datei angegeben ist, wenn Sie Quellcode in ein Git-Repository committen. Informationen zum Erstellen einer [mehrstufigen Aufgabe](container-registry-tasks-multi-step.md), die mithilfe einer YAML-Datei Schritte zum Erstellen, Pushen und optionalen Testen mehrerer Container beim Codecommit definiert, finden Sie unter [Tutorial: Ausführen eines mehrstufigen Containerworkflows in der Cloud, wenn Sie Quellcode committen](container-registry-tutorial-multistep-task.md). Eine Übersicht über ACR Tasks finden Sie unter [Automatisieren von Betriebssystem- und Frameworkpatches mit ACR Tasks](container-registry-tasks-overview.md).

Dieses Tutorial umfasst folgende Punkte:

> [!div class="checklist"]
> * Erstellt eine Aufgabe.
> * Testen der Aufgabe
> * Anzeigen des Aufgabenstatus
> * Auslösen der Aufgabe durch Committen von Code

In diesem Tutorial wird davon ausgegangen, dass Sie bereits die Schritte des [vorherigen Tutorials](container-registry-tutorial-quick-task.md) ausgeführt haben. Führen Sie bei Bedarf die Schritte im Abschnitt [Voraussetzungen](container-registry-tutorial-quick-task.md#prerequisites) des vorherigen Tutorials aus, bevor Sie mit diesem Tutorial fortfahren.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die Azure CLI lokal verwenden möchten, muss bei Ihnen mindestens die Azure CLI-Version **2.0.46** installiert sein, und Sie müssen mit [az login][az-login] angemeldet sein. Führen Sie `az --version` aus, um die Version zu finden. Informationen zur Installation und dem Upgrade der CLI finden Sie bei Bedarf unter [Installieren von Azure CLI][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-the-build-task"></a>Erstellen der Buildaufgabe

Nachdem Sie die erforderlichen Schritte abgeschlossen haben, um ACR Tasks das Lesen des Commitstatus und das Erstellen von Webhooks in einem Repository zu ermöglichen, können Sie eine Aufgabe erstellen, die im Falle eines Commits im Repository einen Containerimage-Buildvorgang auslöst.

Füllen Sie zunächst die folgenden Shell-Umgebungsvariablen mit geeigneten Werten für Ihre Umgebung auf. Dieser Schritt ist zwar nicht zwingend erforderlich, vereinfacht aber das Ausführen der mehrzeiligen Azure CLI-Befehle in diesem Tutorial. Wenn Sie diese Umgebungsvariablen nicht angeben, müssen Sie sie später jedes Mal die einzelnen Werte manuell ersetzen, wenn sie in einem der Beispielbefehle vorkommen.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Führen Sie als Nächstes den folgenden Befehl vom Typ [az acr task create][az-acr-task-create] aus, um die Aufgabe zu erstellen:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file Dockerfile \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> Wenn Sie in der Vorschauversion bereits Aufgaben mit dem `az acr build-task`-Befehl erstellt haben, müssen diese Aufgaben mit dem Befehl [az acr task][az-acr-task] neu erstellt werden.

Durch diese Aufgabe erstellt ACR Tasks jedes Mal, wenn Code in der Verzweigung *master* des durch `--context` angegebenen Repositorys committet wird, das Containerimage auf der Grundlage des Codes in dieser Verzweigung. Das von `--file` angegebene Dockerfile aus dem Repositorystamm wird verwendet, um das Image zu erstellen. Das Argument `--image` gibt für den Versionsteil des Imagetags den parametrisierten Wert `{{.Run.ID}}` an, um sicherzustellen, dass das erstellte Image einem bestimmten Build entspricht und eindeutig gekennzeichnet ist.

Die Ausgabe einer erfolgreichen Ausführung des Befehls [az acr task create][az-acr-task-create] sieht in etwa wie folgt aus:

```console
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2018-09-14T22:42:32.972298+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskhelloworld",
  "location": "westcentralus",
  "name": "taskhelloworld",
  "platform": {
    "architecture": "amd64",
    "os": "Linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myregistry",
  "status": "Enabled",
  "step": {
    "arguments": [],
    "baseImageDependencies": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node",
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Run.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "type": "Docker"
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
          "sourceControlAuthProperties": null,
          "sourceControlType": "GitHub"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-build-task"></a>Testen der Buildaufgabe

Sie verfügen nun über eine Aufgabe, die Ihren Build definiert. Lösen Sie zum Testen der Buildpipeline manuell einen Buildvorgang aus, indem Sie den Befehl [az acr task run][az-acr-task-run] ausführen:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Der Befehl `az acr task run` streamt die Protokollausgabe standardmäßig an Ihre Konsole, wenn Sie den Befehl ausführen.

```console
$ az acr task run --registry $ACR_NAME --name taskhelloworld

2018/09/17 22:51:00 Using acb_vol_9ee1f28c-4fd4-43c8-a651-f0ed027bbf0e as the home volume
2018/09/17 22:51:00 Setting up Docker configuration...
2018/09/17 22:51:02 Successfully set up Docker configuration
2018/09/17 22:51:02 Logging in to registry: myregistry.azurecr.io
2018/09/17 22:51:03 Successfully logged in
2018/09/17 22:51:03 Executing step: build
2018/09/17 22:51:03 Obtaining source code and scanning for dependencies...
2018/09/17 22:51:05 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  23.04kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:9-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 5f574fcf5816
Step 3/5 : RUN cd /src && npm install
 ---> Running in b1bca3b5f4fc
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.078s
Removing intermediate container b1bca3b5f4fc
 ---> 44457db20dac
Step 4/5 : EXPOSE 80
 ---> Running in 9e6f63ec612f
Removing intermediate container 9e6f63ec612f
 ---> 74c3e8ea0d98
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 7382eea2a56a
Removing intermediate container 7382eea2a56a
 ---> e33cd684027b
Successfully built e33cd684027b
Successfully tagged myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:11 Executing step: push
2018/09/17 22:51:11 Pushing image: myregistry.azurecr.io/helloworld:da2, attempt 1
The push refers to repository [myregistry.azurecr.io/helloworld]
4a853682c993: Preparing
[...]
4a853682c993: Pushed
[...]
da2: digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419 size: 1366
2018/09/17 22:51:21 Successfully pushed image: myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:21 Step id: build marked as successful (elapsed time in seconds: 7.198937)
2018/09/17 22:51:21 Populating digests for step id: build...
2018/09/17 22:51:22 Successfully populated digests for step id: build
2018/09/17 22:51:22 Step id: push marked as successful (elapsed time in seconds: 10.180456)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloworld
    tag: da2
    digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 68cdf2a37cdae0873b8e2f1c4d80ca60541029bf


Run ID: da2 was successful after 27s
```

## <a name="trigger-a-build-with-a-commit"></a>Auslösen eines Buildvorgangs durch ein Commit

Nachdem Sie die Aufgabe manuell ausgeführt haben, um sie zu testen, können Sie als Nächstes eine Quellcodeänderung vornehmen, um die Aufgabe automatisch auszulösen.

Vergewissern Sie sich zunächst, dass Sie sich im Verzeichnis mit dem lokalen Klon des [Repositorys][sample-repo] befinden:

```azurecli-interactive
cd acr-build-helloworld-node
```

Führen Sie als Nächstes die folgenden Befehle aus, um eine neue Datei zu erstellen, zu committen und an Ihr Fork des Repositorys auf GitHub zu pushen:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Wenn Sie den Befehl `git push` ausführen, werden Sie unter Umständen zur Eingabe Ihrer GitHub-Anmeldeinformationen aufgefordert. Geben Sie Ihren GitHub-Benutzernamen und als Kennwort das persönliche Zugriffstoken (Personal Access Token, PAT) ein, das Sie zuvor erstellt haben.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Nachdem Sie einen Commit an Ihr Repository gepusht haben, wird der von ACR Tasks erstellte Webhook ausgelöst und initiiert einen Bildvorgang in Azure Container Registry. Zeigen Sie die Protokolle für die derzeit ausgeführte Aufgabe an, um den Buildstatus zu überprüfen und zu überwachen:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

Die Ausgabe sieht in etwa wie im folgenden Beispiel aus und zeigt die derzeit (oder zuletzt) ausgeführte Aufgabe:

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: da4

[...]

Run ID: da4 was successful after 38s
```

## <a name="list-builds"></a>Auflisten der Builds

Führen Sie den Befehl [az acr task list-runs][az-acr-task-list-runs] aus, um eine Liste mit den Aufgabenausführungen anzuzeigen, die ACR Tasks für Ihre Registrierung abgeschlossen hat:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Die Ausgabe des Befehls sollte in etwa wie im folgenden Beispiel aussehen. Die Ausführungen durch ACR Tasks werden angezeigt, und die Spalte „TRIGGER“ (Auslöser) enthält „Git Commit“ für die zuletzt ausgeführte Aufgabe:

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie mithilfe einer Aufgabe automatisch Buildvorgänge für Containerimages auslösen, wenn Sie Quellcode in einem Git-Repository committen. Im nächsten Tutorial erfahren Sie, wie Sie Aufgaben erstellen, die Buildvorgänge auslösen, wenn das Basisimage eines Containerimages aktualisiert wird.

> [!div class="nextstepaction"]
> [Automatisieren von Buildvorgängen für Images nach der Aktualisierung des Basisimages mit Azure Container Registry Build](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-login]: /cli/azure/reference-index#az-login



