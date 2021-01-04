---
title: Integrieren von Azure Service Bus in den Azure Private Link-Dienst
description: Erfahren Sie, wie Sie Azure Service Bus in den Azure Private Link-Dienst integrieren.
author: spelluru
ms.author: spelluru
ms.date: 10/07/2020
ms.topic: article
ms.openlocfilehash: 66de9a4ff65c73264257cb6f7f215fc15820c95f
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427146"
---
# <a name="allow-access-to-azure-service-bus-namespaces-via-private-endpoints"></a>Gewähren des Zugriffs auf Azure Service Bus-Namespaces über private Endpunkte
Mit Azure Private Link können Sie über einen **privaten Endpunkt** in Ihrem virtuellen Netzwerk auf Azure-Dienste wie Azure Service Bus, Azure Storage und Azure Cosmos DB sowie auf in Azure gehostete Kunden-/Partnerdienste zugreifen.

> [!IMPORTANT]
> Dieses Feature wird mit dem Tarif **Premium** von Azure Service Bus unterstützt. Weitere Informationen zum Premium-Tarif finden Sie im Artikel [Service Bus Premium- und Standard-Tarif für Messaging](service-bus-premium-messaging.md).

Ein privater Endpunkt ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet. Der private Endpunkt verwendet eine private IP-Adresse aus Ihrem VNET und bindet den Dienst dadurch in Ihr VNET ein. Der gesamte für den Dienst bestimmte Datenverkehr kann über den privaten Endpunkt geleitet werden. Es sind also keine Gateways, NAT-Geräte, ExpressRoute-/VPN-Verbindungen oder öffentlichen IP-Adressen erforderlich. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst wird über das Microsoft-Backbone-Netzwerk übertragen und dadurch vom öffentlichen Internet isoliert. Sie können eine Verbindung mit einer Instanz einer Azure-Ressource herstellen, was ein Höchstmaß an Granularität bei der Zugriffssteuerung ermöglicht.

Weitere Informationen finden Sie unter [Was ist Azure Private Link?](../private-link/private-link-overview.md).

>[!WARNING]
> Durch das Implementieren privater Endpunkte kann verhindert werden, dass andere Azure-Dienste mit Service Bus interagieren. Als Ausnahme können Sie bestimmten vertrauenswürdigen Diensten selbst dann den Zugriff auf Service Bus-Ressourcen erlauben, wenn private Endpunkte aktiviert sind. Eine Liste der vertrauenswürdigen Dienste finden Sie unter [Vertrauenswürdige Dienste](#trusted-microsoft-services).
>
> Die folgenden Microsoft-Dienste müssen sich in einem virtuellen Netzwerk befinden:
> - Azure App Service
> - Azure-Funktionen



## <a name="add-a-private-endpoint-using-azure-portal"></a>Hinzufügen eines privaten Endpunkts über das Azure-Portal

### <a name="prerequisites"></a>Voraussetzungen

Um einen Service Bus-Namespace in Azure Private Link zu integrieren, benötigen Sie die folgenden Entitäten oder Berechtigungen:

- Einen Service Bus-Namespace.
- Ein virtuelles Azure-Netzwerk
- Ein Subnetz in dem virtuellen Netzwerk Sie können das **Standardsubnetz** verwenden. 
- Berechtigungen vom Typ „Besitzer“ oder „Mitwirkender“ für den Service Bus-Namespace und für das virtuelle Netzwerk.

Der private Endpunkt und das virtuelle Netzwerk müssen sich in der gleichen Region befinden. Wenn Sie über das Portal eine Region für den privaten Endpunkt auswählen, wird automatisch nach virtuellen Netzwerken in dieser Region gefiltert. Der Service Bus-Namespace kann sich in einer anderen Region befinden. Außerdem verwendet der private Endpunkt eine private IP-Adresse in Ihrem virtuellen Netzwerk.

### <a name="steps"></a>steps

Wenn Sie bereits über einen Namespace verfügen, können Sie wie folgt einen privaten Endpunkt erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
2. Geben Sie auf der Suchleiste den Suchbegriff **Service Bus** ein.
3. Wählen Sie in der Liste den **Namespace** aus, dem Sie einen privaten Endpunkt hinzufügen möchten.
2. Wählen Sie im Menü links unter **Einstellungen** die Option **Netzwerk** aus. 

    > [!NOTE]
    > Die Registerkarte **Netzwerk** wird nur für Namespaces vom Typ **Premium** angezeigt.  
    
    Standardmäßig ist die Option **Ausgewählte Netzwerke** ausgewählt. Wenn Sie auf dieser Seite nicht mindestens eine IP-Firewallregel oder ein virtuelles Netzwerk hinzufügen, kann über das öffentliche Internet (mit dem Zugriffsschlüssel) auf den Namespace zugegriffen werden.

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Seite „Netzwerk“ – Standard" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Wenn Sie die Option **Alle Netzwerke** auswählen, akzeptiert der Service Bus-Namespace Verbindungen von beliebigen IP-Adressen (mit dem Zugriffsschlüssel). Die Standardeinstellung entspricht einer Regel, bei der der IP-Adressbereich 0.0.0.0/0 zulässig ist. 

    ![Firewall – Option „Alle Netzwerke“ ausgewählt](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
5. Um den Zugriff auf den Namespace über private Endpunkte zuzulassen, klicken Sie oben auf der Seite auf die Registerkarte **Private Endpunktverbindungen**.
6. Wählen Sie im oberen Seitenbereich die Schaltfläche **+ Privater Endpunkt** aus.

    ![Hinzufügen einer Schaltfläche für den privaten Endpunkt](./media/private-link-service/private-link-service-3.png)
7. Führen Sie auf der Seite **Grundlagen** die folgenden Schritte aus: 
    1. Wählen Sie das **Azure-Abonnement** aus, in dem Sie den privaten Endpunkt erstellen möchten. 
    2. Wählen Sie die **Ressourcengruppe** für die private Endpunktressource aus.
    3. Geben Sie einen **Namen** für den privaten Endpunkt ein. 
    5. Wählen Sie eine **Region** für den privaten Endpunkt aus. Ihr privater Endpunkt muss sich in derselben Region wie Ihr virtuelles Netzwerk befinden, kann aber in einer anderen Region als die Private Link-Ressource enthalten sein, mit der Sie eine Verbindung herstellen. 
    6. Klicken Sie auf **Weiter: Ressource >** unten auf der Seite aus.

        ![Erstellen des privaten Endpunkts: Seite „Grundlagen“](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Führen Sie auf der Seite **Ressource** die folgenden Schritte aus:
    1. Wenn Sie als Verbindungsmethode **Hiermit wird eine Verbindung mit einer Azure-Ressource im eigenen Verzeichnis hergestellt** auswählen, führen Sie die folgenden Schritte aus:   
        1. Wählen Sie das **Azure-Abonnement** aus, in dem Ihr **Service Bus-Namespace** vorhanden ist. 
        2. Wählen Sie für den **Ressourcentyp** **Microsoft.ServiceBus/namespaces** als **Ressourcentyp** aus.
        3. Wählen Sie als **Ressource** in der Dropdownliste einen Service Bus-Namespace aus. 
        4. Vergewissern Sie sich, dass die **Unterressource des Ziels** auf **Namespace** festgelegt ist.
        5. Klicken Sie auf **Weiter: Konfiguration >** unten auf der Seite aus. 
        
            ![Erstellen des privaten Endpunkts: Seite „Ressourcen“](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. Wenn Sie **Verbindung mit einer Azure-Ressource mithilfe einer Ressourcen-ID oder eines Alias herstellen** auswählen, führen Sie die folgenden Schritte aus:
        1. Geben Sie die **Ressourcen-ID** oder den **Alias** ein. Dabei kann es sich um die Ressourcen-ID oder den Alias handeln, den jemand für Sie freigegeben hat. Die einfachste Möglichkeit zum Abrufen dieser Ressourcen-ID besteht darin, im Azure-Portal zum Service Bus-Namespace zu navigieren und den Teil des URI ab `/subscriptions/` zu kopieren. Die folgende Abbildung zeigt ein Beispiel dafür. 
        2. Geben Sie für **Zielunterressource** **Namespace** ein. Dabei handelt es sich um den Unterressourcentyp, auf den der private Endpunkt zugreifen kann. 
        3. (Optional) Geben Sie eine **Anforderungsnachricht** ein. Der Ressourcenbesitzer sieht diese Nachricht beim Verwalten der Verbindung mit dem privaten Endpunkt. 
        4. Wählen Sie anschließend **Weiter: Konfiguration >** unten auf der Seite aus. 

            ![Erstellen des privaten Endpunkts: Verbinden mithilfe der Ressourcen-ID](./media/private-link-service/connect-resource-id.png)
9. Wählen Sie auf der Seite **Konfiguration** das Subnetz in einem virtuellen Netzwerk aus, in dem Sie den privaten Endpunkt bereitstellen möchten. 
    1. Wählen Sie ein **virtuelles Netzwerk** aus. Nur virtuelle Netzwerke im aktuell ausgewählten Abonnement und am aktuell ausgewählten Standort werden in der Dropdownliste aufgeführt. 
    2. Wählen Sie ein **Subnetz** innerhalb des ausgewählten virtuellen Netzwerks aus. 
    3. Klicken Sie auf **Weiter: Tags >** unten auf der Seite aus. 

        ![Erstellen des privaten Endpunkts: Seite „Konfiguration“](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Erstellen Sie auf der Seite **Tags** beliebige Tags (Namen und Werte), die Sie der privaten Endpunktressource zuordnen möchten. Wählen Sie dann am unteren Rand der Seite die Schaltfläche **Überprüfen und erstellen** aus. 
11. Überprüfen Sie auf der Seite **Überprüfen und erstellen** alle Einstellungen, und wählen Sie dann **Erstellen** aus, um den privaten Endpunkt zu erstellen.
    
    ![Erstellen des privaten Endpunkts: Seite “Überprüfen und erstellen“](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Vergewissern Sie sich, dass der private Endpunkt erstellt wurde. Wenn Sie Besitzer der Ressource sind und die Option **Hiermit wird eine Verbindung mit einer Azure-Ressource im eigenen Verzeichnis hergestellt** für die **Verbindungsmethode** ausgewählt haben, sollte die Endpunktverbindung **Automatisch genehmigt** sein. Wenn sie sich im Status **Ausstehend** befindet, lesen Sie den Abschnitt [Verwalten eines privaten Endpunkts mit dem Azure-Portal](#manage-private-endpoints-using-azure-portal).

    ![Privater Endpunkt ist erstellt](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="add-a-private-endpoint-using-powershell"></a>Hinzufügen eines privaten Endpunkts mit PowerShell
Im folgenden Beispiel wird gezeigt, wie Azure PowerShell verwendet wird, um eine private Endpunktverbindung mit einem Service Bus-Namespace herzustellen.

Der private Endpunkt und das virtuelle Netzwerk müssen sich in der gleichen Region befinden. Der Service Bus-Namespace kann sich in einer anderen Region befinden. Außerdem verwendet der private Endpunkt eine private IP-Adresse in Ihrem virtuellen Netzwerk.

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create premium service bus namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Sku @{name = "Premium"; capacity = 1} -Properties @{} -ResourceType "Microsoft.ServiceBus/namespaces" -

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```


## <a name="manage-private-endpoints-using-azure-portal"></a>Verwalten eines privaten Endpunkts mit dem Azure-Portal

Wenn Sie einen privaten Endpunkt erstellen, muss die Verbindung genehmigt werden. Wenn sich die Ressource, für die Sie einen privaten Endpunkt erstellen, in Ihrem Verzeichnis befindet, können Sie die Verbindungsanforderung genehmigen, sofern Sie über ausreichende Berechtigungen verfügen. Wenn Sie eine Verbindung mit einer Azure-Ressource in einem anderen Verzeichnis herstellen, müssen Sie warten, bis der Besitzer dieser Ressource Ihre Verbindungsanforderung genehmigt hat.

Es gibt vier Möglichkeiten für den Bereitstellungsstatus:

| Dienstaktion | Zustand des privaten Endpunkts des Dienstconsumers | BESCHREIBUNG |
|--|--|--|
| Keine | Ausstehend | Die Verbindung wurde manuell erstellt, und die Genehmigung des Besitzers der Private Link-Ressource steht aus. |
| Genehmigen | Genehmigt | Die Verbindung wurde automatisch oder manuell genehmigt und ist zur Verwendung bereit. |
| Reject | Rejected (Abgelehnt) | Die Verbindung wurde vom Besitzer der Private Link-Ressource abgelehnt. |
| Remove (Entfernen) | Getrennt | Die Verbindung wurde vom Besitzer der Private Link-Ressource entfernt, der private Endpunkt wird informativ und sollte zur Bereinigung gelöscht werden. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Genehmigt die Verbindung eines privaten Endpunkts, lehnt sie ab oder entfernt sie.

1. Melden Sie sich beim Azure-Portal an.
1. Geben Sie auf der Suchleiste den Suchbegriff **Service Bus** ein.
1. Wählen Sie den **Namespace** aus, den Sie verwalten möchten.
1. Wählen Sie die Registerkarte **Netzwerk** aus.
5. Wechseln Sie zu dem entsprechenden Abschnitt unten, der Ihrem gewünschten Vorgang entspricht: Genehmigen, Ablehnen oder Entfernen. 

### <a name="approve-a-private-endpoint-connection"></a>Genehmigen einer Verbindung mit einem privaten Endpunkt

1. Sollten ausstehende Verbindungen vorhanden sein, wird in der Liste eine Verbindung mit dem Bereitstellungsstatus **Ausstehend** angezeigt. 
2. Wählen Sie den **privaten Endpunkt** aus, den Sie genehmigen möchten.
3. Wählen Sie die Schaltfläche **Genehmigen** aus.

    ![Genehmigen des privaten Endpunkts](./media/private-link-service/private-endpoint-approve.png)
4. Geben Sie auf der Seite **Verbindung genehmigen** einen optionalen **Kommentar** ein, und wählen Sie dann **Ja** aus. Wenn Sie **Nein** auswählen, geschieht nichts. 

    ![Seite „Verbindung genehmigen“](./media/private-link-service/approve-connection-page.png)
5. Der Status der Verbindung sollte in der Liste in **Genehmigt** geändert werden. 

    ![Verbindungsstatus: Genehmigt](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>Ablehnen einer Verbindung mit einem privaten Endpunkt

1. Falls Sie eine Verbindung mit einem privaten Endpunkt ablehnen möchten, wählen Sie die Endpunktverbindung aus, und klicken Sie auf die Schaltfläche **Ablehnen**. Dies funktioniert sowohl für ausstehende Anforderungen als auch für bereits vorhandene Verbindungen, die vorher genehmigt wurden.

    ![Schaltfläche „Ablehnen“](./media/private-link-service/private-endpoint-reject.png)
2. Geben Sie auf der Seite **Verbindung ablehnen** einen optionalen Kommentar ein, und wählen Sie dann **Ja** aus. Wenn Sie **Nein** auswählen, geschieht nichts. 

    ![Seite „Verbindung ablehnen“](./media/private-link-service/reject-connection-page.png)
3. Der Status der Verbindung sollte in der Liste in **Abgelehnt** geändert werden. 

    ![Endpunkt abgelehnt](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>Entfernen einer Verbindung mit einem privaten Endpunkt

1. Um eine Verbindung mit einem privaten Endpunkt zu entfernen, wählen Sie sie in der Liste aus, und wählen Sie **Entfernen** auf der Symbolleiste aus. 

    ![Schaltfläche „Entfernen“](./media/private-link-service/remove-endpoint.png)
2. Wählen Sie auf der Seite **Verbindung löschen** **Ja** aus, um das Löschen des privaten Endpunkts zu bestätigen. Wenn Sie **Nein** auswählen, geschieht nichts. 

    ![Seite „Verbindung löschen“](./media/private-link-service/delete-connection-page.png)
3. Der Status sollte in **Getrennt** geändert werden. Anschließend wird der Endpunkt nicht mehr in der Liste angezeigt. 

## <a name="validate-that-the-private-link-connection-works"></a>Überprüfen, ob die Private Link-Verbindung funktioniert

Vergewissern Sie sich, dass Ressourcen innerhalb des virtuellen Netzwerks, in dem sich auch der private Endpunkt befindet, mit Ihrem Service Bus-Namespace eine Verbindung über eine private IP-Adresse herstellen, und dass die Integration in die private DNS-Zone korrekt ist.

Erstellen Sie zunächst einen virtuellen Computer. Eine entsprechende Anleitung finden Sie unter [Schnellstart: Erstellen eines virtuellen Windows-Computers im Azure-Portal](../virtual-machines/windows/quick-create-portal.md).

Gehen Sie auf der Registerkarte **Netzwerk** wie folgt vor: 

1. Geben Sie ein **virtuelles Netzwerk** und ein **Subnetz** an. Sie müssen das virtuelle Netzwerk auswählen, in dem Sie den privaten Endpunkt bereitgestellt haben.
2. Geben Sie eine **öffentliche IP-Ressource** an.
3. Wählen Sie für **NIC-Netzwerksicherheitsgruppe** die Option **Keine** aus.
4. Wählen Sie für den **Lastenausgleich** **Nein** aus.

Stellen Sie eine Verbindung mit der VM her, öffnen Sie die Befehlszeile, und führen Sie den folgenden Befehl aus:

```console
nslookup <service-bus-namespace-name>.servicebus.windows.net
```

Das Ergebnis sollte in etwa wie folgt aussehen. 

```console
Non-authoritative answer:
Name:    <service-bus-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Einschränkungen und Entwurfsaspekte

**Preise:** Preisinformationen finden Sie unter [Azure Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link/).

**Einschränkungen:**  Dieses Feature steht in allen öffentlichen Azure-Regionen zur Verfügung.

**Maximal zulässige Anzahl privater Endpunkte pro Service Bus-Namespace:** 120.

Weitere Informationen finden Sie unter [Was ist der Azure Private Link-Dienst? – Einschränkungen](../private-link/private-link-service-overview.md#limitations).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Azure Private Link](../private-link/private-link-service-overview.md).
- Weitere Informationen zu [Azure Service Bus](service-bus-messaging-overview.md).
