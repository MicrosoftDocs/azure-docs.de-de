---
title: Sprachunterstützung – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Der Speech-Dienst unterstützt neben der Sprachübersetzung zahlreiche Sprachen für die Konvertierung von Sprache in Text und Text in Sprache. Dieser Artikel enthält eine umfassende Liste zur Sprachunterstützung der einzelnen Dienstfunktionen.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 118ab33c8d8ce46559957236daa21ba494655e69
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526278"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Sprach- und Stimmunterstützung für den Speech-Dienst

Die Sprachunterstützung ist abhängig von der Funktion des Speech-Diensts. In den folgenden Tabellen ist die Sprachunterstützung für die Dienstangebote [Spracherkennung](#speech-to-text), [Text-zu-Sprache](#text-to-speech), und [Sprachübersetzung](#speech-translation) zusammengefasst.

## <a name="speech-to-text"></a>Spracherkennung

Sowohl das Microsoft Speech SDK als auch die REST-API unterstützen die folgenden Sprachen (Gebietsschemas). 

Um die Genauigkeit zu erhöhen, wird die Anpassung für eine Teilmenge der Sprachen durch das Hochladen von **Audio und menschenmarkierten Transkripten** oder **zugehörigem Text (Sätze)** angeboten. Weitere Informationen zur Anpassung finden Sie unter [Was ist Custom Speech?](./custom-speech-overview.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Sprache                 | Gebietsschema (BCP-47) | Anpassungen  | [Sprachenerkennung](how-to-automatic-language-detection.md) |
|------------------------------------|--------|---------------------------------------------------|-------------------------------|
| Arabisch (Bahrain), modernes Hocharabisch  | `ar-BH` | Sprachmodell                                   | Ja                           | 
| Arabisch (Ägypten)                     | `ar-EG` | Sprachmodell                                   | Ja                          |
| Arabisch (Irak)                      | `ar-IQ` | Sprachmodell                                   |                           |
| Arabisch (Israel)                    | `ar-IL` | Sprachmodell                                   |                           |
| Arabisch (Jordanien)                    | `ar-JO` | Sprachmodell                                   |                           |
| Arabisch (Kuwait)                    | `ar-KW` | Sprachmodell                                   |                           |
| Arabisch (Libanon)                   | `ar-LB` | Sprachmodell                                   |                           |
| Arabisch (Oman)                      | `ar-OM` | Sprachmodell                                   |                           |
| Arabisch (Katar)                     | `ar-QA` | Sprachmodell                                   |                           |
| Arabisch (Saudi-Arabien)              | `ar-SA` | Sprachmodell                                   | Ja                          |
| Arabisch (Staat Palästina)        | `ar-PS` | Sprachmodell                                   |                           |
| Arabisch (Syrien)                     | `ar-SY` | Sprachmodell                                   | Ja                          |
| Arabisch (Vereinigte Arabische Emirate)      | `ar-AE` | Sprachmodell                                   |                           |
| Bulgarisch (Bulgarien)               | `bg-BG` | Sprachmodell                                   |                           |
| Katalanisch (Spanien)                    | `ca-ES` | Sprachmodell                                   | Ja                          |
| Chinesisch (Kantonesisch, traditionell)   | `zh-HK` | Akustische Modell<br>Sprachmodell                 |        Ja                   |
| Chinesisch (Mandarin, vereinfacht)     | `zh-CN` | Akustische Modell<br>Sprachmodell                 |     Ja                      |
| Chinesisch (Taiwanesisch, Mandarin)       | `zh-TW` | Akustische Modell<br>Sprachmodell                 |           Ja                |
| Kroatisch (Kroatien)                 | `hr-HR` | Sprachmodell                                   |                           |
| Tschechisch (Tschechische Republik)             | `cs-CZ` | Sprachmodell                                   |                           |
| Dänisch (Dänemark)                   | `da-DK` | Sprachmodell                                   | Ja                          |
| Niederländisch (Niederlande)                | `nl-NL` | Sprachmodell                                   |    Ja                       |
| Englisch (Australien)                | `en-AU` | Akustische Modell<br>Sprachmodell                 | Ja                          |
| Englisch (Kanada)                   | `en-CA` | Akustische Modell<br>Sprachmodell                 | Ja                          |
| Englisch (Hongkong)                | `en-HK` | Sprachmodell                                   |                           |
| Englisch (Indien)                    | `en-IN` | Akustische Modell<br>Sprachmodell                 | Ja                          |
| Englisch (Irland)                  | `en-IE` | Sprachmodell                                   |                           |
| Englisch (Neuseeland)              | `en-NZ` | Akustische Modell<br>Sprachmodell                 |  Ja                         |
| Englisch (Nigeria)                  | `en-NG` | Sprachmodell                                   |                           |
| Englisch (Philippinen)              | `en-PH` | Sprachmodell                                   |                           |
| Englisch (Singapur)                | `en-SG` | Sprachmodell                                   |                           |
| Englisch (Südafrika)             | `en-ZA` | Sprachmodell                                   |                           |
| Walisisch (Großbritannien)           | `en-GB` | Akustische Modell<br>Sprachmodell<br>Aussprache| Ja                          |
| Englisch (USA)            | `en-US` | Akustische Modell<br>Sprachmodell<br>Aussprache| Ja                          |
| Estnisch (Estland)                  | `et-EE` | Sprachmodell                                   |                           |
| Finnisch (Finnland)                  | `fi-FI` | Sprachmodell                                   |     Ja                      |
| Französisch (Kanada)                    | `fr-CA` | Akustische Modell<br>Sprachmodell                 |     Ja                      |
| Französisch (Frankreich)                    | `fr-FR` | Akustische Modell<br>Sprachmodell<br>Aussprache|      Ja                     |
| Deutsch (Deutschland)                   | `de-DE` | Akustische Modell<br>Sprachmodell<br>Aussprache|  Ja                         |
| Griechisch (Griechenland)                     | `el-GR` | Sprachmodell                                   |                           |
| Gujarati (Indien)                  | `gu-IN` | Sprachmodell                                   |                           |
| Hindi (Indien)                      | `hi-IN` | Akustische Modell<br>Sprachmodell                 |     Ja                      |
| Ungarisch (Ungarn)                | `hu-HU` | Sprachmodell                                   |                           |
| Irisch (Irland)                     | `ga-IE` | Sprachmodell                                   |                           |
| Italienisch (Italien)                    | `it-IT` | Akustische Modell<br>Sprachmodell<br>Aussprache|      Ja                     |
| Japanisch (Japan)                   | `ja-JP` | Akustische Modell<br>Sprachmodell                 |      Ja                     |
| Koreanisch (Korea)                     | `ko-KR` | Akustische Modell<br>Sprachmodell                 |      Ja                     |
| Lettisch (Lettland)                   | `lv-LV` | Sprachmodell                                   |                           |
| Litauisch (Litauen)             | `lt-LT` | Sprachmodell                                   |                           |
| Maltesisch (Malta)                     | `mt-MT` | Sprachmodell                                   |                           |
| Marathi (Indien)                    | `mr-IN` | Sprachmodell                                   |                           |
| Norwegisch, Bokmål (Norwegen)         | `nb-NO` | Sprachmodell                                   |     Ja                      |
| Polnisch (Polen)                    | `pl-PL` | Sprachmodell                                   |       Ja                    |
| Portugiesisch (Brasilien)                | `pt-BR` | Akustische Modell<br>Sprachmodell<br>Aussprache|          Ja                 |
| Portugiesisch (Portugal)              | `pt-PT` | Sprachmodell                                   |             Ja              |
| Rumänisch (Rumänien)                 | `ro-RO` | Sprachmodell                                   |                           |
| Russisch (Russische Föderation)                   | `ru-RU` | Akustische Modell<br>Sprachmodell                 |                Ja           |
| Slowakisch (Slowakei)                  | `sk-SK` | Sprachmodell                                   |                           |
| Slowenisch (Slowenien)               | `sl-SI` | Sprachmodell                                   |                           |
| Spanisch (Argentinien)                | `es-AR` | Sprachmodell                                   |                           |
| Spanisch (Bolivien)                  | `es-BO` | Sprachmodell                                   |                           |
| Spanisch (Chile)                    | `es-CL` | Sprachmodell                                   |                           |
| Spanisch (Kolumbien)                 | `es-CO` | Sprachmodell                                   |                           |
| Spanisch (Costa Rica)               | `es-CR` | Sprachmodell                                   |                           |
| Spanisch (Kuba)                     | `es-CU` | Sprachmodell                                   |                           |
| Spanisch (Dominikanische Republik)       | `es-DO` | Sprachmodell                                   |                           |
| Spanisch (Ecuador)                  | `es-EC` | Sprachmodell                                   |                           |
| Spanisch (El Salvador)              | `es-SV` | Sprachmodell                                   |                           |
| Spanisch (Äquatorialguinea)        | `es-GQ` | Sprachmodell                                   |                           |
| Spanisch (Guatemala)                | `es-GT` | Sprachmodell                                   |                           |
| Spanisch (Honduras)                 | `es-HN` | Sprachmodell                                   |                           |
| Spanisch (Mexiko)                   | `es-MX` | Akustische Modell<br>Sprachmodell                 |    Ja                       |
| Spanisch (Nicaragua)                | `es-NI` | Sprachmodell                                   |                           |
| Spanisch (Panama)                   | `es-PA` | Sprachmodell                                   |                           |
| Spanisch (Paraguay)                 | `es-PY` | Sprachmodell                                   |                           |
| Spanisch (Peru)                     | `es-PE` | Sprachmodell                                   |                           |
| Spanisch (Puerto Rico)              | `es-PR` | Sprachmodell                                   |                           |
| Spanisch (Spanien)                    | `es-ES` | Akustische Modell<br>Sprachmodell                 |  Ja                         |
| Spanisch (Uruguay)                  | `es-UY` | Sprachmodell                                   |                           |
| Spanisch (USA)                      | `es-US` | Sprachmodell                                   |                           |
| Spanisch (Venezuela)                | `es-VE` | Sprachmodell                                   |                           |
| Schwedisch (Schweden)                   | `sv-SE` | Sprachmodell                                   |   Ja                        |
| Tamil (Indien)                      | `ta-IN` | Sprachmodell                                   |                           |
| Telugu (Indien)                     | `te-IN` | Sprachmodell                                   |                           |
| Thai (Thailand)                    | `th-TH` | Sprachmodell                                   |      Ja                     |
| Türkisch (Türkei)                   | `tr-TR` | Sprachmodell                                   |                           |

## <a name="text-to-speech"></a>Text-zu-Sprache

Sowohl das Microsoft Speech SDK als auch die REST-API unterstützen diese Stimmen. Jede dieser Stimmen steht für eine bestimmte Sprache und einen bestimmten Dialekt und wird durch das Gebietsschema identifiziert. Über die [API „voices/list“](rest-text-to-speech.md#get-a-list-of-voices) können Sie auch eine vollständige Liste der Sprachen und Stimmen abrufen, die für die jeweilige Region/den jeweiligen Endpunkt unterstützt werden. 

> [!IMPORTANT]
> Die Preise variieren für Standardstimmen, benutzerdefinierte und neuronale Stimmen. Weitere Informationen finden Sie auf der Seite [Preise](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Neuronale Stimmen

Die neuronale Sprachsynthese ist eine neue Art der Sprachsynthese mithilfe von Deep Neural Networks. Wenn Sie eine neuronale Stimme verwenden, kann die synthetisierte Sprache kaum von menschlichen Aufzeichnungen unterschieden werden.

Gestalten Sie mit neuronalen Stimmen Interaktionen mit Chatbots und Sprachassistenten noch natürlicher und einladender, wandeln Sie digitale Texte wie E-Books in Audiobooks um, und verpassen Sie Ihrem Navigationssystem im Auto ein Upgrade. Durch natürliche, menschenähnliche Intonation und klare Aussprache von Wörtern können neuronale Stimmen die Hörermüdung bei der Interaktion mit KI-Systemen erheblich verringern.

> [!NOTE]
> Neuronale Stimmen werden aus Stichproben erstellt, die eine 24-kHz-Abtastrate verwenden.
> Die Abtastrate aller Stimmen kann bei der Synchronisierung auf andere Abtastraten herauf- oder heruntergesetzt werden.


| Sprache | Gebietsschema | Geschlecht | Name der Stimme | Stilunterstützung |
|---|---|---|---|---|
| Arabisch (Ägypten) | `ar-EG` | Female | `ar-EG-SalmaNeural` | Allgemein |
| Arabisch (Ägypten) | `ar-EG` | Male | `ar-EG-ShakirNeural` <sup>Neu</sup> | Allgemein |
| Arabisch (Saudi-Arabien) | `ar-SA` | Female | `ar-SA-ZariyahNeural` | Allgemein |
| Arabisch (Saudi-Arabien) | `ar-SA` | Male | `ar-SA-HamedNeural` <sup>Neu</sup> | Allgemein |
| Bulgarisch (Bulgarien) | `bg-BG` | Female | `bg-BG-KalinaNeural` | Allgemein |
| Bulgarisch (Bulgarien) | `bg-BG` | Male | `bg-BG-BorislavNeural` <sup>Neu</sup> | Allgemein |
| Katalanisch (Spanien) | `ca-ES` | Female | `ca-ES-AlbaNeural` | Allgemein |
| Katalanisch (Spanien) | `ca-ES` | Female | `ca-ES-JoanaNeural` <sup>Neu</sup> | Allgemein |
| Katalanisch (Spanien) | `ca-ES` | Male | `ca-ES-EnricNeural` <sup>Neu</sup> | Allgemein |
| Chinesisch (Kantonesisch, traditionell) | `zh-HK` | Female | `zh-HK-HiuGaaiNeural` | Allgemein |
| Chinesisch (Kantonesisch, traditionell) | `zh-HK` | Female | `zh-HK-HiuMaanNeural` <sup>Neu</sup> | Allgemein |
| Chinesisch (Kantonesisch, traditionell) | `zh-HK` | Male | `zh-HK-WanLungNeural` <sup>Neu</sup> | Allgemein |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-XiaoxiaoNeural` | Allgemein, mehrere Stimmstile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles)  |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-XiaoyouNeural` | Kinderstimme, optimiert für das Erzählen von Geschichten |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Male | `zh-CN-YunyangNeural` | Optimiert für die Ansage von Nachrichten,<br /> mehrere Stimmstile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Male | `zh-CN-YunyeNeural` | Optimiert für das Erzählen von Geschichten  |
| Chinesisch (Taiwanesisch, Mandarin) | `zh-TW` | Female | `zh-TW-HsiaoChenNeural` <sup>Neu</sup> | Allgemein |
| Chinesisch (Taiwanesisch, Mandarin) | `zh-TW` | Female | `zh-TW-HsiaoYuNeural` | Allgemein |
| Chinesisch (Taiwanesisch, Mandarin) | `zh-TW` | Male | `zh-TW-YunJheNeural` <sup>Neu</sup> | Allgemein |
| Kroatisch (Kroatien) | `hr-HR` | Female | `hr-HR-GabrijelaNeural` | Allgemein |
| Kroatisch (Kroatien) | `hr-HR` | Male | `hr-HR-SreckoNeural` <sup>Neu</sup> | Allgemein |
| Tschechisch (Tschechische Republik) | `cs-CZ` | Female | `cs-CZ-VlastaNeural` | Allgemein |
| Tschechisch (Tschechische Republik) | `cs-CZ` | Male | `cs-CZ-AntoninNeural` <sup>Neu</sup> | Allgemein |
| Dänisch (Dänemark) | `da-DK` | Female | `da-DK-ChristelNeural` | Allgemein |
| Dänisch (Dänemark) | `da-DK` | Male | `da-DK-JeppeNeural` <sup>Neu</sup> | Allgemein |
| Niederländisch (Niederlande) | `nl-NL` | Female | `nl-NL-ColetteNeural` | Allgemein |
| Niederländisch (Niederlande) | `nl-NL` | Female | `nl-NL-FennaNeural` <sup>Neu</sup> | Allgemein |
| Niederländisch (Niederlande) | `nl-NL` | Male | `nl-NL-MaartenNeural` <sup>Neu</sup> | Allgemein |
| Englisch (Australien) | `en-AU` | Female | `en-AU-NatashaNeural` | Allgemein |
| Englisch (Australien) | `en-AU` | Male | `en-AU-WilliamNeural` | Allgemein |
| Englisch (Kanada) | `en-CA` | Female | `en-CA-ClaraNeural` | Allgemein |
| Englisch (Kanada) | `en-CA` | Male | `en-CA-LiamNeural` <sup>Neu</sup> | Allgemein |
| Englisch (Indien) | `en-IN` | Female | `en-IN-NeerjaNeural` | Allgemein |
| Englisch (Indien) | `en-IN` | Male | `en-IN-PrabhatNeural` <sup>Neu</sup> | Allgemein |
| Englisch (Irland) | `en-IE` | Female | `en-IE-EmilyNeural` | Allgemein |
| Englisch (Irland) | `en-IE` | Male | `en-IE-ConnorNeural` <sup>Neu</sup> | Allgemein |
| Walisisch (Großbritannien) | `en-GB` | Female | `en-GB-LibbyNeural` | Allgemein |
| Walisisch (Großbritannien) | `en-GB` | Female | `en-GB-MiaNeural` | Allgemein |
| Walisisch (Großbritannien) | `en-GB` | Male | `en-GB-RyanNeural` | Allgemein |
| Englisch (USA) | `en-US` | Female | `en-US-AriaNeural` | Allgemein, mehrere Stimmstile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles)  |
| Englisch (USA) | `en-US` | Female | `en-US-JennyNeural` | Allgemein |
| Englisch (USA) | `en-US` | Male | `en-US-GuyNeural` | Allgemein |
| Finnisch (Finnland) | `fi-FI` | Female | `fi-FI-NooraNeural` | Allgemein |
| Finnisch (Finnland) | `fi-FI` | Female | `fi-FI-SelmaNeural` <sup>Neu</sup> | Allgemein |
| Finnisch (Finnland) | `fi-FI` | Male | `fi-FI-HarriNeural` <sup>Neu</sup> | Allgemein |
| Französisch (Kanada) | `fr-CA` | Female | `fr-CA-SylvieNeural` | Allgemein |
| Französisch (Kanada) | `fr-CA` | Male | `fr-CA-JeanNeural` | Allgemein |
| Französisch (Frankreich) | `fr-FR` | Female | `fr-FR-DeniseNeural` | Allgemein |
| Französisch (Frankreich) | `fr-FR` | Male | `fr-FR-HenriNeural` | Allgemein |
| Französisch (Schweiz) | `fr-CH` | Female | `fr-CH-ArianeNeural` | Allgemein |
| Französisch (Schweiz) | `fr-CH` | Male | `fr-CH-FabriceNeural` <sup>Neu</sup> | Allgemein |
| Deutsch (Österreich) | `de-AT` | Female | `de-AT-IngridNeural` | Allgemein |
| Deutsch (Österreich) | `de-AT` | Male | `de-AT-JonasNeural` <sup>Neu</sup> | Allgemein |
| Deutsch (Deutschland) | `de-DE` | Female | `de-DE-KatjaNeural` | Allgemein |
| Deutsch (Deutschland) | `de-DE` | Male | `de-DE-ConradNeural` | Allgemein |
| Deutsch (Schweiz) | `de-CH` | Female | `de-CH-LeniNeural` | Allgemein |
| Deutsch (Schweiz) | `de-CH` | Male | `de-CH-JanNeural` <sup>Neu</sup> | Allgemein |
| Griechisch (Griechenland) | `el-GR` | Female | `el-GR-AthinaNeural` | Allgemein |
| Griechisch (Griechenland) | `el-GR` | Male | `el-GR-NestorasNeural` <sup>Neu</sup> | Allgemein |
| Hebräisch (Israel) | `he-IL` | Female | `he-IL-HilaNeural` | Allgemein |
| Hebräisch (Israel) | `he-IL` | Male | `he-IL-AvriNeural` <sup>Neu</sup> | Allgemein |
| Hindi (Indien) | `hi-IN` | Female | `hi-IN-SwaraNeural` | Allgemein |
| Hindi (Indien) | `hi-IN` | Male | `hi-IN-MadhurNeural` <sup>Neu</sup> | Allgemein |
| Ungarisch (Ungarn) | `hu-HU` | Female | `hu-HU-NoemiNeural` | Allgemein |
| Ungarisch (Ungarn) | `hu-HU` | Male | `hu-HU-TamasNeural` <sup>Neu</sup> | Allgemein |
| Indonesisch (Indonesien) | `id-ID` | Female | `id-ID-GadisNeural` <sup>Neu</sup> | Allgemein |
| Indonesisch (Indonesien) | `id-ID` | Male | `id-ID-ArdiNeural` | Allgemein |
| Italienisch (Italien) | `it-IT` | Female | `it-IT-ElsaNeural` | Allgemein |
| Italienisch (Italien) | `it-IT` | Female | `it-IT-IsabellaNeural` | Allgemein |
| Italienisch (Italien) | `it-IT` | Male | `it-IT-DiegoNeural` | Allgemein |
| Japanisch (Japan) | `ja-JP` | Female | `ja-JP-NanamiNeural` | Allgemein |
| Japanisch (Japan) | `ja-JP` | Male | `ja-JP-KeitaNeural` | Allgemein |
| Koreanisch (Korea) | `ko-KR` | Female | `ko-KR-SunHiNeural` | Allgemein |
| Koreanisch (Korea) | `ko-KR` | Male | `ko-KR-InJoonNeural` | Allgemein |
| Malaiisch (Malaysia) | `ms-MY` | Female | `ms-MY-YasminNeural` | Allgemein |
| Malaiisch (Malaysia) | `ms-MY` | Male | `ms-MY-OsmanNeural` <sup>Neu</sup> | Allgemein |
| Norwegisch, Bokmål (Norwegen) | `nb-NO` | Female | `nb-NO-IselinNeural` | Allgemein |
| Norwegisch, Bokmål (Norwegen) | `nb-NO` | Female | `nb-NO-PernilleNeural` <sup>Neu</sup> | Allgemein |
| Norwegisch, Bokmål (Norwegen) | `nb-NO` | Male | `nb-NO-FinnNeural` <sup>Neu</sup> | Allgemein |
| Polnisch (Polen) | `pl-PL` | Female | `pl-PL-AgnieszkaNeural` <sup>Neu</sup> | Allgemein |
| Polnisch (Polen) | `pl-PL` | Female | `pl-PL-ZofiaNeural` | Allgemein |
| Polnisch (Polen) | `pl-PL` | Male | `pl-PL-MarekNeural` <sup>Neu</sup> | Allgemein |
| Portugiesisch (Brasilien) | `pt-BR` | Female | `pt-BR-FranciscaNeural` | Allgemein, mehrere Stimmstile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles)  |
| Portugiesisch (Brasilien) | `pt-BR` | Male | `pt-BR-AntonioNeural` | Allgemein |
| Portugiesisch (Portugal) | `pt-PT` | Female | `pt-PT-FernandaNeural` | Allgemein |
| Portugiesisch (Portugal) | `pt-PT` | Female | `pt-PT-RaquelNeural` <sup>Neu</sup> | Allgemein |
| Portugiesisch (Portugal) | `pt-PT` | Male | `pt-PT-DuarteNeural` <sup>Neu</sup> | Allgemein |
| Rumänisch (Rumänien) | `ro-RO` | Female | `ro-RO-AlinaNeural` | Allgemein |
| Rumänisch (Rumänien) | `ro-RO` | Male | `ro-RO-EmilNeural` <sup>Neu</sup> | Allgemein |
| Russisch (Russische Föderation) | `ru-RU` | Female | `ru-RU-DariyaNeural` | Allgemein |
| Russisch (Russische Föderation) | `ru-RU` | Female | `ru-RU-SvetlanaNeural` <sup>Neu</sup> | Allgemein |
| Russisch (Russische Föderation) | `ru-RU` | Male | `ru-RU-DmitryNeural` <sup>Neu</sup> | Allgemein |
| Slowakisch (Slowakei) | `sk-SK` | Female | `sk-SK-ViktoriaNeural` | Allgemein |
| Slowakisch (Slowakei) | `sk-SK` | Male | `sk-SK-LukasNeural` <sup>Neu</sup> | Allgemein |
| Slowenisch (Slowenien) | `sl-SI` | Female | `sl-SI-PetraNeural` | Allgemein |
| Slowenisch (Slowenien) | `sl-SI` | Male | `sl-SI-RokNeural` <sup>Neu</sup> | Allgemein |
| Spanisch (Mexiko) | `es-MX` | Female | `es-MX-DaliaNeural` | Allgemein |
| Spanisch (Mexiko) | `es-MX` | Male | `es-MX-JorgeNeural` | Allgemein |
| Spanisch (Spanien) | `es-ES` | Female | `es-ES-ElviraNeural` | Allgemein |
| Spanisch (Spanien) | `es-ES` | Male | `es-ES-AlvaroNeural` | Allgemein |
| Schwedisch (Schweden) | `sv-SE` | Female | `sv-SE-HilleviNeural` | Allgemein |
| Schwedisch (Schweden) | `sv-SE` | Female | `sv-SE-SofieNeural` <sup>Neu</sup> | Allgemein |
| Schwedisch (Schweden) | `sv-SE` | Male | `sv-SE-MattiasNeural` <sup>Neu</sup> | Allgemein |
| Tamil (Indien) | `ta-IN` | Female | `ta-IN-PallaviNeural` | Allgemein |
| Tamil (Indien) | `ta-IN` | Male | `ta-IN-ValluvarNeural` <sup>Neu</sup> | Allgemein |
| Telugu (Indien) | `te-IN` | Female | `te-IN-ShrutiNeural` | Allgemein |
| Telugu (Indien) | `te-IN` | Male | `te-IN-MohanNeural` <sup>Neu</sup> | Allgemein |
| Thai (Thailand) | `th-TH` | Female | `th-TH-AcharaNeural` | Allgemein |
| Thai (Thailand) | `th-TH` | Female | `th-TH-PremwadeeNeural` | Allgemein |
| Thai (Thailand) | `th-TH` | Male | `th-TH-NiwatNeural` <sup>Neu</sup> | Allgemein |
| Türkisch (Türkei) | `tr-TR` | Female | `tr-TR-EmelNeural` | Allgemein |
| Türkisch (Türkei) | `tr-TR` | Male | `tr-TR-AhmetNeural` <sup>Neu</sup> | Allgemein |
| Vietnamesisch (Vietnam) | `vi-VN` | Female | `vi-VN-HoaiMyNeural` | Allgemein |
| Vietnamesisch (Vietnam) | `vi-VN` | Male | `vi-VN-NamMinhNeural` <sup>Neu</sup> | Allgemein |

#### <a name="neural-voices-in-preview"></a>Neuronale Stimmen in der Vorschau

Die folgenden neuronalen Stimmen sind in der öffentlichen Vorschau verfügbar. 

| Sprache                         | Gebietsschema  | Geschlecht | Name der Stimme                             | Stilunterstützung |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-XiaohanNeural` | Allgemein, mehrere Stile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-XiaomoNeural` | Allgemein, mehrere Rollen und Stile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-XiaoruiNeural` | Erwachsene Stimme, mehrere Stile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-XiaoxuanNeural` | Allgemein, mehrere Rollen und Stile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Male   | `zh-CN-YunxiNeural` | Allgemein, mehrere Stile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Estnisch (Estland) | `et-EE` | Female | `et-EE-AnuNeural` | Allgemein |
| Estnisch (Estland) | `et-EE` | Male | `et-EE-KertNeural` <sup>Neu</sup> | Allgemein |
| Irisch (Irland) | `ga-IE` | Female | `ga-IE-OrlaNeural` | Allgemein |
| Irisch (Irland) | `ga-IE` | Male | `ga-IE-ColmNeural` <sup>Neu</sup> | Allgemein |
| Lettisch (Lettland) | `lv-LV` | Female | `lv-LV-EveritaNeural` | Allgemein |
| Lettisch (Lettland) | `lv-LV` | Male | `lv-LV-NilsNeural` <sup>Neu</sup> | Allgemein |
| Litauisch (Litauen) | `lt-LT` | Female | `lt-LT-OnaNeural` | Allgemein |
| Litauisch (Litauen) | `lt-LT` | Male | `lt-LT-LeonasNeural` <sup>Neu</sup> | Allgemein |
| Maltesisch (Malta) | `mt-MT` | Female | `mt-MT-GraceNeural` | Allgemein |
| Maltesisch (Malta) | `mt-MT` | Male | `mt-MT-JosephNeural` <sup>Neu</sup> | Allgemein |

> [!IMPORTANT]
> Stimmen in der öffentlichen Vorschau sind nur in drei Dienstregionen verfügbar: „USA, Osten“, „Europa, Westen“ und „Asien, Südosten“.

Weitere Informationen zur regionalen Verfügbarkeit finden Sie unter [Regionen](regions.md#standard-and-neural-voices).

Informationen dazu, wie Sie neuronale Stimmen konfigurieren und anpassen können (z. B. den Sprechstil), finden Sie unter [Anpassen von Sprechweisen](speech-synthesis-markup.md#adjust-speaking-styles).

> [!IMPORTANT]
> Die Stimme `en-US-JessaNeural` wurde in `en-US-AriaNeural` geändert. Wenn Sie zuvor „Jessa“ verwendet haben, wechseln Sie zu „Aria“.

> [!TIP]
> In Sprachsyntheseanforderungen kann weiterhin die vollständige Dienstnamenzuordnung wie etwa „Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)“ verwendet werden.

### <a name="standard-voices"></a>Standardstimmen

Es stehen mehr als 75 Standardstimmen in mehr als 45 Sprachen und Gebietsschemas zur Verfügung, mit denen Sie Text in Sprache umwandeln können. Weitere Informationen zur regionalen Verfügbarkeit finden Sie unter [Regionen](regions.md#standard-and-neural-voices).

> [!NOTE]
> Mit zwei Ausnahmen werden Standardstimmen aus Stichproben erstellt, die eine 16-kHz-Abtastrate verwenden.
> Die Stimmen **en-US-AriaRUS** und **en-US-GuyRUS** werden ebenfalls aus Stichproben erstellt, die eine Abtastrate von 24 khz verwenden.
> Die Abtastrate aller Stimmen kann bei der Synchronisierung auf andere Abtastraten herauf- oder heruntergesetzt werden.

| Sprache | Gebietsschema (BCP-47) | Geschlecht | Name der Stimme |
|--|--|--|--|
| Arabisch (Arabisch) | `ar-EG` | Female | `ar-EG-Hoda`|
| Arabisch (Saudi-Arabien) | `ar-SA` | Male | `ar-SA-Naayf`|
| Bulgarisch (Bulgarien) | `bg-BG` | Male | `bg-BG-Ivan`|
| Katalanisch (Spanien) | `ca-ES` | Female | `ca-ES-HerenaRUS`|
| Chinesisch (Kantonesisch, traditionell) | `zh-HK` | Male | `zh-HK-Danny`|
| Chinesisch (Kantonesisch, traditionell) | `zh-HK` | Female | `zh-HK-TracyRUS`|
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-HuihuiRUS`|
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Male | `zh-CN-Kangkang`|
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-Yaoyao`|
| Chinesisch (Taiwanesisch, Mandarin) |  `zh-TW` | Female | `zh-TW-HanHanRUS`|
| Chinesisch (Taiwanesisch, Mandarin) |  `zh-TW` | Female | `zh-TW-Yating`|
| Chinesisch (Taiwanesisch, Mandarin) |  `zh-TW` | Male | `zh-TW-Zhiwei`|
| Kroatisch (Kroatien) | `hr-HR` | Male | `hr-HR-Matej`|
| Tschechisch (Tschechische Republik) | `cs-CZ` | Male | `cs-CZ-Jakub`|
| Dänisch (Dänemark) | `da-DK` | Female | `da-DK-HelleRUS`|
| Niederländisch (Niederlande) | `nl-NL` | Female | `nl-NL-HannaRUS`|
| Englisch (Australien) | `en-AU` | Female | `en-AU-Catherine`|
| Englisch (Australien) | `en-AU` | Female | `en-AU-HayleyRUS`|
| Englisch (Kanada) | `en-CA` | Female | `en-CA-HeatherRUS`|
| Englisch (Kanada) | `en-CA` | Female | `en-CA-Linda`|
| Englisch (Indien) | `en-IN` | Female | `en-IN-Heera`|
| Englisch (Indien) | `en-IN` | Female | `en-IN-PriyaRUS`|
| Englisch (Indien) | `en-IN` | Male | `en-IN-Ravi`|
| Englisch (Irland) | `en-IE` | Male | `en-IE-Sean`|
| Walisisch (Großbritannien) | `en-GB` | Male | `en-GB-George`|
| Walisisch (Großbritannien) | `en-GB` | Female | `en-GB-HazelRUS`|
| Walisisch (Großbritannien) | `en-GB` | Female | `en-GB-Susan`|
| Englisch (USA) | `en-US` | Male | `en-US-BenjaminRUS`|
| Englisch (USA) | `en-US` | Male | `en-US-GuyRUS`|
| Englisch (USA) | `en-US` | Female | `en-US-AriaRUS`|
| Englisch (USA) | `en-US` | Female | `en-US-ZiraRUS`|
| Finnisch (Finnland) | `fi-FI` | Female | `fi-FI-HeidiRUS`|
| Französisch (Kanada) | `fr-CA` | Female | `fr-CA-Caroline`|
| Französisch (Kanada) | `fr-CA` | Female | `fr-CA-HarmonieRUS`|
| Französisch (Frankreich) | `fr-FR` | Female | `fr-FR-HortenseRUS`|
| Französisch (Frankreich) | `fr-FR` | Female | `fr-FR-Julie`|
| Französisch (Frankreich) | `fr-FR` | Male | `fr-FR-Paul`|
| Französisch (Schweiz) | `fr-CH` | Male | `fr-CH-Guillaume`|
| Deutsch (Österreich) | `de-AT` | Male | `de-AT-Michael`|
| Deutsch (Deutschland) | `de-DE` | Female | `de-DE-HeddaRUS`|
| Deutsch (Deutschland) | `de-DE` | Male | `de-DE-Stefan`|
| Deutsch (Schweiz) | `de-CH` | Male | `de-CH-Karsten`|
| Griechisch (Griechenland) | `el-GR` | Male | `el-GR-Stefanos`|
| Hebräisch (Israel) | `he-IL` | Male | `he-IL-Asaf`|
| Hindi (Indien) | `hi-IN` | Male | `hi-IN-Hemant`|
| Hindi (Indien) | `hi-IN` | Female | `hi-IN-Kalpana`|
| Ungarisch (Ungarn) | `hu-HU` | Male | `hu-HU-Szabolcs`|
| Indonesisch (Indonesien) | `id-ID` | Male | `id-ID-Andika`|
| Italienisch (Italien) | `it-IT` | Male | `it-IT-Cosimo`|
| Italienisch (Italien) | `it-IT` | Female | `it-IT-LuciaRUS`|
| Japanisch (Japan) | `ja-JP` | Female | `ja-JP-Ayumi`|
| Japanisch (Japan) | `ja-JP` | Female | `ja-JP-HarukaRUS`|
| Japanisch (Japan) | `ja-JP` | Male | `ja-JP-Ichiro`|
| Koreanisch (Korea) | `ko-KR` | Female | `ko-KR-HeamiRUS`|
| Malaiisch (Malaysia) | `ms-MY` | Male | `ms-MY-Rizwan`|
| Norwegisch, Bokmål (Norwegen) | `nb-NO` | Female | `nb-NO-HuldaRUS`|
| Polnisch (Polen) | `pl-PL` | Female | `pl-PL-PaulinaRUS`|
| Portugiesisch (Brasilien) | `pt-BR` | Male | `pt-BR-Daniel`|
| Portugiesisch (Brasilien) | `pt-BR` | Female | `pt-BR-HeloisaRUS`|
| Portugiesisch (Portugal) | `pt-PT` | Female | `pt-PT-HeliaRUS`|
| Rumänisch (Rumänien) | `ro-RO` | Male | `ro-RO-Andrei`|
| Russisch (Russische Föderation) | `ru-RU` | Female | `ru-RU-EkaterinaRUS`|
| Russisch (Russische Föderation) | `ru-RU` | Female | `ru-RU-Irina`|
| Russisch (Russische Föderation) | `ru-RU` | Male | `ru-RU-Pavel`|
| Slowakisch (Slowakei) | `sk-SK` | Male | `sk-SK-Filip`|
| Slowenisch (Slowenien) | `sl-SI` | Male | `sl-SI-Lado`|
| Spanisch (Mexiko) | `es-MX` | Female | `es-MX-HildaRUS`|
| Spanisch (Mexiko) | `es-MX` | Male | `es-MX-Raul`|
| Spanisch (Spanien) | `es-ES` | Female | `es-ES-HelenaRUS`|
| Spanisch (Spanien) | `es-ES` | Female | `es-ES-Laura`|
| Spanisch (Spanien) | `es-ES` | Male | `es-ES-Pablo`|
| Schwedisch (Schweden) | `sv-SE` | Female | `sv-SE-HedvigRUS`|
| Tamil (Indien) | `ta-IN` | Male | `ta-IN-Valluvar`|
| Telugu (Indien) | `te-IN` | Female | `te-IN-Chitra`|
| Thai (Thailand) | `th-TH` | Male | `th-TH-Pattara`|
| Türkisch (Türkei) | `tr-TR` | Female | `tr-TR-SedaRUS`|
| Vietnamesisch (Vietnam) | `vi-VN` | Male | `vi-VN-An` |

> [!IMPORTANT]
> Die Stimme `en-US-Jessa` wurde in `en-US-Aria` geändert. Wenn Sie zuvor „Jessa“ verwendet haben, wechseln Sie zu „Aria“.

> [!TIP]
> In Sprachsyntheseanforderungen kann weiterhin die vollständige Dienstnamenzuordnung wie etwa „Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)“ verwendet werden.

### <a name="customization"></a>Anpassung

Custom Voice ist für die neuronale Ebene und die Standardebene verfügbar. Die für diese beiden Ebenen unterstützten Sprachen unterscheiden sich. 

| Sprache | Gebietsschema | Standard | Neuronal |
|--|--|--|--|
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Ja | Ja |
| Chinesisch (Mandarin, vereinfacht), zweisprachig mit Englisch | `zh-CN` (zweisprachig) | Ja | Ja |
| Englisch (Australien) | `en-AU` | Nein | Ja |
| Englisch (Indien) | `en-IN` | Ja | Ja |
| Walisisch (Großbritannien) | `en-GB` | Ja | Ja |
| Englisch (USA) | `en-US` | Ja | Ja |
| Französisch (Kanada) | `fr-CA` | Nein | Ja |
| Französisch (Frankreich) | `fr-FR` | Ja | Ja |
| Deutsch (Deutschland) | `de-DE` | Ja | Ja |
| Italienisch (Italien) | `it-IT` | Ja | Ja |
| Japanisch (Japan) | `ja-JP` | Nein | Ja |
| Koreanisch (Korea) | `ko-KR` | Nein | Ja |
| Portugiesisch (Brasilien) | `pt-BR` | Ja | Ja |
| Spanisch (Mexiko) | `es-MX` | Ja | Ja |
| Spanisch (Spanien) | `es-ES` | Nein | Ja |

Wählen Sie das richtige Gebietsschema, das den Trainingsdaten entspricht, die Sie für das Training eines benutzerdefinierten Sprachmodells benötigen. Wenn die von Ihnen aufgenommenen Daten beispielsweise auf Englisch mit britischem Akzent gesprochen werden, wählen Sie `en-GB`.

> [!NOTE]
> Wir unterstützen kein zweisprachiges Modelltraining in Custom Voice, mit Ausnahme des zweisprachigen Chinesisch-Englisch. Wählen Sie „Chinesisch-Englisch zweisprachig“, wenn Sie eine chinesische Stimme trainieren möchten, die auch Englisch sprechen kann. Zweisprachiges Modelltraining für Chinesisch-Englisch mithilfe der Standardmethode ist nur in den Regionen „Europa, Norden“ und „USA, Norden-Mitte“ verfügbar. Training mit dem Feature „Benutzerdefinierte neuronale Stimme“ ist in den Regionen „Vereinigtes Königreich, Süden“ und „USA, Osten“ verfügbar.

## <a name="speech-translation"></a>Sprachübersetzung

Die **Sprachübersetzungs**-API unterstützt verschiedene Sprachen für die Übersetzung von Sprache in Sprache und Sprache in Text. Die Ausgangssprache muss immer eine der Sprachen aus der Sprachentabelle für die Spracherkennung sein. Welche Zielsprachen verfügbar sind, richtet sich danach, ob es sich beim Übersetzungsziel um Sprache oder Text handelt. Sie können Spracheingaben in mehr als [60 Sprachen](https://www.microsoft.com/translator/business/languages/) übersetzen. Eine Teilmenge der Sprachen steht für die [Sprachsynthese](language-support.md#text-languages) zur Verfügung.

### <a name="text-languages"></a>Sprachen für Textausgabe

| Sprache für Textausgabe           | Sprachcode |
|:------------------------|:-------------:|
| Afrikaans               | `af`          |
| Arabisch                  | `ar`          |
| Bengalisch                  | `bn`          |
| Bosnisch (Lateinisch)         | `bs`          |
| Bulgarisch               | `bg`          |
| Chinesisch (traditionell) | `yue`         |
| Katalanisch                 | `ca`          |
| Chinesisch (vereinfacht)      | `zh-Hans`     |
| Chinesisch (traditionell)     | `zh-Hant`     |
| Kroatisch                | `hr`          |
| Tschechisch                   | `cs`          |
| Dänisch                  | `da`          |
| Niederländisch                   | `nl`          |
| Englisch                 | `en`          |
| Estnisch                | `et`          |
| Fidschi                  | `fj`          |
| Filipino                | `fil`         |
| Finnisch                 | `fi`          |
| Französisch                  | `fr`          |
| Deutsch                  | `de`          |
| Griechisch                   | `el`          |
| Gujarati                | `gu`          |
| Haitianisches Kreolisch          | `ht`          |
| Hebräisch                  | `he`          |
| Hindi                   | `hi`          |
| Hmong Daw               | `mww`         |
| Ungarisch               | `hu`          |
| Indonesisch              | `id`          |
| Irisch                   | `ga`          |
| Italienisch                 | `it`          |
| Japanisch                | `ja`          |
| Kannada                 | `kn`          |
| Suaheli               | `sw`          |
| Klingonisch                 | `tlh-Latn`    |
| Klingonisch (plqaD)         | `tlh-Piqd`    |
| Koreanisch                  | `ko`          |
| Lettisch                 | `lv`          |
| Litauisch              | `lt`          |
| Madagassisch                | `mg`          |
| Malaiisch                   | `ms`          |
| Malayalam               | `ml`          |
| Maltesisch                 | `mt`          |
| Maori                   | `mi`          |
| Marathi                 | `mr`          |
| Norwegisch               | `nb`          |
| Persisch                 | `fa`          |
| Polnisch                  | `pl`          |
| Portugiesisch (Brasilien)     | `pt-br`       |
| Portugiesisch (Portugal)   | `pt-pt`       |
| Pandschabi                 | `pa`          |
| Queretaro-Otomi         | `otq`         |
| Rumänisch                | `ro`          |
| Russisch                 | `ru`          |
| Samoanisch                  | `sm`          |
| Serbisch (Kyrillisch)      | `sr-Cyrl`     |
| Serbisch (Lateinisch)         | `sr-Latn`     |
| Slowakisch                  | `sk`          |
| Slowenisch               | `sl`          |
| Spanisch                 | `es`          |
| Schwedisch                 | `sv`          |
| Tahitisch                | `ty`          |
| Tamilisch                   | `ta`          |
| Telugu                  | `te`          |
| Thailändisch                    | `th`          |
| Tongaisch                  | `to`          |
| Türkisch                 | `tr`          |
| Ukrainisch               | `uk`          |
| Urdu                    | `ur`          |
| Vietnamesisch              | `vi`          |
| Walisisch                   | `cy`          |
| Yukatekisches Maya            | `yua`         |

## <a name="speaker-recognition"></a>Sprechererkennung

In der folgenden Tabelle finden Sie die unterstützten Sprachen für die verschiedenen Sprechererkennungs-APIs. Weitere Informationen zur Sprechererkennung finden Sie in der [Übersicht](speaker-recognition-overview.md).

| Sprache | Gebietsschema (BCP-47) | Textabhängige Überprüfung | Textunabhängige Überprüfung | Textunabhängige Identifikation |
|----|----|----|----|----|
|Englisch (USA)  |  en-US  |  ja  |  ja  |  ja |
|Chinesisch (Mandarin, vereinfacht) | zh-CN     |     – |     ja |     ja|
|Englisch (Australien)     | en-AU     | –     | ja     | ja|
|Englisch (Kanada)     | en-CA     | – |     ja |     ja|
|English (UK)     | en-GB     | –     | ja     | ja|
|Französisch (Kanada)     | fr-CA     | –     | ja |     ja|
|Französisch (Frankreich)     | fr-FR     | –     | ja     | ja|
|Deutsch (Deutschland)     | de-DE     | –     | ja     | ja|
|Italienisch | it-IT     |     –     | ja |     ja|
|Japanisch     | ja-JP | –     | ja     | ja|
|Portugiesisch (Brasilien) | pt-BR |     – |     ja |     ja|
|Spanisch (Mexiko)     | es-MX     | – |     ja |     ja|
|Spanisch (Spanien)     | es-ES | –     | ja |     ja|

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen Sie ein kostenloses Azure-Konto.](https://azure.microsoft.com/free/cognitive-services/)
* [Erkennen von Sprache in C#](./get-started-speech-to-text.md?pivots=programming-language-chsarp)