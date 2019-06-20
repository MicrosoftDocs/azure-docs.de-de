---
title: Informationen zu Vorlagen für VM-Skalierungsgruppen | Microsoft-Dokumentation
description: Informationen zum Erstellen einer Vorlage für eine grundlegende Skalierungsgruppe für VM-Skalierungsgruppen
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
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
ms.openlocfilehash: 8b6a6b78dc74572b22d397b5536efa1394401bbc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64868922"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Informationen zu Vorlagen für VM-Skalierungsgruppen
[Azure Resource Manager-Vorlagen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) sind eine hervorragende Möglichkeit, Gruppen aufeinander bezogener Ressourcen bereitzustellen. In dieser Reihe von Tutorials wird gezeigt, wie Sie eine Vorlage für eine grundlegende Skalierungsgruppe erstellen und für verschiedene Szenarien anpassen. Alle Beispiele stammen aus diesem [GitHub-Repository](https://github.com/gatneil/mvss).

Hier wird eine einfache Vorlage verwendet. Umfassendere Beispiele für Skalierungsgruppenvorlagen finden Sie, indem Sie im [GitHub-Repository mit Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates) nach Ordnern suchen, die die Zeichenfolge `vmss` enthalten.

Wenn Sie bereits mit dem Erstellen von Vorlagen vertraut sind, können Sie gleich mit dem Abschnitt „Nächste Schritte“ fortfahren, um zu erfahren, wie diese Vorlage geändert wird.

## <a name="define-schema-and-contentversion"></a>Definieren von $schema und contentVersion
Definieren Sie zuerst `$schema` und `contentVersion` in der Vorlage. Das Element `$schema` definiert die Version der Vorlagensprache und wird für die Visual Studio-Syntaxhervorhebung und ähnliche Validierungsfunktionen verwendet. Das Element `contentVersion` wird nicht von Azure verwendet. Stattdessen können Sie damit die Vorlagenversion verfolgen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

## <a name="define-parameters"></a>Definieren von Parametern
Definieren Sie als Nächstes die beiden Parameter `adminUsername` und `adminPassword`. Parameter sind Werte, die Sie zum Zeitpunkt der Bereitstellung angeben. Der Parameter `adminUsername` weist den Typ `string` auf, aber da `adminPassword` ein Geheimnis ist, erhält er den Typ `securestring`. Später werden diese Parameter in die Skalierungsgruppenkonfiguration übergeben.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>Definieren von Variablen
Mit Resource Manager-Vorlagen können Sie außerdem Variablen für die spätere Verwendung in der Vorlage definieren. In diesem Beispiel werden keine Variablen verwendet, deshalb ist das JSON-Objekt leer.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definieren von Ressourcen
Als Nächstes wird der Ressourcenabschnitt der Vorlage bearbeitet. Hier definieren Sie, was bereitgestellt werden soll. Im Gegensatz zu `parameters` und `variables` (die JSON-Objekte sind), ist `resources` eine JSON-Liste von JSON-Objekten.

```json
   "resources": [
```

Alle Ressourcen erfordern die Eigenschaften `type`, `name`, `apiVersion` und `location`. Die erste Ressource dieses Beispiels hat den Typ [Microsoft.Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks), den Namen `myVnet` und die API-Version `2018-11-01`. (Die neueste API-Version für einen Ressourcentyp finden Sie in der [Azure Resource Manager-Vorlagenreferenz](/azure/templates/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2018-11-01",
```

## <a name="specify-location"></a>Angeben des Speicherorts
Zum Angeben des Speicherorts für das virtuelle Netzwerk verwenden wir eine [Resource Manager-Vorlagenfunktion](../azure-resource-manager/resource-group-template-functions.md). Diese Funktion muss wie folgt in Anführungszeichen und eckige Klammern eingeschlossen werden: `"[<template-function>]"`. Verwenden Sie in diesem Fall die Funktion `resourceGroup`. Sie nimmt keine Argumente an und gibt ein JSON-Objekt mit Metadaten über die Ressourcengruppe zurück, in der diese Bereitstellung bereitgestellt wird. Die Ressourcengruppe wird zum Zeitpunkt der Bereitstellung vom Benutzer festgelegt. Dieser Wert wird anschließend mit `.location` in dieses JSON-Objekt indiziert, um den Speicherort aus dem JSON-Objekt abzurufen.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Angeben von Eigenschaften des virtuellen Netzwerks
Jede Resource Manager-Ressource hat einen eigenen `properties`-Abschnitt für Konfigurationen, die für die Ressource spezifisch sind. Geben Sie in diesem Fall an, dass das virtuelle Netzwerk ein Subnetz umfassen soll, das den privaten IP-Adressbereich `10.0.0.0/16` verwendet. Eine Skalierungsgruppe ist immer in einem Subnetz enthalten. Sie kann sich nicht auf Subnetze erstrecken.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>Hinzufügen der dependsOn-Liste
Zusätzlich zu den erforderlichen Eigenschaften `type`, `name`, `apiVersion` und `location` kann jede Ressource über eine optionale `dependsOn`-Liste von Zeichenfolgen verfügen. Diese Liste gibt an, welche anderen Ressourcen aus dieser Bereitstellung beendet werden müssen, bevor diese Ressource bereitgestellt wird.

In diesem Fall gibt es nur ein Element in der Liste: das virtuelle Netzwerk aus dem vorherigen Beispiel. Sie geben diese Abhängigkeit an, da das Netzwerk vorhanden sein muss, bevor die Skalierungsgruppe VMs erstellen kann. Auf diese Weise kann die Skalierungsgruppe an diese VMs private IP-Adressen aus dem IP-Adressbereich vergeben, der zuvor in den Netzwerkeigenschaften angegeben wurde. Das Format der Zeichenfolgen in der dependsOn-Liste ist `<type>/<name>`. Verwenden Sie die gleichen Werte für `type` und `name` wie zuvor in der Definition der Ressourcen des virtuellen Netzwerks.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2019-03-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Angeben von Eigenschaften der Skalierungsgruppe
Skalierungsgruppen verfügen über viele Eigenschaften zum Anpassen der virtuellen Computer in der Skalierungsgruppe. Eine vollständige Liste dieser Eigenschaften finden Sie in der [Vorlagenreferenz](/azure/templates/microsoft.compute/virtualmachinescalesets). In diesem Tutorial werden nur einige häufig verwendete Eigenschaften festgelegt.
### <a name="supply-vm-size-and-capacity"></a>Angeben der Größe und der Kapazität der virtuellen Computer
Für die Skalierungsgruppe muss festgelegt werden, welche Größe der virtuelle Computer haben soll („SKU-Name“) und wie viele solcher virtueller Computer erstellt werden sollen („SKU-Kapazität“). Welche Größen für virtuelle Computer verfügbar sind, erfahren Sie unter [Größen für virtuelle Windows-Computer](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Auswählen des Updatetyps
Für die Skalierungsgruppe muss auch festgelegt werden, wie mit Updates für die Skalierungsgruppe umgegangen werden soll. Zurzeit gibt es die drei Optionen: `Manual`, `Rolling` und `Automatic`. Weitere Informationen zu den Unterschieden zwischen den beiden Optionen finden Sie in der Dokumentation zum [Upgraden einer VM-Skalierungsgruppe](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Auswählen des Betriebssystems der virtuellen Computer
Für die Skalierungsgruppe muss festgelegt werden, welches Betriebssystem die virtuellen Computer haben sollen. Erstellen Sie hier die VMs mit einem vollständig gepatchten Ubuntu 16.04-LTS-Image.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>Angeben von computerNamePrefix
Die Skalierungsgruppe stellt mehrere virtuelle Computer bereit. Geben Sie anstelle der Namen der einzelnen VMs `computerNamePrefix` an. Die Skalierungsgruppe fügt für jeden virtuellen Computer einen Index an dieses Präfix an, sodass die Namen der virtuellen Computer das Format `<computerNamePrefix>_<auto-generated-index>` haben.

Verwenden Sie im folgenden Codeausschnitt die bereits erwähnten Parameter, um den Administratorbenutzernamen und das Kennwort für alle VMs in der Skalierungsgruppe festzulegen. Bei diesem Vorgang wird die Vorlagenfunktion `parameters` verwendet. Diese Funktion akzeptiert eine Zeichenfolge, die angibt, auf welchen Parameter verwiesen werden soll, und gibt den Wert für diesen Parameter aus.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Angeben der Netzwerkkonfiguration für die virtuellen Computer
Geben Sie abschließend die Netzwerkkonfiguration für die VMs in der Skalierungsgruppe an. In diesem Fall müssen Sie nur die ID des zuvor erstellten Subnetzes angeben. Dadurch fügt die Skalierungsgruppe die Netzwerkschnittstellen in dieses Subnetz ein.

Sie können die ID des virtuellen Netzwerks, das das Subnetz enthält, mithilfe der Vorlagenfunktion `resourceId` abrufen. Diese Funktion nimmt den Typ und den Namen einer Ressource an und gibt den vollqualifizierten Bezeichner dieser Ressource zurück. Diese ID hat das Format: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

Allerdings ist der Bezeichner des virtuellen Netzwerks nicht ausreichend. Geben Sie das genaue Subnetz an, in dem sich die VMs der Skalierungsgruppe befinden sollen. Verketten Sie dazu `/subnets/mySubnet` mit der ID des virtuellen Netzwerks. Das Ergebnis ist die vollqualifizierte ID des Subnetzes. Führen Sie diese Verkettung mit der Funktion `concat` durch, die eine Reihe von Zeichenfolgen annimmt und deren Verkettung zurückgibt.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
