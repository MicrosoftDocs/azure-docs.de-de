---
title: Einrichten einer privaten Verbindung
description: Richten Sie einen privaten Endpunkt in einer Containerregistrierung ein, und ermöglichen Sie den Zugriff auf ein virtuelles Netzwerk über eine private Verbindung. Der Zugriff auf private Links ist ein Feature der Dienstebene Premium.
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 3193c65a2021d29f03bd9ae6cbc00fd6c349d9bf
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342299"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Herstellen einer privaten Verbindung mit einer Azure-Containerregistrierung über Azure Private Link


Beschränken Sie den Zugriff auf eine Registrierung, indem Sie den Registrierungsendpunkten private IP-Adressen eines virtuellen Netzwerks zuweisen und [Azure Private Link](../private-link/private-link-overview.md) verwenden. Der Netzwerkdatenverkehr zwischen den Clients im virtuellen Netzwerk und den privaten Endpunkten der Registrierung wird über das virtuelle Netzwerk und einen privaten Link im Microsoft-Backbonenetzwerk geleitet, und somit nicht im öffentlichen Internet verfügbar gemacht. Private Link ermöglicht auch den Zugriff auf die private Registrierung aus einem lokalen System über privates Peering mit [Azure ExpressRoute](../expressroute/expressroute-introduction.MD) oder ein [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

Sie können für den privaten Endpunkt der Registrierung [DNS-Einstellungen konfigurieren](../private-link/private-endpoint-overview.md#dns-configuration), sodass die Einstellungen zur zugeordneten privaten IP-Adresse der Registrierung aufgelöst werden. Dank DNS-Konfiguration können Clients und Dienste im Netzwerk weiterhin unter dem vollqualifizierten Domänennamen der Registrierung (beispielsweise *myregistry.azurecr.io* ) auf die Registrierung zugreifen. 

Diese Funktion ist auf der Dienstebene **Premium** der Containerregistrierung verfügbar. Zurzeit können maximal 10 private Endpunkte für eine Registrierung eingerichtet werden. Weitere Informationen zu den Tarifen des Registrierungsdiensts und zu den Einschränkungen finden Sie unter [Azure Container Registry-Tarife](container-registry-skus.md).

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie die Azure CLI-Schritte in diesem Artikel verwenden möchten, wird mindestens die Azure CLI-Version 2.6.0 empfohlen. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli]. Alternativ können Sie [Azure Cloud Shell](../cloud-shell/quickstart.md) verwenden.
* Sollten Sie noch nicht über eine Containerregistrierung verfügen, können Sie eine Registrierung erstellen (Premium-Tarif erforderlich) und ein öffentliches Beispielimage (etwa `mcr.microsoft.com/hello-world`) aus Microsoft Container Registry [importieren](container-registry-import-images.md). Zum Erstellen einer Registrierung können Sie das [Azure-Portal][quickstart-portal] oder die [Azure-Befehlszeilenschnittstelle][quickstart-cli] verwenden.
* Um den Registrierungszugriff mithilfe einer privaten Verbindung in einem anderen Azure-Abonnement zu konfigurieren, müssen Sie den Ressourcenanbieter für Azure Container Registry in diesem Abonnement registrieren. Beispiel:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

In den Azure CLI-Beispielen dieses Artikels werden die folgenden Umgebungsvariablen verwendet. Ersetzen Sie die Werte durch entsprechende Werte für Ihre Umgebung. Alle Beispiele sind für die Bash-Shell formatiert:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Einrichten einer privaten Verbindung: CLI

### <a name="get-network-and-subnet-names"></a>Abrufen des Netzwerk- und Subnetznamens

Für die Einrichtung einer privaten Verbindung benötigen Sie den Namen eines virtuellen Netzwerks und eines Subnetzes. In diesem Beispiel wird für den virtuellen Computer und den privaten Endpunkt der Registrierung das gleiche Subnetz verwendet. In vielen Szenarien wird der Endpunkt jedoch in einem separaten Subnetz eingerichtet. 

Wenn Sie einen virtuellen Computer erstellen, erstellt Azure standardmäßig in der gleichen Ressourcengruppe ein virtuelles Netzwerk. Der Name des virtuellen Netzwerks hängt von dem Namen des virtuellen Computers ab. Wenn Sie Ihren virtuellen Computer beispielsweise *MyDockerVM* nennen, lautet der Standardname des virtuellen Netzwerks *MyDockerVMVNET* mit einem Subnetz namens *MyDockerVMSubnet*. Legen Sie diese Werte in Umgebungsvariablen fest, indem Sie den Befehl [az network vnet list][az-network-vnet-list] ausführen:

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>Deaktivieren von Netzwerkrichtlinien im Subnetz

[Deaktivieren Sie Netzwerkrichtlinien](../private-link/disable-private-endpoint-network-policy.md) (beispielsweise Netzwerksicherheitsgruppen) im Subnetz für den privaten Endpunkt. Aktualisieren Sie Ihre Subnetzkonfiguration mithilfe des Befehls [az network vnet subnet update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Konfigurieren der privaten DNS-Zone

Erstellen Sie eine [private DNS-Zone](../dns/private-dns-privatednszone.md) für die private Domäne der Azure-Containerregistrierung. Später erstellen Sie dann DNS-Einträge für Ihre Registrierungsdomäne innerhalb dieser DNS-Zone.

Wenn Sie die DNS-Standardauflösung für Ihre Containerregistrierung mithilfe einer privaten Zone überschreiben möchten, muss die Zone den Namen **privatelink.azurecr.io** haben. Führen Sie den Befehl [az network private-dns zone create][az-network-private-dns-zone-create] wie folgt aus, um die private Zone zu erstellen:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Erstellen eines Zuordnungslinks

Führen Sie [az network private-dns link vnet create][az-network-private-dns-link-vnet-create] aus, um Ihre private Zone dem virtuellen Netzwerk zuzuordnen. In diesem Beispiel wird ein Link namens *myDNSLink* erstellt.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Erstellen eines privaten Registrierungsendpunkts

In diesem Abschnitt wird der private Endpunkt der Registrierung im virtuellen Netzwerk erstellt. Rufen Sie zunächst die Ressourcen-ID Ihrer Registrierung ab:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Führen Sie den Befehl [az network private-endpoint create][az-network-private-endpoint-create] aus, um den privaten Endpunkt der Registrierung zu erstellen.

Im folgenden Beispiel werden der Endpunkt *myPrivateEndpoint* und die Dienstverbindung *myConnection* erstellt. Übergeben Sie `--group-ids registry`, um eine Containerregistrierungsressource für den Endpunkt anzugeben:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Abrufen privater IP-Adressen

Führen Sie [az network private-endpoint show][az-network-private-endpoint-show] aus, um die Netzwerkschnittstellen-ID vom Endpunkt abzufragen:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Mit der Netzwerkschnittstelle sind in diesem Beispiel zwei private IP-Adressen für die Containerregistrierung verknüpft: eine für die eigentliche Registrierung und eine für den Datenendpunkt der Registrierung. Die folgenden [az resource show][az-resource-show]-Befehle rufen die privaten IP-Adressen für die Containerregistrierung und den Datenendpunkt der Registrierung ab:

```azurecli
PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

> [!NOTE]
> Wenn Ihre Registrierung [georepliziert](container-registry-geo-replication.md) ist, fragen Sie den zusätzlichen Datenendpunkt für jedes Registrierungsreplikat ab.

### <a name="create-dns-records-in-the-private-zone"></a>Erstellen von DNS-Einträgen in der privaten Zone

Mit den folgenden Befehlen werden DNS-Einträge in der privaten Zone für den Registrierungsendpunkt und den zugehörigen Datenendpunkt erstellt. Wenn Sie also beispielsweise über eine Registrierung namens *myregistry* in der Region *westeurope* verfügen, lauten die Endpunktnamen `myregistry.azurecr.io` und `myregistry.westeurope.data.azurecr.io`. 

> [!NOTE]
> Wenn Ihre Registrierung [georepliziert](container-registry-geo-replication.md) ist, erstellen Sie zusätzliche DNS-Einträge für die Datenendpunkt-IP-Adresse jedes Replikats.

Führen Sie zuerst [az network private-dns record-set a create][az-network-private-dns-record-set-a-create] aus, um leere A-Ressourceneintragssätze für den Registrierungsendpunkt und den Datenendpunkt zu erstellen:

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

Führen Sie den Befehl [az network private-dns record-set a add-record][az-network-private-dns-record-set-a-add-record] aus, um die A-Einträge für den Registrierungsendpunkt und den Datenendpunkt zu erstellen:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

Die private Verbindung ist nun konfiguriert und einsatzbereit.

## <a name="set-up-private-link---portal"></a>Einrichten einer privaten Verbindung: Portal

Richten Sie eine private Verbindung ein, wenn Sie eine Registrierung erstellen, oder fügen Sie einer vorhandenen Registrierung eine private Verbindung hinzu. Bei den folgenden Schritten wird davon ausgegangen, dass Sie bereits ein virtuelles Netzwerk und ein Subnetz eingerichtet haben und über einen virtuellen Computer zum Testen verfügen. Sie können auch [ein neues virtuelles Netzwerk und Subnetz erstellen](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint---new-registry"></a>Erstellen eines privaten Endpunkts: neue Registrierung

1. Wenn Sie eine Registrierung im Portal erstellen, wählen Sie auf der Registerkarte **Grundlagen** in **SKU** den Tarif **Premium** aus.
1. Wählen Sie die Registerkarte **Netzwerk** aus.
1. Wählen Sie in **Netzwerkverbindung** die Optionen **Privater Endpunkt** >  **+ Hinzufügen** aus.
1. Geben Sie die folgenden Informationen an:

    | Einstellung | Wert |
    | ------- | ----- |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Geben Sie den Namen einer vorhandenen Gruppe ein, oder erstellen Sie eine neue.|
    | Name | Geben Sie einen eindeutigen Namen ein. |
    | Unterressource |Wählen Sie **Registrierung** aus.|
    | **Netzwerk** | |
    | Virtuelles Netzwerk| Wählen Sie das virtuelle Netzwerk aus, in dem Ihr virtueller Computer bereitgestellt wird (beispielsweise *myDockerVMVNET* ). |
    | Subnet | Wählen Sie ein Subnetz (beispielsweise *myDockerVMSubnet* ) aus, in dem Ihr virtueller Computer bereitgestellt wird. |
    |**Private DNS-Integration**||
    |Integration in eine private DNS-Zone |Wählen Sie **Ja** aus. |
    |Private DNS-Zone |Wählen Sie *(Neu) privatelink.azurecr.io* aus. |
    |||
1. Konfigurieren Sie die restlichen Registrierungseinstellungen, und wählen Sie dann **Überprüfen und erstellen** aus.

  ![Erstellen einer Registrierung mit einem privaten Endpunkt](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Erstellen eines privaten Endpunkts: vorhandene Registrierung

1. Navigieren Sie im Azure-Portal zu Ihrer Containerregistrierung.
1. Wählen Sie unter **Einstellungen** die Option **Netzwerk** aus.
1. Wählen Sie auf der Registerkarte **Private Endpunkte** die Option **+ Privater Endpunkt** aus.
1. Geben Sie auf der Registerkarte **Grundeinstellungen** die folgenden Informationen an:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** | |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Geben Sie den Namen einer vorhandenen Gruppe ein, oder erstellen Sie eine neue.|
    | **Instanzendetails** |  |
    | Name | Geben Sie einen Namen ein. |
    |Region|Wählen Sie eine Region aus.|
    |||
5. Klicken Sie auf **Weiter: Ressource** aus.
6. Geben Sie die folgenden Informationen an:

    | Einstellung | Wert |
    | ------- | ----- |
    |Verbindungsmethode  | Wählen Sie **Hiermit wird eine Verbindung mit einer Azure-Ressource im eigenen Verzeichnis hergestellt** aus.|
    | Subscription| Wählen Sie Ihr Abonnement aus. |
    | Ressourcentyp | Wählen Sie **Microsoft.ContainerRegistry/registries** aus. |
    | Resource |Wählen Sie den Namen Ihrer Registrierung aus.|
    |Unterressource des Ziels |Wählen Sie **Registrierung** aus.|
    |||
7. Klicken Sie auf **Weiter: Konfiguration** aus.
8. Geben Sie die folgenden Informationen an:

    | Einstellung | Wert |
    | ------- | ----- |
    |**Netzwerk**| |
    | Virtuelles Netzwerk| Wählen Sie das virtuelle Netzwerk aus, in dem Ihr virtueller Computer bereitgestellt wird (beispielsweise *myDockerVMVNET* ). |
    | Subnet | Wählen Sie ein Subnetz (beispielsweise *myDockerVMSubnet* ) aus, in dem Ihr virtueller Computer bereitgestellt wird. |
    |**Private DNS-Integration**||
    |Integration in eine private DNS-Zone |Wählen Sie **Ja** aus. |
    |Private DNS-Zone |Wählen Sie *(Neu) privatelink.azurecr.io* aus. |
    |||

1. Klicken Sie auf **Überprüfen + erstellen**. Sie werden zur Seite **Überprüfen und erstellen** weitergeleitet, auf der Azure Ihre Konfiguration überprüft. 
2. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus.

Nachdem der private Endpunkt erstellt wurde, werden DNS-Einstellungen in der privaten Zone auf der Seite **Private Endpunkte** im Portal angezeigt:

1. Navigieren Sie im Portal zu Ihrer Containerregistrierung, und wählen Sie **Einstellungen > Netzwerk** aus.
1. Wählen Sie auf der Registerkarte **Private Endpunkte** den privaten Endpunkt aus, den Sie erstellt haben.
1. Überprüfen Sie auf der Seite **Übersicht** die Verbindungseinstellungen und benutzerdefinierten DNS-Einstellungen.

  ![DNS-Einstellungen des Endpunkts](./media/container-registry-private-link/private-endpoint-overview.png)

Ihre private Verbindung ist nun konfiguriert und einsatzbereit.

## <a name="disable-public-access"></a>Deaktivieren des öffentlichen Zugriffs

Deaktivieren Sie für viele Szenarien den Registrierungszugriff aus öffentlichen Netzwerken. Mit dieser Konfiguration wird verhindert, dass Clients von außerhalb des virtuellen Netzwerks die Registrierungsendpunkte erreichen können. 

### <a name="disable-public-access---cli"></a>Deaktivieren des öffentlichen Zugriffs – CLI

Um den öffentlichen Zugriff mithilfe der Azure CLI zu deaktivieren, führen Sie [az acr update][az-acr-update] aus, und legen Sie `--public-network-enabled` auf `false` fest. 

> [!NOTE]
> Das Argument `public-network-enabled` erfordert Azure CLI 2.6.0 oder höher. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>Deaktivieren des öffentlichen Zugriffs – Portal

1. Navigieren Sie im Portal zu Ihrer Containerregistrierung, und wählen Sie **Einstellungen > Netzwerk** aus.
1. Wählen Sie auf der Registerkarte **Öffentlicher Zugriff** in **Öffentlichen Netzwerkzugriff zulassen** die Option **Deaktiviert** aus. Klicken Sie dann auf **Speichern**.

## <a name="validate-private-link-connection"></a>Überprüfen der Private Link-Verbindung

Vergewissern Sie sich, dass die Ressourcen im Subnetz des privaten Endpunkts eine Verbindung mit Ihrer Registrierung über eine private IP-Adresse herstellen und dass die Integration in die private DNS-Zone korrekt ist.

Stellen Sie zum Überprüfen der Private Link-Verbindung eine SSH-Verbindung mit dem virtuellen Computer her, den Sie im virtuellen Netzwerk eingerichtet haben.

Führen Sie ein Hilfsprogramm wie `nslookup` oder `dig` aus, um die IP-Adresse Ihrer Registrierung über die private Verbindung nachzuschlagen. Beispiel:

```bash
dig $REGISTRY_NAME.azurecr.io
```

Die Beispielausgabe zeigt die IP-Adresse der Registrierung im Adressraum des Subnetzes:

```console
[...]
; <<>> DiG 9.11.3-1ubuntu1.13-Ubuntu <<>> myregistry.azurecr.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52155
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;myregistry.azurecr.io.         IN      A

;; ANSWER SECTION:
myregistry.azurecr.io.  1783    IN      CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 10 IN A      10.0.0.7

[...]
```

Vergleichen Sie dieses Ergebnis mit der öffentlichen IP-Adresse in der Ausgabe von `dig` für die gleiche Registrierung über einen öffentlichen Endpunkt:

```console
[...]
;; ANSWER SECTION:
myregistry.azurecr.io.  2881    IN  CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 2881 IN CNAME xxxx.xx.azcr.io.
xxxx.xx.azcr.io.    300 IN  CNAME   xxxx-xxx-reg.trafficmanager.net.
xxxx-xxx-reg.trafficmanager.net. 300 IN CNAME   xxxx.westeurope.cloudapp.azure.com.
xxxx.westeurope.cloudapp.azure.com. 10  IN A 20.45.122.144

[...]
```

### <a name="registry-operations-over-private-link"></a>Registrierungsvorgänge über die private Verbindung

Vergewissern Sie sich außerdem, dass Sie Registrierungsvorgänge über den virtuellen Computer im Subnetz ausführen können. Stellen Sie eine SSH-Verbindung mit Ihrem virtuellen Computer her, und führen Sie [az acr login][az-acr-login] aus, um sich bei Ihrer Registrierung anzumelden. Je nach VM-Konfiguration müssen die folgenden Befehle unter Umständen mit dem Präfix `sudo` versehen werden.

```bash
az acr login --name $REGISTRY_NAME
```

Führen Sie Registrierungsvorgänge aus (beispielsweise `docker pull`, um ein Beispielimage aus der Registrierung zu pullen). Ersetzen Sie `hello-world:v1` durch ein geeignetes Image und Tag für Ihre Registrierung, und geben Sie den Namen des Anmeldeservers für die Registrierung als Präfix an (nur Kleinbuchstaben):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker ruft erfolgreich mittels Pullvorgang das Image auf den virtuellen Computer ab.

## <a name="manage-private-endpoint-connections"></a>Verwalten von Verbindungen mit dem privaten Endpunkt

Die Verbindungen mit dem privaten Endpunkt einer Registrierung können über das Azure-Portal oder mithilfe von Befehlen aus der Befehlsgruppe [az acr private-endpoint-connection][az-acr-private-endpoint-connection] verwaltet werden. Die Vorgänge umfassen das Genehmigen, Löschen, Auflisten oder Ablehnen von Verbindungen mit dem privaten Endpunkt einer Registrierung sowie das Anzeigen von Details.

Führen Sie also beispielsweise den Befehl [az acr private-endpoint-connection list][az-acr-private-endpoint-connection-list] aus, um die Verbindungen mit dem privaten Endpunkt einer Registrierung aufzulisten. Beispiel:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

Wenn Sie mithilfe der in diesem Artikel beschriebenen Schritte eine Verbindung mit einem privaten Endpunkt einrichten, akzeptiert die Registrierung automatisch Verbindungen von Clients und Diensten, die über Azure RBAC-Berechtigungen für die Registrierung verfügen. Sie können den Endpunkt so einrichten, dass Verbindungen manuell genehmigt werden müssen. Informationen zum Genehmigen und Ablehnen von Verbindungen mit einem privaten Endpunkt finden Sie unter [Verwalten einer Verbindung mit einem privaten Endpunkt](../private-link/manage-private-endpoint.md).

## <a name="add-zone-records-for-replicas"></a>Hinzufügen von Zoneneinträgen für Replikate

Wie in diesem Artikel gezeigt, erstellen Sie beim Hinzufügen einer privaten Endpunktverbindung zu einer Registrierung DNS-Einträge in der `privatelink.azurecr.io`-Zone für die Registrierung und deren Datenendpunkte in den Regionen, in die die Registrierung [repliziert](container-registry-geo-replication.md) wird. 

Wenn Sie später ein neues Replikat hinzufügen, müssen Sie für den Datenendpunkt in dieser Region manuell einen neuen Zoneneintrag hinzufügen. Wenn Sie beispielsweise ein Replikat von *myregistry* im Standort *northeurope* erstellen, fügen Sie einen Zoneneintrag für `myregistry.northeurope.data.azurecr.io` hinzu. Die Schritte hierfür finden Sie in diesem Artikel unter [Erstellen von DNS-Einträgen in der privaten Zone](#create-dns-records-in-the-private-zone).

## <a name="dns-configuration-options"></a>DNS-Konfigurationsoptionen

Der private Endpunkt in diesem Beispiel integriert sich in eine private DNS-Zone, die einem virtuellen Basisnetzwerk zugeordnet ist. Diese Einrichtung verwendet den von Azure bereitgestellten DNS-Dienst direkt, um den öffentlichen FQDN der Registrierung in seine private IP-Adresse im virtuellen Netzwerk aufzulösen. 

Die private Verbindung unterstützt zusätzliche DNS-Konfigurationsszenarien, in denen die private Zone verwendet wird, einschließlich benutzerdefinierter DNS-Lösungen. Angenommen, Sie verfügen z. B. über eine benutzerdefinierte DNS-Lösung, die im virtuellen Netzwerk oder lokal in einem Netzwerk bereitgestellt ist, das über ein VPN-Gateway oder über Azure ExpressRoute mit dem virtuellen Netzwerk verbunden wird. 

Um den öffentlichen FQDN der Registrierung in die private IP-Adresse in diesen Szenarien aufzulösen, müssen Sie eine Weiterleitung auf Serverebene für den Azure DNS-Dienst (168.63.129.16) konfigurieren. Genaue Konfigurationsoptionen und -schritte sind von Ihren vorhandenen Netzwerken und dem DNS abhängig. Beispiele finden Sie unter [DNS-Konfiguration von privaten Azure-Endpunkten](../private-link/private-endpoint-dns.md).

> [!IMPORTANT]
> Falls Sie für Hochverfügbarkeit private Endpunkte in mehreren Regionen erstellt haben, sollten Sie in jeder Region eine separate Ressourcengruppe verwenden und darin das virtuelle Netzwerk und die zugehörige private DNS-Zone platzieren. Diese Konfiguration verhindert auch eine unvorhersehbare DNS-Auflösung, die durch die gemeinsame Nutzung derselben privaten DNS-Zone verursacht wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie alle Azure-Ressourcen in der gleichen Ressourcengruppe erstellt haben und sie nicht mehr benötigen, können Sie die Ressourcen optional mit einem einzigen Befehl [az group delete](/cli/azure/group) löschen:

```azurecli
az group delete --name $RESOURCE_GROUP
```

Wenn Sie Ihre Ressourcen über das Portal bereinigen möchten, navigieren Sie zu Ihrer Ressourcengruppe. Klicken Sie nach dem Laden der Ressourcengruppe auf **Ressourcengruppe löschen** , um die Ressourcengruppe und die dort gespeicherten Ressourcen zu entfernen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Private Link finden Sie in der Dokumentation zu [Azure Private Link](../private-link/private-link-overview.md).
* Wenn Sie Zugriffsregeln für die Registrierung hinter einer Clientfirewall einrichten müssen, finden Sie Informationen hierzu unter [Konfigurieren von Regeln für den Zugriff auf eine Azure-Containerregistrierung hinter einer Firewall](container-registry-firewall-access-rules.md).

<!-- LINKS - external -->
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
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
