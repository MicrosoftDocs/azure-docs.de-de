---
title: Bereitstellen einer Containerinstanz mithilfe einer GitHub Actions-Aktion
description: Konfigurieren Sie eine GitHub Actions-Aktion, die die Schritte für das Erstellen, Pushen und Bereitstellen eines Containerimages in Azure Container Instances automatisiert.
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 0068b9869e5ef7a3b6c0fd21c1b69cdae1d4ab4b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454825"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Konfigurieren einer GitHub Actions-Aktion zum Erstellen einer Containerinstanz

Bei [GitHub Actions](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) handelt es sich um eine Featuresammlung in GitHub, mit der sich Ihre Softwareentwicklungsworkflows am selben Ort automatisieren lassen, an dem Sie auch den Code speichern und gemeinsam an Pull Requests und Problemen arbeiten.

Mit der GitHub-Aktion zum [Bereitstellen in Azure Container Instances](https://github.com/azure/aci-deploy) können Sie die Bereitstellung eines einzelnen Containers in Azure Container Instances automatisieren. Die Aktion ermöglicht es Ihnen, Eigenschaften für eine Containerinstanz festzulegen, ähnlich wie der [Befehl „az container create“][az-container-create].

In diesem Artikel erfahren Sie, wie Sie einen Workflow in einem GitHub-Repository einrichten, der die folgenden Aktionen ausführt:

* Erstellen eines Images auf der Grundlage eines Dockerfile
* Pushen des Images an eine Azure-Containerregistrierung
* Bereitstellen des Containerimages in einer Azure-Containerinstanz

In diesem Artikel werden zwei Möglichkeiten für die Einrichtung des Workflows vorgestellt:

* [Konfigurieren des GitHub-Workflows](#configure-github-workflow): Erstellen Sie einen Workflow in einem GitHub-Repository mithilfe der Aktion zum Bereitstellen von Azure Container Instances und anderer Aktionen.  
* [Verwenden der Erweiterung „Bereitstellung in Azure“](#use-deploy-to-azure-extension): Verwenden Sie den Befehl `az container app up` in der Erweiterung [Bereitstellung in Azure](https://github.com/Azure/deploy-to-azure-cli-extension) in der Azure CLI. Dieser Befehl optimiert die Erstellung des GitHub-Workflows und der Bereitstellungsschritte.

> [!IMPORTANT]
> Die GitHub Actions-Aktion für Azure Container Instances befindet sich derzeit in der Vorschau. Vorschauversionen werden Ihnen zur Verfügung gestellt, wenn Sie die [zusätzlichen Nutzungsbedingungen][terms-of-use] akzeptieren. Einige Aspekte dieses Features werden bis zur allgemeinen Verfügbarkeit unter Umständen noch geändert.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein GitHub-Konto:** Falls Sie noch keins besitzen, erstellen Sie ein Konto unter https://github.com.
* **Azure CLI:** Sie können Azure Cloud Shell oder eine lokale Installation der Azure CLI für diese Azure CLI-Schritte verwenden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].
* **Azure-Containerregistrierung:** Wenn Sie noch keine haben, erstellen Sie eine Azure-Containerregistrierung im Basic-Tarif mithilfe der [Azure CLI](../container-registry/container-registry-get-started-azure-cli.md), des [Azure-Portals](../container-registry/container-registry-get-started-portal.md) oder mithilfe einer anderen Methode. Notieren Sie sich die Ressourcengruppe, die für die Bereitstellung verwendet wurde, die für den GitHub-Workflow verwendet wird.

## <a name="set-up-repo"></a>Einrichten des Repositorys

* Verwenden Sie GitHub für die Beispiele in diesem Artikel, um das folgende Repository zu forken: https://github.com/Azure-Samples/acr-build-helloworld-node.

  Dieses Repository enthält ein Dockerfile und Quelldateien, um ein Containerimage einer kleinen Web-App zu erstellen.

  ![Screenshot der Schaltfläche zum Forken (hervorgehoben) in GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Vergewissern Sie sich, dass GitHub Actions für Ihr Repository aktiviert ist. Navigieren Sie zum geforkten Repository, und klicken Sie auf **Einstellungen** > **Aktionen**. Vergewissern Sie sich unter **Actions permissions** (Aktionsberechtigungen), dass die Option **Enable local and third party Actions for this repository** (Lokale und Drittanbieteraktionen für dieses Repository aktivieren) ausgewählt ist.

## <a name="configure-github-workflow"></a>Konfigurieren des GitHub-Workflows

### <a name="create-service-principal-for-azure-authentication"></a>Erstellen des Dienstprinzipals für die Azure-Authentifizierung

Im GitHub-Workflow müssen Sie die Azure-Anmeldeinformationen bereitstellen, um sich in der Azure CLI zu authentifizieren. Im folgenden Beispiel wird ein Dienstprinzipal erstellt, der für die Ressourcengruppe Ihrer Containerregistrierung die Rolle „Mitwirkender“ innehat.

Rufen Sie zunächst die Ressourcen-ID Ihrer Ressourcengruppe ab: Ersetzen Sie den Namen Ihrer Gruppe im folgenden [az group show][az-group-show]-Befehl:

```azurecli
$groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Verwenden Sie [az ad sp create-for-rbac][az-ad-sp-create-for-rbac], um den Dienstprinzipal zu erstellen:

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

Die Ausgabe Die Ausgabe lautet in etwa wie folgt:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Speichern Sie die JSON-Ausgabe, da sie in einem späteren Schritt verwendet wird. Beachten Sie außerdem `clientId`. Sie benötigen diesen Wert, um den Dienstprinzipal im nächsten Abschnitt zu aktualisieren.

### <a name="update-service-principal-for-registry-authentication"></a>Aktualisieren des Dienstprinzipals für die Registrierungsauthentifizierung

Aktualisieren Sie die Azure-Anmeldeinformationen für den Dienstprinzipal, um Push- und Pullzugriff für Ihre Containerregistrierung zu erlauben. Dieser Schritt ermöglicht dem GitHub-Workflow, den Dienstprinzipal zum [Authentifizieren bei Ihrer Containerregistrierung](../container-registry/container-registry-auth-service-principal.md) zu verwenden und ein Docker-Image zu pushen und zu pullen. 

Rufen Sie die Ressourcen-ID Ihrer Containerregistrierung ab. Fügen Sie im folgenden [az acr show][az-acr-show]-Befehl den Namen Ihrer Registrierung ein:

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Weisen Sie mit [az role assignment create][az-role-assignment-create] die Rolle „AcrPush“ zu, wodurch die Registrierung Push- und Pull-Zugriff erhält. Fügen Sie die Client-ID Ihres Dienstprinzipals ein:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Speichern der Anmeldeinformationen im GitHub-Repository

1. Navigieren Sie in der GitHub-Benutzeroberfläche zum geforkten Repository, und klicken Sie auf **Einstellungen** > **Geheimnisse**. 

1. Klicken Sie auf **Add a new secret** (Neues Geheimnis hinzufügen), um die folgenden Geheimnisse hinzuzufügen:

|`Secret`  |Wert  |
|---------|---------|
|`AZURE_CREDENTIALS`     | Die gesamte JSON-Ausgabe des Schritts zur Dienstprinzipalerstellung |
|`REGISTRY_LOGIN_SERVER`   | Der Name des Anmeldeservers Ihrer Registrierung (nur Kleinbuchstaben), Beispiel: *myregistry.azurecr.io*        |
|`REGISTRY_USERNAME`     |  Der Wert von `clientId` aus der JSON-Ausgabe der Dienstprinzipalerstellung       |
|`REGISTRY_PASSWORD`     |  Der Wert von `clientSecret` aus der JSON-Ausgabe der Dienstprinzipalerstellung |
| `RESOURCE_GROUP` | Der Name der Ressourcengruppe, die Sie für die Festlegung der Berechtigungen des Dienstprinzipals verwendet haben |

### <a name="create-workflow-file"></a>Erstellen der Workflowdatei

1. Klicken Sie in der GitHub-Benutzeroberfläche auf **Aktionen** > **New workflow** (Neuer Workflow).
1. Klicken Sie auf **Set up a workflow yourself** (Workflow selbst einrichten).
1. Fügen Sie unter **Edit new file** (Neue Datei bearbeiten) die folgenden YAML-Inhalte ein, um den Beispielcode zu überschreiben. Akzeptieren Sie den Standarddateinamen `main.yml`, oder geben Sie einen benutzerdefinierten Dateinamen an.
1. Klicken Sie auf **Start commit** (Commit starten), geben Sie optional kurze und erweiterte Beschreibungen Ihres Commits an, und klicken Sie auf **Commit new file** (Neue Datei committen).

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@master
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>Überprüfen des Workflows

Nachdem Sie die Workflowdatei committet haben, wird der Workflow ausgelöst. Wenn Sie sich den Fortschritt des Workflows ansehen möchten, navigieren Sie zu **Aktionen** > **Workflows**. 

![Aufrufen des Workflowfortschritts](./media/container-instances-github-action/github-action-progress.png)

Weitere Informationen zum Anzeigen des Status und der Ergebnisse der einzelnen Workflowschritte finden Sie unter [Verwalten einer Workflowausführung](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run). Wenn der Workflow nicht abgeschlossen wird, lesen Sie [Viewing logs to diagnose failures](https://docs.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run#viewing-logs-to-diagnose-failures) (Anzeigen von Protokollen zur Diagnose von Fehlern).

Nach erfolgreicher Beendigung des Workflows müssen Sie die Informationen zur Containerinstanz namens *aci-sampleapp* abrufen, indem Sie den Befehl [az container show][az-container-show] ausführen. Ersetzen Sie den Namen Ihrer Ressourcengruppe: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

Die Ausgabe Die Ausgabe lautet in etwa wie folgt:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

Nachdem die Instanz bereitgestellt wurde, navigieren Sie in Ihrem Browser zum FQDN des Containers, um sich die laufende Web-App anzeigen zu lassen.

![Gerade ausgeführte Web-App im Browser](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Verwenden der Erweiterung „Bereitstellung in Azure“

Alternativ können Sie den Workflow mit der [Erweiterung „Bereitstellung in Azure“](https://github.com/Azure/deploy-to-azure-cli-extension) in der Azure CLI konfigurieren. Der Befehl `az container app up` in der Erweiterung nimmt zwei Parameter, um einen Workflow einzurichten, der in Azure Container Instances bereitgestellt werden soll. 

Der von der Azure CLI erstellte Workflow ähnelt dem Workflow, den Sie [manuell mithilfe von GitHub erstellen können](#configure-github-workflow).

### <a name="additional-prerequisite"></a>Zusätzliche Anforderung

Zusätzlich zu den [Voraussetzungen](#prerequisites) und der [Einrichtung des Repositorys](#set-up-repo) für dieses Szenario müssen Sie für die **Erweiterung „Bereitstellung in Azure“** in der Azure CLI installieren.

Führen Sie den Befehl [az extension add][az-extension-add] aus, um die Erweiterung zu installieren.

```azurecli
az extension add \
  --name deploy-to-azure
```

Weitere Informationen dazu, wie Sie Erweiterungen finden, installieren und verwalten, finden Sie unter [Verwenden von Erweiterungen mit der Azure CLI](/cli/azure/azure-cli-extensions-overview).

### <a name="run-az-container-app-up"></a>Ausführen von `az container app up`

Für die Ausführung des Befehls [az container app up][az-container-app-up] müssen mindestens folgende Daten angeben:

* Den Namen Ihrer Azure-Containerregistrierung, z. B. *myregistry*
* Die URL zu Ihrem GitHub-Repository, z. B. `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Beispiel für einen Befehl:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Befehlsausführung

* Stellen Sie bei Aufforderung Ihre GitHub-Anmeldeinformationen oder ein [persönliches GitHub-Zugriffstoken](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) bereit, das über den Rollenumfang *Repository* und *Benutzer* verfügt, um sich bei Ihrem GitHub-Konto zu authentifizieren. Wenn Sie GitHub-Anmeldeinformationen angeben, erstellt der Befehl ein persönliches Zugriffstoken für Sie. Befolgen Sie weitere Aufforderungen, um den Workflow zu konfigurieren.

* Der Befehl erstellt Repositorygeheimnisse für den Workflow:

  * Anmeldeinformationen des Dienstprinzipals für die Azure CLI
  * Anmeldeinformationen für den Zugriff auf die Azure-Containerregistrierung

* Nach dem der Befehl die Workflowdatei für Ihr Repository committet hat, wird der Workflow ausgelöst. 

Die Ausgabe Die Ausgabe lautet in etwa wie folgt:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

Wie Sie den Workflowstatus und die Ergebnissen der einzelnen Schritte in der GitHub-Benutzeroberfläche aufrufen, erfahren Sie unter [Verwalten einer Workflowausführung](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run).

### <a name="validate-workflow"></a>Überprüfen des Workflows

Der Workflow stellt eine Azure-Containerinstanz mit dem Basisnamen Ihres GitHub-Repositorys bereit. In diesem Beispiel handelt es sich dabei um *acr-build-helloworld-node*. Nach erfolgreicher Beendigung des Workflows müssen Sie die Informationen zur Containerinstanz namens *acr-build-helloworld-node* abrufen, indem Sie den Befehl [az container show][az-container-show] ausführen. Ersetzen Sie den Namen Ihrer Ressourcengruppe: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name acr-build-helloworld-node \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

Die Ausgabe Die Ausgabe lautet in etwa wie folgt:

```console
FQDN                                                   ProvisioningState
---------------------------------                      -------------------
acr-build-helloworld-node.westus.azurecontainer.io     Succeeded
```

Nachdem die Instanz bereitgestellt wurde, navigieren Sie in Ihrem Browser zum FQDN des Containers, um sich die laufende Web-App anzeigen zu lassen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Beenden Sie die Containerinstanz mithilfe des Befehls [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Zum Löschen der Ressourcengruppe und aller enthaltenen Clusterressourcen führen Sie den Befehl [az group delete][az-group-delete] aus.

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Nächste Schritte

Navigieren Sie zum [GitHub Marketplace](https://github.com/marketplace?type=actions). Dort finden Sie weitere Aktionen zum Automatisieren Ihres Bereitstellungsworkflows.


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
