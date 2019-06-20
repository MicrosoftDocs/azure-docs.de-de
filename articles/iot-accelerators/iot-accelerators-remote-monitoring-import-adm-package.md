---
title: Importieren eines Pakets in die Remoteüberwachungslösung – Azure | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie ein Paket für die automatische Geräteverwaltung in den Solution Accelerator für die Remoteüberwachung importieren.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61443333"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Importieren eines Pakets für die automatische Geräteverwaltung in den Solution Accelerator für die Remoteüberwachung

Eine Konfiguration für die automatische Geräteverwaltung definiert die Konfigurationsänderungen, die für eine Gruppe von Geräten bereitgestellt werden. In diesem Artikel wird vorausgesetzt, dass ein Entwickler in Ihrer Organisation bereits eine Konfiguration für die automatische Geräteverwaltung erstellt hat. Um zu erfahren, wie Entwickler eine solche Konfiguration erstellen, lesen Sie eine der folgenden IoT Hub-Anleitungen:

- [Bedarfsgerechtes Konfigurieren und Überwachen von IoT-Geräten mit dem Azure-Portal](../iot-hub/iot-hub-auto-device-config.md)
- [Bedarfsgerechtes Konfigurieren und Überwachen von IoT-Geräten mit der Azure CLI](../iot-hub/iot-hub-auto-device-config-cli.md)

Ein Entwickler erstellt und testet eine Konfiguration für die automatische Geräteverwaltung in einer Entwicklungsumgebung. Wenn Sie bereit sind, können Sie die Konfiguration in den Solution Accelerator für die Remoteüberwachung importieren.

## <a name="export-a-configuration"></a>Exportieren einer Konfiguration

Verwenden Sie das Azure-Portal, um die Konfiguration für die automatische Geräteverwaltung aus der Entwicklungsumgebung zu exportieren:

1. Navigieren Sie im Azure-Portal zum IoT-Hub, den Sie zum Entwickeln und Testen Ihre IoT-Geräte verwenden. Klicken Sie auf **IoT-Gerätekonfiguration**:

    [![IoT-Gerätekonfiguration](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Klicken Sie auf die Konfiguration, die Sie verwenden möchten. Die Seite **Details zur Gerätekonfiguration** wird angezeigt:

    [![Details zur IoT-Gerätekonfiguration](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Klicken Sie auf **Konfigurationsdatei herunterladen**:

    [![Konfigurationsdatei herunterladen](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Speichern Sie die JSON-Datei als lokale Datei namens **configuration.json**.

Sie haben jetzt eine Datei, die die Konfiguration für die automatische Geräteverwaltung enthält. Im nächsten Abschnitt importieren Sie diese Konfiguration als Paket in die Remoteüberwachungslösung.

## <a name="import-a-package"></a>Importieren eines Pakets

Gehen Sie folgendermaßen vor, um eine Konfiguration für die automatische Geräteverwaltung als Paket in Ihre Lösung zu importieren:

1. Navigieren Sie auf der Webbenutzeroberfläche für die Remoteüberwachung zur Seite **Pakete**:  ![Seite „Pakete“](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Klicken Sie auf **+ Neues Paket**, wählen Sie **Konfiguration** als Pakettyp aus, und klicken Sie auf **Durchsuchen**, um die im vorherigen Abschnitt gespeicherte Datei **configuration.json** auszuwählen:

    ![Auswählen von „Konfiguration“](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Klicken Sie auf **Hochladen**, um das Paket Ihrer Remoteüberwachungslösung hinzuzufügen:

    ![Hochgeladenes Paket](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Sie haben soeben eine Konfiguration für die automatische Geräteverwaltung als Paket hochgeladen. Auf der Seite **Bereitstellungen** können Sie dieses Paket Ihren verbundenen Geräte bereitstellen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie ein Konfigurationspaket erstellen und aus der Remoteüberwachungslösung importieren, finden Sie den nächsten Schritt unter [Durchführen der Massenverwaltung für Ihre verbundenen Geräte](iot-accelerators-remote-monitoring-bulk-configuration-update.md).
