---
title: Anpassen von CoreDNS für Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie CoreDNS anpassen, um mit Azure Kubernetes Service (AKS) Unterdomänen hinzuzufügen oder benutzerdefinierte DNS-Endpunkte zu erweitern
services: container-service
author: palma21
ms.topic: article
ms.date: 03/15/2019
ms.author: jpalma
ms.openlocfilehash: 5b13931bc6a13d988c21f728b996c51270769e0c
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368680"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Anpassen von CoreDNS mit Azure Kubernetes Service

Azure Kubernetes Service (AKS) verwendet das [CoreDNS][coredns]-Projekt für die Cluster-DNS-Verwaltung und die Auflösung aller Cluster der Version *1.12.x* und höher. Zuvor wurde das kube-dns-Projekt verwendet. Das kube-dns-Projekt ist inzwischen jedoch veraltet. Weitere Informationen zur CoreDNS-Anpassung und zu Kubernetes finden Sie in der [offiziellen Upstream-Dokumentation][corednsk8s].

Weil AKS ein verwalteter Dienst ist, können Sie die Hauptkonfiguration für CoreDNS (ein *CoreFile*) nicht ändern. Stattdessen verwenden Sie eine *ConfigMap* von Kubernetes, um die Standardeinstellungen zu überschreiben. Um die standardmäßigen AKS CoreDNS ConfigMaps anzuzeigen, verwenden Sie den Befehl `kubectl get configmaps --namespace=kube-system coredns -o yaml`.

In diesem Artikel wird erläutert, wie Sie ConfigMaps für grundlegende Anpassungsoptionen von CoreDNS in AKS verwenden. Diese Vorgehensweise unterscheidet sich von der Konfiguration von CoreDNS in anderen Kontexten wie der Verwendung von CoreFile. Überprüfen Sie die verwendete Version von CoreDNS, da sich die Konfigurationswerte zwischen den Versionen ändern können.

> [!NOTE]
> `kube-dns` bot verschiedene [Anpassungsoptionen][kubednsblog] über eine Kubernetes-Konfigurationszuordnung. CoreDNS ist **nicht** abwärtskompatibel zu kube-dns. Alle Anpassungen, die Sie zuvor verwendet haben, müssen für die Verwendung mit CoreDNS aktualisiert werden.

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

Wenn Sie eine Konfiguration wie die in den unten aufgeführten Beispielen erstellen, müssen die Namen im Abschnitt *Daten* entweder auf *.server* oder *.override* enden. Diese Namenskonvention wird in der Standard-ConfigMap von AKS CoreDNS definiert, die Sie mit dem Befehl `kubectl get configmaps --namespace=kube-system coredns -o yaml` anzeigen können.

## <a name="what-is-supportedunsupported"></a>Was wird unterstützt/nicht unterstützt?

Alle integrierten CoreDNS-Plug-Ins werden unterstützt. Es werden keine Add-On-/Drittparteien-Plug-Ins unterstützt.

## <a name="rewrite-dns"></a>Erneutes Schreiben des DNS

Ein Szenario, das Sie durchführen müssen, sind dynamische DNS-Namensneuschreibungen. Ersetzen Sie im folgenden Beispiel `<domain to be written>` durch Ihren eigenen vollqualifizierten Domänennamen. Erstellen Sie eine Datei namens `corednsms.yaml`, und fügen Sie die folgende Beispielkonfiguration ein:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          upstream
          fallthrough in-addr.arpa ip6.arpa
        }
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10, but that server must be able to resolve the rewritten domain name
    }
```

> [!IMPORTANT]
> Wenn Sie zu einem DNS-Server umleiten, z. B. die CoreDNS-Dienst-IP, muss dieser DNS-Server in der Lage sein, den umgeschriebenen Domänennamen aufzulösen.

Erstellen Sie die ConfigMap mit dem Befehl [kubectl apply configmap][kubectl-apply], und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl apply -f corednsms.yaml
```

Um zu prüfen, ob die Anpassungen angewendet wurden, verwenden Sie [kubectl get configmaps][kubectl-get], und geben Sie Ihre ConfigMap *coredns-custom* an:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Erzwingen Sie nun das erneute Laden der ConfigMap durch CoreDNS. Der Befehl [kubectl delete pod][kubectl delete] ist nicht destruktiv und verursacht keine Ausfallzeiten. Die `kube-dns`-Pods werden gelöscht, und der Kubernetes Scheduler erstellt sie dann erneut. Diese neuen Pods enthalten den geänderten TTL-Wert.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Der obige Befehl ist richtig. Während wir `coredns` ändern, befindet sich die Bereitstellung unter dem Namen **kube-dns**.

## <a name="custom-forward-server"></a>Benutzerdefinierter Weiterleitungsserver

Wenn Sie einen Weiterleitungsserver für den Netzwerkdatenverkehr angeben müssen, können Sie eine ConfigMap für das Anpassen von DNS erstellen. Im folgenden Beispiel aktualisieren Sie den Namen und die Adresse für den `forward` mit den Werten Ihrer eigenen Umgebung. Erstellen Sie eine Datei namens `corednsms.yaml`, und fügen Sie die folgende Beispielkonfiguration ein:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Erstellen Sie wie im vorherigen Beispiel die ConfigMap mit dem Befehl [kubectl apply configmap][kubectl-apply], und geben Sie den Namen Ihres YAML-Manifests an. Erzwingen Sie für die Neuerstellung dann mit [kubectl delete pod][kubectl delete] das erneute Laden der ConfigMap durch CoreDNS für den Kubernetes Scheduler:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Verwenden benutzerdefinierter Domänen

Sie sollten benutzerdefinierte Domänen konfigurieren, die nur intern aufgelöst werden können. Sie sollten z. B. die benutzerdefinierte Domäne *puglife.local* auflösen, die keine gültige Domäne der obersten Ebene ist. Ohne eine ConfigMap für benutzerdefinierte Domänen kann der AKS-Cluster die Adresse nicht auflösen.

Aktualisieren Sie im folgenden Beispiel die benutzerdefinierte Domäne und IP-Adresse, um Datenverkehr mit den Werten für Ihre eigene Umgebung dorthin zu leiten. Erstellen Sie eine Datei namens `corednsms.yaml`, und fügen Sie die folgende Beispielkonfiguration ein:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: | # you may select any name here, but it must end with the .server file extension
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Erstellen Sie wie im vorherigen Beispiel die ConfigMap mit dem Befehl [kubectl apply configmap][kubectl-apply], und geben Sie den Namen Ihres YAML-Manifests an. Erzwingen Sie für die Neuerstellung dann mit [kubectl delete pod][kubectl delete] das erneute Laden der ConfigMap durch CoreDNS für den Kubernetes Scheduler:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Stubdomänen

CoreDNS kann auch für das Konfigurieren von Stubdomänen verwendet werden. Aktualisieren Sie im folgenden Beispiel die benutzerdefinierten Domänen und IP-Adressen mit den Werten Ihrer eigenen Umgebung. Erstellen Sie eine Datei namens `corednsms.yaml`, und fügen Sie die folgende Beispielkonfiguration ein:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Erstellen Sie wie im vorherigen Beispiel die ConfigMap mit dem Befehl [kubectl apply configmap][kubectl-apply], und geben Sie den Namen Ihres YAML-Manifests an. Erzwingen Sie für die Neuerstellung dann mit [kubectl delete pod][kubectl delete] das erneute Laden der ConfigMap durch CoreDNS für den Kubernetes Scheduler:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Hosts-Plug-In

Da alle integrierten Plug-Ins unterstützt werden, ist das CoreDNS [Hosts][coredns hosts]-Plug-In ebenfalls für die Anpassung verfügbar:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: | # you may select any name here, but it must end with the .override file extension
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>Aktivieren der Protokollierung zum Debuggen von DNS-Abfragen 

Um die DNS-Abfrageprotokollierung zu aktivieren, wenden Sie die folgende Konfiguration in Ihrer coredns-custom-ConfigMap an:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: | # you may select any name here, but it must end with the .override file extension
        log
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden einige Beispielszenarios für die CoreDNS-Anpassung gezeigt. Informationen zum CoreDNS-Projekt finden Sie auf der [Seite des CoreDNS-Upstream-Projekts][coredns].

Weitere Informationen zu den Kernnetzwerkkonzepten finden Sie unter [Netzwerkkonzepte für Anwendungen in Azure Kubernetes Service (AKS)][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
