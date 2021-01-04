---
title: Beschränken des Zugriffs auf die Kubernetes-Konfiguration (kubeconfig) in Azure Kubernetes Service (AKS)
description: Hier erfahren Sie, wie Sie den Zugriff auf die Kubernetes-Konfigurationsdatei (kubeconfig) für Clusteradministratoren und Clusterbenutzer steuern.
services: container-service
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: 371628b02ebecee23697e996ee0d484688167875
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684813"
---
# <a name="use-azure-role-based-access-control-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Definieren des Zugriffs auf die Kubernetes-Konfigurationsdatei in Azure Kubernetes Service (AKS) mithilfe der rollenbasierten Zugriffssteuerung von Azure

Sie können das Tool `kubectl` verwenden, um mit Kubernetes-Clustern zu interagieren. Über die Azure-Befehlszeilenschnittstelle können Sie ganz einfach die Anmeldeinformationen für den Zugriff sowie die Konfigurationsinformationen abrufen, die Sie benötigen, um über `kubectl` eine Verbindung mit Ihren AKS-Clustern herzustellen. Mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) können Sie steuern, wer die Kubernetes-Konfigurationsinformationen (*kubeconfig*) abrufen kann und über welche Berechtigungen diese Personen anschließend verfügen sollen.

In diesem Artikel erfahren Sie, wie Sie Azure-Rollen zuweisen, die einschränken, wer die Konfigurationsinformationen für einen AKS-Cluster abrufen kann.

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

Für den Artikel wird außerdem mindestens Version 2.0.65 der Azure-Befehlszeilenschnittstelle benötigt. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Verfügbare Clusterrollenberechtigungen

Wenn Sie das Tool `kubectl` verwenden, um mit einem AKS-Cluster zu interagieren, wird eine Konfigurationsdatei verwendet, die Clusterverbindungsinformationen definiert. Diese Konfigurationsdatei ist üblicherweise unter *~/.kube/config* gespeichert. In dieser Datei vom Typ *kubeconfig* können mehrere Cluster definiert werden. Verwenden Sie den Befehl [kubectl config use-context][kubectl-config-use-context], um zwischen Clustern zu wechseln.

Der Befehl [az aks get-credentials][az-aks-get-credentials] ermöglicht das Abrufen der Anmeldeinformationen für den Zugriff auf einen AKS-Cluster und führt sie in der Datei *kubeconfig* zusammen. Der Zugriff auf diese Anmeldeinformationen kann mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) gesteuert werden. Mithilfe dieser Azure-Rollen können Sie definieren, wer die Datei *kubeconfig* abrufen kann und über welche Berechtigungen diese Personen innerhalb des Clusters verfügen sollen.

Es stehen zwei integrierte Rollen zur Verfügung:

* **Administratorrolle für Azure Kubernetes Service-Cluster**  
  * Ermöglicht den Zugriff auf den API-Aufruf *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action*. Dieser API-Aufruf [führt die Administratoranmeldeinformationen für den Cluster auf][api-cluster-admin].
  * Lädt *kubeconfig* für die Rolle *clusterAdmin* herunter.
* **Benutzerrolle für Azure Kubernetes Service-Cluster**
  * Ermöglicht den Zugriff auf den API-Aufruf *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action*. Dieser API-Aufruf [führt die Benutzeranmeldeinformationen für den Cluster auf][api-cluster-user].
  * Lädt *kubeconfig* für die Rolle *clusterUser* herunter.

Diese Azure-Rollen können einem Azure Active Directory-Benutzer (AAD) oder einer AAD-Gruppe zugewiesen werden.

> [!NOTE]
> In Clustern, die Azure AD verwenden, verfügen Benutzer mit der Rolle *clusterUser* über eine leere Datei *kubeconfig*, die zur Anmeldung auffordert. Nachdem die Benutzer angemeldet sind, haben Sie Zugriff basierend auf ihren Azure AD-Benutzer- oder -Gruppeneinstellungen. Benutzer mit der Rolle *clusterAdmin* haben Administratorzugriff.
>
> Cluster, die Azure AD nicht verwenden, verwenden nur die Rolle *clusterAdmin*.

## <a name="assign-role-permissions-to-a-user-or-group"></a>Zuweisen von Rollenberechtigungen zu einem Benutzer oder einer Gruppe

Wenn Sie eine der Azure-Rollen zuweisen möchten, benötigen Sie die Ressourcen-ID des AKS-Clusters und die ID des Azure AD-Benutzerkontos oder der Azure AD-Gruppe. Die folgenden Beispielbefehle:

* Abrufen der Clusterressourcen-ID mithilfe des Befehls [az aks show][az-aks-show] für den Cluster *myAKSCluster* in der Ressourcengruppe *myResourceGroup*. Geben Sie nach Bedarf Ihren eigenen Cluster- und Ressourcengruppennamen an.
* Verwenden Sie die Befehle [az account show][az-account-show] und [az ad user show][az-ad-user-show], um Ihre Benutzer-ID abzurufen.
* Weisen Sie schließlich eine Rolle mithilfe des Befehls [az role assignment create][az-role-assignment-create] zu.

Im folgenden Beispiel wird die *Administratorrolle für Azure Kubernetes Service-Cluster* einem einzelnen Benutzerkonto zugewiesen:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Wenn Sie einer Azure AD-Gruppe Berechtigungen zuweisen möchten, aktualisieren Sie den im vorherigen Beispiel gezeigten Parameter `--assignee` mit der Objekt-ID der *Gruppe* anstatt der eines *Benutzers*. Verwenden Sie zum Abrufen der Objekt-ID einer Gruppe den Befehl [az ad group show][az-ad-group-show]. Das folgende Beispiel ruft die Objekt-ID der Azure AD-Gruppe namens *appdev* ab: `az ad group show --group appdev --query objectId -o tsv`

Bei Bedarf können Sie die vorherige Zuweisung für die *Clusterbenutzerrolle* ändern.

Die folgende Beispielausgabe zeigt, dass die Rollenzuweisung erfolgreich erstellt wurde:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>Abrufen und Überprüfen der Konfigurationsinformationen

Rufen Sie nach dem Zuweisen von Azure-Rollen mithilfe des Befehls [az aks get-credentials][az-aks-get-credentials] die Definition der Kubernetes-Konfiguration (*kubeconfig*) für Ihren AKS-Cluster ab. Im folgenden Beispiel werden die Anmeldeinformationen vom Typ *--admin* abgerufen, die ordnungsgemäß funktionieren, wenn dem Benutzer die *Clusteradministratorrolle* erteilt wurde:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Anschließend können Sie sich mithilfe des Befehls [kubectl config view][kubectl-config-view] anhand des *Kontexts* für den Cluster vergewissern, dass die Administratorkonfigurationsinformationen angewendet wurden:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Entfernen von Rollenberechtigungen

Rollenzuweisungen können mithilfe des Befehls [az role assignment delete][az-role-assignment-delete] entfernt werden. Geben Sie die mit den vorherigen Befehlen abgerufene Konto-ID und Clusterressourcen-ID an. Wenn Sie die Rolle nicht einem Benutzer, sondern einer Gruppe zugewiesen haben, geben Sie für den Parameter `--assignee` die entsprechende Gruppenobjekt-ID und nicht die Kontoobjekt-ID an:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Nächste Schritte

[Integrieren Sie die Azure Active Directory-Authentifizierung][aad-integration], um die Sicherheit des Zugriffs auf AKS-Cluster zu verbessern.

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: ./azure-ad-integration-cli.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
