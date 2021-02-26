---
title: 'Schnellstart: Erstellen einer Objekterkennung mit der Custom Vision-Website'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Custom Vision-Website ein Objekterkennungsmodell erstellen, trainieren und testen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: bilderkennung, bilderkennungs-app, custom vision
ms.openlocfilehash: 5ecd5fee565a8d31e0ff05f3b234771446242d02
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221294"
---
# <a name="quickstart-build-an-object-detector-with-the-custom-vision-website"></a>Schnellstart: Erstellen einer Objekterkennung mit der Custom Vision-Website

In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Custom Vision-Website ein Objekterkennungsmodell erstellen. Nachdem Sie ein Modell erstellt haben, können Sie es mit neuen Bildern testen und schließlich in Ihre eigene Bilderkennungs-App integrieren.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Eine Reihe von Bildern, mit denen Sie Ihr Erkennungsmodell trainieren können. Sie können den Satz von [Beispielbildern](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) auf GitHub verwenden. Oder Sie können Ihre eigenen Bilder mithilfe der folgenden Tipps auswählen.

## <a name="create-custom-vision-resources"></a>Erstellen von Custom Vision-Ressourcen

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

Navigieren Sie im Webbrowser zur [Custom Vision-Webseite](https://customvision.ai), und wählen Sie __Sign in__ (Anmelden). Melden Sie sich mit demselben Konto an, mit dem Sie sich auch beim Azure-Portal angemeldet haben.

![Abbildung der Anmeldeseite](./media/browser-home.png)


1. Um Ihr erstes Projekt zu erstellen, wählen Sie **New Project** (Neues Projekt) aus. Das Dialogfeld **Create new project** (Neues Projekt erstellen) wird angezeigt.

    ![Das Dialogfeld „New Project“ enthält Felder für den Namen, eine Beschreibung und Domänen.](./media/get-started-build-detector/new-project.png)

1. Geben Sie einen Namen und eine Beschreibung für das Projekt ein. Wählen Sie dann eine Ressourcengruppe aus. Wenn Ihr angemeldetes Konto einem Azure-Konto zugeordnet ist, zeigt die Dropdownliste „Ressourcengruppe“ alle Ihre Azure-Ressourcengruppen an, die eine Custom Vision Service-Ressource enthalten. 

   > [!NOTE]
   > Wenn keine Ressourcengruppe verfügbar ist, sollten Sie sich vergewissern, dass Sie sich an [customvision.ai](https://customvision.ai) mit demselben Konto angemeldet haben, das Sie auch für die Anmeldung am [Azure-Portal](https://portal.azure.com/) verwendet haben. Stellen Sie darüber hinaus sicher, dass Sie auf der Custom Vision-Website dasselbe „Verzeichnis“ wie im Azure-Portal gewählt haben, in dem sich Ihre Custom Vision-Ressourcen befinden. An beiden Orten können Sie Ihr Verzeichnis jeweils oben rechts im Dropdownmenü mit den Konten auswählen. 

1. Wählen Sie __Object Detection__ (Objekterkennung) unter __Project Types__ (Projekttypen) aus.

1. Wählen Sie dann eine der verfügbaren Domänen aus. Jede Domäne optimiert das Erkennungsmodul für bestimmte Bildtypen, wie in der folgenden Tabelle beschrieben. Sie können die Domäne später bei Bedarf ändern.

    |Domain|Zweck|
    |---|---|
    |__Allgemein__| Für eine Vielzahl von Aufgaben der Objekterkennung optimiert. Wenn keine der anderen Domänen geeignet erscheint oder Sie unsicher sind, welche Domäne Sie wählen sollen, verwenden Sie die Domäne „Generic“. |
    |__Logo__|Für die Suche nach Markenlogos in Bildern optimiert.|
    |__Produkte in Regalen__|Für die Erkennung und Klassifizierung von Produkten in Regalen optimiert.|
    |__Kompaktdomänen__| Für die Bedingungen der Echtzeitobjekterkennung auf Mobilgeräten optimiert. Die von Kompaktdomänen generierten Modelle können für die lokale Ausführung exportiert werden.|

1. Wählen Sie schließlich __Create Project__ (Projekt erstellen) aus.

## <a name="choose-training-images"></a>Auswählen von Trainingsbildern

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Hochladen und Kennzeichnen von Bildern

In diesem Abschnitt werden Bilder hochgeladen und manuell gekennzeichnet, um das Erkennungsmodul zu trainieren. 

1. Wählen Sie zum Hinzufügen von Bildern __Bilder hinzufügen__ und anschließend __Lokale Dateien durchsuchen__ aus. Wählen Sie __Öffnen__ aus, um die Bilder hochzuladen.

    ![Das Steuerelement zum Hinzufügen von Bildern wird oben links und als Schaltfläche unten in der Mitte angezeigt.](./media/get-started-build-detector/add-images.png)

1. Die hochgeladenen Bilder werden im Abschnitt **Ohne Markierungen** der Benutzeroberfläche angezeigt. Der nächste Schritt besteht darin, die Objekte manuell zu kennzeichnen, die vom Erkennungsmodul erkannt werden sollen. Klicken Sie auf das erste Bild, um das Dialogfenster für das Tagging zu öffnen. 

    ![Im Abschnitt „Ohne Markierung“ hochgeladene Bilder](./media/get-started-build-detector/images-untagged.png)

1. Klicken Sie und ziehen Sie ein Rechteck um das Objekt in Ihrem Bild. Geben Sie dann mit der Schaltfläche **+** einen neuen Tagnamen ein, oder wählen Sie ein vorhandenes Tag aus der Dropdownliste aus. Es ist wichtig, jede Instanz der Objekte, die Sie erkennen möchten, zu kennzeichnen, da das Erkennungsmodul den nicht gekennzeichneten Hintergrundbereich beim Trainieren als negatives Beispiel verwendet. Wenn Sie mit dem Tagging fertig sind, klicken Sie auf den Pfeil rechts, um Ihre Tags zu speichern und zum nächsten Bild zu gelangen.

    ![Tagging von Objekten mit rechteckiger Auswahl](./media/get-started-build-detector/image-tagging.png)

Zum Hochladen eines weiteren Bildersatzes kehren Sie zum Anfang dieses Abschnitts zurück und wiederholen die Schritte.

## <a name="train-the-detector"></a>Trainieren des Erkennungsmoduls

Um das Erkennungsmodell zu trainieren, wählen Sie die Schaltfläche **Trainieren** aus. Das Erkennungsmodul verwendet alle aktuellen Bilder und deren Tags zum Erstellen eines Modells, das die einzelnen gekennzeichneten Objekte identifiziert.

![Die Schaltfläche „Trainieren“ oben rechts auf der Header-Symbolleiste der Webseite](./media/getting-started-build-a-classifier/train01.png)

Der Trainingsprozess sollte nur wenige Minuten dauern. Während dieser Zeit werden auf der Registerkarte **Leistung** Informationen über den Trainingsprozess angezeigt.

![Das Browserfenster mit einem Trainingsdialogfeld im Hauptteil](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>Auswerten des Erkennungsmoduls

Nach Abschluss des Trainings wird die Leistung des Modells berechnet und angezeigt. Custom Vision Service verwendet die Bilder, die Sie zum Training gesendet haben, zur Berechnung von Genauigkeit, Trefferquote und mittlerer durchschnittlicher Genauigkeit. Genauigkeit und Trefferquote sind zwei unterschiedliche Messungen der Wirksamkeit eines Erkennungsmoduls:

- Die **Genauigkeit** gibt den Anteil der richtig identifizierten Klassifizierungen an. Beispiel: Wenn das Modell 100 Bilder als Hunde identifiziert hat und 99 davon tatsächlich Hunde zeigten, beträgt die Genauigkeit 99 %.
- Die **Trefferquote** gibt den Anteil der tatsächlichen Klassifizierungen an, die richtig identifiziert wurden. Beispiel: Wenn tatsächlich 100 Bilder von Äpfeln vorhanden sind und das Modell 80 davon als Äpfel identifiziert, beträgt die Trefferquote 80 %.
- Die **durchschnittliche Genauigkeit** ist der Durchschnittswert der durchschnittlichen Genauigkeit (Average Precision, AP). Die durchschnittliche Genauigkeit ist der Bereich unterhalb der Kurve zu Genauigkeit/Abruf. (Für jede ausgeführte Vorhersage wird die Genauigkeit in Bezug auf den Abruf gezeichnet.)

![Die Trainingsergebnisse zeigen Gesamtgenauigkeit, Trefferquote und mittlere durchschnittliche Genauigkeit an.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Wahrscheinlichkeitsschwellenwert

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

### <a name="overlap-threshold"></a>Überlappungsschwellenwert

Mit dem Schieberegler für den Ü **berlappungsschwellenwert** wird gesteuert, wie korrekt eine Objektvorhersage sein muss, damit sie beim Trainieren als korrekt betrachtet wird. Er legt die kleinstmögliche Überlappung zwischen dem vorhergesagten Objektbegrenzungsrahmen und dem tatsächlich vom Benutzer eingegebenen Begrenzungsrahmen fest. Wenn die Begrenzungsrahmen nicht in diesem Maße überlappen, wird die Vorhersage nicht als korrekt betrachtet.

## <a name="manage-training-iterations"></a>Verwalten von Trainingsiterationen

Bei jedem Trainingsvorgang für Ihr Erkennungsmodul erstellen Sie eine neue _Iteration_ mit eigenen aktualisierten Leistungsmetriken. Sie können alle Ihre Iterationen im linken Bereich der Registerkarte **Leistung** anzeigen. Im linken Bereich befindet sich auch die Schaltfläche **Löschen**, mit der Sie veraltete Iterationen löschen können. Beim Löschen einer Iteration werden auch alle Bilder gelöscht, die dieser eindeutig zugeordnet sind.

Informationen zum programmgesteuerten Zugriff auf die trainierten Modelle finden Sie unter [Verwenden des Modells mit der Vorhersage-API](./use-prediction-api.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Quickstart haben Sie erfahren, wie Sie über die Custom Vision-Website ein Objekterkennungsmodell erstellen und trainieren. Informieren Sie sich als Nächstes über den iterativen Prozess zur Verbesserung Ihres Modells.

> [!div class="nextstepaction"]
> [Testen und erneutes Trainieren eines Modells mit Custom Vision Service](test-your-model.md)

* [Was ist Custom Vision?](./overview.md)
