---
title: Verwenden von Azure AD und Kubernetes RBAC für Cluster
titleSuffix: Azure Kubernetes Service
description: Es wird beschrieben, wie Sie die Azure Active Directory-Gruppenmitgliedschaft verwenden, um den Zugriff auf Clusterressourcen per rollenbasierter Zugriffssteuerung von Kubernetes (Kubernetes RBAC) in Azure Kubernetes Service (AKS) zu beschränken.
services: container-service
ms.topic: article
ms.date: 07/21/2020
ms.openlocfilehash: 89bf785a082ff479dcd824a773123fcd061dc22d
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576110"
---
# <a name="control-access-to-cluster-resources-using-kubernetes-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Steuern des Zugriffs auf Clusterressourcen per rollenbasierter Zugriffssteuerung von Kubernetes und mit Azure Active Directory-Identitäten in Azure Kubernetes Service

Azure Kubernetes Service (AKS) kann für die Verwendung von Azure Active Directory (AD) für die Benutzerauthentifizierung konfiguriert werden. In dieser Konfiguration melden Sie sich an einem AKS-Cluster über ein Azure AD-Authentifizierungstoken an. Sie können auch die rollenbasierte Zugriffssteuerung von Kubernetes (Kubernetes RBAC) konfigurieren, um den Zugriff auf Clusterressourcen anhand der Identität oder Gruppenmitgliedschaft eines Benutzers zu beschränken.

In diesem Artikel wird veranschaulicht, wie Sie die Azure AD-Gruppenmitgliedschaft verwenden, um den Zugriff auf Namespaces und Clusterressourcen per Kubernetes RBAC in einem AKS-Cluster zu steuern. In Azure werden Beispielgruppen und -benutzer erstellt, und anschließend werden im AKS-Cluster Rollen und Rollenbindungen (RoleBindings) erstellt, um die entsprechenden Berechtigungen zum Erstellen und Anzeigen von Ressourcen zu gewähren.

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie über einen vorhandenen AKS-Cluster mit aktivierter Azure AD-Integration verfügen. Falls Sie einen AKS-Cluster benötigen, lesen Sie die Informationen unter [Integrieren von Azure Active Directory in AKS][azure-ad-aks-cli].

Azure CLI-Version 2.0.61 oder höher muss installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Erstellen von Demogruppen in Azure AD

In diesem Artikel erstellen wir zwei Benutzerrollen, mit denen veranschaulicht werden kann, wie mit Kubernetes RBAC und Azure AD der Zugriff auf Clusterressourcen gesteuert wird. Die beiden folgenden Beispielrollen werden verwendet:

* **Anwendungsentwickler**
    * Ein Benutzer mit dem Namen *aksdev*, der der Gruppe *appdev* angehört.
* **Websitezuverlässigkeits-Techniker (Site Reliability Engineer, SRE)**
    * Ein Benutzer mit dem Namen *akssre*, der der Gruppe *opssre* angehört.

In Produktionsumgebungen können Sie vorhandene Benutzer und Gruppen in einem Azure AD-Mandanten nutzen.

Rufen Sie zuerst die Ressourcen-ID Ihres AKS-Clusters ab, indem Sie den Befehl [az aks show][az-aks-show] verwenden. Weisen Sie die Ressourcen-ID einer Variablen mit dem Namen *AKS_ID* zu, damit in weiteren Befehlen darauf verwiesen werden kann.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Erstellen Sie die erste Beispielgruppe in Azure AD für die Anwendungsentwickler, indem Sie den Befehl [az ad group create][az-ad-group-create]verwenden. Im folgenden Beispiel wird eine Gruppe mit dem Namen *appdev* erstellt:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Erstellen Sie nun eine Azure-Rollenzuweisung für die Gruppe *appdev*, indem Sie den Befehl [az role assignment create][az-role-assignment-create] verwenden. Bei dieser Zuweisung können alle Mitglieder der Gruppe `kubectl` zum Interagieren mit einem AKS-Cluster nutzen, wenn ihnen die *Benutzerrolle für Azure Kubernetes Service-Cluster* gewährt wird.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Wenn Sie einen Fehler erhalten, z. B. `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`, sollten Sie einige Sekunden warten, bis die ID des Azure AD-Gruppenobjekts im Verzeichnis verteilt wurde. Versuchen Sie dann erneut, den Befehl `az role assignment create` auszuführen.

Erstellen Sie eine zweite Beispielgruppe, und zwar für Websitezuverlässigkeits-Techniker mit dem Namen *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Erstellen Sie wieder eine Azure-Rollenzuweisung, um Mitgliedern der Gruppe die *Benutzerrolle für Azure Kubernetes Service-Cluster* zu gewähren:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Erstellen von Demobenutzern in Azure AD

Nachdem wir in Azure AD zwei Beispielgruppen für Anwendungsentwickler und Websitezuverlässigkeits-Techniker erstellt haben, können wir jetzt zwei Beispielbenutzer erstellen. Um am Ende des Artikels die Integration von Kubernetes RBAC zu testen, melden Sie sich mit diesen Konten am AKS-Cluster an.

Erstellen Sie das erste Benutzerkonto in Azure AD mit dem Befehl [az ad user create][az-ad-user-create].

Im folgenden Beispiel wird ein Benutzer mit dem Anzeigenamen *AKS Dev* und dem Benutzerprinzipalnamen (UPN) `aksdev@contoso.com` erstellt. Aktualisieren Sie den Benutzerprinzipalnamen, damit er eine verifizierte Domäne für Ihren Azure AD-Mandanten enthält (ersetzen Sie *contoso.com* durch Ihre eigene Domäne), und geben Sie Ihre eigene sichere Anmeldeinformation `--password` an:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Fügen Sie den Benutzer nun der Gruppe *appdev* hinzu, die im vorherigen Abschnitt erstellt wurde, indem Sie den Befehl [az ad group member add][az-ad-group-member-add] verwenden:

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Erstellen Sie ein zweites Benutzerkonto. Im folgenden Beispiel wird ein Benutzer mit dem Anzeigenamen *AKS SRE* und dem Benutzerprinzipalnamen (UPN) `akssre@contoso.com` erstellt. Aktualisieren Sie erneut den Benutzerprinzipalnamen, damit er eine verifizierte Domäne für Ihren Azure AD-Mandanten enthält (ersetzen Sie *contoso.com* durch Ihre eigene Domäne), und geben Sie Ihre eigene sichere Anmeldeinformation `--password` an:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Erstellen der AKS-Clusterressourcen für Anwendungsentwickler

Die Azure AD-Gruppen und -Benutzer werden jetzt erstellt. Azure-Rollenzuweisungen wurden für die Gruppenmitglieder erstellt, um die Verbindung mit einem AKS-Cluster als regulärer Benutzer herzustellen. Wir konfigurieren den AKS-Cluster nun so, dass für diese unterschiedlichen Gruppen der Zugriff auf bestimmte Ressourcen zugelassen wird.

Rufen Sie zuerst die Anmeldeinformationen für den Clusteradministrator ab, indem Sie den Befehl [az aks get-credentials][az-aks-get-credentials] verwenden. In einem der folgenden Abschnitte rufen Sie die Clusteranmeldeinformationen für reguläre *Benutzer* ab, um den Azure AD-Authentifizierungsablauf in Aktion sehen zu können.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Erstellen Sie im AKS-Cluster einen Namespace, indem Sie den Befehl [kubectl create namespace][kubectl-create] verwenden. Im folgenden Beispiel wird der Namespacename *dev* erstellt:

```console
kubectl create namespace dev
```

In Kubernetes werden mit *Rollen* die zu gewährenden Berechtigungen definiert, und mit *RoleBindings* (Rollenbindungen) werden sie auf die gewünschten Benutzer bzw. Gruppen angewendet. Diese Zuweisungen können auf einen bestimmten Namespace oder im gesamten Cluster angewendet werden. Weitere Informationen finden Sie unter [Verwenden der Kubernetes RBAC-Autorisierung][rbac-authorization].

Erstellen Sie zuerst eine Rolle für den Namespace *dev*. Mit dieser Rolle wird Vollzugriff auf den Namespace gewährt. In Produktionsumgebungen können Sie für unterschiedliche Benutzer oder Gruppen präzisere Berechtigungen angeben.

Erstellen Sie eine Datei mit dem Namen `role-dev-namespace.yaml`, und fügen Sie das folgende YAML-Manifest ein:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Erstellen Sie die Rolle mit dem Befehl [kubectl apply][kubectl-apply], und geben Sie den Dateinamen Ihres YAML-Manifests an:

```console
kubectl apply -f role-dev-namespace.yaml
```

Rufen Sie als Nächstes die Ressourcen-ID für die Gruppe *appdev* ab, indem Sie den Befehl [az ad group show][az-ad-group-show] verwenden. Diese Gruppe wird im nächsten Schritt als subject-Element eines RoleBinding-Elements festgelegt.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Erstellen Sie nun ein RoleBinding-Element für die Gruppe *appdev*, um die zuvor erstellte Rolle für den Namespacezugriff zu verwenden. Erstellen Sie eine Datei namens `rolebinding-dev-namespace.yaml`, und fügen Sie das folgende YAML-Manifest ein. Ersetzen Sie in der letzten Zeile *groupObjectId* durch die Gruppenobjekt-ID, die vom vorherigen Befehl ausgegeben wird:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Erstellen Sie das RoleBinding-Element mit dem Befehl [kubectl apply][kubectl-apply], und geben Sie den Dateinamen Ihres YAML-Manifests an:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Erstellen der AKS-Clusterressourcen für Websitezuverlässigkeits-Techniker

Wiederholen Sie nun die vorherigen Schritte zum Erstellen eines Namespace, einer Rolle und eines RoleBinding-Elements für die Websitezuverlässigkeits-Techniker.

Erstellen Sie zunächst einen Namespace für *sre*, indem Sie den Befehl [kubectl create namespace][kubectl-create] verwenden:

```console
kubectl create namespace sre
```

Erstellen Sie eine Datei mit dem Namen `role-sre-namespace.yaml`, und fügen Sie das folgende YAML-Manifest ein:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Erstellen Sie die Rolle mit dem Befehl [kubectl apply][kubectl-apply], und geben Sie den Dateinamen Ihres YAML-Manifests an:

```console
kubectl apply -f role-sre-namespace.yaml
```

Rufen Sie die Ressourcen-ID für die Gruppe *opssre* ab, indem Sie den Befehl [az ad group show][az-ad-group-show] verwenden:

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Erstellen Sie ein RoleBinding-Element für die Gruppe *opssre*, um die zuvor erstellte Rolle für den Namespacezugriff zu verwenden. Erstellen Sie eine Datei namens `rolebinding-sre-namespace.yaml`, und fügen Sie das folgende YAML-Manifest ein. Ersetzen Sie in der letzten Zeile *groupObjectId* durch die Gruppenobjekt-ID, die vom vorherigen Befehl ausgegeben wird:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Erstellen Sie das RoleBinding-Element mit dem Befehl [kubectl apply][kubectl-apply], und geben Sie den Dateinamen Ihres YAML-Manifests an:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interagieren mit Clusterressourcen über Azure AD-Identitäten

Wir testen nun, ob die Berechtigungen wie erwartet funktionieren, wenn Sie in einem AKS-Cluster Ressourcen erstellen und verwalten. In diesen Beispielen planen Sie Pods im zugewiesenen Namespace des Benutzers und zeigen sie an. Anschließend versuchen Sie, Pods außerhalb des zugewiesenen Namespace zu planen und anzuzeigen.

Setzen Sie zunächst den Kontext *kubeconfig* zurück, indem Sie den Befehl [az aks get-credentials][az-aks-get-credentials] verwenden. In einem vorherigen Abschnitt haben Sie den Kontext mit den Anmeldeinformationen für den Clusteradministrator festgelegt. Für den Administratorbenutzer werden Azure AD-Anmeldeaufforderungen umgangen. Ohne den Parameter `--admin` wird der Benutzerkontext angewendet, für den alle Anforderungen per Azure AD authentifiziert werden müssen.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Planen Sie einen grundlegenden NGINX-Pod, indem Sie den Befehl [kubectl run][kubectl-run] im Namespace *dev* verwenden:

```console
kubectl run nginx-dev --image=nginx --namespace dev
```

Geben Sie an der Anmeldeaufforderung die Anmeldeinformationen für Ihr eigenes `appdev@contoso.com`-Konto ein, das Sie zu Beginn dieses Artikels erstellt haben. Nach der erfolgreichen Anmeldung wird das Kontotoken für weitere `kubectl`-Befehle zwischengespeichert. Die NGINX-Planung ist erfolgreich, wie die folgende Beispielausgabe zeigt:

```console
$ kubectl run nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Verwenden Sie nun den Befehl [kubectl get pods][kubectl-get], um die Pods im Namespace *dev* anzuzeigen.

```console
kubectl get pods --namespace dev
```

In der folgenden Beispielausgabe können Sie sehen, dass der NGINX-Pod den Status *Running* (Wird ausgeführt) erreicht hat:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Erstellen und Anzeigen von Clusterressourcen außerhalb des zugewiesenen Namespace

Versuchen Sie nun, Pods außerhalb des Namespace *dev* anzuzeigen. Verwenden Sie erneut den Befehl [kubectl get pods][kubectl-get], um nun `--all-namespaces` wie folgt anzuzeigen:

```console
kubectl get pods --all-namespaces
```

Die Gruppenmitgliedschaft des Benutzers verfügt nicht über eine Kubernetes-Rolle, die diese Aktion ermöglicht. Dies ist in der folgenden Beispielausgabe dargestellt:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Versuchen Sie auf die gleiche Weise, einen Pod in einem anderen Namespace zu planen, z. B. in *sre*. Die Gruppenmitgliedschaft des Benutzers wird nicht an einer Kubernetes-Rolle und einem RoleBinding-Element ausgerichtet, um diese Berechtigungen zu gewähren. Dies ist in der folgenden Beispielausgabe dargestellt:

```console
$ kubectl run nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testen des Zugriffs auf die AKS-Clusterressourcen durch Websitezuverlässigkeits-Techniker

Probieren Sie die obigen Befehle aus, wenn Sie als Benutzer *opssre* angemeldet sind, um zu bestätigen, dass unsere Azure AD-Gruppenmitgliedschaft und Kubernetes RBAC für unterschiedliche Benutzer und Gruppen richtig funktionieren.

Setzen Sie den Kontext *kubeconfig* mit dem Befehl [az aks get-credentials][az-aks-get-credentials] zurück. Hiermit wird das zuvor zwischengespeicherte Authentifizierungstoken für den Benutzer *aksdev* gelöscht:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Versuchen Sie, Pods im zugewiesenen Namespace *sre* zu planen und anzuzeigen. Melden Sie sich bei entsprechender Aufforderung mit Ihren eigenen `opssre@contoso.com`-Anmeldeinformationen an, die Sie zu Beginn dieses Artikels erstellt haben:

```console
kubectl run nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Wie in der folgenden Beispielausgabe dargestellt, ist das Erstellen und Anzeigen der Pods erfolgreich:

```console
$ kubectl run nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Versuchen Sie nun, Pods außerhalb des zugewiesenen SRE-Namespace anzuzeigen bzw. zu planen:

```console
kubectl get pods --all-namespaces
kubectl run nginx-sre --image=nginx --namespace dev
```

Für diese `kubectl`-Befehle treten Fehler auf, wie in der folgenden Beispielausgabe zu sehen. Über die Gruppenmitgliedschaft des Benutzers und die Kubernetes-Rolle und RoleBindings werden keine Berechtigungen zum Erstellen oder Verwalten von Ressourcen in anderen Namespaces gewährt:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Artikel haben Sie Ressourcen im AKS-Cluster und Benutzer und Gruppen in Azure AD erstellt. Führen Sie zum Bereinigen aller Ressourcen die folgenden Befehle aus:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Schutz von Kubernetes-Clustern finden Sie unter [Zugriffs- und Identitätsoptionen für AKS][rbac-authorization].

Best Practices zur Identitäts- und Ressourcenkontrolle finden Sie unter [Best Practices für Authentifizierung und Autorisierung in AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
