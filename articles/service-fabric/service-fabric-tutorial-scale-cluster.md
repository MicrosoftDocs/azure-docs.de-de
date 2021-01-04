---
title: Skalieren eines Service Fabric-Clusters in Azure
description: In diesem Tutorial erfahren Sie, wie Sie einen Service Fabric-Cluster in Azure erweitern und horizontal herunterskalieren und übrig gebliebene Ressourcen bereinigen.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7f92ca28afd9d1894867aaa2c18df3a02ee0bd79
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842684"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Tutorial: Skalieren eines Service Fabric-Clusters in Azure

Dieses Tutorial ist der dritte Teil einer Reihe und zeigt, wie Sie Ihren vorhandenen Cluster horizontal hoch- und herunterskalieren. In diesem Tutorial erfahren Sie, wie Sie Ihren Cluster skalieren und gegebenenfalls zurückgebliebene Ressourcen bereinigen.  Weitere Informationen zum Skalieren eines Clusters, der in Azure ausgeführt wird, finden Sie unter [Skalieren von Service Fabric-Clustern](service-fabric-cluster-scaling.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen und Entfernen von Knoten (Auf- und Abskalieren)
> * Hinzufügen und Entfernen von Knotentypen (Auf- und Abskalieren)
> * Erhöhen von Knotenressourcen (Hochskalieren)

In dieser Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * Erstellen eines sicheren [Windows-Clusters](service-fabric-tutorial-create-vnet-and-windows-cluster.md) in Azure mithilfe einer Vorlage
> * [Überwachen eines Clusters](service-fabric-tutorial-monitor-cluster.md)
> * Horizontales Herunter- oder Hochskalieren eines Clusters
> * [Aktualisieren der Runtime eines Clusters](service-fabric-tutorial-upgrade-cluster.md)
> * [Löschen eines Clusters](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installieren Sie [Azure PowerShell](/powershell/azure/install-az-ps) oder die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).
* Erstellen eines sicheren [Windows-Clusters](service-fabric-tutorial-create-vnet-and-windows-cluster.md) in Azure

## <a name="important-considerations-and-guidelines"></a>Wichtige Aspekte und Richtlinien

Workloads von Anwendungen ändern sich im Laufe der Zeit. Benötigen Ihre vorhandenen Dienste mehr (oder weniger) Ressourcen?  [Fügen Sie Knoten hinzu, oder entfernen Sie sie](#add-nodes-to-or-remove-nodes-from-a-node-type) von einem Knotentyp, um die Clusterressourcen zu erhöhen oder zu verringern.

Möchten Sie Ihrem Cluster mehr als 100 Knoten hinzufügen?  Ein/e einzelne/r Service Fabric-Knotentyp/-Skalierungsgruppe kann nicht mehr als 100 Knoten/VMs enthalten.  Zum Skalieren eines Clusters auf über 100 Knoten [fügen Sie zusätzliche Knotentypen hinzu](#add-nodes-to-or-remove-nodes-from-a-node-type).

Weist Ihre Anwendung mehrere Dienste auf, und müssen einige dieser Dienste öffentlich sein oder über Internetzugriff verfügen?  Typische Anwendungen umfassen einen Front-End-Gatewaydienst, der Eingaben von einem Client empfängt, und einen oder mehrere Back-End-Dienste, die mit den Front-End-Diensten kommunizieren. In diesem Fall empfehlen wir Ihnen, dem Cluster [mindestens zwei Knotentypen hinzuzufügen](#add-nodes-to-or-remove-nodes-from-a-node-type).  

Haben Ihre Dienste unterschiedliche Infrastrukturanforderungen, z. B. höhere RAM-Anforderungen oder längere CPU-Zyklen? Ihre Anwendung umfasst beispielsweise einen Front-End-Dienst und einen Back-End-Dienst. Der Front-End-Dienst kann auf kleineren virtuellen Computern (VM-Größen wie D2) platziert werden, die über geöffnete Ports für das Internet verfügen. Der rechenintensive Back-End-Dienst hingegen muss auf größeren virtuellen Computern (mit VM-Größen wie D4, D6, D15) platziert werden, die nicht vom Internet aus zugänglich sind. In diesem Fall empfehlen wir Ihnen, Ihrem Cluster [zwei oder mehr Knotentypen hinzuzufügen](#add-nodes-to-or-remove-nodes-from-a-node-type). Dadurch können für die Knotentypen unterschiedliche Eigenschaften (z.B. Internetkonnektivität oder VM-Größe) festgelegt werden. Außerdem kann die Anzahl von VMs individuell skaliert werden.

Beachten Sie beim Skalieren eines Azure-Clusters die folgenden Richtlinien:

* Ein/e einzelne/r Service Fabric-Knotentyp/-Skalierungsgruppe kann nicht mehr als 100 Knoten/VMs enthalten.  Zum Skalieren eines Clusters auf über 100 Knoten fügen Sie zusätzliche Knotentypen hinzu.
* Primäre Knotentypen mit Ausführung von Produktionsworkloads sollten über eine [Dauerhaftigkeitsstufe][durability] von „Gold“ oder „Silber“ und immer über mindestens fünf Knoten verfügen.
* Andere (nicht primäre) Knotentypen mit Ausführung von zustandsbehafteten Produktionsworkloads sollten immer über mindestens fünf Knoten verfügen.
* Andere (nicht primäre) Knotentypen mit Ausführung von zustandslosen Produktionsworkloads sollten immer über mindestens zwei Knoten verfügen.
* Jeder Knotentyp mit der [Dauerhaftigkeitsstufe][durability] „Gold“ oder „Silber“ sollte immer über mindestens fünf Knoten verfügen.
* Beim horizontalen Herunterskalieren (Entfernen von Knoten) eines primären Knotentyps sollten Sie nie die Anzahl der Instanzen auf einen niedrigeren Wert verringern, als für die [Zuverlässigkeitsstufe][reliability] erforderlich ist.

Weitere Informationen finden Sie unter [Anleitung zu Clusterkapazitäten](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>Exportieren der Vorlage für die Ressourcengruppe

Nach dem Erstellen eines sicheren [Windows-Clusters](service-fabric-tutorial-create-vnet-and-windows-cluster.md) und dem erfolgreichen Einrichten der Ressourcengruppe exportieren Sie die Resource Manager-Vorlage für die Ressourcengruppe. Durch das Exportieren der Vorlage können Sie zukünftige Bereitstellungen des Clusters und seiner Ressourcen automatisieren, da die Vorlage die gesamte Infrastruktur enthält.  Weitere Informationen zum Exportieren von Vorlagen finden Sie unter [Verwalten von Azure Resource Manager-Gruppen mithilfe des Azure-Portals](../azure-resource-manager/management/manage-resource-groups-portal.md).

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu der Ressourcengruppe, die den Cluster enthält (**sfclustertutorialgroup**, wenn Sie dieses Tutorial durcharbeiten). 

2. Wählen Sie im linken Bereich **Bereitstellungen** aus, oder klicken Sie auf den Link unter **Bereitstellungen**. 

3. Wählen Sie die neueste erfolgreiche Bereitstellung aus der Liste aus.

4. Wählen Sie im linken Bereich **Vorlage** und dann **Herunterladen** aus, um die Vorlage als ZIP-Datei zu exportieren.  Speichern Sie die Vorlage und die Parameter auf Ihrem lokalen Computer.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Hinzufügen von Knoten zu oder Entfernen von Knoten von einem Knotentyp

Horizontales Herunter- und Hochskalieren oder horizontale Skalierung ändern die Anzahl der Knoten im Cluster. Beim Ab- und Aufskalieren fügen Sie der Skalierungsgruppe mehr VM-Instanzen hinzu. Diese Instanzen werden zu den Knoten, die Service Fabric verwendet. Service Fabric erkennt, wenn der Skalierungsgruppe durch horizontales Hochskalieren weitere Instanzen hinzugefügt werden, und reagiert automatisch. Sie können die Skalierung für den Cluster jederzeit durchführen – auch bei Ausführung von Workloads im Cluster.

### <a name="update-the-template"></a>Aktualisieren der Vorlage

[Exportieren Sie eine Vorlagen- und Parameterdatei](#export-the-template-for-the-resource-group) aus der Ressourcengruppe für die jüngste Bereitstellung.  Öffnen Sie die Datei *parameters.json*.  Wenn Sie den Cluster mithilfe der [Beispielvorlage][template] aus diesem Tutorial bereitgestellt haben, enthält der Cluster drei Knotentypen und drei Parameter, die die Anzahl der Knoten für jeden Knotentyp festlegen: *nt0InstanceCount*, *nt1InstanceCount* und *nt2InstanceCount*.  Der Parameter *nt1InstanceCount* legt beispielsweise die Anzahl der Instanzen für den zweiten Knotentyp und die Anzahl der VMs in der zugeordneten VM-Skalierungsgruppe fest.

Indem Sie also den Wert von *nt1InstanceCount* aktualisieren, ändern Sie die Anzahl der Knoten im zweiten Knotentyp.  Denken Sie daran, dass Sie einen Knotentyp nicht auf mehr als 100 Knoten horizontal hochskalieren können.  Andere (nicht primäre) Knotentypen mit Ausführung von zustandsbehafteten Produktionsworkloads sollten immer über mindestens fünf Knoten verfügen. Andere (nicht primäre) Knotentypen mit Ausführung von zustandslosen Produktionsworkloads sollten immer über mindestens zwei Knoten verfügen.

Wenn Sie einen Knotentyp der [Zuverlässigkeitsstufe][durability] „Bronze“ horizontal herunterskalieren (Knoten von ihm entfernen), müssen Sie [den Zustand dieser Knoten manuell entfernen](service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  Für die Dauerhaftigkeitsstufen „Silber“ und „Gold“ werden diese Schritte automatisch von der Plattform ausgeführt.

### <a name="deploy-the-updated-template"></a>Bereitstellen der aktualisierten Vorlage
Speichern Sie alle Änderungen in den Dateien *template.json* und *parameters.json*.  Führen Sie den folgenden Befehl aus, um die aktualisierte Vorlage bereitzustellen:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Oder führen Sie den folgenden Azure-CLI-Befehl aus:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Hinzufügen eines Knotentyps zum Cluster

Jeder Knotentyp, der in einem Service Fabric-Cluster definiert ist, der in Azure ausgeführt wird, wird als [separate VM-Skalierungsgruppe](service-fabric-cluster-nodetypes.md) eingerichtet. Jeder Knotentyp kann dann separat verwaltet werden. Sie können jeden Knotentyp einzeln zentral hoch- oder herunterskalieren, bei jedem Typ unterschiedliche Portgruppen öffnen und verschiedene Kapazitätsmetriken verwenden. Sie können die Betriebssystem-SKU, die auf jedem Clusterknoten ausgeführt wird, auch unabhängig voneinander ändern. Beachten Sie aber, dass Sie im Beispielcluster Windows und Linux nicht gemischt ausführen können. Ein/e einzelne/r Knotentyp/-Skalierungsgruppe kann nicht mehr als 100 Knoten enthalten.  Sie können einen Cluster horizontal auf mehr als 100 Knoten skalieren, indem Sie zusätzliche Knotentypen/Skalierungsgruppen hinzufügen. Sie können die Skalierung für den Cluster jederzeit durchführen – auch bei Ausführung von Workloads im Cluster.

### <a name="update-the-template"></a>Aktualisieren der Vorlage

[Exportieren Sie eine Vorlagen- und Parameterdatei](#export-the-template-for-the-resource-group) aus der Ressourcengruppe für die jüngste Bereitstellung.  Öffnen Sie die Datei *parameters.json*.  Wenn Sie den Cluster mithilfe der [Beispielvorlage][template] aus diesem Tutorial bereitgestellt haben, enthält der Cluster drei Knotentypen.  In diesem Abschnitt fügen Sie einen vierten Knotentyp hinzu, indem Sie eine Resource Manager-Vorlage aktualisieren und bereitstellen. 

Zusätzlich zu dem neuen Knotentyp fügen Sie außerdem die zugeordnete VM-Skalierungsgruppe (die in einem separaten Subnetz des virtuellen Netzwerks ausgeführt wird) und die Netzwerksicherheitsgruppe hinzu.  Sie können auch neue oder vorhandene öffentliche IP-Adressen und Azure Load Balancer-Ressourcen für die neue Skalierungsgruppe hinzuzufügen.  Der neue Knotentyp verfügt über eine [Dauerhaftigkeitsstufe][durability] von „Silber“ und die Größe „Standard_D2_V2“.

Fügen Sie in der Datei *template.json* die folgenden neuen Parameter hinzu:
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

Fügen Sie in der Datei *template.json* die folgenden neuen Variablen hinzu:
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

Fügen Sie in der Datei *template.json* der virtuellen Netzwerkressource ein neues Subnetz hinzu:
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

Fügen Sie in der Datei *template.json* eine neue öffentliche IP-Adresse und Lastenausgleichsressourcen hinzu:
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
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
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

Fügen Sie in der Datei *template.json* eine neue Netzwerksicherheitsgruppe und VM-Skalierungsgruppenressourcen hinzu.  Die „NodeTypeRef“-Eigenschaft innerhalb der Service Fabric-Erweiterungseigenschaften der VM-Skalierungsgruppe ordnet den angegebenen Knotentyp der Skalierungsgruppe zu.

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabSMB",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "445",
                    "direction": "Inbound",
                    "priority": 3950,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow SMB traffic within the net, used by fabric to move packages around"
                }
            },
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
                        "properties": {
                            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                            "type": "MicrosoftMonitoringAgent",
                            "typeHandlerVersion": "1.0",
                            "autoUpgradeMinorVersion": true,
                            "settings": {
                                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                            },
                            "protectedSettings": {
                                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                            }
                        }
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "ServiceFabricNode",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                            },
                            "publisher": "Microsoft.Azure.ServiceFabric",
                            "settings": {
                                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                "storageAccountEndPoint": "https://core.windows.net/"
                            },
                            "publisher": "Microsoft.Azure.Diagnostics",
                            "settings": {
                                "WadCfg": {
                                    "DiagnosticMonitorConfiguration": {
                                        "overallQuotaInMB": "50000",
                                        "EtwProviders": {
                                            "EtwEventSourceProviderConfiguration": [
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Actors",
                                                    "scheduledTransferKeywordFilter": "1",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableActorEventTable"
                                                    }
                                                },
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Services",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                    }
                                                }
                                            ],
                                            "EtwManifestProviderConfiguration": [
                                                {
                                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                    "scheduledTransferLogLevelFilter": "Information",
                                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricSystemEventTable"
                                                    }
                                                }
                                            ]
                                        }
                                    }
                                },
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
                        "properties": {
                            "publisher": "Microsoft.Azure.Security",
                            "type": "IaaSAntimalware",
                            "typeHandlerVersion": "1.5",
                            "settings": {
                                "AntimalwareEnabled": "true",
                                "Exclusions": {
                                    "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                                    "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
                                },
                                "RealtimeProtectionEnabled": "true",
                                "ScheduledScanSettings": {
                                    "isEnabled": "true",
                                    "scanType": "Quick",
                                    "day": "7",
                                    "time": "120"
                                }
                            },
                            "protectedSettings": null
                        }
                    }
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
                                        }
                                    }
                                }
                            ],
                            "primary": true
                        }
                    }
                ]
            },
            "osProfile": {
                "adminPassword": "[parameters('adminPassword')]",
                "adminUsername": "[parameters('adminUsername')]",
                "computernamePrefix": "[variables('vmNodeType3Name')]",
                "secrets": [
                    {
                        "sourceVault": {
                            "id": "[parameters('sourceVaultValue')]"
                        },
                        "vaultCertificates": [
                            {
                                "certificateStore": "[parameters('certificateStoreValue')]",
                                "certificateUrl": "[parameters('certificateUrlValue')]"
                            }
                        ]
                    }
                ]
            },
            "storageProfile": {
                "imageReference": {
                    "publisher": "[parameters('vmImagePublisher')]",
                    "offer": "[parameters('vmImageOffer')]",
                    "sku": "[parameters('vmImageSku')]",
                    "version": "[parameters('vmImageVersion')]"
                },
                "osDisk": {
                    "caching": "ReadOnly",
                    "createOption": "FromImage",
                    "managedDisk": {
                        "storageAccountType": "[parameters('storageAccountType')]"
                    }
                }
            }
        }
    },
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

Aktualisieren Sie in der Datei *template.json* die Clusterressource, und fügen Sie einen neuen Knotentyp hinzu:
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

Fügen Sie in der Datei *parameters.json* die folgenden neuen Parameter und Werte hinzu:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>Bereitstellen der aktualisierten Vorlage
Speichern Sie alle Änderungen in den Dateien *template.json* und *parameters.json*.  Führen Sie den folgenden Befehl aus, um die aktualisierte Vorlage bereitzustellen:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Oder führen Sie den folgenden Azure-CLI-Befehl aus:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Entfernen eines Knotentyps aus dem Cluster
Nachdem Sie einen Service Fabric-Cluster erstellt haben, können Sie einen Cluster horizontal skalieren, indem Sie einen Knotentyp (VM-Skalierungsgruppe) und alle dazugehörigen Knoten entfernen. Sie können die Skalierung für den Cluster jederzeit durchführen – auch bei Ausführung von Workloads im Cluster. Wenn der Cluster skaliert wird, werden Ihre Anwendungen ebenfalls automatisch skaliert.

> [!WARNING]
> Es wird nicht empfohlen, regelmäßig „Remove-AzServiceFabricNodeType“ zu verwenden, um einen Knotentyp aus einem Produktionscluster zu entfernen. Es handelt sich um einen sehr gefährlichen Befehl, da er die VM-Skalierungsgruppenressource hinter dem Knotentyp löscht. 

Führen Sie das Cmdlet [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) aus, um den Knotentyp zu entfernen.  Der Knotentyp muss die [Dauerhaftigkeitsstufe][durability] „Silber“ oder „Gold“ haben. Das Cmdlet löscht die dem Knotentyp zugeordnete Skalierungsgruppe, was einige Zeit in Anspruch nimmt.  Führen Sie dann das Cmdlet [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) auf jedem der zu entfernenden Knoten aus, wodurch der Knotenzustand gelöscht und die Knoten aus dem Cluster entfernt werden. Wenn auf den Knoten Dienste vorhanden sind, werden die Dienste zuerst auf einen anderen Knoten verschoben. Wenn der Cluster-Manager keinen Knoten für das Replikat bzw. den Dienst finden kann, wird der Vorgang verzögert/blockiert.

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>Erhöhen von Knotenressourcen 
Nach der Erstellung eines Service Fabric-Clusters können Sie einen Clusterknotentyp vertikal skalieren (Ressourcen der Knoten ändern) oder das Betriebssystem der Knotentyp-VMs aktualisieren, indem Sie den ursprünglichen Knotentyp durch einen neuen Knotentyp (mit aktualisierter VM-SKU oder Betriebssystemimage) ersetzen. Weitere Informationen finden Sie unter [Hochskalieren eines Azure Service Fabric-Knotentyps](service-fabric-scale-up-primary-node-type.md).

> [!IMPORTANT]
> Versuchen Sie niemals, eine direkte Änderung der VM-SKU oder des Betriebssystemimages durchzuführen, da dies ein gefährlicher und nicht unterstützter Vorgang ist.

Sollte dies nicht möglich sein, können Sie einen neuen Cluster erstellen und eine [Wiederherstellung des Anwendungszustands](service-fabric-reliable-services-backup-restore.md) (falls zutreffend) vom alten Cluster durchführen. Sie müssen keine Systemdienstzustände wiederherstellen, denn diese werden neu erstellt, wenn Sie Ihre Anwendungen im neuen Cluster bereitstellen. Wenn Sie in Ihrem Cluster ausschließlich zustandslose Anwendungen ausführen, stellen Sie lediglich Ihre Anwendungen im neuen Cluster bereit und müssen nichts wiederherstellen.

### <a name="update-the-template"></a>Aktualisieren der Vorlage

[Exportieren Sie eine Vorlagen- und Parameterdatei](#export-the-template-for-the-resource-group) aus der Ressourcengruppe für die jüngste Bereitstellung.  Öffnen Sie die Datei *parameters.json*.  Wenn Sie den Cluster mithilfe der [Beispielvorlage][template] aus diesem Tutorial bereitgestellt haben, enthält der Cluster drei Knotentypen.  

Die Größe der VMs im zweiten Knotentyp wird im Parameter *vmNodeType1Size* festgelegt.  Ändern Sie den Wert des Parameters *vmNodeType1Size* von „Standard_D2_V2“ in [Standard_D3_V2](../virtual-machines/dv2-dsv2-series.md), wodurch die Ressourcen jeder VM-Instanz verdoppelt werden.

Die VM-SKU für alle drei Knotentypen wird im Parameter *vmImageSku* festgelegt.  Auch hier sollten Sie mit Vorsicht an das Ändern der VM-SKU eines Knotentyps herangehen, und es wird für den primären Knotentyp auch nicht empfohlen.

### <a name="deploy-the-updated-template"></a>Bereitstellen der aktualisierten Vorlage
Speichern Sie alle Änderungen in den Dateien *template.json* und *parameters.json*.  Führen Sie den folgenden Befehl aus, um die aktualisierte Vorlage bereitzustellen:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Oder führen Sie den folgenden Azure-CLI-Befehl aus:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Hinzufügen und Entfernen von Knoten (Auf- und Abskalieren)
> * Hinzufügen und Entfernen von Knotentypen (Auf- und Abskalieren)
> * Erhöhen von Knotenressourcen (Hochskalieren)

Fahren Sie mit dem folgenden Tutorial fort, um zu erfahren, wie Sie die Runtime eines Clusters aktualisieren:
> [!div class="nextstepaction"]
> [Aktualisieren der Runtime eines Clusters](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
