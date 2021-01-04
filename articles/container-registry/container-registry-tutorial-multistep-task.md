---
title: 'Tutorial: ACR-Aufgabe mit mehreren Schritten'
description: In diesem Tutorial erfahren Sie, wie Sie einen Azure Container Registry Task so konfigurieren, dass automatisch ein mehrstufiger Workflow ausgelöst wird, um Containerimages in der Cloud zu erstellen, auszuführen und zu pushen, wenn Sie Quellcode in einem Git-Repository committen.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: fac409e9acc14048068c0f46ffb2b64cc69582ef
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029988"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Tutorial: Ausführen eines mehrstufigen Containerworkflows in der Cloud, wenn Sie Quellcode committen

Zusätzlich zu einer [Schnellaufgabe](container-registry-tutorial-quick-task.md) unterstützt ACR Tasks mehrstufige, auf mehreren Containern basierende Workflows, die automatisch ausgelöst werden können, wenn Sie Quellcode in einem Git-Repository committen. 

In diesem Tutorial erfahren Sie, wie Sie mithilfe von YAML-Beispieldateien mehrstufige Aufgaben definieren, die beim Committen des Quellcodes mindestens ein Containerimage erstellen, ausführen und in eine Registrierung pushen. Informationen zum Erstellen einer Aufgabe, die nur ein einzelnes Image automatisiert, das auf Codecommit basiert, finden Sie unter [Tutorial: Automatisieren von Containerimagebuilds in der Cloud, wenn Sie Quellcode committen](container-registry-tutorial-build-task.md). Eine Übersicht über ACR Tasks finden Sie unter [Automatisieren von Betriebssystem- und Frameworkpatches mit ACR Tasks](container-registry-tasks-overview.md).

Dieses Tutorial umfasst folgende Punkte:

> [!div class="checklist"]
> * Definieren einer mehrstufigen Aufgabe mithilfe einer YAML-Datei
> * Erstellt eine Aufgabe.
> * Optionales Hinzufügen von Anmeldeinformationen zur Aufgabe, um den Zugriff auf eine andere Registrierung zu ermöglichen
> * Testen der Aufgabe
> * Anzeigen des Aufgabenstatus
> * Auslösen der Aufgabe durch Committen von Code

In diesem Tutorial wird davon ausgegangen, dass Sie bereits die Schritte des [vorherigen Tutorials](container-registry-tutorial-quick-task.md) ausgeführt haben. Führen Sie bei Bedarf die Schritte im Abschnitt [Voraussetzungen](container-registry-tutorial-quick-task.md#prerequisites) des vorherigen Tutorials aus, bevor Sie mit diesem Tutorial fortfahren.

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]
[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-a-multi-step-task"></a>Erstellen einer mehrstufigen Aufgabe

Nachdem Sie die erforderlichen Schritte abgeschlossen haben, um ACR Tasks das Lesen des Commitstatus und das Erstellen von Webhooks in einem Repository zu ermöglichen, erstellen Sie eine mehrstufige Aufgabe, die das Erstellen, Ausführen und Pushen eines Containerimages auslöst.

### <a name="yaml-file"></a>YAML-Datei

Die Schritte für eine mehrstufige Aufgabe definieren Sie in einer [YAML-Datei](container-registry-tasks-reference-yaml.md). Die erste beispielhafte mehrstufige Aufgabe für dieses Tutorial ist in der Datei `taskmulti.yaml` definiert, die sich im Stammverzeichnis des von Ihnen geklonten GitHub-Repositorys befindet:

```yml
version: v1.1.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

Diese mehrstufige Aufgabe führt Folgendes aus:

1. Führt einen `build`-Schritt aus, um ein Image aus der Dockerdatei im Arbeitsverzeichnis zu erstellen. Das Image ist auf die `Run.Registry`, die Registrierung, in der die Aufgabe ausgeführt wird, ausgerichtet und mit einer eindeutigen ACR Tasks-Ausführungs-ID versehen. 
1. Führt einen `cmd` Schritt aus, um das Image in einem temporären Container auszuführen. Dieses Beispiel startet einen zeitintensiven Container im Hintergrund, gibt die Container-ID zurück und beendet dann den Container. In einem realen Szenario können Sie Schritte einfügen, um den ausgeführten Container zu testen, um sicherzustellen, dass er ordnungsgemäß ausgeführt wird.
1. Verschiebt in einem `push` Schritt das erstellte Image per Push in die ausgeführte Registrierung.

### <a name="task-command"></a>Aufgabenbefehl

Füllen Sie zunächst die folgenden Shell-Umgebungsvariablen mit geeigneten Werten für Ihre Umgebung auf. Dieser Schritt ist zwar nicht zwingend erforderlich, vereinfacht aber das Ausführen der mehrzeiligen Azure CLI-Befehle in diesem Tutorial. Wenn Sie diese Umgebungsvariablen nicht angeben, müssen Sie sie später jedes Mal die einzelnen Werte manuell ersetzen, wenn sie in einem der Beispielbefehle vorkommen.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Führen Sie als Nächstes den folgenden Befehl vom Typ [az acr task create][az-acr-task-create] aus, um die Aufgabe zu erstellen:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Durch diese Aufgabe führt ACR Tasks jedes Mal, wenn Code in der Verzweigung *master* des durch `--context` angegebenen Repositorys committet wird, die mehrstufige Aufgabe auf der Grundlage des Codes in dieser Verzweigung aus. Die von `--file` angegebene YAML-Datei aus dem Repositorystamm definiert die Schritte. 

Die Ausgabe einer erfolgreichen Ausführung des Befehls [az acr task create][az-acr-task-create] sieht in etwa wie folgt aus:

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2020-11-20T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
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
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
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

## <a name="test-the-multi-step-workflow"></a>Testen des mehrstufigen Workflows

Lösen Sie zum Testen der mehrstufigen Aufgabe manuell die Aufgabe aus, indem Sie den Befehl [az acr task run][az-acr-task-run] ausführen:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Der Befehl `az acr task run` streamt die Protokollausgabe standardmäßig an Ihre Konsole, wenn Sie den Befehl ausführen. Die Ausgabe zeigt den Fortschritt der Ausführung der einzelnen Aufgabenschritte. Die nachfolgende Ausgabe ist zusammengefasst, um die wichtigsten Schritte darzustellen.

```output
Queued a run with ID: cab
Waiting for an agent...
2020/11/20 00:03:31 Downloading source code...
2020/11/20 00:03:33 Finished downloading source code
2020/11/20 00:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2020/11/20 00:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 00:03:34 Successfully set up Docker network: acb_default_network
2020/11/20 00:03:34 Setting up Docker configuration...
2020/11/20 00:03:34 Successfully set up Docker configuration
2020/11/20 00:03:34 Logging in to registry: myregistry.azurecr.io
2020/11/20 00:03:35 Successfully logged into myregistry.azurecr.io
2020/11/20 00:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:35 Scanning for dependencies...
2020/11/20 00:03:36 Successfully scanned dependencies
2020/11/20 00:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB
[...]
Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2020/11/20 00:03:43 Successfully executed container: acb_step_0
2020/11/20 00:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2020/11/20 00:03:44 Successfully executed container: acb_step_1
2020/11/20 00:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1
[...]
2020/11/20 00:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2020/11/20 00:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2020/11/20 00:03:46 Populating digests for step ID: acb_step_0...
2020/11/20 00:03:47 Successfully populated digests for step ID: acb_step_0
2020/11/20 00:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2020/11/20 00:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2020/11/20 00:03:47 The following dependencies were found:
2020/11/20 00:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cab was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>Auslösen eines Buildvorgangs durch ein Commit

Nachdem Sie die Aufgabe manuell ausgeführt haben, um sie zu testen, können Sie als Nächstes eine Quellcodeänderung vornehmen, um die Aufgabe automatisch auszulösen.

Vergewissern Sie sich zunächst, dass Sie sich im Verzeichnis mit dem lokalen Klon des [Repositorys][sample-repo] befinden:

```console
cd acr-build-helloworld-node
```

Führen Sie als Nächstes die folgenden Befehle aus, um eine neue Datei zu erstellen, zu committen und an Ihr Fork des Repositorys auf GitHub zu pushen:

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Wenn Sie den Befehl `git push` ausführen, werden Sie unter Umständen zur Eingabe Ihrer GitHub-Anmeldeinformationen aufgefordert. Geben Sie Ihren GitHub-Benutzernamen und als Kennwort das persönliche Zugriffstoken (Personal Access Token, PAT) ein, das Sie zuvor erstellt haben.

```azurecli-interactive
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Nachdem Sie einen Commit an Ihr Repository gepusht haben, wird der von ACR Tasks erstellte Webhook ausgelöst und initiiert die Aufgabe in Azure Container Registry. Zeigen Sie die Protokolle für die derzeit ausgeführte Aufgabe an, um den Buildstatus zu überprüfen und zu überwachen:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

Die Ausgabe sieht in etwa wie im folgenden Beispiel aus und zeigt die derzeit (oder zuletzt) ausgeführte Aufgabe:

```output
Showing logs of the last created run.
Run ID: cad

[...]

Run ID: cad was successful after 37s
```

## <a name="list-builds"></a>Auflisten der Builds

Führen Sie den Befehl [az acr task list-runs][az-acr-task-list-runs] aus, um eine Liste mit den Aufgabenausführungen anzuzeigen, die ACR Tasks für Ihre Registrierung abgeschlossen hat:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Die Ausgabe des Befehls sollte in etwa wie im folgenden Beispiel aussehen. Die Ausführungen durch ACR Tasks werden angezeigt, und die Spalte „TRIGGER“ (Auslöser) enthält „Git Commit“ für die zuletzt ausgeführte Aufgabe:

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
cad       example1        linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:35
cac       taskhelloworld  linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:22
cab       example1        linux       Succeeded  Manual     2020-11-20T00:18:36Z  00:00:47
```

## <a name="create-a-multi-registry-multi-step-task"></a>Erstellen einer mehrstufigen Aufgabe für mehrere Registrierungen

ACR Tasks verfügt standardmäßig über die Berechtigung, Images aus der Registry, in der die Aufgabe ausgeführt wird, per Push oder Pull zu verschieben. Möglicherweise möchten Sie eine mehrstufige Aufgabe ausführen, die zusätzlich zur ausgeführten Registrierung auf eine oder mehrere Registrierungen ausgerichtet ist. Möglicherweise müssen Sie z. B. Images in einer Registrierung erstellen und Images mit unterschiedlichen Tags in einer zweiten Registrierung speichern, auf die von einem Produktionssystem zugegriffen wird. In diesem Beispiel wird gezeigt, wie Sie eine solche Aufgabe erstellen und Anmeldeinformationen für eine andere Registrierung bereitstellen.

Wenn Sie noch nicht über eine zweite Registrierung verfügen, erstellen Sie eine für dieses Beispiel. Informationen zum Erstellen einer Registrierung finden Sie bei Bedarf im [vorherigen Tutorial](container-registry-tutorial-quick-task.md) und in der Schnellstartanleitung [Erstellen einer Containerregistrierung mit der Azure-Befehlszeilenschnittstelle](container-registry-get-started-azure-cli.md).

Um die Aufgabe zu erstellen, benötigen Sie den Namen des Anmeldeservers der Registrierung, der die Form *mycontainerregistrydate.azurecr.io* aufweist (alles Kleinbuchstaben). In diesem Beispiel verwenden Sie die zweite Registrierung, um Images zu speichern, die nach dem Erstellungsdatum markiert sind.

### <a name="yaml-file"></a>YAML-Datei

Die zweite beispielhafte mehrstufige Aufgabe für dieses Tutorial ist in der Datei `taskmulti-multiregistry.yaml` definiert, die sich im Stammverzeichnis des von Ihnen geklonten GitHub-Repositorys befindet:

```yml
version: v1.1.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

Diese mehrstufige Aufgabe führt Folgendes aus:

1. Führt zwei `build`-Schritte aus, um ein Image aus der Dockerdatei im Arbeitsverzeichnis zu erstellen:
    * Der erste Schritt ist auf die `Run.Registry`, die Registrierung, in der die Aufgabe ausgeführt wird, ausgerichtet und mit einer ACR Tasks-Ausführungs-ID versehen. 
    * Der zweite Schritt ist auf die Registrierung ausgerichtet, die durch den Wert von `regDate` identifiziert wird, den Sie beim Erstellen der Aufgabe festlegen (oder durch eine externe `values.yaml`-Datei, die an `az acr task create` übergeben wird). Dieses Image ist mit dem Ausführungsdatum markiert.
1. Führt einen `cmd`-Schritt aus, um einen der erstellten Container auszuführen. Dieses Beispiel startet einen zeitintensiven Container im Hintergrund, gibt die Container-ID zurück und beendet dann den Container. In einem realen Szenario können Sie einen ausgeführten Container testen, um sicherzustellen, dass er ordnungsgemäß ausgeführt wird.
1. In einem `push`-Schritt werden die erstellten Images verschoben. Das erste Image in die ausgeführte Registrierung, das zweite in die durch `regDate` identifizierte Registrierung.

### <a name="task-command"></a>Aufgabenbefehl

Erstellen Sie die Aufgabe unter Verwendung der zuvor definierten Shellumgebungsvariablen, indem Sie den folgenden Befehl [az acr task create][az-acr-task-create] ausführen. Ersetzen Sie den Namen Ihrer Registrierung durch *mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Hinzufügen von Anmeldeinformationen für eine Aufgabe

Um Images in die durch den Wert von `regDate` identifizierte Registrierung zu verschieben, verwenden Sie den Befehl [az acr task credential add][az-acr-task-credential-add], um der Aufgabe Anmeldeinformationen für diese Registrierung hinzuzufügen.

Für dieses Beispiel wird empfohlen, einen [Dienstprinzipal](container-registry-auth-service-principal.md) mit Zugriff auf die Registrierung zu erstellen, die auf die Rolle *AcrPush* beschränkt ist, damit er über Berechtigungen zum Pushen von Images verfügt. Informationen zum Erstellen des Dienstprinzipals finden Sie in diesem [Azure CLI-Skript](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

Übergeben Sie die ID und das Kennwort der Dienstprinzipalanwendung im folgenden `az acr task credential add`-Befehl: Ersetzen Sie unbedingt den Anmeldeservernamen *mycontainerregistrydate* durch den Namen Ihrer zweiten Registrierung:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

Die CLI gibt den Namen des von Ihnen hinzugefügten Anmeldeservers der Registrierung zurück.

### <a name="test-the-multi-step-workflow"></a>Testen des mehrstufigen Workflows

Wie im vorherigen Beispiel zum Testen der mehrstufigen Aufgabe lösen Sie manuell die Aufgabe aus, indem Sie den Befehl [az acr task run][az-acr-task-run] ausführen: Um die Aufgabe mit einem Commit in das Git-Repository auszulösen, lesen Sie den Abschnitt [Auslösen eines Buildvorgangs durch ein Commit](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Der Befehl `az acr task run` streamt die Protokollausgabe standardmäßig an Ihre Konsole, wenn Sie den Befehl ausführen. Wie zuvor zeigt die Ausgabe den Fortschritt der Ausführung der einzelnen Aufgabenschritte an. Die Ausgabe ist zusammengefasst, um die wichtigsten Schritte darzustellen.

Ausgabe:

```output
Queued a run with ID: cf1g
Waiting for an agent...
2020/11/20 04:33:39 Downloading source code...
2020/11/20 04:33:41 Finished downloading source code
2020/11/20 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2020/11/20 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 04:33:43 Successfully set up Docker network: acb_default_network
2020/11/20 04:33:43 Setting up Docker configuration...
2020/11/20 04:33:44 Successfully set up Docker configuration
2020/11/20 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2020/11/20 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2020/11/20 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2020/11/20 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2020/11/20 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:47 Scanning for dependencies...
2020/11/20 04:33:47 Successfully scanned dependencies
2020/11/20 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2020/11/20 04:33:55 Successfully executed container: acb_step_0
2020/11/20 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:55 Scanning for dependencies...
2020/11/20 04:33:56 Successfully scanned dependencies
2020/11/20 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2020/11/20 04:33:57 Successfully executed container: acb_step_1
2020/11/20 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2020/11/20 04:33:58 Successfully executed container: acb_step_2
2020/11/20 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:58 Launching container with name: acb_step_3
test
2020/11/20 04:34:09 Successfully executed container: acb_step_3
2020/11/20 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]
[...]
2020/11/20 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2020/11/20 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]
[...]
2020/11/20 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2020/11/20 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2020/11/20 04:34:19 Populating digests for step ID: acb_step_0...
2020/11/20 04:34:21 Successfully populated digests for step ID: acb_step_0
2020/11/20 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2020/11/20 04:34:21 Populating digests for step ID: acb_step_1...
2020/11/20 04:34:23 Successfully populated digests for step ID: acb_step_1
2020/11/20 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2020/11/20 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2020/11/20 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2020/11/20 04:34:23 The following dependencies were found:
2020/11/20 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie mehrstufige, containerbasierte Aufgaben erstellen, die automatisch ausgelöst werden, wenn Sie Quellcode in ein Git-Repository committen. Weitere Informationen zu den erweiterten Features von mehrstufigen Aufgaben, einschließlich der parallelen und abhängigen Ausführung von Schritten, finden Sie in der [ACR Tasks: YAML-Referenz](container-registry-tasks-reference-yaml.md). Im nächsten Tutorial erfahren Sie, wie Sie Aufgaben erstellen, die Buildvorgänge auslösen, wenn das Basisimage eines Containerimages aktualisiert wird.

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
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
