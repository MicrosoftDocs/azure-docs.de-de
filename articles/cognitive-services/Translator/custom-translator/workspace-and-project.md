---
title: 'Definition: Arbeitsbereich und Projekt – Custom Translator'
titleSuffix: Azure Cognitive Services
description: Ein Arbeitsbereich ist ein Bereich zum Erstellen und Entwickeln Ihres benutzerdefinierten Übersetzungssystems. Er kann mehrere Projekte, Modelle und Dokumente enthalten. Ein Projekt ist ein Wrapper für Modelle, Dokumente und Tests. Jedes Projekt enthält automatisch alle Dokumente, die in den Arbeitsbereich hochgeladen werden und über das korrekte Sprachenpaar verfügen.
services: cognitive-services
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 312688ddccd6a65745b919e4c396a95f40a58d3e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66382333"
---
# <a name="what-is-a-custom-translator-workspace"></a>Definition: Custom Translator-Arbeitsbereich

Ein Arbeitsbereich ist ein Bereich zum Erstellen und Entwickeln Ihres benutzerdefinierten Übersetzungssystems. Er kann mehrere Projekte, Modelle und Dokumente enthalten. Alle Aktivitäten, die Sie in Custom Translator ausführen, befinden sich in einem bestimmten Arbeitsbereich.

Der Arbeitsbereich ist privat für Sie und die Personen, die Sie in Ihren Arbeitsbereich einladen. Nicht eingeladene Personen haben keinen Zugriff auf die Inhalte in Ihrem Arbeitsbereich. Sie können beliebig viele Personen in Ihren Arbeitsbereich einladen und deren Zugriff darauf jederzeit ändern oder entfernen. Sie können auch einen neuen Arbeitsbereich erstellen. Standardmäßig enthält ein Arbeitsbereich keine Projekte oder Dokumente, die sich in Ihren anderen Arbeitsbereichen befinden.

## <a name="what-is-a-custom-translator-project"></a>Definition: Custom Translator-Projekt

Ein Projekt ist ein Wrapper für Modelle, Dokumente und Tests. Jedes Projekt enthält automatisch alle Dokumente, die in den Arbeitsbereich hochgeladen werden und über das korrekte Sprachenpaar verfügen. Wenn Sie beispielsweise sowohl ein Projekt für die Sprachrichtung Englisch–Spanisch als auch ein Projekt für die Sprachrichtung Spanisch–Englisch haben, sind die gleichen Dokumente in beiden Projekten enthalten. Jedem Projekt ist eine Kategorie-ID (CategoryID) zugeordnet, die beim Abfragen der [V3-API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) für Übersetzungen verwendet wird. CategoryID ist ein Parameter, mit dem Übersetzungen aus einem benutzerdefinierten System abgerufen werden, das mit Custom Translator erstellt wurde.

## <a name="project-categories"></a>Projektkategorien

Die Kategorie bestimmt die Domäne – den zu verwendenden Terminologie- und Stilbereich – Ihres Projekts. Wählen Sie die Kategorie aus, die für Ihre Dokumente am relevantesten ist. In einigen Fällen beeinflusst die Wahl der Kategorie direkt das Verhalten von Custom Translator.

Es stehen zwei Gruppen von Grundmodellen zur Verfügung: „Allgemein“ und „Technologie“. Wenn Sie die Kategorie **Technologie** auswählen, werden die Grundmodelle vom Typ „Technologie“ verwendet. Bei jeder anderen Kategorieauswahl werden die allgemeinen Grundmodelle verwendet. Das Grundmodell „Technologie“ eignet sich gut für den Technologiebereich, liefert jedoch weniger gute Ergebnisse, wenn die zu übersetzenden Sätze nicht in diesen Bereich fallen. Die Kategorie „Technologie“ sollte daher nur gewählt werden, wenn die Sätze eindeutig in den Technologiebereich fallen.

Sie können in einem Arbeitsbereich Projekte für das gleiche Sprachenpaar in verschiedenen Kategorien erstellen. Custom Translator verhindert die Erstellung von Projektduplikaten mit demselben Sprachenpaar und derselben Kategorie. Diese Einschränkung lässt sich vermeiden, indem Sie Ihrem Projekt eine Bezeichnung zuweisen. Verwenden Sie Bezeichnungen nur, wenn Sie Übersetzungssysteme für mehrere Kunden erstellen, da das Hinzufügen einer eindeutigen Bezeichnung zu Ihrem Projekt in CategoryID Ihres Projekt angezeigt wird.

## <a name="project-labels"></a>Projektbezeichnungen

Mit Custom Translator können Sie Ihrem Projekt eine Projektbezeichnung zuweisen. Die Projektbezeichnung dient zur Unterscheidung von Projekten mit gleichem Sprachenpaar und gleicher Kategorie. Es wird empfohlen, Projektbezeichnungen nur dann zu verwenden, wenn es notwendig ist.

Die Projektbezeichnung wird als Teil von CategoryID verwendet. Wenn die Projektbezeichnung nicht festgelegt oder projektübergreifend identisch festgelegt wird, verfügen Projekte mit der gleichen Kategorie und *verschiedenen* Sprachenpaaren über die gleiche CategoryID. Der Vorteil besteht bei diesem Ansatz darin, dass Ihre Kunden beim Verwenden der Textübersetzungs-API zwischen den Sprachen wechseln können, ohne einen für jedes Projekt einzigartigen CategoryID-Wert zu beachten.

Ein Beispiel: Für Übersetzungen im Bereich Technologie aus dem Englischen ins Französische und aus dem Französischen ins Englische würden Sie zwei Projekte erstellen: eines für Englisch -\> Französisch, und eins für Französisch -\> Englisch. Beide Projekte erhalten die gleiche Kategorie (Technologie) und die Projektbezeichnung bleibt leer. CategoryID würde für beide Projekte übereinstimmen, sodass Sie die API sowohl für englische als auch für französische Übersetzungen abfragen könnten, ohne CategoryID ändern zu müssen.

Wenn Sie ein Sprachdienstleister sind und mehrere Kunden mit unterschiedlichen Modellen bedienen möchten, die die gleiche Kategorie und das gleiche Sprachenpaar verwenden, sind unterschiedliche Projektbezeichnungen hilfreich, um zwischen den Kunden zu unterscheiden.

## <a name="next-steps"></a>Nächste Schritte

- Im Artikel über [Training und Modelle](training-and-model.md) erfahren Sie, wie Sie ein Übersetzungsmodell effizient erstellen.
