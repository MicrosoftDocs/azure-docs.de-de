---
title: 'Azure ExpressRoute: Konfigurieren von ExpressRoute Direct: Befehlszeilenschnittstelle (CLI)'
description: Hier erfahren Sie, wie Sie Azure ExpressRoute Direct mit der Azure-Befehlszeilenschnittstelle so konfigurieren, dass eine direkte Verbindung mit dem globalen Netzwerk von Microsoft hergestellt wird.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/28/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7931ad9e55d62d5fa2b1828d276e56f7c8cd02e1
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286489"
---
# <a name="configure-expressroute-direct-by-using-the-azure-cli"></a>Konfigurieren von ExpressRoute Direct mit der Azure-Befehlszeilenschnittstelle

ExpressRoute Direct ermöglicht das Herstellen einer direkten Verbindung mit dem globalen Netzwerk von Microsoft – dank strategisch verteilten Peeringstandorten auf der ganzen Welt. Weitere Informationen über ExpressRoute finden Sie unter [Informationen zu ExpressRoute Direct](expressroute-erdirect-about.md).

## <a name="create-the-resource"></a><a name="resources"></a>Erstellen der Ressource

1. Melden Sie sich an Azure an, und wählen Sie das Abonnement aus, in dem ExpressRoute enthalten ist. Die ExpressRoute Direct-Ressource und Ihre ExpressRoute-Leitungen müssen sich in demselben Abonnement befinden. Führen Sie in der Azure CLI die folgenden Befehle aus:

   ```azurecli
   az login
   ```

   Überprüfen Sie die Abonnements für das Konto: 

   ```azurecli
   az account list 
   ```

   Wählen Sie das Abonnement aus, für das eine ExpressRoute-Verbindung erstellt werden soll:

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

2. Registrieren Sie Ihr Abonnement erneut bei Microsoft.Network, um auf die APIs expressrouteportslocation und expressrouteport zugreifen zu können.

   ```azurecli
   az provider register --namespace Microsoft.Network
   ```
3. Listen Sie alle Standorte auf, an denen ExpressRoute Direct unterstützt wird:
    
   ```azurecli
   az network express-route port location list
   ```

   **Beispielausgabe**
  
   ```output
   [
   {
    "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
    "location": null,
    "name": "Equinix-Ashburn-DC2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA3",
    "location": null,
    "name": "Equinix-Dallas-DA3",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "111 8th Avenue, New York, NY 10011",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-New-York-NY5",
    "location": null,
    "name": "Equinix-New-York-NY5",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "11 Great Oaks Blvd, SV1, San Jose, CA 95119",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-SV1",
    "location": null,
    "name": "Equinix-San-Jose-SV1",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "2001 Sixth Ave., Suite 350, SE2, Seattle, WA 98121",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Seattle-SE2",
    "location": null,
    "name": "Equinix-Seattle-SE2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ]
   ```
4. Ermitteln Sie, ob für einen der im vorherigen Schritt aufgeführten Standorte Bandbreite verfügbar ist:

   ```azurecli
   az network express-route port location show -l "Equinix-Ashburn-DC2"
   ```

   **Beispielausgabe**

   ```output
   {
   "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
   "availableBandwidths": [
    {
      "offerName": "100 Gbps",
      "valueInGbps": 100
    }
   ],
   "contact": "support@equinix.com",
   "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
   "location": null,
   "name": "Equinix-Ashburn-DC2",
   "provisioningState": "Succeeded",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ```
5. Erstellen Sie eine ExpressRoute Direct-Ressource, die auf dem Standort basiert, den Sie in den vorherigen Schritten ausgewählt haben.

   ExpressRoute Direct unterstützt die Kapselungen QinQ und Dot1Q. Bei Auswahl von QinQ wird jeder ExpressRoute-Leitung dynamisch ein S-Tag zugewiesen, damit sie in der gesamten ExpressRoute Direct-Ressource eindeutig ist. Jedes C-Tag für die Leitung muss innerhalb der Leitung eindeutig sein, aber nicht für die gesamte ExpressRoute Direct-Ressource.  

   Wenn Sie die Kapselung Dot1Q wählen, müssen Sie die Eindeutigkeit des C-Tags (VLAN) für die gesamte ExpressRoute Direct-Ressource sicherstellen.  

   > [!IMPORTANT]
   > Für ExpressRoute Direct kann nur ein Kapselungstyp festgelegt werden. Sie können den Kapselungstyp nicht mehr ändern, nachdem Sie die ExpressRoute Direct-Ressource erstellt haben.
   > 
 
   ```azurecli
   az network express-route port create -n $name -g $RGName --bandwidth 100 gbps  --encapsulation QinQ | Dot1Q --peering-location $PeeringLocationName -l $AzureRegion 
   ```

   > [!NOTE]
   > Außerdem können Sie das Attribut **Encapsulation** auf **Dot1Q** festlegen. 
   >

   **Beispielausgabe**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "02ee21fe-4223-4942-a6bc-8d81daabc94f",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }  
   ```

## <a name="change-adminstate-for-links"></a><a name="state"></a>Ändern von AdminState für Links

Verwenden Sie diesen Prozess, um einen Layer-1-Test durchzuführen. Stellen Sie sicher, dass jede Kreuzverbindung für die primären und sekundären Ports korrekt in jeden Router gepatcht ist.

1. Legen Sie die Links auf **Aktiviert** fest. Wiederholen Sie diesen Schritt, um die einzelnen Links auf **Aktiviert** festzulegen.

   „Links[0]“ ist der primäre Port, „Links[1]“ der sekundäre Port.

   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[0].adminState="Enabled"
   ```
   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[1].adminState="Enabled"
   ```
   **Beispielausgabe**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "<resourceGUID>",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }
   ```

   Nutzen Sie die gleiche Vorgehensweise, um die Ports zu deaktivieren, indem Sie `AdminState = "Disabled"` verwenden.

## <a name="create-a-circuit"></a><a name="circuit"></a>Erstellen einer Leitung

Sie können standardmäßig zehn Leitungen im Abonnement erstellen, in dem die ExpressRoute Direct-Ressource enthalten ist. Der Microsoft-Support kann den Standardgrenzwert heraufsetzen. Sie sind für die Nachverfolgung der Werte für die bereitgestellte und belegte Bandbreite zuständig. Die bereitgestellte Bandbreite ist die Summe der Bandbreiten aller Leitungen der ExpressRoute Direct-Ressource. Die genutzte Bandbreite steht für die physische Nutzung der zugrunde liegenden physischen Schnittstellen.

Sie können zusätzliche Leitungsbandbreiten für ExpressRoute Direct nur verwenden, um die hier beschriebenen Szenarien zu unterstützen. Die Bandbreiten sind 40 GBit/s und 100 GBit/s.

**SkuTier** kann „Local“, „Standard“ oder „Premium“ sein.

**SkuFamily** kann nur „MeteredData“ sein. „Unlimited“ wird für ExpressRoute Direct nicht unterstützt.

Erstellen Sie in der ExpressRoute Direct-Ressource eine Leitung:

  ```azurecli
  az network express-route create --express-route-port "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct" -n "Contoso-Direct-ckt" -g "Contoso-Direct-rg" --sku-family MeteredData --sku-tier Standard --bandwidth 100 Gbps
  ```

  Andere Bandbreiten sind beispielsweise 5 GBit/s, 10 GBit/s und 40 GBit/s.

  **Beispielausgabe**

  ```output
  {
  "allowClassicOperations": false,
  "allowGlobalReach": false,
  "authorizations": [],
  "bandwidthInGbps": 100.0,
  "circuitProvisioningState": "Enabled",
  "etag": "W/\"<etagnumber>\"",
  "expressRoutePort": {
    "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
    "resourceGroup": "Contoso-Direct-rg"
  },
  "gatewayManagerEtag": "",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRouteCircuits/ERDirect-ckt-cli",
  "location": "westus",
  "name": "ERDirect-ckt-cli",
  "peerings": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "serviceKey": "<serviceKey>",
  "serviceProviderNotes": null,
  "serviceProviderProperties": null,
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "MeteredData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "stag": null,
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits"
  }  
  ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu ExpressRoute Direct finden Sie in der [Übersicht](expressroute-erdirect-about.md).
