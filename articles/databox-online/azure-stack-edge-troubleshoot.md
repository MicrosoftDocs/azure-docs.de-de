---
title: Behandeln von Problemen mit Azure Stack Edge Pro im Azure-Portal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Probleme mit Azure Stack Edge Pro beheben. Sie können Diagnosen durchführen, Informationen für den Support sammeln und Protokolle zur Problembehandlung verwenden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/05/2021
ms.author: alkohli
ms.openlocfilehash: ff6d1f46299875354a72a6a93a828df7960169ad
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831540"
---
# <a name="troubleshoot-your-azure-stack-edge-pro-issues"></a>Behandeln von Problemen mit Azure Stack Edge Pro

In diesem Artikel wird beschrieben, wie Sie Probleme mit Azure Stack Edge Pro behandeln können. 

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
>
> * Ausführen von Diagnosen
> * Zusammenstellen des Unterstützungspaket
> * Behandeln von Problemen anhand von Protokollen
> * Behandeln von IoT Edge-Fehlern

## <a name="run-diagnostics"></a>Ausführen von Diagnosen

Zum Diagnostizieren und Behandeln von Gerätefehlern können Sie die Diagnosetests ausführen. Gehen Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts wie folgt vor, um Diagnosetests auszuführen:

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung > Diagnosetests**. Wählen Sie den gewünschten Test aus, und klicken Sie auf **Test ausführen**. Daraufhin werden die Tests ausgeführt, um mögliche Probleme mit Ihren Netzwerk-, Geräte-, Webproxy-, Zeit- oder Cloudeinstellungen zu diagnostizieren. Sie werden benachrichtigt, dass auf dem Gerät Tests ausgeführt werden.

    ![Auswählen von Tests](media/azure-stack-edge-troubleshoot/run-diag-1.png)

2. Nach Abschluss der Tests werden die Ergebnisse angezeigt.

    ![Überprüfen der Testergebnisse](media/azure-stack-edge-troubleshoot/run-diag-2.png)

    Sollte ein Test nicht erfolgreich sein, wird eine URL für die empfohlene Maßnahme angezeigt. Wählen Sie die URL aus, um die empfohlene Maßnahme anzuzeigen.

    ![Überprüfen von Warnungen für Tests mit Fehlern](media/azure-stack-edge-troubleshoot/run-diag-3.png)

## <a name="collect-support-package"></a>Zusammenstellen des Unterstützungspaket

Ein Protokollpaket enthält alle relevanten Protokolle, die das Supportteam von Microsoft bei der Behandlung von Geräteproblemen unterstützen. Sie können ein Unterstützungspaket über die lokale Webbenutzeroberfläche generieren.

Gehen Sie wie folgt vor, um ein Unterstützungspaket zusammenzustellen:

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung > Support**. Wählen Sie **Supportpaket erstellen** aus. Daraufhin wird das Unterstützungspaket zusammengestellt. Dieser Vorgang kann mehrere Minuten dauern.

    ![Klicken auf „Benutzer hinzufügen“](media/azure-stack-edge-troubleshoot/collect-logs-1.png)

2. Nachdem das Supportpaket erstellt wurde, wählen Sie **Supportpaket herunterladen** aus. Ein ZIP-Paket wird an den gewünschten Pfad heruntergeladen. Sie können das Paket entpacken und sich die Systemprotokolldateien ansehen.

    ![Klicken auf „Benutzer 2 hinzufügen“](media/azure-stack-edge-troubleshoot/collect-logs-2.png)

## <a name="use-logs-to-troubleshoot"></a>Behandeln von Problemen anhand von Protokollen

Alle Fehler, die während der Upload- und Aktualisierungsprozesse aufgetreten sind, sind in den entsprechenden Fehlerdateien enthalten.

1. Navigieren Sie zum Anzeigen der Fehlerdateien zu Ihrer Freigabe, und wählen Sie eine Datei aus, um den Inhalt anzuzeigen. 

      ![Herstellen einer Verbindung mit und Anzeigen von Freigabeinhalten](media/azure-stack-edge-troubleshoot/troubleshoot-logs-1.png)

2. Wählen Sie den Ordner _Microsoft Azure Stack Edge Pro_ aus. Dieser Ordner enthält zwei Unterordner:

    * Der Ordner „Upload“ enthält Protokolldateien für Uploadfehler.
    * Der Ordner „Refresh“ ist für Fehler bei der Aktualisierung vorgesehen.

    Hier sehen Sie eine exemplarische Protokolldatei für die Aktualisierung.

    ```xml
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ```

3. Wenn diese Datei einen Fehler enthält (im Beispiel hervorgehoben), notieren Sie sich den Fehlercode (in diesem Fall: 16001). Suchen Sie anhand dieses Fehlercodes in der folgenden Fehlerreferenz nach der entsprechenden Fehlerbeschreibung.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="troubleshoot-iot-edge-errors"></a>Behandeln von IoT Edge-Fehlern

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die [bekannten Probleme in dieser Version](../databox-gateway/data-box-gateway-release-notes.md).