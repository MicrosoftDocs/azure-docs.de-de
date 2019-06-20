---
title: 'Verwenden der Video Indexer-Website zum Anpassen eines Sprachmodells: Azure'
titlesuffix: Azure Media Services
description: In diesem Artikel wird gezeigt, wie ein Sprachmodell mit der Video Indexer-Website angepasst werden kann.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f98cdcab2d108f8dd9d40e3770498ad17b2a8a88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799620"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Anpassen eines Sprachmodells mit der Video Indexer-Website

Mit Video Indexer können Sie benutzerdefinierte Sprachmodelle erstellen, um die Spracherkennung durch Hochladen von Anpassungstexten anzupassen, insbesondere von Texten aus dem Bereich, an dessen Vokabular sich die Engine anpassen soll. Sobald Sie Ihr Modell trainiert haben, werden neue Wörter, die im Anpassungstext vorkommen, erkannt. 

Eine detaillierte Übersicht und bewährte Methoden für benutzerdefinierte Sprachmodelle finden Sie unter [Anpassen eines Sprachmodells mit Video Indexer](customize-language-model-overview.md).

Sie können die Video Indexer-Website verwenden, um benutzerdefinierte Sprachmodelle in Ihrem Konto zu erstellen und zu bearbeiten, wie in diesem Thema beschrieben. Sie können auch die API verwenden. Dies wird unter [Anpassen von Sprachmodellen mithilfe von APIs](customize-language-model-with-api.md) beschrieben.

## <a name="create-a-language-model"></a>Erstellen eines Sprachmodells

1. Navigieren Sie zur [Video Indexer](https://www.videoindexer.ai/)-Website, und melden Sie sich an.
2. Um ein Modell in Ihrem Konto anzupassen, klicken Sie in der oberen rechten Ecke der Seite auf die Schaltfläche **Content model customization** (Anpassung des Inhaltsmodells).

   ![Anpassen eines Inhaltsmodells](./media/content-model-customization/content-model-customization.png)

3. Wählen Sie die Registerkarte **Sprache** aus.

    Eine Liste der unterstützten Sprachen wird angezeigt. 

    ![Anpassen eines Sprachmodells](./media/customize-language-model/customize-language-model.png)

4. Nach dem Auswählen der gewünschten Sprache klicken Sie auf **Modell hinzufügen**.
5. Geben Sie den Namen für das Sprachmodell ein, und drücken Sie die Eingabetaste.

    Dadurch wird das Modell erstellt, und das Hochladen von Textdateien in das Modell wird möglich.
6. Um eine Textdatei hinzuzufügen, klicken Sie auf **Datei hinzufügen**. Der Datei-Explorer wird geöffnet.

7. Navigieren Sie zur Textdatei, und wählen Sie diese aus. Sie können mehrere Textdateien einem Sprachmodell hinzufügen.

    Sie können auch eine Textdatei hinzufügen, indem Sie auf die Schaltfläche **...** auf der rechten Seite des Sprachmodells klicken und **Add file** (Datei hinzufügen) auswählen.
8. Nachdem Sie die Textdateien hochgeladen haben, wählen Sie die grüne Option **Train** (Trainieren) aus.

    ![Trainieren eines Sprachmodells](./media/customize-language-model/train-model.png)

Der Trainingsprozess kann einige Minuten dauern. Wenn der Trainingsprozess abgeschlossen ist, wird **Trained** (Trainiert) neben dem Modell angezeigt. Sie können für die Datei eine Vorschau anzeigen, sie herunterladen und aus dem Modell löschen.

![Trainiertes Sprachmodell](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Verwenden eines Sprachmodells für ein neues Video

Um Ihr Sprachmodell für ein neues Video zu verwenden, gehen Sie folgendermaßen vor:

* Klicken Sie auf die Schaltfläche **Upload** (Hochladen) oben auf der Seite. 

    ![Hochladen](./media/customize-language-model/upload.png)
* Legen Sie Ihre Audio- oder Videodatei in dem Kreis ab, oder suchen Sie nach Ihrer Datei.

    ![Hochladen](./media/customize-language-model/upload2.png)

So können Sie die Ausgangssprache des Videos über die Option **Video source language** (Ausgangssprache Video) auswählen. Klicken Sie auf die Dropdownliste, und wählen Sie daraus ein von Ihnen erstelltes Sprachmodell aus. Ihnen sollten dann die Sprache Ihres Sprachmodells sowie der von Ihnen in Klammern festgelegte Name angezeigt werden.

Wählen Sie unten auf der Seite die Option **Upload** (Hochladen) aus, und Ihr neues Video wird mithilfe des Sprachmodells indiziert.

### <a name="using-a-language-model-to-reindex"></a>Neuindizieren mithilfe eines Sprachmodells

Um Ihr Sprachmodell zum Neuindizieren eines Videos in Ihrer Sammlung zu verwenden, navigieren Sie zu Ihren **Account videos** (Videos in Ihrem Konto) auf der [Video Indexer](https://www.videoindexer.ai/)-Homepage, und zeigen Sie auf den Namen des Videos, welches Sie erneut indizieren möchten.

Sie sehen Optionen zum Bearbeiten, Löschen und Neuindizieren Ihres Videos. Wählen Sie die Option zum Neuindizieren Ihres Videos aus.

![Neuindizieren](./media/customize-language-model/reindex1.png)

So können Sie über die Option **Video source language** (Ausgangssprache Video) die Ausgangssprache des Videos auswählen, mit der Sie Ihr Video erneut indizieren möchten. Klicken Sie auf die Dropdownliste, und wählen Sie daraus ein von Ihnen erstelltes Sprachmodell aus. Ihnen sollten dann die Sprache Ihres Sprachmodells sowie der von Ihnen in Klammern festgelegte Name angezeigt werden.

![Neuindizieren](./media/customize-language-model/reindex.png)

Klicken Sie auf die Schaltfläche **Re-index** (Neuindizieren), und Ihr Video wird mithilfe Ihres Sprachmodells erneut indiziert.

## <a name="edit-a-language-model"></a>Bearbeiten eines Sprachmodells

Sie können ein Sprachmodell bearbeiten durch Ändern seines Namens oder durch Hinzufügen und Löschen von Dateien.

Wenn Sie dem Sprachmodell Dateien hinzufügen oder daraus löschen, müssen Sie das Modell neu trainieren, indem Sie die grüne Option **Train** (Trainieren) auswählen.

### <a name="rename-the-language-model"></a>Umbenennen eines Sprachmodells

Sie können den Namen des Sprachmodells ändern, indem Sie auf die Schaltfläche **...** auf der rechten Seite des Sprachmodells klicken und die Option **Rename** (Umbenennen) auswählen. 

Geben Sie den neuen Namen ein, und drücken Sie die Eingabetaste.

### <a name="add-files"></a>Dateien hinzufügen

Klicken Sie auf **Add file** (Datei hinzufügen), um eine Textdatei hinzuzufügen. Der Datei-Explorer wird geöffnet.

Navigieren Sie zur Textdatei, und wählen Sie diese aus. Sie können mehrere Textdateien einem Sprachmodell hinzufügen.

Sie können auch eine Textdatei hinzufügen, indem Sie auf die Schaltfläche **...** auf der rechten Seite des Sprachmodells klicken und **Add file** (Datei hinzufügen) auswählen.

### <a name="delete-files"></a>Löschen von Dateien

Um eine Datei aus dem Sprachmodell zu löschen, klicken Sie auf die Schaltfläche **...** auf der rechten Seite der Textdatei, und wählen Sie **Delete** (Löschen) aus. Dies öffnet ein neues Fenster mit der Mitteilung, dass die Löschung nicht rückgängig gemacht werden kann. Wählen Sie die Option **Delete** (Löschen) in dem neuen Fenster aus.

Durch diese Aktion wird die Datei vollständig aus dem Sprachmodell entfernt.

## <a name="delete-a-language-model"></a>Löschen eines Sprachmodells

Um ein Sprachmodell aus Ihrem Konto zu löschen, klicken Sie auf die Schaltfläche **...** auf der rechten Seite des Sprachmodells, und wählen Sie **Delete** (Löschen) aus.

Dies öffnet ein neues Fenster mit der Mitteilung, dass die Löschung nicht rückgängig gemacht werden kann. Wählen Sie die Option **Delete** (Löschen) in dem neuen Fenster aus.

Durch diese Aktion wird das Sprachmodell vollständig aus Ihrem Konto entfernt. Jedes Video, das das gelöschte Sprachmodell verwendet hat, behält den gleichen Index bei, bis Sie das Video erneut indizieren. Wenn Sie das Video erneut indizieren, können Sie dem Video ein neues Sprachmodell zuweisen. Andernfalls verwendet Video Indexer das Standardmodell, um das Video erneut zu indizieren. 

## <a name="customize-language-models-by-correcting-transcripts"></a>Anpassen von Sprachmodellen durch Korrigieren von Transkripten

Video Indexer unterstützt die automatische Anpassung von Sprachmodellen basierend auf den tatsächlichen Korrekturen, die Benutzer an den Transkriptionen ihrer Videos vornehmen.

1. Um Korrekturen an einem Transkript vorzunehmen, öffnen Sie das Video, das Sie bearbeiten möchten, aus Ihren Kontovideos. Wählen Sie die Registerkarte **Zeitachse**.

    ![Anpassen eines Sprachmodells](./media/customize-language-model/timeline.png)
1. Klicken Sie auf das Stiftsymbol, um das Transkript Ihrer Transkription zu bearbeiten. 

    ![Anpassen eines Sprachmodells](./media/customize-language-model/edits.png)

    Video Indexer erfasst alle Zeilen, die von Ihnen in der Transkription Ihres Videos korrigiert werden, und fügt sie automatisch zu einer Textdatei namens „Aus Transkriptbearbeitungen“ hinzu. Diese Bearbeitungen werden verwendet, um das spezifische Sprachmodell, mit dem dieses Video indiziert wurde, neu zu trainieren. 
    
    Wenn Sie bei der Indizierung dieses Videos kein Sprachmodell angegeben haben, werden alle Bearbeitungen für dieses Video in einem Standardsprachenmodell namens Kontoanpassungen innerhalb der erkannten Sprache des Videos gespeichert. 
    
    Falls mehrere Änderungen an derselben Zeile vorgenommen wurden, wird nur die letzte Version der korrigierten Zeile für die Aktualisierung des Sprachmodells verwendet.  
    
    > [!NOTE]
    > Für die Anpassung werden nur Textkorrekturen verwendet. Das bedeutet, dass Korrekturen, die keine eigentlichen Wörter beinhalten (z.B. Satzzeichen oder Leerzeichen), nicht berücksichtigt werden. 
    
1. Transkriptkorrekturen werden auf der Registerkarte „Sprache“ auf der Seite zur Anpassung des Inhaltsmodells angezeigt.

    ![Anpassen eines Sprachmodells](./media/customize-language-model/customize.png)

   Um die Datei „Aus Transkriptbearbeitungen“ für jedes Ihrer Sprachmodelle anzusehen, klicken Sie darauf, um sie zu öffnen. 

    ![Aus Transkriptbearbeitungen](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Nächste Schritte

[Customize language model using APIs (Anpassen eines Sprachmodells mithilfe von APIs)](customize-language-model-with-api.md)
