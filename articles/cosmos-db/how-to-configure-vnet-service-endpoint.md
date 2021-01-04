---
title: Konfigurieren des auf einem virtuellen Netzwerk basierenden Zugriffs für ein Azure Cosmos-Konto
description: Dieses Dokument beschreibt die Schritte, die zum Einrichten eines VNET-Dienstendpunkts für Azure Cosmos DB erforderlich sind.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d63d21f4c49e3c7aef035208477ac9fc79f2e51
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637182"
---
# <a name="configure-access-to-azure-cosmos-db-from-virtual-networks-vnet"></a>Konfigurieren des Zugriffs auf Azure Cosmos DB über virtuelle Netzwerke (VNET)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Sie können das Azure Cosmos DB-Konto so konfigurieren, dass der Zugriff nur aus einem bestimmten Subnetz des virtuellen Netzwerks (VNET) zugelassen wird. Wenn der [Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) aktiviert wird, um auf Azure Cosmos DB im Subnetz innerhalb eines virtuellen Netzwerks zuzugreifen, wird der Datenverkehr dieses Subnetzes mit der Identität des Subnetzes und des virtuellen Netzwerks an Azure Cosmos DB gesendet. Sobald der Azure Cosmos DB-Dienstendpunkt aktiviert ist, können Sie den Zugriff auf das Subnetz einschränken, indem Sie es Ihrem Azure Cosmos DB-Konto hinzufügen.

Standardmäßig ist ein Azure Cosmos DB-Konto über jede beliebige Quelle zugänglich, wenn die Anforderung zusammen mit einem gültigen Autorisierungstoken erfolgt. Wenn Sie mindestens ein Subnetz im VNET hinzufügen, erhalten nur Anforderungen aus dem jeweiligen Subnetz eine gültige Antwort. Anforderungen, die aus anderen Quellen stammen, erhalten die Fehlermeldung „403“ (Nicht zulässig). 

Sie können Azure Cosmos DB-Konten so konfigurieren, dass der Zugriff nur aus einem bestimmten Subnetz eines virtuellen Azure-Netzwerks (VNET) zugelassen wird. Um den Zugriff auf ein Azure Cosmos DB-Konto mit Verbindungen aus einem Subnetz in einem virtuellen Netzwerk einzuschränken, sind diese Schritte erforderlich:

1. Aktivieren Sie das Subnetz, um die Identität von Subnetz und virtuellem Netzwerk an Azure Cosmos DB zu senden. Aktivieren Sie dazu einen Dienstendpunkt für Azure Cosmos DB im entsprechenden Subnetz.

1. Fügen Sie im Azure Cosmos DB-Konto eine Regel hinzu, die dieses Subnetz als Quelle für den Zugriff auf das Konto angibt.

> [!NOTE]
> Wenn ein Dienstendpunkt für Ihr Azure Cosmos DB-Konto in einem Subnetz aktiviert ist, wechselt die Quelle des in Azure Cosmos DB eingehenden Datenverkehrs von einer öffentlichen IP-Adresse zu einem virtuellen Netzwerk und Subnetz. Der Wechsel des Datenverkehrs gilt für jedes Azure Cosmos DB-Konto, auf das über dieses Subnetz zugegriffen wird. Wenn Ihre Azure Cosmos DB-Konten über eine IP-basierte Firewall verfügen, um dieses Subnetz zuzulassen, entsprechen Anfragen von vom Dienst aktivierten Subnetzen nicht mehr den IP-Firewallregeln, und sie werden abgelehnt.
>
> Weitere Informationen finden Sie in den Schritten unter [Migrieren einer IP-Firewallregel zur Zugriffssteuerungsliste eines virtuellen Netzwerks](#migrate-from-firewall-to-vnet) in diesem Artikel.

Die folgenden Abschnitte beschreiben, wie Sie den VNET-Dienstendpunkt für ein Azure Cosmos DB-Konto konfigurieren.

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Konfigurieren eines Dienstendpunkts über das Azure-Portal

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurieren eines Dienstendpunkts für ein vorhandenes virtuelles Azure-Netzwerk und Subnetz

1. Suchen Sie auf dem Blatt **Alle Ressourcen** das Azure Cosmos DB-Konto, das Sie schützen möchten.

1. Wählen Sie aus dem Einstellungsmenü die Option **Firewalls und virtuelle Netzwerke**, und aktivieren Sie **Ausgewählte Netzwerke**, um den Zugriff über ausgewählte Netzwerke zuzulassen.

1. Um Zugriff auf das Subnetz eines vorhandenen virtuellen Netzwerks zu gewähren, wählen Sie unter **Virtuelle Netzwerke** die Option **Vorhandenes Azure Virtual Network hinzufügen** aus.

1. Wählen Sie das **Abonnement** aus, aus dem ein virtuelles Azure-Netzwerk hinzugefügt werden soll. Wählen Sie für **Virtuelle Netzwerke** und **Subnetze** die virtuellen Azure-Netzwerke und Subnetze aus, denen Sie Zugriff auf Ihr Azure Cosmos DB-Konto gewähren möchten. Klicken Sie als Nächstes auf **Aktivieren**, um die ausgewählten Netzwerke mit Dienstendpunkten für „Microsoft.AzureCosmosDB“ zu aktivieren. Wenn der Vorgang abgeschlossen ist, klicken Sie auf **Hinzufügen**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Auswählen des virtuellen Netzwerks und des Subnetzes":::

1. Nachdem das Azure Cosmos DB-Konto für den Zugriff aus einem virtuellen Netzwerk aktiviert wurde, lässt es nur den Datenverkehr aus diesem ausgewählten Subnetz zu. Das von Ihnen hinzugefügte virtuelle Netzwerk und das Subnetz sollten wie im folgenden Screenshot angezeigt werden:

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="Erfolgreich konfiguriertes virtuelles Netzwerk und Subnetz":::

> [!NOTE]
> Um VNET-Dienstendpunkte zu aktivieren, benötigen Sie die folgenden Abonnementberechtigungen:
>   * Abonnement mit virtuellem Netzwerk: Netzwerkmitwirkender
>   * Abonnement mit Azure Cosmos DB-Konto: DocumentDB-Kontomitwirkender
>   * Wenn sich Ihr virtuelles Netzwerk und Ihr Azure Cosmos DB-Konto in unterschiedlichen Abonnements befinden, stellen Sie sicher, dass für das Abonnement mit dem virtuellen Netzwerk ebenfalls ein `Microsoft.DocumentDB`-Ressourcenanbieter registriert ist. Informationen zum Registrieren eines Ressourcenanbieters finden Sie im Artikel [Azure-Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md).

Im Folgenden finden Sie die Schritte zum Registrieren eines Abonnements bei einem Ressourcenanbieter.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurieren eines Dienstendpunkts für ein neues virtuelles Azure-Netzwerk und Subnetz

1. Suchen Sie auf dem Blatt **Alle Ressourcen** das Azure Cosmos DB-Konto, das Sie schützen möchten.  

1. Wählen Sie aus dem Einstellungsmenü die Option **Firewalls und virtuelle Azure-Netzwerke**, und aktivieren Sie **Ausgewählte Netzwerke**, um den Zugriff über ausgewählte Netzwerke zuzulassen.  

1. Um Zugriff auf ein neues virtuelles Azure-Netzwerk zu gewähren, wählen Sie unter **Virtuelle Netzwerke** die Option **Neues virtuelles Netzwerk hinzufügen** aus.  

1. Geben Sie die Informationen ein, die zum Erstellen des neuen virtuellen Netzwerks erforderlich sind, und klicken Sie dann auf **Erstellen**. Das Subnetz wird mit einem für „Microsoft.AzureCosmosDB“ aktivierten Dienstendpunkt erstellt.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="Auswählen des virtuellen Netzwerks und Subnetzes für ein neues virtuelle Netzwerk":::

Wenn Ihr Azure Cosmos DB-Konto von anderen Azure-Diensten wie z. B. Azure Cognitive Search verwendet wird oder Stream Analytics oder Power BI darauf zugreifen, gewähren Sie Zugriff, indem Sie das Kontrollkästchen **Accept connections from within global Azure datacenters** (Zugriff aus globalen Azure-Rechenzentren zulassen) aktivieren.

Um sicherzustellen, dass Sie vom Portal aus auf Azure Cosmos DB-Metriken zugreifen können, müssen Sie Optionen für **Zugriff über das Azure-Portal zulassen** aktivieren. Weitere Informationen zu diesen Optionen finden Sie im Artikel [Konfigurieren einer IP-Firewall](how-to-configure-firewall.md). Nachdem Sie den Zugriff aktiviert haben, klicken Sie auf **Speichern**, um die Einstellungen zu speichern.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Entfernen eines virtuellen Netzwerks oder eines Subnetzes

1. Suchen Sie auf dem Blatt **Alle Ressourcen** das Azure Cosmos DB-Konto, dem Sie Dienstendpunkte zugewiesen haben.  

1. Wählen Sie aus Einstellungsmenü die Option **Firewalls und virtuelle Netzwerke** aus.  

1. Um eine Regel für ein virtuelles Netzwerk oder ein Subnetz zu entfernen, klicken Sie neben dem virtuellen Netzwerk oder dem Subnetz auf **...** , und wählen Sie **Entfernen** aus.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="Entfernen eines virtuellen Netzwerks":::

1. Klicken Sie zum Übernehmen der Änderungen auf **Speichern**.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Konfigurieren eines Dienstendpunkts über Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Führen Sie die folgenden Schritte aus, um über Azure PowerShell einen Dienstendpunkt für ein Azure Cosmos DB-Konto zu konfigurieren:  

1. Installieren Sie [Azure PowerShell](/powershell/azure/install-Az-ps), und [melden Sie sich an](/powershell/azure/authenticate-azureps).  

1. Aktivieren Sie den Dienstendpunkt für ein vorhandenes Subnetz eines Virtual Network.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

   > [!NOTE]
   > Wenn Sie PowerShell oder die Azure CLI verwenden, stellen Sie sicher, dass Sie die vollständige Liste der IP-Filter und Zugriffssteuerungslisten des virtuellen Netzwerks in den Parametern angeben, und nicht nur diejenigen, die hinzugefügt werden müssen.

1. Rufen Sie Informationen zum virtuellen Netzwerk ab.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Vorbereiten einer Cosmos DB-Regel für das virtuelle Netzwerk

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Aktualisieren Sie die Azure Cosmos DB-Kontoeigenschaften mit der neuen Virtual Network-Endpunktkonfiguration: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Ihr Azure Cosmos DB-Konto mit dem VNET-Dienstendpunkt aktualisiert wurde, den Sie im vorherigen Schritt konfiguriert haben:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Konfigurieren eines Dienstendpunkts über die Azure CLI

Azure Cosmos-Konten können für Dienstendpunkte konfiguriert werden, wenn sie zu einem späteren Zeitpunkt erstellt oder aktualisiert werden, sofern das Subnetz bereits für sie konfiguriert wurde. Dienstendpunkte können auch für das Cosmos-Konto aktiviert werden, wenn das Subnetz noch nicht für sie konfiguriert wurde. Die Endpunkte werden aktiv, wenn das Subnetz später konfiguriert wird. Diese Flexibilität ermöglicht Administratoren, die weder Zugriff auf das Cosmos-Konto noch auf virtuelle Netzwerkressourcen haben, Konfigurationen unabhängig voneinander vorzunehmen.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Erstellen Sie ein neues Cosmos-Konto, und verbinden Sie es mit einem Back-End-Subnetz eines neuen virtuellen Netzwerks.

In diesem Beispiel erstellen Sie das virtuelle Netzwerk und das Subnetz. Zum Zeitpunkt der Erstellung sind Dienstendpunkte für beide Netze aktiviert.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Verbinden eines Cosmos-Kontos mit einem Back-End-Subnetz und unabhängige Konfiguration

Dieses Beispiel soll veranschaulichen, wie Sie ein Azure Cosmos-Konto mit einem vorhandenen neuen virtuellen Netzwerk verbinden, in dem das Subnetz noch nicht für Dienstendpunkte konfiguriert wurde. Dies erfolgt mithilfe des `--ignore-missing-vnet-service-endpoint`-Parameters. Dadurch kann die Konfiguration für das Cosmos-Konto ohne Fehler abgeschlossen werden, bevor die Konfiguration für das Subnetz des virtuellen Netzwerks abgeschlossen ist. Sobald die Subnetzkonfiguration abgeschlossen ist, kann über das konfigurierte Subnetz auf das Cosmos-Konto zugegriffen werden.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a name="port-range-when-using-direct-mode"></a>Portbereich bei Verwendung des direkten Modus

Wenn Sie Dienstendpunkte mit einem Azure Cosmos-Konto über eine Verbindung im direkten Modus verwenden, muss der TCP-Portbereich von 10.000 bis 20.000 geöffnet sein.

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migrieren einer IP-Firewallregel zur Zugriffssteuerungsliste eines virtuellen Netzwerks

Führen Sie die folgenden Schritte aus, um ein Azure Cosmos DB-Konto von der Verwendung von IP-Firewallregeln zur Verwendung von VNET-Dienstendpunkten zu migrieren.

Sobald ein Azure Cosmos DB-Konto für einen Dienstendpunkt für ein Subnetz konfiguriert ist, werden Anforderungen von diesem Subnetz mit Informationen zu virtuellem Netzwerk und Subnetzquelle statt an eine öffentliche IP-Adresse an Azure Cosmos DB gesendet. Diese Anforderungen stimmen nicht mehr mit einem für das Azure Cosmos DB-Konto konfigurierten IP-Filter überein. Aus diesem Grund sind die folgenden Schritte erforderlich, um Ausfallzeiten zu vermeiden.

Bevor Sie fortfahren, aktivieren Sie den Azure Cosmos DB-Dienstendpunkt im virtuellen Netzwerk und Subnetz mit dem oben unter „Aktivieren Sie den Dienstendpunkt für ein vorhandenes Subnetz eines Virtual Network“ aufgeführten Schritt.

1. Abrufen der Informationen zu virtuellem Netzwerk und Subnetz:

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Bereiten Sie ein neues Virtual Network-Regelobjekt für das Azure Cosmos DB-Konto vor:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Aktualisieren Sie das Azure Cosmos DB-Konto, um den Dienstendpunktzugriff aus dem Subnetz zu aktivieren:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Wiederholen Sie die vorherigen Schritte für alle Azure Cosmos DB-Konten, auf die Sie aus dem Subnetz zugreifen.

1. Entfernen Sie die IP-Firewallregel für das Subnetz aus den Firewallregeln des Azure Cosmos DB-Kontos.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

Hier sind einige häufig gestellte Fragen zur Konfiguration des Zugriffs aus virtuellen Netzwerken:

### <a name="are-notebooks-and-mongocassandra-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>Sind Notebooks und Mongo/Cassandra Shell zurzeit mit Virtual Network-fähigen Konten kompatibel?

Zurzeit werden die Integrationen der [Mongo Shell](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) und der [Cassandra Shell](https://devblogs.microsoft.com/cosmosdb/announcing-native-cassandra-shell-preview/) im Daten-Explorer von Cosmos DB sowie der [Jupyter Notebook-Dienst](./cosmosdb-jupyter-notebooks.md) nicht mit VNET-Zugriff unterstützt. Diese Unterstützung befindet sich derzeit in der aktiven Entwicklung.

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Kann ich sowohl den VNET-Dienstendpunkt als auch eine IP-Zugriffssteuerungsrichtlinie für ein Azure Cosmos DB-Konto festlegen? 

Sie können sowohl den VNET-Dienstendpunkt als auch eine IP-Zugriffssteuerungsrichtlinie (auch als Firewall bekannt) für Ihr Azure Cosmos-Konto aktivieren. Diese beiden Funktionen ergänzen sich und gewährleisten zusammen die Isolation und Sicherheit Ihres Azure Cosmos DB-Kontos. Die Verwendung einer IP-Firewall stellt sicher, dass statische IP-Adressen auf Ihr Konto zugreifen können. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Wie schränke ich den Zugriff auf ein Subnetz innerhalb eines virtuellen Netzwerks ein? 

Um den Zugriff auf das Azure Cosmos DB-Konto über ein Subnetz einzuschränken, müssen Sie zwei Schritte ausführen. Lassen Sie zunächst zu, dass der Datenverkehr aus dem Subnetz seine Subnetz- und virtuelle Netzwerkidentität in Azure Cosmos DB überträgt. Aktivieren Sie dazu den Dienstendpunkt für Azure Cosmos DB im Subnetz. Fügen Sie als Nächstes im Azure Cosmos DB-Konto eine Regel hinzu, die dieses Subnetz als eine Quelle für den Zugriff auf das Konto angibt.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Werden Zugriffssteuerungslisten virtueller Netzwerke und die IP-Firewall Anforderungen oder Verbindungen zurückgewiesen? 

Wenn eine IP-Firewall oder Zugriffsregeln für virtuelle Netzwerke hinzugefügt werden, erhalten nur Anforderungen von zulässigen Quellen gültige Antworten. Andere Anforderungen werden mit einer „403“-Fehlermeldung (Nicht zulässig) zurückgewiesen. Es ist wichtig, die Firewall des Azure Cosmos DB-Kontos von einer Firewall auf Verbindungsebene zu unterscheiden. Die Quelle kann weiterhin eine Verbindung mit dem Dienst herstellen, und die Verbindungen selbst werden nicht zurückgewiesen.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Meine Anforderungen werden blockiert, wenn ich den Dienstendpunkt für Azure Cosmos DB im Subnetz aktiviere. Was ist passiert?

Wenn der Dienstendpunkt für Azure Cosmos DB in einem Subnetz aktiviert ist, wechselt die Quelle des Datenverkehrs, der das Konto erreicht, von der öffentlichen IP-Adresse zu einem virtuellen Netzwerk und Subnetz. Wenn Ihr Azure Cosmos DB-Konto über eine einzige IP-basierte Firewall verfügt, würde der Datenverkehr vom im Dienst aktivierten Subnetz nicht mehr den IP-Firewallregeln entsprechen und daher zurückgewiesen werden. Befolgen Sie die Anweisungen zur nahtlosen Migration von einer IP-basierten Firewall zu einer Zugriffssteuerung, die auf einem virtuellen Netzwerk basiert.

### <a name="are-additional-azure-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Sind zusätzliche Azure RBAC-Berechtigungen für Azure Cosmos-Konten mit VNET-Dienstendpunkten erforderlich?

Nachdem Sie die VNET-Dienstendpunkte einem Azure Cosmos-Konto hinzugefügt haben, benötigen Sie Zugriff auf die `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action`-Aktion für alle VNETs, die in Ihrem Azure Cosmos-Konto konfiguriert sind, um Änderungen an den Kontoeinstellungen vornehmen zu können. Diese Berechtigung ist erforderlich, da der Autorisierungsprozess den Zugriff auf Ressourcen (z.B. auf Datenbank- und virtuelle Netzwerkressourcen) überprüft, bevor Eigenschaften ausgewertet werden.
 
Die Autorisierung überprüft die Berechtigung für die VNET-Ressourcenaktion selbst dann, wenn der Benutzer die VNET-ACLs nicht mithilfe der Azure CLI angibt. Derzeit unterstützt die Steuerungsebene des Azure Cosmos-Kontos das Festlegen des vollständigen Status des Azure Cosmos-Kontos. Einer der Parameter für die Aufrufe der Steuerungsebene ist `virtualNetworkRules`. Wenn dieser Parameter nicht angegeben wird, führt die Azure CLI einen GET DATABASE-Aufruf aus, um die `virtualNetworkRules` abzurufen, und verwendet diesen Wert im Aktualisierungsaufruf.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Haben die virtuellen Netzwerke mit Peering auch Zugriff auf das Azure Cosmos DB-Konto? 
Nur virtuelle Netzwerke und ihre Subnetze, die dem Azure Cosmos DB-Konto hinzugefügt wurden, haben Zugriff. Ihre VNETs mit Peering können erst auf das Konto zugreifen, wenn die Subnetze in den virtuellen Netzwerken mit Peering dem Konto hinzugefügt wurden.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Wie viele Subnetze dürfen maximal auf ein einzelnes Cosmos DB-Konto zugreifen? 
Derzeit sind maximal 256 Subnetze für ein Azure Cosmos DB-Konto zulässig.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Kann ich den Zugriff über VPN und ExpressRoute aktivieren? 
Für den Zugriff auf das Azure Cosmos DB-Konto über eine lokale ExpressRoute-Instanz müssen Sie Microsoft-Peering aktivieren. Wenn Sie eine IP-Firewall oder Zugriffsregeln für das virtuelle Netzwerk eingerichtet haben, können Sie die öffentlichen IP-Adressen, die für das Microsoft-Peering verwendet werden, der IP-Firewall Ihres Azure Cosmos DB-Kontos hinzufügen, um lokalen Diensten Zugriff auf das Azure Cosmos DB-Konto zu gewähren. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Muss ich die Regeln der Netzwerksicherheitsgruppen (NSG) aktualisieren? 
NSG-Regeln werden verwendet, um die Konnektivität mit einem Subnetz mit virtuellem Netzwerk einzuschränken. Wenn Sie den Dienstendpunkt für Azure Cosmos DB dem Subnetz hinzufügen, muss die ausgehende Konnektivität in NSG nicht für Ihr Azure Cosmos DB-Konto geöffnet werden. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Sind Dienstendpunkte für alle VNETs verfügbar?
Nein, nur virtuelle Netzwerke mit Azure Resource Manager können einen Dienstendpunkt aktivieren. Klassische virtuelle Netzwerke unterstützen keine Dienstendpunkte.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Kann ich „Verbindungen aus öffentlichen Azure-Rechenzentren akzeptieren“, wenn der Zugriff auf den Dienstendpunkt für Azure Cosmos DB aktiviert ist?  
Dies ist nur erforderlich, wenn Sie den Zugriff auf Ihr Azure Cosmos DB-Konto über andere Azure-Erstanbieterdienste wie Azure Data Factory, Azure Cognitive Search oder einen beliebigen anderen Dienst ermöglichen möchten, der in einer bestimmten Azure-Region bereitgestellt wurde.

## <a name="next-steps"></a>Nächste Schritte

* Um eine Firewall für Azure Cosmos DB zu konfigurieren, lesen Sie den Artikel [Firewallunterstützung](how-to-configure-firewall.md).
