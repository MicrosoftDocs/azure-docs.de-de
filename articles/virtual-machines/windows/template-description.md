---
title: Virtuelle Computer in einer Azure Resource Manager-Vorlage | Microsoft Azure
description: Es wird beschrieben, wie die Ressource des virtuellen Computers in einer Azure Resource Manager-Vorlage definiert wird.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: 228814db76ea14fa8b74c0c5d634e5afd6d54ac6
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483044"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Virtuelle Computer in einer Azure Resource Manager-Vorlage

In diesem Artikel werden die Aspekte einer Azure Resource Manager-Vorlage beschrieben, die für virtuelle Computer gelten. Es wird keine vollständige Vorlage zum Erstellen eines virtuellen Computers beschrieben. Hierfür benötigen Sie Ressourcendefinitionen für Speicherkonten, Netzwerkschnittstellen, öffentliche IP-Adressen und virtuelle Netzwerke. Weitere Informationen dazu, wie diese Ressourcen zusammen definiert werden können, finden Sie unter [Resource Manager-Vorlage – Exemplarische Vorgehensweise](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

Es sind viele [Vorlagen im Katalog](https://azure.microsoft.com/documentation/templates/?term=VM) enthalten, die eine VM-Ressource aufweisen. Hier werden nicht alle Elemente beschrieben, die in eine Vorlage eingebunden werden können.

 

Dieses Beispiel zeigt einen typischen Ressourcenabschnitt einer Vorlage zum Erstellen einer angegebenen Anzahl von VMs:

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop", 
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
        "adminUsername": "[parameters('adminUsername')]", 
        "adminPassword": "[parameters('adminPassword')]" 
      }, 
      "storageProfile": { 
        "imageReference": { 
          "publisher": "MicrosoftWindowsServer", 
          "offer": "WindowsServer", 
          "sku": "2012-R2-Datacenter", 
          "version": "latest" 
        }, 
        "osDisk": { 
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>Dieses Beispiel basiert auf einem Speicherkonto, das zuvor erstellt wurde. Sie können das Speicherkonto erstellen, indem Sie es über die Vorlage bereitstellen. Für das Beispiel werden auch eine Netzwerkschnittstelle sowie die davon abhängigen Ressourcen verwendet, die in der Vorlage definiert werden. Diese Ressourcen sind im Beispiel nicht dargestellt.
>
>

## <a name="api-version"></a>API-Version

Beim Bereitstellen von Ressourcen mit einer Vorlage müssen Sie eine Version der API angeben, die verwendet werden soll. Im Beispiel wird veranschaulicht, wie die Ressource des virtuellen Computers dieses apiVersion-Element verwendet:

```json
"apiVersion": "2016-04-30-preview",
```

Die Version der API, die Sie in Ihrer Vorlage angeben, wirkt sich darauf aus, welche Eigenschaften Sie in der Vorlage definieren können. Im Allgemeinen sollten Sie beim Erstellen von Vorlagen die neueste Version der API wählen. Für vorhandene Vorlagen können Sie entscheiden, ob Sie weiterhin eine frühere Version der API verwenden oder die Vorlage für die aktuelle Version aktualisieren möchten, um die neuen Features zu nutzen.

Verwenden Sie diese Optionen zum Abrufen der aktuellen API-Versionen:

- REST-API: [Auflisten aller Ressourcenanbieter](/rest/api/resources/providers)
- PowerShell: [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)
- Azure CLI – [az provider show](/cli/azure/provider)


## <a name="parameters-and-variables"></a>Parameter und Variablen

[Parameter](../../azure-resource-manager/templates/template-syntax.md) erleichtern Ihnen das Angeben von Werten für die Vorlage, wenn Sie sie ausführen. Dieser Parameterabschnitt wird im Beispiel verwendet:

```json
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Beim Bereitstellen der Beispielvorlage geben Sie Werte für den Namen und das Kennwort des Administratorkontos auf jeder VM und die Anzahl von zu erstellenden VMs an. Sie haben die Möglichkeit, Parameterwerte in einer separaten Datei anzugeben, die mit der Vorlage verwaltet wird, oder Werte nach Aufforderung anzugeben.

[Variablen](../../azure-resource-manager/templates/template-syntax.md) erleichtern Ihnen das Einrichten von Werten in der Vorlage, die darin wiederholt verwendet werden oder die sich im Laufe der Zeit ändern können. Dieser Variablenabschnitt wird im Beispiel verwendet:

```json
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

Beim Bereitstellen der Beispielvorlage werden für den Namen und Bezeichner des zuvor erstellten Speicherkontos Variablenwerte verwendet. Variablen werden auch genutzt, um die Einstellungen für die Diagnoseerweiterung anzugeben. Verwenden Sie die [bewährten Methoden zum Erstellen von Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/template-best-practices.md), um besser entscheiden zu können, wie Sie die Parameter und Variablen in Ihrer Vorlage strukturieren möchten.

## <a name="resource-loops"></a>Ressourcenschleifen

Wenn Sie mehr als einen virtuellen Computer für Ihre Anwendung benötigen, können Sie in einer Vorlage ein copy-Element verwenden. Mit diesem optionalen Element wird die Erstellung der Anzahl von VMs, die Sie als Parameter angegeben haben, als Schleife durchlaufen:

```json
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Achten Sie im Beispiel auch darauf, dass der Schleifenindex verwendet wird, wenn einige Werte für die Ressource angegeben werden. Wenn Sie beispielsweise eine Instanzanzahl von 3 angegeben haben, lauten die Namen der Betriebssystem-Datenträger myOSDisk1, myOSDisk2 und myOSDisk3:

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>In diesem Beispiel werden verwaltete Datenträger für die virtuellen Computer verwendet.
>
>

Beachten Sie, dass die Erstellung einer Schleife für eine Ressource in der Vorlage bedeuten kann, dass Sie die Schleife auch verwenden müssen, wenn Sie andere Ressourcen erstellen oder darauf zugreifen. Für mehrere VMs kann beispielsweise nicht dieselbe Netzwerkschnittstelle verwendet werden. Wenn Ihre Vorlage also eine Schleife zur Erstellung von drei VMs durchläuft, muss auch eine Schleife zum Erstellen von drei Netzwerkschnittstellen durchlaufen werden. Beim Zuweisen einer Netzwerkschnittstelle zu einer VM wird der Schleifenindex für die Identifizierung genutzt:

```json
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Abhängigkeiten

Um richtig funktionieren zu können, sind die meisten Ressourcen von anderen Ressourcen abhängig. Virtuelle Computer müssen einem virtuellen Netzwerk zugeordnet werden, und hierfür wird eine Netzwerkschnittstelle benötigt. Mit dem [dependsOn](../../azure-resource-manager/templates/define-resource-dependency.md)-Element wird sichergestellt, dass die Netzwerkschnittstelle für die Verwendung bereit ist, bevor die VMs erstellt werden:

```json
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Von Resource Manager werden alle Ressourcen, die nicht von der Bereitstellung einer anderen Ressource abhängig sind, parallel bereitgestellt. Gehen Sie beim Festlegen von Abhängigkeiten mit Bedacht vor, da Sie die Bereitstellung ggf. unabsichtlich verlangsamen, wenn Sie unnötige Abhängigkeiten angeben. Abhängigkeiten können über mehrere Ressourcen hinweg verkettet sein. Die Netzwerkschnittstelle hängt beispielsweise von der öffentlichen IP-Adresse und den Ressourcen des virtuellen Netzwerks ab.

Woran ist erkennbar, dass eine Abhängigkeit erforderlich ist? Sehen Sie sich die Werte an, die Sie in der Vorlage festlegen. Wenn ein Element in der Definition der VM-Ressource auf eine andere Ressource zeigt, die in derselben Vorlage bereitgestellt wird, benötigen Sie eine Abhängigkeit. Für den virtuellen Computer im Beispiel wird beispielsweise ein Netzwerkprofil definiert:

```json
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Zum Festlegen dieser Eigenschaft muss die Netzwerkschnittstelle vorhanden sein. Aus diesem Grund benötigen Sie eine Abhängigkeit. Außerdem müssen Sie eine Abhängigkeit festlegen, wenn eine Ressource (untergeordnetes Element) in einer anderen Ressource (übergeordnetes Element) definiert wird. Beispielsweise sind die Diagnoseeinstellungen und benutzerdefinierten Skripterweiterungen jeweils als untergeordnete Ressourcen des virtuellen Computers definiert. Sie können erst erstellt werden, wenn der virtuelle Computer vorhanden ist. Aus diesem Grund sind beide Ressourcen als abhängig vom virtuellen Computer gekennzeichnet.

## <a name="profiles"></a>Profile

Beim Definieren einer VM-Ressource werden mehrere Profilelemente verwendet. Einige sind erforderlich, und einige sind optional. Die Elemente „hardwareProfile“, „osProfile“, „storageProfile“ und „networkProfile“ sind erforderlich, aber „diagnosticsProfile“ ist optional. Mit diesen Profilen werden beispielsweise folgende Einstellungen definiert:
   
- [size](../sizes.md)
- [Name](/azure/architecture/best-practices/resource-naming) und Anmeldeinformationen
- Datenträger- und [Betriebssystemeinstellungen](cli-ps-findimage.md)
- [Netzwerkschnittstelle](/previous-versions/azure/virtual-network/virtual-network-deploy-multinic-classic-ps) 
- Startdiagnose

## <a name="disks-and-images"></a>Datenträger und Images
   
In Azure können VHD-Dateien für [Datenträger oder Images](../managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) stehen. Wenn das Betriebssystem in einer VHD-Datei für einen spezifischen virtuellen Computer spezialisiert wurde, wird dies als Datenträger bezeichnet. Wenn das Betriebssystem in einer VHD-Datei allgemein gehalten ist, um für die Erstellung von vielen virtuellen Computern verwendet zu werden, wird es als Image bezeichnet.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Erstellen neuer virtueller Computer und neuer Datenträger aus einem Plattformimage

Beim Erstellen einer VM müssen Sie entscheiden, welches Betriebssystem verwendet werden soll. Das imageReference-Element wird verwendet, um das Betriebssystem einer neuen VM zu definieren. Das Beispiel zeigt eine Definition für ein Windows Server-Betriebssystem:

```json
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Sie können diese Definition verwenden, wenn Sie ein Linux-Betriebssystem erstellen möchten:

```json
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Konfigurationseinstellungen für den Betriebssystem-Datenträger werden mit dem osDisk-Element zugewiesen. Das Beispiel definiert einen neuen verwalteten Datenträger mit dem Zwischenspeicherungsmodus **ReadWrite** und der Einstellung, dass der Datenträger aus einem [Plattformimage](cli-ps-findimage.md) erstellt wird:

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Erstellen neuer virtueller Computer aus vorhandenen verwalteten Datenträgern

Entfernen Sie zum Erstellen von virtuellen Computern von vorhandenen Datenträgern die Elemente „imageReference“ und „osProfile“, und definieren Sie diese Datenträgereinstellungen:

```json
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Erstellen neuer virtueller Computer aus einem verwalteten Image

Ändern Sie zum Erstellen eines virtuellen Computers aus einem verwalteten Image das imageReference-Element, und definieren Sie diese Datenträgereinstellungen:

```json
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>Anfügen von Datenträgern für Daten

Optional können Sie den VMs Datenträger für Daten hinzufügen. Die [Anzahl von Datenträgern](../sizes.md) richtet sich nach der Größe des von Ihnen genutzten Betriebssystemdatenträgers. Wenn die Größe der VMs auf „Standard_DS1_v2“ festgelegt ist, können ihnen maximal zwei Datenträger für Daten hinzugefügt werden. Im Beispiel wird jeder VM ein verwalteter Datenträger für Daten hinzugefügt:

```json
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>Erweiterungen

[Erweiterungen](../extensions/features-windows.md) sind zwar eine separate Ressource, aber sie sind eng an virtuelle Computer gebunden. Erweiterungen können als untergeordnete Ressource der VM oder als separate Ressource hinzugefügt werden. Im Beispiel ist zu sehen, wie die [Diagnoseerweiterung](../extensions/diagnostics-template.md) den VMs hinzugefügt wird:

```json
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

Für diese Erweiterungsressource werden die storageName-Variable und die Diagnosevariablen zum Angeben von Werten verwendet. Wenn Sie die Daten ändern möchten, die von dieser Erweiterung gesammelt werden, können Sie der Variablen „wadperfcounters“ weitere Leistungsindikatoren hinzufügen. Außerdem können Sie die Diagnosedaten auch in einem anderen Speicherkonto als für die VM-Datenträger ablegen.

Es gibt viele Erweiterungen, die Sie auf einer VM installieren können, aber am nützlichsten ist wahrscheinlich die [Benutzerdefinierte Skripterweiterung](../extensions/custom-script-windows.md). Im Beispiel wird auf jeder VM nach dem ersten Starten ein PowerShell-Skript mit dem Namen „start.ps1“ ausgeführt:

```json
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

Mit dem Skript „start.ps1“ können viele Konfigurationsaufgaben durchgeführt werden. Beispielsweise werden die Datenträger, die den virtuellen Computern im Beispiel hinzugefügt werden, nicht initialisiert. Sie können ein benutzerdefiniertes Skript verwenden, um sie zu initialisieren. Falls Sie mehrere Startaufgaben durchführen müssen, können Sie die Datei „start.ps1“ verwenden, um andere PowerShell-Skripts im Azure-Speicher aufzurufen. Im Beispiel wird PowerShell verwendet, aber Sie können alle Skripterstellungsmethoden nutzen, die unter dem von Ihnen eingesetzten Betriebssystem verfügbar sind.

Sie können den Status der installierten Erweiterungen über die Erweiterungseinstellungen im Portal anzeigen:

![Abrufen des Erweiterungsstatus](./media/template-description/virtual-machines-show-extensions.png)

Sie können die Erweiterungsinformationen auch abrufen, indem Sie den PowerShell-Befehl **Get-AzVMExtension**, den Azure CLI-Befehl **vm extension get** oder die REST-API **Get extension information** (Erweiterungsinformationen abrufen) verwenden.

## <a name="deployments"></a>Bereitstellungen

Beim Bereitstellen einer Vorlage verfolgt Azure die Ressourcen nach, die Sie als Gruppe bereitgestellt haben, und weist dieser bereitgestellten Gruppe automatisch einen Namen zu. Der Name der Bereitstellung entspricht dem Namen der Vorlage.

Wenn Sie den Status der Ressourcen einer Bereitstellung anzeigen möchten, zeigen Sie die Ressourcengruppe im Azure-Portal an:

![Abrufen von Bereitstellungsinformationen](./media/template-description/virtual-machines-deployment-info.png)
    
Es ist kein Problem, dieselbe Vorlage zum Erstellen von Ressourcen oder Aktualisieren von vorhandenen Ressourcen zu nutzen. Wenn Sie Befehle zum Bereitstellen von Vorlagen verwenden, haben Sie die Möglichkeit, den gewünschten [Modus](../../azure-resource-manager/templates/deploy-powershell.md) anzugeben. Der Modus kann entweder auf **Complete** oder **Incremental** festgelegt werden. Inkrementelle Updates sind die Standardeinstellung. Gehen Sie bei der Verwendung des Modus **Complete** mit Bedacht vor, damit Sie nicht versehentlich Ressourcen löschen. Wenn Sie den Modus auf **Complete** festlegen, löscht Resource Manager alle Ressourcen in der Ressourcengruppe, die nicht in der Vorlage enthalten sind.

## <a name="next-steps"></a>Nächste Schritte

- Erstellen Sie Ihre eigene Vorlage: [Erstellen von Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/template-syntax.md).
- Stellen Sie die Vorlagen bereit, die Sie erstellt haben: [Erstellen Sie einen virtuellen Windows-Computer mit einer Resource Manager-Vorlage](ps-template.md).
- Erfahren Sie, wie Sie die erstellten virtuellen Computer verwalten, indem Sie [Erstellen und Verwalten von virtuellen Windows-Computern mit dem Azure PowerShell-Modul](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) durcharbeiten.
- Informationen zur JSON-Syntax und zu den Eigenschaften von Ressourcentypen in Vorlagen finden Sie in der [Azure Resource Manager-Vorlagenreferenz](/azure/templates/).