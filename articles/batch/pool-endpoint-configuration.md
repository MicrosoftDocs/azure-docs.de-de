---
title: Konfigurieren von Knotenendpunkten im Azure Batch-Pool | Microsoft-Dokumentation
description: Konfigurieren oder Deaktivieren des Zugriffs auf SSH- oder RDP-Ports auf Computeknoten in einem Azure Batch-Pool.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: lahugh
ms.openlocfilehash: 9b6b28b9f55623fbdff6ab80c889141c8815600f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071530"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Konfigurieren oder Deaktivieren des Remotezugriffs auf Computeknoten in einem Azure Batch-Pool

Standardmäßig kann ein [Knotenbenutzer](/rest/api/batchservice/computenode/adduser) mit Netzwerkverbindung über Batch eine externe Verbindung zu einem Serverknoten in einem Batch-Pool herstellen. Beispielsweise kann ein Benutzer per Remote Desktop (RDP) auf Port 3389 eine Verbindung zu einem Computeknoten in einem Windows-Pool herstellen. Auf ähnliche Weise kann ein Benutzer standardmäßig per Secure Shell (SSH) auf Port 22 eine Verbindung zu einem Computeknoten in einem Linux-Pool herstellen. 

In Ihrer Umgebung müssen Sie diese Standardeinstellungen für den externen Zugriff möglicherweise einschränken oder deaktivieren. Ändern Sie diese Einstellungen, indem Sie mit den Batch-APIs die Eigenschaft [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) festlegen. 

## <a name="about-the-pool-endpoint-configuration"></a>Informationen zur Poolendpunktkonfiguration
Die Endpunktkonfiguration besteht aus einem oder mehreren [Pools für die Netzwerkadressübersetzung (NAT)](/rest/api/batchservice/pool/add#inboundnatpool) von Front-End-Ports. (Verwechseln Sie NAT-Pools nicht mit dem Batch-Pool von Computeknoten.) Sie können festlegen, dass jeder NAT-Pool die Standardverbindungseinstellungen für Serverknoten auf den Computeknoten des Pools überschreibt. 

Jede NAT-Pool-Konfiguration enthält eine oder mehrere [Regeln für die Netzwerksicherheitsgruppe (NSG)](/rest/api/batchservice/pool/add#networksecuritygrouprule). Jede NSG-Regel erlaubt oder verweigert bestimmten Netzwerkdatenverkehr zum Endpunkt. Sie können den kompletten Datenverkehr, durch ein [Diensttag](../virtual-network/security-overview.md#service-tags) (wie „Internet“) identifizierten Datenverkehr oder Datenverkehr von bestimmten IP-Adressen oder Subnetzen zulassen oder verweigern.

### <a name="considerations"></a>Überlegungen
* Die Konfiguration des Poolendpunkts ist Teil der [Netzwerkkonfiguration](/rest/api/batchservice/pool/add#networkconfiguration) des Pools. Die Netzwerkkonfiguration kann optional Einstellungen zum Verknüpfen des Pools mit einem [virtuellen Azure-Netzwerk](batch-virtual-network.md) enthalten. Wenn Sie den Pool in einem virtuellen Netzwerk einrichten, können Sie NSG-Regeln erstellen, die Adresseinstellungen im virtuellen Netzwerk verwenden.
* Sie können mehrere NSG-Regeln konfigurieren, wenn Sie einen NAT-Pool konfigurieren. Die Regeln werden gemäß ihrer Priorität geprüft. Sobald eine Regel als gültig erkannt wird, werden keine weiteren Regeln mehr geprüft.


## <a name="example-deny-all-rdp-traffic"></a>Beispiel: Verweigern des gesamten RDP-Datenverkehrs

Im folgenden C#-Codeausschnitt sehen Sie, wie Sie den RDP-Endpunkt auf Computeknoten in einem Windows-Pool konfigurieren müssen, um sämtlichen Netzwerkdatenverkehr zu verweigern. Der Endpunkt verwendet einen Pool an Front-End-Ports im Bereich *60000–60099*. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Beispiel: Verweigern des gesamten SSH-Datenverkehrs aus dem Internet

Im folgenden Python-Ausschnitt sehen Sie, wie Sie den SSH-Endpunkt auf Computeknoten in einem Linux-Pool konfigurieren müssen, um sämtlichen Internetdatenverkehr zu verweigern. Der Endpunkt verwendet einen Pool an Front-End-Ports im Bereich *4000–4100*. 

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                source_address_prefix='Internet'
                )
            ]
        )
        ]
    ) 
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Beispiel: Zulassen von RDP-Datenverkehr von einer bestimmten IP-Adresse

Im folgenden C#-Codeausschnitt sehen Sie, wie Sie den RDP-Endpunkt auf Computeknoten in einem Windows-Pool konfigurieren müssen, um den RDP-Zugriff nur von der IP-Adresse *198.51.100.7* zu gestatten. Die zweite NSG-Regel verweigert Datenverkehr, der nicht mit der IP-Adresse übereinstimmt.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Beispiel: Zulassen von SSH-Datenverkehr von einer bestimmten IP-Adresse

Im folgenden Python-Ausschnitt sehen Sie, wie Sie den SSH-Endpunkt auf Computeknoten in einem Linux-Pool konfigurieren müssen, um den Zugriff nur vom Subnetz *192.168.1.0/24* zu gestatten. Die zweite NSG-Regel verweigert Datenverkehr, der nicht mit dem Subnetz übereinstimmt.

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access='allow',
                source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                priority=175,
                access='deny',
                source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu NSG-Regeln in Azure finden Sie unter [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../virtual-network/security-overview.md).

- Eine detaillierte Übersicht über Batch finden Sie unter [Entwickeln von parallelen Computelösungen in größerem Umfang mit Batch](batch-api-basics.md).

