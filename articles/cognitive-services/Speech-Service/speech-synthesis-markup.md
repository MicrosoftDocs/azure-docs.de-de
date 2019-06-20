---
title: Speech Synthesis Markup Language (SSML) – Speech Services
titleSuffix: Azure Cognitive Services
description: Verwenden der Markupsprache für Sprachsynthese zum Steuern der Aussprache und des Satzrhythmus in Text-zu-Sprache
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: a748250a25c483f61489351943e8ef29a5d5edbe
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165064"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Speech Synthesis Markup Language (SSML)

Die Markupsprache für Sprachsynthese (Speech Synthesis Markup Language, SSML) ist eine XML-basierte Markupsprache, die eine Möglichkeit zum Steuern der Aussprache und des *Satzrhythmus* von Text-zu-Sprache bietet. Satzrhythmus bezieht sich auf den Rhythmus und die Tonhöhe der Sprache – sozusagen ihren „musikalischen“ Aspekt. Sie können Wörter phonetisch angeben, Hinweise zum Interpretieren von Zahlen bereitstellen, Pausen einfügen, die Tonhöhe, Lautstärke und Rate steuern und vieles mehr. Weitere Informationen finden Sie unter [Speech Synthesis Markup Language (SSML) Version 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). 

Eine vollständige Liste der unterstützten Sprachen, Gebietsschemas und Stimmen (neuronal und Standard) finden Sie unter [Sprachunterstützung](language-support.md#text-to-speech).

Die folgenden Abschnitte enthalten Beispiele für allgemeine Sprachsyntheseaufgaben.

## <a name="adjust-speaking-style-for-neural-voices"></a>Anpassen der Sprechweise für neuronale Stimmen

Sie können SSML verwenden, um die Sprechweise bei Verwendung einer der neuronalen Stimmen anzupassen.

Standardmäßig synthetisiert die Sprachsynthese Text in einem neutralen Stil. Die neuronalen Stimmen erweitern SSML um das Element `<mstts:express-as>`, das Text in synthetisierte Sprache in verschiedene Sprechweisen umwandelt. Die Stiltags werden derzeit nur mit diesen Stimmen unterstützt:

* `en-US-JessaNeural` 
* `zh-CN-XiaoxiaoNeural`.

Änderungen der Sprechweise können auf Satzebene vorgenommen werden. Die Sprechweisen variieren je nach Stimme. Wenn ein Sprechweisentyp nicht unterstützt wird, gibt der Dienst die synthetisierte Sprache als neutrale Standardsprechweise zurück.

| Sprache | Style | BESCHREIBUNG | 
|-----------|-----------------|----------|
| `en-US-JessaNeural` | type=`cheerful` | Drückt eine positive und glückliche Emotion aus |
| | type=`empathy` | Drückt ein Gefühl von Anteilnahme und Verständnis aus |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Drückt einen formalen Ton ähnlich wie bei Nachrichtensendungen aus |
| | type=`sentiment` | Vermittelt eine anrührende Botschaft oder Geschichte |

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
<voice name='en-US-JessaNeural'>
<mstts:express-as type="cheerful"> 
    That'd be just amazing! 
</mstts:express-as></voice></speak>
```

## <a name="add-a-break"></a>Hinzufügen einer Pause
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Ändern der Sprechgeschwindigkeit

Die Sprechgeschwindigkeit kann auf Standardstimmen auf Wort- oder Satzebene angewendet werden. Die Sprechgeschwindigkeit kann dagegen nur auf neuronale Stimmen auf Satzebene angewendet werden.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Guy24kRUS'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Aussprache
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Ändern der Lautstärke

Änderungen der Lautstärke können auf Standardstimmen auf Wort- oder Satzebene angewendet werden. Änderungen der Lautstärke können dagegen nur auf neuronale Stimmen auf Satzebene angewendet werden.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Ändern der Tonhöhe

Änderungen der Tonhöhe können auf Standardstimmen auf Wort- oder Satzebene angewendet werden. Änderungen der Tonhöhe können dagegen nur auf neuronale Stimmen auf Satzebene angewendet werden.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='en-US-Guy24kRUS'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Ändern der Tonhöhenkontur

> [!IMPORTANT]
> Änderungen der Tonhöhenkontur werden mit neuronalen Stimmen nicht unterstützt.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>Verwenden mehrerer Stimmen
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Good morning!
</voice>
<voice  name='en-US-Guy24kRUS'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>Nächste Schritte

* [Sprachunterstützung: Stimmen, Gebietsschemas, Sprachen](language-support.md)
