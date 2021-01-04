---
title: 'Schnellstart: Testen von Content Moderator im Web'
titleSuffix: Azure Cognitive Services
description: In diesem Schnellstart verwenden Sie das Onlineprüfungstool Content Moderator, um die grundlegende Funktionalität von Content Moderator zu testen, ohne Code schreiben zu müssen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 09/29/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: Content Moderator, Inhaltsmoderation
ms.openlocfilehash: c026c42fe3c7a7f3f0d6b80e3123904077c104cf
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905212"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Schnellstart: Testen von Content Moderator im Web

In diesem Schnellstart verwenden Sie das Onlineprüfungstool Content Moderator, um die grundlegende Funktionalität von Content Moderator zu testen, ohne Code schreiben zu müssen. Wenn Sie diesen Dienst noch schneller in Ihre Inhaltsmoderations-App integrieren möchten, finden Sie im Abschnitt [Nächste Schritte](#next-steps) weitere Schnellstarts.

## <a name="prerequisites"></a>Voraussetzungen

- Aus einem Webbrowser

## <a name="set-up-the-review-tool"></a>Einrichten des Prüfungstools
Das Content Moderator-Prüfungstool ist ein webbasiertes Tool, mit dem menschliche Reviewer Cognitive Services bei der Entscheidungsfindung unterstützen können. In dieser Anleitung wird der kurze Einrichtungsprozess für das Prüfungstool Schritt für Schritt beschrieben, um Ihnen zu verdeutlichen, wie der Content Moderator-Dienst funktioniert. Navigieren Sie zur Website des [Content Moderator-Prüfungstools](https://contentmoderator.cognitive.microsoft.com/), und registrieren Sie sich.

![Content Moderator-Homepage](images/homepage.PNG)

## <a name="create-a-review-team"></a>Erstellen eines Prüfungsteams

Erstellen Sie als Nächstes ein Prüfungsteam. In einem Produktionsszenario umfasst dieses Team die Gruppe der Personen, die die Moderationsentscheidungen des Diensts manuell überprüfen. Wenn Sie ein Team erstellen möchten, müssen Sie eine **Region** auswählen und einen Teamnamen (unter **Teamname**) sowie eine Team-ID (unter **Team-ID**) angeben. Sie können Ihre Kollegen in das Team einladen, indem Sie ihre E-Mail-Adressen eingeben.

> [!NOTE]
> **Teamname** ist ein Anzeigename für Ihr Prüfungsteam. Dieser Name wird im Azure-Portal angezeigt. Die **Team-ID** dient zur programmgesteuerten Identifizierung Ihres Prüfungsteams.

> [!div class="mx-imgBorder"]
> ![Einladen von Teammitgliedern](images/create-team.png)

Wenn Sie Daten mit einem kundenseitig verwalteten Schlüssel (Customer-Managed Key, CMK) verschlüsseln möchten, werden Sie zur Angabe der **Ressourcen-ID** für Ihre Content Moderator-Ressource im Tarif „E0“ aufgefordert. Die von Ihnen bereitgestellte Ressource muss für dieses Team eindeutig sein. 

> [!div class="mx-imgBorder"]
> ![Einladen von Teammitgliedern mit CMK](images/create-team-cmk.png)

## <a name="upload-sample-content"></a>Hochladen von Beispielinhalten

Sie können nun Beispielinhalte hochladen. Wählen Sie **Testen > Bild**, **Testen > Text** oder **Testen > Video** aus.

> [!div class="mx-imgBorder"]
> ![Bild- oder Textmoderation](images/tryimagesortext.png)

Übermitteln Sie Ihre Inhalte für die Moderation. Sie können den folgenden Beispieltextinhalt verwenden:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Intern ruft das Prüfungstool die Moderations-APIs auf, um Ihre Inhalte zu überprüfen. Wenn die Überprüfung abgeschlossen ist, informiert Sie eine Meldung über die zu überprüfenden Ergebnisse.

> [!div class="mx-imgBorder"]
> ![Moderationsdateien](images/submitted.png)

## <a name="review-moderation-tags"></a>Überprüfen der Moderationstags

Überprüfen Sie die angewandten Moderationstags. Sie können sehen, welche Tags auf Ihre Inhalte angewandt wurden und welche Bewertungen die einzelnen Kategorien enthalten. Weitere Informationen zur Bedeutung der unterschiedlichen Inhaltstags finden Sie in den Artikeln zur [Bild-](image-moderation-api.md), [Text-](text-moderation-api.md) und [Videomoderation](video-moderation-api.md).

<!-- ![Review results](images/reviewresults_text.png) -->

In einem Projekt können Sie oder Ihr Prüfungsteam diese Tags ändern oder bei Bedarf weitere Tags hinzufügen. Sie übermitteln diese Änderungen mit der Schaltfläche **Weiter**. Wenn Ihre Geschäftsanwendung die Moderator-APIs aufruft, werden markierte Inhalte hier der Warteschlange hinzugefügt, damit die Prüfungsteams sie kontrollieren können. So lassen sich große Mengen von Inhalten schnell überprüfen.

Sie haben das Content Moderator-Prüfungstool nun verwendet, um sich die Beispiele für die Möglichkeiten des Content Moderator-Diensts anzusehen. Als Nächstes können Sie mehr über das Prüfungstool und seine Integration in ein Softwareprojekt mithilfe der Überprüfungs-APIs erfahren oder zum Abschnitt [Nächste Schritte](#next-steps) springen, um die Verwendung der Moderations-APIs selbst in Ihrer App zu erlernen.

## <a name="learn-more-about-the-review-tool"></a>Weitere Informationen zum Prüfungstool

Weitere Informationen zum Verwenden des Content Moderator-Prüfungstools finden Sie in der Anleitung zum [Prüfungstool](Review-Tool-User-Guide/human-in-the-loop.md). In den APIs zum Prüfungstool erfahren Sie mehr über die Optimierung der Überprüfung durch Personen:
- Die [Auftrags-API](try-review-api-job.md) überprüft Ihre Inhalte mithilfe der Moderations-APIs und generiert im Prüfungstool Überprüfungen. 
- Die [Überprüfungs-API](try-review-api-review.md) erstellt direkt Bild-, Text- oder Videoüberprüfungen für Moderatoren, ohne die Inhalte zuerst zu scannen. 
- Die [Workflow-API](try-review-api-workflow.md) erstellt und aktualisiert Informationen zu benutzerdefinierten Workflows Ihres Teams und ruft diese ab.

Sie können auch mit den nächsten Schritten fortfahren, um sich über die Verwendung der Moderations-APIs in eigenem Code zu informieren.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie die Moderations-APIs selbst in Ihrer App verwenden.
- Implementieren Sie die Bildmoderation. Verwenden Sie die [API-Konsole](try-image-api.md) oder eine [Schnellstartanleitung für eine Clientbibliothek oder REST-API](client-libraries.md), um Bilder zu überprüfen und anzügliche sowie nur für Erwachsene geeignete Inhalte mithilfe von Tags, Zuverlässigkeitsbewertungen und anderen extrahierten Informationen zu erkennen.
- Implementieren Sie die Textmoderation. Verwenden Sie die [API-Konsole](try-text-api.md) oder eine [Schnellstartanleitung für eine Clientbibliothek oder REST-API](client-libraries.md), um Textinhalte auf anstößige Ausdrücke, computergestützte Klassifizierung von unerwünschtem Text (Vorschau) und personenbezogene Daten zu überprüfen.
- Implementieren Sie die Videomoderation. Befolgen Sie die [Anleitung zur Videomoderation für C#](video-moderation-api.md), um Videos zu scannen und potenziell jugendgefährdende und freizügige Inhalte zu erkennen. 
