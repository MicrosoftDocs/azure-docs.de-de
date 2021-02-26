---
title: Häufig gestellte Fragen (FAQs) zu Identity Protection in Azure Active Directory
description: Häufig gestellte Fragen zu Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6117b1ac78faf84d73f5a78202709aec7a1f84d9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492585"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Häufig gestellte Fragen zu Identity Protection in Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Ignorieren eines Benutzerrisikos – Bekannte Probleme

Über **Benutzerrisiko ignorieren** in Identity Protection (klassisch) wird der Akteur im Risikoverlauf des Benutzers in Identity Protection auf **Azure AD** festgelegt.

Über **Benutzerrisiko ignorieren** in Identity Protection wird der Akteur im Risikoverlauf des Benutzers in Identity Protection auf **\<Admin’s name with a hyperlink pointing to user’s blade\>** festgelegt.

Derzeit verursacht ein bekanntes Problem Wartezeit beim Ablauf des Schließvorgangs für Benutzerrisiken. Wenn Sie eine „Benutzerrisiko-Richtlinie“ haben, wird diese Richtlinie innerhalb von Minuten, nachdem auf „Benutzerrisiko ignorieren“ geklickt wurde, nicht mehr auf verworfene Benutzer angewendet. Allerdings gibt es bekannte Verzögerungen beim Aktualisieren des „Risikozustands“ von verworfenen Benutzern in der Benutzerumgebung. Aktualisieren Sie als Problemumgehung die Seite im Browser, um den aktuellen „Risikozustand“ eines Benutzers anzuzeigen.


## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="why-is-a-user-at-risk"></a>Warum ist ein Benutzer risikobehaftet?

Wenn Sie ein Azure AD Identity Protection-Kunde sind, wechseln Sie zur Ansicht [Riskante Benutzer](howto-identity-protection-investigate-risk.md#risky-users), und klicken Sie auf einen risikobehafteten Benutzer. In der Taskleiste am unteren Rand wird die Registerkarte „Risikoverlauf“ angezeigt. Sie enthält alle Ereignisse, die zu einer Änderung des Benutzerrisikos geführt haben. Um alle risikobehafteten Anmeldungen für den Benutzer anzuzeigen, klicken Sie auf „Riskante Anmeldungen“ des Benutzers. Klicken Sie auf „Risikoerkennungen“, um alle Risikoerkennungen für den Benutzer anzuzeigen.

### <a name="why-was-my-sign-in-blocked-but-identity-protection-didnt-generate-a-risk-detection"></a>Warum wurde meine Anmeldung blockiert, ohne dass Identity Protection eine Risikoerkennung generiert?
Anmeldungen können aus verschiedenen Gründen blockiert werden. Hierbei ist Folgendes wichtig: Identity Protection generiert nur dann eine Risikoerkennung, wenn bei der Authentifizierungsanforderung korrekte Anmeldeinformationen verwendet werden. Wenn ein Benutzer falsche Anmeldeinformationen verwendet, wird dies von Identity Protection nicht gekennzeichnet, da kein Risiko einer Kompromittierung von Anmeldeinformationen besteht. Ein solches entsteht nur, wenn ein böswilliger Akteur die richtigen Informationen verwendet. Im Folgenden finden Sie einige Gründe, aus denen die Anmeldung eines Benutzers blockiert werden kann, ohne dass Identity Protection eine Erkennung generiert:
* Eine **IP-Adresse kann blockiert werden**, wenn schädliche Aktivitäten von dieser Adresse erkannt werden. In der Meldung zur Blockierung der IP-Adresse wird nicht unterschieden, ob die Anmeldeinformationen richtig waren oder nicht. Wenn eine IP-Adresse blockiert ist und keine richtigen Anmeldeinformationen verwendet werden, wird keine Identity Protection-Erkennung generiert.
* **[Smart Lockout](../authentication/howto-password-smart-lockout.md)** kann nach mehreren Fehlversuchen die Anmeldung eines Kontos blockieren.
* Es kann eine **Richtlinie für bedingten Zugriff** in Kraft sein, die andere Bedingungen als Risikostufen verwendet, um eine Authentifizierungsanforderung zu blockieren.

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Wie kann ich einen Bericht mit Erkennungen eines bestimmten Typs abrufen?

Wechseln Sie zur Ansicht „Risikoerkennungen“, und filtern Sie nach „Erkennungstyp“. Anschließend können Sie mithilfe der Schaltfläche **Download** im oberen Bereich diesen Bericht im CSV- oder JSON-Format herunterladen. Weitere Informationen finden Sie im Artikel [ Risiken](howto-identity-protection-investigate-risk.md#risk-detections).

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Warum kann ich nicht meine eigenen Risikostufen für die einzelnen Risikoerkennungen festlegen?

Die Risikostufen in Identity Protection basieren auf der Genauigkeit der Erkennung und werden durch unser überwachtes Machine Learning-System unterstützt. Um festzulegen, welche Werte den Benutzern angezeigt werden, kann der Administrator bestimmte Benutzer/Gruppen in die Richtlinien für Benutzer- und Anmelderisiken einschließen oder sie davon ausschließen.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Warum stimmt der Standort für eine Anmeldung nicht mit dem tatsächlichen Standort überein, von dem aus sich der Benutzer angemeldet hat?

Die IP-Zuordnung bei der Geolocation stellt branchenweit eine Herausforderung dar. Wenn der im Anmeldebericht aufgeführte Standort nicht mit dem tatsächlichen Standort übereinstimmt, wenden Sie sich an den Microsoft-Support. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>Wie kann ich bestimmte Risikoerkennungen schließen, wie das auf der alten Benutzeroberfläche möglich war?

Sie können Feedback zu Risikoerkennungen geben, indem Sie die verknüpfte Anmeldung als kompromittiert oder sicher bestätigen. Das Feedback, das Sie für die Anmeldung geben, wird auf alle Erkennungen für diese Anmeldung übertragen. Wenn Sie Erkennungen schließen möchten, die nicht mit einer Anmeldung verknüpft sind, können Sie dieses Feedback auf Benutzerebene bereitstellen. Weitere Informationen finden Sie in diesem Artikel: [ Senden von Feedback zu Risikoereignissen in Azure AD Identity Protection](howto-identity-protection-risk-feedback.md).

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>Wie weit kann ich zeitlich zurückgehen, um zu verstehen, was mit meinem Benutzer passiert?

- Die Ansicht [Riskante Benutzer](howto-identity-protection-investigate-risk.md#risky-users) zeigt den Risikostatus eines Benutzers auf Grundlage aller bisherigen Anmeldungen an. 
- In der Ansicht [Riskante Anmeldungen](howto-identity-protection-investigate-risk.md#risky-sign-ins) werden die risikobehafteten Anmeldungen in den letzten 30 Tagen angezeigt. 
- Die Ansicht [Risikoerkennungen](howto-identity-protection-investigate-risk.md#risk-detections) enthält die Risikoerkennungen in den letzten 90 Tagen.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>Wie kann ich mehr über eine bestimmte Erkennung erfahren?

Alle Risikoerkennungen sind im Artikel [Was bedeutet Risiko?](concept-identity-protection-risks.md#risk-types-and-detection) dokumentiert. Sie können mit dem Mauszeiger auf das Symbol (i) neben der Erkennung im Azure-Portal zeigen, um mehr über eine Erkennung zu erfahren.

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Wie funktionieren die Feedbackmechanismen in Identity Protection?

**Gefährdung bestätigen** (bei Anmeldung): Informiert Azure AD Identity Protection darüber, dass die Anmeldung nicht vom Identitätsinhaber durchgeführt wurde und weist auf eine Gefährdung hin.

- Nach Erhalt dieses Feedbacks wird der Anmelde- und Benutzerrisikostatus auf **Als gefährdet bestätigt** und die Risikostufe auf **Hoch** festgelegt.

- Darüber hinaus geben wir die Informationen an unsere Machine Learning-Systeme weiter, um die künftige Risikobewertung zu verbessern.

    > [!NOTE]
    > Wenn für den Benutzer bereits eine Bereinigung durchgeführt wurde, klicken Sie nicht auf **Gefährdung bestätigen**, weil dadurch der Anmelde- und Benutzerrisikostatus auf **Als gefährdet bestätigt** und die Risikostufe auf **Hoch** festgelegt wird.

**Sicherheit bestätigen** (bei Anmeldung): Informiert Azure AD Identity Protection darüber, dass die Anmeldung vom Identitätsinhaber durchgeführt wurde und keine Gefährdung vorliegt.

- Nach Erhalt dieses Feedbacks wird der Anmelde- und Benutzerrisikostatus auf **Als sicher bestätigt** und die Risikostufe auf **-** festgelegt.

- Darüber hinaus geben wir die Informationen an unsere Machine Learning-Systeme weiter, um die künftige Risikobewertung zu verbessern. 

    > [!NOTE]
    >Derzeit reicht die Auswahl von „Sicherheit bestätigen“ bei einer Anmeldung nicht aus, zu verhindern, dass zukünftige Anmeldungen mit denselben Eigenschaften als riskant gekennzeichnet werden. Die beste Möglichkeit, das System zu trainieren, die Eigenschaften eines Benutzers zu erlernen, ist die Verwendung der Richtlinie für riskante Anmeldungen mit MFA. Wenn eine riskante Anmeldung für MFA angefordert wird und der Benutzer erfolgreich auf die Anforderung antwortet, kann die Anmeldung erfolgreich durchgeführt werden und dazu beitragen, das System mit dem Verhalten des legitimen Benutzers zu trainieren.
    >
    > Wenn Sie davon ausgehen, dass der Benutzer nicht gefährdet ist, verwenden Sie anstelle von **Als sicher bestätigt** auf Anmeldeebene die Option **Benutzerrisiko ignorieren** auf Benutzerebene. Durch **Benutzerrisiko verwerfen** auf Benutzerebene werden das Benutzerrisiko und alle vergangenen riskanten Anmeldungen und Risikoerkennungen geschlossen.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Warum sehe ich einen Benutzer mit einer niedrigen (oder höheren) Risikobewertung, auch wenn in Identity Protection keine riskanten Anmeldungen oder Risikoerkennungen angezeigt werden?

Da das Benutzerrisiko naturgemäß kumulativ zunimmt und nicht abläuft, kann für einen Benutzer selbst dann ein niedriges oder höheres Benutzerrisiko vorliegen, wenn keine aktuellen riskanten Anmeldungen oder Risikoerkennungen in Identity Protection angezeigt werden. Diese Situation kann eintreten, wenn die einzige schädliche Aktivität für einen Benutzer außerhalb des Zeitraums stattgefunden hat, für den Details zu riskanten Anmeldungen und Risikoerkennungen gespeichert werden. Es gibt keine Ablaufzeiträume für das Benutzerrisiko, weil böswillige Akteure nach der Kompromittierung einer Identität mitunter mehr als 140 Tage in der Kundenumgebung verbleiben, bevor sie ihren Angriff starten. Hier können Kunden die Risikozeitachse für einen Benutzer überprüfen, um die Gründe für ein Benutzerrisiko zu verstehen: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Warum liegt die Bewertung „Hoch“ für „Anmelderisiko (aggregiert)“ vor, wenn die zugehörigen Erkennungen ein niedriges oder mittleres Risiko aufweisen?

Die hohe aggregierte Risikobewertung könnte auf anderen Faktoren der Anmeldung oder darauf basieren, dass für diese Anmeldung mehrere Erkennungen ausgelöst wurden. Umgekehrt kann die aggregierte Risikobewertung „Mittel“ vorliegen, obwohl die mit der Anmeldung verknüpften Erkennungen ein hohes Risiko aufweisen.

### <a name="what-is-the-difference-between-the-activity-from-anonymous-ip-address-and-anonymous-ip-address-detections"></a>Worin besteht der Unterschied zwischen den Erkennungen „Aktivität über anonyme IP-Adresse“ und „Anonyme IP-Adresse“?

Die Quelle der Erkennung „Anonyme IP-Adresse“ ist Azure AD Identity Protection, während die Erkennung „Aktivität über anonyme IP-Adresse“ aus MCAS (Microsoft Cloud App Security) integriert wird. Auch wenn sich die Namen sehr ähneln und Sie möglicherweise Überschneidungen in deren Signalen feststellen, beruhen sie auf unterschiedlichen Back-End-Erkennungen.
