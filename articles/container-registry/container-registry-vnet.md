---
title: Beschränken des Zugriffs mithilfe eines Dienstendpunkts
description: Hier erfahren Sie, wie Sie den Zugriff auf eine Azure-Containerregistrierung mithilfe eines Dienstendpunkts in einem virtuellen Azure-Netzwerk beschränken. Der Zugriff auf Dienstendpunkte ist ein Feature der Dienstebene „Premium“.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 5f9bc7c9a6c8f2061765510a6396611502fd4a2a
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026223"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Beschränken des Zugriffs auf eine Containerregistrierung mithilfe eines Dienstendpunkts in einem virtuellen Azure-Netzwerk

Das virtuelle Azure-Netzwerk ([Azure Virtual Network](../virtual-network/virtual-networks-overview.md)) stellt ein sicheres, privates Netzwerk für Ihre Azure- und lokalen Ressourcen bereit. Mit einem [Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) können Sie die öffentliche IP-Adresse Ihrer Containerregistrierung auf Ihr virtuelles Netzwerk beschränken. Dieser Endpunkt sorgt für eine optimale Datenverkehrsroute zu der Ressource über das Azure-Backbonenetzwerk. Mit jeder Anforderung wird zudem die Identität des virtuellen Netzwerks und des Subnetzes übertragen.

In diesem Artikel erfahren Sie, wie Sie einen Dienstendpunkt für die Containerregistrierung (Vorschauversion) in einem virtuellen Netzwerk konfigurieren. 

> [!IMPORTANT]
> Von Azure Container Registry wird jetzt [Azure Private Link](container-registry-private-link.md) unterstützt. Dadurch können private Endpunkte aus einem virtuellen Netzwerk in einer Registrierung platziert werden. Auf private Endpunkte kann innerhalb des virtuellen Netzwerks über private IP-Adressen zugegriffen werden. In den meisten Netzwerkszenarien empfiehlt es sich, anstelle von Dienstendpunkten private Endpunkte zu verwenden.

Ein Endpunkt für den Registrierungsdienst kann im Tarif **Premium** des Containerregistrierungsdiensts konfiguriert werden. Informationen zu den Tarifen und Einschränkungen des Registrierungsdiensts finden Sie unter [Azure Container Registry-Tarife](container-registry-skus.md).

## <a name="preview-limitations"></a>Einschränkungen der Vorschau

* Momentan ist für Azure Container Registry keine Weiterentwicklung von Dienstendpunkten geplant. Es wird empfohlen, stattdessen [private Endpunkte](container-registry-private-link.md) zu verwenden.
* Dienstendpunkte für eine Registrierung können nicht über das Azure-Portal konfiguriert werden.
* Als Host für den Zugriff auf eine Containerregistrierung mit einem Dienstendpunkt kann nur ein [Azure Kubernetes Service](../aks/intro-kubernetes.md)-Cluster oder ein [virtueller Azure-Computer](../virtual-machines/linux/overview.md) verwendet werden. *Andere Azure-Dienste (einschließlich Azure Container Instances) werden nicht unterstützt.*
* Pro Registrierung werden maximal 100 Netzwerkzugriffsregeln unterstützt.
* Dienstendpunkte für Azure Container Registry werden in der Azure US Government-Cloud oder in der Azure China-Cloud nicht unterstützt.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Um in diesem Artikel die Schritte an der Azure-Befehlszeilenschnittstelle (CLI) ausführen zu können, ist Azure CLI-Version 2.0.58 oder höher erforderlich. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli].

* Sollten Sie noch nicht über eine Containerregistrierung verfügen, können Sie eine Registrierung erstellen (Premium-Tarif erforderlich) und ein Beispielimage (etwa `hello-world`) aus Docker Hub pushen. Zum Erstellen einer Registrierung können Sie das [Azure-Portal][quickstart-portal] oder die [Azure-Befehlszeilenschnittstelle][quickstart-cli] verwenden. 

* Wenn Sie den Registrierungszugriff mithilfe eines Dienstendpunkts in einem anderen Azure-Abonnement einschränken möchten, müssen Sie den Ressourcenanbieter für Azure Container Registry in diesem Abonnement registrieren. Beispiel:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>Konfigurieren des Netzwerkzugriffs für die Registrierung

In diesem Abschnitt konfigurieren Sie Ihre Containerregistrierung für den Zugriff aus einem Subnetz in einem virtuellen Azure-Netzwerk. Es werden Schritte für die Verwendung der Azure CLI bereitgestellt.

### <a name="add-a-service-endpoint-to-a-subnet"></a>Hinzufügen eines Dienstendpunkts zu einem Subnetz

Wenn Sie einen virtuellen Computer erstellen, erstellt Azure standardmäßig in der gleichen Ressourcengruppe ein virtuelles Netzwerk. Der Name des virtuellen Netzwerks hängt von dem Namen des virtuellen Computers ab. Wenn Sie Ihren virtuellen Computer beispielsweise *MyDockerVM* nennen, lautet der Standardname des virtuellen Netzwerks *MyDockerVMVNET* mit einem Subnetz namens *MyDockerVMSubnet*. Dies können Sie mithilfe des Befehls [az network vnet list][az-network-vnet-list] überprüfen:

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
```

Ausgabe:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Verwenden Sie den Befehl [az network vnet subnet update][az-network-vnet-subnet-update], um Ihrem Subnetz einen **Microsoft.ContainerRegistry** -Dienstendpunkt hinzuzufügen. Ersetzen Sie im folgenden Befehl die Werte durch die Namen Ihres virtuellen Netzwerks und Ihres Subnetzes:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Rufen Sie mit dem Befehl [az network vnet subnet show][az-network-vnet-subnet-show] die Ressourcen-ID des Subnetzes ab. Diese benötigen Sie in einem späteren Schritt zum Konfigurieren einer Netzwerkzugriffsregel.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Ausgabe:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="change-default-network-access-to-registry"></a>Ändern des Standardnetzwerkzugriffs auf die Registrierung

Eine Azure-Containerregistrierung lässt standardmäßig Verbindungen von Hosts in beliebigen Netzwerken zu. Um den Zugriff auf ein ausgewähltes Netzwerk zu beschränken, ändern Sie die Standardaktion so, dass der Zugriff verweigert wird. Ersetzen Sie im folgenden Befehl [az acr update][az-acr-update] den Wert durch den Namen Ihrer Registrierung:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Hinzufügen einer Netzwerkregel zur Registrierung

Fügen Sie mit dem Befehl [az acr network-rule add][az-acr-network-rule-add] Ihrer Registrierung eine Netzwerkregel hinzu, die den Zugriff vom Subnetz des virtuellen Computers erlaubt. Ersetzen Sie im folgenden Befehl die Werte durch den Namen der Containerregistrierung und die Ressourcen-ID des Subnetzes: 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>Prüfen des Zugriffs auf die Registrierung

Warten Sie einige Minuten, bis die Konfiguration aktualisiert wurde, und prüfen Sie dann, ob der virtuelle Computer auf die Containerregistrierung zugreifen kann. Stellen Sie eine SSH-Verbindung mit Ihrem virtuellen Computer her, und melden Sie sich mit dem Befehl [az acr login][az-acr-login] bei der Registrierung an. 

```bash
az acr login --name mycontainerregistry
```

Sie können Registerierungsvorgänge ausführen (z.B. `docker pull`), um mittels Pullvorgang ein Beispielimage aus der Registrierung abzurufen. Ersetzen Sie im Befehl die Werte durch den Image- und Tagwert, der Ihrer Registrierung entspricht, und verwenden Sie als Präfix den Namens des Anmeldeservers für die Registrierung (nur Kleinbuchstaben):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker ruft erfolgreich mittels Pullvorgang das Image auf den virtuellen Computer ab.

Dieses Beispiel zeigt, dass Sie über die Netzwerkzugriffsregel auf die private Containerregistrierung zugreifen können. Auf die Containerregistrierung kann jedoch nicht über einen Anmeldehost zugegriffen werden, für den keine Netzwerkzugriffsregel konfiguriert wurde. Wenn Sie versuchen, sich mit dem Befehl `az acr login` oder `docker login` von einem anderen Host anzumelden, sieht die Ausgabe in etwa wie folgt aus:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Wiederherstellen des Standardzugriffs auf die Registrierung

Zum Wiederherstellen des standardmäßigen Zugriffs auf die Registrierung müssen Sie alle konfigurierten Netzwerkregeln entfernen. Stellen Sie dann „Zugriff zulassen“ wieder als Standardaktion ein. 

### <a name="remove-network-rules"></a>Entfernen von Netzwerkregeln

Um eine Liste der Netzwerkregeln anzuzeigen, die für Ihre Registrierung konfiguriert wurden, führen Sie den folgenden Befehl [az acr network-rule list][az-acr-network-rule-list] aus:

```azurecli
az acr network-rule list --name mycontainerregistry 
```

Führen Sie für jede konfigurierte Regel den Befehl [az acr network-rule remove][az-acr-network-rule-remove] aus, um die Regel zu entfernen. Beispiel:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="allow-access"></a>Zugriff zulassen

Ersetzen Sie im folgenden Befehl [az acr update][az-acr-update] den Wert durch den Namen Ihrer Registrierung:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie alle Azure-Ressourcen in der gleichen Ressourcengruppe erstellt haben und sie nicht mehr benötigen, können Sie die Ressourcen optional mit einem einzigen Befehl [az group delete](/cli/azure/group) löschen:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Einschränken des Zugriffs auf eine Registrierung mithilfe eines privaten Endpunkts in einem virtuellen Netzwerk finden Sie unter [Konfigurieren von Azure Private Link für eine Azure-Container Containerregistrierung](container-registry-private-link.md).
* Wenn Sie Zugriffsregeln für die Registrierung hinter einer Clientfirewall einrichten müssen, finden Sie Informationen hierzu unter [Konfigurieren von Regeln für den Zugriff auf eine Azure-Containerregistrierung hinter einer Firewall](container-registry-firewall-access-rules.md).


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
