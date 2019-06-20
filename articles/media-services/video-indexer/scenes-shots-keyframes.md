---
title: Szenen, Aufnahmen und Keyframes in Video Indexer – Azure
titlesuffix: Azure Media Services
description: Dieses Thema bietet eine Übersicht über Szenen, Aufnahmen und Keyframes in Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: d312a93f83ef38fa1ae855a1e313280fc608948d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799401"
---
# <a name="scenes-shots-and-keyframes"></a>Szenen, Aufnahmen und Keyframes

Video Indexer unterstützt die Segmentierung von Videos in zeitliche Einheiten basierend auf strukturellen und semantischen Eigenschaften. Diese Funktion ermöglicht es Kunden, ihre Videoinhalte auf der Grundlage unterschiedlicher Granularitäten einfach zu durchsuchen, zu verwalten und zu bearbeiten. Beispielsweise basierend auf Szenen, Aufnahmen und Keyframes, die in diesem Thema beschrieben werden. Dieses Feature für die **Szenenerkennung** befindet sich derzeit in der Vorschauphase.   

![Szenen, Aufnahmen und Keyframes](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)

## <a name="scene-detection-preview"></a>Szenenerkennung (Vorschau)

Der Video Indexer bestimmt basierend auf visuellen Hinweisen, wann sich eine Szene im Video ändert. Eine Szene stellt ein einzelnes Ereignis dar und besteht aus einer Reihe von aufeinanderfolgenden Aufnahmen, die semantisch zusammenhängen. Ein Szenenminiaturbild ist das erste Keyframe der zugrunde liegenden Aufnahme. Der Video Indexer segmentiert ein Video in Szenen, die auf Farbkohärenz bei aufeinanderfolgenden Aufnahmen basieren, und ruft die Anfangs- und Endzeit jeder Szene ab. Die Szenenerkennung stellt eine anspruchsvolle Aufgabe dar, da sie die Quantifizierung semantischer Aspekte von Videos beinhaltet.

> [!NOTE]
> Sie kann für Videos mit mindestens drei Szenen angewendet werden.

## <a name="shot-detection"></a>Szenenwechselerkennung

Der Video Indexer bestimmt, wann sich eine Aufnahme im Video aufgrund visueller Signale ändert, indem er sowohl abrupte als auch allmähliche Übergänge im Farbschema benachbarter Frames verfolgt. Die Metadaten der Aufnahme beinhalten eine Start- und Endzeit sowie die Liste der Keyframes, die darin enthalten sind. Die Aufnahmen sind aufeinanderfolgende Bilder, die gleichzeitig von derselben Kamera aufgenommen werden.

## <a name="keyframe-detection"></a>Keyframe-Erkennung

Wählt Sie die Frames aus, die die Aufnahme am besten darstellen. Keyframes sind die repräsentativen Einzelframes, die basierend auf ästhetischen Eigenschaften (z.B. Kontrast und Stabilität) aus dem gesamten Video ausgewählt werden. Video Indexer ruft eine Liste von Keyframe-IDs als Teil der Metadaten der Aufnahme ab, anhand derer Kunden das Keyframeminiaturbild extrahieren können. 

Keyframes sind mit Aufnahmen in der Ausgabe-JSON verknüpft. 

## <a name="next-steps"></a>Nächste Schritte

[Untersuchen der von der API erstellten Video Indexer-Ausgabe](video-indexer-output-json-v2.md#scenes)