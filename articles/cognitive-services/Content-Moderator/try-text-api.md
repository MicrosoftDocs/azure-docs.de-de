---
title: Moderieren von Text mit der Textmoderations-API – Content Moderator
titleSuffix: Azure Cognitive Services
description: Testen Sie die Textmoderation mit der Textmoderations-API in der Onlinekonsole.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 664c4289cbfa1f6ce2fce9f9f83b0240bd2d592c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001999"
---
# <a name="moderate-text-from-the-api-console"></a>Moderieren von Text über die API-Konsole

Verwenden Sie die [Textmoderations-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) in Azure Content Moderator, um Ihren Textinhalt auf Obszönitäten zu überprüfen und ihn mit benutzerdefinierten und geteilten Listen abzugleichen.

## <a name="get-your-api-key"></a>Abrufen Ihres API-Schlüssels

Bevor Sie die API in der Onlinekonsole testen können, benötigen Sie Ihren Abonnementschlüssel. Dieser befindet sich auf der Registerkarte **Einstellungen** im Feld **Ocp-Apim-Subscription-Key**. Weitere Informationen finden Sie in der [Übersicht](overview.md).

## <a name="navigate-to-the-api-reference"></a>Navigieren zur API-Referenz

Greifen Sie auf die [Referenz zur Textmoderations-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) zu. 

  Die Seite **Text – Screen** (Text – Überprüfen) wird geöffnet.

## <a name="open-the-api-console"></a>Öffnen der API-Konsole

Wählen Sie für **API-Testkonsole öffnen** die Region aus, die Ihrem Standort am ehesten nahekommt. 

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

### <a name="content-type"></a>Inhaltstyp

Wählen Sie für **Content-Type** den Inhaltstyp aus, den Sie überprüfen möchten. Verwenden Sie für dieses Beispiel den Standardinhaltstyp **text/plain**. Geben Sie in das Feld **Ocp-Apim-Subscription-Key** Ihren Abonnementschlüssel ein.

### <a name="sample-text-to-scan"></a>Beispieltext für Scan

Geben Sie im Feld **Anforderungstext** Text ein. Das folgende Beispiel enthält im Text einen absichtlichen Rechtschreibfehler.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>Analysieren der Antwort

In der folgenden Antwort sind die verschiedenen Erkenntnisse dargestellt, die über die API gewonnen werden können. Diese umfassen potenzielle Obszönitäten, personenbezogene Daten, Klassifizierungen (Vorschauversion) und die automatisch korrigierte Version.

> [!NOTE]
> Das computergestützte Feature „Klassifizierung“ befindet sich in der Vorschauphase und unterstützt nur Englisch.

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
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
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

Eine ausführliche Erläuterung aller Abschnitte in der JSON-Antwort finden Sie im Konzeptleitfaden [Lernen von Textmoderationskonzepten](text-moderation-api.md).

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die REST-API im Code, oder befolgen Sie die Anweisungen im [.NET SDK-Schnellstart](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) für die Integration mit Ihrer Anwendung.