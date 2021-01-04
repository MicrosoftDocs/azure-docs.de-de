---
title: 'Bereitstellen einer IPv6-Dual Stack-Anwendung mit Load Balancer Basic in Azure Virtual Network: Resource Manager-Vorlage'
titlesuffix: Azure Virtual Network
description: Dieser Artikel zeigt, wie eine IPv6-Dual Stack-Anwendung in Azure Virtual Network mit Azure Resource Manager-VM-Vorlagen bereitgestellt wird.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 548b416ed0f21df83dafdb1c2d7015c625c36e4c
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95244292"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template"></a>Bereitstellen einer IPv6-Dual Stack-Anwendung mit Load Balancer Basic in Azure: Vorlage

Dieser Artikel enthält eine Liste von IPv6-Konfigurationsaufgaben mit dem Teil der Azure Resource Manager-VM-Vorlage, der für die jeweilige Aufgabe gilt. Verwenden Sie die in diesem Artikel beschriebene Vorlage, um eine Dual Stack-Anwendung (IPv4 und IPv6) mit Basic Load Balancer bereitzustellen, die ein virtuelles Dual Stack-Netzwerk mit IPv4- und IPv6-Subnetzen, Basic Load Balancer mit dualen Front-End-Konfigurationen (IPv4 und IPv6), VMs mit NICs mit einer dualen IP-Konfiguration, eine Netzwerksicherheitsgruppe und öffentliche IP-Adressen umfasst.

Informationen zum Bereitstellen einer Dual Stack-Anwendung (IPv4 und IPv6) mithilfe von Load Balancer Standard finden Sie unter [Bereitstellen einer IPv6-Dual Stack-Anwendung mit Load Balancer Standard – Vorlage](ipv6-configure-standard-load-balancer-template-json.md).

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
              "sourceAddressPrefix": "fd00:db8:deca:deed::/64",
              "destinationAddressPrefix": "fd00:db8:deca:deed::/64",
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
              "addressPrefix": "fd00:db8:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "fd00:db8:ace:f00d::1"
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
[Hier](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/) finden Sie eine Beispielvorlage für das Bereitstellen einer IPv6-Dual Stack-Anwendung mit Basic Load Balancer in Azure Virtual Network. Das Beispiel basiert auf der Azure Resource Manager-Vorlage.

## <a name="next-steps"></a>Nächste Schritte

Sie können Details zu den Preisen für [öffentliche IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses/), [Netzwerkbandbreite](https://azure.microsoft.com/pricing/details/bandwidth/) oder [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) anzeigen.
