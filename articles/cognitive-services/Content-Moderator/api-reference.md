---
title: API-Referenz – Content Moderator
titlesuffix: Azure Cognitive Services
description: Erfahren Sie mehr über die verschiedenen Inhaltsmoderations- und die Überprüfungs-APIs für Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: sajagtap
ms.openlocfilehash: f802c64e141e8757f633d82ad577fa50f7d076fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66688887"
---
# <a name="content-moderator-api-reference"></a>Content Moderator-API-Referenz

Sie können die ersten Schritte mit Azure Content Moderator-APIs folgendermaßen unternehmen:

- [Abonnieren Sie die Content Moderator-API](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) im Azure-Portal.
- Unter [Testen von Content Moderator im Web](quick-start.md) erfahren Sie, wie Sie sich für das [Content Moderator-Prüfungstool](https://contentmoderator.cognitive.microsoft.com/) registrieren.

## <a name="moderation-apis"></a>Moderations-APIs

Sie können die folgenden Content Moderator-APIs zum Einrichten Ihrer Workflows für die Beitragsmoderation verwenden.

| BESCHREIBUNG | Verweis |
| -------------------- |-------------|
| **Bildmoderations-API**<br /><br />Durchsuchen Sie Bilder und erkennen Sie anzügliche sowie nur für Erwachsene geeignete Inhalte mithilfe von Tags, Zuverlässigkeitsbewertungen und anderen extrahierten Informationen. <br /><br />Anhand dieser Informationen können Sie den Inhalt in Ihrem Workflow für die Beitragsmoderation veröffentlichen, ablehnen oder überprüfen. <br /><br />| [Bildmoderations-API-Referenz](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Bildmoderations-API-Referenz")   |
| **Textmoderations-API**<br /><br />Durchsuchen Sie Textinhalt. Es werden anstößige Ausdrücke und persönliche Daten zurückgegeben. <br /><br />Anhand dieser Informationen können Sie den Inhalt in Ihrem Workflow für die Beitragsmoderation veröffentlichen, ablehnen oder überprüfen.<br /><br /> | [Textmoderations-API-Referenz](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Textmoderations-API-Referenz")   |
| **Videomoderations-API**<br /><br />Durchsuchen Sie Videos und erkennen Sie anzügliche sowie nur für Erwachsene geeignete Inhalte. <br /><br />Anhand dieser Informationen können Sie den Inhalt in Ihrem Workflow für die Beitragsmoderation veröffentlichen, ablehnen oder überprüfen.<br /><br /> | [Videomoderations-API – Überblick](video-moderation-api.md "Videomoderations-API – Überblick")   |
| **Listenverwaltungs-API**<br /><br />Erstellen und verwalten Sie benutzerdefinierte Ausschluss- und Aufnahmeliste von Bildern und Text. Nach der Aktivierung überprüfen die Vorgänge **Image - Match** und **Text - Screen**, ob eine Fuzzyübereinstimmung zwischen dem übermittelten Inhalt und Ihren benutzerdefinierten Listen besteht. <br /><br />Aus Effizienzgründen können Sie den auf maschinellen Lernen basierenden Moderationsschritt überspringen.<br /><br /> | [Listenverwaltungs-API-Referenz](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "Listenverwaltungs-API-Referenz")   |

## <a name="review-apis"></a>Überprüfen von APIs

Die Überprüfungs-APIs enthalten die folgenden Komponenten:

| BESCHREIBUNG | Verweis |
| -------------------- |-------------|
| **Aufträge**<br /><br /> Starten Sie Moderationsworkflows zum Durchsuchen und Überprüfen von Bild- und Textinhalten. Ein Moderationsauftrag durchsucht Ihre Inhalte mithilfe der Bildmoderations-API und der Textmoderations-API. Moderationsaufträge verwenden definierte und Standardworkflows, um Überprüfungen zu generieren. <br /><br />Nachdem ein menschlicher Moderator die automatisch zugewiesenen Tags und Vorhersagedaten überprüft und eine Entscheidung zur Inhaltsmoderation getroffen hat, sendet die Überprüfungs-API alle Informationen an Ihren API-Endpunkt.<br /><br /> | [Auftragsreferenz](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Auftragsreferenz")   |
| **Überprüfungen**<br /><br />Verwenden Sie das Prüfungstool, um direkt Bild- oder Textüberprüfungen für menschliche Moderatoren zu erstellen.<br /><br /> Nachdem ein menschlicher Moderator die automatisch zugewiesenen Tags und Vorhersagedaten überprüft und eine Entscheidung zur Inhaltsmoderation getroffen hat, sendet die Überprüfungs-API alle Informationen an Ihren API-Endpunkt.<br /><br /> | [Überprüfungsreferenz](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Überprüfungsreferenz")   |
| **Workflows**<br /><br />Erstellen, aktualisieren und erhalten Sie Details zu den benutzerdefinierten Workflows, die Ihr Team erstellt. Mithilfe des Prüfungstools können Sie Workflows definieren. <br /> <br />Workflows verwenden normalerweise Content Moderator, können aber auch bestimmte andere APIs verwenden, die als Konnektoren im Prüfungstool verfügbar sind.<br /><br /> | [Workflowreferenz](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Workflowreferenz")   |