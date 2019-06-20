---
title: 'Bereitstellen einer IPv6-Dual Stack-Anwendung in Azure Virtual Network: Resource Manager-Vorlage (Vorschau)'
titlesuffix: Azure Virtual Network
description: Dieser Artikel zeigt, wie eine IPv6-Dual Stack-Anwendung in Azure Virtual Network mit Azure Resource Manager-VM-Vorlagen bereitgestellt wird.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: ae90bc4a12763803f38224d917c4644a68ae7d6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62131072"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---template-preview"></a>Bereitstellen einer IPv6-Dual Stack-Anwendung in Azure: Vorlage (Vorschau)

Dieser Artikel enthält eine Liste von IPv6-Konfigurationsaufgaben mit dem Teil der Azure Resource Manager-VM-Vorlage, der für die jeweilige Aufgabe gilt. Verwenden Sie die in diesem Artikel beschriebene Vorlage, um eine Dual Stack-Anwendung (IPv4 und IPv6) in Azure bereitstellen, die ein virtuelles Dual Stack-Netzwerk mit IPv4- und IPv6-Subnetzen, einen Load Balancer mit dualen Front-End-Konfigurationen (IPv4 und IPv6), VMs mit NICs mit einer dualen IP-Konfiguration, eine Netzwerksicherheitsgruppen und öffentliche IP-Adressen umfasst. 

## <a name="required-configurations"></a>Erforderliche Konfigurationen

Suchen Sie nach den Vorlagenabschnitten in der Vorlage, um zu sehen, wo die jeweilige Aufgabe vorkommen soll.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>IPv6-addressSpace für das virtuelle Netzwerk

Hinzuzufügender Vorlagenabschnitt:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6-Subnetz im addressSpace des virtuellen IPv6-Netzwerks

Hinzuzufügender Vorlagenabschnitt:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>IPv6-Konfiguration für die NIC

Hinzuzufügender Vorlagenabschnitt:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>IPv6-Netzwerksicherheitsgruppen-Regeln (NSG)

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Zusätzliche Konfiguration

Wenn Sie ein virtuelles Netzwerkgerät verwenden, fügen Sie in der Routentabelle IPv6-Routen hinzu. Andernfalls ist diese Konfiguration optional.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>Optionale Konfiguration

### <a name="ipv6-internet-access-for-the-virtual-network"></a>IPv6-Internetzugriff für das virtuelle Netzwerk

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Öffentliche IPv6-IP-Adressen

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6-Front-End für Load Balancer

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>IPv6-Back-End-Adresspool für Load Balancer

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6-Load Balancer-Regeln zum Zuordnen von Ports für ein- und ausgehenden Datenverkehr

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>VM-Beispielvorlage (JSON)
Klicken Sie [hier](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/), um eine IPv6-Dual Stack-Anwendung im virtuellen Azure-Netzwerk mit einer Azure Resource Manager-Vorlage bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte

Sie können Details zu den Preisen für [öffentliche IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses/), [Netzwerkbandbreite](https://azure.microsoft.com/pricing/details/bandwidth/) oder [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) anzeigen.
