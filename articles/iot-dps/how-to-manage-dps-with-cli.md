---
title: Verwenden von Azure CLI und der IoT-Erweiterung zum Verwalten des IoT Hub Device Provisioning Service | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Azure-Befehlszeilenschnittstelle und die IoT-Erweiterung zum Verwalten des IoT Hub Device Provisioning Service verwenden.
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 59d2277bd99fac1e8357c1b0d7336ca7451bf8dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62122862"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Verwenden von Azure CLI und der IoT-Erweiterung zum Verwalten des IoT Hub Device Provisioning Service

Die [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) ist ein plattformübergreifendes Open-Source-Befehlszeilentool zum Verwalten von Azure-Ressourcen wie IoT Edge. Die Azure-Befehlszeilenschnittstelle ist unter Windows, Linux und macOS verfügbar. Mit der Azure-Befehlszeilenschnittstelle können Sie Azure IoT Hub-Ressourcen, Instanzen von Device Provisioning Service und verknüpfte Hubs ohne weitere Tools verwalten.

Mit der IoT-Erweiterung wird die Azure-Befehlszeilenschnittstelle um Features wie die Geräteverwaltung und umfassende IoT Edge-Funktionen erweitert.

In diesem Tutorial führen Sie zuerst die Schritte zum Einrichten der Azure-Befehlszeilenschnittstelle und der IoT-Erweiterung aus. Anschließend wird beschrieben, wie Sie CLI-Befehle zum Durchführen von einfachen Vorgängen von Device Provisioning Service durchführen. 

## <a name="installation"></a>Installation 

### <a name="step-1---install-python"></a>Schritt 1: Installieren von Python

[Python 2.7x oder Python 3.x](https://www.python.org/downloads/) ist erforderlich.

### <a name="step-2---install-the-azure-cli"></a>Schritt 2: Installieren der Azure CLI

Befolgen Sie die [Installationsanleitung](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), um die Azure-Befehlszeilenschnittstelle in Ihrer Umgebung einzurichten. Ihre Azure CLI-Version muss mindestens 2.0.24 lauten. Verwenden Sie `az –version`, um dies zu überprüfen. Diese Version unterstützt az-Erweiterungsbefehle, und das Framework für Knack-Befehle wird eingeführt. Eine einfache Möglichkeit zur Installation unter Windows besteht darin, den [Microsoft Windows Installer (MSI)](https://aka.ms/InstallAzureCliWindows) herunterzuladen und zu installieren.

### <a name="step-3---install-iot-extension"></a>Schritt 3: Installieren der IoT-Erweiterung

In der [Infodatei zur IoT-Erweiterung](https://github.com/Azure/azure-iot-cli-extension) werden mehrere Wege zum Installieren der Erweiterung beschrieben. Die einfachste Möglichkeit ist die Ausführung von `az extension add --name azure-cli-iot-ext`. Nach der Installation können Sie `az extension list` verwenden, um die derzeit installierten Erweiterungen zu überprüfen, oder mit `az extension show --name azure-cli-iot-ext` Details zur IoT-Erweiterung anzeigen. Sie können `az extension remove --name azure-cli-iot-ext` verwenden, um die Erweiterung zu entfernen.


## <a name="basic-device-provisioning-service-operations"></a>Grundlegende Vorgänge des Device Provisioning Service
Im Beispiel wird veranschaulicht, wie Sie sich bei Ihrem Azure-Konto anmelden, eine Azure-Ressourcengruppe erstellen (einen Container, der die Ressourcen für eine Azure-Lösung enthält), einen IoT Hub und einen Device Provisioning-Dienst erstellen, die vorhandenen Device Provisioning-Dienste auflisten und einen verknüpften IoT Hub mit CLI-Befehlen erstellen. 

Führen Sie vor dem Fortfahren die oben beschriebenen Installationsschritte aus. Falls Sie noch kein Azure-Konto besitzen, können Sie sofort ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Anmelden am Azure-Konto
  
    az login

![Anmeldung][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Erstellen der Ressourcengruppe „IoTHubBlogDemo“ für „USA, Osten“ (eastus)

    az group create -l eastus -n IoTHubBlogDemo

![Ressourcengruppe erstellen][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Erstellen von zwei Device Provisioning-Diensten

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Erstellen des Device Provisioning Service][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Auflisten aller vorhandenen Device Provisioning-Dienste unter dieser Ressourcengruppe

    az iot dps list --resource-group IoTHubBlogDemo

![Auflisten der Device Provisioning-Dienste][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Erstellen des IoT Hub „blogDemoHub“ unter der neu erstellten Ressourcengruppe

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![IoT Hub erstellen][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Verknüpfen eines vorhandenen IoT Hubs mit einem Device Provisioning-Dienst

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Hub verknüpfen][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Registrieren des Geräts
> * Starten des Geräts
> * Sicherstellen, dass das Gerät registriert ist

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie mehrere Geräte für Hubs mit Lastausgleich bereitgestellt werden. 

> [!div class="nextstepaction"]
> [Bereitstellen von Geräten für IoT Hubs mit Lastausgleich](./tutorial-provision-multiple-hubs.md)
