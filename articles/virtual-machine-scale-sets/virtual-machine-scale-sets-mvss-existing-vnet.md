---
title: Verweisen auf ein vorhandenes virtuelles Netzwerk in einer Vorlage für eine Azure-Skalierungsgruppe | Microsoft-Dokumentation
description: Informationen zum Hinzufügen eines virtuellen Netzwerks zu einer vorhandenen Vorlage für eine Azure-VM-Skalierungsgruppe
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 8b75b9898eb767866c0843594a82570cfb65d122
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64868951"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Hinzufügen eines Verweises auf ein vorhandenes virtuelles Netzwerk in einer Vorlage für eine Azure-Skalierungsgruppe

In diesem Artikel wird gezeigt, wie die [Vorlage für eine grundlegende Skalierungsgruppe](virtual-machine-scale-sets-mvss-start.md) geändert wird, um die Bereitstellung in einem vorhandenen virtuellen Netzwerk durchzuführen, statt ein neues zu erstellen.

## <a name="change-the-template-definition"></a>Ändern der Vorlagendefinition

In einem [vorherigen Artikel](virtual-machine-scale-sets-mvss-start.md) haben wir eine grundlegende Vorlage für eine Skalierungsgruppe erstellt. Wir verwenden nun diese frühere Vorlage und ändern sie, um eine Vorlage zu erstellen, die eine Skalierungsgruppe in einem vorhandenen virtuellen Netzwerk bereitstellt. 

Fügen Sie zunächst einen `subnetId`-Parameter hinzu. Diese Zeichenfolge wird an die Skalierungsgruppenkonfiguration übergeben und ermöglicht der Skalierungsgruppe das Identifizieren des vorab erstellten Subnetzes, in dem virtuelle Computer bereitgestellt werden sollen. Diese Zeichenfolge muss folgendes Format aufweisen: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`.

Beispiel: Zum Bereitstellen der Skalierungsgruppe in einem vorhandenen virtuellen Netzwerk mit dem Namen `myvnet`, dem Subnetz `mysubnet`, der Ressourcengruppe `myrg`, und dem Abonnement `00000000-0000-0000-0000-000000000000`, wäre „subnetId“: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

Löschen Sie als Nächstes die Ressource für das virtuelle Netzwerk aus dem `resources`-Array, da Sie ein vorhandenes virtuelles Netzwerk verwenden und kein neues bereitstellen müssen.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

Das virtuelle Netzwerk ist bereits vorhanden, bevor die Vorlage bereitgestellt wird, daher besteht keine Notwendigkeit, eine dependsOn-Klausel in der Skalierungsgruppe für das virtuelle Netzwerk anzugeben. Löschen Sie die folgenden Zeilen:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Schließlich übergeben Sie den `subnetId`-Parameter, der vom Benutzer festgelegt wird (statt `resourceId`, um die ID des VNET in der gleichen Bereitstellung abzurufen, was von der Vorlage für die grundlegende Skalierungsgruppe ausgeführt wird).

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
