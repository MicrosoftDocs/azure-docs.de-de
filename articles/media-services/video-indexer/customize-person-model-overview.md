---
title: 'Anpassen eines Personenmodells in Video Indexer: Azure'
titlesuffix: Azure Media Services
description: Dieser Artikel gibt einen Überblick darüber, was ein Personenmodell in Video Indexer ist und wie Sie es anpassen können.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: c74b913fc3ac35039d914fc97c9c438d2e4a3069
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799441"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Anpassen eines Personenmodells in Video Indexer

Video Indexer unterstützt die Erkennung von Prominenten in Ihren Videos. Die Funktion zur Erkennung von Prominenten umfasst ungefähr eine Million Gesichter, die auf häufig angeforderten Datenquellen wie IMDB, Wikipedia und den wichtigsten LinkedIn-Influencern basieren. Von Video Indexer nicht erkannte Gesichter werden weiterhin erkannt, bleiben aber unbenannt. Kunden können benutzerdefinierte Personenmodelle erstellen und Video Indexer in die Lage versetzen, Gesichter zu erkennen, die standardmäßig nicht erkannt werden. Kunden können diese Personenmodelle erstellen, indem sie den Namen einer Person mit Bilddateien des Gesichts der Person kombinieren.  

Wenn Ihr Konto unterschiedliche Anwendungsfälle abdeckt, können Sie davon profitieren, dass Sie mehrere Personenmodelle pro Konto erstellen können. Wenn der Inhalt in Ihrem Konto z. B. in verschiedene Kanäle sortiert werden soll, möchten Sie vielleicht für jeden Kanal ein eigenes Personenmodell erstellen. 

> [!NOTE]
> Jedes Personenmodell unterstützt bis zu 1 Million Personen und jedes Konto hat ein Limit von 50 Personenmodellen. 

Sobald ein Modell erstellt wurde, können Sie es verwenden, indem Sie die Modell-ID eines bestimmten Personenmodells beim Hochladen/Indizieren oder erneuten Indizieren eines Videos angeben. Beim Trainieren eines neuen Gesichts für ein Video wird das bestimmte benutzerdefinierte Modell, dem das Video zugeordnet war, aktualisiert. 

Wenn Sie keine Unterstützung für Mehrpersonenmodelle benötigen, weisen Sie Ihrem Video beim Hochladen/Indizieren oder erneuten Indizieren keine Personenmodell-ID zu. In diesem Fall verwendet Video Indexer das Standardpersonenmodell in Ihrem Konto. 

Sie können die Video Indexer-Website verwenden, um Gesichter zu bearbeiten, die in einem Video erkannt wurden, und um mehrere benutzerdefinierte Personenmodelle in Ihrem Konto zu verwalten, wie im Thema [Anpassen eines Personenmodells mithilfe eines Website](customize-person-model-with-website.md) beschrieben. Sie können auch die API verwenden. Dies wird unter  [Anpassen von Personenmodellen mithilfe von APIs](customize-person-model-with-api.md) beschrieben.