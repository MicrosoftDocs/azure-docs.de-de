---
title: 'Analysieren von Livevideos mithilfe eines eigenen HTTP-Modells: Azure'
description: In diesem Schnellstart wenden Sie maschinelles Sehen an, um den Livevideofeed von einer (simulierten) IP-Kamera mithilfe Ihres eigenen HTTP-Modells zu analysieren.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: d3ba937abcc7bbfd9bb2afe7b15aec28ebb57446
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508510"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-http-model"></a>Schnellstart: Analysieren von Livevideos mithilfe eines eigenen HTTP-Modells

In dieser Schnellstartanleitung wird beschrieben, wie Sie mithilfe von Live Video Analytics in IoT Edge einen Livevideofeed einer (simulierten) IP-Kamera analysieren. Sie erfahren, wie Sie ein Modell für maschinelles Sehen anwenden, um Objekte zu erkennen. Eine Teilmenge der Einzelbilder des Livevideofeeds wird an einen Rückschlussdienst gesendet. Die Ergebnisse werden an IoT Edge Hub gesendet. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Überprüfen des Beispielvideos

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Übersicht

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>Erstellen und Bereitstellen des Mediengraphen

::: zone pivot="programming-language-csharp"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretieren von Ergebnissen

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie weitere Schnellstartanleitungen durcharbeiten möchten, sollten Sie die von Ihnen erstellten Ressourcen beibehalten. Navigieren Sie andernfalls zum Azure-Portal, dort zu Ihren Ressourcengruppen, wählen Sie die Ressourcengruppe aus, unter der Sie diese Schnellstartanleitung ausgeführt haben, und löschen Sie alle Ressourcen.

## <a name="next-steps"></a>Nächste Schritte

* Testen Sie eine [gesicherte Version des YoloV3-Modells](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/tls-yolov3-onnx/readme.md), und stellen Sie sie auf dem IoT Edge-Gerät bereit. 

Zusätzliches für fortgeschrittene Benutzer:

* Verwenden Sie eine [IP-Kamera](https://en.wikipedia.org/wiki/IP_camera) mit RTSP-Unterstützung anstelle des RTSP-Simulators. IP-Kameras, die RTSP unterstützen, finden Sie auf der [Seite mit den ONVIF-konformen Produkten](https://www.onvif.org/conformant-products/). Suchen Sie nach Geräten, die mit den Profilen G, S oder T konform sind.
* Verwenden Sie ein AMD64- oder x64-Linux-Gerät anstelle eines virtuellen Azure-Linux-Computers. Dieses Gerät muss sich im gleichen Netzwerk befinden wie die IP-Kamera. Sie können die Anleitungen unter [Installieren der Azure IoT Edge-Runtime unter Linux](../../iot-edge/how-to-install-iot-edge.md) befolgen. Registrieren Sie das Gerät anschließend beim Azure IoT Hub, indem Sie die Anweisungen in [Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](../../iot-edge/quickstart-linux.md) ausführen.