---
title: Verwalten von IoT Central über Azure CLI | Microsoft-Dokumentation
description: Verwalten von IoT Central über Azure CLI
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/07/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 9e5d842cece316bc9c53e1e8583f40a0f222b91d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66151956"
---
# <a name="manage-iot-central-from-azure-cli"></a>Verwalten von IoT Central über Azure CLI

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Statt IoT Central-Anwendungen über die IoT Central-Seite [Anwendungs-Manager](https://aka.ms/iotcentral) zu erstellen und zu verwalten, können Sie [Azure CLI](/cli/azure/) verwenden, um Ihre Anwendungen zu verwalten.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie Azure CLI auf Ihrem lokalen Computer ausführen möchten, lesen Sie [Installieren der Azure CLI](/cli/azure/install-azure-cli). Wenn Sie die Azure CLI lokal ausführen, verwenden Sie den Befehl **az login**, um sich bei Azure anzumelden, bevor Sie die Befehle verwenden, die in diesem Artikel aufgeführt sind.

## <a name="create-an-application"></a>Erstellen einer Anwendung

Verwenden Sie den Befehl [az iotcentral app create](/cli/azure/iotcentral/app#az-iotcentral-app-create), um eine IoT Central-Anwendung in Ihrem Azure-Abonnement zu erstellen. Beispiel: 

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

Diese Befehle erstellen zuerst für die Anwendung eine Ressourcengruppe in der Region „East US“ (USA, Osten). In der folgenden Tabelle sind die Parameter beschrieben, die für den **az iotcentral app create**-Befehl verwendet werden:

| Parameter         | BESCHREIBUNG |
| ----------------- | ----------- |
| resource-group    | Die Ressourcengruppe, die die Anwendung enthält. Diese Ressourcengruppe muss bereits in Ihrem Abonnement vorhanden sein. |
| location          | Standardmäßig wird in diesem Befehl der Standort aus der Ressourcengruppe verwendet. Derzeit können Sie eine IoT Central-Anwendung in der Region **USA, Osten**, **USA, Westen**, **Europa, Norden** oder **Europa, Westen** erstellen. |
| name              | Der Name der Anwendung im Azure-Portal. |
| subdomain         | Die Unterdomäne in der URL der Anwendung. Im Beispiel lautet die Anwendungs-URL https://mysubdomain.azureiotcentral.com. |
| sku               | Zurzeit ist **S1** (Standard-Tarif) der einzige Wert. Weitere Informationen finden Sie unter [Azure IoT Central – Preise](https://azure.microsoft.com/pricing/details/iot-central/). |
| Vorlage          | Die zu verwendende Anwendungsvorlage. Weitere Informationen finden Sie in der Tabelle unten: |
| display-name      | Der Name der Anwendung, wie er in der Benutzeroberfläche angezeigt wird. |

**Anwendungsvorlagen**

| Vorlagenname            | BESCHREIBUNG |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Erstellt eine leere Anwendung, die Sie mit Ihren eigenen Gerätevorlagen und Geräten füllen können. |
| iotc-demo@1.0.0          | Erstellt eine Anwendung mit einer Gerätevorlage, die bereits für einen gekühlten Verkaufsautomaten erstellt wurde. Verwenden Sie diese Vorlage, um mit der Erkundung von Azure IoT Central zu beginnen. |
| iotc-devkit-sample@1.0.0 | Erstellt eine Anwendung mit Gerätevorlagen, an die Sie ein MXChip- oder Raspberry Pi-Gerät anschließen können. Verwenden Sie diese Vorlage, wenn Sie als Geräteentwickler mit einem dieser Geräte experimentieren. |

## <a name="view-your-applications"></a>Anzeigen Ihrer Anwendungen

Verwenden Sie den Befehl [az iotcentral app list](/cli/azure/iotcentral/app#az-iotcentral-app-list), um Ihre IoT Central-Anwendungen aufzulisten und die Metadaten anzuzeigen.

## <a name="modify-an-application"></a>Ändern einer Anwendung

Verwenden Sie den Befehl [az iotcentral app update](/cli/azure/iotcentral/app#az-iotcentral-app-update), um die Metadaten einer IoT Central-Anwendung zu aktualisieren. Den Anzeigenamen Ihrer Anwendung können Sie beispielsweise wie folgt ändern:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Entfernen einer Anwendung

Verwenden Sie den Befehl [az iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete), um eine IoT Central-Anwendung zu löschen. Beispiel: 

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie Azure IoT Central-Anwendungen über die Azure CLI verwalten, wird der folgende nächste Schritt empfohlen:

> [!div class="nextstepaction"]
> [Verwalten Ihrer Anwendung](howto-administer.md)
