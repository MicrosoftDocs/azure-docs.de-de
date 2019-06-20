---
title: Automatisieren von Erstellungs-, Test- und Patchvorgängen für Images mit Azure Container Registry-Tasks mit mehreren Schritten
description: Diese Artikel ist eine Einführung in Tasks mit mehreren Schritten, ein Feature von ACR Tasks in Azure Container Registry, das taskbasierte Workflows bereitstellt, um Containerimages in der Cloud zu erstellen, zu testen und zu patchen.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/28/2019
ms.author: danlep
ms.openlocfilehash: ac0e4e9019a35d3fdb35c0b7af9cb1289f4bceeb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60829581"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Ausführen von Erstellungs-, Test- und Patchtasks mit mehreren Schritten in ACR Tasks

Tasks mit mehreren Schritten erweitern die ACR Tasks-Funktion zum Erstellen und Pushen einzelner Images um auf mehreren Containern basierende Workflows mit mehreren Schritten. Verwenden Sie mehrstufige Aufgaben zum Erstellen und Pushen mehrerer Images nacheinander oder parallel. Führen Sie diese Images dann als Befehle in einer einzelnen Aufgabenausführung aus. Jeder Schritt definiert einen Build- oder Pushvorgang für ein Containerimage und kann auch die Ausführung eines Containers definieren. Jeder Schritt in einem Task mit mehreren Schritten verwendet einen Container als Ausführungsumgebung.

> [!IMPORTANT]
> Wenn Sie in der Vorschauversion bereits Aufgaben mit dem `az acr build-task`-Befehl erstellt haben, müssen diese Aufgaben mit dem Befehl [az acr task][az-acr-task] neu erstellt werden.

Sie können z.B. eine Aufgabe mit Schritten ausführen, die die folgende Logik automatisieren:

1. Erstellen eines Webanwendungsimages
1. Ausführen des Webanwendungscontainers
1. Erstellen eines Webanwendungs-Testimages
1. Ausführen des Webanwendungs-Testcontainers, der Tests für den aktuell ausgeführten Anwendungscontainer durchführt
1. Wenn die Tests erfolgreich sind: Erstellen eines Helm-Chart-Archivpakets
1. Ausführen von `helm upgrade` mithilfe des neuen Helm-Chart-Archivpakets

Alle Schritte werden in Azure ausgeführt – damit wird die Arbeit auf die Azure-Computeressourcen ausgelagert, und Sie müssen sich nicht um die Infrastrukturverwaltung kümmern. Sie zahlen neben Ihrer Azure-Containerregistrierung nur für die Ressourcen, die Sie tatsächlich verwenden. Informationen zu den Preisen finden Sie auf der Seite [Azure Container Registry – Preise][pricing] im Abschnitt **Container Build**.


## <a name="common-task-scenarios"></a>Gängige Tasks

Aufgaben mit mehreren Schritten ermöglichen Szenarien wie die folgende Logik:

* Erstellen, Taggen und Pushen von einem oder mehreren Containerimages, seriell oder parallel
* Ausführen von Komponententests und Code Coverage und Erfassen der Ergebnisse
* Ausführen und Erfassen von Funktionstests ACR-Aufgaben unterstützen die Ausführung mehrerer Container, die eine Reihe von Anforderungen ausführen.
* Taskbasierte Ausführung, einschließlich vor- und nachbereitenden Schritten eines Containerimage-Buildvorgangs
* Bereitstellen von einem oder mehreren Containern mit Ihrer bevorzugten Bereitstellungs-Engine in Ihrer Zielumgebung

## <a name="multi-step-task-definition"></a>Definition von Tasks mit mehreren Schritten

Ein aus mehreren Schritten bestehender Task in ACR Tasks ist als eine Reihe von Schritten innerhalb einer YAML-Datei definiert. Jeder Schritt kann Abhängigkeiten vom erfolgreichen Abschluss eines der vorherigen Schritte angeben. Folgende Arten von Taskschritten sind verfügbar:

* [`build`](container-registry-tasks-reference-yaml.md#build): Erstellen von einem oder mehreren Containerimages mithilfe der vertrauten `docker build`-Syntax, seriell oder parallel.
* [`push`](container-registry-tasks-reference-yaml.md#push): Pushen von erstellten Images in eine Containerregistrierung. Private Registrierungen wie Azure Container Registry werden ebenso unterstützt wie der öffentliche Docker-Hub.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Ausführen eines Containers in einer Weise, dass dieser als Funktion im Kontext des ausgeführten Tasks dienen kann. Sie können Parameter an den `[ENTRYPOINT]` des Containers übergeben und Eigenschaften wie „env“, „detach“ sowie weitere bekannte `docker run`-Parameter angeben. Der Schritttyp `cmd` ermöglicht Komponenten- und Funktionstests bei gleichzeitiger Ausführung von Containern.

Die folgenden Codeausschnitte zeigen, wie solche Taskschritttypen kombiniert werden. Tasks mit mehreren Schritten können einfach nur das Erstellen eines einzelnen Images aus einem Dockerfile und dessen Übertragung in Ihre Registrierung umfassen. Dabei wird eine ähnliche YAML-Datei wie diese verwendet:

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Sie können auch komplexer sein, z.B. wie bei dieser fiktiven Definition mit mehreren Schritten für Erstellen, Testen, Helm-Paket und Helm-Bereitstellung (Containerregistrierung und Helm-Repository nicht gezeigt):

```yml
version: v1.0.0
steps:
  - id: build-web
    build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
    when: ["-"]
  - id: build-tests
    build -t {{.Run.Registry}}/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["{{.Run.Registry}}/helloworld:{{.Run.ID}}"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
  - id: funcTests
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
    env: ["host=helloworld:80"]
  - cmd: {{.Run.Registry}}/functions/helm package --app-version {{.Run.ID}} -d ./helm ./helm/helloworld/
  - cmd: {{.Run.Registry}}/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image={{.Run.Registry}}/helloworld:{{.Run.ID}}
```

Unter [Taskbeispiele][task-examples] finden Sie vollständige YAML-Dateien und Dockerfiles mit mehreren Schritten für verschiedene Szenarien.

## <a name="run-a-sample-task"></a>Ausführen eines Beispieltasks

Tasks unterstützen sowohl die manuelle Ausführung – die so genannte „schnelle Ausführung“ – als auch die automatisierte Ausführung per Git-Commit oder Aktualisierung des Basisimages.

Um einen Task auszuführen, definieren Sie zunächst die Taskschritte in einer YAML-Datei, und führen dann den Azure CLI-Befehl [az acr run][az-acr-run] aus.

Hier finden Sie einen Azure CLI-Beispielbefehl, der einen Task mithilfe einer YAML-Datei für den Beispieltask ausführt. Die Schritte dieses Tasks erstellen und pushen ein Image. Aktualisieren Sie `\<acrName\>` mit dem Namen Ihrer eigenen Azure-Containerregistrierung, bevor Sie den Befehl ausführen.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Wenn Sie den Task ausführen, sollte die Ausgabe den Status jedes in der YAML-Datei definierten Schritts zeigen. In der folgenden Ausgabe werden die Schritt als `acb_step_0` und `acb_step_1` angezeigt.

```console
$ az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

Weitere Informationen zu automatisierten Builds per Git-Commit oder Update des Basisimages finden Sie in den Tutorialartikeln [Automatisieren von Buildvorgängen für Containerimages](container-registry-tutorial-build-task.md) und [Automatisieren von Buildvorgängen für Images nach der Aktualisierung des Basisimages](container-registry-tutorial-base-image-update.md).

## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie Referenzen und Beispiele für Tasks mit mehreren Schritten:

* [Taskreferenz](container-registry-tasks-reference-yaml.md): Arten von Taskschritten, zugehörige Eigenschaften und Verwendung
* [Taskbeispiele][task-examples]: `task.yaml`-Beispieldateien für verschiedene Szenarien – von ganz einfachen bis zu hochkomplexen
* [Cmd-Repository](https://github.com/AzureCR/cmd): Eine Sammlung von Containern als Befehle für ACR-Tasks.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task