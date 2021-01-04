---
title: Verwalten eines Azure Kubernetes Service-Clusters mit dem Webdashboard
description: Erfahren Sie mehr über die Verwendung des integrierten Kubernetes-Dashboards mit Webbenutzeroberfläche zur Verwaltung eines Azure Kubernetes Service-Clusters (AKS).
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: 85f0a42cdfcbea2223d202a9dc35f58746580e85
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350125"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Zugreifen auf das Kubernetes-Webdashboard in Azure Kubernetes Service (AKS)

Kubernetes enthält ein Webdashboard, das für einfache Verwaltungsvorgänge verwendet werden kann. In diesem Dashboard können Sie den Integritätsstatus und grundlegende Metriken für Ihre Anwendungen anzeigen, Dienste erstellen und bereitstellen und vorhandene Anwendungen bearbeiten. In diesem Artikel wird erläutert, wie Sie mithilfe von Azure CLI auf das Kubernetes-Dashboard zugreifen können. Anschließend werden einige grundlegende Vorgänge im Dashboard vorgestellt.

Weitere Informationen zum Kubernetes-Dashboard finden Sie unter [Web UI (Dashboard)][kubernetes-dashboard] (Webbenutzeroberfläche (Dashboard)). AKS verwendet Version 2.0 und höher des Open-Source-Dashboards.

> [!WARNING]
> **Das AKS-Dashboard-Add-On ist zur Einstellung festgelegt. Verwenden Sie stattdessen die [Kubernetes-Ressourcenansicht im Azure-Portal (Vorschau)][kubernetes-portal].** 
> * Das Kubernetes-Dashboard ist für Cluster mit einer Kubernetes-Version unter 1.18 standardmäßig aktiviert.
> * Das Dashboard-Add-On wird standardmäßig für alle neuen Cluster deaktiviert, die unter Kubernetes 1.18 oder höher erstellt werden. 
 > * Ab Kubernetes 1.19 in der Vorschauversion unterstützt AKS die Installation des verwalteten Add-Ons „kube-dashboard“ nicht mehr. 
 > * Vorhandene Cluster, für die das Add-On aktiviert ist, sind nicht betroffen. Die Benutzer werden weiterhin in der Lage sein, das Open-Source-Dashboard manuell als vom Benutzer installierte Software zu installieren.

## <a name="before-you-begin"></a>Voraussetzungen

Bei den Schritten in diesem Dokument wird davon ausgegangen, dass Sie einen AKS-Cluster erstellt und eine `kubectl`-Verbindung mit dem Cluster hergestellt haben. Wenn Sie einen AKS-Cluster erstellen müssen, finden Sie weitere Informationen unter [Schnellstart: Bereitstellen eines Azure Kubernetes Service-Clusters über die Azure-Befehlszeilenschnittstelle][aks-quickstart].

Außerdem muss mindestens die Version 2.6.0 der Azure-Befehlszeilenschnittstelle installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

## <a name="disable-the-kubernetes-dashboard"></a>Deaktivieren des Kubernetes-Dashboards

Das Add-On „kube-dashboard“ ist **standardmäßig auf Clustern aktiviert, die älter als K8s 1.18** sind. Das Add-On kann durch Ausführen des folgenden Befehls deaktiviert werden.

``` azurecli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>Starten des Kubernetes-Dashboards

> [!WARNING]
> Das AKS-Dashboard-Add-On ist ab Version 1.19 zur Einstellung festgelegt. Verwenden Sie stattdessen die [Kubernetes-Ressourcenansicht im Azure-Portal (Vorschau)][kubernetes-portal]. 
> * Der folgende Befehl öffnet für die Versionen 1.19 und höher nun die Ressourcenansicht im Azure-Portal anstelle des Kubernetes-Dashboards.

Verwenden Sie den Befehl [az aks browse][az-aks-browse], um das Kubernetes-Dashboard auf einem Cluster zu starten. Dieser Befehl erfordert die Installation des Add-Ons „kube-dashboard“ auf dem Cluster, das bei Clustern mit einer Version älter als Kubernetes 1.18 standardmäßig enthalten ist.

Im folgenden Beispiel wird das Dashboard für den Cluster namens *myAKSCluster* in der Ressourcengruppe namens *myResourceGroup* geöffnet:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Der Befehl erstellt einen Proxy zwischen Ihrem Entwicklungssystem und der Kubernetes-API und öffnet einen Webbrowser mit dem Kubernetes-Dashboard. Wenn ein Webbrowser das Kubernetes-Dashboard nicht öffnet, kopieren Sie die in der Azure-Befehlszeilenschnittstelle angegebene URL-Adresse (in der Regel `http://127.0.0.1:8001`).

> [!NOTE]
> Wenn Sie das Dashboard unter `http://127.0.0.1:8001` nicht sehen, können Sie manuell zu den folgenden Adressen weiterleiten. Cluster unter Version 1.16 oder höher verwenden „https“ und erfordern einen separaten Endpunkt.
> * K8s 1.16 oder höher: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1.15 und niedriger: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your Kubernetes RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use Kubernetes RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.

> [!IMPORTANT]
> If your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. The Kubernetes dashboard does not currently support user-provided credentials to determine the level of access, rather it uses the roles granted to the service account. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
> 
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command. The following example shows how to create a sample binding, however, this sample binding does not apply any additional authentication components and may lead to insecure use. The Kubernetes dashboard is open to anyone with access to the URL. Do not expose the Kubernetes dashboard publicly.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].
-->

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>Anmelden am Dashboard (Kubernetes 1.16+)

> [!IMPORTANT]
> Ab [v1.10.1 des Kubernetes-Dashboards](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1) oder Kubernetes v1.16+ kann das Dienstkonto „kubernetes-dashboard“ aufgrund eines [Sicherheitsfixes in diesem Release](https://github.com/kubernetes/dashboard/pull/3400) nicht mehr zum Abrufen von Ressourcen verwendet werden. Infolgedessen geben Anforderungen ohne Authentifizierungsinformationen einen Fehler vom Typ [401 – Nicht autorisiert](https://github.com/Azure/AKS/issues/1573#issuecomment-703040998) zurück. Ein Bearertoken, das von einem Dienstkonto abgerufen wird, kann immer noch wie in diesem [Kubernetes-Dashboard-Beispiel](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui) verwendet werden, aber dies wirkt sich im Vergleich zu älteren Versionen auf den Anmeldefluss des Dashboard-Add-Ons aus.
>
>Wenn Sie noch eine ältere Version als 1.16 verwenden, können Sie immer noch Berechtigungen für das Dienstkonto „kubernetes-dashboard“ erteilen, aber dies wird **nicht empfohlen**:
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

Der dargestellte anfängliche Bildschirm erfordert ein kubeconfig oder Token. Beide Optionen erfordern Ressourcenberechtigungen, um diese Ressourcen im Dashboard anzuzeigen.

![Anmeldebildschirm](./media/kubernetes-dashboard/login.png)

**Verwenden von kubeconfig**

Sowohl für Azure AD-aktivierte als auch für Nicht-Azure AD-aktivierte Cluster kann ein kubeconfig übergeben werden. Stellen Sie sicher, dass die Zugriffstoken gültig sind. Wenn Ihre Token abgelaufen sind, können Sie die Token über kubectl aktualisieren.

1. Legen Sie admin kubeconfig mit `az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>` fest.
1. Wählen Sie `Kubeconfig` aus, und klicken Sie auf `Choose kubeconfig file`, um die Dateiauswahl zu öffnen.
1. Wählen Sie Ihre kubeconfig-Datei aus (Standardeinstellung: $HOME/.kube/config).
1. Klicken Sie auf `Sign In`.

**Verwenden eines Tokens**

1. Für **Nicht-Azure AD-aktivierte Cluster** führen Sie `kubectl config view` aus, und kopieren Sie das mit dem Benutzerkonto Ihres Clusters verbundene Token.
1. Fügen Sie beim Anmelden in die Tokenoption ein.    
1. Klicken Sie auf `Sign In`.

Bei Azure AD-aktivierten Clustern rufen Sie Ihr AAD-Token mit dem folgenden Befehl ab. Bestätigen Sie, dass Sie die Ressourcengruppe und den Clusternamen im Befehl ersetzt haben.

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

Nach erfolgreichem Abschluss wird eine Seite ähnlich der folgenden angezeigt.

![Die Übersichtsseite des Kubernetes-Webdashboards](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Erstellen einer Anwendung

Für die folgenden Schritte ist es erforderlich, dass der Benutzer über Berechtigungen für die jeweiligen Ressourcen verfügt. 

Um zu demonstrieren, wie das Kubernetes-Dashboard die Komplexität von Verwaltungsaufgaben verringern kann, erstellen wir nun eine Anwendung. Sie können eine Anwendung über das Kubernetes-Dashboard erstellen, indem Sie Texteingaben oder eine YAML-Datei bereitstellen oder einen grafischen Assistenten verwenden.

Um eine Anwendung zu erstellen, führen Sie die folgenden Schritte aus:

1. Wählen Sie die Schaltfläche **Erstellen** in der rechten oberen Ecke aus.
1. Um den grafischen Assistenten zu verwenden, wählen Sie **App erstellen** aus.
1. Geben Sie einen Namen für die Bereitstellung an, z.B. *nginx*.
1. Geben Sie den Namen des zu verwendenden Containerimages ein, z.B. *nginx:1.15.5*.
1. Um Port 80 für Webdatenverkehr verfügbar zu machen, erstellen Sie einen Kubernetes-Dienst. Wählen Sie unter **Dienst** die Option **Extern** aus, und geben Sie für den Port und den Zielport jeweils **80** ein.
1. Wenn Sie fertig sind, wählen Sie **Bereitstellen** aus, um die App zu erstellen.

![Bereitstellen einer App im Kubernetes-Webdashboard](./media/kubernetes-dashboard/create-app.png)

Es dauert einen Moment, bis eine externe öffentliche IP-Adresse dem Kubernetes-Dienst zugewiesen wird. Wählen Sie auf der linken Seite unter **Discovery and Load Balancing** (Ermittlung und den Lastenausgleich) die Option **Dienste** aus. Der Dienst Ihrer Anwendung wird aufgeführt, einschließlich der *externen Endpunkte*, wie im folgenden Beispiel gezeigt:

![Anzeigen der Liste der Dienste und Endpunkte](./media/kubernetes-dashboard/view-services.png)

Wählen Sie die Endpunktadresse aus, um ein Webbrowserfenster auf der NGINX-Standardseite zu öffnen:

![Anzeigen der NGINX-Standardseite der bereitgestellten Anwendung](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Anzeigen von Podinformationen

Das Kubernetes-Dashboard kann grundlegende Überwachungsmetriken und Informationen zur Problembehandlung wie z.B. Protokolle bereitstellen.

Um weitere Informationen zu Ihren Anwendungspods anzuzeigen, wählen Sie im linken Menü **Pods** aus. Die Liste der verfügbaren Pods wird angezeigt. Wählen Sie Ihren *nginx*-Pod aus, um die Informationen anzuzeigen, etwa den Ressourcenverbrauch:

![Anzeigen von Podinformationen](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Bearbeiten der Anwendung

Über das Kubernetes-Dashboard können Sie nicht nur Anwendungen erstellen und anzeigen, sondern auch Anwendungsbereitstellungen bearbeiten und aktualisieren. Um zusätzliche Redundanz für die Anwendung bereitzustellen, erhöhen Sie die Anzahl der NGINX-Replikate.

So bearbeiten Sie eine Bereitstellung

1. Wählen Sie im linken Menü **Bereitstellungen** und dann Ihre *nginx*-Bereitstellung aus.
1. Wählen Sie auf der Navigationsleiste rechts oben **Bearbeiten** aus.
1. Suchen Sie den Wert `spec.replica` in der Nähe von Zeile 20. Um die Anzahl der Replikate für die Anwendung zu erhöhen, ändern Sie diesen Wert von *1* in *3*.
1. Klicken Sie abschließend auf **Aktualisieren**.

![Bearbeiten der Bereitstellung zum Aktualisieren der Anzahl von Replikaten](./media/kubernetes-dashboard/edit-deployment.png)

Es dauert einige Zeit, bis die neuen Pods in einer Replikatgruppe erstellt werden. Wählen Sie im linken Menü **Replikatsätze** und dann Ihre *nginx*-Replikatgruppe aus. Die Liste der Pods entspricht nun der aktualisierten Replikatanzahl, wie in der folgenden Beispielausgabe gezeigt:

![Anzeigen von Informationen zur Replikatgruppe](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Kubernetes-Dashboard finden Sie unter [Kubernetes-Dashboard mit Webbenutzeroberfläche][kubernetes-dashboard].

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[kubernetes-portal]: ./kubernetes-portal.md
