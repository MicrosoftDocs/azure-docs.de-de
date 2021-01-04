---
title: Installieren von Linkerd in Azure Kubernetes Service (AKS)
description: Hier erfahren Sie, wie Sie Linkerd installieren und zum Erstellen eines Dienstmeshs in einem AKS-Cluster (Azure Kubernetes Service) verwenden.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d5b2ec220bbf14a79247ed2c78a0e84f4717fae3
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684235"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Installieren von Linkerd in Azure Kubernetes Service (AKS)

[Linkerd][linkerd-github] ist ein Open-Source-Dienstmesh und [CNCF-Incubatingprojekt][linkerd-cncf]. Linkerd ist ein ultraleichtes Dienstmesh, das Features wie Datenverkehrsverwaltung, Dienstidentität sowie Sicherheit, Zuverlässigkeit und Einblicke bereitstellt. Weitere Informationen zu Linkerd finden Sie in den offiziellen [Häufig gestellten Fragen zu Linkerd][linkerd-faq] und der Dokumentation zur [Linkerd-Architektur][linkerd-architecture].

Dieser Artikel beschreibt, wie Sie Linkerd installieren. Die Linkerd-Clientbinärdatei `linkerd` wird auf Ihrem Clientcomputer installiert, und anschließend werden die Linkerd-Komponenten in einem Kubernetes-Cluster in AKS installiert.

> [!NOTE]
> Diese Anweisungen beziehen sich auf die Linkerd-Version `stable-2.6.0`.
>
> `stable-2.6.x` für Linkerd kann für Kubernetes-Versionen `1.13+`ausgeführt werden. Weitere Linkerd-Versionen (stable und edge) finden Sie unter [GitHub – Linkerd-Versionen][linkerd-github-releases].

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Herunterladen und Installieren der Linkerd-Clientbinärdatei „linkerd“
> * Installieren von Linkerd in AKS
> * Überprüfen der Linkerd-Installation
> * Zugreifen auf das Dashboard
> * Deinstallieren von Linkerd von AKS

## <a name="before-you-begin"></a>Voraussetzungen

Bei den in diesem Artikel beschriebenen Schritten wird vorausgesetzt, dass Sie einen AKS-Cluster (Kubernetes `1.13` und höher, Kubernetes RBAC aktiviert) erstellt und eine `kubectl`-Verbindung mit dem Cluster hergestellt haben. Wenn Sie Hilfe bei einem dieser Elemente benötigen, beachten Sie das Dokument [AKS-Schnellstart][aks-quickstart].

Alle Linkerd-Pods müssen für die Ausführung auf Linux-Knoten geplant sein. Dies ist die Standardeinstellung in der unten beschriebenen Installationsmethode, und es ist keine zusätzliche Konfiguration erforderlich.

Im vorliegenden Artikel wird die Linkerd-Installationsanleitung in mehrere Einzelschritte untergliedert. Das Ergebnis hat dieselbe Struktur wie die offizielle [Anleitung][linkerd-getting-started] für die ersten Schritte mit Linkerd.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>Installieren von Linkerd in AKS

Vor der Installation von Linkerd führen wir die Überprüfungen aus, um zu bestimmen, ob die Steuerungsebene auf unserem AKS-Cluster installiert werden kann:

```console
linkerd check --pre
```

Eine Anzeige wie die folgende gibt an, dass Ihr AKS-Cluster ein gültiges Installationsziel für Linkerd ist:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

Nun können Sie die Linkerd-Komponenten installieren. Verwenden Sie die Binärdateien `linkerd` und `kubectl`, um die Linkerd-Komponenten in Ihrem AKS-Cluster zu installieren. Ein `linkerd`-Namespace wird automatisch erstellt, und die Komponenten werden in diesem Namespace installiert.

```console
linkerd install | kubectl apply -f -
```

Linkerd stellt eine Reihe von Objekten bereit. Eine Liste der Ausgabe Ihres Befehls `linkerd install` finden Sie weiter oben. Die Bereitstellung der Linkerd-Komponenten dauert je nach Clusterumgebung etwa eine Minute.

Linkerd ist nun in Ihrem AKS-Cluster bereitgestellt. Um eine erfolgreiche Bereitstellung von Linkerd sicherzustellen, fahren Sie mit dem nächsten Abschnitt [Überprüfen der Linkerd-Installation](#validate-the-linkerd-installation) fort.

## <a name="validate-the-linkerd-installation"></a>Überprüfen der Linkerd-Installation

Vergewissern Sie sich, dass die Ressourcen erfolgreich erstellt wurden. Verwenden Sie die Befehle [kubectl get svc][kubectl-get] und [kubectl get pod][kubectl-get], um den `linkerd`-Namespace abzufragen, in dem die Linkerd-Komponenten mit dem Befehl `linkerd install` installiert wurden:

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

Die folgende Beispielausgabe zeigt die Dienste und (für Linux-Knoten geplanten) Pods, die nun ausgeführt werden sollten:

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

Linkerd stellt einen Befehl über die Clientbinärdatei `linkerd` bereit, um zu überprüfen, ob die Linkerd-Steuerungsebene erfolgreich installiert und konfiguriert wurde.

```console
linkerd check
```

Eine Anzeige wie die folgende gibt an, dass die Installation erfolgreich war:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>Zugreifen auf das Dashboard

Linkerd verfügt über ein Dashboard, das Erkenntnisse zum Dienstmesh und den Workloads bietet. Verwenden Sie den Befehl `linkerd dashboard`, um auf das Dashboard zuzugreifen. Dieser Befehl nutzt [kubectl port-forward][kubectl-port-forward] zum Erstellen einer sicheren Verbindung zwischen Ihrem Clientcomputer und den relevanten Pods in Ihrem AKS-Cluster. Das Dashboard wird anschließend automatisch in Ihrem Standardbrowser geöffnet.

```console
linkerd dashboard
```

Der Befehl erstellt außerdem eine Portweiterleitung und gibt einen Link für die Grafana-Dashboards zurück.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>Deinstallieren von Linkerd von AKS

> [!WARNING]
> Das Löschen von Linkerd in einem laufenden System kann zu Problemen beim Datenverkehr zwischen Ihren Diensten führen. Stellen Sie sicher, dass Sie das System so weit vorbereitet haben, dass es auch ohne Linkerd ordnungsgemäß funktioniert, bevor Sie fortfahren.

Zunächst müssen Sie die Proxys für die Datenebene entfernen. Entfernen Sie alle [Anmerkungen][linkerd-automatic-proxy-injection] zur automatischen Proxyeinschleusung aus Workloadnamespaces, und führen Sie einen Rollout der Workloadbereitstellungen durch. Ihre Workloads verfügen über keine zugeordneten Datenebenenkomponenten mehr.

Entfernen Sie anschließend die Steuerungsebene wie folgt:

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zu weiteren Installations- und Konfigurationsoptionen für Linkerd finden Sie in den folgenden offiziellen Linkerd-Artikeln:

- [Linkerd – Helm-Installation][linkerd-install-with-helm]
- [Linkerd – mehrstufige Installation für Rollenberechtigungen][linkerd-multi-stage-installation]

Weitere Szenarien finden Sie hier:

- [Demo zu Linkerd emojivoto][linkerd-demo-emojivoto]
- [Demo zu Linkerd books][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
