---
title: Planen der App
titleSuffix: Language Understanding - Azure Cognitive Services
description: Beschreiben Sie kurz relevante App-Absichten und -Entitäten, und erstellen Sie dann Ihre Anwendungspläne in Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 9d54cff81f39f41b60800e9b33f3b4da1a735d85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60196234"
---
# <a name="plan-your-luis-app-with-subject-domain-intents-and-entities"></a>Planen Ihrer LUIS-App mit Thema, Absichten und Entitäten

Es ist wichtig, dass Sie Ihre App planen. Identifizieren Sie Ihre Domäne, einschließlich möglicher Absichten und Entitäten, die für Ihre Anwendung relevant sind.  

## <a name="identify-your-domain"></a>Identifizieren Ihrer Domäne

Eine LUIS-App baut auf einem domänenspezifischen Thema auf.  Beispielsweise könnten Sie eine Reise-App erstellen, die Buchungen von Tickets, Flügen, Hotels und Mietwagen ausführt. Eine andere App könnte Inhalte im Zusammenhang mit Sportaktivitäten, dem Nachverfolgen von Fitnessübungen und dem Festlegen von Zielen bereitstellen. Durch die Identifizierung der Domäne können Sie Wörter oder Ausdrücke finden, die für Ihre Domäne wichtig sind.

> [!TIP]
> LUIS bietet [vordefinierte Domänen](luis-how-to-use-prebuilt-domains.md) für viele häufige Szenarien.
> Überprüfen Sie, ob Sie eine vordefinierte Domäne als Ausgangspunkt für Ihre App verwenden können.

## <a name="identify-your-intents"></a>Identifizieren Ihrer Absichten

Überlegen Sie sich, welche [Absichten](luis-concept-intent.md) wichtig für die Aufgabe Ihrer Anwendung sind. Beispiel: eine Reise-App mit Funktionen zum Buchen eines Flugs und Überprüfen des Wetters am Ziel des Benutzers. Sie können die Absichten „BookFlight“ und „GetWeather“ für diese Aktionen definieren. In einer komplexeren App mit umfassenderen Funktionen gibt es weitere Absichten, und Sie sollten diese sorgfältig definieren, damit sie nicht zu spezifisch sind. Beispielsweise sind „BookFlight“ und „BookHotel“ möglicherweise separate Absichten, aber „BookInternationalFlight“ und „BookDomesticFlight“ könnten zu ähnlich sein.

> [!NOTE]
> Als bewährte Methode sollten Sie nur so viele Absichten verwenden, wie zum Ausführen der Funktionen Ihrer App erforderlich sind. Wenn Sie zu viele Absichten definieren, wird es für LUIS schwieriger, Äußerungen richtig zu klassifizieren. Wenn Sie zu wenige definieren, sind sie möglicherweise so allgemein gehalten, dass sie sich überschneiden.

## <a name="create-example-utterances-for-each-intent"></a>Erstellen von Beispieläußerungen für jede Absicht

Nachdem Sie die Absichten festgelegt haben, können Sie 10 bis 15 Beispieläußerungen für jede Absicht erstellen. Zunächst sollten Sie nicht weniger als diese Anzahl von Äußerungen oder viele Äußerungen für jede Absicht erstellen. Jede Äußerung sollte sich von der vorherigen Äußerung unterscheiden. Eine gute Vielfalt in den Äußerungen umfasst die Gesamtzahl der Wörter, die Wortwahl, die Verbform und die Interpunktion. 

## <a name="identify-your-entities"></a>Identifizieren Ihrer Entitäten

Identifizieren Sie in den Beispieläußerungen die Entitäten, die extrahiert werden sollen. Um einen Flug zu buchen, benötigen Sie einige Informationen wie das Ziel, das Datum, die Fluggesellschaft, die Ticketkategorie und die Reiseklasse. Sie erstellen Entitäten für diese Datentypen und markieren dann die [Entitäten](luis-concept-entity-types.md) in den Beispieläußerungen, weil sie für das Erreichen einer Absicht wichtig sind. 

Wenn Sie festlegen, welche Entitäten in Ihrer App verwendet werden sollen, bedenken Sie, dass es verschiedene Typen von Entitäten zum Aufzeichnen der Beziehungen zwischen Objekttypen gibt. Unter [Entitäten in LUIS](luis-concept-entity-types.md) finden Sie weitere Details zu den verschiedenen Typen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Ihre App trainiert und veröffentlicht wurde und Endpunktäußerungen erhält, sollten Sie planen, Vorhersageverbesserungen mit [aktivem Lernen](luis-how-to-review-endpoint-utterances.md), [Ausdruckslisten](luis-concept-feature.md) und [Mustern](luis-concept-patterns.md) zu implementieren. 


* Eine kurze exemplarische Vorgehensweise zum Erstellen einer LUIS-App finden Sie unter [Erstellen Ihrer ersten LUIS-App (Language Understanding Intelligent Service)](luis-get-started-create-app.md).
