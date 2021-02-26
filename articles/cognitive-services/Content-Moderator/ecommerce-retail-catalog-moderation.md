---
title: 'Tutorial: Moderieren von E-Commerce-Produktbildern – Content Moderator'
titleSuffix: Azure Cognitive Services
description: Dieses Tutorial zeigt Ihnen, wie Sie eine Anwendung zum Analysieren und Klassifizieren von Produktbildern mit angegebenen Bezeichnungen einrichten (mit maschinellem Sehen und Custom Vision von Azure). Kennzeichnen Sie anstößige Bilder für die weitere Untersuchung (mit Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 211f58e26ec89c393bf9f91cc3a05044c6b1e802
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221277"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Tutorial: Moderieren von E-Commerce-Produktbildern mit Azure Content Moderator

In diesem Tutorial erfahren Sie, wie Sie Azure Cognitive Services und Content Moderator verwenden, um Produktbilder für ein E-Commerce-Szenario zu klassifizieren und zu moderieren. Sie verwenden maschinelles Sehen und Custom Vision, um Bilder mit Tags (Bezeichnungen) zu versehen. Anschließend erstellen Sie eine Teamüberprüfung, um die Machine Learning-basierten Technologien von Content Moderator mit menschlichen Prüfungsteams zu kombinieren und so ein intelligentes Moderationssystem zu erhalten.

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

> [!div class="checklist"]
> * Registrieren für Content Moderator und Erstellen eines Prüfungsteams
> * Verwenden Sie die Bildersuche-API von Content Moderator, um Bilder auf potenziell nicht jugendfreie Inhalte zu prüfen.
> * Verwenden des Diensts für maschinelles Sehen, um nach Prominenten zu suchen (oder nach anderen Tags, die durch maschinelles Sehen erkannt werden können)
> * Verwenden des Custom Vision-Diensts, um Bilder auf Flaggen, Spielsachen und Stifte (oder andere benutzerdefinierte Tags) zu überprüfen
> * Weiterleiten der kombinierten Überprüfungsergebnisse an menschliche Prüfer zur abschließenden Entscheidungsfindung

Der vollständige Beispielcode steht im GitHub-Repository [Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) zur Verfügung.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Content Moderator-Abonnementschlüssel. Gehen Sie wie unter [Schnellstart: Erstellen eines Cognitive Services-Kontos im Azure-Portal](../cognitive-services-apis-create-account.md) beschrieben vor, um den Content Moderator-Dienst zu abonnieren und Ihren Schlüssel zu erhalten.
- Ein Abonnementschlüssel für maschinelles Sehen (siehe obige Anweisungen).
- Eine beliebige Edition von [Visual Studio 2015 oder 2017](https://www.visualstudio.com/downloads/).
- Bilder für jede Bezeichnung zur Verwendung durch die Custom Vision-Klassifizierung (in diesem Fall Spielsachen, Stifte und US-Flaggen).

## <a name="create-a-review-team"></a>Erstellen eines Prüfungsteams

Informationen zum Registrieren für das [Content Moderator-Prüfungstool](https://contentmoderator.cognitive.microsoft.com/) sowie zum Erstellen eines Prüfungsteams finden Sie unter [Schnellstart: Testen von Content Moderator im Web](quick-start.md). Notieren Sie sich auf der Seite **Anmeldeinformationen** die **Team-ID**.

## <a name="create-custom-moderation-tags"></a>Erstellen benutzerdefinierter Moderationstags

Erstellen Sie als Nächstes im Prüfungstool benutzerdefinierte Tags. (Informationen hierzu finden Sie bei Bedarf im Artikel [Infos zu Tags](./review-tool-user-guide/configure.md#tags).) Wir fügen hier folgende Tags hinzu: **celebrity** (prominente Person), **USA**, **flag** (Flagge), **toy** (Spielzeug) und **pen** (Stift). Es muss sich nicht bei allen Tags um Kategorien handeln, die durch maschinelles Sehen erkannt werden können (beispielsweise **celebrity**). Sie können gerne eigene benutzerdefinierte Tags hinzufügen, solange Sie die Custom Vision-Klassifizierung später für deren Erkennung trainieren.

![Konfigurieren von benutzerdefinierten Tags](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Erstellen eines Visual Studio-Projekts

1. Öffnen Sie in Visual Studio das Dialogfeld „Neues Projekt“. Erweitern Sie **Installiert** und dann **Visual C#** , und wählen Sie **Konsolen-App (.NET Framework)** aus.
1. Nennen Sie die Anwendung **EcommerceModeration**, und wählen Sie anschließend **OK** aus.
1. Falls Sie dieses Projekt einer vorhandenen Projektmappe hinzufügen, sollten Sie es als einzelnes Startprojekt auswählen.

In diesem Tutorial wird der für das Projekt wichtige Code erläutert. Hierbei wird aber nicht auf jede einzelne Codezeile eingegangen. Kopieren Sie den gesamten Inhalt der Datei _Program.cs_ aus dem Beispielprojekt ([Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) in die Datei _Program.cs_ Ihres neuen Projekts. Gehen Sie dann Schritt für Schritt die folgenden Abschnitte durch, um sich mit der Funktionsweise und der praktischen Verwendung des Projekts vertraut zu machen.

## <a name="define-api-keys-and-endpoints"></a>Definieren von API-Schlüsseln und -Endpunkten

Da in diesem Tutorial drei Cognitive Services verwendet werden, sind auch drei entsprechende Schlüssel und API-Endpunkte erforderlich. Sehen Sie sich die folgenden Felder in der Klasse **Program** an:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Sie müssen die Felder vom Typ `___Key` mit den Werten Ihrer Abonnementschlüssel aktualisieren. Darüber hinaus müssen Sie die Felder vom Typ `___Uri` ändern, damit diese jeweils die richtigen Endpunkt-URLs enthalten. (Den Custom Vision-Schlüssel und -Endpunkt erhalten Sie später.) Diese Werte finden Sie auf der Registerkarte **Schnellstart** der einzelnen Azure-Ressourcen. Geben Sie für den Teil `YOURTEAMID` des Felds `ReviewUri` die ID des weiter oben erstellten Prüfungsteams an. Der letzte Teil des Felds `CustomVisionUri` wird später ausgefüllt.

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>Primäre Methodenaufrufe

Sehen Sie sich den folgenden Code in der Methode **Main** an. Er durchläuft eine Liste von Bild-URLs. Er analysiert jedes Bild mit den drei verschiedenen Diensten, erfasst die angewendeten Tags im Array **ReviewTags** und erstellt eine Prüfung für menschliche Moderatoren. Hierfür werden die Bilder an das Content Moderator-Prüfungstool gesendet. Diese Methoden werden in den folgenden Abschnitten behandelt. Hier können Sie bei Bedarf steuern, welche Bilder zur Überprüfung gesendet werden. Verwenden Sie hierfür das Array **ReviewTags** in einer Bedingungsanweisung, um zu überprüfen, welche Tags angewendet wurden.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>Methode „EvaluateAdultRacy“

Sehen Sie sich die Methode **EvaluateAdultRacy** in der Klasse **Program** an. Diese Methode akzeptiert eine Bild-URL und ein Array von Schlüssel-Wert-Paaren als Parameter. Sie ruft (unter Verwendung von REST) die Bild-API von Content Moderator auf, um die Ergebnisse für nicht jugendfreie und freizügige Inhalte des Bilds zu erhalten. Ist eines der Ergebnisse größer als 0,4 (der mögliche Bereich liegt zwischen 0 und 1), wird der entsprechende Wert im Array **ReviewTags** auf **true** festgelegt.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>EvaluateComputerVisionTags-Methode

Die nächste Methode akzeptiert eine Bild-URL und die Informationen Ihres Abonnements für maschinelles Sehen und analysiert, ob das Bild Prominente enthält. Wurde mindestens eine prominente Person gefunden, wird der entsprechende Wert im Array **ReviewTags** auf **true** festgelegt.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>Methode „EvaluateCustomVisionTags“

Sehen Sie sich als Nächstes die Methode **EvaluateCustomVisionTags** an. Diese klassifiziert die eigentlichen Produkte (in diesem Fall Flaggen, Spielsachen und Stifte). Gehen Sie gemäß der Anleitung unter [Erstellen einer Klassifizierung mit Custom Vision](../custom-vision-service/getting-started-build-a-classifier.md) vor, um Ihre eigene benutzerdefinierte Bildklassifizierung zu erstellen und Flaggen, Spielsachen und Stifte (oder andere benutzerdefinierte Tags) in Bildern zu erkennen. Sie können die Bilder im Ordner **sample-images** des [GitHub-Repositorys](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) verwenden, um schnell einige der Kategorien in diesem Beispiel zu trainieren.

![Custom Vision-Webseite mit Trainingsbildern für Stifte, Spielsachen und Flaggen](images/tutorial-ecommerce-custom-vision.PNG)

Rufen Sie nach dem Trainieren Ihrer Klassifizierung den Vorhersageschlüssel und die Vorhersageendpunkt-URL ab (siehe [Abrufen der Vorhersage-URL und des Vorhersage-Schlüssels](../custom-vision-service/use-prediction-api.md#get-the-url-and-prediction-key)), und weisen Sie diese Werte dem Feld `CustomVisionKey` bzw. `CustomVisionUri` zu. Die Methode verwendet diese Werte, um die Klassifizierung abzufragen. Findet die Klassifizierung mindestens eines der benutzerdefinierten Tags auf dem Bild, legt diese Methode die entsprechenden Werte im Array **ReviewTags** auf **true** fest.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Erstellen von Prüfungen für das Prüfungstool

In den vorherigen Abschnitten haben Sie sich damit vertraut gemacht, wie die App eingehende Bilder auf nicht jugendfreie und freizügige Inhalte (Content Moderator), Prominente (maschinelles Sehen) und verschiedene Objekte (Custom Vision) überprüft. Sehen Sie sich als Nächstes die **CreateReview**-Methode an. Diese Methode lädt die Bilder mit allen angewendeten Tags (als _Metadata_ übergeben) in das Content Moderator-Prüfungstool hoch.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

Die Bilder werden auf der Prüfungsregisterkarte des [Content Moderator-Prüfungstools](https://contentmoderator.cognitive.microsoft.com/) angezeigt.

![Screenshot des Content Moderator-Prüfungstools mit mehreren Bildern und hervorgehobenen Tags](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Übermitteln einer Liste mit Testbildern

In der Methode **Main** sehen Sie, dass dieses Programm im Verzeichnis „C:\test\“ nach einer Datei namens _Urls.txt_ sucht, die eine Liste mit Bild-URLs enthält. Erstellen Sie diese Datei und das Verzeichnis, oder ändern Sie den Pfad so, dass er auf Ihre Textdatei verweist. Füllen Sie diese Datei dann mit den URLs von Bildern, die Sie für einen Test verwenden möchten.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Ausführen des Programms

Wenn Sie alle oben genannten Schritte ausgeführt haben, sollte das Programm jedes Bild verarbeiten (also relevante Tags von allen drei Diensten abfragen) und die Bilder dann zusammen mit den Taginformationen in das Content Moderator-Prüfungstool hochladen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein Programm eingerichtet, mit dem Sie die Produktbildanalyse durchführen, Bilder mit Tags nach Produkttyp versehen und es einem Prüfungsteam ermöglichen können, fundierte Entscheidungen für die Inhaltsmoderation zu treffen. Machen Sie sich als Nächstes mit den Details der Bildmoderation vertraut.

> [!div class="nextstepaction"]
> [Überprüfen moderierter Bilder](./review-tool-user-guide/review-moderated-images.md)