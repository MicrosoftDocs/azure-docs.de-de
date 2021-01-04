---
title: Konfigurieren von IP-Firewallregeln für Azure Service Bus
description: Hier erfahren Sie, wie Sie mithilfe von Firewallregeln Verbindungen von bestimmten IP-Adressen mit Azure Service Bus zulassen.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 3aacf54dca07f0e1f2a66c8cdd85f892dda68cd4
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426588"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-ip-addresses-or-ranges"></a>Zulassen des Zugriffs auf den Azure Service Bus-Namespace von bestimmten IP-Adressen oder -Adressbereichen
Standardmäßig kann über das Internet auf Service Bus-Namespaces zugegriffen werden, solange die Anforderung eine gültige Authentifizierung und Autorisierung aufweist. Mit der IP-Firewall können Sie den Zugriff auf eine Gruppe von IPv4-Adressen oder IPv4-Adressbereichen in der [CIDR-Notation (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) weiter einschränken.

Diese Funktion ist in Szenarien hilfreich, in denen Azure Service Bus nur von bestimmten bekannten Websites aus zugänglich sein soll. Mithilfe von Firewallregeln können Sie Regeln konfigurieren, um Datenverkehr von bestimmten IPv4-Adressen zuzulassen. Wenn Sie Service Bus mit [Azure ExpressRoute][express-route] verwenden, können Sie beispielsweise eine **Firewallregel** erstellen, um nur Datenverkehr von den IP-Adressen Ihrer lokalen Infrastruktur oder von Adressen eines unternehmenseigenen NAT-Gateways zuzulassen. 

> [!IMPORTANT]
> Firewalls und virtuelle Netzwerke werden nur im Tarif **Premium** von Service Bus unterstützt. Wenn ein Upgrade auf den Tarif **Premium** nicht möglich ist, wird empfohlen, dass Sie die Sicherheit des SAS-Tokens (Shared Access Signature) gewährleisten und es nur für autorisierte Benutzer freigeben. Weitere Informationen zur SAS-Authentifizierung finden Sie unter [Authentifizierung und Autorisierung](service-bus-authentication-and-authorization.md#shared-access-signature).

## <a name="ip-firewall-rules"></a>IP-Firewallregeln
Die IP-Firewallregeln werden auf der Service Bus-Namespaceebene angewendet. Daher gelten die Regeln für alle Clientverbindungen mit einem beliebigen unterstützten Protokoll. Jeder Verbindungsversuch über eine IP-Adresse, die nicht mit einer IP-Zulassungsregel im Service Bus-Namespace übereinstimmt, wird als nicht autorisiert abgelehnt. In der Antwort wird die IP-Regel nicht erwähnt. IP-Filterregeln werden der Reihe nach angewendet, und die erste Regel, die eine Übereinstimmung mit der IP-Adresse ergibt, bestimmt die Aktion (Zulassen oder Ablehnen).

>[!WARNING]
> Durch Implementieren von Firewallregeln kann verhindert werden, dass andere Azure-Dienste mit Service Bus interagieren. Als Ausnahme können Sie bestimmten vertrauenswürdigen Diensten selbst dann den Zugriff auf Service Bus-Ressourcen erlauben, wenn die IP-Filterung aktiviert ist. Eine Liste der vertrauenswürdigen Dienste finden Sie unter [Vertrauenswürdige Dienste](#trusted-microsoft-services). 
>
> Die folgenden Microsoft-Dienste müssen sich in einem virtuellen Netzwerk befinden:
> - Azure App Service
> - Azure-Funktionen

## <a name="use-azure-portal"></a>Verwenden des Azure-Portals
In diesem Abschnitt erfahren Sie, wie Sie im Azure-Portal IP-Firewallregeln für einen Service Bus-Namespace erstellen. 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem **Service Bus-Namespace**.
2. Wählen Sie im Menü links unter **Einstellungen** die Option **Netzwerk** aus.  

    > [!NOTE]
    > Die Registerkarte **Netzwerk** wird nur für Namespaces vom Typ **Premium** angezeigt.  
    
    Standardmäßig ist die Option **Ausgewählte Netzwerke** ausgewählt. Wenn Sie auf dieser Seite nicht mindestens eine IP-Firewallregel oder ein virtuelles Netzwerk hinzufügen, kann über das öffentliche Internet (mit dem Zugriffsschlüssel) auf den Namespace zugegriffen werden.

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Seite „Netzwerk“ – Standard" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Wenn Sie die Option **Alle Netzwerke** auswählen, akzeptiert der Service Bus-Namespace Verbindungen von beliebigen IP-Adressen. Die Standardeinstellung entspricht einer Regel, bei der der IP-Adressbereich 0.0.0.0/0 zulässig ist. 

    ![Screenshot: Seite „Netzwerk“ im Azure-Portal. Die Option zum Zulassen des Zugriffs von allen Netzwerken ist auf der Registerkarte „Firewalls und virtuelle Netzwerke“ ausgewählt.](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Um den Zugriff nur von bestimmten IP-Adressen aus zuzulassen, wählen Sie die Option **Ausgewählte Netzwerke** aus, sofern sie noch nicht ausgewählt wurde. Gehen Sie im Abschnitt **Firewall** wie folgt vor:
    1. Wählen Sie die Option **Client-IP-Adresse hinzufügen** aus, um Ihrer aktuellen Client-IP Zugriff auf den Namespace zu gewähren. 
    2. Geben Sie für **Adressbereich** eine bestimmte IPv4-Adresse oder einen Bereich von IPv4-Adressen in der CIDR-Notation ein. 
    3. Wählen Sie unter **Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben?** die Option „Ja“ oder „Nein“ aus. 

        > [!WARNING]
        > Wenn Sie die Option **Ausgewählte Netzwerke** auswählen und weder eine IP-Adresse noch einen IP-Adressenbereich angeben, lässt der Dienst Datenverkehr aus allen Netzwerken zu. 

        ![Screenshot: Seite „Netzwerk“ im Azure-Portal. Die Option zum Zulassen des Zugriffs von ausgewählten Netzwerken ist ausgewählt, und der Abschnitt „Firewall“ ist hervorgehoben.](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Klicken Sie auf der Symbolleiste auf **Speichern**, um die Einstellungen zu speichern. Warten Sie einige Minuten, bis die Bestätigung in den Portalbenachrichtigungen angezeigt wird.

    > [!NOTE]
    > Informationen zum Beschränken des Zugriffs auf bestimmte virtuelle Netzwerke finden Sie unter [Zulassen des Zugriffs aus bestimmten Netzwerken](service-bus-service-endpoints.md).

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Verwenden von Resource Manager-Vorlagen
In diesem Abschnitt wird eine Azure Resource Manager-Beispielvorlage verwendet, die ein virtuelles Netzwerk und eine Firewallregel erstellt.


Mithilfe der folgenden Resource Manager-Vorlage können Sie einem vorhandenen Service Bus-Namespace eine VNET-Regel hinzufügen.

Vorlagenparameter:

- **ipMask** ist eine einzelne IPv4-Adresse oder ein Block von IP-Adressen in CIDR-Notation. In CIDR-Notation steht beispielsweise 70.37.104.0/24 für die 256 IPv4-Adressen von 70.37.104.0 bis 70.37.104.255. „24“ gibt dabei die Anzahl signifikanter Präfixbits für den Bereich an.

> [!NOTE]
> Obwohl keine Verweigerungsregeln möglich sind, ist in der Azure Resource Manager-Vorlage die Standardaktion auf **„Zulassen“** festgelegt. Dies schränkt die Verbindungen nicht ein.
> Beim Erstellen von Virtual Network- oder Firewall-Regeln muss die **_"defaultAction"_** wie folgt geändert werden:
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
        "type": "Microsoft.ServiceBus/namespaces/networkrulesets",
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
          "trustedServiceAccessEnabled": false,          
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Gehen Sie zum Bereitstellen der Vorlage gemäß den Anweisungen für [Azure Resource Manager][lnk-deploy] vor.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Einschränken des Service Bus-Zugriffs für virtuelle Azure-Netzwerke finden Sie unter dem folgenden Link:

- [Verwenden von Virtual Network-Dienstendpunkten für Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  ../expressroute/expressroute-faqs.md#supported-services