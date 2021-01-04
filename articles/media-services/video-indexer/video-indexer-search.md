---
title: Suchen nach bestimmten Momenten in Videos mit Video Indexer
titleSuffix: Azure Media Services
description: Erfahren Sie, wie Sie mit Video Indexer in Videos nach bestimmten Momenten suchen.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: a1ca7c677e5f1eb55d74cca45e757676674f303c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030535"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Suchen nach bestimmten Momenten in Videos mit Video Indexer

In diesem Thema erfahren Sie, wie Sie die Video Indexer-Website verwenden, um in Videos nach bestimmten Momenten zu suchen.

1. Navigieren Sie zur [Video Indexer](https://www.videoindexer.ai/)-Website, und melden Sie sich an.
1. Geben Sie Suchwörter an – die Suche wird in allen Videos in der Bibliothek Ihres Kontos durchgeführt. 

    Sie können **Filter** auswählen, um Ihre Suche zu filtern. Im folgenden Beispiel suchen wir nach dem Begriff „Microsoft“, der nur als Bildschirmtext (optische Zeichenerkennung) angezeigt wird.

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="Filter, nur Text":::
1. Klicken Sie auf **Suchen**, um das Ergebnis anzuzeigen.

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="Ergebnis der Videosuche":::

    Wenn Sie ein Ergebnis auswählen, gelangen Sie im Player im Video zum entsprechenden Moment.
1. Klicken Sie im Video auf **Wiedergeben**, oder wählen Sie eins der ursprünglichen Suchergebnisse aus, um die zusammengefassten Erkenntnisse aus dem Video anzuzeigen und zu durchsuchen. 

    Sie können die **Erkenntnisse** anzeigen, durchsuchen und bearbeiten. Wenn Sie eine Erkenntnis auswählen, springt der Player im Video zu genau diesem Moment.  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="Anzeigen, Durchsuchen und Bearbeiten der Erkenntnisse aus dem Video":::

    Wenn Sie das Video mithilfe von Video Indexer-Widgets einbetten, können Sie die Ansicht für Player/Erkenntnisse in Ihrer App anzeigen und Synchronisierungen durchführen. Weitere Informationen finden Sie unter [Einbetten von Video Indexer-Widgets in Ihre App](video-indexer-embed-widgets.md).
1. Klicken Sie auf die Registerkarte **Zeitachse**, um Transkripte anzuzeigen, zu durchsuchen und zu bearbeiten. 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="Anzeigen, Durchsuchen und Bearbeiten der Transkripte des Videos":::

    Zum Bearbeiten des Texts wählen Sie in der oberen rechten Ecke **Bearbeiten** aus und ändern den Text nach Bedarf. 

    Sie können die Transkripte auch übersetzen und herunterladen, indem Sie oben rechts die entsprechende Option auswählen. 

## <a name="embed-download-create-projects"></a>Einbetten, Herunterladen und Erstellen von Projekten

Sie können Ihr Video einbetten, indem Sie unter dem Video die Option **</>Einbetten** auswählen. Weitere Informationen finden Sie unter [Einbetten von visuellen Widgets in Ihre Anwendung](video-indexer-embed-widgets.md).

Sie können das Quellvideo, Erkenntnisse aus dem Video sowie Transkripte herunterladen, indem Sie unter dem Video auf **Download** klicken.

Sie können basierend auf bestimmten Textzeilen und Momenten einen Clip aus Ihrem Video erstellen, indem Sie auf **In Editor öffnen** klicken. Bearbeiten das Video, und speichern Sie das Projekt. Weitere Informationen finden Sie unter [Verwenden des Video Indexer-Editors zum Erstellen von Projekten](use-editor-create-project.md).

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="Einbetten, Herunterladen und Erstellen von Projekten aus dem Video":::

## <a name="next-steps"></a>Nächste Schritte

[Verarbeiten von Inhalten mit der Video Indexer-REST-API](video-indexer-use-apis.md)
