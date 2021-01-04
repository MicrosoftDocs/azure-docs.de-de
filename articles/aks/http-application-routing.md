---
title: Add-On für HTTP-Anwendungsrouting in Azure Kubernetes Service (AKS)
description: In diesem Artikel erfahren Sie mehr über die Verwendung des Add-Ons für das HTTP-Anwendungsrouting zum Zugreifen auf Anwendungen, die in Azure Kubernetes Service (AKS) bereitgestellt wurden.
services: container-service
author: lachie83
ms.topic: article
ms.date: 07/20/2020
ms.author: laevenso
ms.openlocfilehash: bbedb20d9e5c75fd49c08950bbf5d459130206ce
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93125868"
---
# <a name="http-application-routing"></a>HTTP-Anwendungsrouting

Die Lösung für das HTTP-Anwendungsrouting ermöglicht einen einfachen Zugriff auf Anwendungen, die in Ihrem AKS-Cluster (Azure Kubernetes Service) bereitgestellt sind. Bei Aktivierung konfiguriert die Lösung einen [Eingangscontroller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) in Ihrem AKS-Cluster. Darüber hinaus erstellt die Lösung bei der Bereitstellung von Anwendungen auch öffentlich zugängliche DNS-Namen für Anwendungsendpunkte.

Wenn Sie dieses Add-On aktivieren, erstellt es eine DNS-Zone in Ihrem Abonnement. Weitere Informationen zu den DNS-Kosten finden Sie unter [Azure DNS – Preise][dns-pricing].

> [!CAUTION]
> Das Add-On für HTTP-Anwendungsrouting ist so ausgelegt, dass Sie schnell einen Eingangscontroller erstellen und auf Ihre Anwendungen zugreifen können. Dieses Add-On ist zurzeit nicht für die Verwendung in einer Produktionsumgebung konzipiert und wird nicht für die Verwendung in der Produktion empfohlen. Informationen zu produktionsreifen Eingangsbereitstellungen mit mehreren Replikaten und TLS-Unterstützung finden Sie unter [Erstellen eines HTTPS-Eingangscontrollers](./ingress-tls.md).

## <a name="http-routing-solution-overview"></a>Übersicht über die HTTP-Routinglösung

Das Add-On stellt zwei Komponenten bereit: einen [Kubernetes-Eingangscontroller][ingress] und einen [externen DNS-Controller][external-dns].

- **Eingangscontroller:** Der Eingangscontroller ist über einen Kubernetes-Dienst vom Typ LoadBalancer mit dem Internet verbunden. Er überwacht und implementiert [Kubernetes-Eingangsressourcen][ingress-resource], die Routen zu Anwendungsendpunkten erstellen.
- **Externer DNS-Controller:** Überwacht die Kubernetes-Eingangsressourcen und erstellt in der clusterspezifischen DNS-Zone DNS-A-Datensätze.

## <a name="deploy-http-routing-cli"></a>Bereitstellen von HTTP-Routing: Befehlszeilenschnittstelle (CLI)

Das Add-On für HTTP-Anwendungsrouting kann beim Bereitstellen eines AKS-Clusters über die Azure-Befehlszeilenschnittstelle (Command-Line Interface, CLI) aktiviert werden. Verwenden Sie hierzu den Befehl [az aks create][az-aks-create] mit dem Argument `--enable-addons`.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> Falls Sie mehrere Add-Ons aktivieren möchten, geben Sie sie als eine durch Trennzeichen getrennte Liste an. Verwenden Sie zum Aktivieren von HTTP-Anwendungsrouting und -überwachung das Format `--enable-addons http_application_routing,monitoring`.

Sie können auch HTTP-Routing für einen vorhandenen AKS-Cluster mithilfe des Befehls [az aks enable-addons][az-aks-enable-addons] aktivieren. Zum Aktivieren von HTTP-Routing für einen vorhandenen Cluster fügen Sie den Parameter `--addons` hinzu und geben *Http_application_routing* wie im folgenden Beispiel gezeigt an:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

Nachdem der Cluster bereitgestellt oder aktualisiert wurde, rufen Sie mit dem Befehl [az aks show][az-aks-show] den Namen der DNS-Zone ab.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table
```

Dieser Name ist erforderlich, um Anwendungen auf dem AKS-Cluster bereitzustellen, und wird in der folgenden Beispielausgabe dargestellt:

```console
9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io
```

## <a name="deploy-http-routing-portal"></a>Bereitstellen von HTTP-Routing: Portal

Das Add-On für HTTP-Anwendungsrouting kann beim Bereitstellen eines AKS-Clusters über das Azure-Portal aktiviert werden.

![Aktivieren der HTTP-Routingfunktion](media/http-routing/create.png)

Nachdem der Cluster bereitgestellt wurde, navigieren Sie zur automatisch erstellten AKS-Ressourcengruppe, und wählen Sie die DNS-Zone aus. Notieren Sie sich den DNS-Zonennamen. Dieser Name wird zur Bereitstellung von Anwendungen im AKS-Cluster benötigt.

![Abrufen des DNS-Zonennamens](media/http-routing/dns.png)

## <a name="connect-to-your-aks-cluster"></a>Herstellen einer Verbindung mit dem AKS-Cluster

Zum Herstellen der Verbindung mit dem Kubernetes-Cluster auf Ihrem lokalen Computer verwenden Sie den Kubernetes-Befehlszeilenclient [kubectl][kubectl].

Wenn Sie Azure Cloud Shell verwenden, ist `kubectl` bereits installiert. Mit dem Befehl [az aks install-cli][] können Sie ihn auch lokal installieren:

```azurecli
az aks install-cli
```

Mit dem Befehl [az aks get-credentials][] können Sie `kubectl` für die Verbindungsherstellung mit Ihrem Kubernetes-Cluster konfigurieren. Im folgenden Beispiel werden Anmeldeinformationen für den AKS-Cluster namens *MyAKSCluster* in *MyResourceGroup* abgerufen:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKSCluster
```

## <a name="use-http-routing"></a>Verwenden von HTTP-Routing

Die Lösung für das HTTP-Anwendungsrouting darf nur auf Eingangsressourcen ausgelöst werden, die wie folgt kommentiert sind:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Erstellen Sie eine Datei namens **samples-http-application-routing.yaml** , und kopieren Sie den folgenden YAML-Code. Aktualisieren Sie in Zeile 43 `<CLUSTER_SPECIFIC_DNS_ZONE>` mit dem im vorherigen Schritt dieses Artikels erfassten DNS-Zonennamen.

```yaml
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
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: aks-helloworld
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: aks-helloworld.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /
```

Verwenden Sie den Befehl [kubectl apply][kubectl-apply], um die Ressourcen zu erstellen.

```bash
kubectl apply -f samples-http-application-routing.yaml
```

Die erstellten Ressourcen werden im folgenden Beispiel veranschaulicht:

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment.apps/aks-helloworld created
service/aks-helloworld created
ingress.networking.k8s.io/aks-helloworld created
```

Öffnen Sie einen Webbrowser mit *aks-helloworld.\<CLUSTER_SPECIFIC_DNS_ZONE\>* , z. B. *aks-helloworld.9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io* und überprüfen Sie, ob Sie die Demoanwendung sehen. Es kann einige Minuten dauern, bis die Anwendung angezeigt wird.

## <a name="remove-http-routing"></a>Entfernen von HTTP-Routing

Die HTTP-Routinglösung kann mithilfe der Azure CLI entfernt werden. Dazu führen Sie den folgenden Befehl aus und ersetzen dabei Ihren AKS-Cluster und Ressourcengruppennamen.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

Wenn das Routing-Add-On für HTTP-Anwendungen deaktiviert ist, verbleiben eventuell einige Kubernetes-Ressourcen im Cluster. Zu diesen Ressourcen zählen *configMaps* und *secrets* , die im Namespace *kube-system* erstellt werden. Wenn Sie stets einen sauberen Cluster haben möchten, empfiehlt es sich, diese Ressourcen zu entfernen.

Suchen Sie mit den folgenden [kubectl get][kubectl-get]-Befehlen nach Ressourcen des Typs *addon-http-application-routing* :

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

Die folgende Beispielausgabe zeigt configMaps, die gelöscht werden sollten:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

Verwenden Sie zum Löschen von Ressourcen den Befehl [kubectl delete][kubectl-delete]. Geben Sie den Ressourcentyp, den Ressourcennamen und den Namespace an. Das folgende Beispiel löscht eine der vorherigen configMaps:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Wiederholen Sie den vorherigen Schritt `kubectl delete` für alle Ressourcen des Typs *addon-http-application-routing* , die sich noch in Ihrem Cluster befinden.

## <a name="troubleshoot"></a>Problembehandlung

Verwenden Sie den Befehl [kubectl logs][kubectl-logs], um die Anwendungsprotokolle für die externe DNS-Anwendung anzuzeigen. Die Protokolle sollten bestätigen, dass ein A- und TXT-DNS-Eintrag erfolgreich erstellt wurden.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'aks-helloworld' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'aks-helloworld' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Diese Datensätze können auch bei der DNS-Zonenressource im Azure-Portal eingesehen werden.

![Abrufen der DNS-Datensätze](media/http-routing/clippy.png)

Mithilfe des Befehls [kubectl logs][kubectl-logs] können Sie sich die Anwendungsprotokolle für den Nginx-Eingangscontroller anzeigen lassen. Die Protokolle sollten den `CREATE`-Befehl für eine Eingangsressource und das erneute Laden des Controllers bestätigen. Alle HTTP-Aktivitäten werden protokolliert.

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"aks-helloworld", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/aks-helloworld
W0426 21:51:57.908771       9 controller.go:775] service default/aks-helloworld does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-aks-helloworld-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>Bereinigung

Entfernen Sie die zugeordneten Kubernetes-Objekte, die in diesem Artikel erstellt wurden, mithilfe von `kubectl delete`.

```bash
kubectl delete -f samples-http-application-routing.yaml
```

Die Beispielausgabe zeigt, dass Kubernetes-Objekte entfernt wurden.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "aks-helloworld" deleted
service "aks-helloworld" deleted
ingress "aks-helloworld" deleted
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Installation eines HTTPS-gesicherten Eingangscontrollers in AKS finden Sie unter [Eingehender HTTPS-Datenverkehr in Azure Kubernetes Service (AKS)][ingress-https].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials

<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
