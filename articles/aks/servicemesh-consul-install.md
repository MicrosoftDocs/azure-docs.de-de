---
title: Installieren von Consul in Azure Kubernetes Service (AKS)
description: Hier erfahren Sie, wie Sie Consul installieren und zum Erstellen eines Dienstmeshs in einem AKS-Cluster (Azure Kubernetes Service) verwenden.
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 7c5ad53c0040009e9ed1f28072540b46ce7b0b9a
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94683918"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Installieren und Verwenden von Consul in Azure Kubernetes Service (AKS)

[Consul][consul-github] ist ein Open-Source-Dienstmesh, das eine Reihe wichtiger Funktionen für Microservices in einem Kubernetes-Cluster bereitstellt. Zu diesen Features zählen Dienstermittlung, Integritätsüberprüfung, Dienstsegmentierung und Einblicke. Weitere Informationen zu Consul finden Sie in der offiziellen Dokumentation [What is Consul?][consul-docs-concepts] (Was ist Consul?).

In diesem Artikel wird gezeigt, wie Sie Consul installieren. Die Konsul-Komponenten werden in einem Kubernetes-Cluster unter AKS installiert.

> [!NOTE]
> Diese Anweisungen beziehen sich auf die Consul-Version `1.6.0`, und es wird mindestens die Helm-Version `2.14.2` verwendet.
>
> Die Consul-Releases `1.6.x` können für die Kubernetes-Versionen `1.13+` ausgeführt werden. Weitere Consul-Versionen finden Sie auf [GitHub][consul-github-releases]. Informationen zu den Releases finden Sie in den [Consul-Versionshinweisen][consul-release-notes].

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Installieren der Consul-Komponenten in AKS
> * Überprüfen der Consul-Installation
> * Deinstallieren von Consul in AKS

## <a name="before-you-begin"></a>Voraussetzungen

Bei den in diesem Artikel beschriebenen Schritten wird vorausgesetzt, dass Sie einen AKS-Cluster (ab Kubernetes `1.13`, Kubernetes RBAC aktiviert) erstellt und eine `kubectl`-Verbindung mit dem Cluster hergestellt haben. Wenn Sie Hilfe bei einem dieser Elemente benötigen, beachten Sie das Dokument [AKS-Schnellstart][aks-quickstart]. Stellen Sie sicher, dass Ihr Cluster im Linux-Knotenpool über mindestens drei Knoten verfügt.

Sie benötigen [Helm][helm], um diese Schritte ausführen und Consul installieren zu können. In Ihrem Cluster sollte die neueste stabile Version installiert und ordnungsgemäß konfiguriert sein. Wenn Sie Hilfe bei der Installation von Helm benötigen, beachten Sie den [AKS Helm-Installationsleitfaden][helm-install]. Alle Consul-Pods müssen auch für die Ausführung auf Linux-Knoten geplant werden.

Im vorliegenden Artikel wird die Consul-Installationsanleitung in mehrere Einzelschritte untergliedert. Das Endergebnis hat dieselbe Struktur wie die offizielle [Anleitung][consul-install-k8] für die Consul-Installation.

### <a name="install-the-consul-components-on-aks"></a>Installieren der Consul-Komponenten in AKS

Laden Sie zunächst die Version `v0.10.0` des Consul-Helm-Charts herunter. Diese Chartversion enthält die der Consul-Version `1.6.0`.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Verwenden Sie Helm und das heruntergeladene Chart `consul-helm`, um die Consul-Komponenten im `consul`-Namespace Ihres AKS-Clusters zu installieren. 

> [!NOTE]
> **Installationsoptionen**
> 
> Im Rahmen dieser Installation verwenden Sie die folgenden Optionen:
> - `connectInject.enabled=true`: Ermöglicht das Einfügen von Proxys in Pods.
> - `client.enabled=true`: Ermöglicht das Ausführen von Consul-Clients auf jedem Knoten.
> - `client.grpc=true`: Ermöglicht die Verwendung von gRPC-Listenern für „connectInject“.
> - `syncCatalog.enabled=true`: Synchronisiert Kubernetes- und Consul-Dienste.
>
> **Knotenselektoren**
>
> Consul muss aktuell für die Ausführung auf Linux-Knoten geplant werden. Wenn sich in Ihrem Cluster Windows Server-Knoten befinden, müssen Sie sicherstellen, dass Consul-Pods nur für die Ausführung auf Linux-Knoten geplant sind. Wir verwenden [Knotenselektoren][kubernetes-node-selectors], um sicherzustellen, dass Pods für die richtigen Knoten geplant werden.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

Das Helm-Chart `Consul` stellt eine Reihe von Objekten bereit. Eine Liste der Ausgabe Ihres Befehls `helm install` finden Sie weiter oben. Die Bereitstellung der Consul-Komponenten dauert je nach Clusterumgebung etwa drei Minuten.

Consul ist nun in Ihrem AKS-Cluster bereitgestellt. Im nächsten Abschnitt überprüfen wir die Consul-Installation, um uns zu vergewissern, dass die Bereitstellung von Consul erfolgreich war.

## <a name="validate-the-consul-installation"></a>Überprüfen der Consul-Installation

Vergewissern Sie sich, dass die Ressourcen erfolgreich erstellt wurden. Verwenden Sie die Befehle [kubectl get svc][kubectl-get] und [kubectl get pod][kubectl-get], um den Namespace `consul`abzufragen, in dem die Consul-Komponenten mit dem Befehl `helm install` installiert wurden:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

Die folgende Beispielausgabe zeigt die Dienste und (für Linux-Knoten geplanten) Pods, die nun ausgeführt werden sollten:

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

Alle Pods müssen den Status `Running` haben. Wenn Ihre Pods nicht diesen Status haben, warten Sie ein oder zwei Minuten, bis dies der Fall ist. Wenn einer der Pods ein Problem meldet, verwenden Sie den Befehl [kubectl describe pod][kubectl-describe], um die Ausgabe und den Status des betreffenden Pods zu überprüfen.

## <a name="accessing-the-consul-ui"></a>Zugreifen auf die Consul-Benutzeroberfläche

Die Consul-Benutzeroberfläche wurde im Rahmen der obigen Einrichtung installiert und ermöglicht eine UI-basierte Konfiguration für Consul. Die Benutzeroberfläche für Consul wird nicht öffentlich über eine externe IP-Adresse verfügbar gemacht. Verwenden Sie für den Zugriff auf die Consul-Benutzeroberfläche den Befehl [kubectl port-forward][kubectl-port-forward]. Dieser Befehl erstellt eine sichere Verbindung zwischen Ihrem Clientcomputer und dem entsprechenden Pod in Ihrem AKS-Cluster.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

Sie können nun einen Browser öffnen und zu `http://localhost:8080/ui` navigieren, um die Consul-Benutzeroberfläche zu öffnen. Wenn Sie die Benutzeroberfläche öffnen, sollte Folgendes angezeigt werden:

![Consul-Benutzeroberfläche](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Deinstallieren von Consul in AKS

> [!WARNING]
> Das Löschen von Consul in einem laufenden System kann zu Problemen beim Datenverkehr zwischen Ihren Diensten führen. Stellen Sie sicher, dass Sie das System so weit vorbereitet haben, dass es auch ohne Consul ordnungsgemäß funktioniert.

### <a name="remove-consul-components-and-namespace"></a>Entfernen von Consul-Komponenten und -Namespace

Verwenden Sie die folgenden Befehle, um Consul aus Ihrem AKS-Cluster zu entfernen. Der Befehl `helm delete` entfernt das Chart `consul` und der Befehl `kubectl delete namespace` den Namespace `consul`.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zu weiteren Installations- und Konfigurationsoptionen für Consul finden Sie in den folgenden offiziellen Consul-Artikeln:

- [Consul - Helm installation guide][consul-install-k8] (Consul – Installationshandbuch für Helm)
- [Consul - Helm installation options][consul-install-helm-options] (Consul – Installationsoptionen für Helm)

Weitere Szenarien finden Sie hier:

- [Consul-Beispielanwendung][consul-app-example]
- [Consul-Kubernetes-Referenzarchitektur][consul-reference]
- [Consul-Meshgateways][consul-mesh-gateways]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-deployment-guide
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-mesh-gateways]: https://learn.hashicorp.com/consul/kubernetes/mesh-gateways
[consul-reference]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-reference
[consul-app-example]: https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh
[install-wsl]: /windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
