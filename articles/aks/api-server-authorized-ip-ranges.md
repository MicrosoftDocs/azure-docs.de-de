---
title: Vom API-Server autorisierte IP-Adressbereiche in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie Ihre Cluster durch Verwendung von IP-Adressbereichen für den Zugriff auf den API-Server in Azure Kubernetes Service (AKS) schützen.
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: ca6e1c06b3ad90ef12c9bf375bae50d46c5f7c37
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98890634"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Sicherer Zugriff auf den API-Server mit autorisierten IP-Adressbereichen in Azure Kubernetes Service (AKS)

In Kubernetes empfängt der API-Server Anforderungen zum Ausführen von Aktionen im Cluster wie z.B. das Erstellen von Ressourcen oder Skalieren der Anzahl der Knoten. Die API-Server ist die zentrale Möglichkeit zum Interagieren mit einem Cluster und seiner Verwaltung. Um die Clustersicherheit zu verbessern und Angriffe zu minimieren, sollte der Zugriff auf den API-Server nur über eine begrenzte Anzahl von IP-Adressbereichen möglich sein.

Dieser Artikel veranschaulicht die Verwendung der vom API-Server autorisierten IP-Adressbereiche. Diese werden zur Begrenzung der IP-Adressen und CIDRs verwendet, die Zugriff auf die Steuerungsebene erhalten.

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Artikel wird beschrieben, wie Sie einen AKS-Cluster über die Azure-Befehlszeilenschnittstelle erstellen.

Azure CLI-Version 2.0.76 oder höher muss installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

### <a name="limitations"></a>Einschränkungen

Für die Funktion „vom API-Server autorisierte IP-Adressbereiche“ gelten die folgenden Einschränkungen:
- In Clustern, die nach Beendigung der Vorschauphase (Oktober 2019) für vom API-Server autorisierte IP-Adressbereiche erstellt wurden, werden vom API-Server autorisierte IP-Adressbereiche nur für den Lastenausgleich mit der *Standard*-SKU unterstützt. Bestehende Cluster mit einem Lastenausgleich mit *Basic*-SKU und den von API-Servern autorisierten IP-Adressbereichen bleiben weiterhin funktionsfähig. Sie können jedoch nicht zu einem Lastenausgleich mit *Standard*-SKU migriert werden. Diese vorhandenen Cluster funktionieren auch dann weiter, wenn ihre Kubernetes-Version oder die Steuerungsebene aktualisiert werden. Vom API-Server autorisierte IP-Adressbereiche werden für private Cluster nicht unterstützt.
- Diese Funktion ist nicht kompatibel mit Clustern, die die [Previewfunktion „Öffentliche IP-Adresse pro Knoten“-Knotenpools](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools-preview) verwenden.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Übersicht über vom API-Server autorisierte IP-Adressbereiche

Die zugrunde liegenden Kubernetes-APIs werden auf dem Kubernetes-API-Server verfügbar gemacht. Diese Komponente ermöglicht die Interaktion für Verwaltungstools, z.B. `kubectl` oder das Kubernetes-Dashboard. AKS stellt eine Clustersteuerungsebene für Einzelmandanten mit einem dedizierten API-Server bereit. Standardmäßig wird dem API-Server eine öffentliche IP-Adresse zugewiesen, und Sie sollten den Zugriff mithilfe der rollenbasierten Kubernetes-Zugriffssteuerung (Kubernetes RBAC, Role-Based Access Control) steuern.

Zum Absichern des Zugriffs auf AKS-Steuerungsebene/API-Server, die andernfalls öffentlich zugänglich sind, können Sie autorisierte IP-Adressbereiche aktivieren und verwenden. Diese autorisierten IP-Adressbereiche erlauben nur definierten IP-Adressbereichen die Kommunikation mit dem API-Server. Eine Anforderung an den API-Server von einer IP-Adresse, die nicht Teil dieser autorisierten IP-Adressbereiche ist, wird blockiert. Verwenden Sie weiterhin Kubernetes RBAC oder Azure RBAC, um Benutzer und die von ihnen angeforderten Aktionen zu autorisieren.

Weitere Informationen zum API-Server und anderen Clusterkomponenten finden Sie unter [Grundlegende Kubernetes-Konzepte für Azure Kubernetes Service (AKS)][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Erstellen eines AKS-Clusters mit aktivierten vom API-Server autorisierten IP-Adressbereichen

Erstellen Sie mithilfe von [az aks create][az-aks-create] einen Cluster, und geben Sie den Parameter *`--api-server-authorized-ip-ranges`* an, um eine Liste mit autorisierten IP-Adressbereichen bereitzustellen. Dabei handelt es sich in der Regel um IP-Adressbereiche, die von lokalen Netzwerken oder öffentlichen IP-Adressen verwendet werden. Wenn Sie einen CIDR-Bereich angeben, beginnen Sie mit der ersten IP-Adresse im Bereich. *137.117.106.90/29* ist z.B. ein gültiger Bereich, aber stellen Sie sicher, dass Sie die erste IP-Adresse im Bereich angeben, z.B. *137.117.106.88/29*.

> [!IMPORTANT]
> Der Cluster verwendet standardmäßig den [Lastenausgleich mit einer Standard-SKU][standard-sku-lb], den Sie zum Konfigurieren des Ausgangsgateways verwenden können. Wenn Sie vom API-Server autorisierte IP-Adressbereiche bei der Clustererstellung aktivieren, wird neben den von Ihnen angegebenen Bereichen standardmäßig auch die öffentliche IP-Adresse für Ihren Cluster zugelassen. Wenn Sie *""* oder keinen Wert für *`--api-server-authorized-ip-ranges`* angeben, werden vom API-Server autorisierte IP-Adressbereiche deaktiviert. Beachten Sie Folgendes: Geben Sie bei Verwendung von PowerShell *`--api-server-authorized-ip-ranges=""`* (mit Gleichheitszeichen) ein, um Analysefehler zu vermeiden.

Im folgenden Beispiel wird in der Ressourcengruppe *myResourceGroup* ein Cluster namens *myAKSCluster* mit einem einzelnen Knoten und mit aktivierten vom API-Server autorisierten IP-Adressbereichen erstellt. Dabei werden die IP-Adressbereiche *73.140.245.0/24* zugelassen:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> Sie sollten diese Bereiche einer Zulassungsliste hinzufügen:
> - Öffentliche IP-Adresse der Firewall
> - Alle Bereiche mit Netzwerken, von denen aus Sie den Cluster verwalten
> - Wenn Sie in Ihrem AKS-Cluster Azure Dev Spaces verwenden, müssen Sie [zusätzliche Bereiche (auf der Grundlage Ihrer Region)][dev-spaces-ranges] zulassen.
>
> Die Obergrenze für die Anzahl von IP-Adressbereichen, die Sie angeben können, ist „200“.
>
> Die Verteilung der Regeln kann bis zu zwei Minuten dauern. Berücksichtigen Sie dies beim Testen der Verbindung.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Angeben der ausgehenden IP-Adressen für den Lastenausgleich mit Standard-SKU

Wenn Sie beim Erstellen eines AKS-Clusters die ausgehenden IP-Adressen oder -Präfixe für den Cluster angeben, werden diese Adressen oder Präfixe ebenfalls zugelassen. Beispiel:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

Im obigen Beispiel sind alle im Parameter *`--load-balancer-outbound-ip-prefixes`* angegebenen IP-Adressen zulässig, ebenso wie die IP-Adressen im Parameter *`--api-server-authorized-ip-ranges`* .

Stattdessen können Sie den Parameter *`--load-balancer-outbound-ip-prefixes`* angeben, um Präfixe für ausgehende IP-Adressen des Lastenausgleichs zuzulassen.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Ausschließliches Zulassen der ausgehenden öffentlichen IP-Adresse des Lastenausgleichs mit Standard-SKU

Wenn Sie vom API-Server autorisierte IP-Adressbereiche bei der Clustererstellung aktivieren, wird neben den von Ihnen angegebenen Bereichen standardmäßig auch die ausgehende öffentliche IP-Adresse des Lastenausgleichs mit Standard-SKU für Ihren Cluster zugelassen. Soll nur die ausgehende öffentliche IP-Adresse des Lastenausgleichs mit Standard-SKU zugelassen werden, verwenden Sie *0.0.0.0/32*, wenn Sie den Parameter *`--api-server-authorized-ip-ranges`* angeben.

Im folgenden Beispiel wird nur die ausgehende öffentliche IP-Adresse des Lastenausgleichs mit Standard-SKU zugelassen, und auf den API-Server kann nur über die Knoten innerhalb des Clusters zugegriffen werden.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Aktualisieren der vom API-Server autorisierten IP-Adressbereiche eines Clusters

Wenn Sie die vom API-Server autorisierten IP-Adressbereiche für einen vorhandenen Cluster aktualisieren möchten, verwenden Sie den Befehl [az aks update][az-aks-update] mit den Parametern *`--api-server-authorized-ip-ranges`* , *--load-balancer-outbound-ip-prefixes *, *`--load-balancer-outbound-ips`* oder *--load-balancer-outbound-ip-prefixes*.

Im folgenden Beispiel werden vom API-Server autorisierte IP-Adressbereiche für den Cluster namens *myAKSCluster* in der Ressourcengruppe *myResourceGroup* aktualisiert. Der zu autorisierende IP-Adressbereich lautet *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Sie können auch *0.0.0.0/32* verwenden, wenn Sie den Parameter *`--api-server-authorized-ip-ranges`* angeben, um nur die öffentliche IP-Adresse des Lastenausgleichs mit Standard-SKU zuzulassen.

## <a name="disable-authorized-ip-ranges"></a>Deaktivieren autorisierter IP-Adressbereiche

Um autorisierte IP-Adressbereiche zu deaktivieren, geben Sie mit [az aks update][az-aks-update] einen leeren Adressbereich an, um die autorisierten IP-Adressbereiche des API-Servers zu deaktivieren. Beispiel:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="find-existing-authorized-ip-ranges"></a>Ermitteln vorhandener autorisierter IP-Adressbereiche

Zum Ermitteln autorisierter IP-Adressbereiche verwenden Sie [az aks show][az-aks-show] und geben den Clusternamen und die Ressourcengruppe an. Beispiel:

```azurecli-interactive
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query apiServerAccessProfile.authorizedIpRanges'
```

## <a name="update-disable-and-find-authorized-ip-ranges-using-azure-portal"></a>Aktualisieren, Deaktivieren und Ermitteln von autorisierten IP-Adressbereichen über das Azure-Portal

Die obigen Vorgänge zum Hinzufügen, Aktualisieren, Ermitteln und Deaktivieren von autorisierten IP-Adressbereichen können auch über das Azure-Portal ausgeführt werden. Für den Zugriff wechseln Sie auf dem Blatt der Clusterressource unter **Einstellungen** zu **Netzwerk**.

:::image type="content" source="media/api-server-authorized-ip-ranges/ip-ranges-specified.PNG" alt-text="Die Azure-Portalseite mit den Netzwerkeinstellungen der Clusterressource in einem Browser. Die Option zum Festlegen eines IP-Adressbereichs sowie die festgelegten IP-Adressbereiche sind hervorgehoben.":::

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>Wie finde ich meine in `--api-server-authorized-ip-ranges` aufzunehmende IP-Adresse?

Sie müssen Ihre Entwicklungscomputer, Tool- oder Automatisierungs-IP-Adressen der AKS-Clusterliste der genehmigten IP-Adressbereiche hinzufügen, um von dort aus auf den API-Server zugreifen zu können. 

Eine andere Möglichkeit besteht darin, in einem separaten Subnetz innerhalb des virtuellen Netzwerks von Azure Firewall eine Jumpbox mit den erforderlichen Tools zu konfigurieren. Dies setzt voraus, dass Ihre Umgebung über eine Firewall mit dem jeweiligen Netzwerk verfügt, und dass Sie die Firewall-IPs zu autorisierten Bereichen hinzugefügt haben. Wenn Tunneln aus dem AKS-Subnetz in das Firewallsubnetz erzwungen wurde, kann sich die Jumpbox ebenfalls im Clustersubnetz befinden.

Fügen Sie mit dem folgenden Befehl dem Bereich der genehmigten IP-Adressen eine weitere Adresse hinzu.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

>> [!NOTE]
> Im obigen Beispiel werden die vom API-Server autorisierten IP-Adressbereiche an den Cluster angefügt. Um autorisierte IP-Adressbereiche zu deaktivieren, verwenden Sie „az aks update“, und geben Sie einen leeren Adressbereich „“ an. 

Eine weitere Möglichkeit besteht darin, den folgenden Befehl auf Windows-Systemen zu verwenden, um die öffentliche IPv4-Adresse abzurufen, oder Sie können die Schritte in [Ermitteln Ihrer IP-Adresse](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address) verwenden.

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

Sie finden diese Adresse auch, indem Sie in einem Internetbrowser nach „Wie lautet meine IP-Adresse“ suchen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie vom API-Server autorisierte IP-Adressbereiche aktiviert. Dieser Ansatz ist ein Aspekt Ihrer sicheren Ausführung eines AKS-Clusters.

Weitere Informationen finden Sie unter [Sicherheitskonzepte für Anwendungen und Cluster in Azure Kubernetes Service (AKS)][concepts-security] und [Best Practices für Clustersicherheit und Upgrades in Azure Kubernetes Service (AKS)][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
