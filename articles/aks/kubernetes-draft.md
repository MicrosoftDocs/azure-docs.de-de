---
title: Verwenden von Draft mit AKS und Azure Container Registry
description: Verwenden von Draft mit AKS und Azure Container Registry
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: zarhoads
ms.openlocfilehash: 462cfd6ec0a6b25f85dda0245dd4f5feed7cb712
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60465144"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Verwenden von Draft mit Azure Kubernetes Service (AKS)

Draft ist ein Open Source-Tool für das Packen und Bereitstellen von Anwendungscontainern in einem Kubernetes-Cluster. Dadurch können Sie sich auf den Entwicklungszyklus (die „innere Schleife“ der konzentrierten Entwicklung) konzentrieren. Draft wird verwendet, während der Code entwickelt wird, jedoch vor dem Commit an die Versionskontrolle. Mit Draft können Sie für Kubernetes schnell eine Anwendung erneut bereitstellen, wenn sich Änderungen am Code ergeben. Weitere Informationen zu Draft finden Sie in der [Draft-Dokumentation auf GitHub][draft-documentation].

In diesem Artikel wird veranschaulicht, wie Sie Draft mit einem Kubernetes-Cluster in AKS verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Zur Ausführung der Schritte in diesem Artikel müssen Sie einen AKS-Cluster erstellt und eine `kubectl`-Verbindung mit dem Cluster hergestellt haben. Informationen hierzu finden Sie in der [AKS-Schnellstartanleitung][aks-quickstart].

Sie benötigen eine private Docker-Registrierung in Azure Container Registry (ACR). Eine Anleitung zum Erstellen einer ACR-Instanz finden Sie unter [Schnellstart: Azure Container Registry][acr-quickstart].

Helm muss ebenfalls in Ihrem AKS-Cluster installiert sein. Weitere Informationen zum Installieren und Konfigurieren von Helm finden Sie unter [Verwenden von Helm mit Azure Kubernetes Service (AKS)][aks-helm].

Schließlich müssen Sie [Docker](https://www.docker.com) installieren.

## <a name="install-draft"></a>Installieren von Draft

Bei der Draft-Befehlszeilenschnittstelle handelt es sich um einen Client, der auf Ihrem Entwicklungssystem ausgeführt wird und die Bereitstellung von Code in einem Kubernetes-Cluster ermöglicht. Wenn Sie die Draft-Befehlszeilenschnittstelle auf einem Mac installieren möchten, verwenden Sie `brew`. Informationen zu weiteren Installationsoptionen finden Sie in der [Draft-Installationsanleitung][draft-documentation].

> [!NOTE]
> Wenn Sie eine frühere Draft-Version als 0.12 installiert haben, müssen Sie Draft zuerst mit `helm delete --purge draft` aus dem Cluster löschen und dann die lokale Konfiguration durch Ausführen von `rm -rf ~/.draft` entfernen. Wenn Sie macOS verwenden, führen Sie `brew upgrade draft` aus.

```console
brew tap azure/draft
brew install draft
```

Initialisieren Sie Draft nun mithilfe des Befehls `draft init`:

```console
draft init
```

## <a name="configure-draft"></a>Konfigurieren von Draft

Draft erstellt Containerimages lokal und stellt sie anschließend entweder über die lokale Registrierung bereit (beispielsweise im Fall von Minikube) oder verwendet eine von Ihnen angegebene Imageregistrierung. In diesem Artikel wird Azure Container Registry (ACR) verwendet. Sie müssen daher eine Vertrauensstellung zwischen dem AKS-Cluster und der ACR-Registrierung herstellen und Draft anschließend so konfigurieren, dass die Containerimages mithilfe von Push an ACR übertragen werden.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Erstellen der Vertrauensstellung zwischen dem AKS-Cluster und ACR

Wenn Sie eine Vertrauensstellung zwischen einem AKS-Cluster und einer ACR-Registrierung erstellen möchten, müssen Sie dem Azure Active Directory-Dienstprinzipal, der vom AKS-Cluster für den Zugriff auf die ACR-Registrierung verwendet wird, Berechtigungen zuweisen. Geben Sie in den folgenden Befehlen Ihren eigenen `<resourceGroupName>` an, und ersetzen Sie `<aksName>` durch den Namen des AKS-Clusters sowie `<acrName>` durch den Namen Ihrer ACR-Registrierung:

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group <resourceGroupName> --name <aksName> --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group <resourceGroupName> --name <acrName> --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

Weitere Informationen zum Zugriff auf ACR finden Sie unter [Authentifizieren bei ACR](../container-registry/container-registry-auth-aks.md).

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Konfigurieren von Draft für Pushübertragungen und Bereitstellungen aus ACR

Da nun eine Vertrauensstellung zwischen AKS und ACR besteht, können Sie die Nutzung von ACR über Ihren AKS-Cluster aktivieren.

1. Legen Sie den *registry*-Wert der Draft-Konfiguration fest. Ersetzen Sie in den folgenden Befehlen `<acrName>` durch den Namen Ihrer ACR-Registrierung:

    ```console
    draft config set registry <acrName>.azurecr.io
    ```

1. Melden Sie sich bei der ACR-Registrierung mit [az acr login][az-acr-login] an:

    ```azurecli
    az acr login --name <acrName>
    ```

Da eine Vertrauensstellung zwischen ACS und ACR besteht, sind keine Kennwörter oder Geheimnisse für Pull- oder Pushvorgänge im Zusammenhang mit der ACR-Registrierung erforderlich. Die Authentifizierung erfolgt auf der Azure Resource Manager-Ebene mithilfe von Azure Active Directory.

## <a name="run-an-application"></a>Ausführen einer Anwendung

Damit Sie sich in der Praxis einen Überblick über die Funktionsweise von Draft verschaffen können, stellen Sie nun eine Beispielanwendung über das [Draft-Repository][draft-repo] bereit. Klonen Sie zunächst das Repository:

```console
git clone https://github.com/Azure/draft
```

Wechseln Sie in das Verzeichnis mit den Java-Beispielen:

```console
cd draft/examples/example-java/
```

Starten Sie den Prozess mithilfe des Befehls `draft create`. Dieser Befehl erstellt die Artefakte, die verwendet werden, um die Anwendung in einem Kubernetes-Cluster auszuführen. Zu diesen Elementen zählen eine Dockerfile-Datei, ein Helm-Diagramm und eine *draft.toml*-Datei, bei der es sich um die Draft-Konfigurationsdatei handelt.

```
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Führen Sie die Beispielanwendung in Ihrem AKS-Cluster mit dem Befehl `draft up` aus. Dieser Befehl erstellt die Dockerfile-Datei, um ein Containerimage zu erstellen. Das Image wird per Push an ACR übertragen, und schließlich wird das Helm-Diagramm installiert, um die Anwendung in AKS zu starten.

Push- und Pullvorgänge für das Containerimage können beim ersten Ausführen dieses Befehls einige Zeit in Anspruch nehmen. Nach der Zwischenspeicherung der Basisebenen verkürzt sich die Zeit zum Bereitstellen der Anwendung erheblich.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

Wenn beim Pushen des Docker-Images Probleme auftreten, sollten Sie überprüfen, ob Sie sich bei Ihrer ACR-Registrierung erfolgreich mit dem Befehl [az acr login][az-acr-login] angemeldet haben, und anschließend den Befehl `draft up` erneut ausführen.

## <a name="test-the-application-locally"></a>Lokales Testen der Anwendung

Testen Sie die Anwendung mithilfe des Befehls `draft connect`. Mit diesem Befehl wird eine sichere Proxyverbindung mit dem Kubernetes-Pod hergestellt. Nach Abschluss des Vorgangs kann auf die Anwendung über die angegebene URL zugegriffen werden.

> [!NOTE]
> Es kann einige Minuten dauern, bis das Containerimage heruntergeladen wurde und die Anwendung gestartet wird. Sollte beim Zugreifen auf die Anwendung ein Fehler auftreten, versuchen Sie erneut, eine Verbindung herzustellen.

```
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Öffnen Sie zum Zugreifen auf Ihre Anwendung einen Webbrowser, und geben Sie die Daten für die Adresse und den Port ein (z. B. `http://localhost:49804`), die in der Ausgabe von `draft connect` enthalten sind. 

![Beispiel-Java-App, die unter Draft ausgeführt wird](media/kubernetes-draft/sample-app.png)

Drücken Sie `Control+C`, um die Proxyverbindung zu beenden.

> [!NOTE]
> Sie können auch den Befehl `draft up --auto-connect` nutzen, um Ihre Anwendung zu erstellen, bereitzustellen und dann eine Verbindung mit dem ersten ausgeführten Container herzustellen.

## <a name="access-the-application-on-the-internet"></a>Zugreifen auf die Anwendung über das Internet

Im letzten Schritt wurde eine Proxyverbindung mit dem Anwendungs-Pod in Ihrem AKS-Cluster hergestellt. Beim Entwickeln und Testen Ihrer Anwendung können Sie die Anwendung im Internet verfügbar machen. Erstellen Sie hierzu einen Kubernetes-Dienst des Typs [LoadBalancer][kubernetes-service-loadbalancer] oder einen [Eingangscontroller][kubernetes-ingress]. Im Folgenden erstellen Sie einen *LoadBalancer*-Dienst.

Passen Sie zunächst das Draft-Paket *values.yaml* so an, dass ein Dienst des Typs *LoadBalancer* erstellt wird:

```console
vi charts/java/values.yaml
```

Suchen Sie die Eigenschaft *service.type*, und ersetzten Sie den Wert *ClusterIP* durch *LoadBalancer*, wie im folgenden gekürzten Beispiel gezeigt wird:

```yaml
[...]
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
[...]
```

Speichern und schließen Sie die Datei, und starten Sie anschließend mit `draft up` die Anwendung erneut:

```console
draft up
```

Es kann einige Minuten dauern, bis der Dienst eine öffentliche IP-Adresse zurückgibt. Verwenden Sie zum Überwachen des Status den Befehl `kubectl get service` mit dem *watch*-Parameter:

```console
kubectl get service --watch
```

Für die externe IP-Adresse (*EXTERNAL-IP*) des Diensts wird zunächst *pending* (ausstehend) angezeigt:

```
NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
```

Sobald für die externe IP-Adresse nicht mehr *pending*, sondern eine IP-Adresse angezeigt wird, drücken Sie `Control+C`, um den `kubectl`-Überwachungsvorgang zu beenden:

```
NAME                TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Navigieren Sie zum Aufrufen der Anwendung mit `curl` zur externen IP-Adresse Ihres Lastenausgleichs:

```
$ curl 52.175.224.118

Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Durchlaufen der Anwendung

Nachdem Draft konfiguriert wurde und die Anwendung in Kubernetes ausgeführt wird, können Sie nun den Code durchlaufen. Führen Sie zum Testen von aktualisiertem Code den Befehl `draft up` aus, um die laufende Anwendung zu aktualisieren.

Aktualisieren Sie in diesem Beispiel die Java-Anwendung, damit der angezeigte Text angepasst wird. Öffnen Sie die Datei *Hello.java*:

```console
vi src/main/java/helloworld/Hello.java
```

Legen Sie als Ausgabetext *Hello World, I'm Java in AKS!* fest:

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

Führen Sie den Befehl `draft up` aus, um die Anwendung erneut bereitzustellen:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Führen Sie für die IP-Adresse Ihres Lastenausgleichs erneut „curl“ aus, damit die Anwendung aktualisiert wird:

```
$ curl 52.175.224.118

Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Draft finden Sie in der Draft-Dokumentation auf GitHub.

> [!div class="nextstepaction"]
> [Draft-Dokumentation][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[draft-repo]: https://github.com/Azure/draft

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[kubernetes-ingress]: ./ingress-basic.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[az-acr-login]: /cli/azure/acr#az-acr-login
