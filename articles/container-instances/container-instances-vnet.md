---
title: Bereitstellen einer Containergruppe in einem virtuellen Azure-Netzwerk
description: Erfahren Sie, wie Sie eine Containergruppe in einem neuen oder vorhandenen virtuellen Azure-Netzwerk mithilfe der Azure-Befehlszeilenschnittstelle (CLI) bereitstellen.
ms.topic: article
ms.date: 07/02/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 02cf514e6c19387e3a9e2f1c78b65f346fff764e
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746900"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Bereitstellen von Containerinstanzen in einem virtuellen Azure-Netzwerk

Das virtuelle Azure-Netzwerk ([Azure Virtual Network](../virtual-network/virtual-networks-overview.md)) stellt ein sicheres, privates Netzwerk für Ihre Azure- und lokalen Ressourcen bereit. Durch die Bereitstellung von Containergruppen in einem virtuellen Azure-Netzwerk können Ihre Container sicher mit anderen Ressourcen im virtuellen Netzwerk kommunizieren.

In diesem Artikel wird gezeigt, wie Sie den Befehl [az container create][az-container-create] in der Azure CLI verwenden, um Containergruppen entweder in einem neuen oder in einem vorhandenen virtuellen Netzwerk bereitzustellen. 

Netzwerkszenarien und Einschränkungen finden Sie unter [Virtuelle Netzwerkszenarien und -ressourcen für Azure Container Instances](container-instances-virtual-network-concepts.md).

> [!IMPORTANT]
> Die Bereitstellung von Containergruppen in einem virtuellen Netzwerk ist in den meisten Regionen, in denen Azure Container Instances verfügbar ist, für Linux-Container allgemein verfügbar. Details hierzu finden Sie unter [Verfügbarkeit von Regionen und Ressourcen](container-instances-virtual-network-concepts.md#where-to-deploy). 

Beispiele in diesem Artikel sind für die Ausführung in der Bash-Shell formatiert. Wenn Sie eine andere Shell, wie PowerShell oder die Eingabeaufforderung bevorzugen, passen Sie die Zeilenfortsetzungszeichen entsprechend an.


## <a name="deploy-to-new-virtual-network"></a>Bereitstellen von Containergruppen in einem neuen virtuellen Netzwerk

Geben Sie beim Ausführen des Befehls [az container create][az-container-create] Folgendes an, um eine Bereitstellung in ein neues virtuelles Netzwerk durchzuführen und die Netzwerkressourcen von Azure erstellen zu lassen:

* Name des virtuellen Netzwerks
* Adresspräfix des virtuellen Netzwerks im CIDR-Format
* Subnetzname
* Adresspräfix des Subnetzes im CIDR-Format

Das virtuelle Netzwerk und die Adresspräfixe für das Subnetz geben die Adressräume für das virtuelle Netzwerk bzw. das Subnetz an. Diese Werte werden in CIDR-Schreibweise (Classless Inter-Domain Routing) dargestellt, z.B. `10.0.0.0/16`. Weitere Informationen zum Arbeiten mit Subnetzen finden Sie unter [Hinzufügen, Ändern oder Löschen von Subnetzen virtueller Netzwerke](../virtual-network/virtual-network-manage-subnet.md).

Nachdem Sie Ihre erste Containergruppe mit dieser Methode bereitgestellt haben, können Sie Bereitstellungen für das gleiche Subnetz vornehmen, indem Sie die Namen des virtuellen Netzwerks und des Subnetzes oder Netzwerkprofil angeben, das Azure automatisch für Sie erstellt. Da Azure das Subnetz an Azure Container Instances delegiert, können Sie *nur* Containergruppen für das Subnetz bereitstellen.

### <a name="example"></a>Beispiel

Der folgende Befehl [az container create][az-container-create] gibt Einstellungen für ein neues virtuelles Netzwerk und Subnetz an. Geben Sie den Namen einer Ressourcengruppe an, die in einer Region erstellt wurde, in der Containergruppenbereitstellungen in einem virtuellen Netzwerk [verfügbar](container-instances-region-availability.md) sind. Dieser Befehl stellt den öffentlichen Microsoft-Container [aci-helloworld][aci-helloworld] bereit, der einen kleinen Node.js-Webserver ausführt. Dieser verarbeitet Anforderungen von einer statischen Webseite. Im nächsten Abschnitt stellen Sie eine zweite Containergruppe im gleichen Subnetz bereit und testen die Kommunikation zwischen den beiden Containerinstanzen.

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

Wenn Sie mit dieser Methode Bereitstellungen in einem neuen virtuellen Netzwerk ausführen, kann es einige Minuten dauern, bis die Netzwerkressourcen im Zuge der Bereitstellung erstellt werden. Nach der Erstbereitstellung werden weitere Bereitstellungen von Containergruppen im selben Subnetz schneller ausgeführt.

## <a name="deploy-to-existing-virtual-network"></a>Bereitstellen von Containergruppen in einem vorhandenen virtuellen Netzwerk

Um eine Containergruppe in einem vorhandenen virtuellen Netzwerk bereitzustellen, gehen Sie wie folgt vor:

1. Erstellen Sie ein Subnetz innerhalb Ihres vorhandenen virtuellen Netzwerks, verwenden Sie ein vorhandenes Subnetz, in dem bereits eine Containergruppe bereitgestellt ist, oder verwenden Sie ein vorhandenes Subnetz, aus dem *alle* anderen Ressourcen entfernt wurden.
1. Stellen Sie eine Containergruppe mit [az container create][az-container-create] bereit, und geben Sie eine der folgenden Informationen an:
   * Name des virtuellen Netzwerks und des Subnetzes
   * VNET-Ressourcen-ID und Subnetzressourcen-ID, um die Verwendung eines virtuellen Netzwerks über eine andere Ressourcengruppe zu ermöglichen
   * Netzwerkprofilname oder -ID, den bzw. die Sie mit [az network profile list][az-network-profile-list] abrufen können

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird eine zweite Containergruppe im selben zuvor erstellten Subnetz bereitgestellt und die Kommunikation zwischen den beiden Containerinstanzen überprüft.

Rufen Sie zuerst die IP-Adresse der ersten Containergruppe ab, die Sie bereitgestellt haben, d.h. von *appcontainer* :

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

Die Ausgabe zeigt die IP-Adresse der Containergruppe im privaten Subnetz an. Beispiel:

```console
10.0.0.4
```

Legen Sie jetzt `CONTAINER_GROUP_IP` auf die IP-Adresse fest, die Sie mit dem Befehl `az container show` abgerufen haben, und führen Sie den folgenden `az container create`-Befehl aus. Der zweite Container *commchecker* führt ein Alpine Linux-basiertes Image sowie `wget` für die erste private Subnetz-IP-Adresse der Containergruppe aus.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-address>

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

Wenn der zweite Container erfolgreich eine Kommunikation mit dem ersten Container aufgebaut hat, sieht die Ausgabe ähnlich wie im folgendem Beispiel aus:

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

Die Protokollausgabe sollte zeigen, dass `wget` eine Verbindung herstellen und die Indexdatei aus dem ersten Container mithilfe der privaten IP-Adresse im lokalen Subnetz herunterladen konnte. Der Netzwerkdatenverkehr zwischen den zwei Containergruppen fand innerhalb des virtuellen Netzwerks statt.

### <a name="example---yaml"></a>Beispiel – YAML

Sie können auch mithilfe einer YAML-Datei, einer [Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
) oder einer anderen programmgesteuerten Methode wie dem Python-SKD eine Containergruppe in einem vorhandenen virtuellen Netzwerk bereitstellen. 

Wenn Sie z. B. eine YAML-Datei verwenden, können Sie sie in einem virtuellen Netzwerk mit einem Subnetz bereitstellen, das an Azure Container Instances delegiert ist. Geben Sie die folgenden Eigenschaften an:

* `ipAddress`: Die privaten IP-Adresseinstellungen für die Containergruppe.
  * `ports`: Die zu öffnenden Ports, sofern vorhanden.
  * `protocol`: Das Protokoll (TCP oder UDP) für den geöffneten Port.
* `networkProfile`: Netzwerkeinstellungen für das virtuelle Netzwerk und das Subnetz.
  * `id`: Die vollständige Resource Manager-Ressourcen-ID von `networkProfile`.

Um die ID des Netzwerkprofils abzurufen, führen Sie den Befehl [az network profile list][az-network-profile-list] aus. Geben Sie dabei den Namen der Ressourcengruppe an, die Ihr virtuelles Netzwerk und das delegierte Subnetz enthält.

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

Beispielausgabe:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Sobald Sie über die ID des Netzwerkprofils verfügen, kopieren Sie den folgenden YAML-Code, und fügen Sie ihn in eine neue Datei namens *vnet-deploy-aci.yaml* ein. Ersetzen Sie unter `networkProfile` den `id`-Wert durch die ID, die Sie gerade abgerufen haben, und speichern Sie die Datei. Dieser YAML-Code erstellt eine Containergruppe mit dem Namen *appcontaineryaml* in Ihrem virtuellen Netzwerk.

```YAML
apiVersion: '2019-12-01'
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
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Stellen Sie die Containergruppe mit dem Befehl [az container create][az-container-create] bereit, wobei Sie den YAML-Dateinamen als Parameter `--file` angeben:

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

Führen Sie den Befehl [az container show][az-container-show] aus, sobald die Bereitstellung abgeschlossen ist, um den Status anzuzeigen. Beispielausgabe:

```console
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

Für diese Funktion sind zurzeit mehrere zusätzliche Befehle notwendig, um die Netzwerkressourcen, die Sie zuvor erstellt haben, zu löschen. Wenn Sie die Beispielbefehle in den vorherigen Abschnitten dieses Artikels verwendet haben, um Ihr virtuelles Netzwerk und Subnetz zu erstellen, können Sie diese Netzwerkressourcen mit dem folgenden Skript löschen. Das Skript setzt voraus, dass Ihre Ressourcengruppe ein einzelnes virtuelles Netzwerk mit einem einzigen Netzwerkprofil enthält.

Legen Sie vor der Ausführung des Skripts die Variable `RES_GROUP` auf den Namen der Ressourcengruppe fest, die das virtuelle Netzwerk und Subnetz enthält, die gelöscht werden sollen. Aktualisieren Sie den Namen des virtuellen Netzwerks, wenn Sie nicht den zuvor vorgeschlagenen Namen `aci-vnet` verwendet haben. Das Skript ist für die Bash-Shell formatiert. Wenn Sie eine andere Shell, wie PowerShell oder Eingabeaufforderung bevorzugen, müssen Sie die Variablenzuweisung und die Zugriffsmethoden entsprechend anpassen.

> [!WARNING]
> Dieses Skript löscht Ressourcen! Es löscht das virtuelle Netzwerk und alle darin enthaltenen Subnetze. Vergewissern Sie sich daher vor der Ausführung dieses Skripts unbedingt, dass Sie die Ressourcen im virtuellen Netzwerk *tatsächlich* nicht mehr benötigen, einschließlich der darin enthaltenen Subnetze. Einmal gelöscht, **können diese Ressourcen nicht mehr wiederhergestellt werden** .

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Bereitstellen eines neuen virtuellen Netzwerks, eines Subnetzes, eines Netzwerkprofils und einer Containergruppe mithilfe einer Resource Manager-Vorlage finden Sie unter [Create an Azure container group with VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
) (Erstellen einer Azure-Containergruppe mit einem virtuellen Netzwerk).

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
