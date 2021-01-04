---
title: Verschieben eines internen Azure Load Balancers mithilfe des Azure-Portals in eine andere Azure-Region
description: Verwenden einer Azure Resource Manager-Vorlage, um den internen Azure Load Balancer mithilfe des Azure-Portals aus einer Azure-Region in eine andere zu verschieben
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 68a2cb6926cb41956711a9e3c15d21c250d27f0b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698487"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Verschieben eines internen Azure Load Balancers mithilfe des Azure-Portals in eine andere Region

Es gibt verschiedene Szenarien, in denen Sie einen vorhandenen Azure Load Balancer aus einer Region in eine andere verschieben möchten. Beispielsweise können Sie einen internen Load Balancer mit der gleichen Konfiguration für Testzwecke erstellen. Möglicherweise möchten Sie einen internen Load Balancer auch im Rahmen der Planung der Notfallwiederherstellung in eine andere Region verschieben.

Interne Azure Load Balance können nicht aus einer Region in eine andere verschoben werden. Sie können jedoch eine Azure Resource Manager-Vorlage verwenden, um die vorhandene Konfiguration und das virtuelle Netzwerk eines internen Load Balancers zu exportieren.  Anschließend können Sie die Ressource in einer anderen Region bereitstellen, indem Sie den Load Balancer und das virtuelle Netzwerk in eine Vorlage exportieren, die Parameter so ändern, dass Sie der Zielregion entsprechen, und die Vorlagen dann in der neuen Region bereitstellen.  Weitere Informationen zu Resource Manager und Vorlagen finden Sie unter [Schnellstart: Erstellen und Bereitstellen von Azure Resource Manager-Vorlagen über das Azure-Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass sich der interne Azure Load Balancer in der Azure-Region befindet, aus der Sie ihn verschieben möchten.

- Interne Azure Load Balancer können nicht zwischen Regionen verschoben werden.  Sie müssen den neuen Load Balancer Ressourcen in der Zielregion zuordnen.

- Zum Exportieren einer internen Load Balancer-Konfiguration und Bereitstellen einer Vorlage zum Erstellen eines internen Load Balancers in einer anderen Region benötigen Sie die Rolle „Netzwerkmitwirkender“ oder höher.

- Identifizieren Sie das Layout des Quellnetzwerks und alle Ressourcen, die Sie aktuell verwenden. Dieses Layout umfasst Load Balancer, Netzwerksicherheitsgruppen, virtuelle Computer und virtuelle Netzwerke, kann sich aber auch auf weitere Komponenten beziehen.

- Vergewissern Sie sich, dass Ihr Azure-Abonnement das Erstellen interner Load Balancer in der verwendeten Zielregion zulässt. Wenden Sie sich an den Support, um das erforderliche Kontingent zu aktivieren.

- Stellen Sie sicher, dass Ihr Abonnement über genügend Ressourcen verfügt, um das Hinzufügen von Load Balancern für diesen Prozess zu unterstützen.  Weitere Informationen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Vorbereiten und Verschieben
Die folgenden Schritte zeigen, wie Sie den internen Load Balancer für die Verschiebung mit einer Resource Manager-Vorlage vorbereiten und die Konfiguration des internen Load Balancers dann mithilfe des Azure-Portals in die Zielregion verschieben.  Im Rahmen dieses Vorgangs muss die virtuelle Netzwerkkonfiguration des internen Load Balancers eingeschlossen und vor dem Verschieben des internen Load Balancers zuerst durchgeführt werden.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>Exportieren der Vorlage für virtuelle Netzwerke und Bereitstellen über das Azure-Portal

1. Melden Sie sich bei [Azure-Portal](https://portal.azure.com) > **Ressourcengruppen** an.
2. Wechseln Sie zur Ressourcengruppe, die das virtuelle Netzwerk enthält, und klicken Sie darauf.
3. Wählen Sie **Einstellungen** > **Vorlage exportieren** aus.
4. Wählen Sie **Bereitstellen** auf dem Blatt **Vorlage exportieren** aus.
5. Klicken Sie auf **VORLAGE** > **Parameter bearbeiten**, um die Datei **parameters.json** im Online-Editor zu öffnen.
6. Um den Parameter für den Namen des virtuellen Netzwerks zu bearbeiten, ändern Sie unter **parameters** die Eigenschaft **value**:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. Ändern Sie den Wert für den Namen des virtuellen Netzwerks der Quelle im Editor in den Namen Ihrer Wahl für das Ziel-VNet. Stellen Sie sicher, dass Sie den Namen in Anführungszeichen einschließen.

8. Klicken Sie im Editor auf **Speichern**.

9. Klicken Sie auf **VORLAGE** > **Parameter bearbeiten**, um die Datei **parameters.json** im Online-Editor zu öffnen.

10. Zum Bearbeiten der Zielregion, in die das VNET verschoben wird, ändern Sie die Eigenschaft **location** unter „resources“:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```

11. Informationen zum Abrufen von Regionsstandortcodes finden Sie unter [Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/).  Der Code für eine Region ist der Regionsname ohne Leerzeichen, **USA, Mitte** = **centralus**.

12. Sie können wahlweise auch andere Parameter in der Datei **template.json** ändern. Diese sind abhängig von Ihren Anforderungen optional:

    * **Adressraum**: Der Adressraum des VNet kann vor dem Speichern geändert werden, indem der Abschnitt **resources** > **addressSpace** und die Eigenschaft **addressPrefixes** in der Datei **template.json** geändert werden:

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Subnetz**: Der Subnetzname und der Subnetzadressraum können geändert oder hinzugefügt werden, indem Sie den Abschnitt **subnets** der Datei **template.json** ändern. Der Name des Subnetzes kann geändert werden, indem Sie die Eigenschaft **name** ändern. Der Subnetzadressraum kann geändert werden, indem Sie die Eigenschaft **addressPrefix** in der Datei **template.json** ändern:

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

         Die Datei **template.json** muss zum Ändern des Adresspräfixes an zwei Stellen bearbeitet werden: im oben aufgeführten Abschnitt und im unten aufgeführten Abschnitt **type**.  Ändern Sie die Eigenschaft **addressPrefix** so, dass Sie mit der oben genannten Eigenschaft übereinstimmt:

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

13. Klicken Sie im Online-Editor auf **Speichern**.

14. Klicken Sie auf **GRUNDLAGEN** > **Abonnement**,um das Abonnement auszuwählen, in dem das Ziel-VNet bereitgestellt wird.

15. Klicken Sie auf **GRUNDLAGEN** > **Ressourcengruppe**,um die Ressourcengruppe auszuwählen, in der das Ziel-VNet bereitgestellt wird.  Sie können auf **Neu erstellen** klicken, um eine neue Ressourcengruppe für das Ziel-VNET zu erstellen.  Stellen Sie sicher, dass der Name nicht mit dem Namen der Quellressourcengruppe des vorhandenen VNet übereinstimmt.

16. Stellen Sie sicher, dass **GRUNDLAGEN** > **Standort** auf den Zielstandort festgelegt ist, an dem das VNet bereitgestellt werden soll.

17. Stellen Sie unter **EINSTELLUNGEN** sicher, dass der Name mit dem Namen übereinstimmt, den Sie oben im Parameter-Editor eingegeben haben.

18. Aktivieren Sie das Kontrollkästchen unter **NUTZUNGSBEDINGUNGEN**.

19. Klicken Sie auf die Schaltfläche **Kaufen**, um das virtuelle Zielnetzwerk bereitzustellen.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportieren der Vorlage des internen Load Balancers und Bereitstellen aus Azure PowerShell

1. Melden Sie sich bei [Azure-Portal](https://portal.azure.com) > **Ressourcengruppen** an.
2. Suchen Sie die Ressourcengruppe, die den internen Quell-Load Balancer enthält, und klicken Sie darauf.
3. Wählen Sie **Einstellungen** > **Vorlage exportieren** aus.
4. Wählen Sie **Bereitstellen** auf dem Blatt **Vorlage exportieren** aus.
5. Klicken Sie auf **VORLAGE** > **Parameter bearbeiten**, um die Datei **parameters.json** im Online-Editor zu öffnen.

6. Um den Parameter für den Namen des internen Load Balancers zu bearbeiten, ändern Sie die Eigenschaft **defaultValue** des Quellnamens des internen Load Balancers in den Namen Ihres internen Ziel-Load Balancers. Stellen Sie dabei sicher, dass der Name in Anführungszeichen eingeschlossen wird:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. Wenn Sie den Wert des zuvor verschobenen virtuellen Zielnetzwerks bearbeiten möchten, müssen Sie zunächst die Ressourcen-ID abrufen und kopieren und sie dann in die Datei **parameters.json** einfügen. So rufen Sie die ID ab

    1. Melden Sie sich auf einer anderen Browserregisterkarte oder in einem anderen Fenster bei [Azure-Portal](https://portal.azure.com) > **Ressourcengruppen** an.
    2. Suchen Sie nach der Zielressourcengruppe, die das mit den oben beschriebenen Schritten verschobene virtuelle Netzwerk enthält, und klicken Sie darauf.
    3. Wählen Sie **Einstellungen** > **Eigenschaften** aus.
    4. Markieren Sie auf dem Blatt auf der rechten Seite die **Ressourcen-ID**, und kopieren Sie sie in die Zwischenablage.  Alternativ können Sie rechts neben dem Pfad der **Ressourcen-ID** auf die Schaltfläche **In Zwischenablage kopieren** klicken.
    5. Fügen Sie die Ressourcen-ID in die Eigenschaft **DefaultValue** im Editor **Parameter bearbeiten** ein, den Sie in einem anderen Browserfenster oder auf einer anderen Registerkarte geöffnet haben:

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. Klicken Sie im Online-Editor auf **Speichern**.

7. Klicken Sie auf **VORLAGE** > **Parameter bearbeiten**, um die Datei **parameters.json** im Online-Editor zu öffnen.
8. Zum Bearbeiten der Zielregion, in die die Konfiguration des internen Load Balancers verschoben wird, ändern Sie die Eigenschaft **location** unter **resources** in der Datei **template.json**:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

9.  Informationen zum Abrufen von Regionsstandortcodes finden Sie unter [Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/).  Der Code für eine Region ist der Regionsname ohne Leerzeichen, **USA, Mitte** = **centralus**.

10. Sie können wahlweise auch andere Parameter in der Vorlage ändern, die abhängig von Ihren Anforderungen optional sind:

    * **SKU**: Sie können die SKU des internen Load Balancers in der Konfiguration aus „standard“ in „basic“ oder aus „basic“ in „standard“ ändern, indem Sie die Eigenschaft **sku** > **name** in der Datei **template.json** ändern:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Weitere Informationen zu den Unterschieden zwischen Load Balancern der SKU „basic“ und „standard“ finden Sie unter [Übersicht über Azure Load Balancer Standard](./load-balancer-overview.md).

    * **Lastenausgleichsregeln**: Sie können Lastenausgleichsregeln in der Konfiguration hinzufügen oder entfernen, indem Sie Einträge in der Datei **template.json** zum Abschnitt **loadBalancingRules** hinzufügen oder daraus entfernen:

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Weitere Informationen zu Lastenausgleichsregeln finden Sie unter [Was ist Azure Load Balancer?](./load-balancer-overview.md)

    * **Tests**: Sie können in der Konfiguration einen Test des Load Balancers hinzufügen oder entfernen, indem Sie der Datei **template.json** Einträge zum Abschnitt **Probes** hinzufügen oder sie daraus entfernen:

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Weitere Informationen zu Azure Load Balancer-Integritätstests finden Sie unter [Load Balancer-Integritätstests](./load-balancer-custom-probe-overview.md).

    * **NAT-Regeln für eingehenden Datenverkehr**: Sie können NAT-Regeln für eingehenden Datenverkehr für den Load Balancer hinzufügen oder entfernen, indem Sie der Datei **template.json** Einträge zum Abschnitt **inboundNatRules** hinzufügen oder sie daraus entfernen:

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Um das Hinzufügen oder Entfernen einer NAT-Regel für eingehenden Datenverkehr abzuschließen, muss die Regel als Eigenschaft des Typs **type** am Ende der Datei **template.json** vorhanden sein oder aus ihr entfernt werden:

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Weitere Informationen zu NAT-Regeln für eingehenden Datenverkehr finden Sie unter [Was ist Azure Load Balancer?](./load-balancer-overview.md)

12. Klicken Sie im Online-Editor auf **Speichern**.

13. Klicken Sie auf **GRUNDLAGEN** > **Abonnement**,um das Abonnement auszuwählen, in dem der interne Ziel-Load Balancer bereitgestellt wird.

15. Klicken Sie auf **GRUNDLAGEN** > **Ressourcengruppe**, um die Ressourcengruppe auszuwählen, in der der Load Balancer für das Ziel bereitgestellt wird.  Sie können auf **Neu erstellen** klicken, um eine neue Ressourcengruppe für den internen Ziel-Load Balancer zu erstellen, oder Sie können die vorhandene Ressourcengruppe auswählen, die weiter oben für das virtuelle Netzwerk erstellt wurde.  Stellen Sie sicher, dass der Name nicht mit dem Namen der Quellressourcengruppe des vorhandenen internen Quell-Load Balancer übereinstimmt.

16. Stellen Sie sicher, dass **GRUNDLAGEN** > **Standort** auf den Zielstandort festgelegt ist, an dem der interne Load Balancer bereitgestellt werden soll.

17. Stellen Sie unter **EINSTELLUNGEN** sicher, dass der Name mit dem Namen übereinstimmt, den Sie oben im Parameter-Editor eingegeben haben.  Vergewissern Sie sich, dass die Ressourcen-IDs für virtuelle Netzwerke in der Konfiguration mit Angaben aufgefüllt werden.

18. Aktivieren Sie das Kontrollkästchen unter **NUTZUNGSBEDINGUNGEN**.

19. Klicken Sie auf die Schaltfläche **Kaufen**, um das virtuelle Zielnetzwerk bereitzustellen.

## <a name="discard"></a>Verwerfen

Wenn Sie das virtuelle Zielnetzwerk und den internen Load Balancer verwerfen möchten, löschen Sie die Ressourcengruppe, die das virtuelle Zielnetzwerk und den internen Load Balancer enthält.  Wählen Sie hierzu im Portal die Ressourcengruppe im Dashboard aus, und klicken Sie oben auf der Übersichtsseite auf **Löschen**.

## <a name="clean-up"></a>Bereinigung

Um die Änderungen zu übernehmen und die Verschiebung des virtuellen Netzwerks und des internen Load Balancers abzuschließen, löschen Sie das virtuelle Quellnetzwerk und den internen Load Balancer oder die Ressourcengruppe. Wählen Sie hierzu im Portal das virtuelle Netzwerk und den internen Load Balancer oder die Ressourcengruppe im Dashboard aus, und klicken Sie oben auf der Übersichtsseite auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen internen Azure Load Balancer aus einer Region in eine andere verschoben und die Quellressourcen bereinigt.  Weitere Informationen zum Verschieben von Ressourcen zwischen Regionen und zur Notfallwiederherstellung in Azure finden Sie unter:


- [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Verschieben virtueller Azure-Computer in eine andere Region](../site-recovery/azure-to-azure-tutorial-migrate.md)