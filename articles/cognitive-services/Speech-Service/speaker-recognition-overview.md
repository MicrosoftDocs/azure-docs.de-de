---
title: 'Übersicht über die Sprechererkennung: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Die Sprechererkennung bietet Algorithmen, mit denen Sprecher anhand ihrer individuellen Stimmmerkmale mithilfe von Sprachbiometrie überprüft und identifiziert werden. Die Sprechererkennung wird verwendet, um herauszufinden, wer spricht. Dieser Artikel bietet einen Überblick über die Vorteile und Funktionen des Sprechererkennungsdiensts.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: Sprechererkennung, Sprachbiometrie
ms.openlocfilehash: d07a9960c8a586fa137b4b717afbf91740c265d3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015323"
---
# <a name="what-is-speaker-recognition-preview"></a>Was ist Sprechererkennung (Vorschau)?

Der Dienst „Sprechererkennung“ bietet Algorithmen, mit denen Sprecher anhand ihrer individuellen Stimmmerkmale mithilfe von Sprachbiometrie überprüft und identifiziert werden. Die Sprechererkennung wird verwendet, um herauszufinden, wer spricht. Wenn Sie Audiotrainingsdaten für einen einzelnen Sprechers bereitstellen, wird basierend auf den eindeutigen Stimmmerkmalen des Sprechers ein Registrierungsprofil erstellt. Sie können anschließend Sprachbeispiele mit diesem Profil abgleichen, um zu überprüfen, ob es sich um denselben Sprecher handelt (Sprecherüberprüfung), oder Sprachbeispiele mit einer *Gruppe* von registrierten Sprecherprofilen abgleichen, um herauszufinden, ob es eine Übereinstimmung mit einem Profil gibt (Sprecheridentifikation). Im Gegensatz dazu gruppiert die [Sprecherdiarisierung](batch-transcription.md#speaker-separation-diarization) Audiosegmente nach Sprecher in einen Batchvorgang.

## <a name="speaker-verification"></a>Sprecherüberprüfung

Die Sprecherüberprüfung optimiert den Prozess für die Überprüfung einer registrierten Sprecheridentität mithilfe von Passphrasen oder einer Spracheingabe in Freiform. Sie kann verwendet werden, um Kunden sicher und reibungslos für eine Vielzahl von Lösungen zu verifizieren – angefangen bei der Identitätsüberprüfung von Kunden in Callcentern bis hin zu kontaktlosem Zugang zu Gebäuden.

### <a name="how-does-speaker-verification-work"></a>Wie funktioniert die Sprecherüberprüfung?

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="Sprecherüberprüfungs-Flussdiagramm.":::

Die Sprecherüberprüfung kann entweder textabhängig oder textunabhängig sein. Bei der **textabhängigen** Überprüfung müssen die Sprecher sowohl bei der Registrierung als auch bei der Überprüfung dieselbe Passphrase verwenden. Bei der **textunabhängigen** Überprüfung können Sprecher sowohl bei der Registrierung als auch bei der Überprüfung etwas beliebiges sagen.

Bei der **textabhängigen** Überprüfung wird die Stimme des Sprechers registriert, wenn er eine Passphrase aus vorgegebenen Phrasen spricht. Einerseits werden Stimmmerkmale aus der Audioaufzeichnung extrahiert, um eine eindeutige Stimmsignatur zu bilden, und andererseits wird die Passphrase erkannt. Die Stimmsignatur und die Passphrase werden verwendet, um den Sprecher zu überprüfen. 

Bei der **textunabhängigen** Überprüfung gibt es keine Vorgaben für den zu sprechenden Text bei der Registrierung oder für das Audiobeispiel, das überprüft werden soll, da nur Stimmmerkmale extrahiert werden, um die Ähnlichkeit zu bewerten. 

Die APIs sind nicht dafür vorgesehen zu bestimmen, ob das Audio von einer echten Person oder einer Imitation oder Aufzeichnung eines registrierten Sprechers stammt. 

## <a name="speaker-identification"></a>Sprecheridentifikation

Die Sprecheridentifikation wird verwendet, um die Identität eines unbekannten Sprechers innerhalb einer Gruppe registrierter Sprecher zu ermitteln. Sie ermöglicht es Ihnen, Spracheingaben einzelnen Sprechern zuzuordnen und einen Mehrwert aus Szenarios mit mehreren Sprechern zu ziehen:

* Supportlösungen für die Produktivität bei Remotemeetings 
* Erstellen von Gerätepersonalisierung mit mehreren Benutzern

### <a name="how-does-speaker-identification-work"></a>Wie funktioniert die Sprecheridentifikation?

Die Registrierung für die Sprecheridentifikation ist **textunabhängig**. Es spielt also keine Rolle, was der Sprecher in der Audioaufnahme sagt. Ähnlich wie bei der Sprecherüberprüfung wird in der Registrierungsphase die Stimme des Sprechers aufgezeichnet. Stimmmerkmale werden extrahiert, um eine eindeutige Stimmsignatur zu bilden. In der Identifikationsphase wird das Spracheingabebeispiel mit einer bestimmten Liste registrierter Stimmen verglichen (bis zu 50 in jeder Anforderung).

## <a name="data-security-and-privacy"></a>Datensicherheit und -schutz

Sprecheranmeldungsdaten werden in einem gesicherten System gespeichert, einschließlich der Sprachaudioinformationen für die Registrierung und der Sprachsignaturmerkmale. Die Sprachaudiodaten für die Registrierung wird nur beim Upgrade des Algorithmus verwendet, und die Merkmale müssen noch einmal extrahiert werden. Der Dienst speichert weder die Sprachaufzeichnung noch die extrahierten Stimmmerkmale, die während der Erkennungsphase an den Dienst gesendet werden. 

Sie steuern, wie lange Daten aufbewahrt werden sollen. Sie können über API-Aufrufe Registrierungsdaten für einzelne Sprecher erstellen, aktualisieren und entfernen. Beim Löschen des Abonnements werden auch alle mit dem Abonnement verbundenen Sprecherregistrierungsdaten gelöscht. 

Wie bei allen Cognitive Services-Ressourcen müssen Entwickler, die den Sprechererkennungsdienst nutzen, die Microsoft-Richtlinien zu Kundendaten beachten. Sie müssen sicherstellen, dass Sie von den Benutzern die entsprechende Erlaubnis für die Sprechererkennung erhalten haben. Weitere Informationen finden Sie im Microsoft Trust Center auf der  [Seite zu Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/) . 

## <a name="common-questions-and-solutions"></a>Häufige Fragen und Lösungen

| Frage | Lösung |
|---------|----------|
| Für welche Szenarien kann die Sprechererkennung verwendet werden? | Kundenüberprüfung in Callcentern, sprachbasierter Check-In von Patienten, Besprechungstranskription, Personalisierung von Geräten mit mehreren Benutzern|
| Was ist der Unterschied zwischen Identifizierung und Überprüfung? | Identifikation ist der Prozess der Erkennung, welches Mitglied aus einer Gruppe von Sprechern spricht. Mit der Überprüfung wird bestätigt, dass ein Sprecher mit einer bekannten oder **registrierten** Stimme übereinstimmt.|
| Was ist der Unterschied zwischen textabhängiger und textunabhängiger Überprüfung? | Bei der textabhängigen Überprüfung ist eine bestimmte Passphrase für Registrierung und Erkennung erforderlich. Bei der textunabhängigen Überprüfung ist ein längeres Stimmbeispiel für die Registrierung erforderlich, wobei aber ein beliebiger Text gesprochen werden kann, auch während der Erkennung.|
| Welche Sprachen werden unterstützt? | Englisch, Französisch, Spanisch, Chinesisch, Deutsch, Italienisch, Japanisch und Portugiesisch |
| Welche Azure-Regionen werden unterstützt? | Sprechererkennung ist ein Vorschaudienst und derzeit nur in der Region „USA, Westen“ verfügbar.|
| Welche Audioformate werden unterstützt? | Mono 16 Bit, 16 kHz PCM-codiertes WAV |
| Die Antworten für **Akzeptieren** und **Ablehnen** sind nicht genau, wie optimieren Sie den Schwellenwert? | Da der optimale Schwellenwert je nach Szenario stark schwankt, entscheidet die API einfach auf Grundlage eines Standardschwellenwerts von 0,5, ob „Akzeptiert“ oder „Abgelehnt“ wird. Erfahreneren Benutzern wird empfohlen, die Standardentscheidung außer Kraft zu setzen und das Ergebnis auf Grundlage ihres eigenen Szenarios zu optimieren. |
| Können Sie einen Sprecher mehrmals registrieren? | Ja, bei der textabhängigen Überprüfung können Sie einen Sprecher bis zu 50-mal registrieren. Bei der textunabhängigen Überprüfung oder der Sprecheridentifikation können Sie die Registrierung mit bis zu 300 Sekunden Audioeingabe durchführen. |
| Welche Daten werden in Azure gespeichert? | Registrierungsaudiodaten werden im Dienst gespeichert, bis das Stimmprofil [gelöscht](./get-started-speaker-recognition.md#deleting-voice-profile-enrollments) wird. Erkennungsaudiobeispiele werden nicht aufbewahrt oder gespeichert. |

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> * Lesen Sie den [Artikel zu den Grundlagen](./get-started-speaker-recognition.md) der Sprechererkennung, um einen Überblick über gängige Entwurfsmuster zu erhalten, die Sie in Ihren Anwendungen verwenden können.
> * Weitere Informationen finden Sie im [Videotutorial](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) zur textunabhängigen Sprecherüberprüfung.