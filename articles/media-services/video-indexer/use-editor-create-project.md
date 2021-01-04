---
title: Verwenden des Video Indexer-Editors zum Erstellen von Projekten
titleSuffix: Azure Media Services
description: In diesem Thema wird veranschaulicht, wie Sie den Video Indexer-Editor zum Erstellen von Projekten verwenden.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 28186a7dcced47a42b3249f1f74b13e969b41978
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041357"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Verwenden des Video Indexer-Editors zum Erstellen von Projekten

Die Video Indexer-Website ermöglicht es Ihnen, die umfassenden Erkenntnisse Ihres Videos für Folgendes zu nutzen: die richtigen Medieninhalte zu finden, die für Sie interessanten Teile zu finden und die Ergebnisse zum Erstellen eines völlig neuen Projekts zu nutzen. Nach der Erstellung kann das Projekt gerendert und von Video Indexer heruntergeladen sowie in eigenen Bearbeitungsanwendungen oder Downstream-Workflows verwendet werden.

Einige Szenarien, in denen Sie dieses Feature als hilfreich empfinden können, sind: 

* Erstellung von Filmhighlights für Trailer.
* Verwendung alter Clips von Videos in Nachrichtensendungen.
* Erstellung von kürzeren Inhalten für soziale Medien.

In diesem Artikel wird gezeigt, wie Sie ein Projekt von Grund auf neu erstellen und auch wie Sie ein Projekt aus einem Video in Ihrem Konto erstellen.

## <a name="create-new-project-and-manage-videos"></a>Neues Projekt erstellen und Videos verwalten

1. Navigieren Sie zur [Video Indexer](https://www.videoindexer.ai/)-Website, und melden Sie sich an.
1. Wählen Sie die Registerkarte **Projekte** aus. Wenn Sie bereits Projekte erstellt haben, werden hier alle Ihre anderen Projekte angezeigt.
1. Klicken Sie auf **Neues Projekt erstellen**.  

    ![Screenshot der Seite „Video Indexer“ mit ausgewählter Registerkarte „Projekte“](./media/video-indexer-view-edit/new-project.png)
1. Weisen Sie Ihrem Projekt einen Namen zu, indem Sie auf das Stiftsymbol klicken. Ersetzen Sie den Text „Unbenanntes Projekt“ durch Ihren Projektnamen, und klicken Sie auf das Häkchen.

    ![Neues Projekt](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Videos zum Projekt hinzufügen

> [!NOTE]
> Derzeit dürfen Projekte nur Videos enthalten, die in derselben Sprache indiziert sind. Nachdem Sie ein Video in einer Sprache ausgewählt haben, können Sie keine Videos in Ihrem Konto hinzufügen, die in einer anderen Sprache vorliegen.

1. Fügen Sie in diesem Projekt Videos hinzu, mit denen Sie arbeiten möchten, indem Sie **Videos hinzufügen** auswählen.

    Es werden alle Videos in Ihrem Konto und ein Suchfeld mit dem Text „Nach Text, Schlüsselwörtern oder sichtbarem Inhalt suchen“ angezeigt. So suchen Sie nach Videos, die eine bestimmte Person, eine bestimmtes Bezeichnung, eine bestimmte Marke, ein bestimmtes Schlüsselwort oder ein bestimmtes Vorkommen im Transkript und in der OCR enthalten
    
    Im folgenden Bild suchen wir z. B. nach Videos, die „GitHub“ erwähnen.
    
    ![Screenshot: Suche nach Videos, in denen GitHub erwähnt wird, mit zwei Ergebnissen](./media/video-indexer-view-edit/github.png)

    Sie können Ihre Ergebnisse weiter filtern, indem Sie **Ergebnisse filtern** auswählen. Sie können filtern, um Videos anzuzeigen, in denen sich eine bestimmte Person befindet, oder um festzulegen, dass Sie nur Videoergebnisse anzeigen möchten, die in einer bestimmten Sprache vorliegen oder einen bestimmten Besitzer aufweisen. <br/> Sie können auch den Bereich der Abfrage angeben. Wenn Sie z. B. „GitHub“ in der OCR suchen möchten, wählen Sie **Sichtbarer Text** aus.

    ![Filtern](./media/video-indexer-view-edit/visual-text.png)

    Sie können mehrere Filter in Ihrer Abfrage überlagern. Verwenden Sie die Schaltflächen **+** / **-** , um Filter hinzuzufügen/zu entfernen. Verwenden Sie **Filter löschen** , um alle Filter zu entfernen.
1. Um Videos hinzuzufügen, wählen Sie sie aus, und wählen Sie anschließend **Hinzufügen** aus.
1. Jetzt werden alle von Ihnen ausgewählten Videos angezeigt. Dies sind die Videos, aus denen Sie Clips für Ihr Projekt auswählen werden.

    Sie können die Reihenfolge der Videos durch Ziehen und Ablegen oder durch Auswählen der Listenmenüschaltfläche und anschließendem Auswählen von **Nach unten** oder **Nach oben** ändern. Über das Listenmenü können Sie auch das Video aus diesem Projekt entfernen. 

    ![Screenshot: Video Indexer mit einem Kontextmenü zum Entfernen eines der Videos, zum Löschen der Auswahl oder zum Scrollen nach unten](./media/video-indexer-view-edit/rearrange.png)
    
    Sie haben die Möglichkeit, jederzeit weitere Videos zu diesem Projekt hinzuzufügen, indem Sie **Videos hinzufügen** auswählen. Sie können auch mehrere Vorkommen desselben Videos zu Ihrem Projekt hinzufügen. Sie können diese Option wählen, wenn Sie einen Clip aus einem Video und dann einen Clip aus einem anderen Video sowie anschließend einen anderen Clip aus dem ersten Video anzeigen möchten. 

### <a name="select-clips-to-use-in-your-project"></a>Auswählen der in Ihrem Projekt zu verwendenden Clips

Wenn Sie auf der rechten Seite der einzelnen Videos auf den Pfeil nach unten klicken, öffnen Sie die Einblicke in das Video anhand von Zeitstempeln (Clips des Videos). 

1. Wählen Sie **Einblicke anzeigen** aus, um die anzuzeigenden Einblicke anzupassen. 

    ![Einblicke anzeigen](./media/video-indexer-view-edit/insights.png)
1. Um Abfragen für bestimmte Clips zu erstellen, verwenden Sie das Suchfeld mit der Beschriftung „In Transkript, sichtbarem Text, Personen und Bezeichnungen suchen“.
1. Fügen Sie Filter hinzu, um weitere Details zu den gesuchten Szenen festzulegen, indem Sie **Filteroptionen** auswählen.

    ![Filteroptionen](./media/video-indexer-view-edit/filter-options.png)

    Sie können z. B. Clips anzeigen, in denen „GitHub“ erwähnt wird, während Donovan Brown auf dem Bildschirm zu sehen ist. Dazu müssen Sie einen „include“-Filter hinzufügen, der als Art der Einblicke „Personen“ enthält. Sie müssen dann „Donovan Brown“ in das Suchfeld für den Filter eingeben.
    
    ![Screenshot: Video Indexer, in dem Personen für einen „include“-Filter ausgewählt wurden](./media/video-indexer-view-edit/include.png)
    
    Wenn Sie Clips auswählen möchten, bei denen „GitHub“ erwähnt wird, während Donovan Brown _nicht_ auf dem Bildschirm zu sehen ist, ändern Sie einfach über die Dropdownliste den „include“-Filter in einen „exclude“-Filter. 

1. Fügen Sie einen Clip zu Ihrem Projekt hinzu, indem Sie das hinzuzufügende Segment auswählen. Sie können die Auswahl dieses Clips aufheben, indem Sie erneut auf das Segment klicken.
    
    Fügen Sie alle Segmente eines Videos hinzu, indem Sie auf die Listenmenüoption neben dem Video klicken und **Alle Segmente auswählen** auswählen. 

    ![Alle hinzufügen](./media/video-indexer-view-edit/add-all.png)

    Sie können Ihre gesamte Auswahl aufheben, indem Sie „Auswahl löschen“ auswählen.

> [!TIP]
> Während Sie Ihre Clips auswählen und sortieren, können Sie sich das Video im Player auf der rechten Seite der Seite ansehen. 

![Screenshot: Video Indexer mit der Vorschau eines Videos rechts im Fenster](./media/video-indexer-view-edit/preview.png)

Denken Sie daran, Ihr Projekt zu speichern, wenn Sie Änderungen vornehmen, indem Sie **Projekt speichern** auswählen. 

### <a name="render-and-download-the-project"></a>Rendern und Herunterladen des Projekts

> [!NOTE]
> Für kostenpflichtige Konten von Video Indexer verursacht das Rendering Ihres Projekts Kosten für die Codierung. Die Testkonten von Video Indexer sind auf fünf Stunden Rendering beschränkt.

1. Wenn Sie fertig sind, stellen Sie sicher, dass Ihr Projekt gespeichert wurde. Sie können dieses Projekt jetzt rendern. Wählen Sie **Rendern und Herunterladen** aus. 

    ![Screenshot: Video Indexer mit der Option zum Rendern und Herunterladen des Projekts](./media/video-indexer-view-edit/save.png)

    Es wird ein Popupfenster angezeigt, in dem Ihnen mitgeteilt wird, dass Video Indexer eine Datei rendert. Anschließend wird der Link zum Herunterladen an Ihre E-Mail-Adresse gesendet. Wählen Sie „Fortsetzen“ aus. 
    
    Es wird auch oben auf der Seite eine Benachrichtigung angezeigt, dass das Projekt gerendert wird. Sobald das Rendering abgeschlossen ist, wird eine neue Benachrichtigung angezeigt, dass das Projekt erfolgreich gerendert wurde. Klicken Sie auf die Benachrichtigung, um das Projekt herunterzuladen. Dadurch wird das Projekt im MP4-Format heruntergeladen.

    ![Rendering abgeschlossen](./media/video-indexer-view-edit/rendering-done.png)

1. Sie können über die Registerkarte **Projekte** auf gespeicherte Projekte zugreifen. 

    Wenn Sie dieses Projekt auswählen, werden alle Einblicke und die Zeitachse dieses Projekts angezeigt. Wenn Sie **Video-Editor** auswählen, können Sie dieses Projekt weiter bearbeiten. Die Bearbeitung umfasst das Hinzufügen oder Entfernen von Videos und Clips oder das Umbenennen des Projekts.

    ![Video-Editor](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Erstellen eines Projekts aus Ihrem Video

Sie können ein neues Projekt direkt aus einem Video in Ihrem Konto erstellen. 

1. Wechseln Sie auf der Video Indexer-Website zur Registerkarte **Bibliothek**.
1. Öffnen Sie das Video, das Sie zum Erstellen Ihres Projekts verwenden möchten. Wählen Sie auf der Seite mit Einblicken und der Zeitachse die Schaltfläche **Video-Editor** aus.

    Sie gelangen dadurch auf dieselbe Seite, über die Sie auch ein neues Projekt erstellt haben. Anders als beim neuen Projekt werden die mit Zeitstempeln versehenen Einblicksegmente des Videos angezeigt, mit deren Bearbeitung Sie zuvor begonnen haben.

## <a name="see-also"></a>Weitere Informationen

[What is Video Indexer? (preview)](video-indexer-overview.md) (Was ist Video Indexer? (Vorschauversion))

