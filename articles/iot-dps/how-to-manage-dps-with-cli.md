---
title: Verwalten des IoT Hub Device Provisioning Service über die Azure CLI und die IoT-Erweiterung
description: Hier erfahren Sie, wie Sie den IoT Hub Device Provisioning Service (DPS) über die Azure CLI und die IoT-Erweiterung verwalten können.
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
ms.custom: devx-track-azurecli
services: iot-dps
ms.openlocfilehash: 020b0dbc937885923b26c115f91147437b7a0f9b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964727"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Verwenden von Azure CLI und der IoT-Erweiterung zum Verwalten des IoT Hub Device Provisioning Service

Die [Azure-Befehlszeilenschnittstelle](/cli/azure?view=azure-cli-latest) ist ein plattformübergreifendes Open-Source-Befehlszeilentool zum Verwalten von Azure-Ressourcen wie IoT Edge. Die Azure-Befehlszeilenschnittstelle steht unter Windows, Linux und macOS zur Verfügung. Mit der Azure-Befehlszeilenschnittstelle können Sie Azure IoT Hub-Ressourcen, Instanzen von Device Provisioning Service und verknüpfte Hubs ohne weitere Tools verwalten.

Mit der IoT-Erweiterung wird die Azure-Befehlszeilenschnittstelle um Features wie die Geräteverwaltung und umfassende IoT Edge-Funktionen erweitert.

In diesem Tutorial führen Sie zuerst die Schritte zum Einrichten der Azure-Befehlszeilenschnittstelle und der IoT-Erweiterung aus. Anschließend wird beschrieben, wie Sie CLI-Befehle zum Durchführen von einfachen Vorgängen von Device Provisioning Service durchführen. 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="installation"></a>Installation 

### <a name="install-python"></a>Installieren von Python

[Python 2.7x oder Python 3.x](https://www.python.org/downloads/) ist erforderlich.

### <a name="install-the-azure-cli"></a>Installieren der Azure CLI

Befolgen Sie die [Installationsanleitung](/cli/azure/install-azure-cli?view=azure-cli-latest), um die Azure-Befehlszeilenschnittstelle in Ihrer Umgebung einzurichten. Ihre Azure CLI-Version muss mindestens 2.0.70 lauten. Verwenden Sie `az –version`, um dies zu überprüfen. Diese Version unterstützt az-Erweiterungsbefehle, und das Framework für Knack-Befehle wird eingeführt. Eine einfache Möglichkeit zur Installation unter Windows besteht darin, den [Microsoft Windows Installer (MSI)](https://aka.ms/InstallAzureCliWindows) herunterzuladen und zu installieren.

### <a name="install-iot-extension"></a>Installieren der IoT-Erweiterung

In der [Infodatei zur IoT-Erweiterung](https://github.com/Azure/azure-iot-cli-extension) werden mehrere Wege zum Installieren der Erweiterung beschrieben. Die einfachste Möglichkeit ist die Ausführung von `az extension add --name azure-iot`. Nach der Installation können Sie `az extension list` verwenden, um die derzeit installierten Erweiterungen zu überprüfen, oder mit `az extension show --name azure-iot` Details zur IoT-Erweiterung anzeigen. Sie können `az extension remove --name azure-iot` verwenden, um die Erweiterung zu entfernen.


## <a name="basic-device-provisioning-service-operations"></a>Grundlegende Vorgänge des Device Provisioning Service

Im Beispiel wird veranschaulicht, wie Sie sich bei Ihrem Azure-Konto anmelden, eine Azure-Ressourcengruppe erstellen (einen Container, der die Ressourcen für eine Azure-Lösung enthält), einen IoT Hub und einen Device Provisioning-Dienst erstellen, die vorhandenen Device Provisioning-Dienste auflisten und einen verknüpften IoT Hub mit CLI-Befehlen erstellen. 

Führen Sie vor dem Fortfahren die oben beschriebenen Installationsschritte aus. Falls Sie noch kein Azure-Konto besitzen, können Sie sofort ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Anmelden am Azure-Konto
  
```azurecli
az login
```

![Der Screenshot zeigt ein Eingabeaufforderungsfenster, in dem der Befehl „az login“ ausgeführt wird.](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Erstellen der Ressourcengruppe „IoTHubBlogDemo“ für „USA, Osten“ (eastus)

```azurecli
az group create -l eastus -n IoTHubBlogDemo
```

![Ressourcengruppe erstellen](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. Erstellen von zwei Device Provisioning-Diensten

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps
```

![Erstellen des Device Provisioning Service](./media/how-to-manage-dps-with-cli/create-dps.jpg)

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps2
```

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Auflisten aller vorhandenen Device Provisioning-Dienste unter dieser Ressourcengruppe

```azurecli
az iot dps list --resource-group IoTHubBlogDemo
```

![Auflisten der Device Provisioning-Dienste](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Erstellen des IoT Hub „blogDemoHub“ unter der neu erstellten Ressourcengruppe

```azurecli
az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo
```

![IoT Hub erstellen](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Verknüpfen eines vorhandenen IoT Hubs mit einem Device Provisioning-Dienst

```azurecli
az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus
```

![Hub verknüpfen](./media/how-to-manage-dps-with-cli/create-hub.jpg)

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Registrieren des Geräts
> * Starten des Geräts
> * Sicherstellen, dass das Gerät registriert ist

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie mehrere Geräte für Hubs mit Lastausgleich bereitgestellt werden. 

> [!div class="nextstepaction"]
> [Bereitstellen von Geräten für IoT Hubs mit Lastausgleich](./tutorial-provision-multiple-hubs.md)