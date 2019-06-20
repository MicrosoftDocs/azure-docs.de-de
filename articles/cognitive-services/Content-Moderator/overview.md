---
title: Was ist Azure Content Moderator?
titlesuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Content Moderator verwenden, um nicht angemessene Inhalte in von Benutzern generiertem Material nachzuverfolgen, zu kennzeichnen, zu bewerten und zu filtern.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: 14cf500cff9f1f7f8b1573862c3060326e6518fa
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688863"
---
# <a name="what-is-azure-content-moderator"></a>Was ist Azure Content Moderator?

Die Azure Content Moderator-API gehört zu Cognitive Services und überprüft Text-, Bild- und Videoinhalte auf potenziell anstößiges, riskantes oder anderweitig unerwünschtes Material. Gefundenes Material dieser Art wird mithilfe von Flags entsprechend gekennzeichnet. Mit Flags versehene Inhalte können dann von Ihrer App angemessen behandelt werden, um Vorgaben zu erfüllen oder den Benutzern die vorgesehene Umgebung zu bieten. Weitere Informationen zur Bedeutung der verschiedenen Inhaltsflags finden Sie im Abschnitt [Moderations-APIs](#moderation-apis).

## <a name="where-it-is-used"></a>Einsatzgebiete

Im Anschluss finden Sie einige Szenarien, in denen ein Softwareentwickler oder Team Content Moderator verwenden kann:

- Onlinemarktplätze, die Produktkataloge und andere benutzergenerierte Inhalte moderieren
- Gamingunternehmen, die benutzergenerierte Spielartefakte und Chatrooms moderieren
- Messagingplattformen sozialer Medien, die von Benutzern hinzugefügte Bilder, Texte und Videos moderieren
- Medienunternehmen, die eine zentrale Moderation für ihre Inhalte implementieren
- Anbieter im Bildungswesen, die unangemessene Inhalte für Schüler und Lehrer herausfiltern

> [!NOTE]
> Sie können Content Moderator nicht verwenden, um nach Bildern zu suchen, auf denen Kinder in nicht zulässiger Weise dargestellt sind. Qualifizierte Organisationen können jedoch den [PhotoDNA Cloud Service](https://www.microsoft.com/photodna "Microsoft PhotoDNA Cloud Service") verwenden, um nach derartigen Inhalten zu suchen.

## <a name="what-it-includes"></a>Lieferumfang

Der Content Moderator-Dienst umfasst mehrere Webdienst-APIs, die sowohl über REST-Aufrufe als auch über ein .NET SDK verfügbar sind. Darüber hinaus steht ein Tool für die Überprüfung durch Personen zur Verfügung, mit dem Personen den Dienst unterstützen und seine Moderationsfunktion verbessern oder optimieren können.

## <a name="moderation-apis"></a>Moderations-APIs

Der Content Moderator-Dienst umfasst Moderations-APIs, die Inhalte auf potenziell ungeeignetes oder anstößiges Material überprüfen.

![Blockdiagramm für Moderations-APIs von Content Moderator](images/content-moderator-mod-api.png)

Die folgende Tabelle beschreibt die verschiedenen Typen von Moderations-APIs.

| API-Gruppe | BESCHREIBUNG |
| ------ | ----------- |
|[**Textmoderation**](text-moderation-api.md)| Durchsucht Text nach anstößigen Inhalten, explizit sexuellen oder anzüglichen Inhalten, Obszönitäten und personenbezogenen Daten.|
|[**Benutzerdefinierte Begriffslisten**](try-terms-list-api.md)| Überprüft Text auf Begriffe aus einer benutzerdefinierten Begriffsliste (zusätzlich zu den integrierten Begriffen). Mithilfe von benutzerdefinierten Listen können Sie Inhalte im Einklang mit Ihren eigenen Inhaltsrichtlinien blockieren oder zulassen.|  
|[**Bildmoderation**](image-moderation-api.md)| Überprüft Bilder auf nicht jugendfreie oder freizügige Inhalte, erkennt Text in Bildern mithilfe der optischen Zeichenerkennung (Optical Character Recognition, OCR) und erkennt Gesichter.|
|[**Benutzerdefinierte Bildlisten**](try-image-list-api.md)| Überprüft Bilder anhand einer benutzerdefinierten Liste von Bildern. Mithilfe benutzerdefinierter Bildlisten können Sie Instanzen häufig wiederkehrender Inhalte herausfiltern, die Sie nicht erneut klassifizieren möchten.|
|[**Videomoderation**](video-moderation-api.md)| Überprüft Videos auf nicht jugendfreie oder freizügige Inhalte und gibt Zeitmarkierungen für entsprechende Inhalte zurück.|

## <a name="review-apis"></a>Überprüfen von APIs

Mithilfe der Review-APIs können Sie Ihre Moderationspipeline für menschliche Reviewer integrieren. Verwenden Sie die Vorgänge [Jobs](review-api.md#jobs), [Reviews](review-api.md#reviews) und [Workflow](review-api.md#workflows), um mit dem [Review-Tool](#the-review-tool) (unten) Workflows für die Überprüfung durch Personen zu erstellen und zu automatisieren.

> [!NOTE]
> Die Workflow-API ist noch nicht im .NET SDK verfügbar, sie kann jedoch mit dem REST-Endpunkt verwendet werden.

![Blockdiagramm für Review-APIs von Content Moderator](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>Das Prüfungstool

Der Content Moderator-Dienst enthält auch das webbasierte [Überprüfungstool](Review-Tool-User-Guide/human-in-the-loop.md), das die Inhaltsüberprüfungen für menschliche Moderatoren hostet. Der Dienst wird durch die menschlichen Eingaben nicht trainiert. Die Zusammenarbeit zwischen Dienst und menschlichen Überprüfungsteams ermöglicht es Entwicklern jedoch, ein ausgewogenes Verhältnis zwischen Effizienz und Genauigkeit zu erzielen. Das Überprüfungstool bietet auch ein benutzerfreundliches Front-End für eine Vielzahl von Content Moderator-Ressourcen.

![Startseite des Content Moderator-Tools für die Überprüfung durch Personen](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Datenschutz und Sicherheit

Wie bei allen Cognitive Services-Diensten müssen Entwickler, die den Content Moderator-Dienst nutzen, die Microsoft-Richtlinien zu Kundendaten beachten. Weitere Informationen finden Sie im Microsoft Trust Center auf der [Seite zu Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices).

## <a name="next-steps"></a>Nächste Schritte

Anweisungen zu den ersten Schritten mit dem Content Moderator-Dienst finden Sie unter [Schnellstart: Erste Schritte mit Content Moderator](quick-start.md).