---
title: Kennenlernen der Konzepte des Prüfungstools – Content Moderator
titlesuffix: Azure Cognitive Services
description: Erfahren Sie etwas über das Content Moderator-Prüfungstool, eine Website, über die die Moderation von Überprüfungen mit künstlicher Intelligenz (KI) und durch Personen koordiniert wird.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: sajagtap
ms.openlocfilehash: b7ec997fd3e9bfe294050893d80fd57a96a47aae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61269983"
---
# <a name="content-moderator-review-tool"></a>Content Moderator-Prüfungstool

Azure Content Moderator umfasst Dienste für die Kombination der Inhaltsmoderation mit maschinellem Lernen und Überprüfungen durch Personen. Die Website des [Prüfungstools](https://contentmoderator.cognitive.microsoft.com) ist ein benutzerfreundliches Front-End, das einen detaillierten Zugriff auf diese Dienste ermöglicht.

![Prüfungstool-Dashboard in einem Browser](./images/0-dashboard.png)

## <a name="what-it-does"></a>Funktionsbeschreibung

Bei Verwendung in Kombination mit den computergestützten Moderations-APIs ermöglicht das [Prüfungstool](https://contentmoderator.cognitive.microsoft.com) die Durchführung folgender Aufgaben im Inhaltsmoderationsablauf:

- Verwenden einer Gruppe von Tools für die Moderation von Inhalten in mehreren Formaten (Text, Bild und Video)
- Automatisieren der Erstellung von [Überprüfungen](../review-api.md#reviews) durch Personen aus den Ergebnissen der Moderations-API
- Zuweisen oder Eskalieren von Inhaltsüberprüfungen an mehrere, nach Inhaltskategorie oder Erfahrungsstufe organisierte Überprüfungsteams
- Verwenden von standardmäßigen oder benutzerdefinierten Logikfiltern ([Workflows](../review-api.md#workflows)) zum Sortieren und Nachverfolgen von Inhalten ohne jeglichen Code
- Verwenden von [Konnektoren](./configure.md#connectors) zum Verarbeiten von Inhalten mit Microsoft PhotoDNA, Textanalyse und Gesichtserkennungs-APIs zusätzlich zu den Content Moderator-APIs
- Erstellen eines benutzerdefinierten Konnektors zum Erstellen von Workflows für APIs oder Geschäftsprozesse
- Abrufen von Leistungsmetriken zu Ihren Inhaltsmoderationsprozessen

## <a name="review-tool-dashboard"></a>Prüfungstool-Dashboard

Auf der Registerkarte **Dashboard** können Sie wichtige Metriken für im Tool erfolgte Inhaltsüberprüfungen anzeigen. Es wird die Anzahl der gesamten, abgeschlossenen und ausstehenden Überprüfungen für Bild-, Text- und Videoinhalte angezeigt. Zudem werden die Aufschlüsselung der Benutzer und Teams, die Überprüfungen durchgeführt haben, sowie die angewandten Moderationsmarkierungen angezeigt.

![Dashboards anzeigen](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Anmeldeinformationen für das Prüfungstool

Bei der Registrierung für das [Prüfungstool](https://contentmoderator.cognitive.microsoft.com) werden Sie aufgefordert, eine Azure-Region für Ihr Konto auszuwählen. Dies ist darauf zurückzuführen, dass das [Prüfungstool](https://contentmoderator.cognitive.microsoft.com) einen kostenlosen Testschlüssel für Azure Content Moderator-Dienste generiert. Diesen Schlüssel benötigen Sie für den Zugriff auf die Dienste über einen REST-Aufruf oder ein Client-SDK. Sie können Ihren Schlüssel und die API-Endpunkt-URL durch Auswählen von **Einstellungen** > **Anmeldeinformationen** anzeigen.

![Anmeldeinformationen in Content Moderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Zugreifen auf Ressourcen des Prüfungstools und zum Ändern von Einstellungen finden Sie unter [Konfigurieren des Prüfungstools](./configure.md).