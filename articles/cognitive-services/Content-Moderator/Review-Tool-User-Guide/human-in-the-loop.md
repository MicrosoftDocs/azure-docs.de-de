---
title: Kennenlernen der Konzepte des Prüfungstools – Content Moderator
titleSuffix: Azure Cognitive Services
description: Erfahren Sie etwas über das Content Moderator-Prüfungstool, eine Website, über die die Moderation von Überprüfungen mit künstlicher Intelligenz (KI) und durch Personen koordiniert wird.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: ddf4d17cd2631f5bc299e392c46bd9065bb13744
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146616"
---
# <a name="content-moderator-review-tool"></a>Content Moderator-Prüfungstool

Azure Content Moderator stellt Dienste bereit, um die Inhaltsmoderation mit maschinellem Lernen mit menschlichen Überprüfungen zu kombinieren. Die Website zum [Überprüfungstool](https://contentmoderator.cognitive.microsoft.com) ist ein benutzerfreundliches Front-End, das detaillierten Zugriff auf diese Dienste ermöglicht.

## <a name="what-it-does"></a>Funktionsbeschreibung

Bei Verwendung in Kombination mit den computergestützten Moderations-APIs ermöglicht das [Prüfungstool](https://contentmoderator.cognitive.microsoft.com) die Durchführung folgender Aufgaben im Inhaltsmoderationsablauf:

- Verwenden einer Gruppe von Tools für die Moderation von Inhalten in mehreren Formaten (Text, Bild und Video)
- Automatisieren der Erstellung von [Überprüfungen](../review-api.md#reviews) durch Personen aus den Ergebnissen der Moderations-API
- Zuweisen oder Eskalieren von Inhaltsüberprüfungen an mehrere, nach Inhaltskategorie oder Erfahrungsstufe organisierte Überprüfungsteams
- Verwenden von standardmäßigen oder benutzerdefinierten Logikfiltern ([Workflows](../review-api.md#workflows)) zum Sortieren und Nachverfolgen von Inhalten ohne jeglichen Code
- Verwenden Sie [Connectors](./configure.md#connectors) zum Verarbeiten von Inhalten mit Microsoft PhotoDNA, Textanalyse und Gesichtserkennungsdiensten zusätzlich zu den Content Moderator-APIs.
- Abrufen von Leistungsmetriken zu Ihren Inhaltsmoderationsprozessen

## <a name="review-tool-dashboard"></a>Prüfungstool-Dashboard

Auf der Registerkarte **Dashboard** können Sie wichtige Metriken für im Tool erfolgte Inhaltsüberprüfungen anzeigen. Es wird die Anzahl der gesamten, abgeschlossenen und ausstehenden Überprüfungen für Bild-, Text- und Videoinhalte angezeigt. 

Die Tabelle für **ausstehende Überprüfungen** zeigt die Aufschlüsselung der Benutzer und untergeordneten Teams, die über ausstehende oder abgeschlossene Überprüfungen verfügen, sowie die verbleibende SLA-Zeit. Sie können die Einträge in der Tabelle auswählen, um zu deren Überprüfungen zu wechseln. Mit dem Suchfeld oberhalb der Tabelle können Sie die Ergebnisse nach Teamnamen filtern, und mit dem Symbol **Filter** können Sie nach anderen Metriken filtern.

Der Wechsel zur Registerkarte mit **abgeschlossenen Überprüfungen** zeigt die Gesamtzahl der von Benutzern und untergeordneten Teams bearbeiteten oder abgeschlossenen Elemente an. Sie können diese Daten genauso filtern wie die ausstehenden Überprüfungen.

Wenn Sie auf den Text in der oberen rechten Ecke des Dashboards klicken, werden die täglichen persönliche Metriken angezeigt, die die Anzahl der abgeschlossenen Überprüfungen für die einzelnen Inhaltstypen angibt.

> [!div class="mx-imgBorder"]
> ![Prüfungstool-Dashboard in einem Browser](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Anmeldeinformationen für das Prüfungstool

Bei der Registrierung für das [Prüfungstool](https://contentmoderator.cognitive.microsoft.com) werden Sie aufgefordert, eine Azure-Region für Ihr Konto auszuwählen. Dies liegt daran, dass das [Überprüfungstool](https://contentmoderator.cognitive.microsoft.com) einen kostenlosen Testschlüssel für Azure Content Moderator-Dienste generiert. Sie benötigen diesen Schlüssel, um von einem REST-Aufruf oder Client-SDK auf einen der Dienste zuzugreifen. Sie können Ihren Schlüssel und die API-Endpunkt-URL durch Auswählen von **Admin** > **Credentials** (Administrator und Anmeldeinformationen) anzeigen.

> [!div class="mx-imgBorder"]
> ![Anmeldeinformationen in Content Moderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Zugreifen auf Ressourcen des Prüfungstools und zum Ändern von Einstellungen finden Sie unter [Konfigurieren des Prüfungstools](./configure.md).