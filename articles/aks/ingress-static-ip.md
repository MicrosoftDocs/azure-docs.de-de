---
title: Verwenden von Eingangscontrollern mit statischen IP-Adressen
titleSuffix: Azure Kubernetes Service
description: Erfahren Sie, wie Sie einen NGINX-Eingangscontroller mit einer statischen öffentlichen IP-Adresse in einem AKS-Cluster konfigurieren.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: eb58bbe127349aaebed3b1eb00281cf2938c1933
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681583"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Erstellen eines Eingangscontrollers mit einer statischen öffentlichen IP-Adresse in Azure Kubernetes Service (AKS)

Ein Eingangscontroller ist eine Softwarekomponente, die einen Reverseproxy, konfigurierbare Datenverkehrsweiterleitung und TLS-Terminierung für Kubernetes-Dienste bereitstellt. Mithilfe von Ressourcen für eingehende Kubernetes-Daten werden Eingangsregeln und Routen für einzelne Kubernetes-Dienste konfiguriert. Durch die Verwendung von einem Eingangscontroller und Eingangsregeln kann eine einzelne IP-Adresse zum Weiterleiten von Datenverkehr an mehrere Dienste in einem Kubernetes-Cluster verwendet werden.

Dieser Artikel beschreibt, wie Sie den [NGINX-Eingangscontroller][nginx-ingress] in einem AKS-Cluster (Azure Kubernetes Service) bereitstellen. Der Eingangscontroller wird mit einer statischen öffentlichen IP-Adresse konfiguriert. Das Projekt [cert-manager][cert-manager] wird verwendet, um automatisch [Let‘s Encrypt][lets-encrypt]-Zertifikate zu generieren und zu konfigurieren. Schließlich werden zwei Anwendungen im AKS-Cluster ausgeführt, die jeweils über eine einzelne IP-Adresse zugänglich sind.

Sie können außerdem:

- [Erstellen eines einfachen Eingangscontrollers mit Konnektivität mit einem externen Netzwerk][aks-ingress-basic]
- [Aktivieren des Add-Ons für das HTTP-Anwendungsrouting][aks-http-app-routing]
- [Erstellen eines Eingangscontrollers, der Ihre eigenen TLS-Zertifikate verwendet][aks-ingress-own-tls]
- [Erstellen eines Eingangscontrollers, der mit Let's Encrypt automatisch TLS-Zertifikate mit einer dynamischen öffentlichen IP-Adresse generiert][aks-ingress-tls]

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

In diesem Artikel wird [Helm 3][helm] für die Installation des NGINX-Eingangscontrollers und von cert-manager verwendet. Stellen Sie sicher, dass Sie die neueste Version von Helm verwenden und auf die Helm-Repositorys *ingress-nginx* und *jetstack* zugreifen können. Eine Upgradeanleitung finden Sie in der [Helm-Installationsdokumentation][helm-install]. Weitere Informationen zum Konfigurieren und Verwenden von Helm finden Sie unter [Installieren von Anwendungen mit Helm in Azure Kubernetes Service (AKS)][use-helm].

Für den Artikel wird außerdem mindestens Version 2.0.64 der Azure-Befehlszeilenschnittstelle benötigt. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Erstellen eines Eingangscontrollers

Standardmäßig wird ein NGINX-Eingangscontroller mit Zuweisung einer neuen öffentlichen IP-Adresse erstellt. Diese öffentliche IP-Adresse ist nur für die Lebensdauer des Eingangscontrollers statisch und geht verloren, wenn der Controller gelöscht oder neu erstellt wird. Die Bereitstellung einer vorhandenen, statischen öffentlichen IP-Adresse für den NGINX-Eingangscontroller ist eine häufige Konfigurationsanforderung. Die statische öffentliche IP-Adresse wird beibehalten, wenn der Eingangscontroller gelöscht wird. Mit diesem Ansatz können Sie vorhandene DNS-Einträge und -Netzwerkkonfigurationen auf konsistente Weise über den gesamten Lebenszyklus Ihrer Anwendungen hinweg verwenden.

Wenn Sie eine statische öffentliche IP-Adresse erstellen müssen, rufen Sie zuerst den Ressourcengruppennamen des AKS-Clusters mit dem Befehl [az aks show][az-aks-show] ab:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Als Nächstes erstellen Sie eine öffentliche IP-Adresse mit der *statischen* Zuordnungsmethode unter Verwendung des Befehls [az network public-ip create][az-network-public-ip-create]. Im folgenden Beispiel wird in der AKS-Clusterressourcengruppe, die Sie im vorherigen Schritt abgerufen haben, eine öffentliche IP-Adresse mit dem Namen *myAKSPublicIP* erstellt:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

> [!NOTE]
> Mit den obigen Befehlen wird eine IP-Adresse erstellt, die beim Löschen Ihres AKS-Clusters gelöscht wird. Alternativ können Sie eine IP-Adresse in einer anderen Ressourcengruppe erstellen, die separat von Ihrem AKS-Cluster verwaltet werden kann. Wenn Sie eine IP-Adresse in einer anderen Ressourcengruppe erstellen, muss sichergestellt werden, dass der vom AKS-Cluster verwendete Dienstprinzipal über delegierte Berechtigungen für die andere Ressourcengruppe verfügt (z. B. *Netzwerkmitwirkender*). Weitere Informationen finden Sie unter [Verwenden einer statischen öffentlichen IP-Adresse und einer DNS-Bezeichnung mit dem AKS-Lastenausgleich][aks-static-ip].

Nun stellen Sie das *nginx-ingress*-Diagramm mit Helm bereit. Für zusätzliche Redundanz werden zwei Replikate der NGINX-Eingangscontroller mit dem Parameter `--set controller.replicaCount` bereitgestellt. Um vollständig von der Ausführung von Replikaten des Eingangscontrollers zu profitieren, stellen Sie sicher, dass sich mehr als ein Knoten im AKS-Cluster befindet.

Sie müssen zwei zusätzliche Parameter an das Helm-Release übergeben, damit der Eingangscontroller sowohl die statische IP-Adresse des Lastenausgleichs, der dem Eingangscontrollerdienst zugeordnet werden soll, als auch die DNS-Namensbezeichnung kennt, die auf die öffentliche IP-Adressressource angewandt wird. Damit die HTTPS-Zertifikate ordnungsgemäß funktionieren, wird eine DNS-Namensbezeichnung verwendet, um einen vollqualifizierten Domänennamen (FQDN) für die IP-Adresse des Eingangscontrollers zu konfigurieren.

1. Fügen Sie den Parameter `--set controller.service.loadBalancerIP` hinzu. Geben Sie Ihre eigene öffentliche IP-Adresse an, die im vorherigen Schritt erstellt wurde.
1. Fügen Sie den Parameter `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` hinzu. Geben Sie eine DNS-Namensbezeichnung an, die auf die im vorherigen Schritt erstellte öffentliche IP-Adresse angewandt werden soll.

Der Eingangscontroller muss ebenfalls auf einem Linux-Knoten geplant werden. Windows Server-Knoten dürfen nicht auf dem Eingangscontroller ausgeführt werden. Ein Knotenselektor wird mit dem Parameter `--set nodeSelector` angegeben, um den Kubernetes-Scheduler anzuweisen, den NGINX-Eingangscontroller auf einem Linux-basierten Knoten auszuführen.

> [!TIP]
> Im folgenden Beispiel wird der Kubernetes-Namespace *ingress-basic* für die Eingangsressourcen erstellt. Geben Sie ggf. einen Namespace für Ihre eigene Umgebung an. Wenn in Ihrem AKS-Cluster die rollenbasierte Zugriffssteuerung (RBAC) von Kubernetes nicht aktiviert ist, fügen Sie den Helm-Befehlen `--set rbac.create=false` hinzu.

> [!TIP]
> Wenn Sie die [Beibehaltung der Clientquell-IP][client-source-ip] für Anforderungen an Container in Ihrem Cluster aktivieren möchten, fügen Sie dem Helm-Installationsbefehl `--set controller.service.externalTrafficPolicy=Local` hinzu. Die Clientquell-IP wird in der Anforderungskopfzeile unter *X-Forwarded-For* gespeichert. Bei der Verwendung eines Eingangscontrollers mit aktivierter Clientquell-IP-Beibehaltung funktioniert TLS-Pass-Through nicht.

Aktualisieren Sie das folgende Skript mit der **IP-Adresse** des Eingangscontrollers und einem **eindeutigen Namen**, den Sie als FQDN-Präfix verwenden möchten.

> [!IMPORTANT]
> *STATIC_IP* und *DNS_LABEL* müssen beim Ausführen des Befehls durch Ihre eigene IP-Adresse und Ihren eindeutigen Namen ersetzt werden.

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
    --set controller.service.loadBalancerIP="STATIC_IP" \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="DNS_LABEL"
```

Wird der Kubernetes-Lastenausgleichsdienst für den NGINX-Eingangscontroller erstellt, wird Ihre statische IP-Adresse zugewiesen, wie in der folgenden Beispielausgabe gezeigt:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Da noch keine Eingangsregeln erstellt wurden, wird die Standard-404-Seite des NGINX-Eingangscontrollers angezeigt, wenn Sie die öffentliche IP-Adresse im Browser öffnen. Eingangsregeln werden in den folgenden Schritten konfiguriert.

Sie können überprüfen, ob die DNS-Namensbezeichnung angewandt wurde, indem Sie den vollqualifizierten Domänennamen für die öffentliche IP-Adresse wie folgt abfragen:

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?name=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

Der Eingangscontroller ist jetzt über die IP-Adresse oder den vollqualifizierten Domänennamen erreichbar.

## <a name="install-cert-manager"></a>Installieren von cert-manager

Der NGINX-Eingangscontroller unterstützt TLS-Terminierung. Es gibt verschiedene Möglichkeiten zum Abrufen und Konfigurieren von Zertifikaten für HTTPS. Dieser Artikel veranschaulicht die Verwendung von [cert-manager][cert-manager], über den die automatische Zertifikatsgenerierung und Verwaltungsfunktionalität mit [Let‘s Encrypt][lets-encrypt] bereitgestellt werden.

> [!NOTE]
> In diesem Artikel wird die `staging`-Umgebung für Let‘s Encrypt verwendet. Verwenden Sie in Produktionsbereitstellungen `letsencrypt-prod` und `https://acme-v02.api.letsencrypt.org/directory` in den Ressourcendefinitionen und bei der Installation des Helm-Diagramms.

Verwenden Sie zum Installieren des cert-manager-Controllers in einem Kubernetes RBAC-fähigen Cluster den folgenden `helm install`-Befehl:

```console
# Label the cert-manager namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.16.1 \
  --set installCRDs=true \
  --set nodeSelector."beta\.kubernetes\.io/os"=linux \
  jetstack/cert-manager
```

Weitere Informationen zur cert-manager-Konfiguration finden Sie im [cert-manager-Projekt][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Erstellen eines CA-Clusterausstellers

Bevor Zertifikate ausgestellt werden können, benötigt cert-manager eine [Aussteller][cert-manager-issuer]- oder [Clusteraussteller][cert-manager-cluster-issuer]-Ressource. Die Kubernetes-Ressourcen sind funktionell identisch, aber `Issuer` arbeitet in einem einzelnen Namespace, `ClusterIssuer` hingegen in allen Namespaces. Weitere Informationen finden Sie in der [cert-manager-Dokumentation zu „Issuers“][cert-manager-issuer].

Erstellen Sie einen Clusteraussteller, wie z.B. `cluster-issuer.yaml`, mithilfe des folgenden Beispielmanifests. Aktualisieren Sie die E-Mail-Adresse mit einer gültigen Adresse aus Ihrer Organisation:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

Um den Aussteller zu erstellen, verwenden Sie den Befehl `kubectl apply`.

```
$ kubectl apply -f cluster-issuer.yaml --namespace ingress-basic

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Ausführen von Demoanwendungen

Ein Eingangscontroller und eine Zertifikatsverwaltungslösung wurden konfiguriert. Nun führen wir zwei Demoanwendungen im AKS-Cluster-aus. In diesem Beispiel wird Helm verwendet, um mehrere Instanzen einer einfachen Hallo-Welt-Anwendung auszuführen.

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

Im folgenden Beispiel wird der Datenverkehr an die Adresse `https://demo-aks-ingress.eastus.cloudapp.azure.com/` an den Dienst mit dem Namen `aks-helloworld` weitergeleitet. Datenverkehr an die Adresse `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` wird an den Dienst `ingress-demo` weitergeleitet. Aktualisieren Sie *hosts* und *host* auf den DNS-Namen, den Sie in einem vorherigen Schritt erstellt haben.

Erstellen Sie eine Datei mit dem Namen `hello-world-ingress.yaml`, und fügen Sie den folgenden YAML-Beispielcode ein.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
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

Erstellen Sie die Eingangsressource mit dem Befehl `kubectl apply`.

```
$ kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Erstellen eines Zertifikatsobjekts

Als Nächstes muss eine Zertifikatressource erstellt werden. Die Zertifikatressource definiert das gewünschte X.509-Zertifikat. Weitere Informationen finden Sie unter [cert-manager-Zertifikate][cert-manager-certificates].

„Cert-manager“ hat wahrscheinlich automatisch ein Zertifikatsobjekt für Sie erstellt, indem „ingress-shim“ verwendet wurde, das seit v0.2.2 automatisch mit „Cert-manager“ bereitgestellt wird. Weitere Informationen finden Sie in der [Dokumentation zu „ingress-shim“][ingress-shim].

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
apiVersion: cert-manager.io/v1alpha2
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

Verwenden Sie zum Erstellen der Zertifikatsressource den Befehl `kubectl apply`.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Testen der Konfiguration für eingehende Daten

Öffnen Sie einen Webbrowser für den FQDN des Kubernetes-Eingangscontrollers, z.B. *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* .

Da diese Beispiele `letsencrypt-staging` verwenden, ist das ausgestellte TLS/SSL-Zertifikat für den Browser nicht vertrauenswürdig. Akzeptieren Sie die Eingabeaufforderung der Warnung, um Ihre Anwendung zu öffnen. Die Zertifikatinformationen zeigen, dass dieses *Fake LE Intermediate X1*-Zertifikat von Let‘s Encrypt ausgestellt wurde. Dieses gefälschte Zertifikat gibt an, dass `cert-manager` die Anforderung korrekt verarbeitet und ein Zertifikat vom Anbieter erhalten hat:

![Let's Encrypt-Bereitstellungszertifikat](media/ingress/staging-certificate.png)

Wenn Sie Let‘s Encrypt so ändern, dass `prod` anstelle von `staging` verwendet wird, wird ein von Let‘s Encrypt ausgestelltes vertrauenswürdiges verwendet, wie im folgenden Beispiel gezeigt:

![Let‘s Encrypt-Zertifikat](media/ingress/certificate.png)

Die Demoanwendung wird im Webbrowser angezeigt:

![Anwendungsbeispiel 1](media/ingress/app-one.png)

Fügen Sie nun den Pfad */hello-world-two* dem FQDN hinzu, wie z.B. *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`* . Die zweite Demoanwendung mit dem benutzerdefinierten Titel wird angezeigt:

![Anwendungsbeispiel 2](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Artikel wird Helm verwendet, um die Eingangskomponenten, die Zertifikate und die Beispiel-Apps zu installieren. Wenn Sie ein Helm-Diagramm bereitstellen, werden eine Reihe von Kubernetes-Ressourcen erstellt. Diese Ressourcen enthalten Pods, Bereitstellungen und Dienste. Sie können zur Bereinigung der Ressourcen entweder den gesamten Beispielnamespace oder die einzelnen Ressourcen löschen.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Löschen des Beispielnamespace und aller Ressourcen

Verwenden Sie den `kubectl delete`-Befehl mit dem Namespacenamen, um den gesamten Beispielnamespace zu löschen. Alle Ressourcen im Namespace werden gelöscht.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Löschen einzelner Ressourcen

Mehr Kontrolle bietet eine andere Vorgehensweise, bei der Sie einzelne Ressourcen löschen. Entfernen Sie zuerst die Zertifikatressourcen:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Listen Sie nun mit dem Befehl `helm list` die Helm-Versionen auf. Suchen Sie nach Diagrammen mit den Namen *nginx-ingress* und *cert-manager*, wie in der folgenden Beispielausgabe gezeigt:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Deinstallieren Sie die Versionen mit dem Befehl `helm uninstall`. Im folgenden Beispiel werden die NGINX-Eingangsbereitstellung und Zertifikat-Managerbereitstellungen deinstalliert.

```
$ helm uninstall nginx-ingress cert-manager -n ingress-basic

release "nginx-ingress" deleted
release "cert-manager" deleted
```

Entfernen Sie als nächstes die beiden Beispielanwendungen:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
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

- [Helm-Befehlszeilenschnittstelle][helm-cli]
- [NGINX-Eingangscontroller][nginx-ingress]
- [cert-manager][cert-manager]

Sie können außerdem:

- [Erstellen eines einfachen Eingangscontrollers mit Konnektivität mit einem externen Netzwerk][aks-ingress-basic]
- [Aktivieren des Add-Ons für das HTTP-Anwendungsrouting][aks-http-app-routing]
- [Erstellen eines Eingangscontrollers, der ein internes, privates Netzwerk und eine IP-Adresse verwendet][aks-ingress-internal]
- [Erstellen eines Eingangscontrollers, der Ihre eigenen TLS-Zertifikate verwendet][aks-ingress-own-tls]
- [Erstellen eines Eingangscontrollers mit einer dynamischen öffentlichen IP-Adresse und Konfigurieren von Let's Encrypt für das automatische Generieren von TLS-Zertifikaten][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
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
[aks-static-ip]: static-ip.md
