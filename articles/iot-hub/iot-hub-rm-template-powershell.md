---
title: Erstellen einer Azure IoT Hub-Instanz mithilfe einer Vorlage (PowerShell) | Microsoft Docs
description: Erfahren Sie, wie Sie mithilfe einer Azure Resource Manager-Vorlage in Azure PowerShell eine IoT Hub-Instanz erstellen.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: eefa17b699a0599d2d3323409cafddd1d0984c61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61440317"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Erstellen einer IoT Hub-Instanz mithilfe einer Azure Resource Manager-Vorlage (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Erfahren Sie, wie Sie eine Azure Resource Manager-Vorlage verwenden, um eine IoT Hub-Instanz und eine Consumergruppe zu erstellen. Resource Manager-Vorlagen sind JSON-Dateien, mit denen die Ressourcen definiert werden, die Sie für Ihre Lösung bereitstellen müssen. Weitere Informationen zur Entwicklung von Resource Manager-Vorlagen finden Sie in der [Azure Resource Manager-Dokumentation](https://docs.microsoft.com/azure/azure-resource-manager/).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

Die in dieser Schnellstartanleitung verwendete Resource Manager-Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Hier ist eine Kopie der Vorlage angegeben:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Mit der Vorlage wird eine Azure IoT Hub-Instanz mit drei Endpunkten („eventhub“, „cloud-to-device“ und „messaging“) sowie eine Consumergruppe erstellt. Weitere Beispiele für Vorlagen finden Sie unter [Azure Schnellstart-Vorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Das IoT Hub-Vorlagenschema finden Sie [hier](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Es gibt mehrere Methoden zum Bereitstellen einer Vorlage.  In diesem Tutorial verwenden Sie Azure PowerShell.

Wählen Sie zum Ausführen des PowerShell-Skripts die Option **Testen Sie es.** aus, um Azure Cloud Shell zu öffnen. Klicken Sie zum Einfügen des Skripts mit der rechten Maustaste auf die Shell, und wählen Sie „Einfügen“ aus:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

Wie Sie im PowerShell-Skript sehen können, stammt die verwendete Vorlage aus den Azure-Schnellstartvorlagen. Wenn Sie Ihre eigene Vorlage verwenden möchten, müssen Sie zuerst die Vorlagendatei in Cloud Shell hochladen und dann den Dateinamen mithilfe des Schalters `-TemplateFile` angeben.  Ein Beispiel dazu finden Sie unter [Bereitstellen der Vorlage](../azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie eine IoT Hub-Instanz mithilfe einer Azure Resource Manager-Vorlage bereitgestellt haben, möchten Sie vielleicht mehr wissen:

* Informieren Sie sich über die Funktionen der [IoT Hub-Ressourcenanbieter-REST-API][lnk-rest-api].
* Weitere Informationen zu den Funktionen des Azure Resource Manager finden Sie unter [Übersicht über Azure Resource Manager][lnk-azure-rm-overview].
* Informationen zur JSON-Syntax und zu den Eigenschaften, die in Vorlagen verwendet werden sollen, finden Sie unter [Microsoft.Devices resource types](/azure/templates/microsoft.devices/iothub-allversions) (Microsoft.Devices-Ressourcentypen).

Weitere Informationen zum Entwickeln für IoT Hub finden Sie in folgenden Artikeln:

* [Einführung in das C SDK][lnk-c-sdk]
* [Azure IoT SDKs][lnk-sdks]

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [Bereitstellen von KI auf Edge-Geräten mit Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
