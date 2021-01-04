---
title: Aktivieren der Unterstützung mehrerer Namespaces für Application Gateway Ingress Controller
description: Dieser Artikel enthält Informationen zum Aktivieren der Unterstützung mehrerer Namespaces in einem Kubernetes-Cluster mit Application Gateway Ingress Controller.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c13c4410852d97f0bf4548578f40a5cc560804d7
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874592"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Aktivieren der Unterstützung mehrerer Namespaces in einem AKS-Cluster mit Application Gateway Ingress Controller

## <a name="motivation"></a>Motivation
Durch Kubernetes-[Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) kann ein Kubernetes-Cluster partitioniert und Untergruppen eines größeren Teams zugeordnet werden. Diese untergeordneten Teams können dann die Infrastruktur mit präziserer Steuerung der Ressourcen, der Sicherheit und Konfiguration usw. bereitstellen und verwalten. Kubernetes ermöglicht das unabhängige Definieren einer oder mehrerer Eingangsressourcen innerhalb der einzelnen Namespaces.

Ab Version 0.7 können in [Azure Application Gateway Kubernetes Ingress Controller](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) Ereignisse aus mehreren Namespaces erfasst und mehrere Namespaces überwacht werden. Wenn der AKS-Administrator die Verwendung von [Application Gateway](https://azure.microsoft.com/services/application-gateway/) als Eingang festlegt, verwenden alle Namespaces dieselbe Instanz von Application Gateway. Mit einer einzigen Installation von Ingress Controller werden die zugänglichen Namespaces überwacht und die zugeordnete Application Gateway-Instanz konfiguriert.

In AGIC Version 0.7 wird weiterhin ausschließlich der Namespace `default` überwacht, bis dies in der Helm-Konfiguration explizit in einen oder mehrere andere Namespaces geändert wird (siehe Abschnitt unten).

## <a name="enable-multiple-namespace-support"></a>Aktivieren der Unterstützung mehrerer Namespaces
So aktivieren Sie die Unterstützung mehrerer Namespaces
1. Ändern Sie die Datei [helm-config.yaml](#sample-helm-config-file) auf eine der folgenden Weisen:
   - Löschen Sie den Schlüssel `watchNamespace` vollständig aus der Datei [helm-config.yaml](#sample-helm-config-file): In AGIC werden alle Namespaces überwacht.
   - Legen Sie `watchNamespace` auf eine leere Zeichenfolge fest: In AGIC werden alle Namespaces überwacht.
   - Fügen Sie mehrere durch Kommas getrennte Namespaces hinzu (`watchNamespace: default,secondNamespace`): In AGIC werden ausschließlich diese Namespaces überwacht.
2. Wenden Sie die Änderungen der Helm-Vorlage mit `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure` an.

Nach der Bereitstellung von AGIC mit der Funktion zum Überwachen mehrerer Namespaces wird Folgendes durchgeführt:
  - Auflisten von Eingangsressourcen aus allen zugänglichen Namespaces
  - Filtern nach Eingangsressourcen, die mit `kubernetes.io/ingress.class: azure/application-gateway` kommentiert sind
  - Erstellen einer kombinierten [Application Gateway-Konfiguration](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - Anwenden der Konfiguration auf die zugeordnete Application Gateway-Instanz über [ARM](../azure-resource-manager/management/overview.md)

## <a name="conflicting-configurations"></a>In Konflikt stehende Konfigurationen
AGIC kann von mehreren [Eingangsressourcen](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) mit Namespaces angewiesen werden, in Konflikt stehende Konfigurationen für eine Application Gateway-Instanz zu erstellen. (Zwei Eingangsressourcen beanspruchen beispielsweise dieselbe Domäne.)

Oben in der Hierarchie können **Listener** (IP-Adresse, Port und Host) und **Routingregeln** (Bindungslistener, Back-End-Pool und HTTP-Einstellungen) erstellt und von mehreren Namespaces und Eingangsressourcen gemeinsam genutzt werden.

Auf der anderen Seite können Pfade, Back-End-Pools, HTTP-Einstellungen und TLS-Zertifikate durch nur einen Namespace erstellt werden, sodass Duplikate entfernt werden.

Sehen Sie sich z. B. die folgenden beiden Eingangsressourcen mit den doppelt definierten Namespaces `staging` und `production` für `www.contoso.com` an:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

Obwohl die beiden Eingangsressourcen anfordern, dass Datenverkehr für `www.contoso.com` an die entsprechenden Kubernetes-Namespaces weitergeleitet wird, kann nur ein Back-End den Datenverkehr verarbeiten. In AGIC würde eine Konfiguration nach dem Prinzip „First Come, First Served“ für eine der Ressourcen erstellt werden. Wenn zwei Eingangsressourcen gleichzeitig erstellt werden, hat die erste im Alphabet Vorrang. Im Beispiel oben können für den `production`-Eingang lediglich Einstellungen erstellt werden. Application Gateway wird mit den folgenden Ressourcen konfiguriert:

  - Listener: `fl-www.contoso.com-80`
  - Routingregel: `rr-www.contoso.com-80`
  - Back-End-Pool: `pool-production-contoso-web-service-80-bp-80`
  - HTTP-Einstellungen: `bp-production-contoso-web-service-80-80-websocket-ingress`
  - Integritätstest: `pb-production-contoso-web-service-80-websocket-ingress`

Beachten Sie, dass die erstellten Application Gateway-Ressourcen mit Ausnahme von *Listener* und *Routingregel* den Namen des Namespace (`production`) enthalten, für den sie erstellt wurden.

Wenn die zwei Eingangsressourcen zu unterschiedlichen Zeitpunkten in den AKS-Cluster eingefügt werden, ist es wahrscheinlich, dass in AGIC die Application Gateway-Instanz neu konfiguriert und der Datenverkehr von `namespace-B` an `namespace-A` umgeleitet wird.

Wenn Sie beispielsweise `staging` zuerst hinzugefügt haben, wird die Application Gateway-Instanz in AGIC so konfiguriert, dass Datenverkehr an den Back-End-Pool von „staging“ weitergeleitet wird. Das Hinzufügen des `production`-Eingangs zu einem späteren Zeitpunkt führt dazu, dass die Application Gateway-Instanz in AGIC neu konfiguriert wird, sodass Datenverkehr an den Back-End-Pool von `production` weitergeleitet wird.

## <a name="restrict-access-to-namespaces"></a>Beschränken des Zugriffs auf Namespaces
Standardmäßig wird Application Gateway in AGIC basierend auf einem kommentierten Eingang innerhalb eines beliebigen Namespace konfiguriert. Wenn Sie dieses Verhalten einschränken möchten, haben Sie folgende Möglichkeiten:
  - Beschränken Sie die Namespaces, indem Sie die Namespaces, die in AGIC überwacht werden sollen, über den YAML-Schlüssel `watchNamespace` in der Datei [helm-config.yaml](#sample-helm-config-file) explizit definieren.
  - Verwenden Sie [Role/RoleBinding](../aks/azure-ad-rbac.md), um AGIC auf bestimmte Namespaces zu beschränken.

## <a name="sample-helm-config-file"></a>Helm-Beispielkonfigurationsdatei

```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
```