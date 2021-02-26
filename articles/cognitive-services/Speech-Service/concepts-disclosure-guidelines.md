---
title: Entwurfsrichtlinien für die Offenlegung
titleSuffix: Azure Cognitive Services
description: Einführung in die Richtlinien für den Entwurf von Offenlegungen und die Bewertung der Offenlegungsstufe.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: f1889d49d1eb4981e59af6248817a6a9b6e2002d
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537254"
---
# <a name="disclosure-design-guidelines"></a>Entwurfsrichtlinien für die Offenlegung
Erfahren Sie, wie Sie Vertrauen zu Ihren Kunden aufbauen und erhalten können, indem Sie sich in Bezug auf die synthetische Natur Ihres Spracherlebnisses transparent verhalten.

## <a name="what-is-disclosure"></a>Was ist Offenlegung?

Die Offenlegung ist ein Mittel, um Personen wissen zu lassen, dass sie mit einer Stimme interagieren oder ihr zuhören, die synthetisch generiert wird.

## <a name="why-is-disclosure-necessary"></a>Warum ist die Offenlegung erforderlich?

Die Notwendigkeit, die synthetischen Ursprünge einer computergenerierten Stimme offenzulegen, ist relativ neu. In der Vergangenheit war für computergenerierte Stimmen offensichtlich, dass niemand sie jemals mit einer realen Person verwechseln würde. Jeden Tag verbessert sich jedoch der Realismus synthetischer Stimmen, und sie lassen sich immer weniger von menschlichen Stimmen unterscheiden.

## <a name="goals"></a>Ziele
Dies sind die Prinzipien, die beim Entwurf synthetischer Stimmen zu beachten sind:

**Vertrauen stärken**
<br>Entwerfen Sie mit der Absicht, den Turing-Test nicht zu bestehen, ohne die Erfahrung zu beeinträchtigen. Teilen Sie den Benutzern mit, dass sie mit einer synthetischen Stimme interagieren und sich gleichzeitig problemlos auf das Erlebnis einlassen können.

**Anpassen an den Kontext der Verwendung**
<br>Verstehen Sie, wann, wo und wie Ihre Benutzer mit der synthetischen Stimme interagieren werden, um die richtige Art der Offenlegung zur richtigen Zeit bereitzustellen.

**Festlegen eindeutiger Erwartungen**
<br>Ermöglichen Sie es Benutzern, die Funktionen des Agents leicht zu erfassen und zu verstehen. Bieten Sie auf Anfrage die Möglichkeit, mehr über die synthetische Sprachtechnologie zu erfahren.

**Akzeptieren von Fehlern**
<br>Nutzen Sie Momente des Scheiterns, um die Funktionen des Agents zu stärken.

## <a name="how-to-use-this-guide"></a>Verwendung dieses Leitfadens

Dieser Leitfaden hilft Ihnen festzustellen, welche Offenlegungsmuster am besten für Ihr synthetisches Spracherlebnis geeignet sind. Wir stellen dann Beispiele zur Verfügung, wie und wann sie zu verwenden sind. Jedes dieser Muster wurde entwickelt, um die Transparenz hinsichtlich der synthetischen Sprache für den Benutzer zu maximieren und gleichzeitig dem benutzerorientierten Entwurf treu zu bleiben.

In Anbetracht der umfangreichen Gestaltungsrichtlinien für Spracherlebnisse konzentrieren wir uns hier speziell auf Folgendes:

1. [**Bewertung der Offenlegung**](#disclosure-assessment): Ein Prozess zur Ermittlung der Art der Offenlegung, die für die Erfahrung mit Ihrer synthetischen Stimme empfohlen wird.

2. [**Vorgehensweise zum Offenlegen**](concepts-disclosure-patterns.md): Beispiele für Offenlegungsmuster, die auf die Erfahrung mit Ihrer synthetischen Stimme angewendet werden können.

3. [**Zeitpunkt zum Offenlegen**](concepts-disclosure-patterns.md#when-to-disclose): Optimale Momente, die auf der gesamten User Journey offengelegt werden müssen.

## <a name="disclosure-assessment"></a>Bewertung der Offenlegung
Berücksichtigen Sie die Erwartungen Ihrer Benutzer hinsichtlich der Interaktion und dem Kontext, in denen sie die Stimme erleben werden. Wenn der Kontext deutlich macht, dass eine synthetische Stimme &quot;spricht,&quot; kann die Offenlegung minimal, kurzzeitig oder sogar unnötig sein. Die wichtigsten Kontexttypen, die sich auf die Offenlegung auswirken, sind der Persönlichkeitstyp, der Szenariotyp und die Offenlegungsstufe. Es hilft außerdem zu berücksichtigen, wer möglicherweise zuhört.

### <a name="understand-context"></a>Verstehen des Kontexts

Verwenden Sie dieses Arbeitsblatt, um den Kontext für die Erfahrung mit Ihrer synthetischen Stimme zu ermitteln. Sie werden dies im nächsten Schritt anwenden, in dem Sie Ihre Offenlegungsstufe ermitteln.

|                                    | Kontext der Verwendung                                                                                                                                                                                                                                                                                                                                                       | Mögliche Risiken und Herausforderungen                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Persönlichkeitstyp**               | **Wenn einer der folgenden Punkte zutrifft, passt Ihre Persona unter die Kategorie „Menschenähnliche Persona“:**<br><br><ul><li> Persona verkörpert einen echten Menschen, unabhängig davon, ob es sich um eine fiktive Darstellung handelt. (z. B. Foto oder eine computergenerierte Darstellung einer realen Person)<br><br><li> Die synthetische Stimme basiert auf der Stimme einer allgemein erkennbaren realen Person (z. B. Prominenz, politische Figur). | Je ähnlicher die Darstellungen Ihrer Persona einem Menschen sind, desto wahrscheinlicher ist es, dass ein Benutzer sie mit einer realen Person in Verbindung bringt oder sie glauben lässt, dass der Inhalt von einer realen Person gesprochen und nicht von einem Computer generiert wird. </ul>                                                                                                                                                                      |
| **2. Szenariotyp**            | **Wenn einer der folgenden Punkte zutrifft, passt Ihr Stimmerlebnis in die Kategorie „Einfühlsam“:**<br><br><ul><li> Erhält oder zeigt persönliche Informationen vom Benutzer an. <br><br> <li> Überträgt zeitkritische Nachrichten/Informationen (z. B. Notfallwarnung).<br><br><li> Zielt darauf ab, echten Menschen zu helfen, miteinander zu kommunizieren (z. B. persönliche E-Mails/Texte lesen).<br><br> <li> Bietet medizinische/gesundheitliche Unterstützung an. </ul>            | Die Verwendung synthetischer Stimmen fühlt sich für die Personen, die sie verwenden, möglicherweise nicht angemessen oder vertrauenswürdig an, wenn es sich um Themen handelt, die sich auf vertrauliche, persönliche oder dringende Angelegenheiten beziehen. Sie erwarten möglicherweise auch dasselbe Maß an Empathie und Kontextbezogenheit wie bei einem echten Menschen. |
| **3. Offenlegungsstufe** |**Ihr Stimmerlebnis passt höchstwahrscheinlich in die Kategorie „Hoch“, wenn Folgendes zutrifft:** <br><br><ul><li>Der Benutzer wird die synthetische Stimme häufig oder über einen längeren Zeitraum hören oder mit ihr interagieren. </ul>                                                                                                                                                                             | Die Bedeutung von Transparenz und Vertrauensbildung gegenüber den Nutzern ist bei der Etablierung langfristiger Beziehungen noch größer.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Ermitteln der Offenlegungsstufe

Verwenden Sie das folgende Diagramm, um zu ermitteln, ob die Erfahrung mit Ihrer synthetischen Stimme eine starke oder weniger starke Offenlegung erfordert, basierend auf Ihrem Anwendungskontext.

  ![Bewertungsdiagramm für die Offenlegung](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="see-also"></a>Weitere Informationen

* [Entwurfsmuster für die Offenlegung](concepts-disclosure-patterns.md)
* [Offenlegung für Sprecher](https://docs.microsoft.com/legal/cognitive-services/speech-service/disclosure-voice-talent?context=/azure/cognitive-services/speech-service/context/context)
* [Richtlinien für die verantwortungsvolle Bereitstellung von Technologien mit künstlicher Sprache](concepts-guidelines-responsible-deployment-synthetic.md)