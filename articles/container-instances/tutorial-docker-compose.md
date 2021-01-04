---
title: 'Tutorial: Verwenden von Docker Compose zum Bereitstellen einer Gruppe mit mehreren Containern'
description: Verwenden Sie Docker Compose, um eine Anwendung mit mehreren Containern zu erstellen und auszuführen, und rufen Sie die Anwendung dann in Azure Container Instances auf.
ms.topic: tutorial
ms.date: 10/28/2020
ms.custom: ''
ms.openlocfilehash: a71ff438feaef555a85c33d818c287c64621d40d
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913839"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>Tutorial: Bereitstellen einer Gruppe mit mehreren Containern mithilfe von Docker Compose 

In diesem Tutorial verwenden Sie [Docker Compose](https://docs.docker.com/compose/), um eine Anwendung mit mehreren Containern lokal zu definieren und auszuführen, und stellen sie dann als [Containergruppe](container-instances-container-groups.md) in Azure Container Instances bereit. 

Führen Sie Container bei Bedarf in Azure Container Instances aus, wenn Sie cloudnative Apps mit Docker entwickeln und nahtlos von der lokalen Entwicklung zur Cloudbereitstellung wechseln möchten. Diese Funktion wird durch die [Integration zwischen Docker und Azure](https://docs.docker.com/engine/context/aci-integration/) ermöglicht. Sie können native Docker-Befehle verwenden, um entweder [eine einzelne Containerinstanz](quickstart-docker-cli.md) oder eine Gruppe mit mehreren Containern in Azure auszuführen.

> [!IMPORTANT]
> Nicht alle Funktionen von Azure Container Instances werden unterstützt. Geben Sie Feedback zur Integration von Docker und Azure ab, indem Sie im GitHub-Repository [Docker-ACI-Integration](https://github.com/docker/aci-integration-beta) ein Problem erstellen.

> [!TIP]
> Sie können die [Docker-Erweiterung für Visual Studio Code](https://aka.ms/VSCodeDocker) verwenden, um eine integrierte Erfahrung zum Entwickeln, Ausführen und Verwalten von Containern, Images und Kontexten zu erhalten.

In diesem Artikel führen Sie folgende Schritte aus:

> [!div class="checklist"]
> * Erstellen einer Azure Container Registry-Instanz
> * Klonen des Anwendungsquellcodes von GitHub
> * Verwenden von Docker Compose zum lokalen Erstellen und Ausführen einer Anwendung mit mehreren Containern
> * Übertragen des Anwendungsimages mithilfe von Push in Ihre Containerregistrierung
> * Erstellen eines Azure-Kontexts für Docker
> * Aufrufen der Anwendung in Azure Container Instances

## <a name="prerequisites"></a>Voraussetzungen

* **Azure CLI** : Auf Ihrem lokalen Computer muss die Azure CLI installiert sein. Empfohlen wird mindestens die Version 2.10.1. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

* **Docker Desktop** : Sie müssen Docker Desktop, Version 2.3.0.5 oder höher, verwenden, verfügbar für [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) oder [macOS](https://desktop.docker.com/mac/edge/Docker.dmg). Oder installieren Sie die [Docker ACI-Integrations-CLI für Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux).

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="get-application-code"></a>Abrufen von Anwendungscode

Die in diesem Tutorial verwendete Beispielanwendung ist eine einfache Abstimmungs-App. Die Anwendung besteht aus einer Front-End-Webkomponente und einer Back-End-Redis-Instanz. Die Webkomponente wird in ein benutzerdefiniertes Containerimage gepackt. Die Redis-Instanz verwendet ein nicht modifiziertes Image aus Docker Hub.

Verwenden Sie [Git](https://git-scm.com/downloads), um die Beispielanwendung in Ihrer Entwicklungsumgebung zu klonen:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Wechseln Sie in das geklonte Verzeichnis.

```console
cd azure-voting-app-redis
```

Im Verzeichnis befinden sich der Anwendungsquellcode und eine vorab erstellte Docker Compose-Datei namens „docker-compose.yaml“.

## <a name="modify-docker-compose-file"></a>Ändern der Docker Compose-Datei

Öffnen Sie „docker-compose.yaml“ in einem Text-Editor. Die Datei konfiguriert die Dienste `azure-vote-back` und `azure-vote-front`.

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "8080:80"
```

Nehmen Sie in der `azure-vote-front`-Konfiguration folgende zwei Änderungen vor:

1. Aktualisieren Sie die Eigenschaft `image` im `azure-vote-front`-Dienst. Präfigieren Sie den Imagenamen mit dem Anmeldeservernamen Ihrer Azure-Containerregistrierung, \<acrName\>.azure.io. Wenn Ihre Registrierung beispielsweise *myregistry* heißt, und der Name des Anmeldeservers *myregistry.azurecr.io* (alles Kleinbuchstaben) lautet, heißt die Image-Eigenschaft dann `myregistry.azurecr.io/azure-vote-front`.
1. Ändern Sie die `ports`-Zuordnung in `80:80`. Speichern Sie die Datei.

Die aktualisierte Datei sollte in etwa wie folgt aussehen:

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: myregistry.azurecr.io/azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Indem Sie diese Ersetzungen vornehmen, wird das `azure-vote-front`-Image, das Sie im nächsten Schritt erstellen, für Ihre Azure-Containerregistrierung gekennzeichnet, und das Image kann für die Ausführung in Azure Container Instances abgerufen werden.

> [!TIP]
> Für dieses Szenario müssen Sie keine Azure-Containerregistrierung verwenden. Beispielsweise könnten Sie ein privates Repository in Docker Hub auswählen, um Ihr Anwendungsimage zu hosten. Wenn Sie eine andere Registrierung auswählen, aktualisieren Sie die Image-Eigenschaft entsprechend.

## <a name="run-multi-container-application-locally"></a>Lokales Ausführen der Anwendung mit mehreren Containern

Führen Sie [docker-compos up](https://docs.docker.com/compose/reference/up/) aus, wobei die Beispieldatei `docker-compose.yaml` verwendet wird, um das Containerimage zu erstellen, das Redis-Image herunterzuladen und die Anwendung zu starten:

```console
docker-compose up --build -d
```

Verwenden Sie anschließend den Befehl [docker-images](https://docs.docker.com/engine/reference/commandline/images/), um die erstellten Images anzuzeigen. Drei Images wurden heruntergeladen oder erstellt. Das `azure-vote-front`-Image enthält die Front-End-Anwendung, die das `uwsgi-nginx-flask`-Image als Basis verwendet. Das `redis`-Image wird zum Starten einer Redis-Instanz verwendet.

```
$ docker images

REPOSITORY                                TAG        IMAGE ID            CREATED             SIZE
myregistry.azurecr.io/azure-vote-front    latest     9cc914e25834        40 seconds ago      944MB
mcr.microsoft.com/oss/bitnami/redis       6.0.8      3a54a920bb6c        4 weeks ago          103MB
tiangolo/uwsgi-nginx-flask                python3.6  788ca94b2313        9 months ago        9444MB
```

Führen Sie den Befehl [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) aus, um die ausgeführten Container anzuzeigen:

```
$ docker ps

CONTAINER ID        IMAGE                                      COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        myregistry.azurecr.io/azure-vote-front     "/entrypoint.sh /sta…"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:80->80/tcp   azure-vote-front
b62b47a7d313        mcr.microsoft.com/oss/bitnami/redis:6.0.8  "/opt/bitnami/script…"   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

Zum Anzeigen der ausgeführten Anwendungen geben Sie `http://localhost:80` in einem lokalen Webbrowser ein. Die Beispielanwendung wird wie im folgenden Beispiel geladen:

:::image type="content" source="media/tutorial-docker-compose/azure-vote.png" alt-text="Image der Voting-App":::

Nachdem Sie die lokale Anwendung ausprobiert haben, führen Sie [docker-compose down](https://docs.docker.com/compose/reference/down/) aus, um die Anwendung zu beenden und die Container zu entfernen.

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>Übertragen des Images in die Containerregistrierung per Pushvorgang

Um die Anwendung in Azure Container Instances bereitzustellen, müssen Sie das `azure-vote-front`-Image in Ihre Containerregistrierung pushen. Führen Sie [docker-compose push](https://docs.docker.com/compose/reference/push) aus, um das Image zu pushen:

```console
docker-compose push
```

Der Pushvorgang in die Registrierung kann ein paar Minuten dauern.

Um zu überprüfen, ob das Image in Ihrer Registrierung gespeichert wurde, führen Sie den Befehl [az acr repository show](/cli/azure/acr/repository#az-acr-repository-show) aus:

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>Bereitstellen der Anwendung in Azure Container Instances

Ändern Sie als Nächstes den ACI-Kontext. Nachfolgende Docker-Befehle werden in diesem Kontext ausgeführt.

```console
docker context use myacicontext
```

Führen Sie `docker compose up` aus, um die Anwendung in Azure Container Instances zu starten. Das `azure-vote-front`-Image wird aus ihrer Containerregistrierung abgerufen, und die Containergruppe wird in Azure Container Instances erstellt.

```console
docker compose up
```

> [!NOTE]
> Die derzeit in einem ACI-Kontext verfügbaren Docker Compose-Befehle sind `docker compose up` und `docker compose down`. In diesen Befehlen steht zwischen `docker` und `compose` kein Bindestrich.

In kurzer Zeit wird die Containergruppe bereitgestellt. Beispielausgabe:

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

Führen Sie `docker ps` aus, um die in Ausführung befindlichen Container sowie die IP-Adresse anzuzeigen, die der Containergruppe zugewiesen ist.

```console
docker ps
```

Beispielausgabe:

```
CONTAINER ID                           IMAGE                                         COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8                         Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                            Running             52.179.23.131:80->80/tcp
```

Zum Anzeigen der ausgeführten Anwendung in der Cloud geben Sie die angezeigte IP-Adresse in einem lokalen Webbrowser ein. Geben Sie für dieses Beispiel `52.179.23.131` ein. Die Beispielanwendung wird wie im folgenden Beispiel geladen:

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="Image der Voting-App":::

Um die Protokolle des Front-End-Containers anzuzeigen, führen Sie den Befehl [docker logs](https://docs.docker.com/engine/reference/commandline/logs) aus. Zum Beispiel:

```console
docker logs azurevotingappredis_azure-vote-front
```

Sie können auch das Azure-Portal oder andere Azure-Tools verwenden, um die Eigenschaften und den Status der von Ihnen bereitgestellten Containergruppe anzuzeigen.

Wenn Sie mit dem Ausprobieren der Anwendung fertig sind, beenden Sie die Anwendung und die Container mit `docker compose down`:

```console
docker compose down
```

Dieser Befehl löscht die Containergruppe in Azure Container Instances.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Docker Compose verwendet, um von der lokalen Ausführung einer Anwendung mit mehreren Containern zur Ausführung in Azure Container Instances zu wechseln. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Azure Container Registry-Instanz
> * Klonen des Anwendungsquellcodes von GitHub
> * Verwenden von Docker Compose zum lokalen Erstellen und Ausführen einer Anwendung mit mehreren Containern
> * Übertragen des Anwendungsimages mithilfe von Push in Ihre Containerregistrierung
> * Erstellen eines Azure-Kontexts für Docker
> * Aufrufen der Anwendung in Azure Container Instances

Sie können auch die [Docker-Erweiterung](https://aka.ms/VSCodeDocker) für Visual Studio Code verwenden, um eine integrierte Erfahrung zum Entwickeln, Ausführen und Verwalten von Containern, Images und Kontexten zu erhalten.

Wenn Sie weitere Features in Azure Container Instances nutzen möchten, verwenden Sie die Azure-Tools, um eine Gruppe mit mehreren Containern anzugeben. Informationen finden Sie beispielsweise in den Tutorials zum Bereitstellen einer Containergruppe mithilfe der Azure CLI mit einer [YAML-Datei](container-instances-multi-container-yaml.md) oder zum Bereitstellen mithilfe einer [Azure Resource Manager-Vorlage](container-instances-multi-container-group.md). 