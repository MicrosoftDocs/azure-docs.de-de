---
title: Moderieren von Text mit der Textmoderations-API – Content Moderator
titlesuffix: Azure Cognitive Services
description: Testen Sie die Textmoderation mit der Textmoderations-API in der Onlinekonsole.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 21209548d1cfe7b6eebb1757e817a12c797e78a9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66688839"
---
# <a name="moderate-text-from-the-api-console"></a>Moderieren von Text über die API-Konsole

Verwenden Sie die [Textmoderations-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) in Azure Content Moderator, um Ihren Textinhalt auf Obszönitäten zu überprüfen und ihn mit benutzerdefinierten und geteilten Listen abzugleichen.

## <a name="get-your-api-key"></a>Abrufen Ihres API-Schlüssels

Bevor Sie die API in der Onlinekonsole testen können, benötigen Sie Ihren Abonnementschlüssel. Dieser befindet sich auf der Registerkarte **Einstellungen** im Feld **Ocp-Apim-Subscription-Key**. Weitere Informationen finden Sie in der [Übersicht](overview.md).

## <a name="navigate-to-the-api-reference"></a>Navigieren zur API-Referenz

Greifen Sie auf die [Referenz zur Textmoderations-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) zu. 

  Die Seite **Text – Screen** (Text – Überprüfen) wird geöffnet.

## <a name="open-the-api-console"></a>Öffnen der API-Konsole

Wählen Sie für **Open API testing console** (API-Testkonsole öffnen) die Region aus, die Ihrem Standort am ehesten nahekommt. 

  ![Seite „Text – Screen“ (Text – Überprüfen) – Auswahl der Region](images/test-drive-region.png)

  Die API-Konsole **Text – Screen** (Text – Überprüfen) wird geöffnet.

## <a name="select-the-inputs"></a>Auswählen der Eingaben

### <a name="parameters"></a>Parameter

Wählen Sie die Abfrageparameter aus, die Sie für Ihre Textüberprüfung verwenden möchten. Verwenden Sie beispielsweise den Standardwert für **language**. Sie können das Feld auch leer lassen, da der Vorgang die zutreffende Sprache im Rahmen der Ausführung automatisch erkennt.

> [!NOTE]
> Weisen Sie für den Parameter **language** entweder `eng` zu, oder lassen Sie ihn leer, um die computergestützte **Klassifizierungsantwort** (Vorschaufeature) zu erhalten. **Dieses Feature wird nur für Englisch unterstützt**.
>
> Verwenden Sie für die Erkennung **anstößiger Begriffe** den [ISO 639-3-Code](http://www-01.sil.org/iso639-3/codes.asp) der unterstützten Sprachen aus diesem Artikel, oder lassen Sie ihn leer.

Wählen Sie für **autocorrect**, **PII** und **classify (preview)** die Option **true**. Lassen Sie das Feld **ListId** leer.

  ![Konsole „Text – Screen“ (Text – Überprüfen) – Abfrageparameter](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Content-Typ

Wählen Sie für **Content-Type** den Inhaltstyp aus, den Sie überprüfen möchten. Verwenden Sie für dieses Beispiel den Standardinhaltstyp **text/plain**. Geben Sie in das Feld **Ocp-Apim-Subscription-Key** Ihren Abonnementschlüssel ein.

### <a name="sample-text-to-scan"></a>Beispieltext für Scan

Geben Sie im Feld **Anforderungstext** Text ein. Das folgende Beispiel enthält im Text einen absichtlichen Rechtschreibfehler.

> [!NOTE]
> Die ungültige US-Sozialversicherungsnummer im folgenden Beispieltext ist beabsichtigt. Hier soll das Format der Beispieleingabe und -ausgabe vermittelt werden.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>Analysieren der Antwort

In der folgenden Antwort sind die verschiedenen Erkenntnisse dargestellt, die über die API gewonnen werden können. Diese umfassen potenzielle Obszönitäten, personenbezogene Daten, Klassifizierungen (Vorschauversion) und die automatisch korrigierte Version.

> [!NOTE]
> Das computergestützte Feature „Klassifizierung“ befindet sich in der Vorschauphase und unterstützt nur Englisch.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation":null,
"PII":{  
  "Email":[  
    {  
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[  
    {  
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[  
    {  
      "CountryCode":"US",
      "Text":"6657789887",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"870 608 4000",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 870 608 4000",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0344 800 2400",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0800 820 3300",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1 Microsoft Way, Redmond, WA 98052",
      "Index":89
    }
  ],
  "SSN":[  
    {  
      "Text":"999999999",
      "Index":56
    },
    {  
      "Text":"999-99-9999",
      "Index":266
    }
  ]
},
"Classification":{  
  "ReviewRecommended":true,
  "Category1":{  
    "Score":1.5113095059859916E-06
  },
  "Category2":{  
    "Score":0.12747249007225037
  },
  "Category3":{  
    "Score":0.98799997568130493
  }
},
"Language":"eng",
"Terms":[  
  {  
    "Index":21,
    "OriginalIndex":21,
    "ListId":0,
    "Term":"crap"
  }
],
"Status":{  
  "Code":3000,
  "Description":"OK",
  "Exception":null
},
"TrackingId":"2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

Eine ausführliche Erläuterung aller Abschnitte in der JSON-Antwort finden Sie im Konzeptleitfaden [Lernen von Textmoderationskonzepten](text-moderation-api.md).

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die REST-API in Ihrem Code, oder beginnen Sie damit, den [Schnellstart: Analysieren von Text auf anstößige Inhalte in C#](text-moderation-quickstart-dotnet.md) in Ihre Anwendung zu integrieren.
