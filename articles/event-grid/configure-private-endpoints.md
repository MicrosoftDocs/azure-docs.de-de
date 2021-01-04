---
title: Konfigurieren privater Endpunkte für Azure Event Grid-Themen oder -Domänen
description: In diesem Artikel wird beschrieben, wie private Endpunkte für Azure Event Grid-Themen oder -Domänen konfiguriert werden.
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: f8e0cfc0a850ae15ea6d03ff6ca8b90003adbfc9
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916933"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>Konfigurieren privater Endpunkte für Azure Event Grid-Themen oder -Domänen
Sie können [private Endpunkte](../private-link/private-endpoint-overview.md) verwenden, um den Eingang von Ereignissen direkt aus Ihrem virtuellen Netzwerk in Ihre Themen und Domänen sicher über einen [privaten Link](../private-link/private-link-overview.md) zu ermöglichen, ohne das öffentliche Internet zu durchlaufen. Der private Endpunkt verwendet eine IP-Adresse aus dem VNET-Adressraum für Ihr Thema oder Ihre Domäne. Weitere konzeptionelle Informationen finden Sie unter [Netzwerksicherheit](network-security.md).

In diesem Artikel wird beschrieben, wie private Endpunkte für Themen oder Domänen konfiguriert werden.

## <a name="use-azure-portal"></a>Verwenden des Azure-Portals 
In diesem Abschnitt wird gezeigt, wie Sie das Azure-Portal verwenden, um einen privaten Endpunkt für ein Thema oder eine Domäne zu erstellen.

> [!NOTE]
> Die in diesem Abschnitt beschriebenen Schritte beziehen sich hauptsächlich auf Themen. Sie können ähnliche Schritte verwenden, um private Endpunkte für **Domänen** zu erstellen. 

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem Thema oder Ihrer Domäne.
2. Wechseln Sie zur Registerkarte **Netzwerk** Ihrer Themenseite. Wählen Sie auf der Symbolleiste **+ Privaten Endpunkt hinzufügen** aus.

    ![Privaten Endpunkt hinzufügen](./media/configure-private-endpoints/add-button.png)
2. Führen Sie auf der Seite **Grundlagen** die folgenden Schritte aus: 
    1. Wählen Sie ein **Azure-Abonnement** aus, in dem Sie den privaten Endpunkt erstellen möchten. 
    2. Wählen Sie eine **Azure-Ressourcengruppe** für den privaten Endpunkt aus. 
    3. Geben Sie einen **Namen** für den Endpunkt ein. 
    4. Wählen Sie die **Region** für den Endpunkt aus. Ihr privater Endpunkt muss sich in derselben Region wie Ihr virtuelles Netzwerk befinden, kann aber in einer anderen Region als die Private Link-Ressource enthalten sein (in diesem Beispiel ein Event Grid-Thema). 
    5. Wählen Sie anschließend **Weiter: Ressource >** unten auf der Seite aus. 

      ![Privater Endpunkt: Seite „Grundlagen“](./media/configure-private-endpoints/basics-page.png)
3. Führen Sie auf der Seite **Ressource** die folgenden Schritte aus: 
    1. Wenn Sie als Verbindungsmethode **Hiermit wird eine Verbindung mit einer Azure-Ressource im eigenen Verzeichnis hergestellt** auswählen, führen Sie die folgenden Schritte aus. Dieses Beispiel zeigt, wie Sie eine Verbindung mit einer Azure-Ressource in Ihrem Verzeichnis herstellen. 
        1. Wählen Sie das **Azure-Abonnement** aus, in dem Ihr **Thema oder die Domäne** vorhanden ist. 
        1. Wählen Sie unter **Ressourcentyp** **Microsoft.EventGrid/topics** oder **Microsoft.EventGrid/domains** als **Ressourcentyp** aus.
        2. Wählen Sie als **Ressourcen** in der Dropdownliste ein Thema bzw. eine Domäne aus. 
        3. Vergewissern Sie sich, dass die **Unterressource des Ziels** auf **Thema** oder **Domäne** (basierend auf dem ausgewählten Ressourcentyp) festgelegt ist.    
        4. Klicken Sie auf **Weiter: Konfiguration >** unten auf der Seite. 

            ![Screenshot: Seite „Privaten Endpunkt erstellen – Ressource“](./media/configure-private-endpoints/resource-page.png)
    2. Wenn Sie **Verbindung mit einer Ressource mithilfe einer Ressourcen-ID oder eines Alias herstellen** auswählen, führen Sie die folgenden Schritte aus:
        1. Geben Sie die ID der Ressource ein. Beispiel: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. Geben Sie als **Ressource** die Option **Thema** oder **Domäne** ein. 
        3. (Optional) Fügen Sie eine Anforderungsnachricht hinzu. 
        4. Klicken Sie auf **Weiter: Konfiguration >** unten auf der Seite. 

            ![Privater Endpunkt: Ressourcenseite](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. Wählen Sie auf der Seite **Konfiguration** das Subnetz in einem virtuellen Netzwerk aus, in dem Sie den privaten Endpunkt bereitstellen möchten. 
    1. Wählen Sie ein **virtuelles Netzwerk** aus. Nur virtuelle Netzwerke im aktuell ausgewählten Abonnement und am aktuell ausgewählten Standort werden in der Dropdownliste aufgeführt. 
    2. Wählen Sie ein **Subnetz** innerhalb des ausgewählten virtuellen Netzwerks aus. 
    3. Klicken Sie auf **Weiter: Tags >** unten auf der Seite. 

    ![Private Endpunkt: Konfigurationsseite](./media/configure-private-endpoints/configuration-page.png)
5. Erstellen Sie auf der Seite **Tags** beliebige Tags (Namen und Werte), die Sie der privaten Endpunktressource zuordnen möchten. Wählen Sie dann am unteren Rand der Seite die Schaltfläche **Überprüfen und erstellen** aus. 
6. Überprüfen Sie auf der Seite **Überprüfen und erstellen** alle Einstellungen, und wählen Sie dann **Erstellen** aus, um den privaten Endpunkt zu erstellen. 

    ![Privater Endpunkt: Seite „Überprüfen und erstellen“](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>Verwalten der Private Link-Verbindung

Wenn Sie einen privaten Endpunkt erstellen, muss die Verbindung genehmigt werden. Wenn sich die Ressource, für die Sie einen privaten Endpunkt erstellen, in Ihrem Verzeichnis befindet, können Sie die Verbindungsanforderung genehmigen, sofern Sie über ausreichende Berechtigungen verfügen. Wenn Sie eine Verbindung mit einer Azure-Ressource in einem anderen Verzeichnis herstellen, müssen Sie warten, bis der Besitzer dieser Ressource Ihre Verbindungsanforderung genehmigt hat.

Es gibt vier Möglichkeiten für den Bereitstellungsstatus:

| Dienstaktion | Zustand des privaten Endpunkts des Dienstconsumers | BESCHREIBUNG |
|--|--|--|
| Keine | Ausstehend | Die Verbindung wurde manuell erstellt, und die Genehmigung des Besitzers der Private Link-Ressource steht aus. |
| Genehmigen | Genehmigt | Die Verbindung wurde automatisch oder manuell genehmigt und ist zur Verwendung bereit. |
| Reject | Rejected (Abgelehnt) | Die Verbindung wurde vom Besitzer der Private Link-Ressource abgelehnt. |
| Remove (Entfernen) | Getrennt | Die Verbindung wurde vom Besitzer der Private Link-Ressource entfernt, der private Endpunkt wird informativ und sollte zur Bereinigung gelöscht werden. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Vorgehensweise: Verwalten einer Verbindung mit einem privaten Endpunkt
In den folgenden Abschnitten wird gezeigt, wie Sie eine Verbindung mit einem privaten Endpunkt genehmigen oder ablehnen. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Geben Sie in der Suchleiste **Event Grid-Themen** oder **Event Grid-Domänen** ein.
1. Wählen Sie das **Thema** oder die **Domäne** aus, das bzw. die Sie verwalten möchten.
1. Wählen Sie die Registerkarte **Netzwerk** aus.
1. Sollten ausstehende Verbindungen vorhanden sein, wird in der Liste eine Verbindung mit dem Bereitstellungsstatus **Ausstehend** angezeigt. 

### <a name="to-approve-a-private-endpoint"></a>Genehmigen eines privaten Endpunkts
Sie können einen privaten Endpunkt genehmigen, der sich im Zustand „Ausstehend“ befindet. Führen Sie folgende Schritte aus, um die Genehmigung vorzunehmen: 

> [!NOTE]
> Die in diesem Abschnitt beschriebenen Schritte beziehen sich hauptsächlich auf Themen. Sie können ähnliche Schritte verwenden, um private Endpunkte für **Domänen** zu genehmigen. 

1. Wählen Sie den **privaten Endpunkt** aus, den Sie genehmigen möchten, und wählen Sie auf der Symbolleiste dann **Genehmigen** aus.

    ![Privater Endpunkt: Status „Ausstehend“](./media/configure-private-endpoints/pending.png)
1. Geben Sie im Dialogfeld **Verbindung genehmigen** einen Kommentar ein (optional), und wählen Sie dann **Ja** aus. 

    ![Privater Endpunkt: Genehmigen](./media/configure-private-endpoints/approve.png)
1. Vergewissern Sie sich, dass der Status des Endpunkts **Genehmigt** lautet. 

    ![Privater Endpunkt: Status „Genehmigt“](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Ablehnen eines privaten Endpunkts
Sie können einen privaten Endpunkt ablehnen, der sich im Zustand „Ausstehend“ oder „Genehmigt“ befindet. Führen Sie die folgenden Schritte aus, um einen Endpunkt abzulehnen: 

> [!NOTE]
> Die in diesem Abschnitt beschriebenen Schritte beziehen sich auf Themen. Sie können ähnliche Schritte verwenden, um private Endpunkte für **Domänen** abzulehnen. 

1. Wählen Sie den **privaten Endpunkt** aus, den Sie ablehnen möchten, und wählen Sie auf der Symbolleiste dann **Ablehnen** aus.

    ![Screenshot von „Netzwerk – Verbindung mit privatem Endpunkt“ mit ausgewählter Option „Ablehnen“](./media/configure-private-endpoints/reject-button.png)
1. Geben Sie im Dialogfeld **Verbindung ablehnen** einen Kommentar ein (optional), und wählen Sie dann **Ja** aus. 

    ![Privater Endpunkt: Ablehnen](./media/configure-private-endpoints/reject.png)
1. Vergewissern Sie sich, dass der Status des Endpunkts **Abgelehnt** lautet. 

    ![Privater Endpunkt: Status „Abgelehnt“](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Sie können einen privaten Endpunkt im Azure-Portal nicht genehmigen, nachdem er abgelehnt wurde. 


## <a name="use-azure-cli"></a>Mithilfe der Azure-Befehlszeilenschnittstelle
Verwenden Sie zum Erstellen eines privaten Endpunkts die Methode [az network private-endpoint create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create), wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Beschreibungen der Parameter, die im Beispiel verwendet werden, finden Sie in der Dokumentation zu [az network private-endpoint create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create). In diesem Beispiel sind einige Punkte zu beachten: 

- Geben Sie für `private-connection-resource-id` die Ressourcen-ID des **Themas** oder der **Domäne** an. Im vorangehenden Beispiel wird der Typ „Thema“ (topic) verwendet.
- Geben Sie für `group-ids` `topic` oder `domain` an. Im vorherigen Beispiel wird `topic` verwendet. 

Verwenden Sie zum Löschen eines privaten Endpunkts die Methode [az network private-endpoint delete](/cli/azure/network/private-endpoint?#az-network-private-endpoint-delete), wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Die in diesem Abschnitt beschriebenen Schritte beziehen sich auf Themen. Sie können ähnliche Schritte verwenden, um private Endpunkte für **Domänen** zu erstellen. 



### <a name="prerequisites"></a>Voraussetzungen
Aktualisieren Sie die Azure Event Grid-Erweiterung für die CLI, indem Sie den folgenden Befehl ausführen: 

```azurecli-interactive
az extension update -n eventgrid
```

Wenn die Erweiterung nicht installiert ist, führen Sie den folgenden Befehl aus, um sie zu installieren: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>Erstellen eines privaten Endpunkts
Verwenden Sie zum Erstellen eines privaten Endpunkts die Methode [az network private-endpoint create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create), wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Beschreibungen der Parameter, die im Beispiel verwendet werden, finden Sie in der Dokumentation zu [az network private-endpoint create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create). In diesem Beispiel sind einige Punkte zu beachten: 

- Geben Sie für `private-connection-resource-id` die Ressourcen-ID des **Themas** oder der **Domäne** an. Im vorangehenden Beispiel wird der Typ „Thema“ (topic) verwendet.
- Geben Sie für `group-ids` `topic` oder `domain` an. Im vorherigen Beispiel wird `topic` verwendet. 

Verwenden Sie zum Löschen eines privaten Endpunkts die Methode [az network private-endpoint delete](/cli/azure/network/private-endpoint?#az-network-private-endpoint-delete), wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Die in diesem Abschnitt beschriebenen Schritte beziehen sich auf Themen. Sie können ähnliche Schritte verwenden, um private Endpunkte für **Domänen** zu erstellen. 

#### <a name="sample-script"></a>Beispielskript
Dies ist ein Beispielskript, mit dem die folgenden Azure-Ressourcen erstellt werden:

- Resource group
- Virtuelles Netzwerk
- Subnetz im virtuellen Netzwerk
- Azure Event Grid-Thema
- Privater Endpunkt für das Thema

> [!NOTE]
> Die in diesem Abschnitt beschriebenen Schritte beziehen sich auf Themen. Sie können ähnliche Schritte verwenden, um private Endpunkte für Domänen zu erstellen.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# verify that the topic was created.
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

```

### <a name="approve-a-private-endpoint"></a>Genehmigen eines privaten Endpunkts
Im folgenden CLI-Beispielcodeausschnitt wird gezeigt, wie Sie eine Verbindung mit einem privaten Endpunkt genehmigen. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>Ablehnen eines privaten Endpunkts
Im folgenden CLI-Beispielcodeausschnitt wird gezeigt, wie Sie eine Verbindung mit einem privaten Endpunkt ablehnen. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>Deaktivieren des Zugriffs auf das öffentliche Netzwerk
Standardmäßig ist der Zugriff auf das öffentliche Netzwerk für Event Grid-Themen und -Domänen aktiviert. Um den Zugriff nur über private Endpunkte zuzulassen, deaktivieren Sie den Zugriff auf das öffentliche Netzwerk, indem Sie den folgenden Befehl ausführen:  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>Verwenden von PowerShell
In diesem Abschnitt wird gezeigt, wie Sie einen privaten Endpunkt für ein Thema oder eine Domäne mit PowerShell erstellen. 

### <a name="prerequisite"></a>Voraussetzung
Befolgen Sie die Anweisungen unter [Vorgehensweise: Verwenden des Portals zum Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals, der auf Ressourcen zugreifen kann](../active-directory/develop/howto-create-service-principal-portal.md), um eine Azure Active Directory Anwendung zu erstellen, und notieren Sie sich die Werte für **Verzeichnis-ID (Mandant)** , **Anwendungs-ID (Client)** und **Anwendungsgeheimnis(Client)** . 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Vorbereiten von Token und Headern für REST-API-Aufrufe 
Führen Sie die folgenden erforderlichen Befehle aus, um ein Authentifizierungstoken zur Verwendung mit REST-API-Aufrufen und Autorisierungs- und andere Headerinformationen abzurufen. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Erstellen eines Subnetzes mit deaktivierten Endpunktnetzwerkrichtlinien

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Erstellen eines Event Grid-Themas mit einem privaten Endpunkt

> [!NOTE]
> Die in diesem Abschnitt beschriebenen Schritte beziehen sich auf Themen. Sie können ähnliche Schritte verwenden, um private Endpunkte für **Domänen** zu erstellen. 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-06-01"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

Wenn Sie überprüfen, ob der Endpunkt erstellt wurde, sollte ein Ergebnis ähnlich dem folgenden angezeigt werden:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Genehmigen einer Verbindung mit einem privaten Endpunkt
Im folgenden PowerShell-Beispielcodeausschnitt wird gezeigt, wie Sie einen privaten Endpunkt genehmigen. 

> [!NOTE]
> Die in diesem Abschnitt beschriebenen Schritte beziehen sich auf Themen. Sie können ähnliche Schritte verwenden, um private Endpunkte für **Domänen** zu genehmigen. 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Ablehnen einer Verbindung mit einem privaten Endpunkt
Im folgenden Beispiel wird gezeigt, wie Sie einen privaten Endpunkt mit PowerShell ablehnen. Sie können die GUID für den privaten Endpunkt aus dem Ergebnis des vorherigen GET-Befehls abrufen. 

> [!NOTE]
> Die in diesem Abschnitt beschriebenen Schritte beziehen sich auf Themen. Sie können ähnliche Schritte verwenden, um private Endpunkte für **Domänen** abzulehnen. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01" ` 
    -Headers $Headers
```

Sie können die Verbindung auch dann genehmigen, wenn Sie über die API abgelehnt wurde. Wenn Sie das Azure-Portal verwenden, können Sie einen abgelehnten Endpunkt nicht genehmigen. 

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Konfigurieren von IP-Firewalleinstellungen finden Sie unter [Konfigurieren der IP-Firewall für Azure Event Grid-Themen oder -Domänen](configure-firewall.md).
* Informationen zum Beheben von Problemen mit der Netzwerkkonnektivität finden Sie unter [Beheben von Netzwerkverbindungsproblemen](troubleshoot-network-connectivity.md).
