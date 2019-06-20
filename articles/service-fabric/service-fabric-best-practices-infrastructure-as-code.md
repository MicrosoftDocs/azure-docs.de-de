---
title: Bewährte Methoden für Azure Service Fabric-Infrastructure-as-Code | Microsoft-Dokumentation
description: Bewährte Methoden für die Verwaltung von Service Fabric als Infrastructure-as-Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 2dfe1493c6611fb69a417895aaa1028ad5881b9c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237431"
---
# <a name="infrastructure-as-code"></a>Infrastructure-as-Code

Erstellen Sie in einem Produktionsszenario Azure Service Fabric-Cluster mithilfe von Resource Manager-Vorlagen. Resource Manager-Vorlagen bieten bessere Steuerungsmöglichkeiten für die Ressourceneigenschaften und stellen sicher, dass das Ressourcenmodell konsistent ist.

Resource Manager-Beispielvorlagen für Windows und Linux stehen in den [Azure-Beispielen auf GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates) zur Verfügung. Diese Vorlagen können als Ausgangspunkt für Ihre Clustervorlage verwendet werden. Laden Sie `azuredeploy.json` und `azuredeploy.parameters.json` herunter, und bearbeiten Sie die Dateien entsprechend Ihren Anforderungen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Verwenden Sie die folgenden Azure CLI-Befehle, um die im vorhergehenden Schritt heruntergeladenen Vorlagen `azuredeploy.json` und `azuredeploy.parameters.json` bereitzustellen:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Erstellen einer Ressource mit PowerShell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Service Fabric-Ressourcen

Sie können Anwendungen und Dienste mithilfe von Azure Resource Manager in Ihrem Service Fabric-Cluster bereitstellen. Ausführliche Informationen finden Sie unter [Verwalten von Anwendungen und Diensten als Azure Resource Manager-Ressourcen](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource). Der folgende Codeausschnitt zeigt bewährte Service Fabric-spezifische Anwendungsressourcen, die Sie in Ihre Resource Manager-Vorlagenressourcen einschließen können.

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Um Ihre Anwendung mit Azure Resource Manager bereitzustellen, müssen Sie zunächst ein [Service Fabric-Anwendungspaket (SFPKG) erstellen](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg). Das folgende Python-Skript ist ein Beispiel für die Erstellung einer SFPKG-Datei:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Konfiguration des automatischen Betriebssystemupgrades für virtuelle Azure-Computer 
Das Upgraden Ihrer virtuellen Computer ist ein vom Benutzer initiierter Vorgang, und es empfiehlt sich, [automatische Betriebssystemimageupgrades mit Azure-VM-Skalierungsgruppen](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) für die Hostpatchverwaltung von Azure Service Fabric-Clustern zu verwenden. Die Patchorchestrierungsanwendung (POA) ist eine alternative Lösung für das Hosten außerhalb von Azure. Die POA kann zwar auch in Azure verwendet werden, aufgrund des Zusatzaufwands, der durch das Hosten von POA in Azure entsteht, werden für virtuelle Computer jedoch häufig automatische Betriebssystemupgrades über POA bevorzugt. Im Anschluss finden Sie die Resource Manager-Vorlageneigenschaften für Compute-VM-Skalierungsgruppen, um automatische Betriebssystemupgrades zu aktivieren:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Wenn Sie automatische Betriebssystemupgrades mit Service Fabric verwenden, wird das neue Betriebssystemimage nacheinander für die einzelnen Updatedomänen bereitgestellt. So wird gewährleistet, dass die in Service Fabric ausgeführten Dienste hochverfügbar bleiben. Um automatische Betriebssystemupgrades in Service Fabric nutzen zu können, muss Ihr Cluster zur Verwendung der Dauerhaftigkeitsstufe „Silber“ oder höher konfiguriert sein.

Der folgende Registrierungsschlüssel muss auf „false“ festgelegt sein, um die Initiierung unkoordinierter Updates durch Ihre Windows-Hostcomputer zu verhindern: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

Im Anschluss finden Sie die Resource Manager-Vorlageneigenschaften für Compute-VM-Skalierungsgruppen, um den WindowsUpdate-Registrierungsschlüssel auf „false“ festzulegen:
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Konfiguration von Azure Service Fabric-Clusterupgrades
Im Anschluss finden Sie die Resource Manager-Vorlageneigenschaft für Compute-VM-Skalierungsgruppen zum Aktivieren automatischer Upgrades:
```json
"upgradeMode": "Automatic",
```
Wenn Sie Ihren Cluster manuell upgraden möchten, laden Sie die cab-/deb-Distribution auf einen virtuellen Clustercomputer herunter, und rufen Sie anschließend den folgenden PowerShell-Befehl auf:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Nächste Schritte

* Erstellen eines Clusters auf virtuellen Computern oder Computern mit Windows Server: [Erstellen eines Service Fabric-Clusters für Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Erstellen eines Clusters auf virtuellen Computern oder Computern mit Linux: [Erstellen eines Linux-Clusters](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Informieren Sie sich über [Service Fabric-Supportoptionen](service-fabric-support.md).
