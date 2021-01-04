---
title: Verschieben eines externen Azure-Lastenausgleichs in eine andere Azure-Region über das Azure-Portal
description: Verwenden einer Azure Resource Manager-Vorlage, um einen externen Lastenausgleich über das Azure-Portal aus einer Azure-Region in eine andere zu verschieben.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f83ff3d1d03354daef3466c1f48eaa505e378634
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94693748"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Verschieben eines externen Lastenausgleichs in eine andere Region über das Azure-Portal

Es gibt verschiedene Szenarien, in denen Sie einen externen Lastenausgleich aus einer Region in eine andere verschieben können. Beispielsweise können Sie zu Testzwecken einen weiteren externen Lastenausgleich mit der gleichen Konfiguration erstellen. Möglicherweise möchten Sie einen externen Lastenausgleich auch bei der Planung der Notfallwiederherstellung in eine andere Region verschieben.

Im eigentlichen Sinn können Sie einen externen Azure Lastenausgleich nicht von einer Region in eine andere verschieben. Sie können jedoch eine Azure Resource Manager-Vorlage verwenden, um die vorhandene Konfiguration und die öffentliche IP-Adresse eines externen Lastenausgleichs zu exportieren. Anschließend können Sie die Ressource in einer anderen Region bereitstellen, indem Sie den Lastenausgleich und die öffentliche IP-Adresse in eine Vorlage exportieren, die Parameter so ändern, dass sie der Zielregion entsprechen, und die Vorlage dann in der neuen Region bereitstellen. Weitere Informationen zu Resource Manager und Vorlagen finden Sie unter [Exportieren von Ressourcengruppen in Vorlagen](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass sich der externe Azure-Lastenausgleich in der Azure-Region befindet, aus der Sie ihn verschieben möchten.

- Externe Azure Load Balancer können nicht zwischen Regionen verschoben werden. Sie müssen den neuen Load Balancer Ressourcen in der Zielregion zuordnen.

- Zum Exportieren der Konfiguration eines externen Lastenausgleichs und Bereitstellen einer Vorlage zum Erstellen eines externen Lastenausgleichs in einer anderen Region muss Ihnen mindestens die Rolle „Netzwerkmitwirkender“ zugewiesen sein.

- Identifizieren Sie das Layout des Quellnetzwerks und alle Ressourcen, die Sie aktuell verwenden. Dieses Layout umfasst Load Balancer, Netzwerksicherheitsgruppen, öffentliche IP-Adressen und virtuelle Netzwerke, ohne darauf beschränkt zu sein.

- Vergewissern Sie sich, dass Sie mit Ihrem Azure-Abonnement externe Lastenausgleichsmodule in der Zielregion erstellen können. Wenden Sie sich an den Support, um das erforderliche Kontingent zu aktivieren.

- Stellen Sie sicher, dass Ihr Abonnement über ausreichend Ressourcen verfügt, um das Hinzufügen der Lastenausgleichsmodule zu unterstützen. Weitere Informationen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

## <a name="prepare-and-move"></a>Vorbereiten und Verschieben
Die folgenden Schritte zeigen, wie Sie den externen Lastenausgleich für die Verschiebung mithilfe einer Resource Manager-Vorlage vorbereiten und die Konfiguration des externen Lastenausgleichs dann im Azure-Portal in die Zielregion verschieben. Sie müssen zunächst die Konfiguration der öffentlichen IP-Adresse des externen Lastenausgleichs exportieren.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>Exportieren der Vorlage für die öffentliche IP-Adresse und Bereitstellen der öffentlichen IP-Adresse über das Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Ressourcengruppen**.
2. Suchen Sie die Ressourcengruppe, die die öffentliche IP-Adresse der Quelle enthält, und wählen Sie sie aus.
3. Wählen Sie **Einstellungen** > **Vorlage exportieren** aus.
4. Wählen Sie unter **Vorlage exportieren** die Option **Bereitstellen** aus.
5. Wählen Sie **VORLAGE** > **Parameter bearbeiten** aus, um die Datei „parameters.json“ im Online-Editor zu öffnen.
8. Um den Parameter für den Namen der öffentlichen IP-Adresse zu bearbeiten, ändern Sie unter **parameters** die **value**-Eigenschaft vom Namen der öffentlichen IP-Adresse der Quelle in den Namen der öffentlichen IP-Adresse des Ziels. Setzen Sie den Namen in Anführungszeichen.

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    Wählen Sie im Editor die Option **Speichern** aus.

9.  Wählen Sie **VORLAGE** > **Parameter bearbeiten** aus, um die Datei „template.json“ im Online-Editor zu öffnen.

10. Zum Bearbeiten der Zielregion, in die die öffentliche IP-Adresse verschoben wird, ändern Sie unter **resources** die **location**-Eigenschaft:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```
  
    Informationen zum Abrufen von Regionsstandortcodes finden Sie unter [Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/). Der Code für eine Region ist der Regionsname ohne Leerzeichen. Der Code für „USA, Mitte“ (Central US) lautet beispielsweise **centralus**.
    
12. Sie können wahlweise auch andere Parameter in der Vorlage ändern, die abhängig von Ihren Anforderungen optional oder erforderlich sind:

    * **SKU:** Sie können die SKU der öffentlichen IP-Adresse in der Konfiguration von „standard“ in „basic“ oder von „basic“ in „standard“ ändern, indem Sie die Eigenschaft **name** unter **sku** in der Datei „template.json“ ändern:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Informationen zu den Unterschieden zwischen den SKUs „basic“ und „standard“ für öffentliche IP-Adressen finden Sie unter [Erstellen, Ändern oder Löschen einer öffentlichen IP-Adresse](../virtual-network/virtual-network-public-ip-address.md).

    * **Zuweisungsmethode für öffentliche IP** und **Leerlauftimeout**: Sie können die Zuweisungsmethode für die öffentliche IP-Adresse ändern, indem Sie die Eigenschaft **publicIPAllocationMethod** von **Dynamic** in **Static** oder von **Static** in **Dynamic** ändern. Sie können das Leerlauftimeout ändern, indem Sie die Eigenschaft **idleTimeoutInMinutes** in den gewünschten Wert ändern. Der Standardwert ist **4**.

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Informationen zu den Zuweisungsmethoden und den Werten für das Leerlauftimeout finden Sie unter [Erstellen, Ändern oder Löschen einer öffentlichen IP-Adresse](../virtual-network/virtual-network-public-ip-address.md).

 
13. Wählen Sie im Online-Editor die Option **Speichern** aus.

14. Wählen Sie **GRUNDEINSTELLUNGEN** > **Abonnement** aus, um das Abonnement auszuwählen, in dem die öffentliche IP-Adresse des Ziels bereitgestellt wird.

15. Wählen Sie **GRUNDEINSTELLUNGEN** > **Ressourcengruppe** aus, um die Ressourcengruppe auszuwählen, in der die öffentliche IP-Adresse des Ziels bereitgestellt wird. Sie können **Neu erstellen** auswählen, um eine neue Ressourcengruppe für die öffentliche IP-Adresse des Ziels zu erstellen. Stellen Sie sicher, dass der Name nicht mit dem Namen der Quellressourcengruppe der vorhandenen öffentlichen IP-Adresse der Quelle übereinstimmt.

16. Vergewissern Sie sich, dass **GRUNDEINSTELLUNGEN** > **Standort** auf den Zielstandort festgelegt ist, an dem die öffentliche IP-Adresse bereitgestellt werden soll.

17. Stellen Sie unter **EINSTELLUNGEN** sicher, dass der Name mit dem übereinstimmt, den Sie zuvor im Parameter-Editor eingegeben haben.

18. Aktivieren Sie das Kontrollkästchen **GESCHÄFTSBEDINGUNGEN**.

19. Wählen Sie **Kaufen** aus, um die öffentliche IP-Adresse des Ziels bereitzustellen.

20. Wenn Sie eine andere öffentliche IP-Adresse haben, die für den zu verschiebenden Lastenausgleich für ausgehende NAT (Network Address Translation, Netzwerkadressübersetzung) verwendet wird, wiederholen Sie die vorherigen Schritte, um die zweite ausgehende öffentliche IP-Adresse zu exportieren und in der Zielregion bereitzustellen.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>Exportieren der Vorlage des externen Lastenausgleichs und Bereitstellen des Lastenausgleichs über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Ressourcengruppen**.
2. Suchen Sie die Ressourcengruppe, die den externen Lastenausgleich der Quelle enthält, und wählen Sie sie aus.
3. Wählen Sie **Einstellungen** > **Vorlage exportieren** aus.
4. Wählen Sie unter **Vorlage exportieren** die Option **Bereitstellen** aus.
5. Wählen Sie **VORLAGE** > **Parameter bearbeiten** aus, um die Datei „parameters.json“ im Online-Editor zu öffnen.

5. Um den Parameter für den Namen des externen Lastenausgleichs zu bearbeiten, ändern Sie die Eigenschaft **value** des Namens des externen Lastenausgleichs der Quelle in den Namen des externen Lastenausgleichs für das Ziel. Setzen Sie den Namen in Anführungszeichen.

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  Wenn Sie den Wert der in den vorherigen Schritten verschobenen öffentlichen IP-Adresse des Ziels bearbeiten möchten, müssen Sie die Ressourcen-ID abrufen und dann in die Datei „parameters.json“ einfügen. So rufen Sie die ID ab

    1. Melden Sie sich auf einer anderen Browserregisterkarte oder in einem anderen Browserfenster beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Ressourcengruppen** aus.
    2. Suchen Sie die Zielressourcengruppe, die die in den vorherigen Schritten verschobene öffentliche IP-Adresse enthält. Wählen Sie dies aus.
    3. Wählen Sie **Einstellungen** > **Eigenschaften** aus.
    4. Markieren Sie auf dem Blatt auf der rechten Seite die **Ressourcen-ID**, und kopieren Sie sie in die Zwischenablage. Alternativ können Sie rechts neben dem Pfad der **Ressourcen-ID** die Schaltfläche **In Zwischenablage kopieren** auswählen.
    5. Fügen Sie die Ressourcen-ID in die Eigenschaft **value** im Editor **Parameter bearbeiten** ein, der im anderen Browserfenster oder auf der anderen Browserregisterkarte geöffnet ist:

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. Wählen Sie im Online-Editor die Option **Speichern** aus.


7.  Wenn Sie ausgehende NAT und Regeln für ausgehenden Datenverkehr für den Lastenausgleich konfiguriert haben, ist in dieser Datei ein dritter Eintrag für die externe ID für die ausgehende öffentliche IP-Adresse vorhanden. Wiederholen Sie die vorherigen Schritte in der **Zielregion**, um die ID der ausgehenden öffentlichen IP-Adresse zu erhalten. Fügen Sie diese ID in der Datei „parameters.json“ ein:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  Wählen Sie **VORLAGE** > **Parameter bearbeiten** aus, um die Datei „template.json“ im Online-Editor zu öffnen.
9.  Zum Bearbeiten der Zielregion, in die die Konfiguration des externen Lastenausgleichs verschoben wird, ändern Sie die Eigenschaft **location** unter **resources** in der Datei „template.json“:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

10. Informationen zum Abrufen von Regionsstandortcodes finden Sie unter [Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/). Der Code für eine Region ist der Regionsname ohne Leerzeichen. Der Code für „USA, Mitte“ (Central US) lautet beispielsweise **centralus**.

11. Sie können wahlweise auch andere Parameter in der Vorlage ändern, die abhängig von Ihren Anforderungen optional oder erforderlich sind:

    * **SKU:** Sie können die SKU des externen Lastenausgleichs in der Konfiguration von „standard“ in „basic“ oder von „basic“ in „standard“ ändern, indem Sie die Eigenschaft **name** unter **sku** in der Datei „template.json“ ändern:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Informationen zu den Unterschieden zwischen Lastenausgleichsmodulen der SKU „basic“ und „standard“ finden Sie unter [Übersicht: Azure Load Balancer Standard](./load-balancer-overview.md).

    * **Lastenausgleichsregeln:** Sie können Lastenausgleichsregeln in der Konfiguration hinzufügen oder entfernen, indem Sie Einträge in der Datei „template.json“ im Abschnitt **loadBalancingRules** hinzufügen oder entfernen:

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
       Informationen zu Lastenausgleichsregeln finden Sie unter [Was versteht man unter Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Tests:** Sie können in der Konfiguration einen Test des Lastenausgleichs hinzufügen oder entfernen, indem Sie Einträge in der Datei „template.json“ im Abschnitt **probes** hinzufügen oder entfernen:

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
       Weitere Informationen finden Sie unter [Lastenausgleichs-Integritätstests](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

    * **NAT-Regeln für eingehenden Datenverkehr:** Sie können NAT-Regeln für eingehenden Datenverkehr für den Lastenausgleich hinzufügen oder entfernen, indem Sie Einträge in der Datei „template.json“ im Abschnitt **inboundNatRules** hinzufügen oder entfernen:

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
        Um das Hinzufügen oder Entfernen einer NAT-Regel für eingehenden Datenverkehr abzuschließen, muss die Regel als Eigenschaft des Typs **type** am Ende der Datei „template.json“ vorhanden sein oder daraus entfernt werden:

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
        Informationen zu NAT-Regeln für eingehenden Datenverkehr finden Sie unter [Was versteht man unter Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Regeln für ausgehenden Datenverkehr:** Sie können Regeln für ausgehenden Datenverkehr in der Konfiguration hinzufügen oder entfernen, indem Sie die Eigenschaft **outboundRules** in der Datei „template.json“ bearbeiten:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Weitere Informationen finden Sie unter [Lastenausgleichs-Ausgangsregeln](./load-balancer-outbound-connections.md#outboundrules).

12. Wählen Sie im Online-Editor die Option **Speichern** aus.

13. Wählen Sie **GRUNDEINSTELLUNGEN** > **Abonnement** aus, um das Abonnement auszuwählen, in dem der externe Lastenausgleich des Ziels bereitgestellt wird.

15. Wählen Sie **GRUNDEINSTELLUNGEN** > **Ressourcengruppe** aus, um die Ressourcengruppe auszuwählen, in der der externe Lastenausgleich des Ziels bereitgestellt wird. Sie können **Neu erstellen** auswählen, um eine neue Ressourcengruppe für den externen Lastenausgleich des Ziels zu erstellen. Alternativ können Sie die vorhandene Ressourcengruppe auswählen, die Sie zuvor für die öffentliche IP-Adresse erstellt haben. Stellen Sie sicher, dass der Name nicht mit dem Namen der Quellressourcengruppe des vorhandenen externen Lastenausgleichs der Quelle übereinstimmt.

16. Vergewissern Sie sich, dass **GRUNDEINSTELLUNGEN** > **Standort** auf den Zielstandort festgelegt ist, an dem der externe Lastenausgleich bereitgestellt werden soll.

17. Stellen Sie unter **EINSTELLUNGEN** sicher, dass der Name mit dem Namen übereinstimmt, den Sie zuvor im Parameter-Editor eingegeben haben. Vergewissern Sie sich, dass die Ressourcen-IDs für alle öffentlichen IP-Adressen in der Konfiguration entsprechend gefüllt werden.

18. Aktivieren Sie das Kontrollkästchen **GESCHÄFTSBEDINGUNGEN**.

19. Wählen Sie **Kaufen** aus, um die öffentliche IP-Adresse des Ziels bereitzustellen.

## <a name="discard"></a>Verwerfen

Wenn Sie die öffentliche IP-Adresse und den externen Lastenausgleich des Ziels verwerfen möchten, löschen Sie die Ressourcengruppe, in der sie enthalten sind. Wählen Sie hierzu im Portal die Ressourcengruppe im Dashboard aus, und wählen Sie dann oben auf der Übersichtsseite **Löschen** aus.

## <a name="clean-up"></a>Bereinigung

Um die Änderungen zu übernehmen und die Verschiebung der öffentlichen IP-Adresse und des externen Load Balancers abzuschließen, löschen Sie die öffentliche IP-Adresse sowie den externen Load Balancer oder die Ressourcengruppe der Quelle. Wählen Sie hierzu im Portal die Ressourcengruppe im Dashboard aus, und wählen Sie dann oben auf jeder Seite **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen externen Azure Load Balancer aus einer Region in eine andere verschoben und die Quellressourcen bereinigt. Weitere Informationen zum Verschieben von Ressourcen zwischen Regionen und zur Notfallwiederherstellung in Azure finden Sie in folgenden Artikeln:


- [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Verschieben virtueller Azure-Computer in eine andere Region](../site-recovery/azure-to-azure-tutorial-migrate.md)