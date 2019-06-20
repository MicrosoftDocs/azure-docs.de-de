---
title: Erstellen eines HTTP-Eingangscontrollers mit einer statischen IP-Adresse in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie einen NGINX-Eingangscontroller mit einer statischen öffentlichen IP-Adresse in einem AKS-Cluster konfigurieren.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: iainfou
ms.openlocfilehash: 94822c37d6f95bacd1aef36a72176c65c350383f
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431015"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Erstellen eines Eingangscontrollers mit einer statischen öffentlichen IP-Adresse in Azure Kubernetes Service (AKS)

Ein Eingangscontroller ist eine Softwarekomponente, die einen Reverseproxy, konfigurierbare Datenverkehrsweiterleitung und TLS-Terminierung für Kubernetes-Dienste bereitstellt. Mithilfe von Ressourcen für eingehende Kubernetes-Daten werden Eingangsregeln und Routen für einzelne Kubernetes-Dienste konfiguriert. Durch die Verwendung von einem Eingangscontroller und Eingangsregeln kann eine einzelne IP-Adresse zum Weiterleiten von Datenverkehr an mehrere Dienste in einem Kubernetes-Cluster verwendet werden.

Dieser Artikel beschreibt, wie Sie den [NGINX-Eingangscontroller][nginx-ingress] in einem Azure Kubernetes Service-Cluster (AKS) bereitstellen. Der Eingangscontroller wird mit einer statischen öffentlichen IP-Adresse konfiguriert. Das Projekt [cert-manager][cert-manager] wird verwendet, um automatisch [Let‘s Encrypt][lets-encrypt]-Zertifikate zu generieren und zu konfigurieren. Schließlich werden zwei Anwendungen im AKS-Cluster ausgeführt, die jeweils über eine einzelne IP-Adresse zugänglich sind.

Weitere Funktionen:

- [Erstellen eines einfachen Eingangscontrollers mit Verbindung mit einem externen Netzwerk][aks-ingress-basic]
- [Aktivieren des Add-Ons für das HTTP-Anwendungsrouting][aks-http-app-routing]
- [Erstellen eines Eingangscontrollers, der Ihre eigenen TLS-Zertifikate verwendet][aks-ingress-own-tls]
- [Erstellen eines Eingangscontrollers, der mit Let's Encrypt automatisch TLS-Zertifikate mit einer dynamischen öffentlichen IP-Adresse generiert][aks-ingress-tls]

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie noch einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

In diesem Artikel wird Helm verwendet, um den NGINX-Eingangscontroller, cert-manager und eine Beispiel-Web-App zu installieren. Helm muss im AKS-Cluster initialisiert sein und ein Dienstkonto für Tiller verwenden. Stellen Sie sicher, dass Sie das neueste Release von Helm verwenden. Anweisungen zum Upgrade finden Sie in der [Helm-Installationsdokumentation][helm-install]. Weitere Informationen zum Konfigurieren und Verwenden von Helm finden Sie unter [Installieren von Anwendungen mit Helm in Azure Kubernetes Service (AKS)][use-helm].

Für den Artikel wird außerdem mindestens Version 2.0.64 der Azure-Befehlszeilenschnittstelle benötigt. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0][azure-cli-install] Informationen dazu.

## <a name="create-an-ingress-controller"></a>Erstellen eines Eingangscontrollers

Standardmäßig wird ein NGINX-Eingangscontroller mit Zuweisung einer neuen öffentlichen IP-Adresse erstellt. Diese öffentliche IP-Adresse ist nur für die Lebensdauer des Eingangscontrollers statisch und geht verloren, wenn der Controller gelöscht oder neu erstellt wird. Die Bereitstellung einer vorhandenen, statischen öffentlichen IP-Adresse für den NGINX-Eingangscontroller ist eine häufige Konfigurationsanforderung. Die statische öffentliche IP-Adresse wird beibehalten, wenn der Eingangscontroller gelöscht wird. Mit diesem Ansatz können Sie vorhandene DNS-Einträge und -Netzwerkkonfigurationen auf konsistente Weise über den gesamten Lebenszyklus Ihrer Anwendungen hinweg verwenden.

Wenn Sie eine statische öffentliche IP-Adresse erstellen müssen, rufen Sie zuerst den Ressourcengruppennamen des AKS-Clusters mit dem Befehl [az aks show][az-aks-show] ab:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Als Nächstes erstellen Sie eine öffentliche IP-Adresse mit der *statischen* Zuordnungsmethode unter Verwendung des Befehls [az network public-ip create][az-network-public-ip-create]. Im folgenden Beispiel wird in der AKS-Clusterressourcengruppe, die Sie im vorherigen Schritt abgerufen haben, eine öffentliche IP-Adresse mit dem Namen *myAKSPublicIP* erstellt:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o tsv
```

Nun stellen Sie das *nginx-ingress*-Diagramm mit Helm bereit. Fügen Sie den Parameter `--set controller.service.loadBalancerIP` hinzu, und geben Sie Ihre eigene öffentliche IP-Adresse an, die Sie im vorherigen Schritt erstellt haben. Für zusätzliche Redundanz werden zwei Replikate der NGINX-Eingangscontroller mit dem Parameter `--set controller.replicaCount` bereitgestellt. Um vollständig von der Ausführung von Replikaten des Eingangscontrollers zu profitieren, stellen Sie sicher, dass sich mehr als ein Knoten im AKS-Cluster befindet.

Der Eingangscontroller muss ebenfalls auf einem Linux-Knoten geplant werden. Windows Server-Knoten (derzeit in der Vorschau in AKS) dürfen nicht auf dem Eingangscontroller ausgeführt werden. Ein Knotenselektor wird mit dem Parameter `--set nodeSelector` angegeben, um den Kubernetes-Scheduler anzuweisen, den NGINX-Eingangscontroller auf einem Linux-basierten Knoten auszuführen.

> [!TIP]
> Im folgenden Beispiel wird der Kubernetes-Namespace *ingress-basic* für die Eingangsressourcen erstellt. Geben Sie ggf. einen Namespace für Ihre eigene Umgebung an. Wenn in Ihrem AKS-Cluster die RBAC nicht aktiviert ist, fügen Sie den Helm-Befehlen `--set rbac.create=false` hinzu.

> [!TIP]
> Wenn Sie die [Clientquell-IP-Beibehaltung][client-source-ip] für Anforderungen an Container in Ihrem Cluster aktivieren möchten, fügen Sie dem Helm-Installationsbefehl `--set controller.service.externalTrafficPolicy=Local` hinzu. Die Clientquell-IP wird in der Anforderungskopfzeile unter *X-Forwarded-For* gespeichert. Bei der Verwendung eines Eingangscontrollers mit aktivierter Clientquell-IP-Beibehaltung funktioniert SSL-Pass-Through nicht.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
```

Wird der Kubernetes-Lastenausgleichsdienst für den NGINX-Eingangscontroller erstellt, wird Ihre statische IP-Adresse zugewiesen, wie in der folgenden Beispielausgabe gezeigt:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
dinky-panda-nginx-ingress-controller        LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
dinky-panda-nginx-ingress-default-backend   ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Da noch keine Eingangsregeln erstellt wurden, wird die Standard-404-Seite des NGINX-Eingangscontrollers angezeigt, wenn Sie die öffentliche IP-Adresse im Browser öffnen. Eingangsregeln werden in den folgenden Schritten konfiguriert.

## <a name="configure-a-dns-name"></a>Konfigurieren des DNS-Namens

Damit die HTTPS-Zertifikate ordnungsgemäß ausgeführt werden, konfigurieren Sie einen vollqualifizierten Domänennamen (FQDN) für die IP-Adresse des Eingangscontrollers. Aktualisieren Sie das folgende Skript mit der IP-Adresse des Eingangscontrollers und einem eindeutigen Namen, den Sie im FQDN verwenden möchten:

```azurecli-interactive
#!/bin/bash

# Public IP address of your ingress controller
IP="40.121.63.72"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Der Eingangscontroller ist jetzt über den vollqualifizierten Domänennamen zugänglich.

## <a name="install-cert-manager"></a>Installieren von cert-manager

Der NGINX-Eingangscontroller unterstützt TLS-Terminierung. Es gibt verschiedene Möglichkeiten zum Abrufen und Konfigurieren von Zertifikaten für HTTPS. Dieser Artikel veranschaulicht die Verwendung von [cert-manager][cert-manager], über den die automatische Zertifikatsgenerierung und Verwaltungsfunktionalität mit [Let‘s Encrypt][lets-encrypt] bereitgestellt werden.

> [!NOTE]
> In diesem Artikel wird die `staging`-Umgebung für Let‘s Encrypt verwendet. Verwenden Sie in Produktionsbereitstellungen `letsencrypt-prod` und `https://acme-v02.api.letsencrypt.org/directory` in den Ressourcendefinitionen und bei der Installation des Helm-Diagramms.

Verwenden Sie zum Installieren des cert-manager-Controllers in einem RBAC-fähigen Cluster den folgenden `helm install`-Befehl:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  --name cert-manager \
  --namespace cert-manager \
  --version v0.8.0 \
  jetstack/cert-manager
```

Weitere Informationen zur cert-manager-Konfiguration finden Sie unter [cert-manager-Projekt][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Erstellen eines CA-Clusterausstellers

Bevor Zertifikate ausgestellt werden können, benötigt cert-manager eine [Aussteller][cert-manager-issuer]- oder [Clusteraussteller][cert-manager-cluster-issuer]-Ressource. Die Kubernetes-Ressourcen sind funktionell identisch, aber `Issuer` arbeitet in einem einzelnen Namespace, `ClusterIssuer` hingegen in allen Namespaces. Weitere Informationen finden Sie in der Dokumentation zum [cert-manager-Aussteller][cert-manager-issuer].

Erstellen Sie einen Clusteraussteller, wie z.B. `cluster-issuer.yaml`, mithilfe des folgenden Beispielmanifests. Aktualisieren Sie die E-Mail-Adresse mit einer gültigen Adresse aus Ihrer Organisation:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
  namespace: ingress-basic
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Um den Aussteller zu erstellen, verwenden Sie den Befehl `kubectl apply -f cluster-issuer.yaml`.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Ausführen von Demoanwendungen

Ein Eingangscontroller und eine Zertifikatsverwaltungslösung wurden konfiguriert. Nun führen wir zwei Demoanwendungen im AKS-Cluster-aus. In diesem Beispiel wird Helm verwendet, um mehrere Instanzen einer einfachen Hallo-Welt-Anwendung auszuführen.

Bevor Sie Helm-Beispieldiagramme installieren können, fügen Sie das Azure-Beispielrepository wie folgt Ihrer Helm-Umgebung hinzu:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Erstellen Sie die erste Demoanwendung aus einem Helm-Diagramm mit dem folgenden Befehl:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Installieren Sie nun eine zweite Instanz der Demoanwendung. Geben Sie für die zweite Instanz einen neuen Titel an, sodass sich die beiden Anwendungen visuell unterscheiden. Sie geben auch einen eindeutigen Dienstnamen an:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Erstellen einer Eingangsroute

Beide Anwendungen werden nun in Ihrem Kubernetes-Cluster ausgeführt, wurden jedoch mit einem Dienst vom Typ `ClusterIP` konfiguriert. Daher kann nicht über das Internet auf die Anwendungen zugegriffen werden. Um sie öffentlich verfügbar zu machen, erstellen Sie eine Kubernetes-Eingangsressource. Die Eingangsressource konfiguriert Regeln, die den Datenverkehr an eine der beiden Anwendungen weiterleiten.

Im folgenden Beispiel wird der Datenverkehr an die Adresse `https://demo-aks-ingress.eastus.cloudapp.azure.com/` an den Dienst mit dem Namen `aks-helloworld` weitergeleitet. Datenverkehr an die Adresse `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` wird an den Dienst `ingress-demo` weitergeleitet. Aktualisieren Sie *hosts* und *host* auf den DNS-Namen, den Sie in einem vorherigen Schritt erstellt haben.

Erstellen Sie eine Datei mit dem Namen `hello-world-ingress.yaml`, und fügen Sie den folgenden YAML-Beispielcode ein.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Erstellen Sie die Eingangsressource mit dem Befehl `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Erstellen eines Zertifikatsobjekts

Als Nächstes muss eine Zertifikatressource erstellt werden. Die Zertifikatressource definiert das gewünschte X.509-Zertifikat. Weitere Informationen finden Sie unter [cert-manager-Zertifikate][cert-manager-certificates].

„Cert-manager“ hat wahrscheinlich automatisch ein Zertifikatsobjekt für Sie erstellt, indem „ingress-shim“ verwendet wurde, das seit v0.2.2 automatisch mit „Cert-manager“ bereitgestellt wird. Weitere Informationen finden Sie in der [Dokumentation zu „ingress-shim “][ingress-shim].

Verwenden Sie den Befehl `kubectl describe certificate tls-secret --namespace ingress-basic`, um sich zu vergewissern, dass das Zertifikat erfolgreich erstellt wurde.

Wenn das Zertifikat ausgestellt wurde, wird eine Ausgabe ähnlich der folgenden angezeigt:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Wenn Sie eine zusätzliche Zertifikatsressource erstellen müssen, können Sie dies mit dem folgenden Beispielmanifest tun. Aktualisieren Sie *dnsNames* und *domains* auf den DNS-Namen, den Sie in einem vorherigen Schritt erstellt haben. Wenn Sie einen rein internen Eingangscontroller verwenden, geben Sie den internen DNS-Namen für Ihren Dienst an.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Verwenden Sie zum Erstellen der Zertifikatsressource den Befehl `kubectl apply -f certificates.yaml`.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Testen der Konfiguration für eingehende Daten

Öffnen Sie einen Webbrowser für den FQDN des Kubernetes-Eingangscontrollers, z.B. *https://demo-aks-ingress.eastus.cloudapp.azure.com* .

Da diese Beispiele `letsencrypt-staging` verwenden, ist das ausgestellte SSL-Zertifikat für den Browser nicht vertrauenswürdig. Akzeptieren Sie die Eingabeaufforderung der Warnung, um Ihre Anwendung zu öffnen. Die Zertifikatinformationen zeigen, dass dieses *Fake LE Intermediate X1*-Zertifikat von Let‘s Encrypt ausgestellt wurde. Dieses gefälschte Zertifikat gibt an, dass `cert-manager` die Anforderung korrekt verarbeitet und ein Zertifikat vom Anbieter erhalten hat:

![Let's Encrypt-Bereitstellungszertifikat](media/ingress/staging-certificate.png)

Wenn Sie Let‘s Encrypt so ändern, dass `prod` anstelle von `staging` verwendet wird, wird ein von Let‘s Encrypt ausgestelltes vertrauenswürdiges verwendet, wie im folgenden Beispiel gezeigt:

![Let‘s Encrypt-Zertifikat](media/ingress/certificate.png)

Die Demoanwendung wird im Webbrowser angezeigt:

![Anwendungsbeispiel 1](media/ingress/app-one.png)

Fügen Sie nun den Pfad */hello-world-two* dem FQDN hinzu, wie z.B. *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two* . Die zweite Demoanwendung mit dem benutzerdefinierten Titel wird angezeigt:

![Anwendungsbeispiel 2](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Artikel wird Helm verwendet, um die Eingangskomponenten, die Zertifikate und die Beispiel-Apps zu installieren. Wenn Sie ein Helm-Diagramm bereitstellen, werden eine Reihe von Kubernetes-Ressourcen erstellt. Diese Ressourcen enthalten Pods, Bereitstellungen und Dienste. Sie können zur Bereinigung der Ressourcen entweder den gesamten Beispielnamespace oder die einzelnen Ressourcen löschen.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Löschen des Beispielnamespace und aller Ressourcen

Verwenden Sie den `kubectl delete`-Befehl mit dem Namespacenamen, um den gesamten Beispielnamespace zu löschen. Alle Ressourcen im Namespace werden gelöscht.

```console
kubectl delete namespace ingress-basic
```

Entfernen Sie als Nächstes das Helm-Repository für die „Hallo Welt“-AKS-App:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Löschen einzelner Ressourcen

Mehr Kontrolle bietet eine andere Vorgehensweise, bei der Sie einzelne Ressourcen löschen. Entfernen Sie zuerst die Zertifikatressourcen:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Listen Sie nun mit dem Befehl `helm list` die Helm-Versionen auf. Suchen Sie nach Diagrammen mit den Namen *nginx-ingress*, *cert-manager* und *aks-helloworld*, wie in der folgenden Beispielausgabe gezeigt:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
waxen-hamster           1           Wed Mar  6 23:16:00 2019    DEPLOYED    nginx-ingress-1.3.1   0.22.0        kube-system
alliterating-peacock    1           Wed Mar  6 23:17:37 2019    DEPLOYED    cert-manager-v0.6.6     v0.6.2      kube-system
mollified-armadillo     1           Wed Mar  6 23:26:04 2019    DEPLOYED    aks-helloworld-0.1.0                default
wondering-clam          1           Wed Mar  6 23:26:07 2019    DEPLOYED    aks-helloworld-0.1.0                default
```

Löschen Sie die Versionen mit dem Befehl `helm delete`. Im folgenden Beispiel werden die NGINX-Eingangsbereitstellung, der Zertifikat-Manager und die beiden Hallo-Welt-AKS-Beispiel-Apps gelöscht.

```
$ helm delete waxen-hamster alliterating-peacock mollified-armadillo wondering-clam

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Entfernen Sie als Nächstes das Helm-Repository für die Hallo-Welt-AKS-App:

```console
helm repo remove azure-samples
```

Entfernen Sie die Eingangsroute, die Datenverkehr an die Beispiel-Apps weitergeleitet hat:

```console
kubectl delete -f hello-world-ingress.yaml
```

Löschen Sie den Namespace selbst. Verwenden Sie dazu den `kubectl delete`-Befehl mit dem Namespacenamen:

```console
kubectl delete namespace ingress-basic
```

Entfernen Sie abschließend die statische öffentliche IP-Adresse, die für den Eingangscontroller erstellt wurde. Stellen Sie den *MC_* -Namen Ihrer Clusterressourcengruppe bereit, den Sie im ersten Schritt dieses Artikels abgerufen haben, z. B. *MC_meineRessourcenGruppe_meinAKSCluster_eastus*:

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden einige externe Komponenten in AKS berücksichtigt. Weitere Informationen zu diesen Komponenten finden Sie auf den folgenden Projektseiten:

- [Helm CLI][helm-cli]
- [NGINX-Eingangscontroller][nginx-ingress]
- [cert-manager][cert-manager]

Weitere Funktionen:

- [Erstellen eines einfachen Eingangscontrollers mit Verbindung mit einem externen Netzwerk][aks-ingress-basic]
- [Aktivieren des Add-Ons für das HTTP-Anwendungsrouting][aks-http-app-routing]
- [Erstellen eines Eingangscontrollers, der ein internes, privates Netzwerk und eine IP-Adresse verwendet][aks-ingress-internal]
- [Erstellen eines Eingangscontrollers, der Ihre eigenen TLS-Zertifikate verwendet][aks-ingress-own-tls]
- [Erstellen eines Eingangscontrollers mit einer dynamischen öffentlichen IP-Adresse und Konfigurieren von Let's Encrypt für das automatische Generieren von TLS-Zertifikaten][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
