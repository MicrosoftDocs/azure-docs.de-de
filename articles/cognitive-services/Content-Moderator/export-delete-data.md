---
title: Exportieren oder Löschen von Benutzerdaten – Content Moderator
titleSuffix: Azure Cognitive Services
description: Sie haben vollständige Kontrolle über Ihre Daten. Erfahren Sie, wie Sie Daten in Content Moderator anzeigen, exportieren oder löschen können.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 9f74fdc9cd30e1dfbd4df6c94842a9dccb435ef4
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913652"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportieren oder Löschen von Benutzerdaten aus Content Moderator

Content Moderator sammelt Benutzerdaten, um den Dienst zu betreiben. Benutzer haben jedoch mithilfe des [Prüfungstool](https://contentmoderator.cognitive.microsoft.com/) und der [Moderations- und Überprüfungs-APIs](./api-reference.md) vollständige Kontrolle über das Anzeigen, Exportieren und Löschen ihrer Daten.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Weitere Informationen zum Exportieren und Löschen von Benutzerdaten aus Content Moderator finden Sie in der folgenden Tabelle.

| Data | Exportvorgang | Löschvorgang |
| ---- | ---------------- | ---------------- |
| Kontoinformationen (Abonnementschlüssel) | – | Der Löschvorgang wird über das Azure-Portal (Azure-Abonnements) durchgeführt. Verwenden Sie alternativ die Schaltfläche **Team löschen** auf der Seite „Teameinstellungen“ auf der [Benutzeroberfläche für die Überprüfung](https://contentmoderator.cognitive.microsoft.com/). |
| Bilder für benutzerdefinierte Vergleiche | Rufen Sie die [API zum Abrufen der Bild-IDs](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676) auf. Bilder werden in einem unidirektionalen proprietären Hashformat gespeichert, und es gibt keine Möglichkeit, das tatsächliche Bild zu extrahieren. | Rufen Sie die [API zum Löschen aller Bilder](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686) auf. Löschen Sie die Content Moderator-Ressource alternativ über das Azure-Portal. |
| Begriffe für benutzerdefinierte Vergleiche | Rufen Sie die [API zum Abrufen aller Begriffe](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) auf. | Rufen Sie die [API zum Löschen aller Begriffe](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d) auf. Löschen Sie die Content Moderator-Ressource alternativ über das Azure-Portal. |
| Tags | – | Verwenden Sie das Symbol **Löschen** , das für jedes Tag auf der Einstellungsseite für Tags der Benutzeroberfläche für die Überprüfung verfügbar ist. Verwenden Sie alternativ die Schaltfläche **Team löschen** auf der Seite „Teameinstellungen“ auf der [Benutzeroberfläche für die Überprüfung](https://contentmoderator.cognitive.microsoft.com/). |
| Überprüfungen | Rufen Sie die [API zum Abrufen der Überprüfungen](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) auf. | Verwenden Sie die Schaltfläche **Team löschen** auf der Seite „Teameinstellungen“ auf der [Benutzeroberfläche für die Überprüfung](https://contentmoderator.cognitive.microsoft.com/).
| Benutzer | – | Verwenden Sie das Symbol **Löschen** , das für jeden Benutzer auf Seite „Teameinstellungen“ der [Benutzeroberfläche für die Überprüfung](https://contentmoderator.cognitive.microsoft.com/) verfügbar ist. Verwenden Sie alternativ die Schaltfläche **Team löschen** auf der Seite „Teameinstellungen“ auf der [Benutzeroberfläche für die Überprüfung](https://contentmoderator.cognitive.microsoft.com/). |