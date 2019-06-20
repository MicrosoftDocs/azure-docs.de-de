---
title: Datenspeicher
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS speichert Daten verschlüsselt in einem Azure-Datenspeicher für die im Schlüssel angegebene Region.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: diberry
ms.openlocfilehash: a1093c2a6303b453a17a52058303913de5ecfa8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60812952"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Speicherung und Entfernung von Daten in Language Understanding (LUIS) Cognitive Services
LUIS speichert Daten verschlüsselt in einem Azure-Datenspeicher für die im Schlüssel angegebene Region. Diese Daten werden für 30 Tage gespeichert. 

## <a name="export-and-delete-app"></a>Exportieren und Löschen von Apps
Benutzer haben volle Kontrolle über das [Exportieren](luis-how-to-start-new-app.md#export-app) und [Löschen](luis-how-to-start-new-app.md#delete-app) ihrer Apps. 

## <a name="utterances"></a>Äußerungen

Äußerungen können an zwei verschiedenen Speicherorten gespeichert werden. 

* Während des **Erstellungsprozesses** werden Äußerungen erstellt und in der Absicht gespeichert. Für eine erfolgreiche LUIS-App sind Äußerungen in Absichten erforderlich. Sobald die App veröffentlicht ist und Abfragen am Endpunkt empfängt, bestimmt die Abfragezeichenfolge `log=false` der Endpunktanforderung, ob die Endpunktäußerung gespeichert wird. Wenn der Endpunkt gespeichert wurde, wird er Teil der Äußerungen für aktives Lernen, die im Abschnitt **Erstellen** des Portals und im Abschnitt **Endpunktäußerungen überprüfen** zu finden sind. 
* Wenn Sie **Endpunktäußerungen** überprüfen und einer Absicht eine Äußerung hinzufügen, wird die Äußerung nicht mehr als Teil der zu überprüfenden Endpunktäußerungen gespeichert. Sie wird den Absichten der App hinzugefügt. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Löschen von Beispieläußerungen aus einer Absicht
Löschen Sie die Beispieläußerungen für das Training von [LUIS](luis-reference-regions.md). Wenn Sie eine Beispieläußerung aus Ihrer LUIS-App löschen, wird sie vom LUIS-Webdienst entfernt und ist nicht für den Export verfügbar.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Löschen von Äußerungen bei der Überprüfung durch aktives Lernen

Sie können Äußerungen aus der Liste der Benutzeräußerungen, die LUIS auf der Seite **[Review endpoint utterance](luis-how-to-review-endpoint-utterances.md)** (Endpunktäußerungen überprüfen) vorschlägt, löschen. Das Löschen von Äußerungen aus dieser Liste verhindert, dass diese empfohlen werden. Sie werden aber nicht aus den Protokollen gelöscht.

Wenn Sie keine Äußerungen zum aktiven Lernen wünschen, können Sie das [aktive Lernen deaktivieren](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Wenn Sie das aktive Lernen deaktivieren, wird auch die Protokollierung deaktiviert.

### <a name="disable-logging-utterances"></a>Deaktivieren von Protokollierungsäußerungen
[Das Deaktivieren des aktiven Lernens](luis-how-to-review-endpoint-utterances.md#disable-active-learning) deaktiviert die Protokollierung.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Löschen eines Kontos
Wenn Sie ein Konto löschen, werden alle Apps zusammen mit ihren Beispieläußerungen und Protokollen gelöscht. Die Daten werden für 60 Tage beibehalten, bevor das Konto und die Daten dauerhaft gelöscht werden.

Das Löschen von Konten erfolgt über die Seite **Einstellungen**. Wählen Sie Ihren Kontonamen rechts oben auf der Navigationsleiste aus, um die Seite **Einstellungen** anzuzeigen.

## <a name="data-inactivity-as-an-expired-subscription"></a>Dateninaktivität als Zeichen für ein abgelaufenes Abonnement
Im Zusammenhang mit der Beibehaltung und Löschung von Daten kann eine inaktive LUIS-App nach dem _Ermessen von Microsoft_ als abgelaufenes Abonnement behandelt werden. Eine App gilt als inaktiv, wenn sie für die letzten 90 Tage die folgenden Kriterien erfüllt: 

* Es wurden **keine** Aufrufe getätigt.
* Sie wurde nicht geändert.
* Ihr ist zurzeit kein aktueller Schlüssel zugewiesen.
* Es hat sich kein Benutzer bei ihr angemeldet.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfahren Sie mehr zum Exportieren und Löschen von Apps.](luis-how-to-start-new-app.md)
