---
title: 'Verwenden der Video Indexer-Website zum Anpassen eines Personenmodells: Azure'
titlesuffix: Azure Media Services
description: In diesem Artikel wird gezeigt, wie ein Personenmodell mit der Video Indexer-Website angepasst werden kann.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 37e556ca458a5b0a171664e089d39cfb448d5f96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65800117"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Anpassen eines Personenmodells mit der Video Indexer-Website

Video Indexer unterstützt Erkennung von Prominenten für Videoinhalte. Die Funktion zur Erkennung von Prominenten umfasst ungefähr eine Million Gesichter, die auf häufig angeforderten Datenquellen wie IMDB, Wikipedia und den wichtigsten LinkedIn-Influencern basieren. Eine ausführliche Übersicht finden Sie unter [Anpassen eines Personenmodells in Video Indexer](customize-person-model-overview.md).

Sie können die Video Indexer-Website verwenden, um Gesichter zu bearbeiten, die in einem Video erkannt wurden. Dies wird in diesem Artikel beschrieben. Sie können auch die API verwenden. Dies wird unter [Anpassen von Personenmodellen mithilfe von APIs](customize-person-model-with-api.md) beschrieben.

## <a name="central-management-of-person-models-in-your-account"></a>Zentrale Verwaltung von Personenmodellen in Ihrem Konto

1. Um die Personenmodelle in Ihrem Konto anzuzeigen, zu bearbeiten und zu löschen, gehen Sie zur Video Indexer-Website, und melden Sie sich an.
2. Klicken Sie in der oberen rechten Ecke der Seite auf die Schaltfläche „Anpassung des Inhaltsmodells“.

    ![Anpassung des Inhaltsmodells](./media/customize-face-model/content-model-customization.png)
3. Wählen Sie die Registerkarte „Personen“.

    Es wird das standardmäßig Personenmodell für Ihr Konto angezeigt. Das standardmäßige Personenmodell enthält alle Gesichter, die Sie in den Einblicken Ihrer Videos bearbeitet oder geändert haben, für die Sie während der Indizierung kein benutzerdefiniertes Personenmodell angegeben haben. 

    Wenn Sie andere Personenmodelle erstellt haben, werden diese auf dieser Seite aufgeführt. 

    ![Anpassung des Inhaltsmodells](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Erstellen eines neuen Personenmodells

1. Klicken Sie auf die Schaltfläche **+ Modell hinzufügen**.

    ![Neue Person hinzufügen](./media/customize-face-model/add-new-person.png)
2. Geben Sie den Namen des Modells ein, und klicken Sie auf die Schaltfläche neben dem Namen.

    ![Neue Person hinzufügen](./media/customize-face-model/add-new-person2.png)

    Sie haben ein neues Personenmodell erstellt. Jetzt können Sie Gesichter zum neuen Personenmodell hinzufügen.
3. Klicken Sie auf die Menüschaltfläche „Liste“, und wählen Sie **Person hinzufügen**.

    ![Neue Person hinzufügen](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>Hinzufügen einer neuen Person zu einem Personenmodell

> [!NOTE]
> Mit dem Video Indexer können Sie mehrere Personen mit dem gleichen Namen in einem Personenmodell hinzufügen. Es wird jedoch empfohlen, dass Sie jeder Person in Ihrem Modell aus Gründen der Benutzerfreundlichkeit und Übersichtlichkeit eindeutige Namen geben.

1. Um ein neues Gesicht zu einem Personenmodell hinzuzufügen, klicken Sie auf die Menüschaltfläche „Liste“ neben dem Personenmodell, dem Sie das Gesicht hinzufügen möchten.
1. Klicken Sie im Menü auf **+ Person hinzufügen**.

    ![Neues Gesicht zu einer Person hinzufügen](./media/customize-face-model/add-new-face.png)
 
    In einem Popupfenster werden Sie zum Ausfüllen der Personendetails aufgefordert. Geben Sie den Namen der Person ein, und klicken Sie auf das Häkchen.

    ![Neues Gesicht zu einer Person hinzufügen](./media/customize-face-model/add-new-face2.png)
 
Sie können dann aus Ihrem Datei-Explorer wählen oder die Gesichtsbilder des Gesichts per Drag & Drop verschieben. Video Indexer akzeptiert alle standardmäßigen Bilddateitypen (z.B: JPG, PNG und mehr).

Der Video Indexer sollte in der Lage sein, das Vorkommen dieser Person in zukünftigen Videos, die Sie indizieren, und den aktuellen Videos, die Sie bereits indiziert hatten, anhand des Personenmodells zu erkennen, zu dem Sie dieses neue Gesicht hinzugefügt haben. Die Erkennung der Person in Ihren aktuellen Videos kann einige Zeit in Anspruch nehmen, da es sich um einen Batchprozess handelt.


## <a name="rename-a-person-model"></a>Umbenennen eines Personenmodells

Sie können jedes Personenmodell in Ihrem Konto umbenennen, einschließlich des standardmäßigen Personenmodells. Selbst wenn Sie Ihr standardmäßiges Personenmodell umbenennen, dient es weiterhin als das standardmäßige Personenmodell in Ihrem Konto.

1. Klicken Sie auf die Menüschaltfläche „Liste“ neben dem Personenmodell, das Sie umbenennen möchten.
2. Klicken Sie im Menü auf die Option **Umbenennen**.

    ![Umbenennen einer Person](./media/customize-face-model/rename-person.png)
3. Klicken Sie auf den aktuellen Namen des Modells, und geben Sie den neuen Namen ein.

    ![Umbenennen einer Person](./media/customize-face-model/rename-person2.png)
4. Klicken Sie auf das Häkchen, damit Ihr Modell umbenannt wird.

## <a name="delete-a-person-model"></a>Löschen eines Personenmodells

Sie können jedes Personenmodell löschen, das Sie in Ihrem Konto erstellt haben. Ihr standardmäßiges Personenmodell können Sie jedoch nicht löschen.

1. Klicken Sie im Menü auf **Löschen**.

    ![Löschen einer Person](./media/customize-face-model/delete-person.png)
    
    Ein Popupfenster wird eingeblendet und informiert Sie, dass diese Aktion das Personenmodell und alle darin enthaltenen Personen und Dateien löscht. Diese Aktion kann nicht rückgängig gemacht werden. 

    ![Löschen einer Person](./media/customize-face-model/delete-person2.png)
1. Wenn Sie sicher sind, klicken Sie erneut auf „Löschen“.

> [!NOTE]
> Die vorhandenen Videos, die mit diesem (jetzt gelöschten) Personenmodell indiziert wurden, unterstützen nicht die Möglichkeit, die Namen der Gesichter, die im Video erscheinen, zu aktualisieren. Sie können die Namen der Gesichter in diesen Videos erst bearbeiten, nachdem Sie sie mit einem anderen Personenmodell neu indiziert haben. Wenn Sie die Neuindizierung ohne Angabe eines Personenmodells durchführen, wird das Standardmodell verwendet. 

## <a name="manage-existing-people-in-a-person-model"></a>Verwalten von vorhandenen Personen in einem Personenmodell

Um sich den Inhalt eines Ihrer Personenmodelle anzusehen, klicken Sie auf den Pfeil neben dem Namen des Personenmodells.
Das Dropdownmenü zeigt Ihnen alle Personen in diesem bestimmten Personenmodell an. Wenn Sie neben den Personen auf die Menüschaltfläche „Liste“ klicken, werden Optionen zum Verwalten, Umbenennen und Löschen angezeigt.  

![Neues Gesicht zu einer Person hinzufügen](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Umbenennen einer Person

1. Um eine Person in Ihrem Personenmodell umzubenennen, klicken Sie auf die Menüschaltfläche „Liste“, und wählen Sie **Umbenennen**.
1. Klicken Sie auf den aktuellen Namen der Person, und geben Sie den neuen Namen ein.
1. Klicken Sie auf das Häkchen, und die Person wird umbenannt.

### <a name="delete-a-person"></a>Löschen einer Person

1. Um eine Person aus Ihrem Personenmodell zu löschen, klicken Sie auf die Menüschaltfläche „Liste“, und wählen Sie **Löschen**.
1. Ein Popupfenster zeigt Ihnen an, dass diese Aktion die Person löscht und dass diese Aktion nicht rückgängig gemacht werden kann.
1. Klicken Sie erneut auf „Löschen“. Dadurch wir die Person aus dem Personenmodell entfernt.

### <a name="manage-a-person"></a>Verwalten einer Person

Wenn Sie auf **Verwalten** klicken, sehen Sie alle Gesichter, mit denen dieses Personenmodell trainiert wird. Diese Gesichter stammen von allen Vorkommen dieser Person in Videos, die dieses Personenmodell verwenden, oder von Bildern, die Sie manuell hochgeladen haben. 

Sie können der Person weitere Gesichter hinzufügen, indem Sie auf „Bilder hinzufügen“ klicken.

Sie können den Verwaltungsbereich verwenden, um die Person umzubenennen und die Person aus dem Personenmodell zu löschen.

## <a name="use-a-person-model-to-index-a-video"></a>Verwenden eines Personenmodells zum Indizieren eines Videos

Sie können ein Personenmodell verwenden, um Ihr neues Video zu indizieren, indem Sie das Personenmodell während des Hochladens des Videos zuweisen.

Um Ihr Personenmodell für ein neues Video zu verwenden, gehen Sie folgendermaßen vor:

1. Klicken Sie auf die Schaltfläche **Hochladen** oben auf der Seite.

    ![Hochladen](./media/customize-face-model/upload.png)
1. Legen Sie Ihre oder Videodatei in dem Kreis ab, oder suchen Sie nach Ihrer Datei.
1. Klicken Sie auf den Pfeil „Erweiterte Optionen“.

    ![Hochladen](./media/customize-face-model/upload2.png)
1. Klicken Sie auf die Dropdownliste, und wählen Sie das von Ihnen erstellte Personenmodell aus.

    ![Hochladen](./media/customize-face-model/upload3.png)
1. Wählen Sie unten auf der Seite die Option „Hochladen“ aus, und Ihr neues Video wird mithilfe des Personenmodells indiziert.

Wenn Sie beim Hochladen kein Personenmodell angeben, indiziert der Video Indexer das Video anhand des standardmäßigen Personenmodells in Ihrem Konto.

## <a name="use-a-person-model-to-reindex-a-video"></a>Verwenden eines Personenmodells zum Neuindizieren eines Videos 

Um Ihr Personenmodell zum Neuindizieren eines Videos in Ihrer Sammlung zu verwenden, navigieren Sie zu Ihren Videos in Ihrem Konto auf der Video Indexer-Homepage, und zeigen Sie auf den Namen des Videos, welches Sie erneut indizieren möchten.

Sie sehen Optionen zum Bearbeiten, Löschen und Neuindizieren Ihres Videos. 

1. Klicken Sie auf die Option zum Neuindizieren Ihres Videos.

    ![Neuindizieren](./media/customize-face-model/reindex.png)

    Sie können nun das Personenmodell auswählen, mit dem Sie Ihr Video neu indizieren möchten.
1. Klicken Sie auf die Dropdownliste, und wählen Sie das Personenmodell aus, das Sie verwenden möchten. 

    ![Neuindizieren](./media/customize-face-model/reindex2.png)

1. Klicken Sie auf die Schaltfläche **Neuindizieren**, und Ihr Video wird mithilfe Ihres Personenmodell erneut indiziert.

Alle neuen Bearbeitungen, die Sie an den Gesichtern vornehmen, die in dem Video erkannt wurden, das Sie gerade neu indiziert haben, werden in dem Personenmodell gespeichert, mit dem Sie das Video neu indiziert haben.

## <a name="managing-people-in-your-videos"></a>Verwalten von Personen in Ihrer Videos

Sie können die erkannten Gesichter und Personen, die in den Videos, die Sie indizieren, erkannt wurden, verwalten, indem Sie Gesichter bearbeiten und löschen.

Das Löschen eines Gesichts, entfernt ein bestimmtes Gesicht aus den Informationen des Videos.

Wenn Sie ein Gesicht bearbeiten, benennen Sie ein Gesicht um, das erfasst und möglicherweise in Ihrem Video erkannt wird. Wenn Sie ein Gesicht in Ihrem Video bearbeiten, wird dieser Name als Personeneintrag im Personenmodell gespeichert, das dem Video beim Hochladen und Indizieren zugewiesen wurde.

Wenn Sie dem Video beim Hochladen kein Personenmodell zuweisen, wird Ihre Bearbeitung im standardmäßigen Personenmodell Ihres Kontos gespeichert.

### <a name="edit-a-face"></a>Bearbeiten eines Gesichts


> [!NOTE]
> Wenn ein Personenmodell zwei oder mehr verschiedene Personen mit dem gleichen Namen enthält, können Sie diesen Namen nicht in den Videos markieren, die dieses Personenmodell verwenden. Sie können nur Änderungen an Personen vornehmen, die diesen Namen auf der Registerkarte „Personen“ auf der Seite zur Anpassung des Inhaltsmodells im Video Indexer teilen. Aus diesem Grund wird empfohlen, dass Sie jeder Person in Ihrem Personenmodell eindeutige Namen geben.

1. Navigieren Sie zur Video Indexer-Website, und melden Sie sich an.
1. Suchen Sie nach einem Video, das Sie in Ihrem Konto anzeigen und bearbeiten möchten.
1. Um ein Gesicht in Ihrem Video zu bearbeiten, wechseln Sie zur Registerkarte „Erkenntnisse“, und klicken Sie oben rechts im Fenster auf das Stiftsymbol.

    ![Bearbeiten eines Gesichts in Ihrem Video](./media/customize-face-model/edit-face.png)
1. Klicken Sie auf eines der erkannten Gesichter, und ändern Sie deren Namen von "Unbekannt #X" (oder dem Namen, der dem Gesicht zuvor zugewiesen wurde). 
1. Nachdem Sie den neuen Namen eingegeben haben, klicken Sie neben dem neuen Namen auf das Prüfsymbol. Dadurch wird der neue Name gespeichert und alle Vorkommen dieses Gesichts in Ihren anderen aktuellen Videos sowie in zukünftigen Videos, die Sie hochladen, erkannt und benannt. Die Erkennung des Gesichts in Ihren anderen aktuellen Videos kann einige Zeit in Anspruch nehmen, da es sich um einen Batchprozess handelt.

Wenn Sie ein Gesicht mit dem Namen einer vorhandenen Person im Personenmodell benennen, das das Video verwendet, werden die erkannten Gesichtsbilder aus diesem Video dieser Person mit dem, was bereits im Modell existiert, zusammengeführt. Wenn Sie ein Gesicht mit einem völlig neuen Namen benennen, wird ein neuer Personeneintrag in dem Personenmodell erstellt, das das Video verwendet. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Löschen eines Gesichts

Um ein erkanntes Gesicht in Ihrem Video zu löschen, wechseln Sie zur Registerkarte „Erkenntnisse“, und klicken Sie oben rechts im Bereich auf das Stiftsymbol. Klicken Sie unter dem Namen des Gesichts auf die Option „Löschen“. Dadurch wird das erkannte Gesicht aus dem Video entfernt. Das Gesicht der Person wird in den anderen Videos, in denen es vorkommt, weiterhin erkannt. Sie können das Gesicht aber auch aus diesen Videos löschen, nachdem sie indiziert wurden. Die Person, wenn sie benannt wurde, bleibt auch im Personenmodell bestehen, das zur Indizierung des Videos verwendet wurde, aus dem Sie das Gesicht gelöscht haben, es sei denn, Sie löschen die Person ausdrücklich aus dem Personenmodell.

![Löschen eines Gesichts in Ihrem Video](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>Nächste Schritte

[Anpassen des Personenmodells mit APIs](customize-person-model-with-api.md)
