---
title: Dynamisch gesperrte Kennwörter – Azure Active Directory
description: Sperren Sie mit dynamisch gesperrten Kennwörtern in Azure AD unsichere Kennwörter aus Ihrer Umgebung aus.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50452dc5a0c2074c452878c890643f7b21591689
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65977305"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Beseitigen falscher Kennwörter in Ihrer Organisation

Branchenführer schärfen Ihnen ein, dasselbe Kennwort nicht mehrmals zu verwenden, ein Kennwort komplex zu gestalten und nicht einfach Kennwörter wie „Kennwort123“ zu verwenden. Wie können Organisationen gewährleisten, dass ihre Benutzer Anleitungen folgen? Wie können sie sicherstellen, dass Benutzer keine gebräuchlichen Kennwörter verwenden, oder Kennwörter, die bekanntermaßen in neueste Datenschutzverstöße verwickelt sind?

## <a name="global-banned-password-list"></a>Liste global gesperrter Kennwörter

Bei Microsoft arbeiten wir ununterbrochen daran, den Cyberkriminellen immer einen Schritt voraus zu sein. Aus diesem Grund sucht das Azure AD Identity Protection-Team ständig nach häufig verwendeten und gefährdeten Kennwörtern. Dann werden Kennwörter, die als zu häufig verwendet eingestuft werden, durch Eintrag in die Liste global gesperrter Kennwörter blockiert. Weil Cyberkriminelle in ihren Angriffen ähnliche Strategien verwenden, veröffentlicht Microsoft die Inhalte dieser Liste nicht. Diese anfälligen Kennwörter werden blockiert, bevor sie für die Kunden von Microsoft ein echtes Risiko darstellen. Weitere Informationen zur derzeitigen Arbeit in Bezug auf Sicherheitsmaßnahmen finden Sie im [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="custom-banned-password-list"></a>Benutzerdefinierte Liste gesperrter Kennwörter

Manche Organisationen möchten möglicherweise in puncto Sicherheit noch einen Schritt weiter gehen, indem sie die Liste global gesperrter Kennwörter mit ihren eigenen Anpassungen in der von Microsoft als benutzerdefinierte Liste gesperrter Kennwörter bezeichneten Liste ergänzen. Enterprisekunden wie Contoso könnten dann wählen, Varianten ihres Markennamens, unternehmensspezifische Begriffe oder andere Elemente zu blockieren.

Die benutzerdefinierte Liste gesperrter Kennwörter sowie die Möglichkeit, eine lokale Active Directory-Integration zu aktivieren, wird mit dem Azure-Portal verwaltet.

![Ändern der benutzerdefinierten Liste gesperrter Kennwörter unter „Authentifizierungsmethoden“](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Lokale Hybridszenarien

Schützen von ausschließlichen Cloudkonten ist hilfreich, aber viele Organisationen behalten Hybridszenarien einschließlich des lokalen Windows Server Active Directory bei. Es ist möglich, Azure AD-Kennwortschutz für Windows Server Active Directory-Agents lokal zu installieren, um die Liste gesperrter Kennwörter auf Ihre vorhandene Infrastruktur auszudehnen. Zurzeit müssen Benutzer und Administratoren, die Kennwörter lokal ändern, festlegen oder zurücksetzen, die gleiche Kennwortrichtlinie einhalten wie ausschließliche Cloudbenutzer.

## <a name="how-are-passwords-evaluated"></a>Auswerten von Kennwörtern

Wann immer ein Benutzer sein Kennwort ändert oder zurücksetzt, wird das neue Kennwort auf Stärke und Komplexität überprüft, indem es sowohl mit der globalen als auch mit der benutzerdefinierten Liste der verbotenen Kennwörter (falls diese konfiguriert ist) verglichen wird.

Selbst wenn das Kennwort eines Benutzers ein gesperrtes Kennwort enthält, kann das Kennwort dennoch akzeptiert werden, wenn das Gesamtkennwort ansonsten stark genug ist. Ein neu konfiguriertes Kennwort durchläuft die folgenden Schritte, um seine Gesamtstärke zu beurteilen und festzustellen, ob es akzeptiert oder abgelehnt werden sollte.

### <a name="step-1-normalization"></a>Schritt 1: Normalisierung

Ein neues Kennwort durchläuft zunächst einen Normalisierungsprozess. Dies ermöglicht es, einen kleinen Satz verbotener Kennwörter auf einen viel größeren Satz potenziell schwacher Kennwörter zu übertragen.

Die Normalisierung besteht aus zwei Teilen.  Zunächst werden alle Großbuchstaben in Kleinbuchstaben umgewandelt.  Zweitens werden häufige Zeichen ersetzt, wie z.B.:  

| Ursprünglicher Buchstabe  | Ersetzter Buchstabe |
| --- | --- |
| '0'  | 'o' |
| '1'  | 'l' |
| '$'  | 's' |
| '\@'  | 'a' |

Beispiel: Nehmen wir an, dass das Kennwort „blank“ gesperrt ist und ein Benutzer versucht, sein Kennwort in „Bl@nK“ zu ändern. Obwohl „Bl@nk“ nicht ausdrücklich verboten ist, wandelt der Normalisierungsprozess dieses Kennwort in „blank“ um, was ein gesperrtes Kennwort ist.

### <a name="step-2-check-if-password-is-considered-banned"></a>Schritt 2: Überprüfen, ob das Kennwort als gesperrt angesehen wird

#### <a name="fuzzy-matching-behavior"></a>Verhaltens der Fuzzyübereinstimmung

Die Fuzzyübereinstimmung wird für das normalisierte Kennwort verwendet, um festzustellen, ob es ein Kennwort enthält, das entweder in der globalen oder in der benutzerdefinierten Liste der gesperrten Kennwörter enthalten ist. Der Abgleichsprozess basiert auf einer Edit-Distanz eines (1) Vergleichs.  

Beispiel: Nehmen wir an, dass das Kennwort „leer“ gesperrt ist und ein Benutzer versucht, sein Kennwort in eines der folgenden zu ändern.

‘abcdeg’    *(letztes Zeichen von ‘f’ in ‘g’ geändert)* ‘abcdefg’   *’(g’ am Ende angefügt)* ‘abcde’     *(nachfolgendes ‘f’ wurde am Ende gelöscht)*

Jedes der obigen Kennwörter entspricht nicht spezifisch dem verbotenen Kennwort „abcdef“. Da sich jedoch jedes Beispiel in einer Edit-Distanz von 1 des verbotenen Tokens „abcdef“ befindet, werden sie alle als Übereinstimmung mit „abcdef“ betrachtet.

#### <a name="substring-matching-on-specific-terms"></a>Abgleich der Teilzeichenfolge (für bestimmte Ausdrücke)

Der Abgleich der Teilzeichenfolge wird für das normalisierte Kennwort verwendet, um den Vor- und Nachnamen des Benutzers sowie den Mandantennamen zu überprüfen (beachten Sie, dass der Abgleich des Mandantennamens nicht durchgeführt wird, wenn Kennwörter auf einem Active Directory-Domänencontroller überprüft werden).

Beispiel: Der Benutzer Pol möchte sein Kennwort auf „P0l123fb“ zurücksetzen. Nach der Normalisierung würde dieses Kennwort „pol123fb“ lauten. Durch den Abgleich der Teilzeichenfolge wird festgestellt, dass das Kennwort den Vornamen des Benutzers, „Pol“, enthält. Obwohl „P0l123fb“ in keiner Liste gesperrter Kennwörter enthalten war, wurde beim Abgleich der Teilzeichenfolgen „Pol“ im Kennwort gefunden. Aus diesem Grund würde dieses Kennwort zurückgewiesen werden.

#### <a name="score-calculation"></a>Bewertungsberechnung

Der nächste Schritt besteht darin, alle Fälle von verbotenen Kennwörtern in dem normalisierten neuen Kennwort des Benutzers zu identifizieren. Führen Sie dann folgende Schritte aus:

1. Jedes verbotene Kennwort, das sich im Kennwort eines Benutzers befindet, erhält einen Punkt.
2. Jedes verbleibende eindeutige Zeichen erhält einen Punkt.
3. Ein Kennwort muss mindestens 5 Punkte erhalten, um akzeptiert zu werden.

Für die nächsten beiden Beispiele nehmen wir an, dass Contoso den Azure AD-Kennwortschutz verwendet und „contoso“ auf seiner benutzerdefinierten Liste hat. Nehmen wir außerdem an, „blank“ befindet sich auf der globalen Liste.

Beispiel: ein Benutzer ändert sein Kennwort in „C0ntos0Blank12“.

Nach der Normalisierung würde dieses Kennwort „contosoblank12“ lauten. Der Abgleichsprozess ermittelt, dass dieses Kennwort zwei gesperrte Kennwörter enthält: „contoso“ und „blank“. Dieses Kennwort erhält anschließend eine Bewertung:

[contoso] + [blank] + [1] + [2] = 4 Punkte. Da das Kennwort weniger als fünf Punkte erhält, wird es abgelehnt.

Beispiel: ein Benutzer ändert sein Kennwort in „ContoS0Bl@nkf9“.

Nach der Normalisierung würde dieses Kennwort „contosoblankf9!“ lauten. Der Abgleichsprozess ermittelt, dass dieses Kennwort zwei gesperrte Kennwörter enthält: „contoso“ und „blank“. Dieses Kennwort erhält anschließend eine Bewertung:

[Contoso] + [leere] + [f] + [9] + [!] = 5 Punkte. Da dieses Kennwort mindestens fünf Punkte erhalten hat, wird es akzeptiert.

   > [!IMPORTANT]
   > Bitte beachten Sie, dass der Algorithmus für verbotene Kennwörter zusammen mit der globalen Liste basierend auf der laufenden Sicherheitsanalyse und -forschung jederzeit in Azure geändert werden kann und wird. Für den lokalen DC-Agent-Dienst werden aktualisierte Algorithmen erst nach einer Neuinstallation der DC-Agent-Software wirksam.

## <a name="license-requirements"></a>Lizenzanforderungen

|   | Kennwortschutz für Azure AD mit globaler Liste gesperrter Kennwörter | Kennwortschutz für Azure AD mit benutzerdefinierter Liste gesperrter Kennwörter|
| --- | --- | --- |
| Reine Cloudbenutzer | Azure AD Free | Azure AD Premium P1 oder P2 |
| Über die lokale Windows Server Active Directory-Instanz synchronisierte Benutzer | Azure AD Premium P1 oder P2 | Azure AD Premium P1 oder P2 |

> [!NOTE]
> Lokale Windows Server Active Directory-Benutzer, die nicht mit Azure Active Directory synchronisiert werden, können auch die Vorteile des Azure AD-Kennwortschutzes basierend auf vorhandenen Lizenzen für synchronisierte Benutzer nutzen.

Weitere Informationen zur Lizenzierung einschließlich der Kosten finden Sie unter [Azure Active Directory – Preise](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Anzeige für Benutzer

Wenn ein Benutzer versucht, ein Kennwort auf einen Wert zurückzusetzen, der gesperrt würde, wird die folgende Fehlermeldung angezeigt:

Ihr Kennwort ist aufgrund eines enthaltenen Worts, Ausdrucks oder Musters leider leicht zu erraten. Wiederholen Sie den Vorgang mit einem anderen Kennwort.

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren der Liste benutzerdefinierter gesperrter Kennwörter](howto-password-ban-bad.md)
* [Vorschau: Bereitstellen von Azure AD-Kennwortschutz](howto-password-ban-bad-on-premises-deploy.md)
