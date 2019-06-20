---
title: Bereitstellen von Containerinstanzen in einem virtuellen Azure-Netzwerk
description: Erfahren Sie, wie Sie Containergruppen in einem neuen oder vorhandenen virtuellen Azure-Netzwerk bereitstellen.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 25f9d4e02bcb354acf1c771157622f07c5f4bcc1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64712805"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Bereitstellen von Containerinstanzen in einem virtuellen Azure-Netzwerk

Das virtuelle Azure-Netzwerk ([Azure Virtual Network](../virtual-network/virtual-networks-overview.md)) stellt ein sicheres, privates Netzwerk für Ihre Azure- und lokalen Ressourcen bereit. Durch die Bereitstellung von Containergruppen in einem virtuellen Azure-Netzwerk können Ihre Container sicher mit anderen Ressourcen im virtuellen Netzwerk kommunizieren.

Containergruppen, die in einem virtuellen Azure-Netzwerk bereitgestellt werden, ermöglichen folgende Szenarien:

* Direkte Kommunikation zwischen Containergruppen im gleichen Subnetz
* Senden einer [taskbasierten](container-instances-restart-policy.md) Workloadausgabe von Containerinstanzen an eine Datenbank im virtuellen Netzwerk
* Abrufen des Inhalts für Containerinstanzen von einem [Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) im virtuellen Netzwerk
* Containerkommunikation mit virtuellen Computern im virtuellen Netzwerk
* Containerkommunikation mit lokalen Ressourcen über ein [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) oder [ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Dieses Feature befindet sich derzeit in der Vorschauphase. Es gelten einige [Einschränkungen](#preview-limitations). Wenn Sie Vorschauversionen nutzen möchten, müssen Sie die [zusätzlichen Nutzungsbedingungen][terms-of-use] akzeptieren. Einige Aspekte dieses Features werden bis zur allgemeinen Verfügbarkeit unter Umständen noch geändert.

## <a name="virtual-network-deployment-limitations"></a>Einschränkungen für die Bereitstellung in einem virtuellen Netzwerk

Es gelten bestimmte Einschränkungen, wenn Sie Containergruppen für ein virtuelles Netzwerk bereitstellen.

* Um Containergruppen in einem Subnetz bereitstellen zu können, darf das Subnetz keine anderen Ressourcentypen enthalten. Entfernen Sie alle vorhandenen Ressourcen aus einem vorhandenen Subnetz, bevor Sie Containergruppen für dieses bereitstellen, oder erstellen Sie ein neues Subnetz.
* Sie können keine [verwaltete Identität](container-instances-managed-identity.md) in einer Containergruppe verwenden, die in einem virtuellen Netzwerk bereitgestellt wird.
* Aufgrund der zusätzlichen betreffenden Netzwerkressourcen erfolgt das Bereitstellen einer Containergruppe für ein virtuelles Netzwerk in der Regel etwas langsamer als die Bereitstellung einer Standardcontainerinstanz.

## <a name="preview-limitations"></a>Einschränkungen der Vorschau

Für dieses Feature, das sich in der Vorschauphase befindet, gelten die folgenden Einschränkungen beim Bereitstellen von Containergruppen für ein virtuelles Netzwerk. 

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

Ressourceneinschränkungen für Container können von Einschränkungen für nicht vernetzte Containerinstanzen in diesen Regionen abweichen. Derzeit werden nur Linux-Container für dieses Feature unterstützt. Windows-Unterstützung ist geplant.

### <a name="unsupported-networking-scenarios"></a>Nicht unterstützte Netzwerkszenarien 

* **Azure Load Balancer**: Das Positionieren eines Azure Load Balancer vor Containerinstanzen in einer vernetzten Containergruppe wird nicht unterstützt.
* **Peering virtueller Netzwerke**: Sie können kein Peering für ein virtuelles Netzwerk mit einem an Azure Container Instances delegierten Subnetz zu einem anderen virtuellen Netzwerk durchführen.
* **Routingtabellen**: Benutzerdefinierte Routen können nicht in einem Subnetz eingerichtet werden, das an Azure Container Instances delegiert wurde.
* **Netzwerksicherheitsgruppen**: Ausgehende Sicherheitsregeln in Netzwerksicherheitsgruppen, die auf ein an Azure Container Instances delegiertes Subnetz angewendet werden, werden derzeit nicht erzwungen. 
* **Öffentliche IP-Adresse oder DNS-Bezeichnung**: Containergruppen, die in einem virtuellen Netzwerk bereitgestellt werden, unterstützen derzeit keine direkte Bereitstellung von Containern im Internet mit einer öffentlichen IP-Adresse oder einem vollqualifizierten Domänennamen.
* **Interne Namensauflösung**: Die Namensauflösung für Azure-Ressourcen im virtuellen Netzwerk über das interne Azure DNS wird nicht unterstützt.

Für das **Löschen von Netzwerkressourcen** sind [zusätzliche Schritte](#delete-network-resources) erforderlich, nachdem Sie Containergruppen für das virtuelle Netzwerk bereitgestellt haben.

## <a name="required-network-resources"></a>Erforderliche Netzwerkressourcen

Es gibt drei Azure Virtual Network-Ressourcen, die für die Bereitstellung von Containergruppen in einem virtuellen Netzwerk erforderlich sind: das [virtuelle Netzwerk](#virtual-network) selbst, ein [delegiertes Subnetz](#subnet-delegated) innerhalb des virtuellen Netzwerks und ein [Netzwerkprofil](#network-profile). 

### <a name="virtual-network"></a>Virtuelles Netzwerk

Ein virtuelles Netzwerk definiert den Adressraum, in dem Sie mindestens ein Subnetz erstellen. Anschließend stellen Sie Azure-Ressourcen (z.B. Containergruppen) in den Subnetzen in Ihrem virtuellen Netzwerk bereit.

### <a name="subnet-delegated"></a>Subnetz (delegiert)

Subnetze unterteilen das virtuelle Netzwerk in getrennte Adressräume, die von den Azure-Ressourcen, die Sie darin platzieren, verwendet werden können. Sie erstellen eine oder mehrere Subnetze innerhalb eines virtuellen Netzwerks.

Das Subnetz, das Sie für Containergruppen verwenden, darf nur Containergruppen enthalten. Wenn Sie zuerst eine Containergruppe für ein Subnetz bereitstellen, delegiert Azure das Subnetz an Azure Container Instances. Nach erfolgter Delegierung kann das Subnetz nur für Containergruppen verwendet werden. Wenn Sie versuchen, andere Ressourcen als Containergruppen für ein delegiertes Subnetz bereitzustellen, tritt ein Fehler beim Vorgang auf.

### <a name="network-profile"></a>Netzwerkprofil

Ein Netzwerkprofil ist eine Netzwerkkonfigurationsvorlage für Azure-Ressourcen. Es gibt bestimmte Netzwerkeigenschaften für die Ressource an, z.B. das Subnetz, in dem diese bereitgestellt werden sollen. Wenn Sie den Befehl [az container create][az-container-create] zum ersten Mal zum Bereitstellen einer Containergruppe in einem Subnetz (und somit einem virtuellen Netzwerk) verwenden, erstellt Azure ein Netzwerkprofil für Sie. Sie können dieses Netzwerkprofil dann für zukünftige Bereitstellungen in dem Subnetz verwenden. 

Sie müssen die vollständige Resource Manager-Ressourcen-ID eines Netzwerkprofils angeben, um eine Resource Manager-Vorlage, YAML-Datei oder programmgesteuerte Methode zum Bereitstellen einer Containergruppe in einem Subnetz verwenden zu können. Sie können ein Profil nutzen, das zuvor mit [az container create][az-container-create] erstellt wurde, oder ein Profil mit einer Resource Manager-Vorlage erstellen (siehe [Vorlagenbeispiel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) and [Referenz](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Verwenden Sie den Befehl [az network profile list][az-network-profile-list], um die ID eines zuvor erstellten Profils abzurufen. 

In der folgenden Abbildung wurden mehrere Containergruppen für ein Subnetz bereitgestellt, das an Azure Container Instances delegiert wurde. Nachdem Sie eine Containergruppe für ein Subnetz bereitgestellt haben, können Sie zusätzliche Containergruppen für dieses bereitstellen, indem Sie das gleiche Netzwerkprofil angeben.

![Containergruppen in einem virtuellen Netzwerk][aci-vnet-01]

## <a name="deployment-scenarios"></a>Bereitstellungsszenarien

Sie können [az container create][az-container-create] verwenden, um Containergruppen für ein neues virtuelles Netzwerk bereitzustellen, und Azure die Erstellung der erforderlichen Netzwerkressourcen in Ihrem Auftrag erlauben oder Containergruppen für ein vorhandenes virtuelles Netzwerk bereitstellen. 

### <a name="new-virtual-network"></a>Neues virtuelles Netzwerk

Um eine Bereitstellung für ein neues virtuelles Netzwerk vorzunehmen und die automatische Erstellung von Netzwerkressourcen durch Azure zu veranlassen, geben Sie Folgendes beim Ausführen von [az container create][az-container-create] an:

* Name des virtuellen Netzwerks
* Adresspräfix des virtuellen Netzwerks im CIDR-Format
* Subnetzname
* Adresspräfix des Subnetzes im CIDR-Format

Das virtuelle Netzwerk und die Adresspräfixe für das Subnetz geben die Adressräume für das virtuelle Netzwerk bzw. das Subnetz an. Diese Werte werden in CIDR-Schreibweise (Classless Inter-Domain Routing) dargestellt, z.B. `10.0.0.0/16`. Weitere Informationen zum Arbeiten mit Subnetzen finden Sie unter [Hinzufügen, Ändern oder Löschen von Subnetzen virtueller Netzwerke](../virtual-network/virtual-network-manage-subnet.md).

Nachdem Sie Ihre erste Containergruppe mit dieser Methode bereitgestellt haben, können Sie Bereitstellungen für das gleiche Subnetz vornehmen, indem Sie die Namen des virtuellen Netzwerks und des Subnetzes oder Netzwerkprofil angeben, das Azure automatisch für Sie erstellt. Da Azure das Subnetz an Azure Container Instances delegiert, können Sie *nur* Containergruppen für das Subnetz bereitstellen.

### <a name="existing-virtual-network"></a>Vorhandenes virtuelles Netzwerk

Um eine Containergruppe in einem vorhandenen virtuellen Netzwerk bereitzustellen, gehen Sie wie folgt vor:

1. Erstellen Sie ein Subnetz in Ihrem vorhandenen virtuellen Netzwerk, oder entfernen Sie *alle* anderen Ressourcen aus einem vorhandenen Subnetz.
1. Stellen Sie eine Containergruppe mit [az container create][az-container-create] bereit, und geben Sie eine der folgenden Informationen an:
   * Name des virtuellen Netzwerks und des Subnetzes
   * VNET-Ressourcen-ID und Subnetzressourcen-ID, um die Verwendung eines virtuellen Netzwerks über eine andere Ressourcengruppe zu ermöglichen
   * Netzwerkprofilname oder -ID, den bzw. die Sie mit [az network profile list][az-network-profile-list] abrufen können

Sobald Sie Ihre erste Containergruppe für ein vorhandenes Subnetz bereitgestellt haben, delegiert Azure das Subnetz an Azure Container Instances. Sie können dann als Ressourcen nur noch Containergruppen in diesem Subnetz bereitstellen.

## <a name="deployment-examples"></a>Bereitstellungsbeispiele

In den folgenden Abschnitten wird beschrieben, wie mit der Azure CLI Containergruppen für ein virtuelles Netzwerk bereitgestellt werden. Die Befehlsbeispiele sind für die **Bash**-Shell formatiert. Wenn Sie eine andere Shell, wie PowerShell oder die Eingabeaufforderung bevorzugen, passen Sie die Zeilenfortsetzungszeichen entsprechend an.

### <a name="deploy-to-a-new-virtual-network"></a>Bereitstellen in einem neuen virtuellen Netzwerk

Stellen Sie zunächst eine Containergruppe bereit, und geben Sie die Parameter für ein neues virtuelles Netzwerk und Subnetz an. Wenn Sie diese Parameter angeben, erstellt Azure das virtuelle Netzwerk und Subnetz, delegiert das Subnetz an Azure Container Instances und erstellt außerdem ein Netzwerkprofil. Nachdem diese Ressourcen erstellt wurden, wird Ihre Containergruppe im Subnetz bereitgestellt.

Führen Sie den Befehl [az container create][az-container-create] aus, der Einstellungen für ein neues virtuelles Netzwerk und Subnetz angibt. Sie müssen den Namen einer Ressourcengruppe angeben, der in einer Region erstellt wurde, für die Containergruppen in einem virtuellen Netzwerk [unterstützt](#preview-limitations) werden. Dieser Befehl stellt den öffentlichen Microsoft-Container [aci-helloworld][aci-helloworld] bereit, der einen kleinen Node.js-Webserver ausführt. Dieser verarbeitet Anforderungen von einer statischen Webseite. Im nächsten Abschnitt stellen Sie eine zweite Containergruppe im gleichen Subnetz bereit und testen die Kommunikation zwischen den beiden Containerinstanzen.

```azurecli
az container create \
    --name appcontainer \
    --resource-group myResourceGroup \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --vnet aci-vnet \
    --vnet-address-prefix 10.0.0.0/16 \
    --subnet aci-subnet \
    --subnet-address-prefix 10.0.0.0/24
```

Wenn Sie mit dieser Methode Bereitstellungen in einem neuen virtuellen Netzwerk ausführen, kann es einige Minuten dauern, bis die Netzwerkressourcen im Zuge der Bereitstellung erstellt werden. Nach der Erstbereitstellung werden Bereitstellungen von zusätzlichen Containern schneller ausgeführt.

### <a name="deploy-to-existing-virtual-network"></a>Bereitstellen von Containergruppen in einem vorhandenen virtuellen Netzwerk

Nachdem Sie eine Containergruppe in einem neuen virtuellen Netzwerk bereitgestellt haben, stellen Sie eine zweite Containergruppe im gleichen Subnetz bereit, und überprüfen Sie die Kommunikation zwischen den zwei Containerinstanzen.

Rufen Sie zuerst die IP-Adresse der ersten Containergruppe ab, die Sie bereitgestellt haben, d.h. von *appcontainer*:

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

Die Ausgabe sollte die IP-Adresse der Containergruppe im privaten Subnetz anzeigen:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Legen Sie jetzt `CONTAINER_GROUP_IP` auf die IP-Adresse fest, die Sie mit dem Befehl `az container show` abgerufen haben, und führen Sie den folgenden `az container create`-Befehl aus. Der zweite Container *commchecker* führt ein Alpine Linux-basiertes Image sowie `wget` für die erste private Subnetz-IP-Adresse der Containergruppe aus.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

az container create \
    --resource-group myResourceGroup \
    --name commchecker \
    --image alpine:3.5 \
    --command-line "wget $CONTAINER_GROUP_IP" \
    --restart-policy never \
    --vnet aci-vnet \
    --subnet aci-subnet
```

Nach der zweiten Containerbereitstellung werden die jeweiligen Protokolle abgerufen, sodass Sie die Ausgabe des Befehls `wget`, der ausgeführt wurde, sehen können:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Wenn der zweite Container erfolgreich eine Kommunikation mit dem ersten Container aufgebaut hat, sollte die Ausgabe ähnlich wie im folgendem Beispiel aussehen:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

Die Protokollausgabe sollte zeigen, dass `wget` eine Verbindung herstellen und die Indexdatei aus dem ersten Container mithilfe der privaten IP-Adresse im lokalen Subnetz herunterladen konnte. Der Netzwerkdatenverkehr zwischen den zwei Containergruppen fand innerhalb des virtuellen Netzwerks statt.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Bereitstellen von Containergruppen in einem virtuellen Netzwerk: YAML

Sie können auch mithilfe einer YAML-Datei eine Containergruppe in einem vorhandenen virtuellen Netzwerk bereitstellen. Um eine Bereitstellung in einem Subnetz in einem virtuellen Netzwerk vorzunehmen, geben Sie mehrere zusätzliche Eigenschaften in YAML an:

* `ipAddress`: Die IP-Adresseinstellungen für die Containergruppe.
  * `ports`: Die zu öffnenden Ports, sofern vorhanden.
  * `protocol`: Das Protokoll (TCP oder UDP) für den geöffneten Port.
* `networkProfile`: Die Netzwerkeinstellungen wie das virtuelle Netzwerk und Subnetz für eine Azure-Ressource.
  * `id`: Die vollständige Resource Manager-Ressourcen-ID von `networkProfile`.

Um mit einer YAML-Datei eine Containergruppe in einem virtuellen Netzwerk bereitzustellen, müssen Sie zuerst die ID des Netzwerkprofils abrufen. Führen Sie den Befehl [az network profile list][az-network-profile-list] aus, indem Sie den Namen der Ressourcengruppe angeben, die Ihr virtuelles Netzwerk und das delegierte Subnetz enthält.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

Die Ausgabe des Befehls zeigt die vollständige Ressourcen-ID für das Netzwerkprofil:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Sobald Sie über die ID des Netzwerkprofils verfügen, kopieren Sie den folgenden YAML-Code, und fügen Sie ihn in eine neue Datei namens *vnet-deploy-aci.yaml* ein. Ersetzen Sie unter `networkProfile` den `id`-Wert durch die ID, die Sie gerade abgerufen haben, und speichern Sie die Datei. Dieser YAML-Code erstellt eine Containergruppe mit dem Namen *appcontaineryaml* in Ihrem virtuellen Netzwerk.

```YAML
apiVersion: '2018-09-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/container/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Stellen Sie die Containergruppe mit dem Befehl [az container create][az-container-create] bereit, indem Sie den YAML-Dateinamen für den Parameter `--file` angeben:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Sobald die Bereitstellung abgeschlossen ist, führen Sie den Befehl [az container show][az-container-show] aus, um den Status anzuzeigen:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

### <a name="delete-container-instances"></a>Löschen von Containerinstanzen

Wenn Sie die von Ihnen erstellten Containerinstanzen nicht mehr benötigen, löschen Sie sie mit den folgenden Befehlen:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Löschen von Netzwerkressourcen

In der ersten Vorschauversion dieses Features sind mehrere zusätzliche Befehle notwendig, um die Netzwerkressourcen, die Sie zuvor erstellt haben, zu löschen. Wenn Sie die Beispielbefehle in den vorherigen Abschnitten dieses Artikels verwendet haben, um Ihr virtuelles Netzwerk und Subnetz zu erstellen, können Sie diese Netzwerkressourcen mit dem folgenden Skript löschen.

Legen Sie vor der Ausführung des Skripts die Variable `RES_GROUP` auf den Namen der Ressourcengruppe fest, die das virtuelle Netzwerk und Subnetz enthält, die gelöscht werden sollen. Aktualisieren Sie die Namen des virtuellen Netzwerks und des Subnetzes, wenn Sie die zuvor vorgeschlagenen Namen `aci-vnet` und `aci-subnet` nicht verwendet haben. Das Skript ist für die Bash-Shell formatiert. Wenn Sie eine andere Shell, wie PowerShell oder Eingabeaufforderung bevorzugen, müssen Sie die Variablenzuweisung und die Zugriffsmethoden entsprechend anpassen.

> [!WARNING]
> Dieses Skript löscht Ressourcen! Es löscht das virtuelle Netzwerk und alle darin enthaltenen Subnetze. Vergewissern Sie sich daher vor der Ausführung dieses Skripts unbedingt, dass Sie die Ressourcen im virtuellen Netzwerk *tatsächlich* nicht mehr benötigen, einschließlich der darin enthaltenen Subnetze. Einmal gelöscht, **können diese Ressourcen nicht mehr wiederhergestellt werden**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
# Replace aci-vnet and aci-subnet with your VNet and subnet names in the following commands

SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet --remove delegations 0

# Delete the subnet
az network vnet subnet delete --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Bereitstellen eines neuen virtuellen Netzwerks, eines Subnetzes, eines Netzwerkprofils und einer Containergruppe mithilfe einer Resource Manager-Vorlage finden Sie unter [Create an Azure container group with VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
) (Erstellen einer Azure-Containergruppe mit einem virtuellen Netzwerk).

In diesem Artikel wurden verschiedene Ressourcen und Features des virtuellen Netzwerks erläutert, wenn auch nur in Kürze. In der Dokumentation zu Azure Virtual Network wird ausführlich auf diese Themen eingegangen:

* [Virtuelles Netzwerk](../virtual-network/manage-virtual-network.md)
* [Subnetz](../virtual-network/virtual-network-manage-subnet.md)
* [Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
