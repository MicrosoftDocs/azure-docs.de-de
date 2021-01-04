---
title: 'Virtual Network-Dienstendpunkte: Azure Event Hubs | Microsoft-Dokumentation'
description: In diesem Artikel werden Informationen zum Hinzufügen eines Microsoft.EventHub-Dienstendpunkts zu einem virtuellen Netzwerk beschrieben.
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 029338e3835d03b1a66ff6629e872c84113b0ff2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015576"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-virtual-networks"></a>Zulassen des Zugriffs auf Azure Event Hubs-Namespaces aus bestimmten virtuellen Netzwerken 

Die Integration von Event Hubs und [VNET-Dienstendpunkten][vnet-sep] ermöglicht den sicheren Zugriff auf Messagingfunktionen für Workloads, z. B. an virtuelle Netzwerke (VNETs) gebundene virtuelle Computer, wobei der Pfad für den Netzwerkdatenverkehr an beiden Enden geschützt ist.

Nachdem die Konfiguration der Bindung an mindestens einen Dienstendpunkt des VNET-Subnetzes durchgeführt wurde, akzeptiert der entsprechende Event Hubs-Namespace nur noch Datenverkehr von autorisierten Subnetzen in virtuellen Netzwerken. Aus Sicht des virtuellen Netzwerks wird durch die Bindung eines Event Hubs-Namespace an einen Dienstendpunkt ein isolierter Netzwerktunnel vom Subnetz des virtuellen Netzwerks zum Messagingdienst konfiguriert. 

Das Ergebnis ist eine private und isolierte Beziehung zwischen den Workloads, die an das Subnetz gebunden sind, und dem entsprechenden Event Hubs-Namespace, obwohl sich die beobachtbare Netzwerkadresse des Messaging-Dienstendpunkts in einem öffentlichen IP-Bereich befindet. Es gibt eine Ausnahme für dieses Verhalten. Durch die Aktivierung eines Dienstendpunkts wird standardmäßig die `denyall`-Regel in der [IP-Firewall](event-hubs-ip-filtering.md) aktiviert, die dem virtuellen Netzwerk zugeordnet ist. Sie können bestimmte IP-Adressen in der IP-Firewall hinzufügen, um den Zugriff auf den öffentlichen Endpunkt des Event Hub zu ermöglichen. 

>[!WARNING]
> Durch Aktivieren von virtuellen Netzwerken für Ihren Event Hubs-Namespace werden eingehende Anforderungen automatisch blockiert. Dies gilt nicht, wenn die Anforderungen von einem Dienst stammen, der in zulässigen virtuellen Netzwerken betrieben wird. Unter anderem werden Anforderungen von anderen Azure-Diensten, aus dem Azure-Portal und von Protokollierungs-/Metrikdiensten blockiert. Als Ausnahme können Sie bestimmten vertrauenswürdigen Diensten selbst dann den Zugriff auf Event Hubs-Ressourcen erlauben, wenn virtuelle Netzwerke aktiviert sind. Eine Liste der vertrauenswürdigen Dienste finden Sie unter [Vertrauenswürdige Dienste](#trusted-microsoft-services).

> [!NOTE]
> Virtuelle Netzwerke werden in den Tarifen **Standard** und **Dediziert** von Event Hubs unterstützt. Im **Basic**-Tarif werden sie nicht unterstützt.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Erweiterte Sicherheitsszenarien basierend auf der VNET-Integration 

Lösungen, für die eine strikte und auf mehrere Bereiche aufgeteilte Sicherheit erforderlich ist und bei denen die VNET-Subnetze die Segmentierung zwischen den einzelnen aufgeteilten Diensten bereitstellen, benötigen weiterhin Kommunikationspfade zwischen den Diensten, die sich in diesen Bereichen befinden.

Für alle direkten IP-Routen zwischen den Bereichen (auch für HTTPS per TCP/IP) besteht die Gefahr, dass ab der Vermittlungsschicht Sicherheitsrisiken ausgenutzt werden. Bei Messagingdiensten werden isolierte Kommunikationspfade bereitgestellt, für die Nachrichten während des Übergangs zwischen Parteien sogar auf Datenträger geschrieben werden. Workloads in zwei einzelnen virtuellen Netzwerken, die beide an dieselbe Event Hubs-Instanz gebunden sind, können über Nachrichten effizient und zuverlässig kommunizieren, während die Integrität der jeweiligen Netzwerkisolationsgrenze aufrechterhalten wird.
 
Dies bedeutet, dass Ihre sicherheitsrelevanten Cloudlösungen nicht nur Zugriff auf zuverlässige und skalierbare Azure-Funktionen für asynchrones Messaging eines Branchenführers haben, sondern dass das Messaging jetzt auch genutzt werden kann, um Kommunikationspfade zwischen sicheren Lösungsbereichen zu erstellen. Diese sind deutlich sicherer als alle Optionen des Peer-to-Peer-Kommunikationsmodus, einschließlich HTTPS und andere per TLS geschützte Socketprotokolle.

## <a name="bind-event-hubs-to-virtual-networks"></a>Binden von Event Hubs an virtuelle Netzwerke

**VNET-Regeln** sind ein Feature für die Firewallsicherheit, mit dem gesteuert wird, ob Ihr Azure Event Hubs-Namespace Verbindungen eines bestimmten VNET-Subnetzes akzeptiert.

Das Binden eines Event Hubs-Namespace an ein virtuelles Netzwerk ist ein Prozess mit zwei Schritten. Zuerst müssen Sie einen **VNET-Dienstendpunkt** in einem Subnetz eines virtuellen Netzwerks erstellen und für **Microsoft.EventHub** aktivieren, wie im Artikel [Übersicht über Dienstendpunkte][vnet-sep] beschrieben. Nachdem Sie den Dienstendpunkt hinzugefügt haben, binden Sie den Event Hubs-Namespace mit einer **VNET-Regel** daran.

Die VNET-Regel ist eine Zuordnung des Event Hubs-Namespace zu einem Subnetz eines virtuellen Netzwerks. Während die Regel vorhanden ist, wird allen Workloads, die an das Subnetz gebunden sind, Zugriff auf den Event Hubs-Namespace gewährt. Event Hubs selbst stellt niemals ausgehende Verbindungen her, muss keinen Zugriff erhalten und erhält daher niemals die Gewährung des Zugriffs auf Ihr Subnetz, indem diese Regel aktiviert wird.

## <a name="use-azure-portal"></a>Verwenden des Azure-Portals
In diesem Abschnitt erfahren Sie, wie Sie mit dem Azure-Portal einen VNET-Dienstendpunkt hinzufügen. Zum Beschränken des Zugriffs müssen Sie den VNET-Dienstendpunkt für diesen Event Hubs-Namespace integrieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem **Event Hubs-Namespace**.
4. Wählen Sie im linken Menü unter **Einstellungen** die Option **Netzwerk** aus. Die Registerkarte **Netzwerk** wird nur für Namespaces vom Typ **Standard** oder **Dediziert** angezeigt. 

    > [!NOTE]
    > Die Option **Ausgewählte Netzwerke** wird wie in der folgenden Abbildung gezeigt ausgewählt. Wenn Sie auf dieser Seite keine IP-Firewallregel angeben oder kein virtuelles Netzwerk hinzufügen, kann über das **öffentliche Internet** (mit dem Zugriffsschlüssel) auf den Namespace zugegriffen werden. 

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Registerkarte „Netzwerk“ mit ausgewählter Option „Netzwerk“" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    Wenn Sie die Option **Alle Netzwerke** auswählen, akzeptiert der Event Hub Verbindungen von beliebigen IP-Adressen (mit dem Zugriffsschlüssel). Diese Einstellung entspricht einer Regel, bei der der IP-Adressbereich 0.0.0.0/0 zulässig ist. 

    ![Firewall: Option „Alle Netzwerke“ ausgewählt](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Um den Zugriff auf bestimmte Netzwerke zu beschränken, wählen Sie die Option **Ausgewählte Netzwerke** am Anfang der Seite aus, sofern sie noch nicht ausgewählt wurde.
2. Wählen Sie im Abschnitt **Virtuelles Netzwerk** der Seite die Option **+ Vorhandenes virtuelles Netzwerk hinzufügen** _ aus. Wählen Sie _ *+ Neues virtuelles Netzwerk erstellen** aus, wenn Sie ein neues VNET erstellen möchten. 

    ![Hinzufügen eines vorhandenen virtuellen Netzwerks](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. Wählen Sie in der Liste mit den virtuellen Netzwerken das virtuelle Netzwerk und anschließend das **Subnetz** aus. Sie müssen den Dienstendpunkt aktivieren, bevor Sie das virtuelle Netzwerk der Liste hinzufügen. Wenn der Dienstendpunkt nicht aktiviert ist, erhalten Sie im Portal eine entsprechende Aufforderung.
   
   ![Auswählen des Subnetzes](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. Die folgende Erfolgsmeldung sollte angezeigt werden, nachdem der Dienstendpunkt für das Subnetz für **Microsoft.EventHub** aktiviert wurde. Wählen Sie unten auf der Seite die Option **Hinzufügen** aus, um das Netzwerk hinzuzufügen. 

    ![Auswählen des Subnetzes und Aktivieren des Endpunkts](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Falls Sie den Dienstendpunkt nicht aktivieren können, können Sie den fehlenden VNET-Dienstendpunkt ignorieren und die Resource Manager-Vorlage verwenden. Diese Funktionalität ist nicht im Portal verfügbar.
5. Wählen Sie unter **Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben?** die Option „Ja“ oder „Nein“ aus. Weitere Informationen finden Sie unter [Vertrauenswürdige Microsoft-Dienste](#trusted-microsoft-services). 
6. Wählen Sie auf der Symbolleiste **Speichern** aus, um die Einstellungen zu speichern. Warten Sie einige Minuten, bis die Bestätigung in den Portalbenachrichtigungen angezeigt wird.

    ![Speichern des Netzwerks](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)

    > [!NOTE]
    > Informationen, wie Sie den Zugriff auf bestimmte IP-Adressen oder -Adressbereiche beschränken, finden Sie unter [Zulassen des Zugriffs von bestimmten IP-Adressen oder -Adressbereichen](event-hubs-ip-filtering.md).

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Verwenden von Resource Manager-Vorlagen

Mithilfe der folgenden Resource Manager-Vorlage können Sie einem vorhandenen Event Hubs-Namespace eine VNET-Regel hinzufügen.

Vorlagenparameter:

* `namespaceName`: Event Hubs-Namespace
* `vnetRuleName`: Name für die zu erstellende VNET-Regel
* `virtualNetworkingSubnetId`: Vollqualifizierter Resource Manager-Pfad für das Subnetz des virtuellen Netzwerks, z. B. `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` für das Standardsubnetz eines virtuellen Netzwerks.

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

Weitere Informationen zu virtuellen Netzwerken finden Sie unter den folgenden Links:

- [Azure-VNET-Dienstendpunkte][vnet-sep]
- [Azure Event Hubs – IP-Filterung][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
