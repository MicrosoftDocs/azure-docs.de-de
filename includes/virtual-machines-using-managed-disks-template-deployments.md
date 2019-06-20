---
title: include file
description: include file
services: storage
author: jboeshart
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: jaboes
ms.custom: include file
ms.openlocfilehash: 904bd884bc09c1e2016f55ffc8e1e9f635974ac7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178026"
---
# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Verwenden verwalteter Datenträger mit Resource Manager-Vorlagen

In diesem Dokument erhalten Sie Informationen über die Unterschiede zwischen verwalteten und nicht verwalteten Datenträgern bei der Verwendung von Azure Resource Manager-Vorlagen zur Bereitstellung virtueller Computer. In diesem Beispiel werden Sie bei der Aktualisierung vorhandener Vorlagen auf verwaltete Datenträger unterstützt, die vorher nicht verwaltete Datenträger verwendet haben. Zu Referenzzwecken verwenden wir die Vorlage [101-vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) als Leitfaden. Sie können die Vorlage mit [verwalteten Datenträgern](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) und einer vorherigen Version mit [nicht verwalteten Datenträgern](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) anzeigen lassen, wenn Sie sie direkt miteinander vergleichen möchten.

## <a name="unmanaged-disks-template-formatting"></a>Formatieren von Vorlagen mit nicht verwalteten Datenträgern

Zunächst wird die Bereitstellung nicht verwalteter Datenträger betrachtet. Für das Erstellen nicht verwalteter Datenträger benötigen Sie ein Speicherkonto, um die VHD-Dateien zu speichern. Sie können ein neues Speicherkonto erstellen oder ein bereits vorhandenes verwenden. In diesem Artikel erfahren Sie, wie Sie ein neues Speicherkonto erstellen. Erstellen Sie wie unten dargestellt eine Speicherkontoressource im Ressourcenblock.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

Innerhalb eines Objekts für einen virtuellen Computer fügen Sie eine Abhängigkeit vom Speicherkonto hinzu, um sicherzustellen, dass es vor dem virtuellen Computer erstellt wird. Geben Sie anschließend im Bereich `storageProfile` den vollständigen URI des VHD-Speicherorts an, der auf das Speicherkonto verweist und für den Betriebssystem- und andere Datenträger benötigt wird.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Formatieren von Vorlagen mit verwalteten Datenträgern

In Azure Managed Disks wird der Datenträger zu einer Ressource der obersten Ebene und benötigt kein Speicherkonto mehr, um vom Benutzer erstellt zu werden. Verwaltete Datenträger wurden zuerst in der API-Version `2016-04-30-preview` verfügbar gemacht. Sie sind in allen nachfolgenden API-Versionen verfügbar und jetzt der Standard-Datenträgertyp. Die folgenden Abschnitte bieten eine Einführung in die standardmäßigen Einstellungen und erklären, wie Sie Ihre Datenträger weiter anpassen können.

> [!NOTE]
> Es wird empfohlen, eine neuere API-Version als `2016-04-30-preview` zu verwenden, da zwischen `2016-04-30-preview` und `2017-03-30` grundlegende Änderungen vorgenommen wurden.
>
>

### <a name="default-managed-disk-settings"></a>Standardeinstellungen für verwaltete Datenträger

Sie brauchen zum Erstellen eines virtuellen Computers mit verwalteten Datenträgern keine Speicherkontoressource mehr. Außerdem können Sie die Ressource Ihres virtuellen Computers wie folgt aktualisieren. Beachten Sie insbesondere, dass `apiVersion` `2017-03-30` angibt und `osDisk` und `dataDisks` nicht mehr auf einen spezifischen URI für die VHD-Datei verweisen. Bei der Bereitstellung ohne Angabe von zusätzlichen Eigenschaften verwendet der Datenträger einen auf der Größe des virtuellen Computers basierenden Speichertyp. Bei Verwendung einer Premium-fähigen VM-Größe (Größen, deren Name „s“ enthält, wie z. B. Standard_D2s_v3) verwendet das System beispielsweise Premium_LRS-Speicher. Verwenden Sie die SKU-Einstellung des Datenträgers, um einen Speichertyp anzugeben. Wenn kein Name angegeben wird, wird das Format `<VMName>_OsDisk_1_<randomstring>` für den Betriebssystemdatenträger und `<VMName>_disk<#>_<randomstring>` für jeden anderen Datenträger übernommen. Standardmäßig ist Azure Disk Encryption deaktiviert; Zwischenspeichern für den Betriebssystemdatenträger erfolgt mit Lese-/Schreibzugriff und für Datenträger ohne Zugriff. Wie Sie im unteren Beispiel sehen können, besteht immer noch eine Speicherkontoabhängigkeit. Diese wird allerdings nur für Diagnosespeicher und nicht für Datenträgerspeicher benötigt.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Verwenden einer verwalteten Datenträgerressource auf oberster Ebene

Anstatt die Datenträgerkonfiguration im Objekt des virtuellen Computers anzugeben können Sie eine Datenträgerressource auf oberster Ebene erstellen und sie als Teil des Erstellungsprozesses des virtuellen Computers anfügen. Beispielsweise können Sie eine Datenträgerressource zur Verwendung als Datenträger wie folgt erstellen.

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

Innerhalb des VM-Objekts verweisen Sie auf das anzufügende Datenträgerobjekt. Durch Angabe der Ressourcen-ID des verwalteten Datenträgers, der in der Eigenschaft `managedDisk` erstellt wurde, kann der Datenträger hinzugefügt werden, während der virtuelle Computer erstellt wird. Die `apiVersion` für die VM-Ressource wird auf `2017-03-30` festgelegt. Eine Abhängigkeit von der Datenträgerressource wird hinzugefügt, um sicherzustellen, dass sie vor der Erstellung des virtuellen Computers erstellt wird. 

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Erstellen verwalteter Verfügbarkeitsgruppen mit virtuellen Computern mit verwalteten Datenträgern

Fügen Sie zum Erstellen verwalteter Verfügbarkeitsgruppen mit virtuellen Computern, die verwaltete Datenträger verwenden, das Objekt `sku` zur Ressource der Verfügbarkeitsgruppe hinzu, und legen Sie die Eigenschaft `name` auf `Aligned` fest. Durch diese Eigenschaft wird sichergestellt, dass die Datenträger für jeden virtuellen Computer ausreichend voneinander isoliert sind, um einzelne Fehlerquellen zu vermeiden. Beachten Sie, dass die `apiVersion` für die Verfügbarkeitsgruppenressource auf `2018-10-01` festgelegt ist.

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2018-10-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="standard-ssd-disks"></a>Standard-SSD-Datenträger

Im Folgenden werden die in der Resource Manager-Vorlage zum Erstellen von Standard-SSD-Datenträgern erforderlichen Parameter aufgeführt:

* *apiVersion* für Microsoft.Compute muss auf `2018-04-01` (oder höher) festgelegt werden.
* Geben Sie unter *managedDisk.storageAccountType* den Typ `StandardSSD_LRS` an.

Das folgende Beispiel zeigt den Abschnitt *properties.storageProfile.osDisk* für eine VM, die Standard-SSD-Datenträger verwendet:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Eine vollständige Beispielvorlage zum Erstellen eines Standard-SSD-Datenträgers mit einer Vorlage finden Sie unter [Create a Virtual Machine from a Windows Image with multiple empty Standard SSD Data Disks](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/) (Erstellen eines virtuellen Computers aus einem Windows-Image mit mehreren leeren Standard-SSD-Datenträgern).

### <a name="additional-scenarios-and-customizations"></a>Zusätzliche Szenarios und Anpassungen

Lesen Sie bitte die REST-API-Dokumentation zum Erstellen eines verwalteten Datenträgers [create a managed disk REST API documentation](/rest/api/manageddisks/disks/disks-create-or-update) für vollständige Informationen zu REST-API-Spezifikationen. Hier finden Sie zusätzliche Szenarios sowie standardmäßige und gültige Werte, die durch Bereitstellung von Vorlagen an die API übermittelt werden können. 

## <a name="next-steps"></a>Nächste Schritte

* Vollständige Vorlagen, die verwaltete Datenträger verwenden, finden Sie unter den folgenden Links des Repositorys für Azure-Schnellstartvorlagen.
    * [Windows VM with managed disk (Virtueller Windows-Computer mit verwalteten Datenträgern)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux VM with managed disk (Virtueller Linux-Computer mit verwalteten Datenträgern)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Full list of managed disk templates (Vollständige Liste der Vorlagen mit verwalteten Datenträgern)](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Im Dokument [Azure Managed Disks – Übersicht](../articles/virtual-machines/windows/managed-disks-overview.md) finden Sie weitere Informationen zu verwalteten Datenträgern.
* Lesen Sie die Referenzdokumentation zu den Vorlagen für VM-Ressourcen. Diese finden Sie im Dokument [Microsoft.Compute/virtualMachines template reference](/azure/templates/microsoft.compute/virtualmachines).
* Lesen Sie die Referenzdokumentation zu den Vorlagen für Datenträgerressourcen. Diese finden Sie im Dokument [Microsoft.Compute/disks template reference](/azure/templates/microsoft.compute/disks).
* Informationen zum Verwenden von verwalteten Datenträgern in Azure-VM-Skalierungsgruppen finden Sie im Dokument [Verwenden von Datenträgern mit Skalierungsgruppen](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks).
