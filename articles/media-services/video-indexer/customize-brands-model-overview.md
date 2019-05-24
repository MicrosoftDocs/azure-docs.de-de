---
title: 'Anpassen eines Markenmodells in Video Indexer: Azure'
titlesuffix: Azure Media Services
description: Dieser Artikel gibt einen Überblick darüber, was ein Markenmodell in Video Indexer ist und wie Sie es anpassen können.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 863dbd9a6044ee33ae39ac9693a7d4f74382b9c7
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799673"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Anpassen eines Markenmodells in Video Indexer

Video Indexer unterstützt die Markenerkennung aus Sprache und visuellem Text während der Indizierung und Neuindizierung von Video- und Audioinhalten. Das Feature zur Erkennung von Marken identifiziert Erwähnungen von Produkten, Diensten und Unternehmen, die von der Markendatenbank von Bing vorgeschlagen werden. Wenn Microsoft beispielsweise in einem Video- oder Audioinhalt erwähnt wird oder im visuellen Text in einem Video angegeben wird, erkennt Video Indexer dies als Marke im Inhalt. Marken werden über den Kontext eindeutig von anderen Begriffen unterschieden.

Die Markenerkennung ist in einer Vielzahl von Geschäftsszenarien nützlich, z. B. bei der Archivierung und Erkennung von Inhalten, bei kontextabhängiger Werbung, Social Media-Analysen, Wettbewerbsanalyse im Einzelhandel und vielem mehr. Die Markenerkennung von Video Indexer ermöglicht es Ihnen, die Erwähnungen von Marken in Sprache und visuellem Text über die Markendatenbank von Bing und durch Anpassungen zu indizieren, indem Sie ein benutzerdefiniertes Markenmodell für jedes Video Indexer-Konto erstellen. Das Feature für benutzerdefinierte Markenmodelle ermöglicht das Auswählen, ob Video Indexer Marken aus der Bing-Markendatenbank erkennt, bestimmte Marken von der Erkennung ausschließt (im Wesentlichen eine Blockliste von Marken erstellt) und Marken einbezieht, die Teil Ihres Modells sein sollten, die möglicherweise nicht in der Bing-Markendatenbank enthalten sind (im Wesentlichen Erstellung einer Whitelist von Marken). Das von Ihnen erstellte benutzerdefinierte Markenmodell ist nur in dem Konto verfügbar, in dem Sie das Modell erstellt haben.

## <a name="out-of-the-box-detection-example"></a>Beispiel zur vorkonfigurierten Erkennung

In der Präsentation [Microsoft Build 2017 Day 2](https://www.videoindexer.ai/media/ed6ede78ad/) wird die Marke „Microsoft Windows“ mehrfach verwendet. Gelegentlich im Transkript, manchmal als sichtbarer Text, aber nie buchstäblich. Video Indexer erkennt mit hoher Genauigkeit auf der Grundlage des Kontexts, dass ein Begriff tatsächlich eine Marke ist, wobei über 90.000 Marken vorkonfiguriert sind und ständig aktualisiert werden. Video Indexer erkennt die Marke bei 02:25 in den Sprachinhalten und dann erneut bei 02:40 im sichtbaren Text, der Teil des Windows-Logos ist.

![Markenübersicht](./media/content-model-customization/brands-overview.png)

Beim Erwähnen von Fenstern (windows) im Kontext des Bauwesens wird das Wort „Windows“ nicht als Marke erkannt. Dasselbe gilt basierend auf fortschrittlichen Machine Learning-Algorithmen auch für „Box“, „Apple“, „Fox“ usw., da diese Algorithmen wissen, wie diese Wörter eindeutig anhand des Kontexts unterschieden werden. Die Markenerkennung funktioniert für alle unterstützten Sprachen. Klicken Sie hier, um das vollständige [Keynote-Video und den Index für Microsoft Build 2017 Day 2](https://www.videoindexer.ai/media/ed6ede78ad/) anzuzeigen.

Lesen Sie „Nächste Schritte“, um zu erfahren, wie Sie bei eigenen Marken vorgehen.

## <a name="next-steps"></a>Nächste Schritte

[Anpassen des Markenmodells mit APIs](customize-brands-model-with-api.md)

[Anpassen des Markenmodells über die Website](customize-brands-model-with-website.md)
