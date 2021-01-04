---
title: Entwurfsmuster für die Offenlegung
titleSuffix: Azure Cognitive Services
description: Entwurfsmuster und bewährte Methoden für die Offenlegung.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: 348277d42c543bc45cf98bfeaf7ea8313afe8f6f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021982"
---
# <a name="disclosure-design-patterns"></a>Entwurfsmuster für die Offenlegung
Nachdem Sie die richtige [Offenlegungsstufe](concepts-disclosure-guidelines.md#disclosure-assessment) für die Erfahrung mit Ihrer synthetischen Stimme festgelegt haben, ist es an der Zeit, mögliche Entwurfsmuster zu untersuchen.
## <a name="overview"></a>Übersicht
Es gibt ein Spektrum von Entwurfsmustern für die Offenlegung, die Sie auf die Erfahrung mit Ihrer synthetischen Stimme anwenden können. Wenn das Ergebnis Ihrer Offenlegungsbewertung „Hohe Offenlegung“ war, empfehlen wir die [**explizite Offenlegung**](#explicit-disclosure), d. h. die Herkunft der synthetischen Stimme vollständig zu kommunizieren. Die [**implizite Offenlegung**](#implicit-disclosure) umfasst Hinweise und Interaktionsmuster, die Spracherfahrungen begünstigen, unabhängig davon, ob die erforderlichen Offenlegungsstufen hoch oder niedrig sind.
![Spektrum von Offenlegungsmustern](media/responsible-ai/disclosure-patterns/affordances.png)






| Explizite Offenlegungsmuster                                                                                                                                                                                    | Implizite Offenlegungsmuster                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Transparente Einführung](#transparent-introduction)<br> [Verbale transparente Einführung](#verbal-transparent-introduction)<br>  [Explizite Quellenangabe](#explicit-byline)<br>  [Anpassung und Kalibrierung](#customization-and-calibration)<br> [Elterliche Offenlegung](#parental-disclosure)<br> [Bereitstellung von Möglichkeiten, um mehr über die Erstellung der Stimme zu erfahren](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Offenlegung von Funktionen](#capability-disclosure)<br>[Implizite Hinweise und Feedback](#implicit-cues--feedback)<br> [Interaktive Transparenz](#conversational-transparency) |



Verwenden Sie die folgende Tabelle, um direkt auf die Muster zu verweisen, die für Ihre synthetische Stimme gelten. Einige der anderen Bedingungen in diesem Diagramm können auch für Ihr Szenario gelten:<br/>



| Wenn Folgendes für die Erfahrung mit Ihrer synthetischen Stimme gilt... | Empfehlungen | Entwurfsmuster |
| --- | --- | --- |
| Erfordert hohe Offenlegung  | Verwenden Sie vorab mindestens ein explizites Muster und implizite Hinweise, um den Benutzern zu helfen, Beziehungen aufzubauen. |[Explizite Offenlegung](#explicit-disclosure)<br>[Implizite Offenlegung](#implicit-disclosure)  |
| Erfordert geringe Offenlegung | Die Offenlegung kann minimal oder unnötig sein, könnte aber von einigen impliziten Mustern profitieren. | [Offenlegung von Funktionen](#capability-disclosure)<br>[Interaktive Transparenz](#conversational-transparency)  |
| Verfügt über ein hohes Maß an Bindung | Erstellt für die langfristige Nutzung und Bereitstellung mehrerer Einstiegspunkte für die Offenlegung entlang der User Journey. Es wird dringend empfohlen, über ein Onboardingerlebnis zu verfügen. | [Transparente Einführung](#transparent-introduction)<br>[Anpassung und Kalibrierung](#customization-and-calibration)<br>[Offenlegung von Funktionen](#capability-disclosure) |
| Umfasst Kinder als primäre Zielgruppe | Ausrichtung auf Eltern als primäre Zielgruppe für die Offenlegung und Sicherstellung, dass sie den Kindern die Offenlegung effektiv vermitteln.  | [Elterliche Offenlegung](#parental-disclosure)<br>[Verbale transparente Einführung](#verbal-transparent-introduction)<br> [Implizite Offenlegung](#implicit-disclosure)<br> [Interaktive Transparenz](#conversational-transparency)  |
| Schließt blinde Benutzer oder Personen mit eingeschränkter Sehkraft als primäre Zielgruppe ein.  | Bezieht alle Benutzer ein und stellt sicher, dass jede Form der visuellen Offenlegung mit alternativen Text- oder Soundeffekten verbunden ist. Einhaltung der Standards der Barrierefreiheit für Kontrastverhältnis und Anzeigeformat. Verwendung von akustischen Hinweisen zur Kommunikation der Offenlegung.  | [Verbale transparente Einführung](#verbal-transparent-introduction) <br>[Akustische Hinweise](#implicit-cues--feedback)<br>[Haptische Hinweise](#implicit-cues--feedback)<br>[Interaktive Transparenz](#conversational-transparency)<br>[Standards der Barrierefreiheit](https://www.microsoft.com/accessibility) |
| Besitzt keinen Bildschirm, kein Gerät oder verwendet die Stimme als primären oder einzigen Interaktionsmodus. | Verwendung von akustischen Hinweisen zur Kommunikation der Offenlegung. | [Verbale transparente Einführung](#verbal-transparent-introduction) <br> [Akustische Hinweise](#implicit-cues--feedback)  |
| Umfasst potenziell mehrere Benutzer/Zuhörer (z. B. persönlicher Assistent in mehreren Haushalten).  | Achten Sie auf verschiedene Benutzerkontexte und Verständnisebenen und bieten Sie mehrere Möglichkeiten der Offenlegung für die User Journey.  | [Transparente Einführung (Wiederkehrender Benutzer)](#transparent-introduction)<br> [Bereitstellung von Möglichkeiten, um mehr über die Erstellung der Stimme zu erfahren](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Interaktive Transparenz](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Explizite Offenlegung
Wenn die Erfahrung Ihrer synthetischen Stimme eine hohe Offenlegung erfordert, ist es am besten, mindestens eines der folgenden expliziten Muster zu verwenden, um die synthetische Natur eindeutig darzustellen.
### <a name="transparent-introduction"></a>Transparente Einführung

Bevor die Spracherfahrung beginnt, stellen Sie den digitalen Assistenten vor, wobei Sie hinsichtlich der Herkunft seiner Stimme und seinen Funktionen völlig transparent informieren. Der optimale Zeitpunkt für die Verwendung dieses Musters ist, wenn Sie einen neuen Benutzer einbinden oder einem wiederkehrenden Benutzer neue Features vorstellen. Die Implementierung impliziter Hinweise während einer Einführung hilft den Benutzern, ein mentales Modell zur synthetischen Natur des digitalen Agenten zu entwickeln.

#### <a name="first-time-user-experience"></a>Erfahrung erstmaliger Benutzer

![Transparente Einführung bei der Erfahrung beim ersten Durchlauf](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*Die synthetische Stimme wird beim Onboarding eines neuen Benutzers eingeführt.*

Empfehlungen
- Beschreiben Sie, dass die Stimme künstlich ist (z. B. &quot;digital&quot;).
- Beschreiben Sie, wozu der Agent in der Lage ist.
- Geben Sie explizit die Ursprünge der Stimme an.
- Bieten Sie einen Einstiegspunkt, um mehr über die synthetische Stimme zu erfahren.

#### <a name="returning-user-experience"></a>Erfahrung wiederkehrender Benutzer

Wenn ein Benutzer die Onboardingerfahrung überspringt, bieten Sie weiterhin Einstiegspunkte für die transparente Einführung an, bis der Benutzer die Stimme zum ersten Mal auslöst.
<br/>

![Transparente Einführung bei der Erfahrung wiederkehrender Benutzer](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Bereitstellung eines konsistenten Einstiegspunkts in die Erfahrung mit der synthetischen Stimme. Ermöglicht es dem Benutzer, zur Onboardingerfahrung zurückzukehren, wenn er die Stimme zum ersten Mal zu einem beliebigen Zeitpunkt der User Journey auslöst.*


### <a name="verbal-transparent-introduction"></a>Verbale transparente Einführung

Eine gesprochene Eingabeaufforderung, die die Herkunft der Stimme des digitalen Assistenten angibt, ist für sich ausreichend explizit, dass eine Offenlegung erreicht wird. Dieses Muster eignet sich am besten für Szenarien mit hoher Offenlegung, bei denen die Stimme der einzige verfügbare Interaktionsmodus ist.
<br/>

![Verbal gesprochene transparente Einführung](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Verwenden Sie eine transparente Einführung, wenn es Momente in der Benutzererfahrung gibt, in denen Sie die Stimme einer Person bereits einführen oder zuordnen können.*


![Verbal gesprochene transparente Einführung in der ersten Person](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*Zur zusätzlichen Transparenz kann der Sprecher die Ursprünge der synthetischen Stimme in der ersten Person offenlegen.*

### <a name="explicit-byline"></a>Explizite Quellenangabe

Verwenden Sie dieses Muster, wenn der Benutzer mit einem Audioplayer oder einer interaktiven Komponente interagieren soll, um die Stimme auszulösen.


![Explizite Quellenangabe in einem Nachrichtenmedienszenario](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Eine explizite Quellenangabe ist die Zuordnung dessen, woher die Stimme stammt.*

Empfehlungen

- Bieten Sie einen Einstiegspunkt, um mehr über die synthetische Stimme zu erfahren.

### <a name="customization-and-calibration"></a>Anpassung und Kalibrierung

Bieten Sie den Benutzern die Kontrolle darüber, wie der digitale Assistent auf sie reagiert (d. h. wie die Stimme klingt).  Wenn ein Benutzer mit einem System zu seinen eigenen Bedingungen und mit Blick auf bestimmte Ziele interagiert, dann hat er per Definition bereits verstanden, dass es sich nicht um eine echte Person handelt.

#### <a name="user-control"></a>Benutzerkontrolle

Bieten Sie Optionen an, die einen bedeutsamen und spürbaren Einfluss auf die Erfahrung mit der synthetischen Stimme haben.

![Benutzereinstellungen](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*Benutzereinstellungen ermöglichen es Benutzern, ihr Erlebnis anzupassen und zu verbessern.*

Empfehlungen

- Ermöglichen Sie es Benutzern, die Stimme anzupassen (z. B. Sprache und Sprachtyp auswählen).
- Bieten Sie Benutzern eine Möglichkeit, dem System beizubringen, auf seine einzigartige Stimme zu reagieren (z. B. Sprachkalibrierung, benutzerdefinierte Befehle).
- Führen Sie eine Optimierung für benutzergenerierte oder kontextuelle Interaktionen (z. B. Erinnerungen) durch.

#### <a name="persona-customization"></a>Anpassung der Persona

Bieten Sie Möglichkeiten zur Anpassung der Stimme des digitalen Assistenten. Wenn die Stimme auf einer prominenten oder allgemein erkennbaren Person basiert, sollten Sie sowohl visuelle als auch gesprochene Einführungen verwenden, wenn Benutzer eine Vorschau auf die Stimme hören.

![Stimmanpassung](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Die Möglichkeit, aus einer Reihe von Stimmen auszuwählen, hilft dabei, die künstliche Natur zu vermitteln.*

Empfehlungen
- Bieten Sie den Benutzern eine Vorschau auf den Klang der einzelnen Stimmen.
- Verwenden Sie für jede Stimme eine authentische Einführung.
- Bieten Sie Einstiegspunkte, um mehr über die synthetische Stimme zu erfahren.

### <a name="parental-disclosure"></a>Elterliche Offenlegung

Zusätzlich zur Einhaltung der COPPA-Vorschriften sollten Sie die Eltern darüber informieren, wenn Ihr primäres Zielpublikum kleine Kinder sind und Ihre Offenlegungsstufe hoch ist. Bei sensiblen Anwendungen ist es sinnvoll, die Benutzererfahrung so lange zu sperren, bis ein Erwachsener die Verwendung der synthetischen Stimme bestätigt hat. Ermutigen Sie die Eltern, die Nachricht an ihre Kinder weiterzugeben.

![Offenlegung für Eltern](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*Eine für Eltern optimierte transparente Einführung stellt sicher, dass ein Erwachsener auf die synthetische Natur der Stimme aufmerksam gemacht wurde, bevor ein Kind mit ihr interagiert.*

Empfehlungen

- Richten Sie die Offenlegung auf Eltern als primäre Zielgruppe aus.
- Ermutigen Sie die Eltern, die Offenlegung an ihre Kinder weiterzugeben.
- Bieten Sie Einstiegspunkte, um mehr über die synthetische Stimme zu erfahren.
- Sperren Sie die Benutzererfahrung, indem Sie den Eltern eine einfache &quot;Sicherheitsfrage&quot; stellen, um zu zeigen, dass sie die Offenlegung gelesen haben.

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Bereitstellung von Möglichkeiten, um mehr über die Erstellung der Stimme zu erfahren

Bieten Sie kontextabhängige Einstiegspunkte für eine Seite, ein Popupfenster oder eine externe Website, die mehr Informationen über die Technologien mit künstlicher Sprache bieten. Sie können z. B. einen Link einblenden, um während des Onboardings mehr zu erfahren, oder wenn der Benutzer während einer Unterhaltung weitere Informationen anfordert.

![Einstiegspunkt für weitere Informationen](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Beispiel eines Einstiegspunkts, der die Möglichkeit bietet, mehr über die synthetische Stimme zu erfahren.*

Sobald ein Benutzer weitere Informationen über die synthetische Stimme anfordert, ist das Hauptziel, ihn über die Ursprünge der synthetischen Stimme aufzuklären und in Bezug auf die Technologie transparent vorzugehen.

![Bereitstellen von weiteren Informationen zur synthetischen Stimme für Benutzer](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Weitere Informationen können über eine externe Website bereitgestellt werden.*

Empfehlungen

- Vereinfachen Sie komplexe Konzepte, und vermeiden Sie die Verwendung von juristischem und technischem Fachjargon.
- Verstecken Sie diese Inhalte nicht in Datenschutzerklärungen und Nutzungsbedingungen.
- Halten Sie den Inhalt übersichtlich und verwenden Sie Abbildungen, wenn verfügbar.

## <a name="implicit-disclosure"></a>Implizite Offenlegung

Konsistenz ist der Schlüssel zur impliziten Offenlegung während der gesamten User Journey. Die konsistente Verwendung von visuellen und akustischen Hinweisen über Geräte und Interaktionsformen hinweg kann dazu beitragen, Assoziationen zwischen impliziten Mustern und expliziter Offenlegung herzustellen.

![Konsistenz der impliziten Hinweise](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Implizite Hinweise und Feedback

Anthropomorphismus kann sich auf verschiedene Weise manifestieren, von der tatsächlichen visuellen Darstellung des Agents über die Stimme, Geräusche, Lichtmuster, hüpfende Formen bis hin zur Vibration eines Geräts. Nutzen Sie bei der Definition Ihrer Persona implizite Hinweise und Feedbackmuster, anstatt auf einen sehr menschenähnlichen Avatar zu setzen. Dies ist eine Möglichkeit, die Notwendigkeit einer expliziteren Offenlegung zu minimieren.

![Visuelle Hinweise und Feedback](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Diese Hinweise helfen, den Agent zu vermenschlichen, ohne zu menschenähnlich zu werden. Sie können auch selbst zu effektiven Offenlegungsmechanismen werden, wenn sie im Laufe der Zeit konsistent eingesetzt werden.*

Berücksichtigen Sie die verschiedenen Interaktionsmodi Ihrer Benutzererfahrung, wenn Sie die folgenden Arten von Hinweisen integrieren:

| Visuelle Hinweise                                                                                                                                                               | Akustische Hinweise                                                      | Haptische Hinweise |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>Dynamische Hinweise in Echtzeit (z. B. Animationen)<br> Nicht auf dem Bildschirm angezeigte Hinweise (z. B. Lichter und Muster auf einem Gerät)<br>  | Sonicon (z. B. ein kurzer unverwechselbarer Sound, eine Reihe von Noten) | Vibration   |

### <a name="capability-disclosure"></a>Offenlegung von Funktionen

Die Offenlegung kann implizit erreicht werden, indem genaue Erwartungen an das, was der digitale Assistent kann, gestellt werden. Stellen Sie Beispielbefehle bereit, damit Benutzer lernen können, wie sie mit dem digitalen Assistenten interagieren, und bieten Sie eine kontextbezogene Hilfe, um mehr über die synthetische Stimme in den frühen Phasen der Benutzererfahrung zu erfahren.

![Beispiel der Standardantworten auf eine Unterhaltung, die Sie gestalten können.](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Interaktive Transparenz

Wenn Unterhaltungen in unerwartete Bahnen verlaufen, sollten Sie die Gestaltung von Standardantworten in Betracht ziehen, die dazu beitragen können, die Erwartungen zurückzusetzen, die Transparenz zu erhöhen und die Benutzer in erfolgreiche Bahnen zu lenken. Es gibt Möglichkeiten, die explizite Offenlegung auch in Unterhaltungen zu nutzen.

![Behandeln unerwarteter Pfade](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Nebensächliche oder &quot;persönliche&quot; Fragen, die an den Agent gerichtet werden, sind ein guter Zeitpunkt, um die Benutzer an die synthetische Natur des Agent zu erinnern und sie dazu zu bringen, sich angemessen damit auseinanderzusetzen oder sie an eine echte Person weiterzuleiten.

![Behandeln von nebensächlichen Fragen](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Zeitpunkt zum Offenlegen

Es gibt viele Möglichkeiten der Offenlegung während der gesamten User Journey. Entwurf für die erste Nutzung, zweite Nutzung, N-te Nutzung..., aber auch zum Annehmen von Momenten mit &quot;Fehlern&quot;, um die Transparenz hervorzuheben – wie bei einem Fehler des Systems oder wenn der Benutzer eine Einschränkung der Fähigkeiten des Agent entdeckt.

![Offenlegungsmöglichkeiten während einer User Journey](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Beispiel für eine Standard-User Journey mit einem digitalen Assistenten, der verschiedene Offenlegungsmöglichkeiten aufzeigt.

### <a name="up-front"></a>Vorab

Der optimale Zeitpunkt für die Offenlegung ist das erste Mal, dass eine Person mit der synthetischen Stimme interagiert.  In einem Szenario mit einem persönlichen Sprachassistenten wäre dies beim Onboarding oder wenn der Benutzer die Erfahrung zum ersten Mal macht. In anderen Szenarien könnte es das erste Mal sein, dass eine synthetische Stimme Inhalte auf einer Website vorliest oder dass ein Benutzer zum ersten Mal mit einem virtuellen Character interagiert.

- [Transparente Einführung](#transparent-introduction)
- [Offenlegung von Funktionen](#capability-disclosure)
- [Anpassung und Kalibrierung](#customization-and-calibration)
- [Implizite Hinweise](#implicit-cues--feedback)

### <a name="upon-request"></a>Auf Anforderung

Die Benutzer sollten bei Bedarf jederzeit während der User Journey auf zusätzliche Informationen zugreifen, Einstellungen steuern und eine transparente Kommunikation erhalten können.

- [Bereitstellung von Möglichkeiten, um mehr über die Erstellung der Stimme zu erfahren](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Anpassung und Kalibrierung](#customization-and-calibration)
- [Interaktive Transparenz](#conversational-transparency)

### <a name="continuously"></a>Fortlaufend

Verwenden Sie die impliziten Entwurfsmuster, die das Benutzererlebnis fortlaufend verbessern.

- [Offenlegung von Funktionen](#capability-disclosure)
- [Implizite Hinweise](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>Wenn das System versagt

Nutzen Sie die Offenlegung als Gelegenheit, um in angemessener Weise zu scheitern.

- [Interaktive Transparenz](#conversational-transparency)
- [Bereitstellung von Möglichkeiten, um mehr über die Erstellung der Stimme zu erfahren](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Übergabe an Mensch](#conversational-transparency)



## <a name="additional-resources"></a>Zusätzliche Ressourcen
- [Microsoft Bot-Richtlinien](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Cortana-Entwurfsrichtlinien](/cortana/voice-commands/voicecommand-design-guidelines)
- [Microsoft Windows UWP Speech – Entwurfsrichtlinien](/windows/uwp/design/input/speech-interactions)
- [Microsoft Windows Mixed Reality – Richtlinien zur Sprachsteuerung](/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>Referenz

* [Offenlegung für Sprecher](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Richtlinien für die verantwortungsvolle Bereitstellung von Technologien mit künstlicher Sprache](concepts-guidelines-responsible-deployment-synthetic.md)
* [Kontrollübersicht](concepts-gating-overview.md)
* [Vorgehensweise zum Offenlegen](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>Nächste Schritte

* [Offenlegung für Sprecher](/legal/cognitive-services/speech-service/disclosure-voice-talent)