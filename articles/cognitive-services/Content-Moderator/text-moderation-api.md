---
title: Textmoderation – Content Moderator
titleSuffix: Azure Cognitive Services
description: Verwenden Sie Textmoderation für potenziell unerwünschten Text, personenbezogene Daten und benutzerdefinierte Begriffslisten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: pafarley
ms.openlocfilehash: ae49a8738ba711ac6c77f2e299852ad61f70be56
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912904"
---
# <a name="learn-text-moderation-concepts"></a>Lernen von Textmoderationskonzepten

Verwenden Sie die Textmoderationsmodelle von Content Moderator, um Textinhalte zu analysieren.

Sie können den Inhalt auf Basis Ihrer Richtlinien und Schwellenwerte blockieren, genehmigen oder überprüfen (unter [Überprüfungen, Workflows und Aufträge](./review-api.md) erfahren Sie, wie Sie Überprüfungen durch Personen einrichten). Verwenden Sie die Textmoderationsmodelle, um Umgebungen, in denen Partner, Mitarbeiter und Kunden Textinhalte generieren, durch Moderation durch Personen zu ergänzen. Zu solchen Umgebungen zählen etwa Chatrooms, Diskussionsforen, Chatbots, E-Commerce-Kataloge und Dokumente.

Die Antwort des Diensts enthält folgende Informationen:

- Obszönitäten: begriffsbasierter Abgleich mit einer integrierten Liste anstößiger Begriffe in verschiedenen Sprachen
- Klassifizierung: Computergestützte Klassifizierung in drei Kategorien
- Personenbezogene Daten
- Automatisch korrigierter Text
- Ursprünglicher Text
- Sprache

## <a name="profanity"></a>Obszönitäten

Wenn die API anstößige Begriffe in einer der [unterstützten Sprachen](./language-support.md) erkennt, werden diese Begriffe in die Antwort aufgenommen. Die Antwort enthält auch deren Position (`Index`) im ursprünglichen Text. `ListId` im folgenden JSON-Beispielcode bezieht sich auf Begriffe aus [benutzerdefinierten Begriffslisten](try-terms-list-api.md) (sofern verfügbar).

```json
"Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }
```

> [!NOTE]
> Weisen Sie für den Parameter **language** entweder `eng` zu, oder lassen Sie ihn leer, um die computergestützte **Klassifizierungsantwort** (Vorschaufeature) zu erhalten. **Dieses Feature wird nur für Englisch unterstützt**.
>
> Verwenden Sie für die Erkennung **anstößiger Begriffe** den [ISO 639-3-Code](http://www-01.sil.org/iso639-3/codes.asp) der unterstützten Sprachen aus diesem Artikel, oder lassen Sie ihn leer.

## <a name="classification"></a>Klassifizierung

Das computergestützte **Textklassifizierungsfeature** von Content Moderator wird **nur für Englisch** unterstützt und hilft bei der Erkennung potenziell unerwünschter Inhalte. Die gekennzeichneten Inhalte werden je nach Kontext unter Umständen als ungeeignet bewertet. Das Feature vermittelt die Wahrscheinlichkeit jeder Kategorie und kann eine Überprüfung durch Personen empfehlen. Für das Feature wird ein trainiertes Modell verwendet, um Äußerungen zu identifizieren, die unter Umständen beleidigend, abfällig oder diskriminierend sind. Hierzu gehören auch Jargon und Abkürzungen sowie anstößige und absichtlich falsch geschriebene Wörter, die überprüft werden. 

Der folgende Auszug aus dem JSON-Auszug zeigt eine Beispielausgabe:

```json
"Classification": {
    "ReviewRecommended": true,
    "Category1": {
        "Score": 1.5113095059859916E-06
    },
    "Category2": {
        "Score": 0.12747249007225037
    },
    "Category3": {
        "Score": 0.98799997568130493
    }
}
```

### <a name="explanation"></a>Erklärung

- `Category1` gibt an, dass unter Umständen Sprache vorhanden ist, die in bestimmten Situationen als sexuell freizügig oder nicht jugendfrei betrachtet werden kann.
- `Category2` gibt an, dass unter Umständen Sprache vorhanden ist, die in bestimmten Situationen als zweideutig bzw. anzüglich oder als nur für Erwachsene bestimmt betrachtet werden kann.
- `Category3` gibt an, dass unter Umständen Sprache vorhanden ist, die in bestimmten Situationen als anstößig betrachtet werden kann.
- `Score` ist ein Wert zwischen 0 und 1. Je höher der Wert, desto höher die vom Modell ermittelte Wahrscheinlichkeit, dass die Kategorie zutreffend ist. Diese Funktion basiert nicht auf manuell programmierten Ergebnissen, sondern auf einem statistischen Modell. Es wird empfohlen, anhand Ihrer eigenen Inhalte zu testen, wie die jeweiligen Kategorien zu Ihren Anforderungen passen.
- `ReviewRecommended` ist entweder „true“ oder „false“ (abhängig von den internen Ergebnisschwellenwerten). Kunden müssen entscheiden, ob sie diesen Wert verwenden oder auf der Grundlage ihrer Inhaltsrichtlinien eigene Schwellenwerte festlegen möchten.

## <a name="personal-data"></a>Personenbezogene Daten

Das Feature für personenbezogene Daten erkennt, ob unter Umständen Daten dieser Art vorhanden sind:

- E-Mail-Adresse
- US-Postanschrift
- IP-Adresse
- US-Telefonnummer

Das folgende Beispiel zeigt eine exemplarische Antwort:

```json
"pii":{
  "email":[
      {
        "detected":"abcdef@abcd.com",
        "sub_type":"Regular",
        "text":"abcdef@abcd.com",
        "index":32
      }
  ],
  "ssn":[

  ],
  "ipa":[
      {
        "sub_type":"IPV4",
        "text":"255.255.255.255",
        "index":72
      }
  ],
  "phone":[
      {
        "country_code":"US",
        "text":"6657789887",
        "index":56
      }
  ],
  "address":[
      {
        "text":"1 Microsoft Way, Redmond, WA 98052",
        "index":89
      }
  ]
}
```

## <a name="auto-correction"></a>Autokorrektur

Angenommen, der Eingabetext lautet wie folgt („lzay“ und „f0x“ wurden absichtlich so geschrieben):

> The qu!ck brown f0x jumps over the lzay dog.

Bei Verwendung der Autokorrektur enthält die Antwort die korrigierte Version des Texts:

> The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Erstellen und Verwalten benutzerdefinierter Begriffslisten

Die standardmäßige globale Begriffsliste deckt zwar die meisten Fälle ab, in bestimmten Fällen möchten Sie Inhalte aber ggf. auf spezielle Begriffe im Zusammenhang mit Ihren geschäftlichen Anforderungen überprüfen. So kann es beispielsweise wünschenswert sein, Markennamen von Mitbewerbern aus Benutzerbeiträgen herauszufiltern.

> [!NOTE]
> Die Obergrenze liegt bei **fünf Benennungslisten** , wobei jede Liste **max. 10.000 Benennungen** enthalten kann.
>

Das folgende Beispiel zeigt die entsprechende Listen-ID:

```json
"Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }
```

Content Moderator bietet eine [Begriffslisten-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) mit Vorgängen für die Verwaltung benutzerdefinierter Begriffslisten. Beginnen Sie mit der [API-Konsole für Begriffslisten](try-terms-list-api.md), und verwenden Sie die REST-API-Codebeispiele. Falls Sie mit Visual Studio und C# vertraut sind, können Sie sich auch die [.NET-Schnellstartanleitung für Begriffslisten](term-lists-quickstart-dotnet.md) ansehen.

## <a name="next-steps"></a>Nächste Schritte

Testen Sie die APIs mit der [Textmoderations-API-Konsole](try-text-api.md). Weitere Informationen zur Einrichtung der Überprüfung durch Personen finden Sie auch unter [Überprüfungen, Workflows und Aufträge](./review-api.md).