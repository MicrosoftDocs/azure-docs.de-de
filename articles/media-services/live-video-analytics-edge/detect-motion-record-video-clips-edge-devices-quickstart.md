---
title: 'Erkennen von Bewegung und Aufzeichnen von Videos auf Edgegeräten: Azure'
description: In diesem Schnellstart erfahren Sie, wie Sie Live Video Analytics in IoT Edge verwenden, um den Livevideofeed von einer (simulierten) IP-Kamera zu analysieren und zu erkennen, ob Bewegungen vorhanden sind, und in diesem Fall einen MP4-Videoclip im lokalen Dateisystem auf dem Edgegerät aufzuzeichnen.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: e4334cdd14242337a2a870f31886606020654685
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498352"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Schnellstart: Erkennen von Bewegung und Aufzeichnen von Videos auf Edgegeräten
 
In dieser Schnellstartanleitung wird beschrieben, wie Sie mithilfe von Live Video Analytics in IoT Edge den Livevideofeed einer (simulierten) IP-Kamera analysieren. Es wird veranschaulicht, wie Bewegung im Video erkannt wird, damit dann ein MP4-Videoclip im lokalen Dateisystem auf dem Edgegerät aufgezeichnet werden kann. Für die Schnellstartanleitung werden ein virtueller Azure-Computer als IoT Edge-Gerät und ein simulierter Livevideostream verwendet. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Überprüfen des Beispielvideos

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Übersicht

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/overview.md)]
::: zone-end

## <a name="examine-and-edit-the-sample-files"></a>Untersuchen und Bearbeiten der Beispieldateien

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/examine-edit-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/examine-edit-sample-files.md)]
::: zone-end

## <a name="review---check-the-modules-status"></a>Überprüfung: Überprüfen des Status der Module

::: zone pivot="programming-language-csharp"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/check-modules-status.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/check-modules-status.md)]
::: zone-end

## <a name="review---prepare-for-monitoring-events"></a>Überprüfung: Vorbereiten der Überwachung von Ereignissen

::: zone pivot="programming-language-csharp"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prepare-monitoring-events.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prepare-monitoring-events.md)]
::: zone-end

## <a name="run-the-sample-program"></a>Ausführen des Beispielprogramms

::: zone pivot="programming-language-csharp"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/run-sample-program.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/run-sample-program.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretieren von Ergebnissen 

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="play-the-mp4-clip"></a>Wiedergeben des MP4-Clips

::: zone pivot="programming-language-csharp"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/play-mp4-clip.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/play-mp4-clip.md)]
::: zone-end

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie auch die anderen Schnellstartanleitungen durcharbeiten möchten, sollten Sie die von Ihnen erstellten Ressourcen beibehalten. Navigieren Sie andernfalls im Azure-Portal zu Ihren Ressourcengruppen, wählen Sie die Ressourcengruppe aus, unter der Sie diese Schnellstartanleitung ausgeführt haben, und löschen Sie dann alle Ressourcen.

## <a name="next-steps"></a>Nächste Schritte

* Arbeiten Sie die Schnellstartanleitung [Ausführen von Live Video Analytics mit einem eigenen Modell](use-your-model-quickstart.md) durch, in der beschrieben wird, wie KI auf Livevideofeeds angewendet wird.
* Zusätzliches für fortgeschrittene Benutzer:

    * Verwenden Sie eine [IP-Kamera](https://en.wikipedia.org/wiki/IP_camera) mit RTSP-Unterstützung anstelle des RTSP-Simulators. IP-Kameras, die RTSP unterstützen, finden Sie auf der [Seite mit den ONVIF-konformen Produkten](https://www.onvif.org/conformant-products). Suchen Sie nach Geräten, die mit den Profilen G, S oder T konform sind.
    * Verwenden Sie ein AMD64- oder x64-Linux-Gerät anstelle einer Linux-VM in Azure. Dieses Gerät muss sich im gleichen Netzwerk befinden wie die IP-Kamera. Befolgen Sie die Anleitung unter [Installieren der Azure IoT Edge-Runtime auf Debian-basierten Linux-Systemen](../../iot-edge/how-to-install-iot-edge.md). Befolgen Sie anschließend die Anleitung unter [Schnellstart: Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](../../iot-edge/quickstart-linux.md), um das Gerät für Azure IoT Hub zu registrieren.