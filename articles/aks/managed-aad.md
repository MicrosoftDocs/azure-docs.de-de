---
title: Verwenden von Azure AD in Azure Kubernetes Service
description: Erfahren Sie, wie Sie Azure AD in Azure Kubernetes Service (AKS) verwenden.
services: container-service
ms.topic: article
ms.date: 08/26/2020
ms.author: thomasge
ms.openlocfilehash: f229075d0bad4f9522e02e30bdabc1d42bb086cf
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684184"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Von AKS verwaltete Azure Active Directory-Integration

Die von AKS verwaltete Azure AD-Integration vereinfacht deren Nutzung, weil es für Benutzer nicht mehr erforderlich ist, eine Client-App und eine Server-App zu erstellen und dem Azure AD-Mandanten Leseberechtigungen für Verzeichnisse zu gewähren. In der neuen Version verwaltet der AKS-Ressourcenanbieter die Client- und Server-App.

## <a name="azure-ad-authentication-overview"></a>Übersicht über die Azure AD-Authentifizierung

Clusteradministratoren können die rollenbasierte Zugriffssteuerung von Kubernetes (Kubernetes RBAC) auf Grundlage einer Benutzeridentität oder Verzeichnisgruppenmitgliedschaft konfigurieren. Die Azure AD-Authentifizierung wird für AKS-Cluster mit OpenID Connect bereitgestellt. OpenID Connect ist eine Identitätsebene, die auf dem OAuth 2.0-Protokoll aufbaut. Weitere Informationen zu OpenID Connect finden Sie in der [OpenID Connect-Dokumentation][open-id-connect].

Informieren Sie sich in der [Dokumentation zu den Konzepten der Azure Active Directory-Integration](concepts-identity.md#azure-active-directory-integration) über den Ablauf der Azure AD-Integration.

## <a name="limitations"></a>Einschränkungen 

* Die von AKS verwaltete Azure AD-Integration kann nicht deaktiviert werden.
* Bei nicht für Kubernetes RBAC aktivierten Clustern wird die von AKS verwaltete Azure AD-Integration nicht unterstützt.
* Die Änderung des mit der von AKS verwalteten Azure AD-Integration verknüpften Azure AD-Mandanten wird nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Befehlszeilenschnittstelle ab Version 2.11.0
* kubectl mit der Mindestversion [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) oder [kubelogin](https://github.com/Azure/kubelogin)
* Wenn Sie [Helm](https://github.com/helm/helm) verwenden, benötigen Sie mindestens Version 3.3 von Helm.

> [!Important]
> Sie müssen kubectl mit der Mindestversion 1.18.1 verwenden. Wenn Sie nicht die richtige Version verwenden, können Authentifizierungsprobleme auftreten.

Verwenden Sie zum Installieren von kubectl und kubelogin die folgenden Befehle:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

Verwenden Sie für andere Betriebssysteme [diese Anweisungen](https://kubernetes.io/docs/tasks/tools/install-kubectl/).


## <a name="before-you-begin"></a>Voraussetzungen

Für Ihren Cluster benötigen Sie eine Azure AD-Gruppe. Diese Gruppe wird als Administratorengruppe benötigt, damit dem Cluster Administratorberechtigungen für den Cluster erteilt werden können. Sie können eine vorhandene Azure AD-Gruppe verwenden oder eine neue erstellen. Notieren Sie die Objekt-ID Ihrer Azure AD-Gruppe.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Verwenden Sie den folgenden Befehl, um eine neue Azure AD-Gruppe für Ihre Clusteradministratoren zu erstellen:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Erstellen eines AKS-Clusters mit aktiviertem Azure AD

Erstellen Sie mit den folgenden CLI-Befehlen einen AKS-Cluster.

Erstellen Sie eine Azure-Ressourcengruppe:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Erstellen Sie einen AKS-Cluster, und ermöglichen Sie den Verwaltungszugriff für Ihre Azure AD-Gruppe.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Eine erfolgreiche Erstellung eines von AKS verwalteten Azure AD-Clusters wird im Antworttext mit dem folgenden Abschnitt angegeben:
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Sobald der Cluster erstellt ist, können Sie darauf zugreifen.

## <a name="access-an-azure-ad-enabled-cluster"></a>Zugreifen auf einen Azure AD-fähigen Cluster

Sie benötigen die integrierte Rolle [Azure Kubernetes Service-Clusterbenutzer](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role), um die unten angegebenen Schritte auszuführen.

Rufen Sie die Benutzeranmeldeinformationen für den Zugriff auf den Cluster ab:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Befolgen Sie die Anweisungen zum Anmelden.

Verwenden Sie den Befehl „kubectl get nodes“, um die Knoten im Cluster anzuzeigen:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Konfigurieren Sie die [rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, Azure RBAC)](./azure-ad-rbac.md), um zusätzliche Sicherheitsgruppen für Ihre Cluster zu konfigurieren.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Behandeln von Zugriffsproblemen mit Azure AD

> [!Important]
> In den unten beschriebenen Schritten wird die reguläre Azure AD-Gruppenauthentifizierung umgangen. Wenden Sie dies nur in Notfällen an.

Falls Sie dauerhaft blockiert werden, weil Sie nicht über Zugriff auf eine gültige Azure AD-Gruppe mit Zugriff auf Ihren Cluster verfügen, können Sie trotzdem die Administratoranmeldeinformationen abrufen, um direkt auf den Cluster zuzugreifen.

Zum Ausführen dieser Schritte benötigen Sie Zugriff auf die integrierte [Administratorrolle für Azure Kubernetes Service-Cluster](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role).

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Aktivieren der von AKS verwalteten Azure AD-Integration in einem vorhandenen Cluster

Sie können die von AKS verwaltete Azure AD-Integration in einem vorhandenen Cluster mit Kubernetes RBAC aktivieren. Stellen Sie sicher, dass Ihre Administratorengruppe Zugriff auf Ihren Cluster behält.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

Eine erfolgreiche Aktivierung eines von AKS verwalteten Azure AD-Clusters wird im Antworttext mit dem folgenden Abschnitt angegeben:

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Laden Sie die Benutzeranmeldeinformationen erneut herunter, um auf Ihren Cluster zuzugreifen, indem Sie [diese Schritte][access-cluster] ausführen.

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Upgrade auf die von AKS verwaltete Azure AD-Integration

Wenn in Ihrem Cluster eine Azure AD-Legacy-Integration verwendet wird, können Sie ein Upgrade auf die von AKS verwaltete AD-Integration durchführen.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Eine erfolgreiche Migration eines von AKS verwalteten Azure AD-Clusters wird im Antworttext mit dem folgenden Abschnitt angegeben:

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Wenn Sie auf den Cluster zugreifen möchten, befolgen Sie die [hier][access-cluster] beschriebenen Schritte.

## <a name="non-interactive-sign-in-with-kubelogin"></a>Nicht interaktive Anmeldung per kubelogin

Es gibt einige nicht interaktive Szenarien, z. B. Continuous Integration-Pipelines, die für Kubectl derzeit nicht verfügbar sind. Sie können [`kubelogin`](https://github.com/Azure/kubelogin) verwenden, um über eine nicht interaktive Dienstprinzipalanmeldung auf den Cluster zuzugreifen.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über die [Verwendung der Azure RBAC-Integration für die Kubernetes-Autorisierung][azure-rbac-integration].
* Informieren Sie sich über die [Azure AD-Integration mit Kubernetes RBAC][azure-ad-rbac].
* Verwenden Sie [kubelogin](https://github.com/Azure/kubelogin), um auf Features der Azure-Authentifizierung zuzugreifen, die in Kubectl nicht verfügbar sind.
* Informieren Sie sich über die [Identitätskonzepte für AKS und Kubernetes][aks-concepts-identity].
* Erstellen Sie mit [Azure Resource Manager-Vorlagen (ARM)][aks-arm-template] von AKS verwaltete Cluster, die für Azure AD geeignet sind.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
