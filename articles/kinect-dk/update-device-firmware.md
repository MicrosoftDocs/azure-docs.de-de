---
title: Aktualisieren der Azure Kinect DK-Firmware
description: Erfahren Sie, wie Sie die Azure Kinect DK-Gerätefirmware mit dem Azure Kinect-Firmwaretool aktualisieren.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Firmware, Update, Wiederherstellung
ms.openlocfilehash: 71557798a584635356cc47fd7c4a0309df4d018a
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356223"
---
# <a name="update-azure-kinect-dk-firmware"></a>Aktualisieren der Azure Kinect DK-Firmware

Dieses Dokument bietet Hilfestellung zum Aktualisieren der Gerätefirmware auf Ihrem Azure Kinect DK.

Beim Azure Kinect DK wird die Firmware nicht automatisch aktualisiert. Sie können das [Azure Kinect-Firmwaretool](azure-kinect-firmware-tool.md) verwenden, um die Firmware manuell auf die letzte verfügbare Version zu aktualisieren.

## <a name="prepare-for-firmware-update"></a>Vorbereiten des Firmwareupdates

1. [Laden Sie das SDK herunter](sensor-sdk-download.md).
2. Installieren Sie das SDK.
3. Am SDK-Installationsspeicherort sollten Sie unter (SDK-Installationsspeicherort)\tools\ Folgendes finden:

    - AzureKinectFirmwareTool.exe
    - Eine BIN-Firmwaredatei im Firmwareordner, z. B. *AzureKinectDK_Fw_1.5.926614.bin*.

4. Verbinden Sie Ihr Gerät mit dem Host-PC, und schalten Sie es ein.

> [!IMPORTANT]
> Achten Sie darauf, dass USB-Verbindung und Stromversorgung während des Firmwareupdates verbunden bleiben. Das Trennen einer der Verbindungen während des Updates kann zu einem beschädigten Zustand der Firmware führen.

## <a name="update-device-firmware"></a>Aktualisieren der Gerätefirmware

1. Öffnen Sie eine Eingabeaufforderung im Ordner (SDK-Installationsort)\tools\.
2. Aktualisieren der Firmware mit dem Azure Kinect-Firmwaretool

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    Beispiel:

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. Warten Sie, bis das Firmwareupdate abgeschlossen ist. Abhängig von der Imagegröße kann dies einige Minuten dauern.

### <a name="verify-device-firmware-version"></a>Überprüfen der Firmwareversion des Geräts

1. Vergewissern Sie sich, dass die Firmware aktualisiert wurde.

    `AzureKinectFirmwareTool.exe -q`

2. Sehen Sie dazu das folgende Beispiel an.

    ```console
       >AzureKinectFirmwareTool.exe -q
       == Azure Kinect DK Firmware Tool ==
        Device Serial Number: 000805192412
        Current Firmware Versions:
        RGB camera firmware:      1.6.102
        Depth camera firmware:    1.6.75
        Depth config file:        6109.7
        Audio firmware:           1.6.14
        Build Config:             Production
        Certificate Type:         Microsoft
    ```

3. Wenn die oben dargestellte Ausgabe angezeigt wird, wurde die Firmware aktualisiert.

4. Nach dem Firmwareupdate können Sie den [Azure Kinect-Viewer](azure-kinect-viewer.md) ausführen, um zu überprüfen, ob alle Sensoren wie erwartet arbeiten.

## <a name="troubleshooting"></a>Problembehandlung

Firmwareupdates können aus verschiedenen Gründen fehlschlagen. Wenn ein Firmwareupdate fehlschlägt, führen Sie zur Abhilfe die folgenden Schritte aus:

1. Versuchen Sie, den Firmware-Updatebefehl ein zweites Mal auszuführen.

2. Vergewissern Sie sich, dass das Gerät weiterhin verbunden ist, indem Sie die Firmwareversion abfragen.        AzureKinectFirmareTool.exe

3. Wenn alle anderen Versuche fehlschlagen, führen Sie die Schritte zur [Wiederherstellung](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) aus, um auf die Firmenfirmware zurückzusetzen, und versuchen Sie es erneut.

Konsultieren Sie für alle weiteren Probleme die [Microsoft-Supportseiten](./index.yml)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>[Azure Kinect DK-Firmwaretool](azure-kinect-firmware-tool.md)