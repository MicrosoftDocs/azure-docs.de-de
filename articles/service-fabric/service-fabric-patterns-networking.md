---
title: Netzwerkmuster für Azure Service Fabric | Microsoft-Dokumentation
description: In diesem Thema werden häufige Netzwerkmuster für Service Fabric sowie die Vorgehensweise zum Erstellen eines Clusters mithilfe der Netzwerkfeatures von Azure beschrieben.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: aljo
ms.openlocfilehash: d5aa09f3ff899766e6eb6d1784e4417f7b48eac0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66110268"
---
# <a name="service-fabric-networking-patterns"></a>Netzwerkmuster für Service Fabric
Sie können Ihre Azure Service Fabric-Cluster in andere Azure-Netzwerkfeatures integrieren. In diesem Artikel erfahren Sie, wie Sie Cluster erstellen, die folgende Features nutzen:

- [Vorhandenes virtuelles Netzwerk oder Subnetz](#existingvnet)
- [Statische öffentliche IP-Adressen](#staticpublicip)
- [Rein interner Lastenausgleich](#internallb)
- [Interner und externer Lastenausgleich](#internalexternallb)

Service Fabric wird in einer standardmäßigen VM-Skalierungsgruppe ausgeführt. Alle Funktionen, die Sie in einer VM-Skalierungsgruppe verwenden können, können Sie mit einem Service Fabric-Cluster verwenden. Die Netzwerkabschnitte der Azure Resource Manager-Vorlagen für VM-Skalierungsgruppen und Service Fabric sind identisch. Sobald Sie ein vorhandenes virtuelles Netzwerk bereitgestellt haben, ist es einfach, andere Netzwerkfeatures wie Azure ExpressRoute, Azure-VPN-Gateway, eine Netzwerksicherheitsgruppe und Peering virtueller Netzwerke zu integrieren.

Service Fabric unterscheidet sich in einem Aspekt von anderen Netzwerkfeatures. Das [Azure-Portal](https://portal.azure.com) verwendet intern den Service Fabric-Ressourcenanbieter, um Informationen über Knoten und Anwendungen von einem Cluster abzurufen. Der Service Fabric-Ressourcenanbieter erfordert öffentlich zugänglichen eingehenden Zugriff auf den HTTP-Gatewayport (standardmäßig Port 19080) auf dem Verwaltungsendpunkt. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) verwendet den Verwaltungsendpunkt zur Verwaltung Ihres Clusters. Der Service Fabric-Ressourcenanbieter verwendet diesen Port außerdem zur Abfrage von Informationen über Ihren Cluster, um sie im Azure-Portal anzuzeigen. 

Wenn vom Service Fabric-Ressourcenanbieter aus kein Zugriff auf Port 19080 möglich ist, wird eine Meldung wie *Knoten nicht gefunden* im Portal angezeigt, und die Liste Ihrer Knoten und Anwendungen ist leer. Wenn Sie Ihren Cluster im Azure-Portal anzeigen möchten, muss Ihr Lastenausgleich eine öffentliche IP-Adresse bereitstellen, und Ihre Netzwerksicherheitsgruppe muss über Port 19080 eingehenden Datenverkehr zulassen. Wenn Ihr Setup diese Anforderungen nicht erfüllt, zeigt das Azure-Portal den Status Ihres Clusters nicht an.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Vorlagen

Alle Service Fabric-Vorlagen befinden sich in [GitHub](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking). Sie sollten in der Lage sein, die Vorlagen mithilfe der folgenden PowerShell-Befehle ohne Änderungen bereitzustellen. Wenn Sie die vorhandene Azure Virtual Network-Vorlage oder die Vorlage der statischen öffentlichen IP bereitstellen, lesen Sie zuerst den Abschnitt [Anfangssetup](#initialsetup) dieses Artikels.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Anfangssetup

### <a name="existing-virtual-network"></a>Vorhandenes virtuelles Netzwerk

Im folgenden Beispiel beginnen wir mit einem vorhandenen virtuellen Netzwerk mit dem Namen „ExistingRG-vnet“ in der Ressourcengruppe **ExistingRG**. Das Subnetz ist standardmäßig benannt. Diese Standardressourcen werden erstellt, wenn Sie im Azure-Portal einen standardmäßigen virtuellen Computer erstellen. Sie könnten das virtuelle Netzwerk und Subnetz erstellen, ohne den virtuellen Computer zu erstellen, aber einem vorhandenen virtuellen Netzwerk einen Cluster hinzuzufügen dient im Wesentlichen dazu, die Netzwerkkonnektivität mit anderen virtuellen Computern bereitzustellen. Das Erstellen des virtuellen Computers ist ein gutes Beispiel dafür, wie ein vorhandenes virtuelles Netzwerk in der Regel verwendet wird. Wenn Ihr Service Fabric-Cluster nur einen internen Lastenausgleich ohne öffentliche IP-Adresse verwendet, können Sie die VM mit öffentlicher IP-Adresse als *Jumpbox* verwenden.

### <a name="static-public-ip-address"></a>Statische öffentliche IP-Adressen

Eine statische öffentliche IP-Adresse ist im Allgemeinen eine dedizierte Ressourcen, die getrennt von dem virtuellen Computer bzw. den virtuellen Computern verwaltet wird, dem bzw. denen sie zugewiesen ist. Sie wird in einer dedizierten Netzwerkressourcengruppe bereitgestellt (statt in der Service Fabric-Clusterressourcengruppe selbst). Erstellen Sie entweder über das Azure-Portal oder PowerShell eine statische öffentliche IP-Adresse mit dem Namen staticIP1 in der gleichen Ressourcengruppe ExistingRG:

```powershell
PS C:\Users\user> New-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Service Fabric-Vorlage

In den Beispielen in diesem Artikel wird die Service Fabric-template.json verwendet. Sie können die Vorlage mit dem standardmäßigen Portal-Assistenten vom Portal herunterladen, bevor Sie einen Cluster erstellen. Sie können auch eine der [Beispielvorlagen](https://github.com/Azure-Samples/service-fabric-cluster-templates) verwenden, z.B. den [sicheren Service Fabric-Cluster mit fünf Knoten](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Vorhandenes virtuelles Netzwerk oder Subnetz

1. Ändern Sie den Parameter des Subnetzes in den Namen des vorhandenen Subnetzes, und fügen Sie zwei weitere Parameter hinzu, um auf das vorhandene virtuelle Netzwerk zu verweisen:

    ```json
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```

2. Kommentieren Sie das Attribut `nicPrefixOverride` von `Microsoft.Compute/virtualMachineScaleSets` aus, da Sie das vorhandene Subnetz verwenden und diese Variable in Schritt 1 deaktiviert haben.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Ändern Sie die Variable `vnetID` so, dass sie auf das vorhandene virtuelle Netzwerk zeigt:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Entfernen Sie `Microsoft.Network/virtualNetworks` aus Ihren Ressourcen, sodass Azure kein neues virtuelles Netzwerk erstellt:

    ```json
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

5. Kommentieren Sie das virtuelle Netzwerk aus dem `dependsOn`-Attribut von `Microsoft.Compute/virtualMachineScaleSets` aus, sodass Sie nicht vom Erstellen eines neuen virtuellen Netzwerks abhängig sind:

    ```json
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

6. Stellen Sie die Vorlage bereit:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Nach der Bereitstellung sollte Ihr virtuelles Netzwerk die neuen Skalierungsgruppen-VMs enthalten. Der Knotentyp der VM-Skalierungsgruppe sollte das vorhandene virtuelle Netzwerk und Subnetz anzeigen. Sie können auch mit dem Remotedesktopprotokoll (Remote Desktop Protocol, RDP) auf den virtuellen Computer zugreifen, der sich bereits im virtuellen Netzwerk befand, und die neuen Skalierungsgruppen-VMs pingen:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Ein weiteres Beispiel, das nicht für Service Fabric spezifisch ist, finden Sie [hier](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statische öffentliche IP-Adressen

1. Fügen Sie Parameter für den Namen der vorhandenen Ressourcengruppe der statischen IP-Adresse, den Namen und den vollqualifizierten Domänennamen (FQDN) hinzu:

    ```json
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. Entfernen Sie den `dnsName`-Parameter. (Die statische IP-Adresse hat bereits einen.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Fügen Sie eine Variable hinzu, um auf die vorhandene statische IP-Adresse zu verweisen:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Entfernen Sie `Microsoft.Network/publicIPAddresses` aus Ihren Ressourcen, sodass Azure keine neue IP-Adresse erstellt:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. Kommentieren Sie die IP-Adresse aus dem `dependsOn`-Attribut von `Microsoft.Network/loadBalancers` aus, sodass Sie nicht vom Erstellen einer neuen IP-Adresse abhängig sind:

    ```json
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. Ändern Sie in der `Microsoft.Network/loadBalancers`-Ressource das `publicIPAddress`-Element von `frontendIPConfigurations` so, dass auf die vorhandene statische IP-Adresse statt auf eine neu erstellte verwiesen wird:

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. Ändern Sie in der `Microsoft.ServiceFabric/clusters`-Ressource `managementEndpoint` in den DNS-FQDN der statischen IP-Adresse. Wenn Sie einen sicheren Cluster verwenden, stellen Sie sicher, dass Sie *http://* in *https://* ändern. (Beachten Sie, dass dieser Schritt nur für Service Fabric-Cluster gilt. Wenn Sie eine VM-Skalierungsgruppe verwenden, überspringen Sie diesen Schritt.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Stellen Sie die Vorlage bereit:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Nach der Bereitstellung sehen Sie, dass Ihr Lastenausgleich an die öffentliche statische IP-Adresse aus der anderen Ressourcengruppe gebunden ist. Der Service Fabric-Clientverbindungsendpunkt und der [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)-Endpunkt zeigen beide auf den DNS-FDQN der statischen IP-Adresse.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Rein interner Lastenausgleich

Dieses Szenario ersetzt den externen Lastenausgleich in der standardmäßigen Service Fabric-Vorlage durch einen rein internen Lastenausgleich. Informationen zu Konsequenzen für das Azure-Portal und den Service Fabric-Ressourcenanbieter finden Sie im vorherigen Abschnitt.

1. Entfernen Sie den `dnsName`-Parameter. (Er ist nicht erforderlich.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Optional können Sie einen statischen IP-Adressparameter hinzufügen, wenn Sie eine statische Zuordnungsmethode verwenden. Wenn Sie eine dynamische Zuordnungsmethode verwenden, müssen Sie diesen Schritt nicht ausführen.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Entfernen Sie `Microsoft.Network/publicIPAddresses` aus Ihren Ressourcen, sodass Azure keine neue IP-Adresse erstellt:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. Entfernen Sie das IP-Adressen-`dependsOn`-Attribut von `Microsoft.Network/loadBalancers`, sodass Sie nicht vom Erstellen einer neuen IP-Adresse abhängig sind. Fügen Sie das Attribut `dependsOn` des virtuellen Netzwerks hinzu, da der Lastenausgleich jetzt vom Subnetz aus dem virtuellen Netzwerk abhängig ist:

    ```json
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Ändern Sie die Einstellung `frontendIPConfigurations` des Lastenausgleichs von der Verwendung einer `publicIPAddress` in die Verwendung eines Subnetzes und einer `privateIPAddress`. `privateIPAddress` verwendet eine vordefinierte statische interne IP-Adresse. Um eine dynamische IP-Adresse zu verwenden, entfernen Sie das `privateIPAddress`-Element, und ändern Sie dann `privateIPAllocationMethod` in **Dynamisch**.

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. Ändern Sie in der `Microsoft.ServiceFabric/clusters`-Ressource `managementEndpoint` so, dass auf die interne Adresse des Lastenausgleichs verwiesen wird. Wenn Sie einen sicheren Cluster verwenden, stellen Sie sicher, dass Sie *http://* in *https://* ändern. (Beachten Sie, dass dieser Schritt nur für Service Fabric-Cluster gilt. Wenn Sie eine VM-Skalierungsgruppe verwenden, überspringen Sie diesen Schritt.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Stellen Sie die Vorlage bereit:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Nach der Bereitstellung verwendet Ihr Lastenausgleich die private statische IP-Adresse 10.0.0.250. Wenn Sie in diesem gleichen virtuellen Netzwerk über einen anderen Computer verfügen, können Sie zu dem internen [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)-Endpunkt gehen. Beachten Sie, dass er eine Verbindung mit einem der Knoten hinter dem Lastenausgleich herstellt.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Interner und externer Lastenausgleich

In diesem Szenario beginnen Sie mit dem vorhandenen externen Lastenausgleich mit einem einzigen Knotentyp und fügen einen internen Lastenausgleich für den gleichen Knotentyp hinzu. Ein Back-End-Port, der einem Back-End-Adresspool angefügt ist, kann nur einem einzelnen Lastenausgleich zugewiesen werden. Wählen Sie, welcher Lastenausgleich Ihre Anwendungsports und welcher Lastenausgleich Ihre Verwaltungsendpunkte (Ports 19000 und 19080) enthalten soll. Wenn Sie die Verwaltungsendpunkte auf den internen Lastenausgleich legen, beachten Sie die weiter oben in diesem Artikel erläuterten Service Fabric-Ressourcenanbieter-Einschränkungen. In unserem Beispiel verbleiben die Verwaltungsendpunkte auf dem externen Lastenausgleich. Sie können auch einen Port 80-Anwendungsport hinzufügen und auf dem internen Lastenausgleich platzieren.

In einem Zwei-Knotentypen-Cluster befindet sich ein Knotentyp auf dem externen Lastenausgleich. Der andere Knotentyp befindet sich auf dem internen Lastenausgleich. Um einen Zwei-Knotentypen-Cluster zu verwenden, wandeln Sie in der im Portal erstellten Zwei-Knotentypen-Vorlage (mit zwei Lastenausgleichsmodulen) den zweiten Lastenausgleich in einen internen Lastenausgleich um. Weitere Informationen finden Sie unter [Rein interner Lastenausgleich](#internallb).

1. Fügen Sie den statischen IP-Adressparameter für den internen Lastenausgleich hinzu. (Hinweise zur Verwendung einer dynamischen IP-Adresse finden Sie in vorherigen Abschnitten dieses Artikels.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Fügen Sie einen Parameter für den Anwendungsport 80 hinzu.

3. Um interne Versionen der vorhandenen Netzwerkvariablen hinzuzufügen, kopieren Sie sie, fügen Sie sie ein, und fügen Sie ihrem Namen das Suffix „-Int“ an:

    ```json
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Wenn Sie mit der vom Portal generierten Vorlage mit Anwendungsport 80 beginnen, fügt die Standardportalvorlage dem externen Lastenausgleich AppPort1 (Port 80) hinzu. Entfernen Sie in diesem Fall AppPort1 vom externen Lastenausgleich `loadBalancingRules` und von Tests, damit Sie ihn später dem internen Lastenausgleich hinzufügen können:

    ```json
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. Fügen Sie eine zweite `Microsoft.Network/loadBalancers`-Ressource hinzu. Sie ähnelt dem im Abschnitt [Rein interner Lastenausgleich](#internallb) erstellten internen Lastenausgleich, verwendet jedoch die „-Int“-Lastenausgleichsvariablen und implementiert nur den Anwendungsport 80. Dies entfernt auch `inboundNatPools`, um die RDP-Endpunkte auf dem öffentlichen Lastenausgleich beizubehalten. Wenn Sie die RDP-Endpunkte auf dem internen Lastenausgleich wünschen, verschieben Sie `inboundNatPools` vom externen Lastenausgleich auf diesen internen Lastenausgleich:

    ```json
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LoadBalancerBEAddressPool",
                            "properties": {}
                        }
                    ],
                    "loadBalancingRules": [
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. Fügen Sie in `networkProfile` für die `Microsoft.Compute/virtualMachineScaleSets`-Ressource den internen Back-End-Adresspool hinzu:

    ```json
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. Stellen Sie die Vorlage bereit:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Nach der Bereitstellung sehen Sie zwei Lastenausgleichsmodule in der Ressourcengruppe. Wenn Sie die Lastenausgleichsmodule durchsuchen, sehen Sie die öffentliche IP-Adresse und die Verwaltungsendpunkte (Ports 19000 und 19080), die der öffentlichen IP-Adresse zugewiesen sind. Sie können auch die statische interne IP-Adresse und den Anwendungsendpunkt (Port 80) sehen, die dem internen Lastenausgleich zugewiesen sind. Beide Lastenausgleichsmodule verwenden den gleichen VM-Skalierungsgruppen-Back-End-Pool.

## <a name="next-steps"></a>Nächste Schritte
[Erstellen eines Clusters](service-fabric-cluster-creation-via-arm.md) ternalLB.json
    ```

Nach der Bereitstellung sehen Sie zwei Lastenausgleichsmodule in der Ressourcengruppe. Wenn Sie die Lastenausgleichsmodule durchsuchen, sehen Sie die öffentliche IP-Adresse und die Verwaltungsendpunkte (Ports 19000 und 19080), die der öffentlichen IP-Adresse zugewiesen sind. Sie können auch die statische interne IP-Adresse und den Anwendungsendpunkt (Port 80) sehen, die dem internen Lastenausgleich zugewiesen sind. Beide Lastenausgleichsmodule verwenden den gleichen VM-Skalierungsgruppen-Back-End-Pool.

## <a name="next-steps"></a>Nächste Schritte
[Erstellen eines Clusters](service-fabric-cluster-creation-via-arm.md)
