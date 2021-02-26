---
title: Verwenden von TLS-Zertifikaten für den Eingang
titleSuffix: Azure Kubernetes Service
description: Erfahren Sie, wie Sie einen NGINX-Eingangscontroller, der Ihre eigenen Zertifikate verwendet, in einem AKS-Cluster (Azure Kubernetes Service) konfigurieren.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: ee196bd749ad5821a1855d4549b22698c724b3f5
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104925"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Erstellen eines HTTPS-Eingangscontrollers und Verwenden Ihrer eigenen TLS-Zertifikate in Azure Kubernetes Service (AKS)

Ein Eingangscontroller ist eine Softwarekomponente, die einen Reverseproxy, konfigurierbare Datenverkehrsweiterleitung und TLS-Terminierung für Kubernetes-Dienste bereitstellt. Mithilfe von Ressourcen für eingehende Kubernetes-Daten werden Eingangsregeln und Routen für einzelne Kubernetes-Dienste konfiguriert. Durch die Verwendung von einem Eingangscontroller und Eingangsregeln kann eine einzelne IP-Adresse zum Weiterleiten von Datenverkehr an mehrere Dienste in einem Kubernetes-Cluster verwendet werden.

Dieser Artikel beschreibt, wie Sie den [NGINX-Eingangscontroller][nginx-ingress] in einem AKS-Cluster (Azure Kubernetes Service) bereitstellen. Sie generieren Ihre eigenen Zertifikate und erstellen ein Kubernetes-Geheimnis zur Verwendung mit der Eingangsroute. Schließlich werden zwei Anwendungen im AKS-Cluster ausgeführt, die jeweils über eine einzelne IP-Adresse zugänglich sind.

Sie können außerdem:

- [Erstellen eines einfachen Eingangscontrollers mit Konnektivität mit einem externen Netzwerk][aks-ingress-basic]
- [Aktivieren des Add-Ons für das HTTP-Anwendungsrouting][aks-http-app-routing]
- [Erstellen eines Eingangscontrollers, der ein internes, privates Netzwerk und eine IP-Adresse verwendet][aks-ingress-internal]
- Erstellen eines Eingangscontrollers, der Let's Encrypt für das automatische Generieren von TLS-Zertifikaten [mit einer dynamischen öffentlichen IP-Adresse][aks-ingress-tls] oder [mit einer statischen öffentlichen IP-Adresse][aks-ingress-static-tls] verwendet

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Artikel wird [Helm 3][helm] für die Installation des NGINX-Eingangscontrollers verwendet. Stellen Sie sicher, dass Sie die neueste Version von Helm verwenden und auf das Helm-Repository *ingress-nginx* zugreifen können. Eine Upgradeanleitung finden Sie in der [Helm-Installationsdokumentation][helm-install]. Weitere Informationen zum Konfigurieren und Verwenden von Helm finden Sie unter [Installieren von Anwendungen mit Helm in Azure Kubernetes Service (AKS)][use-helm].

Für den Artikel wird außerdem mindestens Version 2.0.64 der Azure-Befehlszeilenschnittstelle benötigt. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Erstellen eines Eingangscontrollers

Verwenden Sie zum Erstellen des Eingangscontrollers `Helm` zum Installieren von *nginx-ingress*. Für zusätzliche Redundanz werden zwei Replikate der NGINX-Eingangscontroller mit dem Parameter `--set controller.replicaCount` bereitgestellt. Um vollständig von der Ausführung von Replikaten des Eingangscontrollers zu profitieren, stellen Sie sicher, dass sich mehr als ein Knoten im AKS-Cluster befindet.

Der Eingangscontroller muss ebenfalls auf einem Linux-Knoten geplant werden. Windows Server-Knoten dürfen nicht auf dem Eingangscontroller ausgeführt werden. Ein Knotenselektor wird mit dem Parameter `--set nodeSelector` angegeben, um den Kubernetes-Scheduler anzuweisen, den NGINX-Eingangscontroller auf einem Linux-basierten Knoten auszuführen.

> [!TIP]
> Im folgenden Beispiel wird der Kubernetes-Namespace *ingress-basic* für die Eingangsressourcen erstellt. Geben Sie ggf. einen Namespace für Ihre eigene Umgebung an. Wenn in Ihrem AKS-Cluster die rollenbasierte Zugriffssteuerung (RBAC) von Kubernetes nicht aktiviert ist, fügen Sie den Helm-Befehlen `--set rbac.create=false` hinzu.

> [!TIP]
> Wenn Sie die [Beibehaltung der Clientquell-IP][client-source-ip] für Anforderungen an Container in Ihrem Cluster aktivieren möchten, fügen Sie dem Helm-Installationsbefehl `--set controller.service.externalTrafficPolicy=Local` hinzu. Die Clientquell-IP wird in der Anforderungskopfzeile unter *X-Forwarded-For* gespeichert. Bei der Verwendung eines Eingangscontrollers mit aktivierter Clientquell-IP-Beibehaltung funktioniert TLS-Pass-Through nicht.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Während der Installation wird eine öffentliche Azure-IP-Adresse für den Eingangscontroller erstellt. Diese öffentliche IP-Adresse ist für die Lebensdauer des Eingangscontrollers statisch. Wenn Sie den Eingangscontroller löschen, geht die Zuweisung der öffentlichen IP-Adresse verloren. Wenn Sie dann einen weiteren Eingangscontroller erstellen, wird eine neue öffentliche IP-Adresse zugewiesen. Wenn Sie die Verwendung der öffentlichen IP-Adresse beibehalten möchten, können Sie stattdessen [einen Eingangscontroller mit einer statischen öffentlichen IP-Adresse erstellen][aks-ingress-static-tls].

Sie rufen die öffentliche IP-Adresse mit dem Befehl `kubectl get service` ab.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Es dauert möglicherweise einige Minuten, bis die IP-Adresse dem Dienst zugewiesen wird.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Notieren Sie sich diese öffentliche IP-Adresse, da sie im letzten Schritt verwendet wird, um die Bereitstellung zu testen.

Es wurden noch keine Eingangsregeln erstellt. Wenn Sie zu der öffentlichen IP-Adresse navigieren, wird die Standard-404-Seite des NGINX-Eingangscontrollers angezeigt.

## <a name="generate-tls-certificates"></a>Generieren von TLS-Zertifikaten

In diesem Artikel generieren wir ein selbstsigniertes Zertifikat mit `openssl`. Für die Produktion sollten Sie ein vertrauenswürdiges, signiertes Zertifikat über einen Anbieter oder Ihre eigene Zertifizierungsstelle anfordern. Im nächsten Schritt generieren Sie ein Kubernetes-*Geheimnis* unter Verwendung des TLS-Zertifikats und des privaten Schlüssels, der von OpenSSL generiert wurde.

Im folgenden Beispiel wird ein RSA X509-Zertifikat (2048 Bit) namens *aks-ingress-tls.crt* generiert, das für 365 Tage gültig ist. Die private Schlüsseldatei heißt *aks-ingress-tls.key*. Für ein Kubernetes-TLS-Geheimnis sind beide Dateien erforderlich.

Dieser Artikel verwendet den allgemeinen Antragstellernamen *demo.azure.com*, der nicht geändert werden muss. Geben Sie für die Produktion Ihre eigenen Organisationswerte für den `-subj`-Parameter an:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Erstellen eines Kubernetes-Geheimnisses für das TLS-Zertifikat

Damit Kubernetes das TLS-Zertifikat und den privaten Schlüssel für den Eingangscontroller verwenden kann, erstellen und verwenden Sie ein Geheimnis. Das Geheimnis wird einmal definiert und verwendet das Zertifikat und die Schlüsseldatei, das bzw. die im vorherigen Schritt erstellt wurde. Auf dieses Geheimnis verweisen Sie, wenn Sie Eingangsrouten definieren.

Im folgenden Beispiel wird ein Geheimnis namens *aks-ingress-tls* erstellt:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Ausführen von Demoanwendungen

Ein Eingangscontroller und ein Geheimnis für Ihr Zertifikat wurden konfiguriert. Nun führen wir zwei Demoanwendungen im AKS-Cluster-aus. In diesem Beispiel wird Helm verwendet, um mehrere Instanzen einer einfachen Hallo-Welt-Anwendung auszuführen.

Um den Eingangscontroller in Aktion zu sehen, führen Sie zwei Demoanwendungen im AKS-Cluster aus. In diesem Beispiel verwenden Sie `kubectl apply`, um mehrere Instanzen einer einfachen *Hallo Welt*-Anwendung auszuführen.

Erstellen Sie die Datei *aks-helloworld.yaml*, und kopieren Sie den folgenden YAML-Beispielcode hinein:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

Erstellen Sie die Datei *ingress-demo.yaml*, und kopieren Sie den folgenden YAML-Beispielcode hinein:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

Führen Sie die beiden Demoanwendungen mit `kubectl apply` aus:

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Erstellen einer Eingangsroute

Beide Anwendungen werden nun in Ihrem Kubernetes-Cluster ausgeführt, wurden jedoch mit einem Dienst vom Typ `ClusterIP` konfiguriert. Daher kann nicht über das Internet auf die Anwendungen zugegriffen werden. Um sie öffentlich verfügbar zu machen, erstellen Sie eine Kubernetes-Eingangsressource. Die Eingangsressource konfiguriert Regeln, die den Datenverkehr an eine der beiden Anwendungen weiterleiten.

Im folgenden Beispiel wird der Datenverkehr an die Adresse `https://demo.azure.com/` an den Dienst mit dem Namen `aks-helloworld` weitergeleitet. Datenverkehr an die Adresse `https://demo.azure.com/hello-world-two` wird an den Dienst `ingress-demo` weitergeleitet. Für diesen Artikel müssen Sie diese Demohostnamen nicht ändern. In der Produktion geben Sie die Namen an, die im Rahmen der Zertifikatanforderung und des Generierungsvorgangs angegeben wurden.

> [!TIP]
> Wenn der beim Zertifikatanforderungsvorgang angegebene Hostname (CN-Name) nicht dem Host entspricht, der in Ihrer Eingangsroute definiert wurde, zeigt der Eingangscontroller die Warnung *Kubernetes Ingress Controller Fake Certificate* an. Stellen Sie sicher, dass der Name Ihres Zertifikats mit dem des Eingangsroutenhosts übereinstimmt.

Im Abschnitt *tls* wird die Eingangsroute dazu aufgefordert, das Geheimnis namens *aks-ingress-tls* für den Host *demo.azure.com* zu verwenden. Geben Sie in Ihrer Produktionsumgebung Ihre eigene Hostadresse an.

Erstellen Sie eine Datei mit dem Namen `hello-world-ingress.yaml`, und fügen Sie den folgenden YAML-Beispielcode ein.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
```

Erstellen Sie die Eingangsressource mit dem Befehl `kubectl apply -f hello-world-ingress.yaml`.

```console
kubectl apply -f hello-world-ingress.yaml
```

Die Beispielausgabe zeigt, wie die Eingangsressource erstellt wird.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testen der Konfiguration für eingehende Daten

Zum Testen der Zertifikate mit unserem Fakehost *demo.azure.com* verwenden Sie `curl` und geben den Parameter *--resolve* an. Mit diesem Parameter können Sie den Namen *demo.azure.com* der öffentlichen IP-Adresse des Eingangscontrollers zuordnen. Geben Sie die öffentliche IP-Adresse Ihres eigenen Eingangscontrollers an, wie im folgenden Beispiel gezeigt:

```
curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com
```

Die Adresse wurde ohne zusätzlichen Pfad angegeben, sodass der Eingangscontroller standardmäßig die Route */* verwendet. Die erste Demoanwendung wird zurückgegeben, wie in der folgenden verkürzten Beispielausgabe gezeigt:

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Durch den Parameter *-v* in unserem `curl`-Befehl werden ausführliche Informationen ausgegeben, einschließlich des erhaltenen TLS-Zertifikats. Während der CURL-Ausgabe können Sie überprüfen, ob Ihr eigenes TLS-Zertifikat verwendet wurde. Durch den Parameter *-k* wird die Seite auch dann weiterhin geladen, wenn wir ein selbstsigniertes Zertifikat verwenden. Das folgende Beispiel zeigt, dass das Zertifikat *issuer: CN=demo.azure.com; O=aks-ingress-tls* verwendet wurde:

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

Fügen Sie nun den Pfad */hello-world-two* der Adresse hinzu, z.B`https://demo.azure.com/hello-world-two`. Die zweite Demoanwendung wird mit dem benutzerdefinierten Titel zurückgegeben, wie in der folgenden verkürzten Beispielausgabe gezeigt:

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Artikel wird Helm verwendet, um die Eingangskomponenten und die Beispiel-Apps zu installieren. Wenn Sie ein Helm-Diagramm bereitstellen, werden eine Reihe von Kubernetes-Ressourcen erstellt. Zu diesen Ressourcen gehören Pods, Bereitstellungen und Dienste. Sie können zur Bereinigung der Ressourcen entweder den gesamten Beispielnamespace oder die einzelnen Ressourcen löschen.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Löschen des Beispielnamespace und aller Ressourcen

Verwenden Sie den `kubectl delete`-Befehl mit dem Namespacenamen, um den gesamten Beispielnamespace zu löschen. Alle Ressourcen im Namespace werden gelöscht.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Löschen einzelner Ressourcen

Mehr Kontrolle bietet eine andere Vorgehensweise, bei der Sie einzelne Ressourcen löschen. Listen Sie mit dem Befehl `helm list` die Helm-Releases auf. 

```console
helm list --namespace ingress-basic
```

Suchen Sie nach einer Chart mit dem Namen *nginx-ingress*, wie in der folgenden Beispielausgabe gezeigt:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1 
```

Deinstallieren Sie die Versionen mit dem Befehl `helm uninstall`. 

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

Im folgenden Beispiel wird die NGINX-Eingangsbereitstellung deinstalliert.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Entfernen Sie als nächstes die beiden Beispielanwendungen:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Entfernen Sie die Eingangsroute, die Datenverkehr an die Beispiel-Apps weitergeleitet hat:

```console
kubectl delete -f hello-world-ingress.yaml
```

Löschen Sie das Zertifikatgeheimnis:

```console
kubectl delete secret aks-ingress-tls --namespace ingress-basic
```

Abschließend können Sie den Namespace selbst löschen. Verwenden Sie dazu den `kubectl delete`-Befehl mit dem Namespacenamen:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden einige externe Komponenten in AKS berücksichtigt. Weitere Informationen zu diesen Komponenten finden Sie auf den folgenden Projektseiten:

- [Helm-Befehlszeilenschnittstelle][helm-cli]
- [NGINX-Eingangscontroller][nginx-ingress]

Sie können außerdem:

- [Erstellen eines einfachen Eingangscontrollers mit Konnektivität mit einem externen Netzwerk][aks-ingress-basic]
- [Aktivieren des Add-Ons für das HTTP-Anwendungsrouting][aks-http-app-routing]
- [Erstellen eines Eingangscontrollers, der ein internes, privates Netzwerk und eine IP-Adresse verwendet][aks-ingress-internal]
- Erstellen eines Eingangscontrollers, der Let's Encrypt für das automatische Generieren von TLS-Zertifikaten [mit einer dynamischen öffentlichen IP-Adresse][aks-ingress-tls] oder [mit einer statischen öffentlichen IP-Adresse][aks-ingress-static-tls] verwendet

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
