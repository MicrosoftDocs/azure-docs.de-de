---
title: Konfigurieren des VNET- und subnetzbasierten Zugriffs für Ihr Azure Cosmos DB-Konto
description: Dieses Dokument beschreibt die Schritte, die zum Einrichten eines VNET-Dienstendpunkts für Azure Cosmos DB erforderlich sind.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 375e79d2fe70e0988d8c58997a746f77b21d7f50
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242004"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Konfigurieren des Zugriffs über virtuelle Netzwerke (VNET)

Sie können Azure Cosmos DB-Konten so konfigurieren, dass der Zugriff nur aus einem bestimmten Subnetz eines virtuellen Azure-Netzwerks (VNET) zugelassen wird. Um den Zugriff auf ein Azure Cosmos DB-Konto mit Verbindungen aus einem Subnetz in einem virtuellen Netzwerk einzuschränken, sind diese Schritte erforderlich:
 
1. Aktivieren Sie das Subnetz, um die Identität von Subnetz und virtuellem Netzwerk an Azure Cosmos DB zu senden. Aktivieren Sie dazu einen Dienstendpunkt für Azure Cosmos DB im entsprechenden Subnetz.

1. Fügen Sie im Azure Cosmos DB-Konto eine Regel hinzu, die dieses Subnetz als Quelle für den Zugriff auf das Konto angibt.

> [!NOTE]
> Wenn ein Dienstendpunkt für Ihr Azure Cosmos DB-Konto in einem Subnetz aktiviert ist, wechselt die Quelle des in Azure Cosmos DB eingehenden Datenverkehrs von einer öffentlichen IP-Adresse zu einem virtuellen Netzwerk und Subnetz. Der Wechsel des Datenverkehrs gilt für jedes Azure Cosmos DB-Konto, auf das über dieses Subnetz zugegriffen wird. Wenn Ihre Azure Cosmos DB-Konten über eine IP-basierte Firewall verfügen, um dieses Subnetz zuzulassen, entsprechen Anfragen von vom Dienst aktivierten Subnetzen nicht mehr den IP-Firewallregeln, und sie werden abgelehnt. 
>
> Weitere Informationen finden Sie in den Schritten unter [Migrieren einer IP-Firewallregel zur Zugriffssteuerungsliste eines virtuellen Netzwerks](#migrate-from-firewall-to-vnet) in diesem Artikel. 

Die folgenden Abschnitte beschreiben, wie Sie den VNET-Dienstendpunkt für ein Azure Cosmos DB-Konto konfigurieren.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a id="configure-using-portal"></a>Konfigurieren eines Dienstendpunkts über das Azure-Portal

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurieren eines Dienstendpunkts für ein vorhandenes virtuelles Azure-Netzwerk und Subnetz

1. Suchen Sie auf dem Blatt **Alle Ressourcen** das Azure Cosmos DB-Konto, das Sie schützen möchten.

1. Wählen Sie aus dem Einstellungsmenü die Option **Firewalls und virtuelle Netzwerke**, und aktivieren Sie **Ausgewählte Netzwerke**, um den Zugriff über ausgewählte Netzwerke zuzulassen.

1. Um Zugriff auf das Subnetz eines vorhandenen virtuellen Netzwerks zu gewähren, wählen Sie unter **Virtuelle Netzwerke** die Option **Vorhandenes Azure Virtual Network hinzufügen** aus.

1. Wählen Sie das **Abonnement** aus, aus dem ein virtuelles Azure-Netzwerk hinzugefügt werden soll. Wählen Sie für **Virtuelle Netzwerke** und **Subnetze** die virtuellen Azure-Netzwerke und Subnetze aus, denen Sie Zugriff auf Ihr Azure Cosmos DB-Konto gewähren möchten. Klicken Sie als Nächstes auf **Aktivieren**, um die ausgewählten Netzwerke mit Dienstendpunkten für „Microsoft.AzureCosmosDB“ zu aktivieren. Wenn der Vorgang abgeschlossen ist, klicken Sie auf **Hinzufügen**. 

   ![Auswählen des Virtual Network und des Subnetzes](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. Nachdem das Azure Cosmos DB-Konto für den Zugriff aus einem virtuellen Netzwerk aktiviert wurde, lässt es nur den Datenverkehr aus diesem ausgewählten Subnetz zu. Das von Ihnen hinzugefügte virtuelle Netzwerk und das Subnetz sollten wie im folgenden Screenshot angezeigt werden:

   ![Erfolgreich konfiguriertes virtuelles Netzwerk und Subnetz](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Um VNET-Dienstendpunkte zu aktivieren, benötigen Sie die folgenden Abonnementberechtigungen:
>   * Abonnement mit virtuellem Netzwerk: Netzwerkmitwirkender
>   * Abonnement mit Azure Cosmos DB-Konto: DocumentDB-Kontomitwirkender
>   * Wenn sich Ihr virtuelles Netzwerk und Ihr Azure Cosmos DB-Konto in unterschiedlichen Abonnements befinden, stellen Sie sicher, dass für das Abonnement mit dem virtuellen Netzwerk ebenfalls ein `Microsoft.DocumentDB`-Ressourcenanbieter registriert ist. Informationen zum Registrieren eines Ressourcenanbieters finden Sie im Artikel [Azure-Ressourcenanbieter und -typen](../azure-resource-manager/resource-manager-supported-services.md). 

Im Folgenden finden Sie die Schritte zum Registrieren eines Abonnements bei einem Ressourcenanbieter.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurieren eines Dienstendpunkts für ein neues virtuelles Azure-Netzwerk und Subnetz

1. Suchen Sie auf dem Blatt **Alle Ressourcen** das Azure Cosmos DB-Konto, das Sie schützen möchten.  

1. Wählen Sie aus dem Einstellungsmenü die Option **Firewalls und virtuelle Azure-Netzwerke**, und aktivieren Sie **Ausgewählte Netzwerke**, um den Zugriff über ausgewählte Netzwerke zuzulassen.  

1. Um Zugriff auf ein neues virtuelles Azure-Netzwerk zu gewähren, wählen Sie unter **Virtuelle Netzwerke** die Option **Neues virtuelles Netzwerk hinzufügen** aus.  

1. Geben Sie die Informationen ein, die zum Erstellen des neuen virtuellen Netzwerks erforderlich sind, und klicken Sie dann auf **Erstellen**. Das Subnetz wird mit einem für „Microsoft.AzureCosmosDB“ aktivierten Dienstendpunkt erstellt.

   ![Auswählen des virtuellen Netzwerks und Subnetzes für ein neues virtuelle Netzwerk](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Wenn Ihr Azure Cosmos DB-Konto von anderen Azure-Diensten wie z.B. Azure Search verwendet wird oder Stream Analytics oder Power BI darauf zugreifen, gewähren Sie den Zugriff, indem Sie das Kontrollkästchen **Zugriff aus öffentlichen Azure-Rechenzentren zulassen** aktivieren.

Um sicherzustellen, dass Sie vom Portal aus auf Azure Cosmos DB-Metriken zugreifen können, müssen Sie Optionen für **Zugriff über das Azure-Portal zulassen** aktivieren. Weitere Informationen zu diesen Optionen finden Sie im Artikel [Konfigurieren einer IP-Firewall](how-to-configure-firewall.md). Nachdem Sie den Zugriff aktiviert haben, klicken Sie auf **Speichern**, um die Einstellungen zu speichern.

## <a id="remove-vnet-or-subnet"></a>Entfernen eines virtuellen Netzwerks oder eines Subnetzes 

1. Suchen Sie auf dem Blatt **Alle Ressourcen** das Azure Cosmos DB-Konto, dem Sie Dienstendpunkte zugewiesen haben.  

2. Wählen Sie aus Einstellungsmenü die Option **Firewalls und virtuelle Netzwerke** aus.  

3. Um eine Regel für ein virtuelles Netzwerk oder ein Subnetz zu entfernen, klicken Sie neben dem virtuellen Netzwerk oder dem Subnetz auf **...** , und wählen Sie **Entfernen** aus.

   ![Entfernen eines Virtual Network](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Klicken Sie zum Übernehmen der Änderungen auf **Speichern**.

## <a id="configure-using-powershell"></a>Konfigurieren eines Dienstendpunkts über Azure PowerShell

> [!NOTE]
> Wenn Sie PowerShell oder die Azure CLI verwenden, stellen Sie sicher, dass Sie die vollständige Liste der IP-Filter und Zugriffssteuerungslisten des virtuellen Netzwerks in den Parametern angeben, und nicht nur diejenigen, die hinzugefügt werden müssen.

Führen Sie die folgenden Schritte aus, um über Azure PowerShell einen Dienstendpunkt für ein Azure Cosmos DB-Konto zu konfigurieren:  

1. Installieren Sie [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps), und [melden Sie sich an](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Aktivieren Sie den Dienstendpunkt für ein vorhandenes Subnetz eines Virtual Network.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
   ```

1. Rufen Sie Informationen zum virtuellen Netzwerk ab.

   ```powershell
   $vnProp = Get-AzVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Rufen Sie Eigenschaften des Azure Cosmos DB-Kontos ab, indem Sie das folgende Cmdlet ausführen:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Initialisieren Sie die Variablen für die spätere Verwendung. Richten Sie alle Variablen aus der vorhandenen Kontodefinition ein.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $virtualNetworkRules = @(@{
      id = "$($vnProp.Id)/subnets/$sname";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Aktualisieren Sie die Azure Cosmos DB-Kontoeigenschaften mit der neuen Konfiguration, indem Sie die folgenden Cmdlets ausführen: 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Ihr Azure Cosmos DB-Konto mit dem VNET-Dienstendpunkt aktualisiert wurde, den Sie im vorherigen Schritt konfiguriert haben:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Konfigurieren eines Dienstendpunkts über die Azure CLI 

1. Aktivieren Sie den Dienstendpunkt für ein Subnetz in einem virtuellen Netzwerk.

1. Aktualisieren Sie das vorhandene Azure Cosmos DB-Konto mit Zugriffssteuerungslisten (ACLs) für das Subnetz.

   ```azurecli-interactive

   name="<Azure Cosmos DB account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
    --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Erstellen Sie ein neues Azure Cosmos DB-Konto mit Subnetz-ACLs.

   ```azurecli-interactive
   az cosmosdb create \
    --name $name \
    --kind GlobalDocumentDB \
    --resource-group $resourceGroupName \
    --max-interval 10 \
    --max-staleness-prefix 200 \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/default"
   ```

## <a id="migrate-from-firewall-to-vnet"></a>Migrieren einer IP-Firewallregel zur Zugriffssteuerungsliste eines virtuellen Netzwerks 

Die folgenden Schritte sind nur für Azure Cosmos DB-Konten mit vorhandenen IP-Firewallregeln erforderlich, die ein Subnetz zulassen, wenn Sie anstelle einer Firewallregel VNET- und subnetzbasierte Zugriffssteuerungslisten verwenden möchten.

Sobald ein Dienstendpunkt für das Azure Cosmos DB-Konto für ein Subnetz aktiviert wurde, werden die Anforderungen mit einer Quelle gesendet, die anstelle einer öffentlichen IP-Adresse Informationen zu einem virtuellen Netzwerk und Subnetz enthält. Diese Anforderungen entsprechen keinem IP-Filter. Dieser Quellenwechsel erfolgt für alle Azure Cosmos DB-Konten, auf die über das Subnetz mit aktiviertem Dienstendpunkt zugegriffen wird. Um eine Downtime zu vermeiden, führen Sie die folgenden Schritte aus:

1. Rufen Sie Eigenschaften des Azure Cosmos DB-Kontos ab, indem Sie das folgende Cmdlet ausführen:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Initialisieren Sie die Variablen für die spätere Verwendung. Richten Sie alle Variablen aus der vorhandenen Kontodefinition ein. Fügen Sie die ACL des virtuellen Netzwerks zu allen Azure Cosmos DB-Konten hinzu, auf die über das Subnetz mit dem Flag `ignoreMissingVNetServiceEndpoint` zugegriffen wird.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $subnetID = "Subnet ARM URL" e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{
      id = $subnetID;
      ignoreMissingVNetServiceEndpoint = "True";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Aktualisieren Sie die Azure Cosmos DB-Kontoeigenschaften mit der neuen Konfiguration, indem Sie die folgenden Cmdlets ausführen:

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. Wiederholen Sie die Schritte 1 bis 3 für alle Azure Cosmos DB-Konten, auf die Sie aus dem Subnetz zugreifen.

1.  Warten Sie 15 Minuten, und aktualisieren Sie dann das Subnetz, um den Dienstendpunkt zu aktivieren.

1.  Aktivieren Sie den Dienstendpunkt für ein vorhandenes Subnetz eines Virtual Network.

    ```powershell
    $rgname= "<Resource group name>"
    $vnName = "<virtual network name>"
    $sname = "<Subnet name>"
    $subnetPrefix = "<Subnet address range>"

    Get-AzVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
    ```

1. Entfernen Sie die IP-Firewallregel für das Subnetz.

## <a name="next-steps"></a>Nächste Schritte

* Um eine Firewall für Azure Cosmos DB zu konfigurieren, lesen Sie den Artikel [Firewallunterstützung](firewall-support.md).
