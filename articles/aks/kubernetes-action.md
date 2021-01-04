---
title: Erstellen, Testen und Bereitstellen von Containern für den Azure Kubernetes Service mithilfe von GitHub Actions
description: Erfahren Sie, wie Sie mit GitHub Actions Ihren Container für Kubernetes bereitstellen können.
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: b17d005afa4b14a7895dbc7c9d7f9a67c680e320
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560354"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub Actions für die Bereitstellung im Kubernetes Service

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) bietet Ihnen die Flexibilität, einen automatisierten Workflow für den Softwareentwicklungs-Lebenszyklus zu erstellen. Sie können mehrere Kubernetes-Aktionen verwenden, um Container von Azure Container Registry zu Azure Kubernetes Service mit GitHub Actions bereitzustellen. 

## <a name="prerequisites"></a>Voraussetzungen 

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein GitHub-Konto. Falls Sie noch nicht über ein Konto verfügen, können Sie sich [kostenlos](https://github.com/join) registrieren.  
- Ein funktionierender Kubernetes-Cluster
    - [Tutorial: Vorbereiten einer Anwendung für Azure Kubernetes Service](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>Übersicht über die Workflowdatei

Ein Workflow wird durch eine YAML-Datei im Pfad `/.github/workflows/` in Ihrem Repository definiert. Diese Definition enthält die verschiedenen Schritte und Parameter, die den Workflow bilden.

Für einen Workflow, der auf AKS ausgerichtet ist, besteht die Datei aus drei Abschnitten:

|`Section`  |Aufgaben  |
|---------|---------|
|**Authentifizierung** | Anmelden bei einer privaten Containerregistrierung (ACR). |
|**Build** | Erstellen und Pushen des Containerimages.  |
|**Bereitstellen** | 1. Festlegen des Ziel-AKS-Clusters. |
| |2. Erstellen eines generischen/docker-registry-Geheimnisses im Kubernetes-Cluster.  |
||3. Bereitstellen auf dem Kubernetes-Cluster.|

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Sie können mit dem Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) in der [Azure CLI](/cli/azure/) einen [Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) erstellen. Sie können diesen Befehl mit [Azure Cloud Shell](https://shell.azure.com/) im Azure-Portal oder durch Auswählen der Schaltfläche **Ausprobieren** ausführen.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Ersetzen Sie im obigen Befehl die Platzhalter durch Ihre Abonnement-ID und Ressourcengruppe. Bei der Ausgabe handelt es sich um die Anmeldeinformationen für die Rollenzuweisung, die Zugriff auf ihre Ressource bereitstellen. Der Befehl sollte ein JSON-Objekt ausgeben, das diesem ähnelt.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Kopieren Sie dieses JSON-Objekt, das Sie zum Authentifizieren aus GitHub verwenden können.

## <a name="configure-the-github-secrets"></a>Konfigurieren der GitHub-Geheimnisse

Führen Sie zum Konfigurieren der Geheimnisse die folgenden Schritte aus:

1. Navigieren Sie in [GitHub](https://github.com/) zu Ihrem Repository, und wählen Sie **Einstellungen > Geheimnisse > Neues Geheimnis hinzufügen** aus.

    ![Der Screenshot zeigt den Link zum Hinzufügen eines neuen Geheimnisses für ein Repository.](media/kubernetes-action/secrets.png)

2. Fügen Sie die Inhalte des obigen `az cli`-Befehls als Wert der Geheimnisvariablen ein. Beispiel: `AZURE_CREDENTIALS`.

3. Definieren Sie in gleicher Weise die folgenden zusätzlichen Geheimnisse für die Containerregistrierungs-Anmeldeinformationen, und legen Sie sie in der Aktion für die Docker-Anmeldung fest. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Nach der Definition werden die Geheimnisse wie folgt angezeigt.

    ![Der Screenshot zeigt bestehende Geheimnisse für ein Repository.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Erstellen eines Containerimages und Bereitstellen im Azure Kubernetes Service-Cluster

Das Erstellen und Pushen der Containerimages erfolgt mit der Aktion `Azure/docker-login@v1`. 


```yml
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}

```

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Bereitstellen im Azure Kubernetes Service-Cluster

Zum Bereitstellen eines Containerimages in AKS müssen Sie die Aktion `Azure/k8s-deploy@v1` verwenden. Diese Aktion umfasst fünf Parameter:

| **Parameter**  | **Erklärung**  |
|---------|---------|
| **namespace** | (Optional) Wählen Sie den Ziel-Kubernetes-Namespace aus. Wenn der Namespace nicht angegeben ist, werden die Befehle im Standardnamespace ausgeführt. | 
| **manifests** |  (Erforderlich) Pfad zu den Manifestdateien, die für die Bereitstellung verwendet werden. |
| **images** | (Optional) Vollständig qualifizierte Ressourcen-URL der Images, die für Ersetzungen in den Manifestdateien verwendet werden sollen. |
| **imagepullsecrets** | (Optional) Name eines docker-registry-Geheimnisses, das bereits im Cluster eingerichtet wurde. Jeder dieser Geheimnisnamen wird im Feld „imagePullSecrets“ für die Workloads hinzugefügt, die in den Eingabemanifestdateien gefunden werden. |
| **kubectl-version** | (Optional) Installiert eine bestimmte Version der kubectl-Binärdatei. |


Vor der Bereitstellung für AKS müssen Sie den Kubernetes-Zielnamespace festlegen und ein Geheimnis für Imagepullvorgänge erstellen. Weitere Informationen über das Pullen von Images finden Sie unter [Abrufen von Images aus einer Azure Container Registry-Instanz per Pull in einem Kubernetes-Cluster](../container-registry/container-registry-auth-kubernetes.md). 

```yaml
  # Create namespace if doesn't exist
  - run: |
      kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
  
  # Create image pull secret for ACR
  - uses: azure/k8s-create-secret@v1
    with:
      container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
      container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
      container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
      secret-name: ${{ env.SECRET }}
      namespace: ${{ env.NAMESPACE }}
      force: true
```


Schließen Sie Ihre Bereitstellung mit der Aktion `k8s-deploy` ab. Ersetzen Sie die Umgebungsvariablen durch Werte für Ihre Anwendung. 

```yaml

on: [push]

# Environment variables available to all jobs and steps in this workflow
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  SECRET: {secret-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
    
    # Set the target Azure Kubernetes Service (AKS) cluster. 
    - uses: azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: ${{ env.CLUSTER_NAME }}
        resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}
    
    # Create namespace if doesn't exist
    - run: |
        kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
    
    # Create image pull secret for ACR
    - uses: azure/k8s-create-secret@v1
      with:
        container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
        force: true
    
    # Deploy app to AKS
    - uses: azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        imagepullsecrets: |
          ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ihr Kubernetes-Cluster, Ihre Containerregistrierung und Ihr Repository nicht mehr benötigt werden, bereinigen Sie die bereitgestellten Ressourcen, indem Sie die Ressourcengruppe und Ihr GitHub-Repository löschen. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu Azure Kubernetes Service](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>Weitere Kubernetes GitHub Actions

* [Installer für Kubectl-Tool](https://github.com/Azure/setup-kubectl) (`azure/setup-kubectl`): Installiert eine bestimmte Version von kubectl auf dem Runner.
* [Kubernetes: Festlegen des Kontexts](https://github.com/Azure/k8s-set-context) (`azure/k8s-set-context`): Legen Sie den Kubernetes-Zielclusterkontext fest, der von anderen Aktionen verwendet wird, oder führen Sie beliebige kubectl-Befehle aus.
* [AKS: Festlegen des Kontexts](https://github.com/Azure/aks-set-context) (`azure/aks-set-context`): Legen Sie den Azure Kubernetes Service-Zielclusterkontext fest.
* [Kubernetes: Erstellen eines Geheimnisses](https://github.com/Azure/k8s-create-secret) (`azure/k8s-create-secret`): Erstellen Sie ein generisches Geheimnis oder docker-registry-Geheimnis im Kubernetes-Cluster.
* [Kubernetes: Bereitstellen](https://github.com/Azure/k8s-deploy) (`azure/k8s-deploy`): Erstellen Sie Manifeste und stellen Sie sie in Kubernetes-Clustern bereit.
* [Einrichten von Helm](https://github.com/Azure/setup-helm) (`azure/setup-helm`): Installieren Sie eine bestimmte Version der Helm-Binärdatei auf dem Runner.
* [Kubernetes: Baking](https://github.com/Azure/k8s-bake) (`azure/k8s-bake`): Erstellen Sie eine Manifestdatei, die Sie mithilfe von helm2, kustomize oder kompose für Bereitstellungen verwenden können.
* [Kubernetes – Lint](https://github.com/azure/k8s-lint) (`azure/k8s-lint`): Überprüfen (Lint) Sie die Manifestdateien.
