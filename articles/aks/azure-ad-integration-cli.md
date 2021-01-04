---
title: Integrieren von Azure Active Directory in Azure Kubernetes Service (Legacy)
description: Erfahren Sie, wie Sie mit der Azure-Befehlszeilenschnittstelle (Azure CLI) einen Azure Active Directory-fähigen Azure Kubernetes Service-Cluster (AKS-Cluster) erstellen (Legacy).
services: container-service
author: TomGeske
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: 4aa63493bb14db69821ac04db1d2c5a846de7dbe
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682467"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli-legacy"></a>Integrieren von Azure Active Directory in Azure Kubernetes Service mit Azure CLI (Legacy)

Azure Kubernetes Service (AKS) kann für die Verwendung von Azure Active Directory (AD) für die Benutzerauthentifizierung konfiguriert werden. In dieser Konfiguration melden Sie sich an einem AKS-Cluster über ein Azure AD-Authentifizierungstoken an. Clusterbetreiber können auch die rollenbasierte Zugriffssteuerung von Kubernetes (Kubernetes RBAC) auf der Grundlage einer Benutzeridentität oder Verzeichnisgruppenmitgliedschaft konfigurieren.

In diesem Artikel wird erläutert, wie Sie die erforderlichen Azure AD-Komponenten erstellen, anschließend einen Azure AD-fähigen Cluster bereitstellen und dann eine grundlegende Kubernetes-Rolle im AKS-Cluster erstellen.

Die vollständiges Beispielskript, das in diesem Artikel verwendet wird, finden Sie unter [Azure CLI-Beispiele: AKS-Integration in Azure AD][complete-script].

> [!Important]
> AKS verfügt über eine neue verbesserte [von AKS verwaltete Azure AD][managed-aad]-Umgebung, in der Sie die Server- oder Clientanwendung nicht verwalten müssen. Wenn Sie die Migration durchführen möchten, folgen Sie den [hier angegebenen Anweisungen][managed-aad-migrate].

## <a name="the-following-limitations-apply"></a>Es gelten die folgenden Einschränkungen:

- Azure AD kann nur für Kubernetes RBAC-fähige Cluster aktiviert werden.
- Die Integration von Legacy-Azure AD kann nur während der Clustererstellung aktiviert werden.

## <a name="before-you-begin"></a>Voraussetzungen

Azure CLI-Version 2.0.61 oder höher muss installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

Wechseln Sie zu [https://shell.azure.com](https://shell.azure.com), um Cloud Shell in Ihrem Browser zu öffnen.

Aus Konsistenzgründen und um die Ausführung der Befehle in diesem Artikel zu erleichtern, erstellen Sie eine Variable für den gewünschten AKS-Clusternamen. Im folgenden Beispiel wird der Name *myakscluster* verwendet:

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Übersicht über die Azure AD-Authentifizierung

Die Azure AD-Authentifizierung wird für AKS-Cluster mit OpenID Connect bereitgestellt. OpenID Connect ist eine Identitätsebene, die auf dem OAuth 2.0-Protokoll aufbaut. Weitere Informationen zu OpenID Connect finden Sie in der [OpenID Connect-Dokumentation][open-id-connect].

Innerhalb des Kubernetes-Clusters werden Authentifizierungstoken mithilfe der Webhooktokenauthentifizierung überprüft. Die Webhooktokenauthentifizierung wird als Teil des AKS-Clusters konfiguriert und verwaltet. Weitere Informationen zur Webhooktokenauthentifizierung finden Sie in der [Dokumentation zur Webhookauthentifizierung][kubernetes-webhook].

> [!NOTE]
> Bei der Konfiguration von Azure AD für die AKS-Authentifizierung werden zwei Azure AD-Anwendungen konfiguriert. Dieser Vorgang muss von einem Azure-Mandantenadministrator ausgeführt werden.

## <a name="create-azure-ad-server-component"></a>Erstellen der Azure AD-Serverkomponente

Für die Integration mit AKS erstellen und verwenden Sie eine Azure AD-Anwendung, die als Endpunkt für die Identitätsanforderungen fungiert. Die erste benötigte Azure AD-Anwendung erhält die Azure AD-Gruppenmitgliedschaft für einen Benutzer.

Erstellen Sie die Serveranwendungskomponente mit dem Befehl [az ad app create][az-ad-app-create], und aktualisieren Sie anschließend die Gruppenmitgliedschaftsansprüche mit dem Befehl [az ad app update][az-ad-app-update]. Im folgenden Beispiel wird die Variable *aksname* (im Abschnitt [Voraussetzungen](#before-you-begin) definiert) verwendet, und es wird eine Variable erstellt.

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group membership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Erstellen Sie nun mit dem Befehl [az ad sp create][az-ad-sp-create] einen Dienstprinzipal für die Server-App. Dieser Dienstprinzipal authentifiziert sich in der Azure Platform. Rufen Sie anschließend mit dem Befehl [az ad sp credential reset][az-ad-sp-credential-reset] das Dienstprinzipalgeheimnis ab, und weisen Sie es der Variablen *serverApplicationSecret* für die Verwendung in einem der folgenden Schritte zu:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Der Azure AD-Dienstprinzipal benötigt die Berechtigungen zum Ausführen der folgenden Aktionen:

* Verzeichnisdaten lesen
* Anmelden und Benutzerprofil lesen

Weisen Sie diese Berechtigungen mit dem Befehl [az ad app permission add][az-ad-app-permission-add] zu:

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Gewähren Sie schließlich mit dem Befehl [az ad app permission grant][az-ad-app-permission-grant] die im vorherigen Schritt zugewiesenen Berechtigungen für die Serveranwendung. Dieser Schritt schlägt fehl, wenn das aktuelle Konto keinem Mandantenadministrator gehört. Sie müssen auch Berechtigungen für die Azure AD-Anwendung hinzufügen, um Informationen anzufordern, für die andernfalls die Zustimmung des Administrators erforderlich wäre. Verwenden Sie dazu den Befehl [az ad app permission admin-consent][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Erstellen der Azure AD-Clientkomponente

Die zweite Azure AD-Anwendung wird beim Anmelden eines Benutzers beim AKS-Cluster mit der Kubernetes-CLI (`kubectl`) verwendet. Diese Clientanwendung empfängt die Authentifizierungsanforderung vom Benutzer und überprüft dessen Anmeldeinformationen und Berechtigungen. Erstellen Sie mit dem Befehl [az ad app create][az-ad-app-create] die Azure AD-App für die Clientkomponente:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Erstellen Sie mit dem Befehl [az ad sp create][az-ad-sp-create] einen Dienstprinzipal für die Clientanwendung:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Rufen Sie mit dem Befehl [az ad app show][az-ad-app-show] die oAuth2-ID für die Server-App ab, um den Authentifizierungsflow zwischen den beiden App-Komponenten zu ermöglichen. Diese oAuth2-ID wird im nächsten Schritt verwendet.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Fügen Sie mit dem Befehl [az ad app permission add][az-ad-app-permission-add] die Berechtigungen für die Client- und Serveranwendungskomponenten hinzu, um den Kommunikationsfluss oAuth2 zu verwenden: Gewähren Sie anschließend mit dem Befehl [az ad app permission grant][az-ad-app-permission-grant] Berechtigungen für die Clientanwendung zur Kommunikation mit der Serveranwendung:

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Bereitstellen des Clusters

Die beiden Azure AD-Anwendungen sind nun erstellt. Jetzt müssen Sie den AKS-Cluster selbst erstellen. Erstellen Sie zuerst mit dem Befehl [az group create][az-group-create] eine Ressourcengruppe. Im folgenden Beispiel wird die Ressourcengruppe in der Region *EastUS* erstellt:

Erstellen einer Ressourcengruppe für den Cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Rufen Sie mit dem Befehl [az account show][az-account-show] die Mandanten-ID Ihres Azure-Abonnements ab. Erstellen Sie anschließend mit dem Befehl [az aks create][az-aks-create] den AKS-Cluster. Im Befehl zum Erstellen des AKS-Clusters werden die IDs der Server- und der Clientanwendung, das Dienstprinzipal-Geheimnis der Serveranwendung und Ihre Mandanten-ID angegeben:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Rufen Sie schließlich mit dem Befehl [az aks get-credentials][az-aks-get-credentials] die Anmeldeinformationen für den Clusteradministrator ab. In einem der folgenden Schritte rufen Sie die Clusteranmeldeinformationen für reguläre *Benutzer* ab, um den Azure AD-Authentifizierungsablauf in Aktion sehen zu können.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-kubernetes-rbac-binding"></a>Erstellen einer Kubernetes RBAC-Bindung

Bevor ein Azure Active Directory-Konto mit dem AKS-Cluster verwendet werden kann, muss eine Rollenbindung oder eine Clusterrollenbindung erstellt werden. *Rollen* definieren die zu erteilenden Berechtigungen, und *Bindungen* wenden diese auf die gewünschten Benutzer an. Diese Zuweisungen können auf einen bestimmten Namespace oder im gesamten Cluster angewendet werden. Weitere Informationen finden Sie unter [Verwenden der Kubernetes RBAC-Autorisierung][rbac-authorization].

Rufen Sie mit dem Befehl [az ad signed-in-user show][az-ad-signed-in-user-show] den Benutzerprinzipalnamen (UPN) für den derzeit angemeldeten Benutzer ab. Dieses Benutzerkonto wird im nächsten Schritt für die Azure AD-Integration aktiviert.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Wenn sich der Benutzer, für den die Kubernetes RBAC-Bindung gewährt wird, auf demselben Azure AD-Mandanten befindet, sollten Sie Berechtigungen gemäß *userPrincipalName* zuweisen. Befindet sich der Benutzer in einem anderen Azure AD-Mandanten, müssen Sie stattdessen die *objectId*-Eigenschaft abfragen und verwenden.

Erstellen Sie ein YAML-Manifest mit dem Namen `basic-azure-ad-binding.yaml`, und fügen Sie die folgenden Inhalte ein. Ersetzen Sie in der letzten Zeile *userPrincipalName_or_objectId* durch den UPN oder die Objekt-ID-Ausgabe des vorherigen Befehls:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Erstellen Sie die ClusterRoleBinding mit dem Befehl [kubectl apply][kubectl-apply], und geben Sie den Dateinamen Ihres YAML-Manifests an:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Zugriff auf Cluster mit Azure AD

Testen wir nun die Integration der Azure AD-Authentifizierung für den AKS-Cluster. Legen Sie den `kubectl`-Konfigurationskontext auf die Verwendung regulärer Benutzeranmeldeinformationen fest. Dieser Kontext übergibt alle Authentifizierungsanforderungen über Azure AD zurück.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Verwenden Sie nun den Befehl [kubectl get pods][kubectl-get], um Pods für alle Namespaces anzuzeigen:

```console
kubectl get pods --all-namespaces
```

Sie erhalten eine Anmeldeaufforderung, sich in einem Webbrowser mit Azure AD-Anmeldeinformationen zu authentifizieren. Nach erfolgreicher Authentifizierung zeigt der Befehl `kubectl` die Pods im AKS-Cluster an, wie in der folgenden Beispielausgabe veranschaulicht:

```console
kubectl get pods --all-namespaces
```

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

Das für `kubectl` empfangene Authentifizierungstoken wird zwischengespeichert. Sie werden nur erneut aufgefordert, sich anzumelden, wenn das Token abgelaufen ist oder die Kubernetes-Konfigurationsdatei neu erstellt wird.

Wenn wie in der folgenden Beispielausgabe nach erfolgreicher Anmeldung im Webbrowser eine Meldung zu einem Authentifizierungsfehler angezeigt wird, untersuchen Sie die folgenden möglichen Probleme:

```output
error: You must be logged in to the server (Unauthorized)
```

* Sie haben die entsprechende Objekt-ID oder den UPN definiert, je nachdem, ob das Benutzerkonto im gleichen Azure AD-Mandanten vorhanden ist oder nicht.
* Der Benutzer ist kein Mitglied der über 200 Gruppen.
* Das Geheimnis, das in der Anwendungsregistrierung für den Server definiert ist, entspricht dem mit `--aad-server-app-secret` konfigurierten Wert.

## <a name="next-steps"></a>Nächste Schritte

Das vollständige Skript mit den Befehlen aus diesem Artikel finden Sie im [Skript zur Azure AD-Integration im Repository mit AKS-Beispielen][complete-script].

Informationen zur Verwendung von Azure AD-Benutzern und -Gruppen für die Steuerung des Zugriffs auf Clusterressourcen finden Sie unter [Steuern des Zugriffs auf Clusterressourcen per rollenbasierter Zugriffssteuerung von Kubernetes und mit Azure Active Directory-Identitäten in Azure Kubernetes Service][azure-ad-rbac].

Weitere Informationen zum Schutz von Kubernetes-Clustern finden Sie unter [Zugriffs- und Identitätsoptionen für AKS][rbac-authorization].

Best Practices zur Identitäts- und Ressourcenkontrolle finden Sie unter [Best Practices für Authentifizierung und Autorisierung in AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[managed-aad]: managed-aad.md
[managed-aad-migrate]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
