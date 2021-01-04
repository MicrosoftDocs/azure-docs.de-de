---
title: Verwenden von Inhaltsüberprüfungen über das Prüfungstool – Content Moderator
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie menschliche Moderatoren mit dem Prüfungstool Bilder in einem Webportal überprüfen können.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 77d7b8a0bf4d7fe9a94a61ea3f2f4279246ffb69
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903886"
---
# <a name="create-human-reviews"></a>Erstellen von Überprüfungen durch Personen

In diesem Artikel wird erläutert, wie Sie [Überprüfungen](../review-api.md#reviews) auf der Website des Prüfungstools einrichten. In Überprüfungen werden Inhalte gespeichert und angezeigt, damit Moderatoren auf sie zugreifen können. Moderatoren können die angewandten Markierungen ändern und nach Bedarf eigene benutzerdefinierte Markierungen anwenden. Wenn ein Benutzer eine Überprüfung abgeschlossen hat, werden die Ergebnisse an einen angegebenen Rückrufendpunkt gesendet und die Inhalte auf der Website entfernt.

## <a name="prerequisites"></a>Voraussetzungen

- Melden Sie sich auf der Website des [Content Moderator-Prüfungstools](https://contentmoderator.cognitive.microsoft.com/) an, oder erstellen Sie dort ein Konto.

## <a name="image-reviews"></a>Bildbetrachtungen

1. Navigieren Sie zum [Prüfungstool](https://contentmoderator.cognitive.microsoft.com/), wählen Sie die Registerkarte **Ausprobieren** aus, und laden Sie einige Bilder zum Überprüfen hoch.
1. Navigieren Sie nach der abgeschlossenen Verarbeitung der hochgeladenen Bilder zur Registerkarte **Überprüfen**, und wählen Sie **Bild** aus.

    ![Chrome-Browser mit dem Prüfungstool und hervorgehobener Bildüberprüfungsoption](images/review-images-1.png)

    Die Bilder werden mit allen Bezeichnungen angezeigt, die beim automatischen Moderationsprozess zugewiesen wurden. Die Bilder, die Sie über das Prüfungstool übermitteln, sind für andere Prüfer nicht sichtbar.

1. Über den Schieberegler **Anzuzeigende Überprüfungen** (1) können Sie optional die Anzahl der Bilder anpassen, die auf dem Bildschirm angezeigt werden sollen. Klicken Sie auf die Schaltfläche für **markierte** oder **nicht markierte** Bilder (2), um die Bilder entsprechend zu sortieren. Klicken Sie auf einen Markierungsbereich (3), um ihn ein- oder auszuschalten.

    ![Chrome-Browser mit dem Prüfungstool und für die Überprüfung markierten Bildern](images/review-images-2.png)

1. Um weitere Informationen in einem Bild anzuzeigen, klicken Sie auf die Auslassungspunkte in der Miniaturansicht, und wählen Sie **Details anzeigen** aus. Über die Option **Verschieben nach** können Sie ein Bild einem untergeordneten Team zuweisen. (Weitere Informationen zu untergeordneten Teams finden Sie im Abschnitt [Teams](./configure.md#manage-team-and-subteams).)

    ![Ein Bild mit hervorgehobener Detailansichtsoption](images/review-images-3.png)

1. Durchsuchen Sie die Bildmoderationsinformationen auf der Seite mit den Details.

    ![Ein Bild mit aufgeführten Moderationsdetails in einem separaten Bereich](images/review-images-4.png)

1. Wenn Sie die Markierungszuweisungen überprüft und ggf. aktualisiert haben, klicken Sie auf **Weiter**, um Ihre Überprüfungen einzusenden. Nach dem Absenden haben Sie etwa fünf Sekunden Zeit, um auf die Schaltfläche **Vorh.** zu klicken, um zum vorherigen Bildschirm zurückzukehren und die Bilder erneut zu überprüfen. Danach sind die Bilder nicht mehr in der Warteschlange zum Senden und die Schaltfläche **Vorh.** ist nicht mehr verfügbar.

## <a name="text-reviews"></a>Textüberprüfungen

Die Funktion für Textüberprüfungen ist der Funktion für Bildüberprüfungen sehr ähnlich. Statt Inhalte hochzuladen, schreiben Sie einfach Text oder fügen ihn ein (bis zu 1.024 Zeichen). Der Text wird in Content Moderator analysiert, und Markierungen werden angewandt (zusätzlich zu anderen Moderationsinformationen, z. B. für anstößige Ausdrücke und personenbezogene Daten). In Textüberprüfungen können Sie die angewandten Markierungen ein- und ausschalten und/oder benutzerdefinierte Markierungen anwenden, bevor Sie die Überprüfung übermitteln.

![Screenshot des Überprüfungstools mit markiertem Text in einem Chrome-Browserfenster](../images/reviewresults_text.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde erläutert, wie Sie Überprüfungen über das [Prüfungstool](https://contentmoderator.cognitive.microsoft.com) von Content Moderator einrichten und verwenden. Sehen Sie sich als Nächstes die [Anleitung für die API-Konsole](../try-review-api-review.md) oder den [.NET SDK-Schnellstart](../client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) an, um zu erfahren, wie Überprüfungen programmgesteuert erstellt werden können.