---
title: Erstellen eines Eingangscontrollers mit einem neuen Application Gateway
description: Dieser Artikel enthält Informationen zum Bereitstellen eines Application Gateway-Eingangscontrollers mit einem neuen Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5e3473a9afefe73fe7b07d3efda1f53675264fc8
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874626"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Installieren eines Application Gateway-Eingangscontrollers (Application Gateway Ingress Controller, AGIC) mithilfe eines neuen Application Gateways

Die nachfolgenden Anweisungen gehen davon aus, dass der Application Gateway-Eingangscontroller (Application Gateway Ingress Controller, AGIC) in einer Umgebung ohne bereits vorhandene Komponenten installiert wird.

## <a name="required-command-line-tools"></a>Erforderliche Befehlszeilentools

Es wird empfohlen, [Azure Cloud Shell](https://shell.azure.com/) für alle unten aufgeführten Befehlszeilenvorgänge zu verwenden. Starten Sie Ihre Shell über shell.azure.com oder durch Klicken auf den Link:

[![Start einbetten](https://shell.azure.com/images/launchcloudshell.png "Starten von Azure Cloud Shell")](https://shell.azure.com)

Alternativ können Sie Cloud Shell über das Azure-Portal mithilfe des folgenden Symbols starten:

![Start über das Portal](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

Ihre [Azure Cloud Shell](https://shell.azure.com/) verfügt bereits über alle erforderlichen Tools. Wenn Sie sich für die Verwendung einer anderen Umgebung entscheiden, stellen Sie sicher, dass die folgenden Befehlszeilentools installiert sind:

* `az` – Azure CLI: [Installationsanleitungen](/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl` – Kubernetes-Befehlszeilentool: [Installationsanleitungen](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm` – Kubernetes-Paket-Manager: [Installationsanleitungen](https://github.com/helm/helm/releases/latest)
* `jq` – Befehlszeilen-JSON-Prozessor: [Installationsanleitungen](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Erstellen einer Identität

Führen Sie die folgenden Schritte aus, um ein AAD-[Dienstprinzipalobjekt](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) (Azure Active Directory) zu erstellen. Notieren Sie sich die Werte für `appId`, `password` und `objectId`. Diese werden in den folgenden Schritten verwendet.

1. Erstellen des AD-Dienstprinzipals ([Mehr über Azure RBAC erfahren](../role-based-access-control/overview.md)):
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    Die Werte `appId` und `password` aus der JSON-Ausgabe werden in den folgenden Schritten verwendet.


1. Verwenden Sie die `appId` aus der Ausgabe des vorherigen Befehls, um die `objectId` des neuen Dienstprinzipals abzurufen:
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    Die Ausgabe dieses Befehls ist `objectId`. Sie wird in der Azure Resource Manager-Vorlage verwendet.

1. Erstellen Sie die Parameterdatei, die später in der Azure Resource Manager-Vorlagenbereitstellung verwendet wird.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    Legen Sie zum Bereitstellen eines für **Kubernetes RBAC** aktivierten Clusters das Feld `aksEnableRBAC` auf `true` fest.

## <a name="deploy-components"></a>Bereitstellungskomponenten
In diesem Schritt werden Ihrem Abonnement die folgenden Komponenten hinzugefügt:

- [Azure Kubernetes Service](../aks/intro-kubernetes.md)
- [Application Gateway](./overview.md) v2
- [Virtuelles Netzwerk](../virtual-network/virtual-networks-overview.md) mit 2 [Subnetzen](../virtual-network/virtual-networks-overview.md)
- [Öffentliche IP-Adresse](../virtual-network/virtual-network-public-ip-address.md)
- [Verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md), die von der [AAD-Podidentität verwendet wird](https://github.com/Azure/aad-pod-identity/blob/master/README.md)

1. Laden Sie die Azure Resource Manager-Vorlage herunter, und ändern Sie die Vorlage nach Bedarf.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Stellen Sie die Azure Resource Manager-Vorlage mit `az cli` bereit. Dieser Vorgang kann bis zu 5 Minuten dauern.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az group deployment create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. Nachdem die Bereitstellung abgeschlossen ist, laden Sie die Bereitstellungsausgabe in eine Datei namens `deployment-outputs.json` herunter.
    ```azurecli
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Einrichten des Azure Application Gateway-Eingangscontrollers

Mit den Anweisungen im vorherigen Abschnitt haben wir einen neuen AKS-Cluster und ein Application Gateway erstellt und konfiguriert. Wir können nun eine Beispiel-App und einen Eingangscontroller in unserer neuen Kubernetes-Infrastruktur bereitstellen.

### <a name="setup-kubernetes-credentials"></a>Einrichten von Kubernetes-Anmeldeinformationen
Für die folgenden Schritte benötigen Sie den Befehl zum Einrichten von [kubectl](https://kubectl.docs.kubernetes.io/), mit dem eine Verbindung mit unserem neuen Kubernetes-Cluster hergestellt wird. In [Cloud Shell](https://shell.azure.com/) ist `kubectl` bereits installiert. Wir verwenden die `az` CLI zum Abrufen von Anmeldeinformationen für Kubernetes.

Abrufen von Anmeldeinformationen für den neu bereitgestellten AKS ([weitere Informationen](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)):
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>Installieren der AAD-Podidentität
  Die Azure Active Directory-Podidentität bietet tokenbasierten Zugriff auf [Azure Resource Manager](../azure-resource-manager/management/overview.md) (ARM).

  Die [AD-Podidentität](https://github.com/Azure/aad-pod-identity) fügt Ihrem Kubernetes-Cluster folgende Komponenten hinzu:
   * Kubernetes-[CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity` und `AzureIdentityBinding`
   * [MIC-Komponente (Managed Identity Controller)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
   * [NMI-Komponente (Node Managed Identity)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)


So installieren Sie die AAD-Podidentität in Ihrem Cluster:

   - *Für Kubernetes RBAC aktivierter* AKS-Cluster

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *Für Kubernetes RBAC deaktivierter* AKS-Cluster

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Installieren von Helm
[Helm](../aks/kubernetes-helm.md) ist ein Paket-Manager für Kubernetes. Wir nutzen Helm, um das `application-gateway-kubernetes-ingress`-Paket zu installieren.

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

### <a name="install-ingress-controller-helm-chart"></a>Installieren des Helm-Eingangscontrollerdiagramms

1. Verwenden Sie die oben erstellte Datei `deployment-outputs.json`, und erstellen Sie die folgenden Variablen.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Bearbeiten Sie die neu heruntergeladene Datei „helm-config.yaml“, und füllen Sie die Abschnitte `appgw` und `armAuth` aus.
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Werte:
     - `verbosityLevel`: Legt die Ausführlichkeitsebene der AGIC-Protokollinfrastruktur fest. Unter [Protokolliergrade](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) finden Sie mögliche Werte.
     - `appgw.subscriptionId`: Die ID des Azure-Abonnements, in dem sich die Application Gateway-Instanz befindet. Beispiel: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: Der Name der Azure-Ressourcengruppe, in der die Application Gateway-Instanz erstellt wurde. Beispiel: `app-gw-resource-group`
     - `appgw.name`: Name der Application Gateway-Instanz. Beispiel: `applicationgatewayd0f0`
     - `appgw.shared`: Für dieses boolesche Flag muss standardmäßig `false` festgelegt werden. Legen Sie es auf `true` fest, wenn Sie eine [freigegebene Application Gateway-Instanz](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway) benötigen.
     - `kubernetes.watchNamespace`: Geben Sie den Namespace an, der von AGIC überwacht werden soll. Dabei kann es sich um eine einzelne Zeichenfolge oder eine durch Trennzeichen getrennte Namespaceliste handeln.
    - `armAuth.type`: kann `aadPodIdentity` oder `servicePrincipal` sein.
    - `armAuth.identityResourceID`: Die Ressourcen-ID der verwalteten Azure-Identität.
    - `armAuth.identityClientId`: Die Client-ID der Identität Weitere Informationen zur Identität finden Sie unten.
    - `armAuth.secretJSON`: Nur erforderlich, wenn der Typ des Dienstprinzipalgeheimnisses ausgewählt wird (beim Festlegen von `armAuth.type` auf `servicePrincipal`) 


   > [!NOTE]
   > Bei `identityResourceID` und `identityClientID` handelt es sich um Werte, die während der Schritte [Komponenten bereitstellen](ingress-controller-install-new.md#deploy-components) erstellt wurden. Sie können mit dem folgenden Befehl erneut abgerufen werden:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>` im obigen Befehl ist die Ressourcengruppe Ihres Application Gateways. `<identity-name>` ist der Name der erstellten Identität. Alle Identitäten für ein bestimmtes Abonnement werden unter Verwendung von `az identity list` aufgelistet:


1. Installieren Sie das Application Gateway-Eingangscontrollerpaket:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Installieren einer Beispiel-App
Nach der Installation der Application Gateway-Instanz sowie von AKS und AGIC können Sie die Beispiel-App über [Azure Cloud Shell](https://shell.azure.com/) installieren:

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

Alternativ können Sie Folgendes ausführen:

* Laden Sie die oben genannte YAML-Datei herunter:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* Wenden Sie die YAML-Datei an:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Weitere Beispiele
In dieser [Anleitung mit Vorgehensweisen](ingress-controller-expose-service-over-http-https.md) finden Sie weitere Beispiele für das Bereitstellen eines AKS-Diensts im Internet über HTTP oder HTTPS mit Application Gateway.