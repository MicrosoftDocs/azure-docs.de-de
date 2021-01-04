---
title: 'Migrieren zu V3: Translator'
titleSuffix: Azure Cognitive Services
description: Dieser Artikel enthält die Schritte für die Migration von V2 zu V3 für Azure Cognitive Services Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: f22213bf192b12e046bab12346e5d5d83fb36b96
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94364274"
---
# <a name="translator-v2-to-v3-migration"></a>Translator-Migration von V2 zu V3

> [!NOTE]
> V2 wurde am 30. April 2018 eingestellt. Migrieren Sie Ihre Anwendungen zu V3, um die Vorteile der neuen Funktionen zu nutzen, die ausschließlich in V3 verfügbar sind.
> 
> Der Microsoft Translator-Hub wurde am 17. Mai 2019 eingestellt. [Anzeigen von wichtigen Migrationsinformationen und Datumsangaben](https://www.microsoft.com/translator/business/hub/).  

Das Microsoft Translator-Team hat die Version 3 (V3) von Translator veröffentlicht. Dieses Release enthält neue Features, veraltete Methoden und ein neues Format für das Senden und Empfangen von Daten an bzw. vom Microsoft Translator-Dienst. Dieses Dokument stellt Informationen für das Ändern von Anwendungen für die Verwendung von V3 bereit. 

Am Ende dieses Dokuments finden Sie nützliche Links, unter denen Sie weitere Informationen finden.

## <a name="summary-of-features"></a>Zusammenfassung der Features

* Keine Ablaufverfolgung: In V3 gilt das Feature „Keine Ablaufverfolgung“ für alle Tarife im Azure-Portal. Dieses Feature bedeutet, dass kein Text, der an die V3-API übermittelt wird, von Microsoft gespeichert wird.
* JSON: XML wird durch JSON ersetzt. Alle Daten, die an diesen Dienst gesendet und von ihm empfangen werden, sind im JSON-Format.
* Mehrere Zielsprachen in einer einzelnen Anforderung: Die Translate-Methode akzeptiert mehrere Zielsprachen für die Übersetzung einer einzelnen Anforderung. Eine einzelne Anforderung kann also beispielsweise von Englisch in Deutsch, Spanisch und Japanisch (oder in eine andere Gruppe von Sprachen) übersetzt werden.
* Bilinguales Wörterbuch: Eine Methode für ein bilinguales Wörterbuch wurde zur API hinzugefügt. Diese Methode enthält „lookup“ und „examples“.
* Transkription: Die Transliterate-Methode wurde zur API hinzugefügt. Diese Methode konvertiert Wörter und Sätze in einem Skript (z.B. Arabisch) in ein anderes Skript (z.B. Latein).
* Sprachen: Eine neue Languages-Methode stellt Sprachinformationen im JSON-Format bereit, die mit den Methoden „Translate“, „Dictionary“ und „Transliterate“ verwendet werden können.
* Erweiterung der Translate-Methode: Neue Funktionen wurden zur Translate-Methode hinzugefügt, um einige der Features zu unterstützen, die in der V2-API als separate Methoden enthalten waren. Ein Beispiel dafür ist TranslateArray.
* Methode „Speak“: Die Sprachsynthesefunktion wird in Microsoft Translator nicht mehr unterstützt. Die Sprachsynthesefunktion ist über den [Microsoft Speech-Dienst](../speech-service/text-to-speech.md) verfügbar.

Die folgende Liste der V2- und V3-Methoden enthält die V3-Methoden und -APIs, die die Funktionen von V2 übernehmen.

| API-Methode in V2   | API-Kompatibilität in V3 |
|:----------- |:-------------|
| `Translate`     | [Translate](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Translate](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Sprachen](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Sprachen](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Microsoft Speech-Dienst](../speech-service/language-support.md#text-to-speech)         |
| `Speak`     | [Microsoft Speech-Dienst](../speech-service/text-to-speech.md)          |
| `Detect`     | [Detect](reference/v3-0-detect.md)         |
| `DetectArray`     | [Detect](reference/v3-0-detect.md)         |
| `AddTranslation`     | Das Feature wird nicht mehr unterstützt.       |
| `AddTranslationArray`    | Das Feature wird nicht mehr unterstützt.          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Das Feature wird nicht mehr unterstützt.         |
| `GetTranslationsArray`      | Das Feature wird nicht mehr unterstützt.         |

## <a name="move-to-json-format"></a>Wechsel zum JSON-Format

In Microsoft Translator V2 wurden Daten im XML-Format akzeptiert und zurückgegeben. In V3 sind alle mithilfe der API gesendeten und empfangenen Daten im JSON-Format. XML wird in V3 nicht mehr akzeptiert oder zurückgegeben.

Diese Änderung wirkt sich auf verschiedene Aspekte von Anwendungen aus, die für die Textübersetzungs-API von V2 geschrieben wurden. Beispiel: Die Sprachen-API gibt Sprachinformationen für die Textübersetzung, Transkription und zwei Wörterbuchmethoden zurück. Sie können alle Sprachinformationen für alle Methoden in einem Aufruf oder einzeln anfordern.

Die Languages-Methode erfordert keine Authentifizierung. Klicken Sie auf den folgenden Link, um alle Sprachinformationen für V3 im JSON-Format anzuzeigen:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Authentifizierungsschlüssel

Der Authentifizierungsschlüssel, den Sie für V2 verwenden, wird in V3 akzeptiert. Sie müssen kein neues Abonnement erstellen. Während der Migrationsphase von einem Jahr können Sie V2 und V3 in Ihren Apps mischen. Dadurch wird das Veröffentlichen neuer Versionen vereinfacht, während Sie noch von V2 (XML) zu V3 (JSON) migrieren.

## <a name="pricing-model"></a>Preismodell

Microsoft Translator V3 wird genau wie V2 berechnet: pro Zeichen, einschließlich Leerzeichen. Durch die neuen Features in V3 werden einige Änderungen daran vorgenommen, welche Zeichen abgerechnet werden.

| V3-Methode   | Abgerechnete Zeichen |
|:----------- |:-------------|
| `Languages`     | Wenn keine Zeichen übermittelt werden, werden keine Zeichen gezählt und abgerechnet.          |
| `Translate`     | Der Betrag hängt davon ab, wie viele Zeichen für die Übersetzung übermittelt werden und in wie viele Sprachen diese übersetzt werden. Wenn 50 Zeichen in 5 Sprachen übersetzt werden, werden 50×5 Zeichen berechnet.           |
| `Transliterate`     | Die Anzahl der Zeichen, die für die Transkription übermittelt werden, wird berechnet.         |
| `Dictionary lookup & example`     | Die Anzahl der Zeichen, die für die Wörterbuchsuche und Beispiele übermittelt werden, wird berechnet.         |
| `BreakSentence`     | Kostenlos.       |
| `Detect`     | Kostenlos.      |

## <a name="v3-end-points"></a>V3-Endpunkte

Global

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Textübersetzungsmethoden der V3-API

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Kompatibilität und Anpassung

> [!NOTE]
> 
> Der Microsoft Translator-Hub wurde am 17. Mai 2019 eingestellt. [Anzeigen von wichtigen Migrationsinformationen und Datumsangaben](https://www.microsoft.com/translator/business/hub/).   

Microsoft Translator V3 verwendet standardmäßig eine neuronale maschinelle Übersetzung. Daher kann es nicht mit dem Microsoft Translator-Hub verwendet werden. Der Translator-Hub unterstützt nur ältere statistische maschinelle Übersetzungen. Die neuronale Übersetzung kann nun mithilfe des benutzerdefinierten Translators angepasst werden. [Weitere Informationen zum Anpassen von neuronaler maschineller Übersetzung](custom-translator/overview.md)

Die neuronale Übersetzung mit der Text-API von V3 unterstützt die Verwendung der Standardkategorien (SMT, speech, tech, generalnn) nicht.

| Version | Endpunkt | DSGVO-konformer Datenverarbeiter | Verwendung von Translator Hub | Verwendung von „Benutzerdefinierter Translator“ (Vorschau) |
| :------ | :------- | :------------------------ | :----------------- | :------------------------------ |
|Translator-Version 2|    api.microsofttranslator.com|    Nein    |Ja    |Nein|
|Translator-Version 3|    api.cognitive.microsofttranslator.com|    Ja|    Nein|    Ja|

**Translator-Version 3**
* Ist allgemein verfügbar und wird vollständig unterstützt.
* Ist ein DVGO-konformer Datenverarbeiter und erfüllt alle ISO 20001-, ISO 20018- und SOC 3-Zertifizierungsanforderungen. 
* Ermöglicht den Aufruf der neuronalen Netzwerkübersetzungssysteme (NMT), die Sie mit „Benutzerdefinierter Translator“ (Vorschau) angepasst haben, dem neuen NMT-Anpassungsfeature von Translator. 
* Bietet keinen Zugriff auf benutzerdefinierte Übersetzungssysteme, die mit Microsoft Translator Hub erstellt wurden.

Sie verwenden die Translator-Version 3, wenn Sie den Endpunkt „api.cognitive.microsofttranslator.com“ verwenden.

**Translator-Version 2**
* Sie erfüllt nicht alle ISO 20001-, ISO 20018- und SOC 3-Zertifizierungsanforderungen. 
* Sie ermöglicht keinen Aufruf der neuronalen Netzwerkübersetzungssysteme, die Sie mit dem Anpassungsfeature von Translator angepasst haben.
* Bietet Zugriff auf benutzerdefinierte Übersetzungssysteme, die mit Microsoft Translator Hub erstellt wurden.
* Sie verwenden die Translator-Version 2, wenn Sie den Endpunkt „api.microsofttranslator.com“ verwenden.

In keiner Translator-Version wird eine Aufzeichnung Ihrer Übersetzungen erstellt. Ihre Übersetzungen werden zu keinem Zeitpunkt für Dritte freigegeben. Weitere Informationen finden Sie auf der [Webseite zur Vertraulichkeit](https://www.aka.ms/NoTrace).

## <a name="links"></a>Links

* [Microsoft-Datenschutzrichtlinie](https://privacy.microsoft.com/privacystatement)
* [Rechtliche Informationen zu Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Online Services Terms (Lizenzbedingungen für Onlinedienste)](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Dokumentation zu V3.0](reference/v3-0-reference.md)