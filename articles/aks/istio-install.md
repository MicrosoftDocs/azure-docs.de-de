---
title: Installieren von Istio in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie Istio installieren und zum Erstellen eines Service Mesh in einem AKS-Cluster (Azure Kubernetes Service) verwenden.
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 33d86ab8c88b45c7787620773f0df6e7fe888cf3
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65850414"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installieren und Verwenden von Istio in Azure Kubernetes Service (AKS)

[Istio][istio-github] ist ein Open Source-Service Mesh, das eine Reihe wichtiger Funktionen für Microservices in einem Kubernetes-Cluster bietet. Zu diesen Funktionen gehören: Verwaltung des Datenverkehrs, Dienstidentität und -sicherheit, Richtlinienerzwingung und Telemetrie. Weitere Informationen zu Istio finden Sie in der offiziellen Dokumentation [What is Istio? (Was ist Istio)][istio-docs-concepts].

In diesem Artikel wird gezeigt, wie Sie Istio installieren. Die Istio-Clientbinärdatei `istioctl` wird auf Ihrem Clientcomputer installiert, und anschließend werden die Istio-Komponenten in einem Kubernetes-Cluster in AKS installiert.

> [!NOTE]
> Diese Anweisungen beziehen sich auf die Istio-Version `1.1.3`.
>
> Der Istio-Versionen `1.1.x` wurden vom Istio-Team mit den Kubernetes-Versionen `1.11`, `1.12` und `1.13` getestet. Weitere Istio-Versionen finden Sie unter [GitHub – Istio-Releases][istio-github-releases]. Weitere Informationen zu den Releases finden Sie unter [Istio – Versionshinweise][istio-release-notes].

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Herunterladen von Istio
> * Installieren der Istio-Clientbinärdatei „istioctl“
> * Installieren der Istio-CRDs in AKS
> * Installieren der Istio-Komponenten in AKS
> * Überprüfen der Istio-Installation
> * Zugreifen auf die Add-Ons
> * Deinstallieren von Istio aus AKS

## <a name="before-you-begin"></a>Voraussetzungen

Bei den in diesem Artikel beschriebenen Schritten wird vorausgesetzt, dass Sie einen AKS-Cluster (Kubernetes `1.11` und höher, RBAC aktiviert) erstellt und eine `kubectl`-Verbindung mit dem Cluster hergestellt haben. Wenn Sie Hilfe bei einem dieser Elemente benötigen, beachten Sie das Dokument [AKS-Schnellstart][aks-quickstart].

Sie benötigen für diese Anweisungen [Helm][helm] und müssen Istio installieren. Es wird empfohlen, im Cluster Version `2.12.2` oder höher ordnungsgemäß zu installieren und zu konfigurieren. Wenn Sie Hilfe bei der Installation von Helm benötigen, beachten Sie den [AKS Helm-Installationsleitfaden][helm-install]. Alle Istio-Pods müssen auch für die Ausführung auf Linux-Knoten geplant werden.

Im vorliegenden Artikel wird die Istio-Installationsanleitung in mehrere Einzelschritte untergliedert. Das Endergebnis hat dieselbe Struktur wie beim Befolgen der offiziellen [Anleitung][istio-install-helm] für die Installation von Istio.

## <a name="download-istio"></a>Herunterladen von Istio

Laden Sie zunächst das neueste Istio-Release herunter, und extrahieren Sie dieses. Die Schritte für eine Bash-Shell unter macOS, Linux oder einem Windows-Subsystem für Linux und eine PowerShell-Befehlsshell unterscheiden sich geringfügig. Wählen Sie einen der folgenden Installationsschritte gemäß Ihrer bevorzugten Umgebung aus:

* [Bash unter MacOS, Linux oder Windows-Subsystem für Linux](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

Verwenden Sie unter MacOS `curl` zum Herunterladen das neueste Istio-Release, und extrahieren Sie dieses dann wie folgt mit `tar`:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

Verwenden Sie unter Linux oder Windows-Subsystem für Linux `curl`, um das neueste Istio-Release herunterzuladen, und extrahieren Sie dieses dann wie folgt mit `tar`:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Wechseln Sie jetzt zum Abschnitt [Installieren der Istio-Clientbinärdatei „istioctl“](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

Verwenden Sie in PowerShell `Invoke-WebRequest` zum Herunterladen des neuesten Istio-Releases, und extrahieren Sie dieses dann wie folgt mit `Expand-Archive`:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Wechseln Sie jetzt zum Abschnitt [Installieren der Istio-Clientbinärdatei „istioctl“](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>Installieren der Istio-Clientbinärdatei „istioctl“

> [!IMPORTANT]
> Führen Sie alle Schritte in diesem Abschnitt im Ordner der obersten Ebene des Istio-Release aus, das Sie heruntergeladen und extrahiert haben.

Die Clientbinärdatei `istioctl` wird auf dem Clientcomputer ausgeführt und ermöglicht die Interaktion mit dem Istio-Dienstmesh. Die Installationsschritte unterscheiden sich bei den verschiedenen Clientbetriebssystemen leicht voneinander. Wählen Sie einen der folgenden Installationsschritte gemäß Ihrer bevorzugten Umgebung aus:

* [MacOS](#macos)
* [Linux oder Windows-Subsystem für Linux](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>macOS

Verwenden Sie die folgenden Befehle, um die Istio-Clientbinärdatei `istioctl` in einer Bash-basierten Shell unter MacOS zu installieren. Diese Befehle kopieren die `istioctl`-Clientbinärdatei an den Speicherort des Standardbenutzerprogramms in Ihrem `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Wenn Sie die Befehlszeilen-Vervollständigung für die Istio-Clientbinärdatei `istioctl` wünschen, richten Sie diese wie folgt ein:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Fahren Sie nun mit dem nächsten Abschnitt [Installieren der Istio-CRDs in AKS](#install-the-istio-crds-on-aks) fort.

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux oder Windows-Subsystem für Linux

Verwenden Sie die folgenden Befehle, um die Istio-Clientbinärdatei `istioctl` in einer Bash-basierten Shell unter Linux oder [Windows-Subsystem für Linux][install-wsl] zu installieren. Diese Befehle kopieren die `istioctl`-Clientbinärdatei an den Speicherort des Standardbenutzerprogramms in Ihrem `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Wenn Sie die Befehlszeilen-Vervollständigung für die Istio-Clientbinärdatei `istioctl` wünschen, richten Sie diese wie folgt ein:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Fahren Sie nun mit dem nächsten Abschnitt [Installieren der Istio-CRDs in AKS](#install-the-istio-crds-on-aks) fort.

### <a name="windows"></a>Windows

Verwenden Sie die folgenden Befehle, um die Istio-Clientbinärdatei `istioctl` in einer **PowerShell**-basierten Shell unter Windows zu installieren. Diese Befehle kopieren die Clientbinärdatei `istioctl` in einen Istio-Ordner und stellen sie dauerhaft über Ihren `PATH` zur Verfügung. Sie benötigen keine erhöhten Rechte (Administrator), um diese Befehle auszuführen.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Istio\", "User")
```

Fahren Sie nun mit dem nächsten Abschnitt [Installieren der Istio-CRDs in AKS](#install-the-istio-crds-on-aks) fort.

## <a name="install-the-istio-crds-on-aks"></a>Installieren der Istio-CRDs in AKS

> [!IMPORTANT]
> Führen Sie alle Schritte in diesem Abschnitt im Ordner der obersten Ebene des Istio-Release aus, das Sie heruntergeladen und extrahiert haben.

Istio verwendet [benutzerdefinierte Ressourcendefinitionen (Custom Resource Definitions, CRDs)][kubernetes-crd] für das Verwalten der Laufzeitkonfiguration. Wir müssen die Istio-CRDs zuerst installieren, da die Istio-Komponenten Abhängigkeiten zu diesen aufweisen. Verwenden Sie Helm und das Diagramm `istio-init` für das Installieren der Istio-CRDs im `istio-system`-Namespace in Ihrem AKS-Cluster:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[Aufträge][kubernetes-jobs] werden als Teil des Helm-Diagramms `istio-init` bereitgestellt, um die CRDs zu installieren. Die Ausführung dieser Aufträge sollte je nach Ihrer Clusterumgebung zwischen 1 und 2 Minuten dauern. Folgendermaßen können Sie überprüfen, ob die Aufträge erfolgreich abgeschlossen wurden:

```azurecli
kubectl get jobs -n istio-system
```

Die folgende Beispielausgabe zeigt, dass die Aufträge erfolgreich abgeschlossen wurden.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Nachdem Sie den erfolgreichen Abschluss der Aufträge bestätigt haben, überprüfen Sie, ob die richtige Anzahl von Istio-CRDs installiert wurde. Sie können überprüfen, ob alle 53 Istio-CRDs installiert wurden, indem Sie den entsprechenden Befehl für Ihre Umgebung ausführen. Der Befehl sollte als Anzahl `53` zurückgeben.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

PowerShell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Wenn Sie diesen Punkt erreichen, haben Sie die Istio-CRDs erfolgreich installiert. Fahren Sie nun mit dem nächsten Abschnitt [Installieren der Istio-Komponenten in AKS](#install-the-istio-components-on-aks) fort.

## <a name="install-the-istio-components-on-aks"></a>Installieren der Istio-Komponenten in AKS

> [!IMPORTANT]
> Führen Sie alle Schritte in diesem Abschnitt im Ordner der obersten Ebene des Istio-Release aus, das Sie heruntergeladen und extrahiert haben.

Wir installieren [Grafana][grafana] und [Kiali][kiali] als Teil unserer Istio-Installation. Grafana stellt Analyse- und Überwachungsdashboards bereit, und Kiali bietet ein Übersichtsdashboard für das Dienstmesh. Bei diesem Setup erfordert jede dieser Komponenten Anmeldeinformationen, die als ein [Geheimnis][kubernetes-secrets] bereitgestellt werden müssen.

Bevor Sie die Istio-Komponenten installieren können, müssen Sie die Geheimnisse für Grafana und Kiali erstellen. Sie erstellen diese Geheimnisse durch Ausführen der entsprechenden Befehle für Ihre Umgebung.

### <a name="add-grafana-secret"></a>Hinzufügen von Grafana-Geheimnissen

Ersetzen Sie das `REPLACE_WITH_YOUR_SECURE_PASSWORD`-Token durch Ihr Kennwort, und führen Sie die folgenden Befehle aus:

#### <a name="macos-linux"></a>macOS, Linux

```bash
GRAFANA_USERNAME=$(echo -n "grafana" | base64)
GRAFANA_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$GRAFANA_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("grafana"))
$GRAFANA_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE" | kubectl apply -f -
```

### <a name="add-kiali-secret"></a>Hinzufügen von Kiali-Geheimnissen

Ersetzen Sie das `REPLACE_WITH_YOUR_SECURE_PASSWORD`-Token durch Ihr Kennwort, und führen Sie die folgenden Befehle aus:

#### <a name="macos-linux"></a>macOS, Linux

```bash
KIALI_USERNAME=$(echo -n "kiali" | base64)
KIALI_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$KIALI_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("kiali"))
$KIALI_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE" | kubectl apply -f -
```

### <a name="install-istio-components"></a>Installieren der Istio-Komponenten

Nachdem Sie in Ihrem AKS-Cluster erfolgreich die Geheimnisse für Grafana und Kiali erstellt haben, können Sie die Istio-Komponenten installieren. Verwenden Sie Helm und das Diagramm `istio` für das Installieren der Istio-Komponenten im `istio-system`-Namespace in Ihrem AKS-Cluster. Verwenden Sie die passenden Befehle für Ihre Umgebung.

> [!NOTE]
> Im Rahmen dieser Installation verwenden Sie die folgenden Optionen:
> - `global.controlPlaneSecurityEnabled=true`: Aktivieren von gegenseitigem TLS für die Steuerungsebene
> - `mixer.adapters.useAdapterCRDs=false`: Entfernen der Überwachungen für Mixeradapter-CRDs, da diese demnächst als veraltet markiert werden und dadurch die Leistung verbessert wird
> - `grafana.enabled=true`: Aktivieren der Grafana-Bereitstellung für Analyse- und Überwachungsdashboards
> - `grafana.security.enabled=true`: Aktivieren der Authentifizierung für Grafana
> - `tracing.enabled=true`: Aktivieren der Jaeger-Bereitstellung für die Ablaufverfolgung
> - `kiali.enabled=true`: Aktivieren der Kiali-Bereitstellung für ein Übersichtsdashboard für das Dienstmesh

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

PowerShell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

Das Helm-Diagramm `istio` stellt eine große Anzahl von Objekten bereit. Eine Liste der Ausgabe Ihres Befehls `helm install` finden Sie weiter oben. Je nach Clusterumgebung kann die Bereitstellung der Istio-Komponenten 4 bis 5 Minuten dauern.

> [!NOTE]
> Alle Istio-Pods müssen für die Ausführung auf Linux-Knoten geplant werden. Wenn Sie in Ihrem Cluster zusätzlich zu den Linux-Knotenpools auch über Windows Server-Knotenpools verfügen, vergewissern Sie sich, dass alle Istio-Pods für die Ausführung auf Linux-Knoten geplant wurden.

An diesem Punkt haben Sie Istio in Ihrem AKS-Cluster bereitgestellt. Um eine erfolgreiche Bereitstellung von Istio sicherzustellen, fahren Sie mit dem nächsten Abschnitt [Überprüfen der Istio-Installation](#validate-the-istio-installation) fort.

## <a name="validate-the-istio-installation"></a>Überprüfen der Istio-Installation

Vergewissern Sie sich zunächst, dass die erwarteten Dienste erstellt wurden. Verwenden Sie den Befehl [Kubectl get svc][kubectl-get], um die ausgeführten Dienste anzuzeigen. Fragen Sie den `istio-system`-Namespace ab, in dem die Istio- und Add-On-Komponenten vom Helm-Diagramm `istio` installiert wurden:

```console
kubectl get svc --namespace istio-system --output wide
```

Die folgende Beispielausgabe zeigt die Dienste, die jetzt ausgeführt werden sollten:

- `istio-*`-Dienste
- Add-On-Ablaufverfolgungsdienste `jaeger-*`, `tracing` und `zipkin`
- Add-On-Metrikdienst `prometheus`
- Add-On-Dienst `grafana` für Analyse- und -Überwachungsdashboards
- Add-On-Dashboarddienst `kiali` für das Dienstmesh

Wenn das `istio-ingressgateway` für die externe IP-Adresse `<pending>` anzeigt, warten Sie einige Minuten, bis vom Azure-Netzwerk eine IP-Adresse zugewiesen wurde.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                                                                                      AGE       SELECTOR
grafana                  ClusterIP      10.0.81.182    <none>          3000/TCP                                                                                                                                     119s      app=grafana
istio-citadel            ClusterIP      10.0.96.33     <none>          8060/TCP,15014/TCP                                                                                                                           119s      istio=citadel
istio-galley             ClusterIP      10.0.237.158   <none>          443/TCP,15014/TCP,9901/TCP                                                                                                                   119s      istio=galley
istio-ingressgateway     LoadBalancer   10.0.154.12    20.188.211.19   15020:30603/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:31198/TCP,15030:30610/TCP,15031:30937/TCP,15032:31344/TCP,15443:31499/TCP   119s      app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.178.56    <none>          15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       119s      istio=pilot
istio-policy             ClusterIP      10.0.116.118   <none>          9091/TCP,15004/TCP,15014/TCP                                                                                                                 119s      istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.31.160    <none>          443/TCP                                                                                                                                      119s      istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.187.246   <none>          9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       119s      istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>          5775/UDP,6831/UDP,6832/UDP                                                                                                                   119s      app=jaeger
jaeger-collector         ClusterIP      10.0.116.63    <none>          14267/TCP,14268/TCP                                                                                                                          119s      app=jaeger
jaeger-query             ClusterIP      10.0.22.108    <none>          16686/TCP                                                                                                                                    119s      app=jaeger
kiali                    ClusterIP      10.0.142.50    <none>          20001/TCP                                                                                                                                    119s      app=kiali
prometheus               ClusterIP      10.0.138.134   <none>          9090/TCP                                                                                                                                     119s      app=prometheus
tracing                  ClusterIP      10.0.165.210   <none>          80/TCP                                                                                                                                       118s      app=jaeger
zipkin                   ClusterIP      10.0.126.211   <none>          9411/TCP                                                                                                                                     118s      app=jaeger
```

Vergewissern Sie sich als Nächstes, dass die erforderlichen Pods erstellt wurden. Verwenden Sie den Befehl [kubectl get pods][kubectl-get], und fragen Sie den `istio-system`-Namespace erneut ab:

```console
kubectl get pods --namespace istio-system
```

Die folgende Beispielausgabe zeigt die Pods, die ausgeführt werden:

- `istio-*`-Pods
- Add-On-Metrikpod `prometheus-*`
- Add-On-Pod `grafana-*` für Analyse- und -Überwachungsdashboards
- Add-On-Pod `kiali` für Dienstmeshdashboard

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-88779954d-nzpm7                  1/1       Running     0          6m26s
istio-citadel-7f699dc8c8-n7q8g           1/1       Running     0          6m26s
istio-galley-649bc8cd97-wfjzm            1/1       Running     0          6m26s
istio-ingressgateway-65dfbd566-42wkn     1/1       Running     0          6m26s
istio-init-crd-10-tmtw5                  0/1       Completed   0          20m38s
istio-init-crd-11-ql25l                  0/1       Completed   0          20m38s
istio-pilot-958dd8cc4-4ckf9              2/2       Running     0          6m26s
istio-policy-86b4b7cf9-zf7v7             2/2       Running     4          6m26s
istio-sidecar-injector-d48786c5c-pmrj9   1/1       Running     0          6m26s
istio-telemetry-7f6996fdcc-84w94         2/2       Running     3          6m26s
istio-tracing-79db5954f-h7hmz            1/1       Running     0          6m26s
kiali-5c4cdbb869-s28dv                   1/1       Running     0          6m26s
prometheus-67599bf55b-pgxd8              1/1       Running     0          6m26s
```

Es sollen zwei `istio-init-crd-*`-Pods mit dem Status `Completed` vorhanden sein. Diese Pods waren verantwortlich für die Ausführung der Aufträge zur Erstellung der CRDs in einem vorherigen Schritt. Alle anderen Pods sollten den Status `Running` aufweisen. Wenn Ihre Pods nicht diesen Status haben, warten Sie ein oder zwei Minuten, bis dies der Fall ist. Wenn einer der Pods ein Problem meldet, verwenden Sie den Befehl [kubectl describe pod] [ kubectl-describe], um die Ausgabe und den Status des betreffenden Pods zu überprüfen.

## <a name="accessing-the-add-ons"></a>Zugreifen auf die Add-Ons

Einige der bei der obigen Einrichtung installierten Add-ons für Istio bieten zusätzliche Funktionen. Die Benutzeroberflächen für die Add-Ons werden nicht über eine externe IP-Adresse öffentlich verfügbar gemacht. Um auf diese Add-On-Benutzeroberflächen zuzugreifen, verwenden Sie den Befehl [kubectl port-forward][kubectl-port-forward]. Dieser Befehl erstellt eine sichere Verbindung zwischen Ihrem Clientcomputer und dem entsprechenden Pod in Ihrem AKS-Cluster.

Sie haben zuvor in diesem Artikel für Grafana und Kiali eine zusätzliche Sicherheitsebene hinzugefügt, indem Sie Anmeldeinformationen angegeben haben.

### <a name="grafana"></a>Grafana

Die Analyse- und Überwachungsdashboards für Istio werden von [Grafana][grafana] bereitgestellt. Leiten Sie den lokalen Port `3000` auf Ihrem Clientcomputer an Port `3000` auf dem Pod weiter, auf dem Grafana in Ihrem AKS-Cluster ausgeführt wird:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

Die folgende Beispielausgabe zeigt die für Grafana konfigurierte Portweiterleitung:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Sie können jetzt unter folgender URL auf Ihrem Clientcomputer auf Grafana zugreifen – [ http://localhost:3000 ](http://localhost:3000). Sie müssen die Anmeldeinformationen verwenden, die Sie zuvor nach Aufforderung mithilfe des Grafana-Geheimnisses erstellten haben.

### <a name="prometheus"></a>Prometheus

Die Metriken für Istio werden von [Prometheus][prometheus] bereitgestellt. Leiten Sie den lokalen Port `9090` auf Ihrem Clientcomputer an Port `9090` auf dem Pod weiter, auf dem Prometheus in Ihrem AKS-Cluster ausgeführt wird:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

Die folgende Beispielausgabe zeigt die für Prometheus konfigurierte Portweiterleitung:

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

Sie erreichen jetzt den Prometheus-Ausdrucksbrowser unter folgender URL auf Ihrem Clientcomputer – [ http://localhost:9090 ](http://localhost:9090).

### <a name="jaeger"></a>Jaeger

Die Ablaufverfolgung in Istio wird über [Jaeger][jaeger] bereitgestellt. Leiten Sie den lokalen Port `16686` auf Ihrem Clientcomputer an Port `16686` auf dem Pod weiter, auf dem Jaeger in Ihrem AKS-Cluster ausgeführt wird:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

Die folgende Beispielausgabe zeigt die für Jaeger konfigurierte Portweiterleitung:

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

Sie erreichen jetzt die Benutzeroberfläche von Jaeger für die Ablaufverfolgung unter folgender URL auf Ihrem Clientcomputer – [ http://localhost:16686 ](http://localhost:16686).

### <a name="kiali"></a>Kiali

Ein Service Mesh-Telemetriedashboard wird von [Kiali][kiali] bereitgestellt. Leiten Sie den lokalen Port `20001` auf Ihrem Clientcomputer an Port `20001` auf dem Pod weiter, auf dem Kiali in Ihrem AKS-Cluster ausgeführt wird:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

Die folgende Beispielausgabe zeigt die für Kiali konfigurierte Portweiterleitung:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Sie erreichen jetzt das Service Mesh-Telemetriedashboard von Kiali unter der folgenden URL auf Ihrem Clientcomputer – [ http://localhost:20001/kiali/console/ ](http://localhost:20001/kiali/console/). Sie müssen die Anmeldeinformationen verwenden, die Sie zuvor nach Aufforderung mithilfe des Kiali-Geheimnisses erstellten haben.

## <a name="uninstall-istio-from-aks"></a>Deinstallieren von Istio aus AKS

> [!WARNING]
> Das Löschen von Istio in einem laufenden System kann zu Problemen beim Datenverkehr zwischen Ihren Diensten führen. Stellen Sie sicher, dass Sie das System so weit vorbereitet haben, dass es auch ohne Istio ordnungsgemäß fortgesetzt werden kann.

### <a name="remove-istio-components-and-namespace"></a>Entfernen von Istio-Komponenten und -Namespace

Verwenden Sie die folgenden Befehle, um Istio aus Ihrem AKS-Cluster zu entfernen. Die Befehle `helm delete` entfernen die Diagramme `istio` und `istio-init`, und der Befehl `kubectl delete ns` entfernt den `istio-system`-Namespace.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Entfernen der Istio-CRDs

Die oben aufgeführten Befehle löschen alle Istio-Komponenten und den Namespace, die Istio-CRDs bleiben aber weiterhin erhalten. Um die CRDs zu löschen, können Sie einen der folgenden Ansätze anwenden.

Ansatz 1: Bei diesem Befehl wird davon ausgegangen, dass Sie diesen Schritt im Ordner auf oberster Ebene des heruntergeladenen und extrahierten Istio-Releases ausführen, mit dem Sie Istio installiert haben.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Ansatz 2: Verwenden Sie einen dieser Befehle, wenn Sie keinen Zugriff mehr auf das heruntergeladene und extrahierte Istio-Release mehr haben, mit dem Sie Istio installiert haben. Die Ausführung dieses Befehls dauert etwas länger (einige Minuten).

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

PowerShell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Nächste Schritte

In der folgenden Dokumentation wird beschrieben, wie Sie Istio verwenden können, um ein intelligentes Routing für den Rollout eines Canaryrelease bereitzustellen:

> [!div class="nextstepaction"]
> [AKS Istio intelligent routing scenario (Szenario für intelligentes Routing mit AKS Istio)][istio-scenario-routing]

Um mehr über Installations- und Konfigurationsoptionen für Istio zu erfahren, beachten Sie die folgenden offiziellen Istio-Artikel:

- [Istio – Helm installation guide (Istio – Installationshandbuch für Helm)][istio-install-helm]
- [Istio – Helm installation options (Istio – Installationsoptionen für Helm)][istio-install-helm-options]

Mithilfe des [Istio Bookinfo-Anwendungsbeispiels][istio-bookinfo-example] können Sie sich weitere Szenarios ansehen.

Um zu erfahren, wie Sie Ihre AKS-Anwendung mit Application Insights und Istio überwachen können, lesen Sie die folgende Dokumentation zu Azure Monitor:
- [Anwendungsüberwachung ohne Instrumentierung für gehostete Kubernetes-Anwendungen][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/about/notes/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md