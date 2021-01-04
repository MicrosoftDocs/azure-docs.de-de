---
title: GitHub-Aktionen und Azure Kubernetes Service (Vorschau)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Überprüfen und Testen von Änderungen eines Pull Requests direkt im Azure Kubernetes Service mithilfe von GitHub-Aktionen und Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container, GitHub-Aktionen, Helm, Service Mesh, Service Mesh-Routing, kubectl, k8s
manager: gwallace
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 9bed61861c80f141270e50b644b32ae42fbe8e77
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995562"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub-Aktionen und Azure Kubernetes Service (Vorschau)

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces stellt mithilfe von GitHub Actions einen Workflow zur Verfügung, mit dem Sie Änderungen einer Pull Request direkt in AKS testen können, bevor die Pull Request im Hauptzweig Ihres Repository zusammengeführt wird. Durch eine laufende Anwendung zum Prüfen der Änderungen einer Pull Request kann die Konfidenz sowohl des Entwicklers als auch der Teammitglieder erhöht werden. Diese laufende Anwendung kann auch dazu beitragen, dass Teammitglieder wie Produktmanager und Designer in frühen Entwicklungsphasen Teil des Überprüfungsprozesses werden.

In diesem Leitfaden lernen Sie Folgendes:

* Einrichten von Azure Dev Spaces in einem verwalteten Kubernetes-Cluster in Azure
* Bereitstellen einer umfangreichen Anwendung mit mehreren Microservices in einem Entwicklungsbereich.
* Richten Sie CI/CD mit GitHub-Aktionen ein.
* Testen eines einzelnen Microservice in einem isolierten Entwicklungsbereich im Kontext der vollständigen Anwendung.

> [!IMPORTANT]
> Diese Funktion steht derzeit als Vorschau zur Verfügung. Vorschauversionen werden Ihnen zur Verfügung gestellt, wenn Sie die [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) akzeptieren. Einige Aspekte dieses Features werden bis zur allgemeinen Verfügbarkeit unter Umständen noch geändert.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen.
* Die [Azure CLI][azure-cli-installed] muss installiert sein.
* [Helm 3 muss installiert sein][helm-installed].
* Ein GitHub-Konto mit [aktivierten GitHub-Aktionen][github-actions-beta-signup].
* Ausführung der [Azure Dev Spaces-Beispielanwendung „Bike Sharing“](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) in einem AKS-Cluster.

## <a name="create-an-azure-container-registry"></a>Erstellen einer Azure-Containerregistrierung

Erstellen Sie eine ACR-Instanz (Azure Container Registry):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Der ACR-Name muss innerhalb von Azure eindeutig sein und aus 5 bis 50 alphanumerischen Zeichen bestehen. Alle verwendeten Buchstaben müssen Kleinbuchstaben sein.

Speichern Sie den *loginServer*-Wert der Ausgabe, da er in einem späteren Schritt verwendet wird.

## <a name="create-a-service-principal-for-authentication"></a>Erstellen eines Dienstprinzipals für die Authentifizierung

Verwenden Sie [az ad sp create-for-rbac][az-ad-sp-create-for-rbac], um einen Dienstprinzipal zu erstellen. Beispiel:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Speichern Sie die JSON-Ausgabe, da sie in einem späteren Schritt verwendet wird.

Verwenden Sie [az aks show][az-aks-show], um die *ID* Ihres AKS-Clusters anzuzeigen:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Verwenden Sie [az acr show][az-acr-show], um die *ID* der ACR anzuzeigen:

```azurecli
az acr show --name <acrName> --query id
```

Verwenden Sie [az role assignment create][az-role-assignment-create], um Zugriff als *Mitwirkender* auf Ihren AKS-Cluster und *AcrPush*-Zugriff auf Ihre ACR zu gewähren.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Sie müssen der Besitzer des AKS-Clusters und der ACR sein, um dem Dienstprinzipal Zugriff auf diese Ressourcen zu gewähren.

## <a name="configure-your-github-action"></a>Konfigurieren der GitHub-Aktion

> [!IMPORTANT]
> Für das Repository müssen GitHub-Aktionen aktiviert sein. Um GitHub-Aktionen für das Repository zu aktivieren, navigieren Sie in GitHub zu Ihrem Repository, klicken Sie auf die Registerkarte „Aktionen“ und aktivieren Sie Aktionen für dieses Repository.

Navigieren Sie zum geforkten Repository und klicken Sie auf *Einstellungen*. Klicken Sie in der linken Randleiste auf *Geheimnisse*. Klicken Sie auf *Neues Geheimnis hinzufügen*, um die nachstehend aufgeführten neuen Geheimnisse hinzuzufügen:

1. *AZURE_CREDENTIALS*: Die gesamte Ausgabe der Dienstprinzipalerstellung.
1. *RESOURCE_GROUP*: Die Ressourcengruppe für den AKS-Cluster (in diesem Beispiel *MyResourceGroup*).
1. *CLUSTER_NAME*: Der Name des AKS-Clusters (in diesem Beispiel *MyAKS*).
1. *CONTAINER_REGISTRY*: Der *loginServer* für die ACR.
1. *Host*: Der Host für den Entwicklungsbereich im Format *<MASTER_SPACE>.<APP_NAME>.<HOST_SUFFIX>* (in diesem Beispiel *dev.bikesharingweb.fedcab0987.eus.azds.io*).
1. *IMAGE_PULL_SECRET*: Der Name des Geheimnisses, das Sie verwenden möchten, z. B. *demo-secret*.
1. *MASTER_SPACE*: Der Name des übergeordneten Entwicklungsbereichs (in diesem Beispiel *dev*).
1. *REGISTRY_USERNAME*: *clientID* in der JSON-Ausgabe der Dienstprinzipalerstellung.
1. *REGISTRY_USERNAME*: *clientSecret* in der JSON-Ausgabe der Dienstprinzipalerstellung.

> [!NOTE]
> Alle aufgeführten Geheimnisse werden von der GitHub-Aktion verwendet und in [.github/workflows/bikes.yml][github-action-yaml] konfiguriert.

Optional: Wenn Sie den Masterbereich nach der PR-Zusammenführung aktualisieren möchten, können Sie das *GATEWAY_HOST*-Geheimnis im Format *<MASTERBEREICH>.gateway.<HOSTSUFFIX>* hinzufügen. In diesem Beispiel ist dies *dev.gateway.fedcab0987.eus.azds.io*. Nachdem Sie Ihre Änderungen im Master-Branch in Ihrem Fork zusammengeführt haben, wird eine weitere Aktion ausgeführt, um die gesamte Anwendung im Master-Entwicklungsbereich neu zu erstellen und auszuführen. In diesem Beispiel ist *dev* der Masterbereich. Diese Aktion wird in [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml] konfiguriert.

Wenn die Änderungen in Ihrem PR in einem untergeordneten Bereich ausgeführt werden sollen, aktualisieren Sie die Geheimnisse *MASTER_SPACE* und *HOST*. Ein Beispiel: Die Anwendung in *dev* wird mit einem untergeordneten Bereich *dev/azureuser1* ausgeführt. Der PR soll in einem untergeordneten Bereich von *dev/azureuser1* ausgeführt werden:

* Aktualisieren Sie *MASTER_SPACE* auf den untergeordneten Bereich, der zum übergeordneten Bereich werden soll; in diesem Beispiel *azureuser1*.
* Aktualisieren Sie *HOST* auf *<GRANDPARENT_SPACE>.<APP_NAME>.<HOST_SUFFIX>* ; in diesem Beispiel ist dies *dev.bikesharingweb.fedcab0987.eus.azds.io*.

## <a name="create-a-new-branch-for-code-changes"></a>Neuen Branch für Codeänderungen erstellen

Navigieren Sie zu `BikeSharingApp/` und erstellen Sie einen neue Branch mit dem Namen *bike-images*.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Bearbeiten Sie [Bikes/server.js][bikes-server-js] und entfernen Sie die Zeilen 232 und 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Der Abschnitt sollte jetzt folgendermaßen aussehen:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Speichern Sie die Datei und verwenden Sie dann `git add` sowie `git commit`, um die Änderungen zu bereitzustellen.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Änderungen pushen

Verwenden Sie `git push`, um den neuen Branch per Push in Ihr geforktes Repository zu pushen:

```cmd
git push origin bike-images
```

Navigieren Sie nach dem Pushen in GitHub zum geforkten Repository und erstellen Sie eine Pull Request mit dem *master*-Branch in Ihrem geforkten Repository als Basisbranch im Vergleich zum *bike-images*-Branch.

Navigieren Sie nach dem Öffnen der Pull Request zur Registerkarte *Aktionen*. Vergewissern Sie sich, dass eine neue Aktion gestartet wurde und der *Bikes*-Dienst erstellt wird.

## <a name="view-the-child-space-with-your-changes"></a>Untergeordneten Bereich mit Ihren Änderungen anzeigen

Nachdem die Aktion abgeschlossen wurde, wird basierend auf den Änderungen in der Pull Request ein Kommentar mit einer URL zu Ihrem neuen untergeordneten Bereich angezeigt.

> [!div class="mx-imgBorder"]
> ![GitHub-Aktions-URL](../media/github-actions/github-action-url.png)

Navigieren Sie zum Dienst *bikesharingweb*, indem Sie die URL im Kommentar öffnen. Wählen Sie *Aurelia Briggs (customer)* als Benutzer und dann ein zu mietendes Fahrrad aus. Vergewissern Sie sich, dass das Platzhalterbild für das Fahrrad nicht mehr angezeigt wird.

Wenn Sie Ihre Änderungen im *master*-Branch in Ihrem Fork zusammenführen, wird eine weitere Aktion ausgeführt, um die gesamte Anwendung im übergeordneten Entwicklungsbereich neu zu erstellen und auszuführen. In diesem Beispiel ist der übergeordnete Bereich *dev*. Diese Aktion wird in [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml] konfiguriert.

## <a name="clean-up-your-azure-resources"></a>Bereinigen Ihrer Azure-Ressourcen

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Funktionsweise von Azure Dev Spaces:

> [!div class="nextstepaction"]
> [Funktionsweise von Azure Dev Spaces](../how-dev-spaces-works.md)

[azure-cli-installed]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
