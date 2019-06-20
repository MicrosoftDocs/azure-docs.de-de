---
title: Vom API-Server autorisierte IP-Adressbereiche in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie Ihre Cluster durch Verwendung von IP-Adressbereichen für den Zugriff auf den API-Server in Azure Kubernetes Service (AKS) schützen.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: 185c16e76094fe55a54fb17bef24fcd03d7b54f0
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475155"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Vorschau – sicherer Zugriff auf den API-Server mit autorisierten IP-Adressbereichen in Azure Kubernetes Service (AKS)

In Kubernetes empfängt der API-Server Anforderungen zum Ausführen von Aktionen im Cluster wie z.B. das Erstellen von Ressourcen oder Skalieren der Anzahl der Knoten. Die API-Server ist die zentrale Möglichkeit zum Interagieren mit einem Cluster und seiner Verwaltung. Um die Clustersicherheit zu verbessern und Angriffe zu minimieren, sollte der Zugriff auf den API-Server nur über eine begrenzte Anzahl von IP-Adressbereichen möglich sein.

Dieser Artikel veranschaulicht die Verwendung vom API-Server autorisierter IP-Adressbereiche zur Begrenzung von Anforderungen an die Steuerungsebene. Diese Funktion steht derzeit als Vorschau zur Verfügung.

> [!IMPORTANT]
> AKS-Previewfunktionen stehen gemäß dem Self-Service- und Aktivierungsprinzip zur Verfügung. Sie werden zum Sammeln von Feedback und Fehlern mithilfe unserer Community bereitgestellt. In der Vorschauversion sind diese Features nicht für den Einsatz in der Produktion vorgesehen. Features in der öffentlichen Vorschau unterliegen dem Prinzip des „bestmöglichen Supports“. Unterstützung durch die Teams für den technischen AKS-Support steht nur während der Geschäftszeiten in der Zeitzone „Pacific Standard Time“ (PST) zur Verfügung. Weitere Informationen hierzu finden Sie in den folgenden Supportartikeln:
>
> * [Unterstützungsrichtlinien für Azure Kubernetes Service][aks-support-policies]
> * [Häufig gestellte Fragen zum Azure-Support][aks-faq]

## <a name="before-you-begin"></a>Voraussetzungen

Vom API-Server autorisierte IP-Adressbereiche funktionieren nur für neue AKS-Cluster, die Sie erstellen. In diesem Artikel wird beschrieben, wie Sie einen AKS-Cluster über die Azure-Befehlszeilenschnittstelle erstellen.

Azure CLI-Version 2.0.61 oder höher muss installiert und konfiguriert sein. Führen Sie  `az --version` aus, um die Version zu ermitteln. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie weitere Informationen unter [Installieren der Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installieren der CLI-Erweiterung „aks-preview“

Die CLI-Befehle zum Konfigurieren der vom API-Server autorisierten IP-Adressbereiche sind in der *aks-preview*-CLI-Erweiterung verfügbar. Installieren Sie die Azure CLI-Erweiterung *aks-preview* mit dem Befehl [az extension add][az-extension-add], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Wenn Sie zuvor die Erweiterung *aks-preview* installiert haben, installieren Sie alle verfügbaren Updates mit dem Befehl `az extension update --name aks-preview`.

### <a name="register-feature-flag-for-your-subscription"></a>Registrieren des Featureflags für Ihr Abonnement

Um vom API-Server autorisierte IP-Adressbereiche verwenden zu können, müssen Sie zuerst ein Featureflag in Ihrem Abonnement aktivieren. Verwenden Sie den Befehl [az feature register][az-feature-register] wie im folgenden Beispiel gezeigt, um das Featureflag *APIServerSecurityPreview* zu registrieren:

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Sie können den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list] überprüfen:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

Wenn Sie fertig sind, aktualisieren Sie die Registrierung des *Microsoft.ContainerService*-Ressourcenanbieters mit dem Befehl [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Einschränkungen

Wenn Sie vom API-Server autorisierte IP-Adressbereiche konfigurieren, gelten die folgenden Einschränkungen:

* Derzeit können Sie Azure Dev Spaces nicht verwenden, da die Kommunikation mit dem API-Server auch blockiert ist.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Übersicht über vom API-Server autorisierte IP-Adressbereiche

Die zugrunde liegenden Kubernetes-APIs werden auf dem Kubernetes-API-Server verfügbar gemacht. Diese Komponente ermöglicht die Interaktion für Verwaltungstools, z.B. `kubectl` oder das Kubernetes-Dashboard. AKS stellt einen Clustermaster mit Einzelmandant mit einem dedizierten API-Server bereit. Standardmäßig wird der API-Server einer öffentlichen IP-Adresse zugewiesen, und Sie sollten den Zugriff mithilfe der rollenbasierten Zugriffssteuerung (RBAC, Role-based Access Control) steuern.

Zum Absichern des Zugriffs auf AKS-Steuerungsebene/API-Server, die andernfalls öffentlich zugänglich sind, können Sie autorisierte IP-Adressbereiche aktivieren und verwenden. Diese autorisierten IP-Adressbereiche erlauben nur definierten IP-Adressbereichen die Kommunikation mit dem API-Server. Eine Anforderung einer IP-Adresse, die nicht Teil dieser autorisierten IP-Adressbereiche ist, an den API-Server wird blockiert. Sie sollten weiterhin RBAC verwenden, um anschließend Benutzer und die Aktionen, die sie anfordern, zu autorisieren.

Weitere Informationen zum API-Server und anderen Clusterkomponenten finden Sie unter [Grundlegende Kubernetes-Konzepte für Azure Kubernetes Service (AKS)][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Erstellen eines AKS-Clusters

Vom API-Server autorisierte IP-Adressbereiche funktionieren nur für neue AKS-Cluster. Sie können autorisierte IP-Adressbereiche nicht im Rahmen der Clustererstellung aktivieren. Wenn Sie versuchen, autorisierte IP-Adressbereiche im Rahmen der Clustererstellung zu aktivieren, können die Clusterknoten nicht während der Bereitstellung auf den API-Server zugreifen, da die IP-Adresse für ausgehenden Datenverkehr zu diesem Zeitpunkt nicht definiert ist.

Erstellen Sie zuerst mit dem Befehl [az aks create][az-aks-create] einen Cluster. Im folgenden Beispiel wird ein Einzelknotencluster mit dem Namen *myAKSCluster* in der Ressourcengruppe mit dem Namen *myResourceGroup* erstellt.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-outbound-gateway-for-firewall-rules"></a>Erstellen eines ausgehenden Gateways für Firewallregeln

Um sicherzustellen, dass die Knoten in einem Cluster zuverlässig mit dem API-Server kommunizieren können, wenn Sie im nächsten Abschnitt autorisierte IP-Adressbereiche aktivieren, erstellen Sie eine Azure Firewall-Instanz für die Verwendung als ausgehendes Gateway. Die IP-Adresse der Azure Firewall-Instanz wird dann im nächsten Abschnitt der Liste der vom API-Server autorisierten IP-Adressen hinzugefügt.

> [!WARNING]
> Bei der Verwendung von Azure Firewall können erhebliche Kosten über einen monatlichen Abrechnungszyklus anfallen. Die Anforderung der Verwendung von Azure Firewall sollte nur in diesem anfänglichen Vorschauzeitraum erforderlich sein. Weitere Informationen und Beispiele zur Kostenplanung finden Sie unter [Azure Firewall – Preise][azure-firewall-costs].

Rufen Sie zuerst den *MC_* -Ressourcengruppennamen für den AKS-Cluster und das virtuelle Netzwerk auf. Erstellen Sie dann ein Subnetz mit dem Befehl [az network vnet subnet create][az-network-vnet-subnet-create]. Im folgenden Beispiel wird ein Subnetz namens *AzureFirewallSubnet* mit dem CIDR-Bereich *10.200.0.0/16* erstellt:

```azurecli-interactive
# Get the name of the MC_ cluster resource group
MC_RESOURCE_GROUP=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query nodeResourceGroup -o tsv)

# Get the name of the virtual network used by the cluster
VNET_NAME=$(az network vnet list \
    --resource-group $MC_RESOURCE_GROUP \
    --query [0].name -o tsv)

# Create a subnet in the virtual network for Azure Firewall
az network vnet subnet create \
    --resource-group $MC_RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name AzureFirewallSubnet \
    --address-prefixes 10.200.0.0/16
```

Installieren Sie zum Erstellen einer Azure Firewall-Instanz die *azure-firewall*-CLI-Erweiterung mithilfe des Befehls [az extension add][az-extension-add]. Erstellen Sie dann eine Firewall mithilfe des [az network firewall create] [ az-network-firewall-create]-Befehls. Im folgenden Beispiel wird eine Azure Firewall-Instanz mit dem Namen *myAzureFirewall* erstellt:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Einer Azure Firewall-Instanz wird eine öffentliche IP-Adresse zugewiesen, über die ausgehender Datenverkehr läuft. Erstellen Sie eine öffentliche Adresse mit dem [az network public-ip create][az-network-public-ip-create]-Befehl, und erstellen Sie dann eine IP-Konfiguration auf der Firewall mithilfe des [az network firewall ip-config create][az-network-firewall-ip-config-create]-Befehls, die die öffentliche IP-Adresse anwendet:

```azurecli-interactive
# Create a public IP address for the firewall
FIREWALL_PUBLIC_IP=$(az network public-ip create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallPublicIP \
    --sku Standard \
    --query publicIp.ipAddress -o tsv)

# Associated the firewall with virtual network
az network firewall ip-config create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallIPConfig \
    --vnet-name $VNET_NAME \
    --firewall-name myAzureFirewall \
    --public-ip-address myAzureFirewallPublicIP
```

Jetzt erstellen Sie die Azure Firewall-Netzwerkregel, um sämtlichen *TCP*-Datenverkehr *zuzulassen*, mit dem [az network firewall network-rule create][az-network-firewall-network-rule-create]-Befehl. Im folgenden Beispiel wird eine Netzwerkregel mit dem Namen *AllowTCPOutbound* für Datenverkehr mit einer beliebigen Quell- oder Zieladresse erstellt:

```azurecli-interactive
az network firewall network-rule create \
    --resource-group $MC_RESOURCE_GROUP \
    --firewall-name myAzureFirewall \
    --name AllowTCPOutbound \
    --collection-name myAzureFirewallCollection \
    --priority 200 \
    --action Allow \
    --protocols TCP \
    --source-addresses '*' \
    --destination-addresses '*' \
    --destination-ports '*'
```

Um die Azure Firewall-Instanz mit der Netzwerkroute zu verknüpfen, rufen Sie die vorhandenen Routentabelleninformationen, die interne IP-Adresse der Azure Firewall-Instanz und die IP-Adresse des API-Servers ab. Diese IP-Adressen werden im nächsten Abschnitt angegeben, um zu steuern, wie Datenverkehr für die Clusterkommunikation weitergeleitet werden sollte.

```azurecli-interactive
# Get the AKS cluster route table
ROUTE_TABLE=$(az network route-table list \
    --resource-group $MC_RESOURCE_GROUP \
    --query "[?contains(name,'agentpool')].name" -o tsv)

# Get internal IP address of the firewall
FIREWALL_INTERNAL_IP=$(az network firewall show \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewall \
    --query ipConfigurations[0].privateIpAddress -o tsv)

# Get the IP address of API server endpoint
K8S_ENDPOINT_IP=$(kubectl get endpoints -o=jsonpath='{.items[?(@.metadata.name == "kubernetes")].subsets[].addresses[].ip}')
```

Abschließend erstellen Sie eine Route in der vorhandenen AKS-Netzwerkroutentabelle mithilfe des [az network route-table route create][az-network-route-table-route-create]-Befehls, der dem Datenverkehr die Verwendung der Azure Firewall-Appliance für die Kommunikation mit dem API-Server erlaubt.

```azurecli-interactive
az network route-table route create \
    --resource-group $MC_RESOURCE_GROUP \
    --route-table-name $ROUTE_TABLE \
    --name AzureFirewallAPIServer \
    --address-prefix $K8S_ENDPOINT_IP/32 \
    --next-hop-ip-address $FIREWALL_INTERNAL_IP \
    --next-hop-type VirtualAppliance

echo "Public IP address for the Azure Firewall instance that should be added to the list of API server authorized addresses is:" $FIREWALL_PUBLIC_IP
```

Notieren Sie sich die öffentliche IP-Adresse Ihrer Azure Firewall-Appliance. Diese Adresse wird im nächsten Abschnitt der Liste der vom API-Server autorisierten IP-Adressbereiche hinzugefügt.

## <a name="enable-authorized-ip-ranges"></a>Aktivieren autorisierter IP-Adressbereiche

Um die vom API-Server autorisierten IP-Adressbereiche zu aktivieren, geben Sie eine Liste der autorisierten IP-Adressbereiche an. Wenn Sie einen CIDR-Bereich angeben, beginnen Sie mit der ersten IP-Adresse im Bereich. *137.117.106.90/29* ist z.B. ein gültiger Bereich, aber stellen Sie sicher, dass Sie die erste IP-Adresse im Bereich angeben, z.B. *137.117.106.88/29*.

Geben Sie mit dem [az aks update][az-aks-update]-Befehl die zulässigen *--api-server-authorized-ip-ranges* (vom API-Server autorisierten IP-Adressbereiche) an. Diese IP-Adressbereiche sind in der Regel die von Ihren lokalen Netzwerken verwendeten Adressbereiche. Fügen Sie die öffentliche IP-Adresse Ihrer eigenen Azure Firewall-Instanz hinzu, die sie im vorherigen Schritt abgerufen haben, wie z.B. *20.42.25.196/32*.

Im folgenden Beispiel werden vom API-Server autorisierte IP-Adressbereiche für den Cluster namens *myAKSCluster* in der Ressourcengruppe *myResourceGroup* aktiviert. Die zu autorisierenden IP-Adressbereiche sind *20.42.25.196/32* (die öffentliche IP-Adresse der Azure Firewall-Instanz) und dann *172.0.0.10/16* und *168.10.0.10/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.10/16,168.10.0.10/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>Aktualisieren oder Deaktivieren autorisierter IP-Adressbereiche

Zum Aktualisieren oder Deaktivieren autorisierter IP-Adressbereiche verwenden Sie erneut den [az aks update][az-aks-update]-Befehl. Geben Sie den aktualisierten CIDR-Bereich an, den Sie zulassen möchten, oder geben Sie einen leeren Bereich an, um vom API-Server autorisierte IP-Adressbereiche zu deaktivieren, wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie vom API-Server autorisierte IP-Adressbereiche aktiviert. Dieser Ansatz ist ein Aspekt Ihrer sicheren Ausführung eines AKS-Clusters.

Weitere Informationen finden Sie unter [Sicherheitskonzepte für Anwendungen und Cluster in Azure Kubernetes Service (AKS)][concepts-security] und [Best Practices für Clustersicherheit und Upgrades in Azure Kubernetes Service (AKS)][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[create-aks-sp]: kubernetes-service-principal.md#manually-create-a-service-principal
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
