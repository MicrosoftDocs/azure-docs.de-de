---
title: 'Tutorial: Aktivieren des Eingangscontroller-Add-ons für einen vorhandenen AKS-Cluster mit vorhandenem Azure Application Gateway'
description: Anhand dieses Tutorials können Sie das Eingangscontroller-Add-On für Ihren vorhandenen AKS-Cluster mit einem vorhandenen Application Gateway aktivieren.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: caya
ms.openlocfilehash: d491b714c7d553fbd89d72315f46e6927d437717
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593811"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway-through-azure-cli-preview"></a>Tutorial: Aktivieren des Application Gateway-Eingangscontroller-Add-Ons für einen vorhandenen AKS-Cluster mit einem vorhandenen Application Gateway über die Azure CLI (Vorschau)

Mit der Azure CLI können Sie das Add-On [Application Gateway-Eingangscontroller (Application Gateway Ingress Controller, AGIC)](ingress-controller-overview.md) (derzeit in der Vorschauphase) für einen Cluster für [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/) aktivieren. In diesem Tutorial erfahren Sie, wie Sie Ihre Kubernetes-Anwendung mit dem AGIC-Add-On über einen vorhandenen Application Gateway in einem vorhandenen AKS-Cluster verfügbar machen, die in separaten virtuellen Netzwerken bereitgestellt werden. Zunächst erstellen Sie einen AKS-Cluster in einem virtuellen Netzwerk und ein Application Gateway in einem anderen virtuellen Netzwerk, um vorhandene Ressourcen zu simulieren. Anschließend aktivieren Sie das AGIC-Add-On, fassen die beiden virtuellen Netzwerke zusammen und stellen eine Beispielanwendung bereit, die über das Application Gateway mithilfe des AGIC-Add-Ons verfügbar gemacht wird. Wenn Sie das AGIC-Add-On für ein vorhandenes Application Gateway und einen vorhandenen AKS-Cluster im gleichen virtuellen Netzwerk aktivieren, können Sie den Schritt zum Peering weiter unten überspringen. Das Add-On ermöglicht eine viel schnellere Bereitstellung von AGIC für Ihren AKS-Cluster als das [bisherige Verfahren mit Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) und bietet außerdem umfassende Verwaltungsfunktionen.  

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Ressourcengruppe 
> * Erstellen eines neuen AKS-Clusters 
> * Erstellen eines neuen Application Gateway 
> * Aktivieren des AGIC-Add-Ons im vorhandenen AKS-Cluster mithilfe des vorhandenen Application Gateway 
> * Peering der virtuellen Netzwerke mit dem Application Gateway und dem AKS-Cluster
> * Bereitstellen einer Beispielanwendung mit AGIC für eingehenden Datenverkehr auf dem AKS-Cluster
> * Überprüfen der Erreichbarkeit der Anwendung über das Application Gateway

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0.4 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

 - Registrieren Sie das Featureflag *AKS-IngressApplicationGatewayAddon*, indem Sie den Befehl [az feature register](/cli/azure/feature#az-feature-register) verwenden, wie im folgenden Beispiel gezeigt. Sie müssen dies nur einmal pro Abonnement durchführen, während sich das Add-On noch in der Vorschauphase befindet:
     ```azurecli-interactive
     az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
     ```
    Es dauert einige Minuten, bis der Status „Registriert“ angezeigt wird. Sie können den Registrierungsstatus mithilfe des Befehls [az feature list](/cli/azure/feature#az-feature-register) überprüfen:
     ```azurecli-interactive
     az feature list -o table --query "[?contains(name, 'microsoft.containerservice/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
     ```

 - Wenn Sie so weit sind, aktualisieren Sie mithilfe des Befehls [az provider register](/cli/azure/provider#az-provider-register) die Registrierung des Microsoft.ContainerService-Ressourcenanbieters:
    ```azurecli-interactive
    az provider register --namespace Microsoft.ContainerService
    ```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

In Azure ordnen Sie verwandte Ressourcen einer Ressourcengruppe zu. Erstellen Sie mit [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *canadacentral* (Region) erstellt. 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Bereitstellen eines neuen AKS-Clusters

Sie stellen nun einen neuen AKS-Cluster bereit, um einen vorhandenen AKS-Cluster zu simulieren, für den Sie das AGIC-Add-On aktivieren möchten.  

Im folgenden Beispiel stellen Sie einen AKS-Cluster mit dem Namen *myCluster* über [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) und [Verwaltete Identitäten](../aks/use-managed-identity.md) in der von Ihnen erstellten Ressourcengruppe *myResourceGroup* bereit.    

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Um zusätzliche Parameter für den Befehl `az aks create` zu konfigurieren, lesen Sie die Referenzen [hier](/cli/azure/aks#az-aks-create). 

## <a name="deploy-a-new-application-gateway"></a>Bereitstellen eines neuen Application Gateway 

Sie stellen nun ein neues Application Gateway bereit, um ein vorhandenes Application Gateway zu simulieren, das Sie für den Lastenausgleich des Datenverkehrs zum AKS-Cluster *myCluster* verwenden möchten. Das Application Gateway erhält den Namen *myApplicationGateway*, Sie müssen jedoch zunächst eine Ressource mit öffentlicher IP-Adresse und dem Namen *myPublicIp* und ein neues virtuelles Netzwerk mit dem Namen *myVnet* und dem Adressraum 11.0.0.0/8 erstellen sowie ein Subnetz mit dem Adressraum 11.1.0.0/16 und dem Namen *mySubnet*. Stellen Sie dann das Application Gateway in *mySubnet* mithilfe von *myPublicIp* bereit. 

Wenn Sie ein AKS-Cluster und ein Application Gateway in separaten virtuellen Netzwerken verwenden, dürfen sich die Adressräume der beiden virtuellen Netzwerke nicht überlappen. Der Standardadressraum, in dem ein AKS-Cluster bereitgestellt wird, ist 10.0.0.0/8. Daher legen Sie das Adresspräfix für das virtuelle Netzwerk mit dem Application Gateway auf 11.0.0.0/8 fest. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Das AGIC-Add-On (Application Gateway Ingress Controller, Application Gateway-Eingangscontroller) unterstützt **nur** Application Gateway v2-SKUs (Standard und WAF) und **keine** Application Gateway v1-SKUs. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-with-existing-application-gateway"></a>Aktivieren des AGIC-Add-Ons im vorhandenen AKS-Cluster mithilfe des vorhandenen Application Gateway 

Nun aktivieren Sie das AGIC-Add-On in dem von Ihnen erstellten AKS-Cluster *myCluster* und geben an, dass das AGIC-Add-On das vorhandene Application Gateway *myApplicationGateway* verwenden soll, das Sie zuvor erstellt haben. Vergewissern Sie sich, dass Sie am Anfang dieses Tutorials die aks-preview-Erweiterung hinzugefügt/aktualisiert haben. 

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="peer-the-two-virtual-networks-together"></a>Peering zwischen den beiden virtuellen Netzwerken

Da der AKS-Cluster in einem eigenen virtuellen Netzwerk und das Application Gateway in einem anderen virtuellen Netzwerk bereitgestellt wurde, müssen Sie die beiden virtuellen Netzwerke per Peering miteinander verknüpfen, damit Datenverkehr vom Application Gateway zu den Pods im Cluster fließt. Für das Peering der beiden virtuellen Netzwerke muss der Azure CLI-Befehl zweimal ausgeführt werden, um sicherzustellen, dass die Verbindung bidirektional ist. Mit dem ersten Befehl wird eine Peeringverbindung vom virtuellen Netzwerk mit dem Application Gateway zum virtuellen AKS-Netzwerk erstellt. Mit dem zweiten Befehl wird eine Peeringverbindung in umgekehrter Richtung erstellt. 

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```
## <a name="deploy-a-sample-application-using-agic"></a>Bereitstellen einer Beispielanwendung mit AGIC 

Nun stellen Sie eine Beispielanwendung im erstellten AKS-Cluster bereit, die das AGIC-Add-On für den eingehenden Datenverkehr verwendet, und verbinden das Application Gateway mit dem AKS-Cluster. Zuerst rufen Sie die Anmeldeinformationen für den von Ihnen bereitgestellten AKS-Cluster ab, indem Sie den Befehl `az aks get-credentials` ausführen. 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Sobald Sie über die Anmeldeinformationen für den von Ihnen erstellten Cluster verfügen, führen Sie den folgenden Befehl aus, um eine Beispielanwendung einzurichten, die AGIC für beim Cluster eingehenden Datenverkehr verwendet. AGIC aktualisiert das Application Gateway, das Sie zuvor eingerichtet haben, mit den entsprechenden Routingregeln für die neue Beispielanwendung, die Sie bereitgestellt haben.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Überprüfen der Erreichbarkeit der Anwendung

Nachdem das Application Gateway für den Datenverkehr zum AKS-Cluster eingerichtet wurde, überprüfen Sie, ob Ihre Anwendung erreichbar ist. Rufen Sie zunächst die IP-Adresse des eingehenden Datenverkehrs ab. 

```azurecli-interactive
kubectl get ingress
```

Überprüfen Sie, ob die von Ihnen erstellte Beispielanwendung ausgeführt wird, indem Sie die IP-Adresse des Application Gateway aufrufen, die Sie mit dem Befehl oben abgerufen haben, oder verwenden Sie `curl`. Es kann eine Minute dauern, bis das Application Gateway aktualisiert wurde. Wenn der Status des Application Gateway im Portal also noch „Aktualisieren“ lautet, warten Sie, bis der Vorgang abgeschlossen ist, bevor Sie versuchen, die IP-Adresse zu erreichen. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Entfernen Sie die Ressourcengruppe, das Anwendungsgateway und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zum Deaktivieren des AGIC-Add-Ons](./ingress-controller-disable-addon.md)
