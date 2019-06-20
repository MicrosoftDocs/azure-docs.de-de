---
title: Verwalten von IoT Central über Azure PowerShell | Microsoft-Dokumentation
description: Verwalten von IoT Central über Azure PowerShell
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 086c7d303fd199090de3be77b2456c4ebcd053a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66726936"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Verwalten von IoT Central über Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Statt IoT Central-Anwendungen über die IoT Central-Seite [Anwendungs-Manager](https://aka.ms/iotcentral) zu erstellen und zu verwalten, können Sie [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) verwenden, um Ihre Anwendungen zu verwalten.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie Azure PowerShell auf Ihrem lokalen Computer ausführen möchten, lesen Sie [Installieren des Azure PowerShell-Moduls](https://docs.microsoft.com/powershell/azure/install-az-ps). Wenn Sie Azure PowerShell lokal ausführen, verwenden Sie das Cmdlet **Connect-AzAccount**, um sich bei Azure anzumelden, bevor Sie die Cmdlets verwenden, die in diesem Artikel aufgeführt sind.

## <a name="install-the-iot-central-module"></a>Installieren des IoT Central-Moduls

Führen Sie den folgenden Befehl aus, um zu überprüfen, ob das [IoT Central-Modul](https://docs.microsoft.com/powershell/module/az.iotcentral/) in Ihre PowerShell-Umgebung installiert ist:

```powershell
Get-InstalledModule -name Az.I*
```

Wenn die Liste der installierten Module den Eintrag **Az.IotCentral** nicht enthält, führen Sie den folgenden Befehl aus:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Erstellen einer Anwendung

Verwenden Sie das Cmdlet [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp), um eine IoT Central-Anwendung in Ihrem Azure-Abonnement zu erstellen. Beispiel:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Das Skript erstellt zuerst für die Anwendung eine Ressourcengruppe in der Region „East US“ (USA, Osten). In der folgenden Tabelle sind die Parameter beschrieben, die für den **New-AzIotCentralApp**-Befehl verwendet werden:

|Parameter         |Beschreibung |
|------------------|------------|
|ResourceGroupName |Die Ressourcengruppe, die die Anwendung enthält. Diese Ressourcengruppe muss bereits in Ihrem Abonnement vorhanden sein. |
|Location |Standardmäßig wird in diesem Cmdlet der Standort aus der Ressourcengruppe verwendet. Derzeit können Sie eine IoT Central-Anwendung in der Region **USA, Osten**, **USA, Westen**, **Europa, Norden** oder **Europa, Westen** erstellen. |
|NAME              |Der Name der Anwendung im Azure-Portal. |
|Unterdomäne         |Die Unterdomäne in der URL der Anwendung. Im Beispiel lautet die Anwendungs-URL https://mysubdomain.azureiotcentral.com. |
|Sku               |Zurzeit ist **S1** (Standard-Tarif) der einzige Wert. Weitere Informationen finden Sie unter [Azure IoT Central – Preise](https://azure.microsoft.com/pricing/details/iot-central/). |
|Vorlage          | Die zu verwendende Anwendungsvorlage. Weitere Informationen finden Sie in der Tabelle unten: |
|DisplayName       |Der Name der Anwendung, wie er in der Benutzeroberfläche angezeigt wird. |

**Anwendungsvorlagen**

|Vorlagenname  |BESCHREIBUNG |
|---------------|------------|
|iotc-default@1.0.0 |Erstellt eine leere Anwendung, die Sie mit Ihren eigenen Gerätevorlagen und Geräten füllen können. |
|iotc-demo@1.0.0    |Erstellt eine Anwendung mit einer Gerätevorlage, die bereits für einen gekühlten Verkaufsautomaten erstellt wurde. Verwenden Sie diese Vorlage, um mit der Erkundung von Azure IoT Central zu beginnen. |
|iotc-devkit-sample@1.0.0 |Erstellt eine Anwendung mit Gerätevorlagen, an die Sie ein MXChip- oder Raspberry Pi-Gerät anschließen können. Verwenden Sie diese Vorlage, wenn Sie als Geräteentwickler mit einem dieser Geräte experimentieren. |

## <a name="view-your-iot-central-applications"></a>Anzeigen Ihrer IoT Central-Anwendungen

Verwenden Sie das Cmdlet [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp), um Ihre IoT Central-Anwendungen aufzulisten und die Metadaten anzuzeigen.

## <a name="modify-an-application"></a>Ändern einer Anwendung

Verwenden Sie das Cmdlet [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp), um die Metadaten einer IoT Central-Anwendung zu aktualisieren. Den Anzeigenamen Ihrer Anwendung können Sie beispielsweise wie folgt ändern:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Entfernen einer Anwendung

Verwenden Sie das Cmdlet [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp), um eine IoT Central-Anwendung zu löschen. Beispiel:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie Azure IoT Central-Anwendungen über Azure PowerShell verwalten, wird der folgende nächste Schritt empfohlen:

> [!div class="nextstepaction"]
> [Verwalten Ihrer Anwendung](howto-administer.md)
