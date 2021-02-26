---
title: Pushen und Pullen von Containerimages
description: Informationen zum Pushen und Pullen von Docker-Images an eine private Containerregistrierung in Azure mit der Docker CLI
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 83ef385313b035f5e5d7d993e7948725906c75a7
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987769"
---
# <a name="push-your-first-image-to-your-azure-container-registry-using-the-docker-cli"></a>Pushen des ersten Image in eine Azure-Containerregistrierung mit der Docker CLI

Eine Azure-Containerregistrierung dient zum Speichern und Verwalten privater Containerimages und anderer Artefakte, ähnlich wie in [Docker Hub](https://hub.docker.com/) öffentliche Docker-Containerimages gespeichert werden. Sie können die [Docker-Befehlszeilenschnittstelle](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) für [Anmeldung](https://docs.docker.com/engine/reference/commandline/login/), [Push](https://docs.docker.com/engine/reference/commandline/push/), [Pull](https://docs.docker.com/engine/reference/commandline/pull/) und andere Vorgänge für Containerimages in Ihrer Containerregistrierung verwenden.

In den folgenden Schritten laden Sie ein öffentliches [Nginx-Image](https://store.docker.com/images/nginx) herunter, kennzeichnen es für Ihre private Azure-Containerregistrierung und pushen es zuerst in Ihre Registrierung und pullen es dann aus der Registrierung.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Containerregistrierung**: Erstellen Sie in Ihrem Azure-Abonnement eine Containerregistrierung. Verwenden Sie beispielsweise das [Azure-Portal](container-registry-get-started-portal.md) oder die [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI** – Darüber hinaus muss Docker lokal installiert sein. Für Docker sind Pakete erhältlich, mit denen Docker auf einem [macOS][docker-mac]-, [Windows][docker-windows]- oder [Linux][docker-linux]-System problemlos konfiguriert werden kann.

## <a name="log-in-to-a-registry"></a>Anmelden an einer Registrierung

Es gibt [verschiedene Möglichkeiten für die Authentifizierung](container-registry-authentication.md) bei Ihrer privaten Containerregistrierung. Die empfohlene Methode bei Verwendung einer Befehlszeile ist der Azure CLI-Befehl [az acr login](/cli/azure/acr#az-acr-login). Um sich z. B. bei einer Registrierung mit dem Namen *myregistry* anzumelden, melden Sie sich bei der Azure CLI an und authentifizieren sich dann bei Ihrer Registrierung:

```azurecli
az login
az acr login --name myregistry
```

Sie können sich auch mit [docker login](https://docs.docker.com/engine/reference/commandline/login/) anmelden. Angenommen, Sie haben Ihrer Registrierung für ein Automatisierungsszenario [einen Dienstprinzipal zugewiesen](container-registry-authentication.md#service-principal). Wenn Sie den folgenden Befehl ausführen, geben Sie bei Aufforderung interaktiv die Haupt-AppID (Benutzername) und das Kennwort des Dienstes an. Best Practices zur Verwaltung von Anmeldeinformationen finden Sie in der Befehlsreferenz [Docker-Anmeldung](https://docs.docker.com/engine/reference/commandline/login/):

```
docker login myregistry.azurecr.io
```

Beide Befehle geben nach Abschluss `Login Succeeded` zurück.
> [!NOTE]
>* Zur schnelleren und komfortableren Anmeldung sollten Sie Visual Studio Code mit der Docker-Erweiterung verwenden.

> [!TIP]
> Geben Sie immer den vollqualifizierten Registrierungsnamen (nur Kleinbuchstaben) an, wenn Sie `docker login` verwenden und Images für die Pushübertragung in Ihre Registrierung kennzeichnen. In den Beispielen in diesem Artikel wird der vollqualifizierte Name *myregistry.azurecr.io* verwendet.

## <a name="pull-a-public-nginx-image"></a>Pullen eines öffentlichen Nginx-Images

Pullen Sie zunächst das öffentliche Nginx-Image auf Ihren lokalen Computer. In diesem Beispiel wird ein Image aus der Microsoft Container Registry gepullt.

```
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

## <a name="run-the-container-locally"></a>Lokales Ausführen des Containers

Führen Sie den folgenden [docker run](https://docs.docker.com/engine/reference/run/)-Befehl aus, um eine lokale Instanz des Nginx-Containers interaktiv (`-it`) auf Port 8080 zu starten. Das Argument `--rm` gibt an, dass der Container entfernt werden soll, wenn Sie ihn beenden.

```
docker run -it --rm -p 8080:80 mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Navigieren Sie zu `http://localhost:8080`, um die von NGINX bereitgestellte Standardwebseite im ausgeführten Container anzuzeigen. Eine Seite ähnlich der folgenden wird angezeigt:

![Nginx auf lokalem Computer](./media/container-registry-get-started-docker-cli/nginx.png)

Da Sie den Container interaktiv mit `-it` gestartet haben, können Sie nach dem Navigieren im Browser die Ausgabe des Nginx-Servers in der Befehlszeile sehen.

Drücken Sie zum Beenden und Entfernen des Containers die Tastenkombination `Control`+`C`.

## <a name="create-an-alias-of-the-image"></a>Erstellen eines Alias des Images

Verwenden Sie [docker tag](https://docs.docker.com/engine/reference/commandline/tag/), um einen Alias des Images mit vollqualifiziertem Pfad zur Registrierung zu erstellen. In diesem Beispiel wird der `samples`-Namespace angegeben, um den Stamm der Registrierung nicht zu überladen.

```
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx
```

Weitere Informationen zum Kennzeichnen mit Namespaces finden Sie unter [Bewährte Methoden für Azure Container Registry](container-registry-best-practices.md) im Abschnitt [Repositorynamespaces](container-registry-best-practices.md#repository-namespaces).

## <a name="push-the-image-to-your-registry"></a>Übertragen des Images per Push in Ihre Registrierung

Nachdem Sie das Image mit dem vollqualifizierten Pfad in Ihrer privaten Registrierung gekennzeichnet haben, können Sie es nun per Pushvorgang mit [docker push](https://docs.docker.com/engine/reference/commandline/push/) in die Registrierung übertragen:

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Übertragen des Images aus Ihrer Registrierung per Pull

Verwenden Sie den Befehl [docker pull](https://docs.docker.com/engine/reference/commandline/pull/), um das Image per Pullvorgang aus Ihrer Registrierung zu übertragen:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Starten des Nginx-Containers

Verwenden Sie den Befehl [docker run](https://docs.docker.com/engine/reference/run/), um das Image auszuführen, das Sie per Pullvorgang aus der Registrierung übertragen haben:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Navigieren Sie zu `http://localhost:8080`, um den ausgeführten Container anzuzeigen.

Drücken Sie zum Beenden und Entfernen des Containers die Tastenkombination `Control`+`C`.

## <a name="remove-the-image-optional"></a>Entfernen des Images (optional)

Wenn Sie das Nginx-Image nicht mehr benötigen, können Sie es mit dem Befehl [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) lokal löschen.

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Um Images aus Ihrer Azure-Containerregistrierung zu entfernen, können Sie den Azure CLI-Befehl [az acr repository delete](/cli/azure/acr/repository#az-acr-repository-delete) ausführen. Mit dem folgenden Befehl werden beispielsweise das durch das `samples/nginx:latest`-Tag referenzierte Manifest, alle eindeutigen Ebenendaten und alle anderen Tags gelöscht, die auf das Manifest verweisen.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit den Grundlagen vertraut gemacht haben, können Sie mit der Verwendung Ihrer Registrierung beginnen! Stellen Sie beispielsweise Containerimages aus Ihrer Registrierung bereit:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Installieren Sie optional die [Docker-Erweiterung für Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) und die [Azure-Kontoerweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) für die Verwendung mit Ihren Azure-Containerregistrierungen. In Visual Studio Code können Sie Pull- und Pushvorgänge für Images in einer Azure-Containerregistrierung oder auch ACR Tasks ausführen.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
