---
title: 'Virtual Network-Dienstendpunkte: Azure Event Hubs | Microsoft-Dokumentation'
description: In diesem Artikel werden Informationen zum Hinzufügen eines Microsoft.EventHub-Dienstendpunkts zu einem virtuellen Netzwerk beschrieben.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 03/12/2019
ms.author: shvija
ms.openlocfilehash: 15912ce2e100a4317e775d72972ca6eacfac0d42
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080542"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Verwenden von Virtual Network-Dienstendpunkten mit Azure Event Hubs

Die Integration von Event Hubs mit [VNET-Dienstendpunkten][vnet-sep] ermöglicht den sicheren Zugriff auf Messagingfunktionen für Workloads, z.B. an virtuelle Netzwerke (VNETs) gebundene virtuelle Computer, wobei der Pfad für den Netzwerkdatenverkehr an beiden Enden geschützt ist.

Nachdem die Konfiguration der Bindung an mindestens einen Dienstendpunkt des VNET-Subnetzes durchgeführt wurde, akzeptiert der entsprechende Event Hubs-Namespace nur noch Datenverkehr von autorisierten Subnetzen in virtuellen Netzwerken. Aus Sicht des virtuellen Netzwerks wird durch die Bindung eines Event Hubs-Namespace an einen Dienstendpunkt ein isolierter Netzwerktunnel vom Subnetz des virtuellen Netzwerks zum Messagingdienst konfiguriert. 

Das Ergebnis ist eine private und isolierte Beziehung zwischen den Workloads, die an das Subnetz gebunden sind, und dem entsprechenden Event Hubs-Namespace, obwohl sich die beobachtbare Netzwerkadresse des Messaging-Dienstendpunkts in einem öffentlichen IP-Bereich befindet. Es gibt bei diesem Verhalten eine Ausnahme. Das Aktivieren eines Dienstendpunkts aktiviert standardmäßig die denyall-Regel in der dem virtuellen Netzwerk zugeordneten IP-Firewall. Sie können bestimmte IP-Adressen in der IP-Firewall hinzufügen, um den Zugriff auf den öffentlichen Endpunkt des Event Hub zu ermöglichen. 


>[!WARNING]
> Die Implementierung der VNET-Integration kann verhindern, dass andere Azure-Dienste mit Event Hubs interagieren.
>
> Vertrauenswürdige Microsoft-Dienste werden bei Implementierung von Virtual Networks nicht unterstützt.
>
> Allgemeine Azure-Szenarien, die nicht mit Virtual Networks funktionieren (beachten Sie, dass die Liste **NICHT** vollständig ist):
> - Azure Monitor
> - Azure Stream Analytics
> - Integration in Azure Event Grid
> - Azure IoT Hub-Routen
> - Azure IoT Device Explorer
> - Azure-Daten-Explorer
>
> Die folgenden Microsoft-Dienste müssen in einem virtuellen Netzwerk ausgeführt werden:
> - Azure-Web-Apps
> - Azure-Funktionen

> [!IMPORTANT]
> Virtuelle Netzwerke werden in den Tarifen **Standard** und **Dediziert** von Event Hubs unterstützt. Sie werden nicht im Basic-Tarif unterstützt.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Erweiterte Sicherheitsszenarien basierend auf der VNET-Integration 

Lösungen, für die eine strikte und auf mehrere Bereiche aufgeteilte Sicherheit erforderlich ist und bei denen die VNET-Subnetze die Segmentierung zwischen den einzelnen aufgeteilten Diensten bereitstellen, benötigen weiterhin Kommunikationspfade zwischen den Diensten, die sich in diesen Bereichen befinden.

Für alle direkten IP-Routen zwischen den Bereichen (auch für HTTPS per TCP/IP) besteht die Gefahr, dass ab der Vermittlungsschicht Sicherheitsrisiken ausgenutzt werden. Bei Messagingdiensten werden vollständig isolierte Kommunikationspfade bereitgestellt, für die Nachrichten während des Übergangs zwischen Parteien sogar auf Datenträger geschrieben werden. Workloads in zwei einzelnen virtuellen Netzwerken, die beide an dieselbe Event Hubs-Instanz gebunden sind, können über Nachrichten effizient und zuverlässig kommunizieren, während die Integrität der jeweiligen Netzwerkisolationsgrenze aufrechterhalten wird.
 
Dies bedeutet, dass Ihre sicherheitsrelevanten Cloudlösungen nicht nur Zugriff auf zuverlässige und skalierbare Azure-Funktionen für asynchrones Messaging eines Branchenführers haben, sondern dass das Messaging jetzt auch genutzt werden kann, um Kommunikationspfade zwischen sicheren Lösungsbereichen zu erstellen. Diese sind deutlich sicherer als alle Optionen des Peer-to-Peer-Kommunikationsmodus, einschließlich HTTPS und andere per TLS geschützte Socketprotokolle.

## <a name="bind-event-hubs-to-virtual-networks"></a>Binden von Event Hubs an virtuelle Netzwerke

*VNET-Regeln* sind ein Feature für die Firewallsicherheit, mit dem gesteuert wird, ob Ihr Azure Event Hubs-Namespace Verbindungen eines bestimmten VNET-Subnetzes akzeptiert.

Das Binden eines Event Hubs-Namespace an ein virtuelles Netzwerk ist ein Prozess mit zwei Schritten. Zuerst müssen Sie einen **VNET-Dienstendpunkt** in einem Subnetz eines virtuellen Netzwerks erstellen und für „Microsoft.EventHub“ aktivieren, wie in der [Übersicht über Dienstendpunkte][vnet-sep] beschrieben. Nachdem Sie den Dienstendpunkt hinzugefügt haben, binden Sie den Event Hubs-Namespace mit einer *VNET-Regel* daran.

Die VNET-Regel ist eine Zuordnung des Event Hubs-Namespace zu einem Subnetz eines virtuellen Netzwerks. Während die Regel vorhanden ist, wird allen Workloads, die an das Subnetz gebunden sind, Zugriff auf den Event Hubs-Namespace gewährt. Event Hubs stellt selbst niemals ausgehende Verbindungen her, muss keinen Zugriff erhalten und erhält daher niemals die Gewährung des Zugriffs auf Ihr Subnetz, indem diese Regel aktiviert wird.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Erstellen einer VNET-Regel mit Azure Resource Manager-Vorlagen

Mithilfe der folgenden Resource Manager-Vorlage können Sie einem vorhandenen Event Hubs-Namespace eine VNET-Regel hinzufügen.

Vorlagenparameter:

* **namespaceName**: Event Hubs-Namespace
* **vnetRuleName**: Name für die zu erstellende VNET-Regel
* **virtualNetworkingSubnetId**: Vollqualifizierter Resource Manager-Pfad für das Subnetz des virtuellen Netzwerks, z. B. `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` für das Standardsubnetz eines virtuellen Netzwerks.

> [!NOTE]
> Obwohl keine Verweigerungsregeln möglich sind, ist in der Azure Resource Manager-Vorlage die Standardaktion auf **„Zulassen“** festgelegt. Dies schränkt die Verbindungen nicht ein.
> Bei der Erstellung von Regeln für virtuelle Netzwerke oder Firewalls muss die Standardaktion (***defaultAction***) geändert werden.
> 
> from
> ```json
> "defaultAction": "Allow"
> ```
> zu
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Gehen Sie zum Bereitstellen der Vorlage gemäß der Anleitung für [Azure Resource Manager][lnk-deploy] vor.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu virtuellen Netzwerken finden Sie unter den folgenden Links:

- [Azure-VNET-Dienstendpunkte][vnet-sep]
- [Azure Event Hubs IP filtering][ip-filtering] (Azure Event Hubs – IP-Filterung)

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md
