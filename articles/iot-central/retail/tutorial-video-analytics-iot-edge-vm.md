---
title: 'Tutorial: Erstellen einer IoT Edge-Instanz für die Videoanalyse in Azure IoT Central (Linux-VM)'
description: 'In diesem Tutorial wird veranschaulicht, wie Sie auf einem virtuellen Linux-Computer eine IoT Edge-Instanz für die Videoanalyse erstellen, die mit der Anwendungsvorlage „Videoanalyse: Objekt- und Bewegungserkennung“ verwendet werden kann.'
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 10ddbf3dde62380eb79af685ad41b22e4552cea1
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832622"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-linux-vm"></a>Tutorial: Erstellen einer IoT Edge-Instanz für die Videoanalyse (Linux-VM)

Azure IoT Edge ist ein vollständig verwalteter Dienst für lokale Cloud Intelligence. Hierfür wird Folgendes bereitgestellt und ausgeführt:

* Benutzerdefinierte Logik
* Azure-Dienste
* Künstliche Intelligenz

In IoT Edge werden diese Dienste direkt auf plattformübergreifenden IoT-Geräten ausgeführt, damit Sie Ihre IoT-Lösung in der Cloud oder offline sicher und bedarfsgesteuert ausführen können.

In diesem Tutorial wird veranschaulicht, wie Sie ein IoT Edge-Gerät auf einer Azure-VM vorbereiten. Auf der IoT Edge-Instanz werden die Module für die Livevideoanalyse ausgeführt, die von der Azure IoT Central-Anwendungsvorlage „Videoanalyse: Objekt- und Bewegungserkennung“ verwendet werden.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
> * Erstellen einer Azure-VM mit installierter Azure IoT Edge-Runtime
> * Vorbereiten der IoT Edge-Installation auf das Hosten des Moduls für die Livevideoanalyse und Herstellen der Verbindung mit IoT Central

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, sollten Sie das vorgeschaltete Tutorial [Erstellen einer Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“ in Azure IoT Central (YOLO v3)](./tutorial-video-analytics-create-app-yolo-v3.md) bzw. [Erstellen einer Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“ in Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) durcharbeiten.

Darüber hinaus benötigen Sie ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie auf der [Azure-Anmeldeseite](https://aka.ms/createazuresubscription) kostenlos eines erstellen.

## <a name="deploy-azure-iot-edge"></a>Bereitstellen einer Azure IoT Edge-Instanz

Wählen Sie die folgende Schaltfläche aus, um eine Azure-VM mit der aktuellen IoT Edge-Runtime und installierten Modulen für die Livevideoanalyse zu erstellen:

[![Schaltfläche zum Bereitstellen auf Azure für „iotedge-vm-deploy“](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Flive-video-analytics%2Fmaster%2Fref-apps%2Flva-edge-iot-central-gateway%2Fvm_deploy%2FedgeModuleVMDeploy.json)

Verwenden Sie die Informationen in der folgenden Tabelle, um das Formular **Benutzerdefinierte Bereitstellung** auszufüllen:

| Feld | Wert |
| ----- | ----- |
| Subscription | Wählen Sie Ihr Azure-Abonnement. |
| Resource group | *lva-rg*: Die Ressourcengruppe, die Sie im vorherigen Tutorial erstellt haben. |
| Region       | *USA, Osten* |
| Präfix der DNS-Bezeichnung | Wählen Sie ein eindeutiges DNS-Präfix für die VM aus. Es darf nur aus alphabetischen Zeichen bestehen und darf keine Zahlen oder Sonderzeichen enthalten. |
| Administratorbenutzername | *AzureUser* |
| Administratorkennwort | Geben Sie ein Kennwort ein. Notieren Sie sich das Kennwort in der Datei *scratchpad.txt* zur späteren Verwendung. |
| Bereichs-ID | Die **Bereichs-ID**, die Sie sich im vorherigen Tutorial beim Hinzufügen des Gatewaygeräts in der Datei *scratchpad.txt* notiert haben. |
| Geräte-ID | *gateway-001*: Das Gatewaygerät, das Sie im vorherigen Tutorial erstellt haben |
| Geräteschlüssel | Der **Primärschlüssel des Geräts**, den Sie sich im vorherigen Tutorial beim Hinzufügen des Gatewaygeräts in der Datei *scratchpad.txt* notiert haben |
| IoT Central-App-Host | Die **Anwendungs-URL**, die Sie sich im vorherigen Tutorial in der Datei *scratchpad.txt* notiert haben. Beispiel: *traders.azureiotcentral.com*. |
| API-Token für IoT Central-App | Das **API-Token für den Operator**, das Sie sich im vorherigen Tutorial in der Datei *scratchpad.txt* notiert haben |
| Schlüssel für die IoT Central-Gerätebereitstellung | Der **Primärschlüssel für die Gruppe „SAS-IoT-Devices“** , den Sie sich im vorherigen Tutorial in der Datei *scratchpad.txt* notiert haben |
| Größe des virtuellen Computers | *Standard_DS1_v2* |
| Ubuntu-Betriebssystemversion | *18.04-LTS* |
| Standort | *[resourceGroup().location]* |

Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie nach Abschluss der Validierung die Option **Erstellen** aus. Normalerweise dauert es ungefähr drei Minuten, bis die Bereitstellung abgeschlossen ist. Navigieren Sie im Azure-Portal zur Ressourcengruppe **lva-rg**, nachdem die Bereitstellung abgeschlossen ist.

## <a name="ensure-the-iot-edge-runtime-loads-the-modules"></a>Sicherstellen, dass die IoT Edge-Runtime die Module lädt

Navigieren Sie im Azure-Portal zur Ressourcengruppe **lva-rg**, und wählen Sie den virtuellen Computer aus. Wählen Sie anschließend im Abschnitt **Support + Problembehandlung** die Option **Serielle Konsole** aus.

Drücken Sie die **EINGABETASTE**, um eine Eingabeaufforderung vom Typ `login:` zu erhalten. Verwenden Sie *AzureUser* als Benutzernamen und das zugehörige Kennwort, das Sie beim Erstellen der VM ausgewählt haben.

Führen Sie den folgenden Befehl aus, um die Version der IoT Edge-Runtime zu überprüfen. Zum Zeitpunkt der Artikelerstellung lautet die Version 1.0.9:

```bash
sudo iotedge --version
```

Listen Sie Ihre IoT Edge-Module mit dem folgenden Befehl auf:

```bash
sudo iotedge list
```

Für die Bereitstellung wurden die folgenden fünf IoT Edge-Module für die Ausführung konfiguriert:

* LvaEdgeGatewayModule
* edgeAgent
* edgeHub
* lvaEdge
* lvaYolov3

Für die Bereitstellung wurde eine benutzerdefinierte IoT Edge-Umgebung mit den erforderlichen Modulen für die Livevideoanalyse erstellt. Für die Bereitstellung wurde die Standarddatei **config.yaml** aktualisiert, um sicherzustellen, dass für die IoT Edge-Runtime der IoT Device Provisioning-Dienst für die Verbindungsherstellung mit IoT Central verwendet wurde. Darüber hinaus wurde für die Bereitstellung eine Datei mit dem Namen **state.json** im Ordner **/data/storage** erstellt, um zusätzliche Konfigurationsdaten für die Module bereitzustellen. Weitere Informationen finden Sie im Tutorial [Erstellen einer IoT Edge-Instanz für die Videoanalyse (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md).

Informationen zur Problembehandlung für das IoT Edge-Gerät finden Sie unter [Behandeln von Problemen bei Ihrem IoT Edge-Gerät](../../iot-edge/troubleshoot.md).

## <a name="use-the-rtsp-simulator"></a>Verwenden des RTSP-Simulators

Falls Sie nicht über echte Kameras verfügen, die Sie an Ihr IoT Edge-Gerät anschließen können, können Sie die beiden simulierten Kamerageräte in der Vorlage für die Videoanalyseanwendung verwenden. In diesem Abschnitt wird veranschaulicht, wie Sie auf Ihrem IoT Edge-Gerät einen simulierten Videodatenstrom verwenden.

Hierbei wird der [Live555 Media Server](http://www.live555.com/mediaServer/) als RTSP-Simulator in einem Docker-Container genutzt.

> [!NOTE]
> Verweise auf Drittanbietersoftware in diesem Repository dienen nur zu Informations- und Komfortzwecken. Microsoft leistet keine Unterstützung für die Drittanbietersoftware und stellt auch keine Rechte dafür bereit. Weitere Informationen finden Sie unter [Live555 Media Server](http://www.live555.com/mediaServer/).

Führen Sie den folgenden Befehl aus, um das Hilfsprogramm **rtspvideo** in einem Docker-Container auf Ihrer IoT Edge-VM auszuführen. Der Docker-Container erstellt einen RTSP-Hintergrunddatenstrom:

```bash
sudo docker run -d --name live555 --rm -p 554:554 mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2
```

Verwenden Sie den folgenden Befehl, um die Docker-Container aufzulisten:

```bash
sudo docker ps
```

Die Liste enthält einen Container mit dem Namen **live555**.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Anwendung nicht mehr benötigen, können Sie alle erstellten Ressourcen wie folgt entfernen:

1. Navigieren Sie in der IoT Central-Anwendung zur Seite **Ihre Anwendung** im Abschnitt **Verwaltung**. Wählen Sie anschließend die Option **Löschen**.
1. Löschen Sie im Azure-Portal die Ressourcengruppe **lva-rg**.
1. Beenden Sie auf Ihrem lokalen Computer den Docker-Container **amp-viewer**.

## <a name="next-steps"></a>Nächste Schritte

Sie haben die IoT Edge-Runtime, die LVA-Module und den Live555-Simulationsdatenstrom nun auf einer Linux-VM unter Azure bereitgestellt.

Informationen zum Verwalten der Kameras erhalten Sie beim Durcharbeiten des nächsten Tutorials.

> [!div class="nextstepaction"]
> [Überwachen und Verwalten einer Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“](./tutorial-video-analytics-manage.md)