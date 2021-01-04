---
title: Erstellen von Containerimages für Service Fabric in Azure
description: In diesem Tutorial erfahren Sie, wie Sie Containerimages für eine Service Fabric-Anwendung mit mehreren Containern erstellen.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 31b5f870465bc1dff9d6ff7827a4efed084bcf62
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92739061"
---
# <a name="tutorial-create-container-images-on-a-linux-service-fabric-cluster"></a>Tutorial: Erstellen von Containerimages für einen Linux-Service Fabric-Cluster

Dieses Tutorial ist der erste Teil einer Tutorialreihe, in der die Verwendung von Containern in einem Linux-basierten Service Fabric-Cluster veranschaulicht wird. In diesem Tutorial wird eine Anwendung mit mehreren Containern für die Verwendung mit Service Fabric vorbereitet. In den nachfolgenden Tutorials werden diese Images als Teil einer Service Fabric-Anwendung verwendet. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Klonen der Anwendungsquelle von GitHub
> * Erstellen eines Containerimages aus der Anwendungsquelle
> * Bereitstellen einer Azure Container Registry-Instanz (ACR)
> * Kennzeichnen eines Containerimages für ACR
> * Hochladen des Images in die ACR-Instanz

In dieser Tutorialreihe lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen von Containerimages für Service Fabric
> * [Erstellen und Ausführen einer Service Fabric-Anwendung mit Containern](service-fabric-tutorial-package-containers.md)
> * [Ausführen des Failovers und der Skalierung in Service Fabric](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>Voraussetzungen

* Einrichtung der Linux-Entwicklungsumgebung für Service Fabric. Befolgen Sie die [hier](service-fabric-get-started-linux.md) aufgeführten Anweisungen, um die Linux-Umgebung einzurichten.
* Für dieses Tutorial müssen Sie mindestens Version 2.0.4 der Azure-Befehlszeilenschnittstelle ausführen. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).
* Außerdem ist es erforderlich, dass Sie über ein Azure-Abonnement verfügen. Weitere Informationen zu einer kostenlosen Testversion finden Sie [hier](https://azure.microsoft.com/free/).

## <a name="get-application-code"></a>Abrufen von Anwendungscode

Die in diesem Tutorial verwendete Beispielanwendung ist eine Abstimmungs-App. Die Anwendung besteht aus einer Front-End-Webkomponente und einer Back-End-Redis-Instanz. Die Komponenten werden in Containerimages gepackt.

Verwenden Sie Git, um eine Kopie der Anwendung in Ihre Entwicklungsumgebung herunterzuladen.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

Die Projektmappe enthält zwei Ordner und die Datei „docker-compose.yml“. Der Ordner „azure-vote“ enthält den Python-Front-End-Dienst und die zur Imageerstellung verwendete Dockerfile. Das Verzeichnis „Voting“ enthält das Service Fabric-Anwendungspaket, das für den Cluster bereitgestellt wird. Diese Verzeichnisse enthalten die erforderlichen Ressourcen für dieses Tutorial.

## <a name="create-container-images"></a>Erstellen von Containerimages

Führen Sie im Verzeichnis **azure-vote** den folgenden Befehl aus, um das Image für die Front-End-Webkomponente zu erstellen. Dieser Befehl verwendet die Dockerfile-Datei in diesem Verzeichnis, um das Image zu erstellen.

```bash
docker build -t azure-vote-front .
```
> [!Note]
> Wenn die Berechtigung verweigert wird, befolgen Sie [diese](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user) Dokumentation zum Arbeiten mit Docker ohne sudo.

Die Befehlsausführung kann eine Weile dauern, da alle erforderlichen Abhängigkeiten vom Docker-Hub abgerufen werden müssen. Verwenden Sie anschließend den Befehl [docker-images](https://docs.docker.com/engine/reference/commandline/images/), um die erstellten Images anzuzeigen.

```bash
docker images
```

Beachten Sie, dass zwei Images heruntergeladen oder erstellt wurden. Das Image *azure-vote-front* enthält die Anwendung. Es wurde von einem *Python* -Image von Docker Hub abgeleitet.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Bereitstellen von Azure Container Registry

Führen Sie zunächst den Befehl **az login** aus, um sich bei Ihrem Azure-Konto anzumelden.

```azurecli
az login
```

Verwenden Sie dann den Befehl **az account** , um Ihr Abonnement für die Erstellung der Azure Container Registry-Instanz auszuwählen. Anstelle von <subscription_id> müssen Sie die Abonnement-ID Ihres Azure-Abonnements angeben.

```azurecli
az account set --subscription <subscription_id>
```

Für die Bereitstellung einer Azure Container Registry-Instanz benötigen Sie zunächst eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mithilfe des Befehls **az group create** eine Ressourcengruppe. In diesem Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *westus* erstellt.

```azurecli
az group create --name <myResourceGroup> --location westus
```

Erstellen Sie mit dem Befehl **az acr create** eine Azure Container Registry-Instanz. Ersetzen Sie \<acrName> durch den Namen der Containerregistrierung, die Sie für Ihr Abonnement erstellen möchten. Dieser muss aus alphanumerischen Zeichen bestehen und eindeutig sein.

```azurecli
az acr create --resource-group <myResourceGroup> --name <acrName> --sku Basic --admin-enabled true
```

Im weiteren Verlauf des Tutorials verwenden wir „acrName“ als Platzhalter für den von Ihnen gewählten Namen der Containerregistrierung. Bitte notieren Sie sich diesen Wert.

## <a name="sign-in-to-your-container-registry"></a>Anmelden bei Ihrer Containerregistrierung

Melden Sie sich zunächst bei Ihrer ACR-Instanz an, bevor Sie Images per Push in sie übertragen. Verwenden Sie den Befehl **az acr login** , um den Vorgang abzuschließen. Geben Sie den eindeutigen Namen an, den die Containerregistrierung bei ihrer Erstellung erhalten hat.

```azurecli
az acr login --name <acrName>
```

Nach Abschluss des Vorgangs wird eine Erfolgsmeldung zurückgegeben.

## <a name="tag-container-images"></a>Kennzeichnen von Containerimages

Jedes Containerimage muss mit dem loginServer-Namen der Registrierung gekennzeichnet werden. Dieses Tag wird beim Übertragen von Containerimages per Push in eine Imageregistrierung für das Routing verwendet.

Verwenden Sie den Befehl [docker images](https://docs.docker.com/engine/reference/commandline/images/), um eine Liste der aktuellen Images anzuzeigen.

```bash
docker images
```

Ausgabe:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

Führen Sie den folgenden Befehl aus, um den loginServer-Namen abzurufen:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Dadurch wird eine Tabelle mit den folgenden Ergebnissen ausgegeben. Dieses Ergebnis wird zur Kennzeichnung Ihres **azure-vote-front** -Images verwendet, bevor es im nächsten Schritt an die Containerregistrierung weitergeleitet wird.

```output
Result
------------------
<acrName>.azurecr.io
```

Kennzeichnen Sie nun das Image *azure-vote-front* mit dem „loginServer“-Namen der Containerregistrierung. Fügen Sie zudem `:v1` am Ende des Imagenamens hinzu. Dieses Tag gibt die Imageversion an.

```bash
docker tag azure-vote-front <acrName>.azurecr.io/azure-vote-front:v1
```

Führen Sie nach der Kennzeichnung den Befehl „docker images“ aus, um den Vorgang zu überprüfen.

Ausgabe:

```output
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf       23 minutes ago      708MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Übertragen von Images in die Registrierung per Push

Übertragen Sie das Image *azure-vote-front* per Push in die Registrierung. 

Verwenden Sie das folgende Beispiel, und ersetzen Sie den ACR-loginServer-Namen durch den loginServer-Namen aus Ihrer Umgebung.

```bash
docker push <acrName>.azurecr.io/azure-vote-front:v1
```

Die Ausführung der Docker-Push-Befehle nimmt einige Minuten in Anspruch.

## <a name="list-images-in-registry"></a>Auflisten von Images in der Registrierung

Führen Sie den Befehl [az acr repository list](/cli/azure/acr/repository) aus, um eine Liste der Images zurückzugeben, die per Push in Ihre Azure Container Registry-Instanz übertragen wurden. Aktualisieren Sie den Befehl mit dem Namen der ACR-Instanz.

```azurecli
az acr repository list --name <acrName> --output table
```

Ausgabe:

```output
Result
----------------
azure-vote-front
```

Nach Abschluss des Tutorials ist das Containerimage in einer privaten Azure Container Registry-Instanz gespeichert. Dieses Image wird in den nachfolgenden Tutorials aus ACR in einem Service Fabric-Cluster bereitgestellt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden eine Anwendung aus GitHub gepullt sowie Containerimages erstellt und in eine Registrierung gepusht. Die folgenden Schritte wurden durchgeführt:

> [!div class="checklist"]
> * Klonen der Anwendungsquelle von GitHub
> * Erstellen eines Containerimages aus der Anwendungsquelle
> * Bereitstellen einer Azure Container Registry-Instanz (ACR)
> * Kennzeichnen eines Containerimages für ACR
> * Hochladen des Images in die ACR-Instanz

Im nächsten Tutorial erfahren Sie, wie Sie Container mithilfe von Yeoman in eine Service Fabric-Anwendung packen.

> [!div class="nextstepaction"]
> [Packen und Bereitstellen von Containern als Service Fabric-Anwendung](service-fabric-tutorial-package-containers.md)
