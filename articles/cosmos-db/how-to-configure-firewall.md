---
title: Konfigurieren einer IP-Firewall für Ihr Azure Cosmos DB-Konto
description: Erfahren Sie, wie IP-Zugriffssteuerungsrichtlinien für die Firewallunterstützung in Azure Cosmos DB-Datenbankkonten konfiguriert werden.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 24ebc7eb4c9abc72a89419611e4b4b3fa2db88b4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241960"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Konfigurieren der IP-Firewall in Azure Cosmos DB

Sie können die in Ihrem Azure Cosmos DB-Konto gespeicherten Daten mithilfe von IP-Firewalls sichern. Azure Cosmos DB unterstützt die IP-basierte Zugriffssteuerung zur Unterstützung der Firewall für eingehende Verbindungen. Sie können über eine der folgenden Methoden eine IP-Firewall für das Azure Cosmos DB-Konto festlegen:

* Über das Azure-Portal
* Deklarativ durch Verwenden einer Azure Resource Manager-Vorlage
* Programmgesteuert über die Azure CLI oder mit Azure PowerShell durch Aktualisieren der Eigenschaft **ipRangeFilter**

## <a id="configure-ip-policy"></a> Konfigurieren einer IP-Firewall über das Azure-Portal

Um die IP-Zugriffssteuerungsrichtlinie im Azure-Portal festzulegen, wechseln Sie zur Seite für das Azure Cosmos DB-Konto, und wählen Sie im Navigationsmenü **Firewall und virtuelle Netzwerke** aus. Ändern Sie den Wert **Zugriff zulassen über** in **Ausgewählte Netzwerke**, und klicken Sie dann auf **Speichern**. 

![Screenshot: Öffnen der Seite „Firewall“ im Azure-Portal](./media/how-to-configure-firewall/azure-portal-firewall.png)

Wenn die IP-Zugriffssteuerung aktiviert ist, können Sie im Azure-Portal IP-Adressen, IP-Adressbereiche und Schalter angeben. Schalter ermöglichen den Zugriff auf andere Azure-Dienste und das Azure-Portal. Details zu diesen Schaltern finden Sie in den folgenden Abschnitten.

> [!NOTE]
> Nach dem Aktivieren der IP-Zugriffssteuerungsrichtlinie für Ihr Azure Cosmos DB-Konto werden alle Anforderungen für das Azure Cosmos DB-Konto von Computern abgelehnt, die nicht in der Liste der zugelassenen IP-Adressbereiche enthalten sind. Darüber hinaus wird das Durchsuchen der Azure Cosmos DB-Ressourcen über das Portal blockiert, um die Integrität der Zugriffssteuerung sicherzustellen.

### <a name="allow-requests-from-the-azure-portal"></a>Zulassen von Anforderungen über das Azure-Portal

Wenn Sie eine IP-Zugriffssteuerungsrichtlinie programmgesteuert aktivieren, müssen Sie der Eigenschaft **ipRangeFilter** die IP-Adresse für das Azure-Portal hinzufügen, damit der Zugriff weiterhin möglich ist. Die IP-Adressen des Portals lauten wie folgt:

|Region|IP-Adresse|
|------|----------|
|Deutschland|51.4.229.218|
|China|139.217.8.252|
|US Gov|52.244.48.71|
|Alle anderen Regionen|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Sie können den Zugriff auf das Azure-Portal ermöglichen, indem Sie wie im folgenden Screenshot gezeigt die Option **Zugriff über das Azure-Portal zulassen** aktivieren: 

![Screenshot: Aktivieren des Zugriffs auf das Azure-Portal](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Zulassen von Anforderungen von globalen Azure-Rechenzentren oder anderen Quellen in Azure

Wenn Sie in Diensten, die keine statische IP bereitstellen, z.B. Azure Stream Analytics oder Azure Functions, auf das Azure Cosmos DB-Konto zugreifen, können Sie dennoch den Zugriff über die IP-Firewall einschränken. Um den Zugriff auf das Azure Cosmos DB-Konto von diesen Diensten aus zuzulassen, fügen Sie die IP-Adresse 0.0.0.0 zur Liste der zugelassenen IP-Adressen hinzu. Die Adresse 0.0.0.0 beschränkt Anforderungen für das Azure Cosmos DB-Konto auf den IP-Adressbereich des Azure-Rechenzentrums. Mit dieser Einstellung ist der Zugriff auf das Azure Cosmos DB-Konto über andere IP-Adressbereiche nicht zulässig.

> [!NOTE]
> Über diese Option wird die Firewall so konfiguriert, dass alle von Azure ausgehenden Anforderungen zugelassen werden, einschließlich Anforderungen von den in Azure bereitgestellten Abonnements anderer Kunden. Die Liste der durch diese Option zugelassenen IP-Adressen ist umfangreich, sodass die Wirksamkeit einer Firewallrichtlinie eingeschränkt wird. Verwenden Sie diese Option nur, wenn die Anforderungen nicht von statischen IP-Adressen oder Subnetzen in virtuellen Netzwerken stammen. Durch das Aktivieren dieser Option wird automatisch der Zugriff über das Azure-Portal ermöglicht, da das Azure-Portal in Azure bereitgestellt wird.

Sie können den Zugriff auf das Azure-Portal ermöglichen, indem Sie wie im folgenden Screenshot gezeigt die Option **Verbindungen aus öffentlichen Azure-Rechenzentren akzeptieren** aktivieren: 

![Screenshot: Öffnen der Seite „Firewall“ im Azure-Portal](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Anforderungen über Ihre aktuelle IP-Adresse

Um die Entwicklung zu vereinfachen, unterstützt das Azure-Portal Sie dabei, die IP-Adresse Ihres Clientcomputers zu ermitteln und der Zulassungsliste hinzuzufügen. So können auf Ihrem Computer ausgeführte Apps auf Ihr Azure Cosmos DB-Konto zugreifen. 

Das Portal erkennt die Client-IP-Adresse automatisch. Es kann sich um die Client-IP-Adresse Ihres Computers oder um die IP-Adresse Ihres Netzwerkgateways handeln. Sie müssen diese IP-Adresse entfernen, bevor Sie Ihre Workloads in die Produktion überführen. 

Um Ihre aktuelle IP-Adresse zur IP-Liste hinzuzufügen, aktivieren Sie die Option **Meine aktuelle IP-Adresse hinzufügen**. Klicken Sie dann auf **Speichern**.

![Screenshot: Konfigurieren der Firewalleinstellungen für die aktuelle IP-Adresse](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Anforderungen über Clouddienste

In Azure ist es üblich, dass Clouddienste Dienstlogik der mittleren Ebene mithilfe von Azure Cosmos DB hosten. Um den Zugriff auf das Azure Cosmos DB-Konto über einen Clouddienst zu ermöglichen, muss die öffentliche IP-Adresse des Clouddiensts der Liste der zugelassenen IP-Adressen hinzugefügt werden, die dem Azure Cosmos DB-Konto zugeordnet ist. Dies erfolgt durch das [Konfigurieren der IP-Zugriffssteuerungsrichtlinie](#configure-ip-policy). So wird sichergestellt, dass alle Rolleninstanzen von Clouddiensten Zugriff auf das Azure Cosmos DB-Konto haben. 

Sie können die IP-Adressen für Ihre Clouddienste im Azure-Portal abrufen (siehe dazu den folgenden Screenshot).

![Screenshot mit der öffentlichen IP-Adresse für einen Clouddienst im Azure-Portal](./media/how-to-configure-firewall/public-ip-addresses.png)

Wenn Sie Ihren Clouddienst horizontal hochskalieren, indem Sie Rolleninstanzen hinzufügen, haben diese neuen Instanzen automatisch Zugriff auf das Azure Cosmos DB-Konto, da sie zum gleichen Clouddienst gehören.

### <a name="requests-from-virtual-machines"></a>Anforderungen über virtuelle Computer

Sie können auch [virtuelle Computer](https://azure.microsoft.com/services/virtual-machines/) oder [VM-Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) zum Hosten von Diensten der mittleren Ebene über Azure Cosmos DB verwenden. Um Ihr Cosmos DB-Konto so zu konfigurieren, dass virtuelle Computer (VMs) darauf zugreifen können, müssen Sie die öffentliche IP-Adresse der VM und/oder VM-Skalierungsgruppe als eine der zulässigen IP-Adressen für Ihr Azure Cosmos DB-Konto konfigurieren, indem Sie [die IP-Zugriffssteuerungsrichtlinie konfigurieren](#configure-ip-policy). 

Sie können die IP-Adressen für virtuelle Computer wie im folgenden Screenshot gezeigt im Azure-Portal abrufen:

![Screenshot mit einer öffentlichen IP-Adresse für einen virtuellen Computer im Azure-Portal](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Wenn Sie der Gruppe VM-Instanzen hinzufügen, erhalten diese automatisch Zugriff auf das Azure Cosmos DB-Konto.

### <a name="requests-from-the-internet"></a>Anforderungen aus dem Internet

Wenn Sie über einen Computer im Internet auf das Azure Cosmos DB-Konto zugreifen, muss die Client-IP-Adresse oder der Client-IP-Adressbereich des Computers der Liste der zugelassenen IP-Adressen für das Konto hinzugefügt werden.

## <a id="configure-ip-firewall-arm"></a>Konfigurieren einer IP-Firewall mithilfe einer Resource Manager-Vorlage

Zum Konfigurieren der Zugriffssteuerung für Ihr Azure Cosmos DB-Konto muss in der Resource Manager-Vorlage das Attribut **ipRangeFilter** mit einer Liste der zugelassenen IP-Adressbereiche angegeben werden. Fügen Sie der Vorlage beispielsweise folgenden JSON-Code hinzu:

```json
   {
     "apiVersion": "2015-04-08",
     "type": "Microsoft.DocumentDB/databaseAccounts",
     "kind": "GlobalDocumentDB",
     "name": "[parameters('databaseAccountName')]",
     "location": "[resourceGroup().location]",
     "properties": {
       "databaseAccountOfferType": "Standard",
       "name": "[parameters('databaseAccountName')]",
       "ipRangeFilter":"183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
     }
   }
```

## <a id="configure-ip-firewall-cli"></a>Konfigurieren einer IP-Zugriffssteuerungsrichtlinie über die Azure CLI

Der folgende Befehl zeigt, wie ein Azure Cosmos DB-Konto mit IP-Zugriffssteuerung erstellt wird: 

```azurecli-interactive

name="<Azure Cosmos DB account name>"
resourceGroupName="<Resource group name>"

az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

Führen Sie den folgenden Befehl aus, um die Firewalleinstellungen für ein vorhandenes Konto zu aktualisieren:

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="configure-ip-firewall-ps"></a>Konfigurieren einer IP-Zugriffssteuerungsrichtlinie mithilfe von PowerShell

Das folgende Skript zeigt, wie Sie ein Azure Cosmos DB-Konto mit IP-Zugriffssteuerung erstellen:

```azurepowershell-interactive

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

# Add local machine's IP address to firewall, InterfaceAlias is your Network Adapter's name
$ipRangeFilter = Get-NetIPConfiguration | Where-Object InterfaceAlias -eq "Ethernet 2" | Select-Object IPv4Address

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "ipRangeFilter"=$ipRangeFilter
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="troubleshoot-ip-firewall"></a>Behandeln von Problemen mit einer IP-Zugriffssteuerungsrichtlinie

Sie haben folgende Optionen, um Probleme mit einer IP-Zugriffssteuerungsrichtlinie zu behandeln: 

### <a name="azure-portal"></a>Azure-Portal 
Durch das Aktivieren einer IP-Zugriffssteuerungsrichtlinie für Ihr Azure Cosmos DB-Konto werden alle Anforderungen für das Konto von Computern außerhalb der Liste der zugelassenen IP-Adressbereiche blockiert. Um Vorgänge auf Portaldatenebene zu ermöglichen, z.B. das Durchsuchen von Containern und das Abfragen von Dokumenten, müssen Sie den Zugriff über das Azure-Portal im Bereich **Firewall** im Portal explizit zulassen.

### <a name="sdks"></a>SDKs 
Beim Zugriff auf Azure Cosmos DB-Ressourcen mithilfe von SDKs über Computer, die nicht in der Zulassungsliste enthalten sind, wird eine generische Antwort **403: Nicht zulässig** ohne weitere Details zurückgegeben. Überprüfen Sie die Liste der zugelassenen IP-Adressen für Ihr Konto, und stellen Sie sicher, dass die richtige Richtlinienkonfiguration auf das Cosmos DB-Konto angewendet wird. 

### <a name="source-ips-in-blocked-requests"></a>Überprüfen von Quell-IP-Adressen in blockierten Anforderungen
Aktivieren Sie die Diagnoseprotokollierung für Ihr Azure Cosmos DB-Konto. Diese Protokolle zeigen jede Anforderung und Antwort. Die firewallbezogenen Meldungen werden mit einem „403“-Rückgabecode protokolliert. Wenn Sie diese Meldungen filtern, können Sie die Quell-IP-Adressen für die blockierten Anforderungen anzeigen. Siehe [Diagnoseprotokollierung für Azure Cosmos DB](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Anforderungen über ein Subnetz mit einem aktiviertem Dienstendpunkt für Azure Cosmos DB
Bei Anforderungen über ein Subnetz in einem virtuellen Netzwerk, für das der Dienstendpunkt für Azure Cosmos DB aktiviert ist, werden die Identitäten des virtuellen Netzwerks und des Subnetzes an Azure Cosmos DB-Konten gesendet. Diese Anforderungen enthalten nicht die öffentliche IP-Adresse der Quelle und werden deshalb durch die IP-Filter abgelehnt. Um den Zugriff über bestimmte Subnetze in virtuellen Netzwerken zu ermöglichen, fügen Sie eine VNET-Zugriffssteuerungsliste hinzu. Informationen dazu finden Sie unter [Konfigurieren des VNET- und subnetzbasierten Zugriffs für Ihr Azure Cosmos DB-Konto](how-to-configure-vnet-service-endpoint.md). Es kann bis zu 15 Minuten dauern, bis die Firewallregeln angewandt werden.


## <a name="next-steps"></a>Nächste Schritte

Informationen zum Konfigurieren eines VNET-Dienstendpunkts für Ihr Azure Cosmos DB-Konto finden Sie in den folgenden Artikeln:

* [Zugreifen auf Azure Cosmos DB-Ressourcen über virtuelle Netzwerke und Subnetze](vnet-service-endpoint.md)
* [Konfigurieren des VNET- und subnetzbasierten Zugriffs für Ihr Azure Cosmos DB-Konto](how-to-configure-vnet-service-endpoint.md)

