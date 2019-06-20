---
title: Bereitstellen von Containern mit Helm in Kubernetes in Azure
description: Erfahren Sie, wie Sie das Helm-Paketerstellungstool verwenden, um Container in einem AKS-Cluster (Azure Kubernetes Service) bereitzustellen.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 05/23/2019
ms.author: zarhoads
ms.openlocfilehash: 76a5391cbe142851d9b1f60ea9346af2e7a35d6a
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392135"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Installieren von Anwendungen mit Helm in Azure Kubernetes Service (AKS)

[Helm][helm] ist ein Open Source-Verpackungstool, das Ihnen dabei hilft, Kubernetes-Anwendungen zu installieren und ihren Lebenszyklus zu verwalten. Ähnlich wie Linux-Paket-Manager (z.B. *APT* und *Yum*) wird Helm zur Verwaltung von Kubernetes-Diagrammen verwendet, bei denen es sich um Pakete aus vorkonfigurierten Kubernetes-Ressourcen handelt.

Dieser Artikel veranschaulicht die Konfiguration und Verwendung von Helm in einem Kubernetes-Cluster in AKS.

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie noch einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

Außerdem muss die Helm CLI installiert sein, d. h. der Client, der auf Ihrem Entwicklungssystem ausgeführt wird. Sie ermöglicht es Ihnen, Anwendungen mit Helm zu starten, zu beenden und zu verwalten. Wenn Sie Azure Cloud Shell verwenden, ist die Helm-CLI bereits installiert. Installationsanweisungen für Ihre lokale Plattform finden Sie unter [Installieren von Helm][helm-install].

> [!IMPORTANT]
> Helm ist für die Ausführung auf Linux-Knoten vorgesehen. Wenn sich in Ihrem Cluster Windows Server-Knoten befinden, müssen Sie sicherstellen, dass Helm-Pods nur für die Ausführung auf Linux-Knoten geplant sind. Sie müssen auch sicherstellen, dass alle von Ihnen installierten Helm-Diagramme auch für die Ausführung auf den richtigen Knoten geplant sind. Die Befehle in diesem Artikel verwenden [node-selectors][k8s-node-selector], um sicherzustellen, dass die Pods für die richtigen Knoten geplant sind, aber nicht alle Helm-Diagramme können einen Knotenselektor verfügbar machen. Sie können auch andere Optionen für Ihren Cluster verwenden, z. B. [Taints][taints].

## <a name="create-a-service-account"></a>Erstellen eines Dienstkontos

Vor der Bereitstellung von Helm in einem RBAC-fähigen AKS-Cluster benötigen Sie ein Dienstkonto und eine Rollenbindung für den Tiller-Dienst. Weitere Informationen zum Sichern von Helm/Tiller in einem RBAC-fähigen Cluster finden Sie unter [Tiller, Namespaces und RBAC][tiller-rbac]. Überspringen Sie diesen Schritt, wenn Ihr AKS-Cluster nicht RBAC-fähig ist.

Erstellen Sie eine Datei namens `helm-rbac.yaml`, und fügen Sie den folgenden YAML-Code ein:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Erstellen Sie das Dienstkonto und die Rollenbindung mithilfe des Befehls `kubectl apply`:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>Schützen von Tiller und Helm

Der Helm-Client und der Tiller-Dienst verwenden TLS/SSL, um sich gegenseitig zu authentifizieren und miteinander zu kommunizieren. Diese Authentifizierungsmethode unterstützt den Schutz von Kubernetes-Clustern und legt fest, welche Dienste bereitgestellt werden können. Sie können eigene signierte Zertifikate generieren, um die Sicherheit zu erhöhen. Jeder Helm-Benutzer würde ein eigenes Clientzertifikat erhalten, und Tiller würde im Kubernetes-Cluster initialisiert werden, auf den die Zertifikate angewendet wurden. Weitere Informationen finden Sie unter [Using TLS/SSL between Helm and Tiller (Verwenden von TLS/SSL zwischen Helm und Tiller)][helm-ssl].

Mit einem RBAC-fähigen Kubernetes-Cluster können Sie steuern, über welche Zugriffsebene Tiller für den Cluster verfügt. Sie können den Kubernetes-Namespace definieren, in dem Tiller bereitgestellt wurde, und einschränken, in welchen Namespaces Tiller Ressourcen bereitstellen kann. Durch diesen Ansatz können Sie Tiller-Instanzen in verschiedenen Namespaces erstellen, die Bereitstellung einschränken und die Benutzer des Helm-Clients auf bestimmte Namespaces beschränken. Weitere Informationen finden Sie unter [Helm role-based access controls (Helm: Rollenbasierte Zugriffssteuerung)][helm-rbac].

## <a name="configure-helm"></a>Konfigurieren von Helm

Verwenden Sie den Befehl [helm init][helm-init], um Tiller in einem AKS-Cluster bereitzustellen. Wenn Ihr Cluster nicht RBAC-fähig ist, entfernen Sie das Argument `--service-account` und den Wert. Wenn Sie TLS/SSL für Tiller und Helm konfiguriert haben, überspringen Sie diesen grundlegenden Initialisierungsschritt, und stellen Sie stattdessen wie im nächsten Beispiel dargestellt den erforderlichen Parameter `--tiller-tls-` bereit.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

Wenn Sie TLS/SSL zwischen Helm und Tiller konfiguriert haben, stellen Sie wie im folgenden Beispiel dargestellt die `--tiller-tls-*`-Parameter und die Namen Ihrer eigenen Zertifikate bereit:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="find-helm-charts"></a>Suchen von Helm-Diagrammen

Helm-Diagramme dienen zum Bereitstellen von Anwendungen in einem Kubernetes-Cluster. Verwenden Sie zum Suchen nach zuvor erstellten Helm-Diagrammen den Befehl [helm search][helm-search]:

```console
helm search
```

Die folgende verkürzte Beispielausgabe veranschaulicht einige der verfügbaren Helm-Diagramme:

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

Verwenden Sie zum Aktualisieren der Liste der Diagramme den Befehl [helm repo update][helm-repo-update]. Das folgende Beispiel stellt das erfolgreiche Update eines Repositorys dar:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Ausführen von Helm-Diagrammen

Verwenden Sie zum Installieren von Diagrammen mit Helm den Befehl [helm install][helm-install], und geben Sie den Namen des zu installierenden Diagramms an. Um die Installation eines Helm-Diagramms in Aktion zu sehen, installieren wir eine grundlegende nginx-Bereitstellung mithilfe eines Helm-Diagramms. Wenn Sie TLS/SSL konfiguriert haben, fügen Sie den `--tls`-Parameter hinzu, um Ihr Helm-Clientzertifikat zu verwenden.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Die folgende verkürzte Beispielausgabe zeigt den Bereitstellungsstatus der Kubernetes-Ressource an, die vom Helm-Diagramm erstellt wurde:

```
$ helm install stable/nginx-ingress --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME:   flailing-alpaca
LAST DEPLOYED: Thu May 23 12:55:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                      DATA  AGE
flailing-alpaca-nginx-ingress-controller  1     0s

==> v1/Pod(related)
NAME                                                            READY  STATUS             RESTARTS  AGE
flailing-alpaca-nginx-ingress-controller-56666dfd9f-bq4cl       0/1    ContainerCreating  0         0s
flailing-alpaca-nginx-ingress-default-backend-66bc89dc44-m87bp  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                                           TYPE          CLUSTER-IP  EXTERNAL-IP  PORT(S)                     AGE
flailing-alpaca-nginx-ingress-controller       LoadBalancer  10.0.109.7  <pending>    80:31219/TCP,443:32421/TCP  0s
flailing-alpaca-nginx-ingress-default-backend  ClusterIP     10.0.44.97  <none>       80/TCP                      0s
...
```

Es dauert maximal zwei Minuten, bis *EXTERNAL-IP* für den nginx-ingress-controller-Dienst aufgefüllt ist und Sie mit einem Webbrowser auf den Dienst zugreifen können.

## <a name="list-helm-releases"></a>Auflisten von Helm-Versionen

Verwenden Sie für die Anzeige einer Liste der im Cluster installierten Releases den Befehl [helm list][helm-list]. In folgendem Beispiel wird das nginx-ingress-Release veranschaulicht, das im vorherigen Schritt bereitgestellt wurde. Wenn Sie TLS/SSL konfiguriert haben, fügen Sie den `--tls`-Parameter hinzu, um Ihr Helm-Clientzertifikat zu verwenden.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Helm-Diagramm bereitstellen, werden eine Reihe von Kubernetes-Ressourcen erstellt. Zu diesen Ressourcen gehören Pods, Bereitstellungen und Dienste. Um diese Ressourcen zu bereinigen, verwenden Sie den Befehl `helm delete`. Geben Sie dabei den Namen Ihrer Version an, die Sie mit dem vorherigen Befehl `helm list` ermittelt haben. Im folgenden Beispiel wird die Version namens *flailing-alpaca* gelöscht:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwalten von Kubernetes-Anwendungsbereitstellungen mit Helm finden Sie in der Helm-Dokumentation.

> [!div class="nextstepaction"]
> [Helm-Dokumentation][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
[helm-ssl]: https://docs.helm.sh/using_helm/#using-ssl-between-helm-and-tiller

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md