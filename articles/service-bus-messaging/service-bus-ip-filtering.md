---
title: Firewallregeln für Azure Service Bus | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe von Firewallregeln Verbindungen von bestimmten IP-Adressen mit Azure Service Bus zulassen.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 04/23/2019
ms.author: aschhab
ms.openlocfilehash: 540435e3e018ae77477030ae8b9f727d71782121
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64704592"
---
# <a name="use-firewall-rules"></a>Verwenden von Firewallregeln

Für Szenarien, in denen Azure Service Bus nur von bestimmten bekannten Sites aus zugänglich sein soll, können Sie mithilfe von Firewallregeln Regeln für die Akzeptierung von Datenverkehr konfigurieren, der von bestimmten IPv4-Adressen stammt. Bei diesen Adressen kann es sich beispielsweise um die Adressen eines unternehmenseigenen NAT-Gateways handeln.

## <a name="when-to-use"></a>Einsatzgebiete

Wenn Sie Ihre Umgebung so einrichten möchten, dass Service Bus nur Datenverkehr aus einem bestimmten IP-Adressbereich empfängt, können Sie eine *Firewall* verwenden, um andere IP-Adressen für Service Bus-Endpunkte zu blockieren. Ein Beispiel wäre etwa, wenn Sie Service Bus mit [Azure Express Route][express-route] verwenden, um private Verbindungen mit Ihrer lokalen Infrastruktur zu erstellen. 

## <a name="how-filter-rules-are-applied"></a>Anwenden von Filterregeln

Die IP-Filterregeln werden auf der Service Bus-Namespaceebene angewendet. Daher gelten die Regeln für alle Clientverbindungen mit einem beliebigen unterstützten Protokoll.

Jeder Verbindungsversuch über eine IP-Adresse, die nicht mit einer IP-Zulassungsregel im Service Bus-Namespace übereinstimmt, wird als nicht autorisiert abgelehnt. In der Antwort wird die IP-Regel nicht erwähnt.

## <a name="default-setting"></a>Standardeinstellung

Das Raster **IP-Filter** im Portal ist für Service Bus standardmäßig leer. Diese Standardeinstellung bedeutet, dass Ihr Namespace Verbindungen von allen IP-Adressen akzeptiert. Die Standardeinstellung entspricht einer Regel, bei der der IP-Adressbereich 0.0.0.0/0 zulässig ist.

## <a name="ip-filter-rule-evaluation"></a>Auswertung von IP-Filterregeln

IP-Filterregeln werden der Reihenfolge nach angewendet, und die erste Regel, die eine Übereinstimmung mit der IP-Adresse ergibt, bestimmt die Aktion (Zulassen oder Ablehnen).

>[!WARNING]
> Durch Implementieren von Firewallregeln kann verhindert werden, dass andere Azure-Dienste mit Service Bus interagieren.
>
> Vertrauenswürdige Microsoft-Dienste werden bei der Implementierung der IP-Filterung (Firewallregeln) nicht unterstützt und in Kürze verfügbar gemacht.
>
> Im Anschluss finden Sie einige allgemeine Azure-Szenarien, in denen die IP-Filterung nicht funktioniert. (Hinweis: Die Liste ist **NICHT** vollständig.)
> - Azure Monitor
> - Azure Stream Analytics
> - Integration in Azure Event Grid
> - Azure IoT Hub-Routen
> - Azure IoT Device Explorer
> - Azure-Daten-Explorer
>
> Die folgenden Microsoft-Dienste müssen in einem virtuellen Netzwerk ausgeführt werden:
> - Azure App Service
> - Azure-Funktionen

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Erstellen einer VNET- und einer Firewallregel mit Azure Resource Manager-Vorlagen

> [!IMPORTANT]
> Firewalls und virtuelle Netzwerke werden nur im Tarif **Premium** von Service Bus unterstützt.

Mithilfe der folgenden Resource Manager-Vorlage können Sie einem vorhandenen Service Bus-Namespace eine VNET-Regel hinzufügen.

Vorlagenparameter:

- **ipMask** ist eine einzelne IPv4-Adresse oder ein Block von IP-Adressen in CIDR-Notation. In CIDR-Notation steht beispielsweise 70.37.104.0/24 für die 256 IPv4-Adressen von 70.37.104.0 bis 70.37.104.255. „24“ gibt dabei die Anzahl signifikanter Präfixbits für den Bereich an.

> [!NOTE]
> Obwohl keine Verweigerungsregeln möglich sind, ist in der Azure Resource Manager-Vorlage die Standardaktion auf **Zulassen** festgelegt. Dies schränkt die Verbindungen nicht ein.
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
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Gehen Sie zum Bereitstellen der Vorlage gemäß der Anleitung für [Azure Resource Manager][lnk-deploy] vor.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Einschränken des Service Bus-Zugriffs für virtuelle Azure-Netzwerke finden Sie unter dem folgenden Link:

- [Use Virtual Network service endpoints with Azure Service Bus][lnk-vnet] (Verwenden von Virtual Network-Dienstendpunkten für Service Bus)

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
