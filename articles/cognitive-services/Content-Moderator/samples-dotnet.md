---
title: Codebeispiele – Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Azure Cognitive Services Content Moderator über das SDK in Ihren .NET-Anwendungen verwenden.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: a746a4baf172bcb6728c06f2a31b460139377e46
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912921"
---
# <a name="content-moderator-net-sdk-samples"></a>Content Moderator: .NET-SDK-Beispiele

Die folgende Liste enthält Links zu den Codebeispielen, die mit dem Azure Content Moderator SDK für .NET erstellt wurden.

## <a name="moderation"></a>Moderation

- **Bildmoderation:** [Werten Sie Bilder für nicht jugendfreie Inhalte, Texte und Gesichter aus.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs) Weitere Informationen finden Sie im [Schnellstart zum .NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Benutzerdefinierte Bilder:** [Moderieren Sie Bilder mit benutzerdefinierten Bildlisten.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs) Weitere Informationen finden Sie im [Schnellstart zum .NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

> [!NOTE]
> Die Obergrenze liegt bei **fünf Bildlisten** , wobei jede Liste **max. 10.000 Bilder** enthalten kann.
>

- **Textmoderation:** [Bildschirmtext für Obszönitäten und personenbezogene Daten](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Weitere Informationen finden Sie im [Schnellstart zum .NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Benutzerdefinierte Benennungen:** [Moderieren Sie Benennungen mit benutzerdefinierten Benennungslisten.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs) Weitere Informationen finden Sie im [Schnellstart zum .NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

> [!NOTE]
> Die Obergrenze liegt bei **fünf Benennungslisten** , wobei jede Liste **max. 10.000 Benennungen** enthalten kann.
>

- **Videomoderation:** [Prüfen Sie ein Video auf nicht jugendfreie Inhalte, und rufen Sie die Ergebnisse ab.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs) Weitere Informationen finden Sie im [Schnellstart](video-moderation-api.md).

## <a name="review"></a>Überprüfung

- **Bildaufträge:** [Starten Sie einen Moderationsauftrag, der Überprüfungen durchführt und erstellt.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs) Weitere Informationen finden Sie im [Schnellstart](moderation-jobs-quickstart-dotnet.md).
- **Bildüberprüfungen:** [Erstellen Sie Überprüfungen für HITL-Simulationen (Human-in-the-Loop).](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs) Weitere Informationen finden Sie im [Schnellstart](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Videoüberprüfungen:** [Erstellen Sie Videoüberprüfungen für HITL-Simulationen (Human-in-the-Loop).](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs) Weitere Informationen finden Sie im [Schnellstart](video-reviews-quickstart-dotnet.md).
- **Überprüfungen von Videotranskripts:** [Erstellen Sie Überprüfungen von Videotranskripts für HITL-Simulationen (Human-in-the-Loop).](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) Weitere Informationen finden Sie im [Schnellstart](video-reviews-quickstart-dotnet.md).

Alle .NET-Beispiele finden Sie auf [GitHub unter den .NET-Beispielen zu Content Moderator](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).