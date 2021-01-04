---
title: Konfigurieren einer IP-Firewall für Ihr Azure Cosmos DB-Konto
description: Hier erfahren Sie, wie IP-Zugriffssteuerungsrichtlinien für die Firewallunterstützung in Azure Cosmos-Datenbankkonten konfiguriert werden.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: ec4ec5b3ea522200562d05d1891f46e69c9e5ca8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092159"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Konfigurieren der IP-Firewall in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Zum Sichern der in Ihrem Konto gespeicherten Daten unterstützt Azure Cosmos DB ein auf Geheimnissen basierendes Autorisierungsmodell, das einen starken hashbasierten Nachrichtenauthentifizierungscode (HMAC) nutzt. Darüber hinaus unterstützt Azure Cosmos DB die IP-basierte Zugriffssteuerung zur Unterstützung der Firewall für eingehende Verbindungen. Dieses Modell ähnelt den Firewallregeln eines herkömmlichen Datenbanksystems und bietet zusätzliche Sicherheit für Ihr Konto. Mit Firewalls können Sie nun Ihr Azure Cosmos-Konto so konfigurieren, dass es nur über eine genehmigte Gruppe von Computern und/oder Clouddiensten zugänglich ist. Für den Zugriff auf Daten, die in Ihrer Azure Cosmos-Datenbank gespeichert sind, über diese genehmigten Gruppen von Computern und Diensten muss der Aufrufer weiterhin ein gültiges Autorisierungstoken vorlegen.

## <a name="ip-access-control"></a><a id="ip-access-control-overview"></a>IP-Zugriffssteuerung

Standardmäßig ist ein Azure Cosmos-Konto über das Internet zugänglich, solange die Anforderung zusammen mit einem gültigen Autorisierungstoken erfolgt. Zum Konfigurieren der auf IP-Richtlinien basierenden Zugriffssteuerung muss der Benutzer die Gruppe der IP-Adressen oder IP-Adressbereiche im CIDR-Format (Classless Inter-Domain Routing, klassenloses domänenübergreifendes Routing) angeben, die als Liste der zulässigen Client-IPs für den Zugriff auf ein bestimmtes Azure Cosmos-Konto aufgenommen wird. Nachdem diese Konfiguration angewendet wurde, erhalten alle Anforderungen von Computern, die nicht in dieser Zulassungsliste enthalten sind, die Antwort „403 (Nicht zulässig)“. Wenn Sie eine IP-Firewall verwenden, empfiehlt es sich, dem Azure-Portal Zugriff auf Ihr Konto zu gewähren. Zugriff ist erforderlich, um den Daten-Explorer verwenden zu können und um Metriken für Ihr Konto, die im Azure-Portal angezeigt werden, abzurufen. Bei Verwendung des Daten-Explorers müssen Sie nicht nur dem Azure-Portal den Zugriff auf Ihr Konto gestatten, sondern auch Ihre Firewalleinstellungen aktualisieren, um den Firewallregeln Ihre aktuelle IP-Adresse hinzuzufügen. Beachten Sie, dass die Verteilung von Änderungen an der Firewall bis zu 15 Minuten dauern kann.

Sie können die IP-basierte Firewall mit Subnetz- und VNET-Zugriffssteuerung kombinieren. Durch diese Kombination können Sie den Zugriff auf eine beliebigen Quelle beschränken, die eine öffentliche IP-Adresse aufweist bzw. zu einem bestimmten Subnetz im VNET gehört. Weitere Informationen zur Verwendung von Subnetz- und VNET-basierter Zugriffssteuerung finden Sie unter [Zugreifen auf Azure Cosmos DB-Ressourcen über virtuelle Netzwerke](./how-to-configure-vnet-service-endpoint.md).

Zusammenfassend lässt sich sagen, dass für den Zugriff auf ein Azure Cosmos-Konto immer ein Autorisierungstoken erforderlich ist. Wenn IP-Firewall- und VNET-Zugriffssteuerungslisten (ACLs) nicht eingerichtet sind, kann mit dem Authentifizierungstoken auf das Azure Cosmos-Konto zugegriffen werden. Nachdem IP-Firewall- oder VNET-ACLs oder beides für das Azure Cosmos-Konto eingerichtet wurden, erhalten nur Anforderungen, die aus den von Ihnen angegebenen Quellen stammen (und das Autorisierungstoken aufweisen), gültige Antworten. 

Sie können die in Ihrem Azure Cosmos DB-Konto gespeicherten Daten mithilfe von IP-Firewalls sichern. Azure Cosmos DB unterstützt die IP-basierte Zugriffssteuerung zur Unterstützung der Firewall für eingehende Verbindungen. Sie können über eine der folgenden Methoden eine IP-Firewall für das Azure Cosmos DB-Konto festlegen:

* Über das Azure-Portal
* Deklarativ durch Verwenden einer Azure Resource Manager-Vorlage
* Programmgesteuert über die Azure CLI oder mit Azure PowerShell durch Aktualisieren der Eigenschaft **ipRangeFilter**

## <a name="configure-an-ip-firewall-by-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Konfigurieren einer IP-Firewall über das Azure-Portal

Um die IP-Zugriffssteuerungsrichtlinie im Azure-Portal festzulegen, wechseln Sie zur Seite für das Azure Cosmos DB-Konto, und wählen Sie im Navigationsmenü **Firewall und virtuelle Netzwerke** aus. Ändern Sie den Wert **Zugriff zulassen über** in **Ausgewählte Netzwerke** , und klicken Sie dann auf **Speichern**.

:::image type="content" source="./media/how-to-configure-firewall/azure-portal-firewall.png" alt-text="Screenshot: Öffnen der Seite „Firewall“ im Azure-Portal":::

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

Sie können Anforderungen für den Zugriff auf das Azure-Portal ermöglichen, indem Sie wie im folgenden Screenshot gezeigt die Option **Zugriff über das Azure-Portal zulassen** auswählen:

:::image type="content" source="./media/how-to-configure-firewall/enable-azure-portal.png" alt-text="Screenshot: Aktivieren des Zugriffs auf das Azure-Portal":::

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Zulassen von Anforderungen von globalen Azure-Rechenzentren oder anderen Quellen in Azure

Wenn Sie in Diensten, die keine statische IP bereitstellen, z.B. Azure Stream Analytics oder Azure Functions, auf das Azure Cosmos DB-Konto zugreifen, können Sie dennoch den Zugriff über die IP-Firewall einschränken. Sie können den Zugriff über andere Quellen innerhalb von Azure ermöglichen, indem Sie wie im folgenden Screenshot gezeigt die Option **Verbindungen aus Azure-Rechenzentren akzeptieren** auswählen:

:::image type="content" source="./media/how-to-configure-firewall/enable-azure-services.png" alt-text="Screenshot: Akzeptieren von Verbindungen aus Azure-Rechenzentren":::

Wenn Sie diese Option aktivieren, wird die IP-Adresse `0.0.0.0` zur Liste der zulässigen IP-Adressen hinzugefügt. Die IP-Adresse `0.0.0.0` beschränkt Anforderungen für das Azure Cosmos DB-Konto auf den IP-Adressbereich des Azure-Rechenzentrums. Mit dieser Einstellung ist der Zugriff auf das Azure Cosmos DB-Konto über andere IP-Adressbereiche nicht zulässig.

> [!NOTE]
> Über diese Option wird die Firewall so konfiguriert, dass alle von Azure ausgehenden Anforderungen zugelassen werden, einschließlich Anforderungen von den in Azure bereitgestellten Abonnements anderer Kunden. Die Liste der durch diese Option zugelassenen IP-Adressen ist umfangreich, sodass die Wirksamkeit einer Firewallrichtlinie eingeschränkt wird. Verwenden Sie diese Option nur, wenn die Anforderungen nicht von statischen IP-Adressen oder Subnetzen in virtuellen Netzwerken stammen. Durch das Aktivieren dieser Option wird automatisch der Zugriff über das Azure-Portal ermöglicht, da das Azure-Portal in Azure bereitgestellt wird.

### <a name="requests-from-your-current-ip"></a>Anforderungen über Ihre aktuelle IP-Adresse

Um die Entwicklung zu vereinfachen, unterstützt das Azure-Portal Sie dabei, die IP-Adresse Ihres Clientcomputers zu ermitteln und der Zulassungsliste hinzuzufügen. So können auf Ihrem Computer ausgeführte Apps auf Ihr Azure Cosmos DB-Konto zugreifen.

Das Portal erkennt die Client-IP-Adresse automatisch. Es kann sich um die Client-IP-Adresse Ihres Computers oder um die IP-Adresse Ihres Netzwerkgateways handeln. Sie müssen diese IP-Adresse entfernen, bevor Sie Ihre Workloads in die Produktion überführen.

Um Ihre aktuelle IP-Adresse zur IP-Liste hinzuzufügen, aktivieren Sie die Option **Meine aktuelle IP-Adresse hinzufügen**. Klicken Sie dann auf **Speichern**.

:::image type="content" source="./media/how-to-configure-firewall/enable-current-ip.png" alt-text="Screenshot: Konfigurieren der Firewalleinstellungen für die aktuelle IP-Adresse":::

### <a name="requests-from-cloud-services"></a>Anforderungen über Clouddienste

In Azure ist es üblich, dass Clouddienste Dienstlogik der mittleren Ebene mithilfe von Azure Cosmos DB hosten. Um den Zugriff auf das Azure Cosmos DB-Konto über einen Clouddienst zu ermöglichen, muss die öffentliche IP-Adresse des Clouddiensts der Liste der zugelassenen IP-Adressen hinzugefügt werden, die dem Azure Cosmos DB-Konto zugeordnet ist. Dies erfolgt durch das [Konfigurieren der IP-Zugriffssteuerungsrichtlinie](#configure-ip-policy). So wird sichergestellt, dass alle Rolleninstanzen von Clouddiensten Zugriff auf das Azure Cosmos DB-Konto haben.

Sie können die IP-Adressen für Ihre Clouddienste im Azure-Portal abrufen (siehe dazu den folgenden Screenshot).

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses.png" alt-text="Screenshot: Öffentliche IP-Adresse für einen Clouddienst im Azure-Portal":::

Wenn Sie Ihren Clouddienst aufskalieren, indem Sie Rolleninstanzen hinzufügen, haben diese neuen Instanzen automatisch Zugriff auf das Azure Cosmos DB-Konto, da sie zum gleichen Clouddienst gehören.

### <a name="requests-from-virtual-machines"></a>Anforderungen über virtuelle Computer

Sie können auch [virtuelle Computer](https://azure.microsoft.com/services/virtual-machines/) oder [VM-Skalierungsgruppen](../virtual-machine-scale-sets/overview.md) zum Hosten von Diensten der mittleren Ebene über Azure Cosmos DB verwenden. Um Ihr Cosmos DB-Konto so zu konfigurieren, dass virtuelle Computer (VMs) darauf zugreifen können, müssen Sie die öffentliche IP-Adresse der VM und/oder VM-Skalierungsgruppe als eine der zulässigen IP-Adressen für Ihr Azure Cosmos DB-Konto konfigurieren, indem Sie [die IP-Zugriffssteuerungsrichtlinie konfigurieren](#configure-ip-policy).

Sie können die IP-Adressen für virtuelle Computer wie im folgenden Screenshot gezeigt im Azure-Portal abrufen:

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses-dns.png" alt-text="Screenshot: Öffentliche IP-Adresse für einen virtuellen Computer im Azure-Portal":::

Wenn Sie der Gruppe VM-Instanzen hinzufügen, erhalten diese automatisch Zugriff auf das Azure Cosmos DB-Konto.

### <a name="requests-from-the-internet"></a>Anforderungen aus dem Internet

Wenn Sie über einen Computer im Internet auf das Azure Cosmos DB-Konto zugreifen, muss die Client-IP-Adresse oder der Client-IP-Adressbereich des Computers der Liste der zugelassenen IP-Adressen für das Konto hinzugefügt werden.

## <a name="configure-an-ip-firewall-by-using-a-resource-manager-template"></a><a id="configure-ip-firewall-arm"></a>Konfigurieren einer IP-Firewall mithilfe einer Resource Manager-Vorlage

Zum Konfigurieren der Zugriffssteuerung für Ihr Azure Cosmos DB-Konto muss in der Resource Manager-Vorlage das Attribut **ipRules** mit einem Array der zugelassenen IP-Adressbereiche angegeben sein. Wenn Sie die IP-Firewall für ein bereits bereitgestelltes Cosmos-Konto konfigurieren, stellen Sie sicher, dass das `locations`-Array mit dem aktuell bereitgestellten übereinstimmt. Sie können nicht gleichzeitig das `locations`-Array und andere Eigenschaften ändern. Weitere Informationen und Beispiele zu Azure Resource Manager-Vorlagen für Azure Cosmos DB finden Sie unter [Azure Resource Manager-Vorlagen für Azure Cosmos DB](./templates-samples-sql.md).

> [!IMPORTANT]
> Die Eigenschaft **ipRules** wurde in API-Version 2020-04-01 eingeführt. In früheren Versionen wurde stattdessen die Eigenschaft **ipRangeFilter** verfügbar gemacht: eine Liste mit durch Trennzeichen getrennten IP-Adressen.

Das folgende Beispiel zeigt, wie die Eigenschaft **ipRules** in der API-Version 2020-04-01 oder höher verfügbar gemacht wird:

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2020-04-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRules": [
      {
        "ipAddressOrRange": "40.76.54.131"
      },
      {
        "ipAddressOrRange": "52.176.6.30"
      },
      {
        "ipAddressOrRange": "52.169.50.45"
      },
      {
        "ipAddressOrRange": "52.187.184.26"
      }
    ]
  }
}
```

Hier sehen Sie ein Beispiel für eine beliebige API-Version vor 2020-04-01:

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRangeFilter":"40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
  }
}
```

## <a name="configure-an-ip-access-control-policy-by-using-the-azure-cli"></a><a id="configure-ip-firewall-cli"></a>Konfigurieren einer IP-Zugriffssteuerungsrichtlinie über die Azure CLI

Der folgende Befehl zeigt, wie ein Azure Cosmos DB-Konto mit IP-Zugriffssteuerung erstellt wird:

```azurecli-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
ipRangeFilter='192.168.221.17,183.240.196.255,40.76.54.131'

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False \
    --ip-range-filter $ipRangeFilter
```

## <a name="configure-an-ip-access-control-policy-by-using-powershell"></a><a id="configure-ip-firewall-ps"></a>Konfigurieren einer IP-Zugriffssteuerungsrichtlinie mithilfe von PowerShell

Das folgende Skript zeigt, wie Sie ein Azure Cosmos DB-Konto mit IP-Zugriffssteuerung erstellen:

```azurepowershell-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$ipRules = @("192.168.221.17","183.240.196.255","40.76.54.131")

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0; "isZoneRedundant"=False },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=False }
)

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "ipRules"=$ipRules
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2020-04-01" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Behandeln von Problemen mit einer IP-Zugriffssteuerungsrichtlinie

Sie haben folgende Optionen, um Probleme mit einer IP-Zugriffssteuerungsrichtlinie zu behandeln:

### <a name="azure-portal"></a>Azure-Portal

Durch das Aktivieren einer IP-Zugriffssteuerungsrichtlinie für Ihr Azure Cosmos DB-Konto werden alle Anforderungen für das Konto von Computern außerhalb der Liste der zugelassenen IP-Adressbereiche blockiert. Um Vorgänge auf Portaldatenebene zu ermöglichen, z.B. das Durchsuchen von Containern und das Abfragen von Dokumenten, müssen Sie den Zugriff über das Azure-Portal im Bereich **Firewall** im Portal explizit zulassen.

### <a name="sdks"></a>SDKs

Beim Zugriff auf Azure Cosmos DB-Ressourcen mithilfe von SDKs über Computer, die nicht in der Zulassungsliste enthalten sind, wird eine generische Antwort **403: Nicht zulässig** ohne weitere Details zurückgegeben. Überprüfen Sie die Liste der zugelassenen IP-Adressen für Ihr Konto, und stellen Sie sicher, dass die richtige Richtlinienkonfiguration auf das Cosmos DB-Konto angewendet wird.

### <a name="source-ips-in-blocked-requests"></a>Überprüfen von Quell-IP-Adressen in blockierten Anforderungen

Aktivieren Sie die Diagnoseprotokollierung für Ihr Azure Cosmos DB-Konto. Diese Protokolle zeigen jede Anforderung und Antwort. Die firewallbezogenen Meldungen werden mit einem „403“-Rückgabecode protokolliert. Wenn Sie diese Meldungen filtern, können Sie die Quell-IP-Adressen für die blockierten Anforderungen anzeigen. Siehe [Diagnoseprotokollierung für Azure Cosmos DB](./monitor-cosmos-db.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Anforderungen über ein Subnetz mit einem aktiviertem Dienstendpunkt für Azure Cosmos DB

Bei Anforderungen über ein Subnetz in einem virtuellen Netzwerk, für das der Dienstendpunkt für Azure Cosmos DB aktiviert ist, werden die Identitäten des virtuellen Netzwerks und des Subnetzes an Azure Cosmos DB-Konten gesendet. Diese Anforderungen enthalten nicht die öffentliche IP-Adresse der Quelle und werden deshalb durch die IP-Filter abgelehnt. Um den Zugriff über bestimmte Subnetze in virtuellen Netzwerken zu ermöglichen, fügen Sie eine VNET-Zugriffssteuerungsliste hinzu. Informationen dazu finden Sie unter [Konfigurieren des VNET- und subnetzbasierten Zugriffs für Ihr Azure Cosmos DB-Konto](how-to-configure-vnet-service-endpoint.md). Es kann bis zu 15 Minuten dauern, bis die Firewallregeln angewandt werden.

### <a name="private-ip-addresses-in-list-of-allowed-addresses"></a>Private IP-Adressen in der Liste zulässiger Adressen

Wenn die Liste zulässiger Adressen private IP-Adressen enthält, kann ein Azure Cosmos-Konto nicht erstellt oder aktualisiert werden. Stellen Sie sicher, dass in der Liste keine privaten IP-Adressen angegeben sind.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Konfigurieren eines VNET-Dienstendpunkts für Ihr Azure Cosmos DB-Konto finden Sie in den folgenden Artikeln:

* [Zugreifen auf Azure Cosmos DB-Ressourcen über virtuelle Netzwerke und Subnetze](how-to-configure-vnet-service-endpoint.md)
* [Konfigurieren des VNET- und subnetzbasierten Zugriffs für Ihr Azure Cosmos DB-Konto](how-to-configure-vnet-service-endpoint.md)