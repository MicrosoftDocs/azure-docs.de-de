---
title: 'Verwenden der Video Indexer-Website zum Anpassen eines Markenmodells: Azure'
titlesuffix: Azure Media Services
description: In diesem Artikel wird gezeigt, wie ein Markenmodell mit der Video Indexer-Website angepasst werden kann.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 721fde63aeae8704761b3c21f489dcad77cb89e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799614"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Anpassen eines Markenmodells mit der Video Indexer-Website

Video Indexer unterstützt die Markenerkennung aus Sprache und visuellem Text während der Indizierung und Neuindizierung von Video- und Audioinhalten. Das Feature zur Erkennung von Marken identifiziert Erwähnungen von Produkten, Diensten und Unternehmen, die von der Markendatenbank von Bing vorgeschlagen werden. Wenn Microsoft beispielsweise in einem Video- oder Audioinhalt erwähnt wird oder im visuellen Text in einem Video erscheint, erkennt Video Indexer dies als Marke im Inhalt. Ein benutzerdefiniertes Markenmodell ermöglicht das Auswählen, ob Video Indexer Marken aus der Bing-Markendatenbank erkennt, bestimmte Marken von der Erkennung ausschließt (im Wesentlichen eine Blockliste von Marken erstellt) und Marken einbezieht, die Teil Ihres Modells sein sollten, die möglicherweise nicht in der Bing-Markendatenbank enthalten sind (im Wesentlichen Erstellung einer Whitelist von Marken).

Eine ausführliche Übersicht finden Sie unter [Übersicht](customize-brands-model-overview.md).

Sie können die Video Indexer-Website zum Erstellen, Verwenden und Bearbeiten von benutzerdefinierten Markenmodellen verwenden, die in einem Video erkannt wurden, wie in diesem Thema beschrieben. Sie können auch die API verwenden. Dies wird unter [Anpassen von Markenmodellen mithilfe von APIs](customize-brands-model-with-api.md) beschrieben.

## <a name="edit-the-settings-of-the-brands-model"></a>Bearbeiten der Einstellungen des Markenmodells  

Sie haben die Möglichkeit festzulegen, ob Marken aus der Bing-Markendatenbank erkannt werden sollen oder nicht. Hierzu müssen Sie die Einstellungen des Markenmodells bearbeiten.

1. Navigieren Sie zur [Video Indexer](https://www.videoindexer.ai/)-Website, und melden Sie sich an.
2. Um ein Modell in Ihrem Konto anzupassen, klicken Sie in der oberen rechten Ecke der Seite auf die Schaltfläche **Content model customization** (Anpassung des Inhaltsmodells).
 
   ![Anpassen des Inhaltsmodells](./media/content-model-customization/content-model-customization.png) 
3. Um Marken zu bearbeiten, wählen Sie die Registerkarte **Marken** aus.

    ![Anpassen von Markenmodellen](./media/customize-brand-model/customize-brand-model.png)
4. Aktivieren Sie die Option **Von Bing vorgeschlagene Marken anzeigen**, wenn Sie möchten, dass Video Indexer die von Bing vorgeschlagenen Marken berücksichtigt. Lassen Sie die Option deaktiviert, wenn Sie nicht möchten, dass Video Indexer in Ihrem Inhalt von Bing vorgeschlagene Marken erkennt. 

## <a name="include-brands-in-the-model"></a>Einbeziehen von Marken in das Modell

Der Abschnitt **Marken einschließen** stellt benutzerdefinierte Marken dar, die Video Indexer erkennen soll, auch wenn sie nicht von Bing vorgeschlagen werden.  

### <a name="add-a-brand"></a>Hinzufügen einer Marke

1. Klicken Sie auf „+ Marke hinzufügen“.

    ![Anpassen von Markenmodellen](./media/customize-brand-model/add-brand.png)

    Geben Sie einen Namen (erforderlich), eine Kategorie (optional), eine Beschreibung (optional) und eine Verweis-URL (optional) an.
    Das Feld „Kategorie“ soll Sie beim Kennzeichnen Ihrer Marken unterstützen. Dieses Feld wird als *Tags* der Marke angezeigt, wenn die Video Indexer-APIs verwendet werden. Beispielsweise kann die Marke „Azure“ als „Cloud“ gekennzeichnet oder eingestuft werden.

    Das Feld für die Verweis-URL kann eine beliebige Referenzwebsite für die Marke sein, z.B. ein Link zur entsprechenden Wikipedia-Seite.
2. Klicken Sie auf „Marke hinzufügen“. Sie sehen dann, dass die Marke der Liste **Marken einschließen** hinzugefügt wurde.

### <a name="edit-a-brand"></a>Bearbeiten einer Marke

1. Klicken Sie auf das Stiftsymbol neben der Marke, die Sie bearbeiten möchten.

    Sie können die Kategorie, die Beschreibung oder die Verweis-URL einer Marke aktualisieren. Sie können den Namen einer Marke nicht ändern, weil Namen von Marken eindeutig sind. Wenn Sie den Markennamen ändern müssen, löschen Sie die gesamte Marke (siehe nächster Abschnitt,) und erstellen Sie dann eine neue Marke mit dem neuen Namen.
2. Klicken Sie auf die Schaltfläche **Aktualisieren**, um die Marke mit den neuen Informationen zu aktualisieren.

### <a name="delete-a-brand"></a>Löschen einer Marke

1. Klicken Sie auf das Papierkorbsymbol neben der Marke, die Sie löschen möchten.
2. Klicken Sie auf „Löschen“. Die Marke wird nicht mehr in der Liste *Marken einschließen* angezeigt.

## <a name="exclude-brands-from-the-model"></a>Ausschließen von Marken aus dem Modell

Der Abschnitt **Marken ausschließen** stellt die Marken dar, die Video Indexer nicht erkennen soll.

### <a name="add-a-brand"></a>Hinzufügen einer Marke

1. Klicken Sie auf „+ Marke hinzufügen“.

    Geben Sie einen Namen (erforderlich) und eine Kategorie (optional) an.
2. Klicken Sie auf „Marke hinzufügen“. Sie sehen dann, dass die Marke der Liste *Marken ausschließen* hinzugefügt wurde.

### <a name="edit-a-brand"></a>Bearbeiten einer Marke

1. Klicken Sie auf das Stiftsymbol neben der Marke, die Sie bearbeiten möchten.

    Sie können nur die Kategorie eine Marke aktualisieren. Sie können den Namen einer Marke nicht ändern, weil Namen von Marken eindeutig sind. Wenn Sie den Markennamen ändern müssen, löschen Sie die gesamte Marke (siehe nächster Abschnitt,) und erstellen Sie dann eine neue Marke mit dem neuen Namen.
2. Klicken Sie auf die Schaltfläche **Aktualisieren**, um die Marke mit den neuen Informationen zu aktualisieren.

### <a name="delete-a-brand"></a>Löschen einer Marke

1. Klicken Sie auf das Papierkorbsymbol neben der Marke, die Sie löschen möchten.
2. Klicken Sie auf „Löschen“. Die Marke wird nicht mehr in der Liste *Marken ausschließen* angezeigt.

## <a name="next-steps"></a>Nächste Schritte

[Anpassen des Markenmodells mit APIs](customize-brands-model-with-api.md)
