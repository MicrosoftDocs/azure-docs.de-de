---
title: 'Schnellstart: Testen von Content Moderator im Web – Content Moderator'
titlesuffix: Azure Cognitive Services
description: In diesem Schnellstart verwenden Sie das Onlineprüfungstool Content Moderator, um die grundlegende Funktionalität von Content Moderator zu testen, ohne Code schreiben zu müssen.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: d1c65b590fd68f68fdaeb110c0fd00e0c5a6649a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127653"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Schnellstart: Testen von Content Moderator im Web

In diesem Schnellstart verwenden Sie das Onlineprüfungstool Content Moderator, um die grundlegende Funktionalität von Content Moderator zu testen, ohne Code schreiben zu müssen. Wenn Sie diesen Dienst noch schneller in Ihre App integrieren möchten, finden Sie im Abschnitt [Nächste Schritte](#next-steps) weitere Schnellstarts.

## <a name="prerequisites"></a>Voraussetzungen

- Aus einem Webbrowser

## <a name="set-up-the-review-tool"></a>Einrichten des Prüfungstools
Das Content Moderator-Prüfungstool ist ein webbasiertes Tool, mit dem menschliche Reviewer Cognitive Services bei der Entscheidungsfindung unterstützen können. In dieser Anleitung werden Sie durch den kurzen Einrichtungsprozess für das Prüfungstool geführt, sodass Sie sehen können, wie der Content Moderator-Dienst funktioniert. Navigieren Sie zur Website des [Content Moderator-Prüfungstools](https://contentmoderator.cognitive.microsoft.com/), und registrieren Sie sich.

![Content Moderator-Homepage](images/homepage.PNG)

## <a name="create-a-review-team"></a>Erstellen eines Prüfungsteams

Erstellen Sie als Nächstes ein Prüfungsteam. In einem Produktionsszenario ist dies die Gruppe von Personen, die manuell die Moderationsentscheidungen des Diensts überprüfen. Für diese Anleitung müssen Sie nur einen Teamnamen festlegen. Sie können Ihre Kollegen in das Team einladen, indem Sie ihre E-Mail-Adressen eingeben.

![Einladen von Teammitgliedern](images/QuickStart-2-small.png)

## <a name="upload-sample-content"></a>Hochladen von Beispielinhalten

Sie können nun Beispielinhalte hochladen. Wählen Sie **Testen > Bild**, **Testen > Text** oder **Testen > Video** aus.

![Bild- oder Textmoderation](images/tryimagesortext.png)

Übermitteln Sie Ihre Inhalte für die Moderation. Intern ruft das Prüfungstool die Moderations-APIs auf, um Ihre Inhalte zu überprüfen. Wenn die Überprüfung abgeschlossen ist, informiert Sie eine Meldung über die zu überprüfenden Ergebnisse.

![Moderationsdateien](images/submitted.png)

## <a name="review-moderation-tags"></a>Überprüfen der Moderationstags

Überprüfen Sie die angewandten Moderationstags. Sie können sehen, welche Tags auf Ihre Inhalte angewandt wurden und welche Bewertungen die einzelnen Kategorien enthalten. Weitere Informationen zur Bedeutung der unterschiedlichen Inhaltstags finden Sie unter den Themen zur [Bild-](image-moderation-api.md), [Text-](text-moderation-api.md) und [Videomoderation](video-moderation-api.md).

![Überprüfen der Ergebnisse](images/reviewresults_text.png)

In einem Projekt können Sie oder Ihr Prüfungsteam diese Tags ändern oder bei Bedarf weitere Tags hinzufügen. Sie übermitteln diese Änderungen mit der Schaltfläche **Weiter**. Wenn Ihre Geschäftsanwendung die Moderator-APIs aufruft, werden markierte Inhalte hier der Warteschlange hinzugefügt, damit die Prüfungsteams sie kontrollieren können. So lassen sich große Mengen von Inhalten schnell überprüfen.

An diesem Punkt haben Sie das Content Moderator-Prüfungstool verwendet, um Beispiele für die Möglichkeiten des Content Moderator-Diensts zu sehen. Als Nächstes können Sie mehr über das Prüfungstool und seine Integration in ein Softwareprojekt mithilfe der Überprüfungs-APIs erfahren oder zum Abschnitt [Nächste Schritte](#next-steps) springen, um die Verwendung der Moderations-APIs selbst in Ihrer App zu erlernen.

## <a name="learn-more-about-the-review-tool"></a>Weitere Informationen zum Prüfungstool

Weitere Informationen zum Verwenden des Content Moderator-Prüfungstools finden Sie in der Anleitung zum [Prüfungstool](Review-Tool-User-Guide/human-in-the-loop.md). In den APIs zum Prüfungstool erfahren Sie mehr über die Optimierung der Überprüfung durch Personen:
- Die [Auftrags-API](try-review-api-job.md) überprüft Ihre Inhalte mithilfe der Moderations-APIs und generiert im Prüfungstool Überprüfungen. 
- Die [Überprüfungs-API](try-review-api-review.md) erstellt direkt Bild-, Text- oder Videoüberprüfungen für Moderatoren, ohne die Inhalte zuerst zu scannen. 
- Die [Workflow-API](try-review-api-workflow.md) erstellt und aktualisiert Informationen zu benutzerdefinierten Workflows Ihres Teams und ruft diese ab.

Sie können auch mit den nächsten Schritten fortfahren, um sich über die Verwendung der Moderations-APIs in eigenem Code zu informieren.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie die Moderations-APIs selbst in Ihrer App verwenden.
- Implementieren Sie die Bildmoderation. Nutzen Sie die [API-Konsole](try-image-api.md) oder den [C#-Schnellstart](image-moderation-quickstart-dotnet.md), um Bilder zu überprüfen und anzügliche sowie nur für Erwachsene geeignete Inhalte mithilfe von Tags, Zuverlässigkeitsbewertungen und anderen extrahierten Informationen zu erkennen.
- Implementieren Sie die Textmoderation. Nutzen Sie die [API-Konsole](try-text-api.md) oder den [C#-Schnellstart](text-moderation-quickstart-dotnet.md), um Textinhalte auf obszöne, computergestützte unerwünschte Textklassifizierung (Vorschau) und personenbezogene Daten zu überprüfen.
- Implementieren Sie die Videomoderation. Befolgen Sie die [Anleitung zur Videomoderation für C#](video-moderation-api.md), um Videos zu scannen und potenziell jugendgefährdende und freizügige Inhalte zu erkennen. 
