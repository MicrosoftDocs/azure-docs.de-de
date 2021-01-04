---
title: Entwickeln unter Azure Kubernetes Service (AKS) mit Helm
description: Verwenden Sie Helm mit AKS und Azure Container Registry, um Anwendungscontainer in einem Cluster zu packen und auszuführen.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: 2c4e39ae664c89e4f6d49f2941115e5a7d44cf7b
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929172"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Schnellstart: Entwickeln unter Azure Kubernetes Service (AKS) mit Helm

[Helm][helm] ist ein Open Source-Verpackungstool, das Ihnen dabei hilft, Kubernetes-Anwendungen zu installieren und ihren Lebenszyklus zu verwalten. Ähnlich wie Linux-Paket-Manager (z.B. *APT* und *Yum*) wird Helm zur Verwaltung von Kubernetes-Diagrammen verwendet, bei denen es sich um Pakete aus vorkonfigurierten Kubernetes-Ressourcen handelt.

In diesem Artikel erfahren Sie, wie Sie das Helm-Paket zum Verpacken verwenden und eine Anwendung unter AKS ausführen können. Weitere Informationen zum Installieren einer vorhandenen Anwendung mit Helm finden Sie unter [Installieren vorhandener Anwendungen mit Helm in AKS][helm-existing].

## <a name="prerequisites"></a>Voraussetzungen

* ein Azure-Abonnement. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen.
* Die [Azure CLI](/cli/azure/install-azure-cli) muss installiert sein.
* [Helm v3 muss installiert sein][helm-install].

## <a name="create-an-azure-container-registry"></a>Erstellen einer Azure-Containerregistrierung
Damit Sie Helm zum Ausführen Ihrer Anwendung in Ihrem AKS-Cluster verwenden können, benötigen Sie eine Azure Container Registry-Instanz (ACR), um darin Ihre Containerimages zu speichern. Im nachfolgenden Beispiel wird [az acr create][az-acr-create] verwendet, um mithilfe der SKU *Basic* eine ACR-Instanz mit dem Namen *MyHelmACR* in der Ressourcengruppe *MyResourceGroup* zu erstellen. Geben Sie Ihren eigenen eindeutigen Registrierungsnamen an. Der Registrierungsname muss innerhalb von Azure eindeutig sein und zwischen 5 und 50 alphanumerische Zeichen enthalten. Die *Basic*-SKU ist ein kostenoptimierter Einstiegspunkt für Entwicklungszwecke, der ein ausgewogenes Verhältnis von Speicher und Durchsatz bietet.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus: Notieren Sie sich den *loginServer*-Wert für Ihre ACR-Instanz, da Sie diesen in einem späteren Schritt benötigen werden. Im nachfolgenden Beispiel ist *myhelmacr.azurecr.io* der *loginServer*-Wert für *MyHelmACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="create-an-azure-kubernetes-service-cluster"></a>Erstellen eines Azure Kubernetes Service-Clusters

Erstellen Sie einen AKS-Cluster. Der folgende Befehl erstellt einen AKS-Cluster mit dem Namen MyAKS und fügt MyHelmACR an.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

Ihr AKS-Cluster benötigt Zugriff auf Ihre ACR-Instanz, um dieser die Containerimages über einen Pullvorgang entnehmen und diese ausführen zu können. Der obige Befehl gewährt dem *MyAKS*-Cluster außerdem Zugriff auf Ihre *MYHelmACR*-ACR.

## <a name="connect-to-your-aks-cluster"></a>Herstellen einer Verbindung mit dem AKS-Cluster

Zum Herstellen der Verbindung mit dem Kubernetes-Cluster auf Ihrem lokalen Computer verwenden Sie den Kubernetes-Befehlszeilenclient [kubectl][kubectl].

Wenn Sie Azure Cloud Shell verwenden, ist `kubectl` bereits installiert. Mit dem Befehl [az aks install-cli][] können Sie ihn auch lokal installieren:

```azurecli
az aks install-cli
```

Mit dem Befehl [az aks get-credentials][] können Sie `kubectl` für die Verbindungsherstellung mit Ihrem Kubernetes-Cluster konfigurieren. Im folgenden Beispiel werden Anmeldeinformationen für den AKS-Cluster namens *MyAKS* in der Ressourcengruppe *myResourceGroup* abgerufen:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>Herunterladen der Beispielanwendung

In diesem Schnellstart wird [eine Node.js-Beispielanwendung aus dem Azure Dev Spaces-Beispielrepository][example-nodejs] verwendet. Klonen Sie die Anwendung von GitHub, und navigieren Sie zum Verzeichnis `dev-spaces/samples/nodejs/getting-started/webfrontend`.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Erstellen eines Dockerfile

Verwenden Sie zum Erstellen eines neuen *Dockerfile* Folgendes:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Erstellen und Pushen der Beispielanwendung in die ACR

Verwenden Sie den Befehl [az acr build][az-acr-build], um ein Image mithilfe der vorherigen Dockerfile zu erstellen und in die Registrierung zu pushen. Der Punkt (`.`) am Ende des Befehls legt den Speicherort des Dockerfile fest (in diesem Fall: das aktuelle Verzeichnis).

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>Erstellen Ihres Helm-Diagramms

Generieren Sie Ihr Helm-Diagramm mit dem Befehl `helm create`.

```console
helm create webfrontend
```

Nehmen Sie die folgenden Aktualisierungen an *webfrontend/values.yaml* vor: Ersetzen Sie den Anmeldeserver („loginServer“) Ihrer Registrierung, den Sie in einem früheren Schritt notiert haben, z. B. *myhelmacr.azurecr.io*:

* Ändern Sie `image.repository` in `<loginServer>/webfrontend`.
* Ändern Sie `service.type` in `LoadBalancer`.

Beispiel:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: *myhelmacr.azurecr.io*/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

Aktualisieren Sie in *webfrontend/Chart.yaml* `appVersion` auf `v1`. Beispiel:

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Ausführen Ihres Helm-Diagramms

Verwenden Sie den Befehl `helm install`, um Ihre Anwendung mithilfe Ihres Helm-Diagramms zu installieren.

```console
helm install webfrontend webfrontend/
```

Es kann einige Minuten dauern, bis der Dienst eine öffentliche IP-Adresse zurückgibt. Verwenden Sie zum Überwachen des Status den Befehl `kubectl get service` mit dem *watch*-Parameter:

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Navigieren Sie in einem Browser mithilfe der `<EXTERNAL-IP>` zum Lastenausgleich Ihrer Anwendung, um die Beispielanwendung anzuzeigen.

## <a name="delete-the-cluster"></a>Löschen des Clusters

Wenn der Cluster nicht mehr benötigt wird, entfernen Sie über den Befehl [az group delete][az-group-delete] die Ressourcengruppe, den AKS-Cluster, die Containerregistrierung, die darin gespeicherten Containerimages sowie alle weiteren mit dem Cluster in Verbindung stehenden Ressourcen.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Wenn Sie den Cluster löschen, wird der vom AKS-Cluster verwendete Azure Active Directory-Dienstprinzipal nicht entfernt. Schritte zum Entfernen des Dienstprinzipals finden Sie unter den [Überlegungen zum AKS-Dienstprinzipal und dessen Löschung][sp-delete]. Wenn Sie eine verwaltete Identität verwendet haben, wird die Identität von der Plattform verwaltet und muss nicht entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Helm finden Sie in der Helm-Dokumentation.

> [!div class="nextstepaction"]
> [Helm-Dokumentation][helm-documentation]

[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
