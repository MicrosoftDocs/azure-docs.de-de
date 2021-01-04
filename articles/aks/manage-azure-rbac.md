---
title: Verwalten von Azure RBAC in Kubernetes aus Azure
titleSuffix: Azure Kubernetes Service
description: Erfahren Sie, wie Sie Azure RBAC für Kubernetes-Autorisierung mit Azure Kubernetes Service (AKS) verwenden.
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: a2a385b2be4e1005a7aabd76261b3190ecd2a506
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684218"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Verwenden von Azure RBAC für Kubernetes-Autorisierung (Vorschau)

Bereits heute können Sie [integrierte Authentifizierung zwischen Azure Active Directory (Azure AD) und AKS](managed-aad.md) nutzen. Wenn diese Integration aktiviert ist, können Kunden Azure AD-Benutzer, -Gruppen oder -Dienstprinzipale als Antragsteller in Kubernetes RBAC verwenden. Weitere Informationen dazu finden Sie [hier](azure-ad-rbac.md).
Dank dieser Funktion müssen Sie Benutzeridentitäten und Anmeldeinformationen für Kubernetes nicht separat verwalten. Sie müssen jedoch weiterhin Azure RBAC und Kubernetes RBAC separat einrichten und verwalten. Weitere Informationen zur Authentifizierung und Autorisierung mit RBAC in AKS finden Sie [hier](concepts-identity.md).

In diesem Dokument wird ein neuer Ansatz behandelt, der die einheitliche Verwaltung und Zugriffssteuerung für Azure-Ressourcen, AKS- und Kubernetes-Ressourcen ermöglicht.

## <a name="before-you-begin"></a>Voraussetzungen

Die Möglichkeit, RBAC für Kubernetes-Ressourcen aus Azure zu verwalten, bietet Ihnen die Wahl, RBAC für die Clusterressourcen entweder mithilfe von Azure oder nativer Kubernetes-Mechanismen zu verwalten. Wenn diese Option aktiviert ist, werden Azure AD-Prinzipale exklusiv von Azure RBAC überprüft, während reguläre Kubernetes-Benutzer und Dienstkonten exklusiv durch Kubernetes RBAC überprüft werden. Weitere Informationen zur Authentifizierung und Autorisierung mit RBAC in AKS finden Sie [hier](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Voraussetzungen 
- Stellen Sie sicher, dass Sie mindestens über Version 2.9.0 der Azure-Befehlszeilenschnittstelle verfügen.
- Stellen Sie sicher, dass das `EnableAzureRBACPreview`-Featureflag aktiviert ist.
- Stellen Sie sicher, dass die `aks-preview` [-CLI-Erweiterung][az-extension-add] v0.4.55 oder höher installiert ist.
- Stellen Sie sicher, dass [kubectl v1.18.3 oder höher][az-aks-install-cli] installiert ist.

#### <a name="register-enableazurerbacpreview-preview-feature"></a>Registrieren der Previewfunktion `EnableAzureRBACPreview`

Um einen AKS-Cluster zu erstellen, der Azure RBAC für die Kubernetes-Autorisierung verwendet, müssen Sie das Featureflag `EnableAzureRBACPreview` für Ihr Abonnement aktivieren.

Registrieren Sie das `EnableAzureRBACPreview`-Featureflag mit dem Befehl [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 Sie können den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list] überprüfen:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

Wenn Sie so weit sind, aktualisieren Sie mithilfe des Befehls [az provider register][az-provider-register] die Registrierung des *Microsoft.ContainerService*-Ressourcenanbieters:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>Installieren der CLI-Erweiterung „aks-preview“

Sie benötigen die *aks-preview*-CLI-Erweiterung, Version 0.4.55 oder höher, um einen AKS-Cluster zu erstellen, der Azure RBAC verwendet. Installieren Sie die Azure CLI-Erweiterung *aks-preview* mit dem Befehl [az extension add][az-extension-add], oder installieren Sie mit dem Befehl [az extension update][az-extension-update] alle verfügbaren Updates:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Einschränkungen

- Erfordert eine [verwaltete Integration in Azure AD](managed-aad.md).
- Sie können Azure RBAC für Kubernetes-Autorisierung während der Vorschau nicht in vorhandene Cluster integrieren. Bei allgemeiner Verfügbarkeit (GA) wird dies jedoch möglich sein.
- Verwenden Sie [kubectl v1.18.3 oder höher][az-aks-install-cli].
- Wenn Sie CRDs verwenden und benutzerdefinierte Rollendefinitionen erstellen, besteht zurzeit die einzige Möglichkeit zum Abdecken von CRDs darin, `Microsoft.ContainerService/managedClusters/*/read` bereitzustellen. AKS arbeitet an der Bereitstellung differenziererer Berechtigungen für CRDs. Für die restlichen Objekte können Sie die spezifischen API-Gruppen verwenden, z. B. `Microsoft.ContainerService/apps/deployments/read`.
- Es kann bis zu 5 Minuten dauern, bis neue Rollenzuweisungen verteilt und vom Autorisierungsserver aktualisiert werden.
- Der für die Authentifizierung konfigurierte Azure AD-Mandant muss der Mandant für das Abonnement sein, das den AKS-Cluster enthält. 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Erstellen eines neuen Clusters mit Azure RBAC und verwalteter Azure AD-Integration

Erstellen Sie mit den folgenden CLI-Befehlen einen AKS-Cluster.

Erstellen Sie eine Azure-Ressourcengruppe:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Erstellen Sie den AKS-Cluster mit verwalteter Azure AD-Integration und Azure RBAC für Kubernetes-Autorisierung.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Eine erfolgreiche Erstellung eines Clusters mit Azure AD-Integration und Azure RBAC für Kubernetes-Autorisierung enthält den folgenden Abschnitt im Antworttext:

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>Erstellen von Rollenzuweisungen für Benutzer für den Zugriff auf den Cluster

AKS stellt die folgenden vier integrierten Rollen bereit:


| Role                                | BESCHREIBUNG  |
|-------------------------------------|--------------|
| RBAC-Viewer von Azure Kubernetes Service  | Ermöglicht schreibgeschützten Zugriff, um die meisten Objekte in einem Namespace anzuzeigen. Es ist nicht möglich, Rollen oder Rollenbindungen anzuzeigen. Diese Rolle lässt das Anzeigen von `Secrets` nicht zu, da das Lesen des Inhalts von Geheimnissen den Zugriff auf ServiceAccount-Anmeldeinformationen im Namespace ermöglicht, was den API-Zugriff als beliebiges Dienstkonto im Namespace ermöglichen würde (eine Form von Berechtigungsausweitung).  |
| RBAC-Writer von Azure Kubernetes Service | Ermöglicht Lese-/Schreibzugriff auf die meisten Objekte in einem Namespace. Diese Rolle lässt das Anzeigen oder Ändern von Rollen oder Rollenbindungen nicht zu. Diese Rolle ermöglicht jedoch den Zugriff auf `Secrets` und das Ausführen von Pods als beliebiges Dienstkonto im Namespace, sodass sie verwendet werden kann, um die API-Zugriffsebenen eines beliebigen ServiceAccount im Namespace zu erhalten. |
| RBAC-Administrator von Azure Kubernetes Service  | Ermöglicht Administratorzugriff, der in einem Namespace erteilt werden soll. Ermöglicht Lese-/Schreibzugriff auf die meisten Ressourcen in einem Namespace (oder Clusterbereich), einschließlich der Möglichkeit zum Erstellen von Rollen und Rollenbindungen innerhalb des Namespace. Diese Rolle lässt keinen Schreibzugriff auf das Ressourcenkontingent oder den Namespace selbst zu. |
| RBAC-Clusteradministrator von Azure Kubernetes Service  | Ermöglicht Superuserzugriff, um beliebige Aktionen für beliebige Ressourcen auszuführen. Diese Rolle ermöglicht die vollständige Kontrolle über alle Ressourcen im Cluster und in allen Namespaces. |


Rollenzuweisungen, die für den **gesamten AKS-Cluster** gelten, können entweder auf dem Blatt „Zugriffssteuerung (IAM)“ der Clusterressource im Azure-Portal oder mithilfe von Azure CLI-Befehlen wie unten gezeigt erfolgen:

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

Dabei kann `<AAD-ENTITY-ID>` ein Benutzername (z. B. user@contoso.com) oder sogar die ClientID eines Dienstprinzipals sein.

Sie können auch Rollenzuweisungen erstellen, die sich auf einen bestimmten **Namespace** innerhalb des Clusters beziehen:

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

Derzeit müssen Rollenzuweisungen, die für Namespaces gelten, über die Azure CLI konfiguriert werden.


### <a name="create-custom-roles-definitions"></a>Erstellen von benutzerdefinierten Rollendefinitionen

Optional können Sie auch eine eigene Rollendefinition erstellen und dann wie oben beschrieben zuweisen.

Im Folgenden finden Sie ein Beispiel für eine Rollendefinition, die es Benutzern ermöglicht, nur Bereitstellungen zu lesen. Andere Berechtigungen bestehen nicht. Sie können die vollständige Liste der möglichen Aktionen [hier](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice) überprüfen.


Kopieren Sie den folgenden JSON-Code in eine Datei mit dem Namen `deploy-view.json`.

```json
{
    "Name": "AKS Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"  
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]   
}
```

Ersetzen Sie `<YOUR SUBSCRIPTION ID>` durch die ID aus Ihrem Abonnement, die Sie durch Ausführen des folgenden Befehls abrufen können:

```azurecli-interactive
az account show --query id -o tsv
```


Nun können Sie die Rollendefinition erstellen, indem Sie den folgenden Befehl aus dem Ordner ausführen, in dem Sie `deploy-view.json` gespeichert haben:

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

Da Sie nun über Ihre Rollendefinition verfügen, können Sie sie einem Benutzer oder einer anderen Identität zuweisen, indem Sie Folgendes ausführen:

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>Verwenden von Azure RBAC für Kubernetes-Autorisierung mit `kubectl`

> [!NOTE]
> Vergewissern Sie sich, dass die aktuelle Version von kubectl verwendet wird, indem Sie den folgenden Befehl ausführen:
>
> ```azurecli-interactive
> az aks install-cli
> ```
> Möglicherweise müssen Sie ihn mit `sudo`-Berechtigungen ausführen. 

Nun haben Sie Ihre gewünschte Rolle und Berechtigungen zugewiesen. Sie können mit dem Aufrufen der Kubernetes-API beginnen, z. B. aus `kubectl`.

Zu diesem Zweck rufen Sie zunächst die kubeconfig-Datei des Clusters mit dem folgenden Befehl ab:

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> Sie benötigen die integrierte Rolle [Azure Kubernetes Service-Clusterbenutzer](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role), um den oben beschriebenen Schritt auszuführen.

Nun können Sie kubectl beispielsweise zum Auflisten der Knoten im Cluster verwenden. Bei der ersten Ausführung müssen Sie sich anmelden, und nachfolgende Befehle verwenden das entsprechende Zugriffstoken.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>Verwenden von Azure RBAC für Kubernetes-Autorisierung mit `kubelogin`

Um zusätzliche Szenarien wie nicht interaktive Anmeldungen, ältere `kubectl`-Versionen oder die Nutzung von SSO in mehreren Clustern freizugeben, ohne dass Sie sich bei einem neuen Cluster anmelden müssen, hat AKS ein Ausführungs-Plug-In namens [`kubelogin`](https://github.com/Azure/kubelogin) erstellt (vorausgesetzt, dass Ihr Token noch gültig ist).

Sie können das Plug-In verwenden, indem Sie Folgendes ausführen:

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

Beim ersten Mal müssen Sie sich interaktiv wie bei regulärem kubectl anmelden. Danach ist dies aber nicht mehr erforderlich, auch nicht bei neuen Azure AD-Clustern (solange das Token noch gültig ist).

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>Bereinigung

### <a name="clean-role-assignment"></a>Bereinigen der Rollenzuweisung

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
Kopieren Sie die ID oder IDs aus allen Zuweisungen, die Sie vorgenommen haben.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>Bereinigen der Rollendefinition

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>Löschen des Clusters und der Ressourcengruppe

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu AKS-Authentifizierung und -Autorisierung, Kubernetes RBAC und Azure RBAC finden Sie [hier](concepts-identity.md).
- Lesen Sie [hier](../role-based-access-control/overview.md) mehr zum Thema Azure RBAC.
- Lesen Sie [hier](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice) mehr über alle Aktionen, die Sie verwenden können, um benutzerdefinierte Azure-Rollen für die Kubernetes-Autorisierung detailliert zu definieren.


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli&preserve-view=true
