---
title: Verwalten eines Azure Kubernetes Service-Clusters mit dem Webdashboard
description: Erfahren Sie mehr über die Verwendung des integrierten Kubernetes-Dashboards mit Webbenutzeroberfläche zur Verwaltung eines Azure Kubernetes Service-Clusters (AKS).
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: twhitney
ms.openlocfilehash: 80c0bd630ba2263696b72b003e27c53f1e457704
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304530"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Zugreifen auf das Kubernetes-Webdashboard in Azure Kubernetes Service (AKS)

Kubernetes enthält ein Webdashboard, das für einfache Verwaltungsvorgänge verwendet werden kann. In diesem Dashboard können Sie den Integritätsstatus und grundlegende Metriken für Ihre Anwendungen anzeigen, Dienste erstellen und bereitstellen und vorhandene Anwendungen bearbeiten. In diesem Artikel wird erläutert, wie Sie mithilfe von Azure CLI auf das Kubernetes-Dashboard zugreifen können. Anschließend werden einige grundlegende Vorgänge im Dashboard vorgestellt.

Weitere Informationen zum Kubernetes-Dashboard finden Sie unter [Kubernetes Web UI Dashboard (Kubernetes-Dashboard mit Webbenutzeroberfläche)][kubernetes-dashboard].

## <a name="before-you-begin"></a>Voraussetzungen

Bei den Schritten in diesem Dokument wird davon ausgegangen, dass Sie einen AKS-Cluster erstellt und eine `kubectl`-Verbindung mit dem Cluster hergestellt haben. Wenn Sie einen AKS-Cluster erstellen müssen, finden Sie weitere Informationen unter [AKS-Schnellstart][aks-quickstart].

Außerdem muss die Version 2.0.46 oder höher der Azure-Befehlszeilenschnittstelle installiert und konfiguriert sein. Führen Sie  `az --version`  aus, um die Version zu ermitteln. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie weitere Informationen unter [Installieren der Azure CLI][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Starten des Kubernetes-Dashboards

Verwenden Sie den Befehl [az aks browse][az-aks-browse], um das Kubernetes-Dashboard zu starten. Im folgenden Beispiel wird das Dashboard für den Cluster namens *myAKSCluster* in der Ressourcengruppe namens *myResourceGroup* geöffnet:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Der Befehl erstellt einen Proxy zwischen Ihrem Entwicklungssystem und der Kubernetes-API und öffnet einen Webbrowser mit dem Kubernetes-Dashboard. Wenn ein Webbrowser das Kubernetes-Dashboard nicht öffnet, kopieren Sie die in der Azure-Befehlszeilenschnittstelle angegebene URL-Adresse (in der Regel `http://127.0.0.1:8001`).

![Die Übersichtsseite des Kubernetes-Webdashboards](./media/kubernetes-dashboard/dashboard-overview.png)

### <a name="for-rbac-enabled-clusters"></a>Für RBAC-fähige Cluster

Wenn Ihr AKS-Cluster RBAC verwendet, muss ein *ClusterRoleBinding*-Element erstellt werden, bevor Sie ordnungsgemäß auf das Dashboard zugreifen können. Das Kubernetes-Dashboard wird standardmäßig mit minimalem Lesezugriff bereitgestellt und zeigt RBAC-Zugriffsfehler an. Das Kubernetes-Dashboard unterstützt derzeit keine vom Benutzer bereitgestellten Anmeldeinformationen für die Ermittlung der Zugriffsebene, sondern verwendet die dem Dienstkonto zugewiesenen Rollen. Ein Clusteradministrator kann zusätzlichen Zugriff auf das Dienstkonto *kubernetes-dashboard* gewähren, dies kann jedoch ein Vektor für Berechtigungsausweitung sein. Sie können auch Azure Active Directory-Authentifizierung integrieren, um differenziertere Zugriffsmöglichkeiten zu bieten.

Verwenden Sie wie im folgenden Beispiel dargestellt den Befehl [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding], um eine Bindung zu erstellen. 

> [!WARNING]
> Diese Beispielbindung wendet keine zusätzlichen Authentifizierungskomponenten an und kann daher zu unsicherer Verwendung führen. Das Kubernetes-Dashboard steht allen Personen zur Verfügung, die Zugriff auf die URL haben. Machen Sie das Kubernetes-Dashboard nicht öffentlich verfügbar.
>
> Weitere Informationen zur Verwendung der unterschiedlichen Authentifizierungsmethoden finden Sie in der Wiki des Kubernetes-Dashboards unter [Zugriffssteuerung][dashboard-authentication].

```console
kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

Sie können nun in Ihrem RBAC-fähigen Cluster auf das Kubernetes-Dashboard zugreifen. Verwenden Sie wie zuvor beschrieben den Befehl [az aks browse][az-aks-browse], um das Kubernetes-Dashboard zu starten.

## <a name="create-an-application"></a>Erstellen einer Anwendung

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
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
