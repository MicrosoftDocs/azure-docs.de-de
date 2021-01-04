---
title: Installieren von Istio in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie Istio installieren und zum Erstellen eines Service Mesh in einem AKS-Cluster (Azure Kubernetes Service) verwenden.
author: paulbouwer
ms.topic: article
ms.date: 10/02/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: c42e9c31397e9313898d7029366bc8de169d368e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94683827"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installieren und Verwenden von Istio in Azure Kubernetes Service (AKS)

[Istio][istio-github] ist ein Open-Source-Dienstmesh, das eine Reihe wichtiger Funktionen für Microservices in einem Kubernetes-Cluster bereitstellt. Zu diesen Funktionen gehören: Verwaltung des Datenverkehrs, Dienstidentität und -sicherheit, Richtlinienerzwingung und Telemetrie. Weitere Informationen zu Istio finden Sie in der offiziellen Dokumentation [What is Istio?][istio-docs-concepts] (Was ist Istio?).

In diesem Artikel wird gezeigt, wie Sie Istio installieren. Die Istio-Clientbinärdatei `istioctl` wird auf Ihrem Clientcomputer installiert, und anschließend werden die Istio-Komponenten in einem Kubernetes-Cluster in AKS installiert.

> [!NOTE]
> Die folgenden Anweisungen beziehen sich auf die Istio-Version `1.7.3`.
>
> Die Istio-Versionen `1.7.x` wurden vom Istio-Team mit der Kubernetes-Version `1.16+` getestet. Weitere Istio-Versionen finden Sie unter [GitHub – Istio-Releases][istio-github-releases]. Informationen zu den Releases finden Sie unter [Istio-Neuigkeiten][istio-release-notes], und unterstützte Kubernetes-Versionen finden Sie in den [allgemeinen FAQ für Istio][istio-faq].

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Herunterladen und Installieren der Istio-Clientbinärdatei „istioctl“
> * Installieren von Istio in AKS
> * Überprüfen der Istio-Installation
> * Zugreifen auf die Add-Ons
> * Deinstallieren von Istio aus AKS

## <a name="before-you-begin"></a>Voraussetzungen

Bei den in diesem Artikel beschriebenen Schritten wird vorausgesetzt, dass Sie einen AKS-Cluster (Kubernetes `1.16` und höher, Kubernetes RBAC aktiviert) erstellt und eine `kubectl`-Verbindung mit dem Cluster hergestellt haben. Wenn Sie Hilfe bei einem dieser Elemente benötigen, beachten Sie das Dokument [AKS-Schnellstart][aks-quickstart].

Stellen Sie sicher, dass Sie die Dokumentation zur [Istio-Leistung und -Skalierbarkeit](https://istio.io/docs/concepts/performance-and-scalability/) gelesen und die zusätzlichen Ressourcenanforderungen für die Ausführung von Istio in Ihrem AKS-Cluster verstanden haben. Die Anforderungen an Core und Arbeitsspeicher variieren je nach Workload. Wählen Sie eine geeignete Anzahl von Knoten und eine geeignete VM-Größe für Ihr Setup aus.

Im vorliegenden Artikel wird die Istio-Installationsanleitung in mehrere Einzelschritte untergliedert. Das Endergebnis hat dieselbe Struktur wie beim Befolgen der offiziellen [Anleitung][istio-install-istioctl] für die Installation von Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-operator-on-aks"></a>Installieren des Istio-Operators in AKS

Istio bietet einen [Operator][istio-install-operator], um die Installation und Aktualisierungen der Istio-Komponenten in Ihrem AKS-Cluster zu verwalten. Wir installieren den Istio-Operator mithilfe der `istioctl`-Clientbinärdatei.

```bash
istioctl operator init
```

Es sollte etwas wie die folgende Ausgabe angezeigt werden, um zu bestätigen, dass der Istio-Operator installiert wurde.

```console
Using operator Deployment image: docker.io/istio/operator:1.7.3
✔ Istio operator installed
✔ Installation complete
```

Der Istio-Operator wird in den `istio-operator`-Namespace installiert. Fragen Sie den Namespace ab.

```bash
kubectl get all -n istio-operator
```

Die folgenden Komponenten sollten als bereitgestellt angezeigt werden.

```console
NAME                                  READY   STATUS    RESTARTS   AGE
pod/istio-operator-6d7958b7bf-wxgdc   1/1     Running   0          2m43s

NAME                     TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/istio-operator   ClusterIP   10.0.8.57    <none>        8383/TCP   2m43s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-operator   1/1     1            1           2m43s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-operator-6d7958b7bf   1         1         1       2m43s
```

Erfahren Sie mehr über das Operator-Muster, und wie es bei der Automatisierung komplexer Aufgaben mittels [kubernetes.io][kubernetes-operator] helfen kann.


### <a name="install-istio-components"></a>Installieren der Istio-Komponenten

Nachdem wir nun den Istio-Operator erfolgreich in unserem AKS-Cluster installiert haben, müssen wir die Istio-Komponenten installieren. 

Wir nutzen das `default` [Istio-Konfigurationsprofil][istio-configuration-profiles], um die [Istio-Operator-Spezifikationen][istio-control-plane] zu erstellen.

Sie können den folgenden `istioctl`-Befehl ausführen, um die Konfiguration für das Istio-Konfigurationsprofil `default` anzuzeigen.

```bash
istioctl profile dump default
```

> [!NOTE]
> Istio muss aktuell für die Ausführung auf Linux-Knoten geplant werden. Wenn sich in Ihrem Cluster Windows Server-Knoten befinden, müssen Sie sicherstellen, dass Istio-Pods nur für die Ausführung auf Linux-Knoten geplant sind. Wir verwenden [Knotenselektoren][kubernetes-node-selectors], um sicherzustellen, dass Pods für die richtigen Knoten geplant werden.

> [!CAUTION]
> Die Istio-Features [Istio CNI][istio-feature-cni] befinden sich derzeit in der [Alpha-Phase][istio-feature-stages] und müssen daher mit Bedacht verwendet werden. 

Erstellen Sie eine Datei namens `istio.aks.yaml` mit dem folgenden Inhalt. Diese Datei nimmt die [Istio-Operator-Spezifikation][istio-control-plane] zum Konfigurieren von Istio auf.

```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
  name: istio-control-plane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  # Enable the addons that we will want to use
  addonComponents:
    grafana:
      enabled: true
    prometheus:
      enabled: true
    tracing:
      enabled: true
    kiali:
      enabled: true
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
    kiali:
      dashboard:
        auth:
          strategy: anonymous 
```

Erstellen Sie den `istio-system`-Namespace, und stellen Sie die Istio-Operator-Spezifikation in diesem Namespace bereit. Der Istio-Operator sucht nach der Istio-Operator-Spezifikation und verwendet diese, um Istio in Ihrem AKS-Cluster zu installieren und zu konfigurieren.

```bash
kubectl create ns istio-system

kubectl apply -f istio.aks.yaml 
```

An diesem Punkt haben Sie Istio in Ihrem AKS-Cluster bereitgestellt. Um eine erfolgreiche Bereitstellung von Istio sicherzustellen, fahren Sie mit dem nächsten Abschnitt [Überprüfen der Istio-Installation](#validate-the-istio-installation) fort.

## <a name="validate-the-istio-installation"></a>Überprüfen der Istio-Installation

Fragen Sie den `istio-system`-Namespace ab, in dem die Istio- und Add-On-Komponenten des Istio-Operators installiert wurden:

```bash
kubectl get all -n istio-system
```

Die folgenden Komponenten sollten als angezeigt werden:

- `istio*`: die Istio-Komponenten
- `jaeger-*`, `tracing` und `zipkin`: Ablaufverfolgungs-Add-On
- `prometheus`: Metrik-Add-On
- `grafana`: Analyse- und Überwachungsdashboard-Add-On
- `kiali`: Dienstmeshdashboard-Add-On

```console
NAME                                        READY   STATUS    RESTARTS   AGE
pod/grafana-7cf9794c74-mpfbp                1/1     Running   0          5m53s
pod/istio-ingressgateway-86b5dbdcb9-ndrp5   1/1     Running   0          5m57s
pod/istio-tracing-c98f4b8fc-zqklg           1/1     Running   0          82s
pod/istiod-6965c56995-4ph9h                 1/1     Running   0          6m15s
pod/kiali-7b44985d68-p87zh                  1/1     Running   0          81s
pod/prometheus-6868989549-5ghzz             1/1     Running   0          81s

NAME                                TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                                                      AGE
service/grafana                     ClusterIP      10.0.226.39    <none>         3000/TCP                                                     5m54s
service/istio-ingressgateway        LoadBalancer   10.0.143.56    20.53.72.254   15021:32166/TCP,80:31684/TCP,443:31302/TCP,15443:30863/TCP   5m57s
service/istiod                      ClusterIP      10.0.211.228   <none>         15010/TCP,15012/TCP,443/TCP,15014/TCP,853/TCP                6m16s
service/jaeger-agent                ClusterIP      None           <none>         5775/UDP,6831/UDP,6832/UDP                                   82s
service/jaeger-collector            ClusterIP      10.0.7.62      <none>         14267/TCP,14268/TCP,14250/TCP                                82s
service/jaeger-collector-headless   ClusterIP      None           <none>         14250/TCP                                                    82s
service/jaeger-query                ClusterIP      10.0.52.172    <none>         16686/TCP                                                    82s
service/kiali                       ClusterIP      10.0.71.179    <none>         20001/TCP                                                    82s
service/prometheus                  ClusterIP      10.0.171.151   <none>         9090/TCP                                                     82s
service/tracing                     ClusterIP      10.0.195.137   <none>         80/TCP                                                       82s
service/zipkin                      ClusterIP      10.0.136.111   <none>         9411/TCP                                                     82s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana                1/1     1            1           5m54s
deployment.apps/istio-ingressgateway   1/1     1            1           5m58s
deployment.apps/istio-tracing          1/1     1            1           83s
deployment.apps/istiod                 1/1     1            1           6m16s
deployment.apps/kiali                  1/1     1            1           83s
deployment.apps/prometheus             1/1     1            1           82s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-7cf9794c74                1         1         1       5m54s
replicaset.apps/istio-ingressgateway-86b5dbdcb9   1         1         1       5m58s
replicaset.apps/istio-tracing-c98f4b8fc           1         1         1       83s
replicaset.apps/istiod-6965c56995                 1         1         1       6m16s
replicaset.apps/kiali-7b44985d68                  1         1         1       82s
replicaset.apps/prometheus-6868989549             1         1         1       82s

NAME                                                       REFERENCE                         TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   7%/80%    1         5         1          5m57s
horizontalpodautoscaler.autoscaling/istiod                 Deployment/istiod                 1%/80%    1         5         1          6m16s
```

Sie können auch zusätzliche Erkenntnisse in die Installation gewinnen, indem Sie sich die Protokolle für den Istio-Operator ansehen.

```bash
kubectl logs -n istio-operator -l name=istio-operator -f
```

Wenn das `istio-ingressgateway` für die externe IP-Adresse `<pending>` anzeigt, warten Sie einige Minuten, bis vom Azure-Netzwerk eine IP-Adresse zugewiesen wurde.

Alle Pods müssen den Status `Running` haben. Wenn Ihre Pods nicht diesen Status haben, warten Sie ein oder zwei Minuten, bis dies der Fall ist. Wenn einer der Pods ein Problem meldet, verwenden Sie den Befehl [kubectl describe pod][kubectl-describe], um die Ausgabe und den Status des betreffenden Pods zu überprüfen.

## <a name="accessing-the-add-ons"></a>Zugreifen auf die Add-Ons

Es wurden einige Add-Ons vom Istio-Operator installiert, die zusätzliche Funktionen bieten. Die Webanwendungen für die Add-Ons werden **nicht** öffentlich über eine externe IP-Adresse verfügbar gemacht. 

Verwenden Sie den Befehl `istioctl dashboard`, um auf diese Add-On-Benutzeroberflächen zuzugreifen. Dieser Befehl verwendet [kubectl port-forward][kubectl-port-forward] und einen willkürlichen Port, um eine sichere Verbindung zwischen Ihrem Clientcomputer und dem relevanten Pod in Ihrem AKS-Cluster herzustellen. Anschließend wird automatisch die Add-On-Webanwendung in Ihrem Standardbrowser geöffnet.

### <a name="grafana"></a>Grafana

Die Analyse- und Überwachungsdashboards für Istio werden von [Grafana][grafana] bereitgestellt. Sie müssen die Anmeldeinformationen verwenden, die Sie zuvor nach Aufforderung mithilfe des Grafana-Geheimnisses erstellten haben. Gehen Sie wie folgt vor, um das Grafana-Dashboard auf sichere Weise zu öffnen:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Die Metriken für Istio werden von [Prometheus][prometheus] bereitgestellt. Gehen Sie wie folgt vor, um das Prometheus-Dashboard auf sichere Weise zu öffnen:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Die Ablaufverfolgung in Istio wird über [Jaeger][jaeger] bereitgestellt. Gehen Sie wie folgt vor, um das Jaeger-Dashboard auf sichere Weise zu öffnen:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

Ein Service Mesh-Telemetriedashboard wird von [Kiali][kiali] bereitgestellt. Sie müssen die Anmeldeinformationen verwenden, die Sie zuvor nach Aufforderung mithilfe des Kiali-Geheimnisses erstellten haben. Gehen Sie wie folgt vor, um das Kiali-Dashboard auf sichere Weise zu öffnen:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Für die [Envoy][envoy]-Proxys steht eine einfache Oberfläche zur Verfügung. Sie enthält Konfigurationsinformationen und Metriken für einen Envoy-Proxy, der in einem bestimmten Pod ausgeführt wird. Gehen Sie wie folgt vor, um die Envoy-Oberfläche auf sichere Weise zu öffnen:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Deinstallieren von Istio aus AKS

> [!WARNING]
> Das Löschen von Istio in einem laufenden System kann zu Problemen beim Datenverkehr zwischen Ihren Diensten führen. Stellen Sie sicher, dass Sie das System so weit vorbereitet haben, dass es auch ohne Istio ordnungsgemäß fortgesetzt werden kann.

### <a name="remove-istio"></a>Entfernen von Istio

Um Istio aus Ihrem AKS-Cluster zu entfernen, löschen Sie die `IstioOperator`-Ressource namens `istio-control-plane`, die wir zuvor hinzugefügt haben. Der Istio-Operator erkennt, dass die Istio-Operator-Spezifikation entfernt wurde, und löscht dann alle zugeordneten Istio-Komponenten.

```bash
kubectl delete istiooperator istio-control-plane -n istio-system
```

Sie können Folgendes ausführen, um zu überprüfen, ob alle Istio-Komponenten gelöscht wurden.

```bash
kubectl get all -n istio-system
```

### <a name="remove-istio-operator"></a>Entfernen von Istio-Operator

Nachdem Istio erfolgreich deinstalliert wurde, können Sie auch den Istio-Operator entfernen.

```bash
istioctl operator remove
```

Entfernen Sie dann schließlich die `istio-`-Namespaces.

```bash
kubectl delete ns istio-system
kubectl delete ns istio-operator
```

## <a name="next-steps"></a>Nächste Schritte

Um mehr über Installations- und Konfigurationsoptionen für Istio zu erfahren, beachten Sie die folgenden offiziellen Istio-Leitfäden:

- [Istio: Installationsleitfäden][istio-installation-guides]

Weitere Szenarien finden Sie hier:

- [Istio-Anwendungsbeispiel: BookInfo][istio-bookinfo-example]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-install-operator]: https://istio.io/latest/docs/setup/install/operator/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[kubernetes-operator]: https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
