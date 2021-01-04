---
title: Firewallregeln für Azure Event Hubs | Microsoft-Dokumentation
description: Verwenden von Firewallregeln zum Zulassen von Verbindungen von bestimmten IP-Adressen mit Azure Event Hubs.
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: e07f863bf8b7d5f64ec0ba04bf16fba12f4a785d
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427444"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-ip-addresses-or-ranges"></a>Zulassen des Zugriffs auf Azure Event Hubs-Namespaces von bestimmten IP-Adressen oder -Adressbereichen
Standardmäßig kann über das Internet auf Event Hubs-Namespaces zugegriffen werden, solange die Anforderung eine gültige Authentifizierung und Autorisierung aufweist. Mit der IP-Firewall können Sie den Zugriff auf eine Gruppe von IPv4-Adressen oder IPv4-Adressbereichen in der [CIDR-Notation (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) weiter einschränken.

Diese Funktion ist in Szenarien hilfreich, in denen Azure Event Hubs nur von bestimmten bekannten Websites aus zugänglich sein soll. Mithilfe von Firewallregeln können Sie Regeln konfigurieren, um Datenverkehr von bestimmten IPv4-Adressen zuzulassen. Wenn Sie z. B. Event Hubs mit [Azure Express Route][express-route] verwenden, können Sie eine **Firewallregel** erstellen, um Datenverkehr nur von den IP-Adressen Ihrer lokalen Infrastruktur zuzulassen. 

>[!WARNING]
> Durch das Aktivieren von Firewallregeln für Ihren Event Hubs-Namespace werden eingehende Anforderungen automatisch blockiert, es sei denn, die Anforderungen stammen von einem Dienst, der von zulässigen öffentlichen IP-Adressen aus operiert. Unter anderem werden Anforderungen von anderen Azure-Diensten, aus dem Azure-Portal und von Protokollierungs-/Metrikdiensten blockiert. Als Ausnahme können Sie bestimmten vertrauenswürdigen Diensten selbst dann den Zugriff auf Event Hubs-Ressourcen erlauben, wenn IP-Filter aktiviert sind. Eine Liste der vertrauenswürdigen Dienste finden Sie unter [Vertrauenswürdige Microsoft-Dienste](#trusted-microsoft-services).

## <a name="ip-firewall-rules"></a>IP-Firewallregeln
Die IP-Firewallregeln werden auf der Event Hubs-Namespaceebene angewendet. Daher gelten die Regeln für alle Clientverbindungen mit einem beliebigen unterstützten Protokoll. Jeder Verbindungsversuch von einer IP-Adresse, die nicht mit einer zulässigen IP-Regel im Event Hubs-Namespace übereinstimmt, wird als nicht autorisiert abgelehnt. In der Antwort wird die IP-Regel nicht erwähnt. IP-Filterregeln werden der Reihe nach angewendet, und die erste Regel, die eine Übereinstimmung mit der IP-Adresse ergibt, bestimmt die Aktion (Zulassen oder Ablehnen).

## <a name="use-azure-portal"></a>Verwenden des Azure-Portals
In diesem Abschnitt erfahren Sie, wie Sie im Azure-Portal IP-Firewallregeln für einen Event Hubs-Namespace erstellen. 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem **Event Hubs-Namespace**.
4. Wählen Sie im linken Menü unter **Einstellungen** die Option **Netzwerk** aus. Die Registerkarte **Netzwerk** wird nur für Namespaces vom Typ **Standard** oder **Dediziert** angezeigt. 
    > [!NOTE]
    > Die Option **Ausgewählte Netzwerke** wird wie in der folgenden Abbildung gezeigt ausgewählt. Wenn Sie auf dieser Seite keine IP-Firewallregel angeben oder kein virtuelles Netzwerk hinzufügen, kann über das **öffentliche Internet** (mit dem Zugriffsschlüssel) auf den Namespace zugegriffen werden.  

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Registerkarte „Netzwerk“ mit ausgewählter Option „Netzwerk“" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    Wenn Sie die Option **Alle Netzwerke** auswählen, akzeptiert der Event Hub Verbindungen von beliebigen IP-Adressen (mit dem Zugriffsschlüssel). Diese Einstellung entspricht einer Regel, bei der der IP-Adressbereich 0.0.0.0/0 zulässig ist. 

    ![Screenshot: Seite „Firewall und virtuelle Netzwerke“ mit ausgewählter Option „Alle Netzwerke“](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Um den Zugriff auf bestimmte IP-Adressen zu beschränken, vergewissern Sie sich, dass die Option **Ausgewählte Netzwerke** ausgewählt ist. Gehen Sie im Abschnitt **Firewall** wie folgt vor:
    1. Wählen Sie die Option **Client-IP-Adresse hinzufügen** aus, um Ihrer aktuellen Client-IP Zugriff auf den Namespace zu gewähren. 
    2. Geben Sie für **Adressbereich** eine bestimmte IPv4-Adresse oder einen Bereich von IPv4-Adressen in der CIDR-Notation ein. 
3. Wählen Sie unter **Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben?** die Option „Ja“ oder „Nein“ aus. Weitere Informationen finden Sie unter [Vertrauenswürdige Microsoft-Dienste](#trusted-microsoft-services). 

      ![Firewall: Option „Alle Netzwerke“ ausgewählt](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Klicken Sie auf der Symbolleiste auf **Speichern**, um die Einstellungen zu speichern. Warten Sie einige Minuten, bis die Bestätigung in den Portalbenachrichtigungen angezeigt wird.

    > [!NOTE]
    > Informationen zum Beschränken des Zugriffs auf bestimmte virtuelle Netzwerke finden Sie unter [Zulassen des Zugriffs aus bestimmten Netzwerken](event-hubs-service-endpoints.md).

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]


## <a name="use-resource-manager-template"></a>Verwenden von Resource Manager-Vorlagen

> [!IMPORTANT]
> Firewallregeln werden in den Tarifen **Standard** und **Dediziert** von Event Hubs unterstützt. Sie werden nicht im Basic-Tarif unterstützt.

Mithilfe der folgenden Resource Manager-Vorlage können Sie einem vorhandenen Event Hubs-Namespace eine IP-Filterregel hinzufügen.

Vorlagenparameter:

- **ipMask** ist eine einzelne IPv4-Adresse oder ein Block von IP-Adressen in CIDR-Notation. In CIDR-Notation steht beispielsweise 70.37.104.0/24 für die 256 IPv4-Adressen von 70.37.104.0 bis 70.37.104.255. „24“ gibt dabei die Anzahl signifikanter Präfixbits für den Bereich an.

> [!NOTE]
> Obwohl keine Verweigerungsregeln möglich sind, ist in der Azure Resource Manager-Vorlage die Standardaktion auf **„Zulassen“** festgelegt. Dies schränkt die Verbindungen nicht ein.
> Bei der Erstellung von Regeln für Virtual Network oder Firewalls muss **_"defaultAction"_** wie folgt geändert werden:
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
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
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
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkrulesets",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
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

Informationen zum Einschränken des Event Hubs-Zugriffs für virtuelle Azure-Netzwerke finden Sie unter dem folgenden Link:

- [VNET-Dienstendpunkte für Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
