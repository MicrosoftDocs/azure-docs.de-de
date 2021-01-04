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
ms.openlocfilehash: 0b3ccc31c9159b5d7b1615add89e8fdc308bf8df
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763457"
---
### <a name="publish-the-device-template"></a>Veröffentlichen der Gerätevorlage

Bevor Sie der Anwendung ein Gerät hinzufügen können, müssen Sie die Gerätevorlage veröffentlichen:

1. Wählen Sie in der Gerätevorlage **LVA-Edgegateway v2** die Option **Veröffentlichen** aus.

1. Wählen Sie auf der Seite **Diese Gerätevorlage für die Anwendung veröffentlichen** die Option **Veröffentlichen** aus.

**LVA-Edgegateway v2** ist jetzt als Gerätetyp verfügbar, der in der Anwendung auf der Seite **Geräte** verwendet werden kann.

## <a name="migrate-the-gateway-device"></a>Migrieren des Gatewaygeräts

Das vorhandene Gerät **gateway-001** nutzt die Gerätevorlage **LVA-Edgegateway**. Wenn Sie das neue Bereitstellungsmanifest verwenden möchten, migrieren Sie das Gerät zur neuen Gerätevorlage:

So migrieren Sie das Gerät **gateway-001**:

1. Navigieren Sie zur Seite **Geräte**, und wählen Sie das Gerät **gateway-001** aus, um es in der Liste hervorzuheben.

1. Klicken Sie auf **Migrieren**. Wird das Symbol **Migrieren** nicht angezeigt, wählen Sie **...** aus, um weitere Optionen anzuzeigen.

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="Migrieren des Gatewaygeräts zu einer neuen Version":::

1. Wählen Sie im Dialogfeld **Migrieren** in der Liste die Option **LVA-Edgegateway v2** und anschließend **Migrieren** aus.

Nach einigen Sekunden ist die Migration abgeschlossen. Ihr Gerät verwendet jetzt die Gerätevorlage **LVA-Edgegateway v2** mit Ihrem angepassten Bereitstellungsmanifest.

Kein Gerät verwendet nun die ursprüngliche Gerätevorlage **LVA-Edgegateway**. Löschen Sie diese Gerätevorlage:

1. Navigieren Sie zur Seite **Gerätevorlagen**, und wählen Sie die Gerätevorlage **LVA-Edgegateway** aus.

1. Wählen Sie **Löschen** aus, um die Gerätevorlage zu löschen.

### <a name="get-the-device-credentials"></a>Abrufen der Geräteanmeldeinformationen

Sie benötigen die Anmeldeinformationen, mit denen das Gerät eine Verbindung mit Ihrer IoT Central-Anwendung herstellen kann. So rufen Sie die Anmeldeinformationen ab:

1. Wählen Sie auf der Seite **Geräte** das Gerät **gateway-001** aus.

1. Wählen Sie **Verbinden**.

1. Notieren Sie sich auf der Seite **Geräteverbindung** in der Datei *scratchpad.txt* den **ID-Bereich**, die **Geräte-ID** und den **Primärschlüssel** des Geräts. Diese Werte werden später benötigt.

1. Stellen Sie sicher, dass die Verbindungsmethode auf **Shared Access Signature** festgelegt ist.

1. Klicken Sie auf **Schließen**.

## <a name="next-steps"></a>Nächste Schritte

Sie haben jetzt mit der Anwendungsvorlage **Videoanalyse: Objekt- und Bewegungserkennung** eine IoT Central-Anwendung und dann eine Gerätevorlage für das Gatewaygerät erstellt und der Anwendung anschließend ein Gatewaygerät hinzugefügt.

Gehen Sie wie folgt vor, falls Sie die Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“ mit IoT Edge-Modulen ausprobieren möchten, für die eine Cloud-VM mit simulierten Videodatenströmen ausgeführt wird:

> [!div class="nextstepaction"]
> [Erstellen einer IoT Edge-Instanz für die Videoanalyse (Linux-VM)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

Gehen Sie wie folgt vor, falls Sie die Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“ mit IoT Edge-Modulen ausprobieren möchten, für die ein echtes Gerät mit einer echten **ONVIF**-Kamera ausgeführt wird:

> [!div class="nextstepaction"]
> [Erstellen einer IoT Edge-Instanz für die Videoanalyse (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
