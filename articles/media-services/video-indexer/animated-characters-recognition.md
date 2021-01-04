---
title: Erkennung animierter Figuren mit Video Indexer
titleSuffix: Azure Media Services
description: In diesem Thema erfahren Sie, wie Sie die Erkennung animierter Figuren mit Video Indexer verwenden.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: f1b19f178a60671108ec32ef6fbed1afe73a0cee
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854749"
---
# <a name="animated-character-detection-preview"></a>Erkennung animierter Figuren (Vorschauversion)

Azure Media Services Video Indexer unterstützt die Erkennung und Gruppierung von Figuren in animierten Inhalten mittels Integration von [Cognitive Services Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Diese Funktion steht sowohl über das Portal als auch über die API zur Verfügung.

Nach dem Hochladen eines animierten Videos mit einem bestimmten Animationsmodell extrahiert Video Indexer Keyframes, erkennt animierte Figuren in diesen Frames, gruppiert ähnliche Figuren und wählt das beste Beispiel aus. Anschließend werden die gruppierten Figuren an Custom Vision gesendet, um sie auf der Grundlage der trainierten Modelle zu identifizieren. 

Wenn Ihr Modell noch nicht trainiert wurde, werden die Figuren namenlos erkannt. Wenn Sie Namen hinzufügen und das Modell trainieren, erkennt Video Indexer die Figuren und benennt sie entsprechend.

## <a name="flow-diagram"></a>Flussdiagramm

Das folgende Diagramm zeigt den Ablauf des Erkennungsprozesses für animierte Figuren:

![Flussdiagramm](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Konten

Abhängig von der Art Ihres Video Indexer-Kontos stehen unterschiedliche Features zur Verfügung. Unter [Erstellen eines Video Indexer-Kontos mit Azure-Verbindung](connect-to-azure.md) erfahren Sie, wie Sie Ihr Konto mit Azure verbinden.

* Testkonto: Video Indexer verwendet ein internes Custom Vision-Konto, um ein Modell zu erstellen und mit Ihrem Video Indexer-Konto zu verbinden. 
* Kostenpflichtiges Konto: Sie verbinden Ihr Custom Vision-Konto mit Ihrem Video Indexer-Konto. (Sollten Sie noch nicht über ein Konto verfügen, müssen Sie erst ein Konto erstellen.)

### <a name="trial-vs-paid"></a>Gegenüberstellung von Testkonto und kostenpflichtigem Konto

|Funktionalität|Testversion|Kostenpflichtig|
|---|---|---|
|Custom Vision-Konto|Wird im Hintergrund durch Video Indexer verwaltet. |Ihr Custom Vision-Konto wird mit Video Indexer verbunden.|
|Anzahl von Animationsmodellen|Eine|Bis zu 100 Modelle pro Konto (Custom Vision-Einschränkung)|
|Training des Modells|Video Indexer trainiert das Modell für neue Figuren anhand von zusätzlichen Beispielen vorhandener Figuren.|Der Kontobesitzer trainiert das Modell, wenn er bereit ist, Änderungen vorzunehmen.|
|Erweiterte Optionen in Custom Vision|Kein Zugriff auf das Custom Vision-Portal|Sie können die Modelle selbst im Custom Vision-Portal anpassen.|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>Verwenden der Erkennung animierter Figuren über das Portal und die API

Weitere Informationen finden Sie unter [Verwenden der Erkennung animierter Figuren über das Portal und die API](animated-characters-recognition-how-to.md).

## <a name="limitations"></a>Einschränkungen

* Die Animationserkennung wird momentan in der Region „Asien, Osten“ nicht unterstützt.
* Kleine oder weit entfernte Figuren werden in Videos mit schlechter Qualität möglicherweise nicht ordnungsgemäß erkannt.
* Es wird empfohlen, für jede Gruppe von animierten Figuren (beispielsweise pro animierter Serie) ein eigenes Modell zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

[What is Video Indexer? (preview)](video-indexer-overview.md) (Was ist Video Indexer? (Vorschauversion))