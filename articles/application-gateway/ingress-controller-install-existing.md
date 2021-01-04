---
title: Erstellen eines Eingangscontrollers mit einem vorhandenen Application Gateway
description: Dieser Artikel enthält Informationen zum Bereitstellen eines Application Gateway-Eingangscontrollers mit einem vorhandenen Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 2f585dd80219afac7c67eebabd72cb41dce0b673
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018697"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Installieren eines Application Gateway-Eingangscontrollers (Application Gateway Ingress Controller, AGIC) mithilfe eines vorhandenen Application Gateways

Der Application Gateway-Eingangscontroller (Application Gateway Ingress Controller, AGIC) ist ein Pod innerhalb Ihres Kubernetes-Clusters.
AGIC überwacht die Kubernetes-[Eingangsressourcen](https://kubernetes.io/docs/concepts/services-networking/ingress/) und erstellt und wendet Application Gateway-Konfiguration basierend auf dem Status des Kubernetes-Clusters an.

## <a name="outline"></a>Gliederung:
- [Voraussetzungen](#prerequisites)
- [Azure Resource Manager-Authentifizierung (ARM)](#azure-resource-manager-authentication)
    - Option 1: [Einrichten von aad-pod-identity](#set-up-aad-pod-identity) und Erstellen einer Azure-Identität für ARM
    - Option 2: [Verwenden eines Dienstprinzipals](#using-a-service-principal)
- [Installieren des Eingangscontrollers mit Helm](#install-ingress-controller-as-a-helm-chart)
- [Application Gateway mit mehreren Clustern/freigegebenes Application Gateway](#multi-cluster--shared-application-gateway): Installieren von AGIC in einer Umgebung, in der Application Gateway von einem oder mehreren AKS-Clustern und/oder anderen Azure-Komponenten gemeinsam genutzt wird.

## <a name="prerequisites"></a>Voraussetzungen
In diesem Dokument wird davon ausgegangen, dass Sie bereits die folgenden Tools und die folgende Infrastruktur installiert haben:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) mit aktiviertem [erweitertem Netzwerk](../aks/configure-azure-cni.md)
- [Application Gateway v2](./tutorial-autoscale-ps.md) im gleichen virtuellen Netzwerk wie AKS
- [AAD-Podidentität](https://github.com/Azure/aad-pod-identity) auf dem AKS-Cluster
- [Cloud Shell](https://shell.azure.com/) ist die Azure Shell-Umgebung, in der die `az` CLI, `kubectl` und `helm` installiert werden. Diese Tools sind für die unten aufgeführten Befehle erforderlich.

__Sichern__ Sie vor der Installation von AGIC die Konfiguration Ihres Application Gateways:
  1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer `Application Gateway`-Instanz.
  2. Klicken Sie in `Export template` auf `Download`.

Die heruntergeladene ZIP-Datei enthält JSON-Vorlagen, die Bash und PowerShell-Skripts, die Sie zum Wiederherstellen des App Gateways verwenden können, wenn dies erforderlich werden sollte.

## <a name="install-helm"></a>Installieren von Helm
[Helm](../aks/kubernetes-helm.md) ist ein Paket-Manager für Kubernetes. Wir nutzen Helm, um das `application-gateway-kubernetes-ingress`-Paket zu installieren.
Verwenden von [Cloud Shell](https://shell.azure.com/), um Helm zu installieren:

1. Installieren Sie [Helm](../aks/kubernetes-helm.md), und führen Sie Folgendes aus, um das Helm-Paket `application-gateway-kubernetes-ingress` hinzuzufügen:

    - *Für Kubernetes RBAC aktivierter* AKS-Cluster

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - *Für Kubernetes RBAC deaktivierter* AKS-Cluster

    ```bash
    helm init
    ```

1. Fügen Sie das AGIC-Helm-Repository hinzu:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Azure Resource Manager-Authentifizierung

AGIC kommuniziert mit dem Kubernetes-API-Server und Azure Resource Manager. Für den Zugriff auf diese APIs ist eine Identität erforderlich.

## <a name="set-up-aad-pod-identity"></a>Einrichten von AAD-Podidentität

[AAD-Podidentität](https://github.com/Azure/aad-pod-identity) ist ein Controller (ähnlich wie AGIC), der ebenfalls für AKS ausgeführt wird. Er bindet Azure Active Directory-Identitäten an Ihre Kubernetes-Pods. Für eine Anwendung in einem Kubernetes-Pod ist eine Identität erforderlich, um mit anderen Azure-Komponenten kommunizieren zu können. Im konkreten Fall benötigen wir hier die Berechtigung für den AGIC-Pod, um HTTP-Anforderungen an [ARM](../azure-resource-manager/management/overview.md) zu senden.

Befolgen Sie die [Installationsanweisungen für die AAD-Podidentität](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra), um diese Komponente Ihrem AKS hinzuzufügen.

Als nächstes müssen wir eine Azure-Identität erstellen und ihr ARM-Berechtigungen zuweisen.
Verwenden Sie [Cloud Shell](https://shell.azure.com/), um die folgenden Befehle auszuführen und eine Identität zu erstellen:

1. Erstellen Sie eine Azure-Identität **in derselben Ressourcengruppe wie die AKS-Knoten**. Die Auswahl der richtigen Ressourcengruppe ist wichtig. Die im folgenden Befehl erforderliche Ressourcengruppe ist *nicht* die im AKS-Portalbereich referenzierte Ressourcengruppe. Dies ist die Ressourcengruppe der virtuellen `aks-agentpool`-Computer. In der Regel beginnt diese Ressourcengruppe mit `MC_` und enthält den Namen Ihres AKS. Beispiel: `MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Für die unten aufgeführten Rollenzuweisungsbefehle müssen wir `principalId` für die neu erstellte Identität abrufen:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Gewähren Sie der Identität `Contributor` Zugriff auf Ihre Application Gateway-Instanz. Hierfür benötigen Sie die ID des Application Gateways, die in etwa wie folgt aussieht: `/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Rufen Sie die Liste der Application Gateway-IDs in Ihrem Abonnement wie folgt ab: `az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Gewähren Sie der Identität `Reader` Zugriff auf die Application Gateway-Ressourcengruppe. Die Ressourcengruppen-ID sieht wie folgt aus: `/subscriptions/A/resourceGroups/B`. Sie können alle Ressourcengruppen wie folgt abrufen: `az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Verwenden eines Dienstprinzipals
Es ist auch möglich, AGIC-Zugriff auf ARM über ein Kubernetes-Geheimnis bereitzustellen.

1. Erstellen Sie einen Active Directory-Dienstprinzipal, und codieren Sie ihn als base64. Die base64-Codierung ist erforderlich, damit das JSON-Blob in Kubernetes gespeichert wird.

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. Fügen Sie der Datei `helm-config.yaml` das base64-codierte JSON-Blob hinzu. Weitere Informationen zu `helm-config.yaml` finden Sie im nächsten Abschnitt.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Installieren des Eingangscontrollers als Helm-Diagramm
In den ersten Schritten installieren wir Tiller von Helm auf Ihrem Kubernetes-Cluster. Verwenden Sie [Cloud Shell](https://shell.azure.com/), um das AGIC Helm-Paket zu installieren:

1. Hinzufügen des `application-gateway-kubernetes-ingress`-Repositorys von Helm und Ausführen eines Helm-Updates

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. Laden Sie die Datei „helm-config.yaml“ herunter, mit der AGIC konfiguriert wird:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Oder kopieren Sie die folgende YAML-Datei: 
    
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Bearbeiten Sie „helm-config.yaml“, und geben Sie die Werte für `appgw` und `armAuth` an.
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > `<identity-resource-id>` und `<identity-client-id>` sind die Eigenschaften der Azure AD-Identität, die Sie im vorherigen Abschnitt eingerichtet haben. Sie können diese Informationen abrufen, indem Sie den folgenden Befehl ausführen: `az identity show -g <resourcegroup> -n <identity-name>`, wobei `<resourcegroup>` die Ressourcengruppe ist, in der das AKS-Clusterobjekt der obersten Ebene, Application Gateway und die verwaltete Identität bereitgestellt werden.

1. Installieren des Helm-Diagramms `application-gateway-kubernetes-ingress` mit der `helm-config.yaml`-Konfiguration aus dem vorherigen Schritt

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    Alternativ dazu können Sie die Datei `helm-config.yaml` und den Helm-Befehl in einem einzigen Schritt kombinieren:
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Überprüfen des Protokolls des neu erstellten Pods, um sicherzustellen, dass er ordnungsgemäß gestartet wurde

Lesen Sie [diese Anleitung mit Vorgehensweisen](ingress-controller-expose-service-over-http-https.md), um zu verstehen, wie Sie einen AKS-Dienst über HTTP oder HTTPS über ein Azure Application Gateway im Internet bereitstellen können.



## <a name="multi-cluster--shared-application-gateway"></a>Application Gateway mit mehreren Clustern/freigegebenes Application Gateway
Standardmäßig übernimmt AGIC den vollständigen Besitz des Application Gateways, mit dem er verbunden ist. AGIC Version 0.8.0 und höher kann ein einzelnes Application Gateway mit anderen Azure-Komponenten gemeinsam nutzen. Beispielsweise können wir das gleiche Application Gateway für eine App verwenden, die in einer VM-Skalierungsgruppe und in einem AKS-Cluster gehostet wird.

__Sichern__ Sie die Konfiguration Ihres Application Gateways, bevor Sie diese Einstellung aktivieren:
  1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer `Application Gateway`-Instanz.
  2. Klicken Sie in `Export template` auf `Download`.

Die heruntergeladene ZIP-Datei enthält JSON-Vorlagen, die Bash und PowerShell-Skripts, die Sie zum Wiederherstellen des App Gateways verwenden können.

### <a name="example-scenario"></a>Beispielszenario
Sehen wir uns eine imaginäre Application Gateway-Instanz an, die den Datenverkehr für zwei Websites verwaltet:
  - `dev.contoso.com`: Gehostet in einem neuen AKS mithilfe von Application Gateway und AGIC
  - `prod.contoso.com`: Gehostet in einer [Azure-VM-Skalierungsgruppe](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

Mit den Standardeinstellungen übernimmt AGIC den Besitz des Application Gateways, auf das er verweist, zu 100 %. AGIC überschreibt die gesamte Konfiguration des App Gateways. Wenn wir manuell einen Listener für `prod.contoso.com` (auf dem Application Gateway) erstellen würden, ohne ihn in Kubernetes Ingress zu definieren, löscht AGIC die `prod.contoso.com`-Konfiguration innerhalb von Sekunden.

Um AGIC zu installieren und auch `prod.contoso.com` über die Computer der VM-Skalierungsgruppe zu verarbeiten, müssen wir AGIC darauf beschränken, nur `dev.contoso.com` zu konfigurieren. Dies wird durch Instanziierung der folgenden [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) ermöglicht:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

Mit dem oben genannten Befehl wird ein `AzureIngressProhibitedTarget`-Objekt erstellt. Dadurch wird AGIC (Version 0.8.0 und höher) über das Vorhandensein der Application Gateway-Konfiguration für `prod.contoso.com` informiert und ausdrücklich angewiesen, keine Änderungen an der Konfiguration für diesen Hostnamen vorzunehmen.


### <a name="enable-with-new-agic-installation"></a>Aktivierung mit der neuen AGIC-Installation
Um AGIC (Version 0.8.0 und höher) auf eine Teilmenge der Application Gateway-Konfiguration zu beschränken, ändern Sie die `helm-config.yaml`-Vorlage.
Fügen Sie im Abschnitt `appgw:` den Schlüssel `shared` hinzu, und legen Sie ihn auf `true` fest.

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Anwenden der Helm-Änderungen:
  1. Stellen Sie sicher, dass die `AzureIngressProhibitedTarget`-CRD wie folgt installiert ist:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Aktualisieren Sie Helm:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

AKS weist als Ergebnis eine neue Instanz von `AzureIngressProhibitedTarget` namens `prohibit-all-targets` auf:
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

Das Objekt `prohibit-all-targets` verhindert (wie der Name schon sagt), dass AGIC die Konfiguration für *beliebige* Hosts und Pfade ändert.
Bei der Helm-Installation mit `appgw.shared=true` wird AGIC bereitgestellt, aber es werden keine Änderungen am Application Gateway vorgenommen.


### <a name="broaden-permissions"></a>Ausweiten der Berechtigungen
Dies ist möglich, da Helm mit `appgw.shared=true` und das standardmäßige `prohibit-all-targets`-Objekt verhindern, dass AGIC eine beliebige Konfiguration anwendet.

Erweitern Sie die AGIC-Berechtigungen wie folgt:
1. Erstellen Sie ein neues `AzureIngressProhibitedTarget`-Objekt mit Ihrem spezifischen Setup:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. Erst nachdem Sie Ihr eigenes benutzerdefiniertes Verbot erstellt haben, können Sie das standardmäßige Verbot löschen, das zu umfangreich ist:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Aktivierung für eine vorhandene AGIC-Installation
Nehmen wir an, dass wir bereits einen funktionierenden AKS, ein Application Gateway und einen konfigurierten AGIC in unserem Cluster verwenden. Wir verfügen über eingehenden Datenverkehr für `prod.contosor.com` und bedienen ihn erfolgreich aus AKS. Wir möchten unserem vorhandenen Application Gateway `staging.contoso.com` hinzufügen, müssen dies jedoch auf einer [VM](https://azure.microsoft.com/services/virtual-machines/) hosten. Wir verwenden die vorhandene Application Gateway-Instanz erneut und konfigurieren manuell einen Listener und Back-End-Pools für `staging.contoso.com`. Eine manuelle Anpassung der Application Gateway-Konfiguration (über das [Portal](https://portal.azure.com), [ARM-APIs](/rest/api/resources/) oder [Terraform](https://www.terraform.io/)) würde jedoch im Widerspruch zur Annahme des AGIC über den vollständigen Besitz stehen. Kurz nachdem wir Änderungen vorgenommen haben, überschreibt oder löscht AGIC diese.

Wir können AGIC verbieten, Änderungen an einer Teilmenge der Konfiguration vorzunehmen.

1. Erstellen eines `AzureIngressProhibitedTarget`-Objekts:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. Anzeigen des neu erstellten Objekts:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. Ändern der Application Gateway-Konfiguration über das Portal: Hinzufügen von Listenern, Routingregeln, Back-Ends usw. Das neue Objekt, das wir erstellt haben (`manually-configured-staging-environment`), verhindert, dass AGIC Application Gateway-Konfiguration für `staging.contoso.com`überschreibt.