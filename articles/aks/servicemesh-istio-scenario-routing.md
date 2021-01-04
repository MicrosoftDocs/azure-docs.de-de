---
title: Verwenden von Istio für intelligentes Routing
titleSuffix: Azure Kubernetes Service
description: Erfahren Sie, wie Sie mit Istio intelligentes Routing und Canary-Releases in einem Cluster von Azure Kubernetes Service (AKS) bereitstellen
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d66f3099ba225fbdd2bfc3d54db56ffd8ed2c43f
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684031"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Verwenden von intelligentem Routing und Canary-Releases mit Istio in Azure Kubernetes Service (AKS)

[Istio][istio-github] ist ein Open-Source-Dienstmesh, das eine Reihe wichtiger Funktionen für Microservices in einem Kubernetes-Cluster bereitstellt. Zu diesen Funktionen gehören: Verwaltung des Datenverkehrs, Dienstidentität und -sicherheit, Richtlinienerzwingung und Telemetrie. Weitere Informationen zu Istio finden Sie in der offiziellen Dokumentation [What is Istio?][istio-docs-concepts] (Was ist Istio?).

In diesem Artikel wird die Verwendung der Funktion zur Verwaltung des Datenverkehrs von Istio beschrieben. Um intelligentes Routing und Canary-Releases zu untersuchen, wird eine als Beispiel dienende AKS-Abstimmungs-App verwendet.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Bereitstellen der Anwendung
> * Aktualisieren der Anwendung
> * Rollout eines Canary-Releases der Anwendung
> * Abschließen des Rollouts

## <a name="before-you-begin"></a>Voraussetzungen

> [!NOTE]
> Dieses Szenario wurde mit der Istio-Version `1.3.2` getestet.

Bei den in diesem Artikel beschriebenen Schritten wird vorausgesetzt, dass Sie einen AKS-Cluster (ab Kubernetes `1.13`, Kubernetes RBAC aktiviert) erstellt und eine `kubectl`-Verbindung mit dem Cluster hergestellt haben. Ferner muss Istio in Ihrem Cluster installiert sein.

Wenn Sie Hilfe bei einem dieser Elemente benötigen, lesen Sie den [AKS-Schnellstart][aks-quickstart] und die Anleitung [Installieren und Verwenden von Istio in Azure Kubernetes Service (AKS)][istio-install].

## <a name="about-this-application-scenario"></a>Informationen zu diesem Anwendungsszenario

In der als Beispiel dienenden AKS-Abstimmungs-App können Benutzer zwischen zwei Abstimmungsoptionen (**Katzen** oder **Hunde**) wählen. Es gibt eine Speicherkomponente, die die Anzahl der abgegebenen Stimmen für die einzelne Optionen persistent speichert. Darüber hinaus gibt es eine Analysekomponente, die Details zu den für jede Option abgegebenen Stimmen bereitstellt.

In diesem Anwendungsszenario stellen Sie zunächst Version `1.0` der Abstimmungs-App und Version `1.0` der Analysekomponente bereit. Die Analysekomponente bietet einfache Zähler für die Anzahl der Stimmen. Die Abstimmungs-App und die Analysekomponente interagieren mit Version `1.0` der durch Redis unterstützten Speicherkomponente.

Sie führen ein Upgrade der Analysekomponente auf Version `1.1` durch, die Zähler und jetzt auch Summen und Prozentwerte bietet.

Eine Teilmenge der Benutzer testet Version `2.0` der App über ein Canary-Release. Diese neue Version verwendet eine Speicherkomponente, die durch eine MySQL-Datenbank unterstützt wird.

Sobald Sie sicher sind, dass Version `2.0` bei Ihrer Teilmenge der Benutzer wie erwartet funktioniert, führen Sie für alle Ihre Benutzer ein Rollout von Version `2.0` durch.

## <a name="deploy-the-application"></a>Bereitstellen der Anwendung

Wir beginnen mit der Bereitstellung der Anwendung in Ihrem AKS-Cluster (Azure Kubernetes Service). Das folgende Diagramm zeigt, was am Ende dieses Abschnitts ausgeführt wird: Version `1.0` aller Komponenten, wobei eingehende Anforderungen über das Istio-Eingangsgateway verarbeitet werden:

![Diagramm von Version 1.0 aller Komponenten mit eingehenden Anforderungen, die über das Istio-Eingangsgateway verarbeitet werden](media/servicemesh/istio/scenario-routing-components-01.png)

Die Artefakte, die Sie für diesen Artikel benötigen, finden Sie im GitHub-Repository [Azure-Samples/aks-voting-app][github-azure-sample]. Sie können entweder die Artefakte herunterladen oder das Repository wie folgt klonen:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Wechseln Sie im heruntergeladenen bzw. geklonten Repository in den folgenden Ordner, und führen Sie alle nachfolgenden Schritte aus diesem Ordner aus:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Erstellen Sie zunächst wie folgt für die als Beispiel dienende AKS-Abstimmungs-App in Ihrem AKS-Cluster einen Namespace mit dem Namen `voting`:

```console
kubectl create namespace voting
```

Bezeichnen Sie den Namespace mit `istio-injection=enabled`. Diese Bezeichnung weist Istio an, automatisch die Istio-Proxys als Sidecars in alle Ihre Pods in diesem Namespace einzufügen.

```console
kubectl label namespace voting istio-injection=enabled
```

Nun erstellen wir die Komponenten für die AKS-Abstimmungs-App. Erstellen Sie diese Komponenten im `voting`-Namespace, den Sie oben erstellt haben.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Die folgende Beispielausgabe zeigt, dass die Ressourcen erstellt wurden:

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio verfügt über bestimmte Anforderungen für Pods und Dienste. Weitere Informationen finden Sie in der [Dokumentation zu Istio-Anforderungen für Pods und Dienste][istio-requirements-pods-and-services].

Um die erstellten Pods anzuzeigen, verwenden Sie wie folgt den Befehl [kubectl get pods][kubectl-get]:

```console
kubectl get pods -n voting --show-labels
```

Die folgende Beispielausgabe zeigt, dass es drei Instanzen des Pods `voting-app` und je eine Instanz der Pods `voting-analytics` und `voting-storage` gibt. Jede der Pods verfügt über zwei Container. Einer dieser Container ist die Komponente, der andere der `istio-proxy`:

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Zum Anzeigen von Informationen zum Pod verwenden wir den Befehl [kubectl describe pod][kubectl-describe] mit Bezeichnungsselektoren, um den Pod `voting-analytics` auszuwählen. Wir filtern die Ausgabe, um die Details der beiden im Pod enthaltenen Container anzuzeigen:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Sie können erst dann eine Verbindung mit der Abstimmungs-App herstellen, nachdem Sie das [Gateway][istio-reference-gateway] und den [virtuellen Dienst][istio-reference-virtualservice] für Istio erstellt haben. Diese Istio-Ressourcen leiten Datenverkehr vom Standard-Istio-Eingangsgateway an Ihre Anwendung weiter.

> [!NOTE]
> Ein **Gateway** ist eine Komponente am Rand des Service Mesh, die eingehenden oder ausgehenden HTTP- und TCP-Datenverkehr empfängt.
> 
> Ein **virtueller Dienst** definiert Routingregeln für einen oder mehrere Zieldienste.

Verwenden Sie den Befehl `kubectl apply`, um das Gateway und den virtuellen Dienst yaml bereitzustellen. Denken Sie daran, den Namespace anzugeben, in dem diese Ressourcen bereitgestellt werden.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Die folgende Beispielausgabe zeigt das erstellte neue Gateway und den virtuellen Dienst:

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Rufen Sie mit dem folgenden Befehl die IP-Adresse des Istio-Eingangsgateways ab:

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Die folgende Beispielausgabe zeigt die IP-Adresse des Eingangsgateways:

```output
20.188.211.19
```

Öffnen Sie einen Browser, und fügen Sie die IP-Adresse ein. Die als Beispiel dienende AKS-Abstimmungs-App wird angezeigt.

![Die AKS-Abstimmungs-App wird in Ihrem für Istio aktivierten AKS-Cluster ausgeführt.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

Aus den Informationen am unteren Bildschirmrand geht hervor, dass die App Version `1.0` von `voting-app` und Version `1.0` von `voting-storage` (Redis) verwendet.

## <a name="update-the-application"></a>Aktualisieren der Anwendung

Nun wird eine neue Version der Analysekomponente bereitgestellt. Diese neue Version `1.1` zeigt zusätzlich zum Zähler für jede Kategorie auch Summen und Prozentwerte an.

Das folgende Diagramm zeigt, was am Ende dieses Abschnitts ausgeführt wird. Nur an Version `1.1` unserer Komponente `voting-analytics` wird Datenverkehr von Komponente `voting-app` geleitet. Zwar wird Version `1.0` unserer Komponente `voting-analytics` weiterhin ausgeführt und vom Dienst `voting-analytics` referenziert, doch die Istio-Proxys erlauben keinen Datenverkehr von und zu dieser Komponente.

![Diagramm, das veranschaulicht, dass nur Version 1.1 der Komponente „voting-analytics“ über Datenverkehr verfügt, der von der Komponente „voting-app“ weitergeleitet wird](media/servicemesh/istio/scenario-routing-components-02.png)

Wir stellen Version `1.1` der Komponente `voting-analytics` bereit. Erstellen Sie diese Komponente im Namespace `voting`:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Die folgende Beispielausgabe zeigt, dass die Ressourcen erstellt wurden:

```output
deployment.apps/voting-analytics-1-1 created
```

Öffnen Sie die als Beispiel dienende AKS-Abstimmungs-App erneut in einem Browser. Verwenden Sie dabei die IP-Adresse des Istio-Eingangsgateways, die Sie im vorherigen Schritt ermittelt haben.

Ihr Browser wechselt zwischen den beiden unten gezeigten Ansichten. Da Sie für die Komponente `voting-analytics` einen Kubernetes-[Service][kubernetes-service] mit nur einem Bezeichnungsselektor (`app: voting-analytics`) verwenden, verwendet Kubernetes das Standardverhalten „Roundrobin“ zwischen den Pods, die diesem Selektor entsprechen. In diesem Fall ist es sowohl die Version `1.0` als auch die Version `1.1` Ihrer Pods des Typs `voting-analytics`.

![Version 1.0 der Analytics-Komponente, die in unserer AKS-Abstimmungs-App ausgeführt wird.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Version 1.1 der Analytics-Komponente, die in unserer AKS-Abstimmungs-App ausgeführt wird.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Das Umschalten zwischen den beiden Versionen der `voting-analytics`-Komponente können Sie wie folgt sichtbar machen. Denken Sie daran, die IP-Adresse Ihres eigenen Istio-Eingangsgateways zu verwenden.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

Die folgende Beispielausgabe zeigt den relevanten Teil der zurückgegebenen Website, während die Website zwischen den Versionen wechselt:

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Begrenzen des Datenverkehrs auf Version 1.1 der Anwendung

Lassen Sie uns nun den Datenverkehr nur auf Version `1.1` der Komponente `voting-analytics` und Version `1.0` der Komponente `voting-storage` beschränken. Anschließend definieren Sie Routingregeln für alle übrigen Komponenten.

> * Ein **virtueller Dienst** definiert Routingregeln für einen oder mehrere Zieldienste.
> * Eine **Zielregel** definiert Datenverkehrsrichtlinien und versionsspezifische Richtlinien.
> * Eine **Richtlinie** definiert, welche Authentifizierungsmethoden für Workloads akzeptiert werden können.

Sie verwenden den Befehl `kubectl apply`, um die Definition Ihrer `voting-app` für den virtuellen Dienst zu ersetzen und [Zielregeln][istio-reference-destinationrule] und [virtuelle Dienste][istio-reference-virtualservice] für die übrigen Komponenten hinzuzufügen. Sie fügen eine [Richtlinie][istio-reference-policy] zum `voting`-Namespace hinzu, um sicherzustellen, dass die gesamte Kommunikation zwischen Diensten durch gegenseitige TLS und Clientzertifikate geschützt wird.

* Für die Richtlinie ist `peers.mtls.mode` auf `STRICT` festgelegt, um sicherzustellen, dass innerhalb des `voting`-Namespace gegenseitige TLS zwischen Ihren Diensten erzwungen wird.
* Wir legen außerdem in allen unseren Zielregeln `trafficPolicy.tls.mode` auf `ISTIO_MUTUAL` fest. Istio stellt Dienste mit starken Identitäten bereit und schützt die Kommunikation zwischen Diensten mithilfe von gegenseitigen TLS- und Clientzertifikaten, die Istio transparent verwaltet.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

Die folgende Beispielausgabe zeigt, dass die neue Richtlinie, die Zielregeln und die virtuellen Dienste erfolgreich erstellt bzw. aktualisiert wurden:

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Wenn Sie die AKS-Abstimmungs-App erneut in einem Browser öffnen, wird von der `voting-analytics`-Komponente nur die neue Version `1.1` der `voting-app`-Komponente verwendet.

![Version 1.1 der Analytics-Komponente, die in unserer AKS-Abstimmungs-App ausgeführt wird.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Sie können wie folgt aufzeigen, dass das Routing jetzt nur zu Version `1.1` Ihrer `voting-analytics`-Komponente erfolgt. Denken Sie daran, die IP-Adresse Ihres eigenen Istio-Eingangsgateways zu verwenden:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

Die folgende Beispielausgabe zeigt den relevanten Teil der zurückgegebenen Website:

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Vergewissern Sie sich, dass Istio gegenseitige TLS zum Schützen der Kommunikation zwischen Ihren einzelnen Diensten verwendet. Dazu wenden wir den Befehl [authn tls-check][istioctl-authn-tls-check] auf die Clientbinärdatei `istioctl` an, der die folgende Form annimmt.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Dieser Befehlssatz liefert Informationen zum Zugriff auf die angegebenen Dienste von allen Pods, die sich in einem Namespace befinden und einem Satz von Beschriftungen entsprechen:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

Diese folgende Beispielausgabe zeigt, dass gegenseitige TLS für jede unserer obigen Abfragen erzwungen wird. Die Ausgabe zeigt auch die Richtlinien und Zielregeln, die gegenseitige TLS erzwingen:

```output
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Rollout eines Canary-Releases der Anwendung

Lassen Sie uns nun eine neue Version `2.0` der Komponenten `voting-app`, `voting-analytics` und `voting-storage` bereitstellen. Die neue `voting-storage`-Komponente verwendet MySQL anstelle von Redis, und die Komponenten `voting-app` und `voting-analytics` werden so aktualisiert, dass sie diese neue `voting-storage`-Komponente verwenden können.

Die `voting-app`-Komponente unterstützt nun die Funktion „Featureflags“. Mit diesem Featureflag können Sie die Eignung des Canary-Release von Istio für eine Teilmenge von Benutzern testen.

Das folgende Diagramm zeigt, was am Ende dieses Abschnitts ausgeführt wird.

* Version `1.0` der `voting-app`-Komponente, Version `1.1` der `voting-analytics`-Komponente und Version `1.0` der `voting-storage`-Komponente können miteinander kommunizieren.
* Version `2.0` der `voting-app`-Komponente, Version `2.0` der `voting-analytics`-Komponente und Version `2.0` der `voting-storage`-Komponente können miteinander kommunizieren.
* Auf Version `2.0` der `voting-app`-Komponente können nur Benutzer zugreifen, für die ein bestimmtes Featureflag festgelegt ist. Diese Änderung wird mit einem Featureflag über ein Cookie verwaltet.

![Diagramm, das veranschaulicht, was nach diesem Abschnitt ausgeführt werden sollte](media/servicemesh/istio/scenario-routing-components-03.png)

Aktualisieren Sie zunächst die Istio-Zielregeln und die virtuellen Dienste, sodass diese neuen Komponenten berücksichtigt werden. Diese Aktualisierungen stellen sicher, dass Sie keinen Datenverkehr falsch an die neuen Komponenten weiterleiten und die Benutzer keinen unerwarteten Zugriff erhalten:

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Die folgende Beispielausgabe zeigt, dass die Zielregeln und virtuellen Dienste erfolgreich aktualisiert wurden:

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Nun fügen wir die Kubernetes-Objekte für die Komponenten der neuen Version `2.0` hinzu. Sie aktualisieren außerdem den `voting-storage`-Dienst so, dass auch der Port `3306` für MySQL eingeschlossen wird:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Die folgende Beispielausgabe zeigt, dass die Kubernetes-Objekte erfolgreich aktualisiert bzw. erstellt wurden:

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Warten Sie, bis alle Pods der Version `2.0` ausgeführt werden. Verwenden Sie den Befehl [kubectl get pods][kubectl-get] mit dem Überwachungsswitch `-w`, um die Änderungsüberwachung auf allen Pods im Namespace `voting` durchzuführen:

```console
kubectl get pods --namespace voting -w
```

Sie sollten jetzt zwischen Version `1.0` und Version `2.0` (Canary) der Abstimmungsanwendung wechseln können. Der Featureflag-Umschalter am unteren Bildschirmrand setzt ein Cookie. Dieses Cookie wird vom virtuellen Dienst der `voting-app` verwendet, um Benutzer zur neuen Version `2.0` zu leiten.

![Version 1.0 der AKS-Abstimmungs-App: Featureflag IST NICHT festgelegt.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![Version 2.0 der AKS-Abstimmungs-App: Featureflag IST festgelegt.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

Die Abstimmungszähler unterscheiden sich in den verschiedenen Versionen der Anwendung. An diesem Unterschied ist ersichtlich, dass Sie zwei verschiedene Speicher-Back-Ends verwenden.

## <a name="finalize-the-rollout"></a>Abschließen des Rollouts

Nachdem Sie das Canary-Release erfolgreich getestet haben, aktualisieren Sie den virtuellen Dienst von `voting-app`, um den gesamten Datenverkehr an Version `2.0` der Komponente `voting-app` zu leiten. Alle Benutzer sehen dann Version `2.0` der Anwendung, und zwar unabhängig davon, ob das Featureflag festgelegt ist oder nicht:

![Diagramm, das veranschaulicht, dass Version 2.0 der Anwendung unabhängig davon für Benutzer angezeigt wird, ob das Featureflag festgelegt wurde oder nicht](media/servicemesh/istio/scenario-routing-components-04.png)

Aktualisieren Sie alle Zielregeln, um diejenigen Versionen der Komponenten zu entfernen, die nicht mehr aktiv sein sollen. Aktualisieren Sie anschließend alle virtuellen Dienste, damit nicht mehr auf diese Versionen verwiesen wird.

Da es nun keinen Datenverkehr mehr zu den älteren Versionen der Komponenten gibt, können Sie jetzt alle Bereitstellungen für diese Komponenten sicher löschen.

![Die Komponenten der AKS-Abstimmungs-App und das Routing.](media/servicemesh/istio/scenario-routing-components-05.png)

Sie haben nun erfolgreich das Rollout einer neuen Version der AKS-Abstimmungs-App durchgeführt.

## <a name="clean-up"></a>Bereinigung 

Sie können die AKS-Abstimmungs-App, die wir in diesem Szenario verwendet haben, aus Ihrem AKS-Cluster entfernen, indem Sie den Namespace `voting` wie folgt löschen:

```console
kubectl delete namespace voting
```

Die folgende Beispielausgabe zeigt, dass alle Komponenten der AKS-Abstimmungs-App aus Ihrem AKS-Cluster entfernt wurden.

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>Nächste Schritte

Mithilfe der [Istio-Beispielanwendung „Bookinfo“][istio-bookinfo-example] können Sie sich weitere Szenarios ansehen.

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.mesh.v1alpha1/#AuthenticationPolicy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./servicemesh-istio-install.md
