---
title: include file
description: include file
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2f3e4bf640b8da31a7fa4d818b94b0372d3026b8
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763425"
---
Die Beispielanwendung umfasst zwei simulierte Geräte und ein IoT Edge-Gateway. Die folgenden Tutorials veranschaulichen zwei Ansätze zum Experimentieren mit den Funktionen des Gateways und zum besseren Verständnis:

* Erstellen Sie das IoT Edge-Gateway auf einer Azure-VM, und schließen Sie eine simulierte Kamera an.
* Erstellen Sie das IoT Edge-Gateway auf einem echten Gerät, z. B. Intel NUC, und schließen Sie eine echte Kamera an.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
> * Verwenden der Anwendungsvorlage für die Azure IoT Central-Videoanalyse zum Erstellen einer Filialenanwendung
> * Anpassen der Anwendungseinstellungen
> * Erstellen einer Gerätevorlage für ein IoT Edge-Gatewaygerät
> * Hinzufügen eines Gatewaygeräts zu Ihrer IoT Central-Anwendung

## <a name="prerequisites"></a>Voraussetzungen

Zum Abschließen dieses Tutorials benötigen Sie Folgendes:

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie auf der Seite [Azure-Anmeldeseite](https://aka.ms/createazuresubscription) eines erstellen.
* Bei Verwendung einer echten Kamera benötigen Sie Konnektivität zwischen dem IoT Edge-Gerät und der Kamera sowie den **Real-Time Streaming Protocol**-Kanal.

## <a name="initial-setup"></a>Erste Einrichtung

In diesen Tutorials aktualisieren und verwenden Sie mehrere Konfigurationsdateien. Die Anfangsversionen dieser Dateien sind im GitHub-Repository [LVA-gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) verfügbar. Das Repository enthält eine [Scratchpad](https://github.com/Azure/live-video-analytics/blob/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)-Textdatei, die Sie herunterladen und verwenden können, um Konfigurationswerte für die von Ihnen bereitgestellten Dienste aufzuzeichnen. Mit dieser Datei können Sie später Schritte in den Tutorials ausführen.

Erstellen Sie auf Ihrem lokalen Computer einen Ordner mit dem Namen *lva-configuration*, um darin Kopien dieser Dateien zu speichern. Klicken Sie anschließend mit der rechten Maustaste auf die folgenden Links, und wählen Sie **Speichern unter** aus, um die Datei im Ordner *lva-configuration* zu speichern:
