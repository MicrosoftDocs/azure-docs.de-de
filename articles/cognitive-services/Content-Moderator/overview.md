---
title: Was ist Azure Content Moderator?
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Content Moderator verwenden, um nicht angemessene Inhalte in von Benutzern generiertem Material nachzuverfolgen, zu kennzeichnen, zu bewerten und zu filtern.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: Content Moderator, Azure Content Moderator, Onlinemoderator, Inhaltsfilterungssoftware, Inhaltsmoderationsdienst, Inhaltsmoderation
ms.openlocfilehash: 57a390a1da1e3a10b9fda4b531a83ee48e91125b
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560372"
---
# <a name="what-is-azure-content-moderator"></a>Was ist Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Content Moderator ist ein KI-Dienst für die Behandlung potenziell anstößiger, riskanter oder anderweitig unerwünschter Inhalte. Er umfasst den KI-gestützten Inhaltsmoderationsdienst, der Text, Bilder und Videos überprüft und automatisch mit Inhaltsflags versieht, sowie das Überprüfungstool (eine onlinebasierte Moderatorumgebung für eine Gruppe menschlicher Prüfer).

Die Integration einer Inhaltsüberprüfungssoftware in Ihre App kann beispielsweise empfehlenswert sein, um gesetzliche Vorschriften einzuhalten oder Benutzern die für sie beabsichtigte Umgebung zu bieten.

## <a name="where-its-used"></a>Verwendung des Diensts

Im Anschluss finden Sie einige Szenarien, in denen ein Softwareentwickler oder Team einen Inhaltsmoderationsdienst benötigt:

- Onlinemarktplätze, die Produktkataloge und andere benutzergenerierte Inhalte moderieren
- Gamingunternehmen, die benutzergenerierte Spielartefakte und Chatrooms moderieren
- Messagingplattformen sozialer Medien, die von Benutzern hinzugefügte Bilder, Texte und Videos moderieren
- Medienunternehmen, die eine zentrale Moderation für ihre Inhalte implementieren
- Anbieter im Bildungswesen, die unangemessene Inhalte für Schüler und Lehrer herausfiltern

> [!IMPORTANT]
> Sie können Content Moderator nicht verwenden, um nach Bildern zu suchen, auf denen Kinder in nicht zulässiger Weise dargestellt sind. Qualifizierte Organisationen können jedoch den [PhotoDNA-Clouddienst](https://www.microsoft.com/photodna "Microsoft PhotoDNA-Clouddienst") verwenden, um nach derartigen Inhalten zu suchen.

## <a name="what-it-includes"></a>Lieferumfang

Der Content Moderator-Dienst umfasst mehrere Webdienst-APIs, die sowohl über REST-Aufrufe als auch über ein .NET SDK verfügbar sind. Darüber hinaus steht ein Prüfungstool zur Verfügung, mit dem Personen den Dienst unterstützen und seine Moderationsfunktion verbessern oder optimieren können.

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

Mithilfe der Review-APIs können Sie Ihre Moderationspipeline für menschliche Reviewer integrieren. Verwenden Sie die Vorgänge [Jobs](review-api.md#jobs), [Reviews](review-api.md#reviews) und [Workflow](review-api.md#workflows), um mit dem [Review-Tool](#review-tool) (unten) Workflows für die Überprüfung durch Personen zu erstellen und zu automatisieren.

> [!NOTE]
> Die Workflow-API ist noch nicht im .NET SDK verfügbar, sie kann jedoch mit dem REST-Endpunkt verwendet werden.

![Blockdiagramm für Review-APIs von Content Moderator](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Überprüfungstool

Der Content Moderator-Dienst enthält auch das webbasierte [Überprüfungstool](Review-Tool-User-Guide/human-in-the-loop.md), das die Inhaltsüberprüfungen für menschliche Moderatoren hostet. Der Dienst wird durch die menschlichen Eingaben nicht trainiert. Die Zusammenarbeit zwischen Dienst und menschlichen Überprüfungsteams ermöglicht es Entwicklern jedoch, ein ausgewogenes Verhältnis zwischen Effizienz und Genauigkeit zu erzielen. Das Überprüfungstool bietet auch ein benutzerfreundliches Front-End für verschiedene Content Moderator-Ressourcen.

![Startseite des Content Moderator-Prüfungstools](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Datenschutz und Sicherheit

Wie bei allen Cognitive Services-Diensten müssen Entwickler, die den Content Moderator-Dienst nutzen, die Microsoft-Richtlinien zu Kundendaten beachten. Weitere Informationen finden Sie im Microsoft Trust Center auf der [Seite zu Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices).

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit Content Moderator im Webportal finden Sie unter [Schnellstart: Testen von Content Moderator im Web](quick-start.md). Alternativ können Sie eine [Schnellstartanleitung für eine Clientbibliothek oder REST-API](client-libraries.md) durchlaufen, um die grundlegenden Szenarien im Code zu implementieren.