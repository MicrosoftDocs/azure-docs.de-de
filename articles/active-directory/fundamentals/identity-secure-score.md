---
title: Was ist der Identity Secure Score? – Azure Active Directory
description: Verwenden von Identity Secure Score zum Verbessern des Sicherheitsstatus Ihres Verzeichnisses
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdff2305914ca6e4144f7784d1a60026a1d27c0
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988679"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Was ist der Identity Secure Score in Azure Active Directory?

Wie sicher ist Ihr Azure AD-Mandant? Wenn Sie diese Frage nicht beantworten können, lesen Sie diesen Artikel, um zu erfahren, wie Sie den Status Ihrer Identitätssicherheit mit dem Identity Secure Score überwachen und verbessern können.

## <a name="what-is-an-identity-secure-score"></a>Was ist ein Identity Secure Score?

Der Identity Secure Score ist eine Zahl zwischen 1 und 223, die Aufschluss darüber gibt, in welchem Maße Sie die von Microsoft empfohlenen bewährten Sicherheitsmethoden befolgen. Im Identity Secure Score ist jede Verbesserungsaktion auf Ihre spezifische Konfiguration zugeschnitten.  

![Sicherheitsbewertung](./media/identity-secure-score/identity-secure-score-overview.png)

Anhand der Bewertung können Sie:

- Den Status Ihrer Identitätssicherheit objektiv messen
- Verbesserungen der Identitätssicherheit planen
- Den Erfolg Ihrer Verbesserungen überprüfen

Sie können im Identity Secure Score-Dashboard auf die Bewertung und zugehörige Informationen zugreifen. Im Dashboard finden Sie folgende Informationen:

- Ihren Identity Secure Score
- Ein Vergleichsdiagramm, in dem dargestellt ist, wie Ihr Identity Secure Score im Vergleich zu anderen Mandanten in der gleichen Branche und ähnlicher Größe ausfällt
- Ein Trenddiagramm, in dem dargestellt ist, wie sich Ihr Identity Secure Score im Laufe der Zeit geändert hat
- Eine Liste der möglichen Verbesserungsaktionen

Mithilfe der vorgeschlagenen Verbesserungsaktionen können Sie:

- Ihren Sicherheitsstatus und Ihre Bewertung verbessern
- Die für Ihre Organisation verfügbaren Features im Rahmen Ihrer Identitätsinvestitionen nutzen

## <a name="how-do-i-get-my-secure-score"></a>Wie erhalte ich meinen Secure Score?

Der Identity Secure Score ist in allen Editionen von Azure AD verfügbar. Besuchen Sie das [Azure AD-Übersichtsdashboard](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore), um auf Ihre Bewertung zuzugreifen.

## <a name="how-does-it-work"></a>Wie funktioniert dies?

Azure führt alle 48 Stunden eine Untersuchung Ihrer Sicherheitskonfiguration durch, bei der Ihre Einstellungen mit den empfohlenen bewährten Methoden verglichen werden. Basierend auf dem Ergebnis dieser Untersuchung wird eine neue Bewertung für Ihr Verzeichnis berechnet. Es ist möglich, dass Ihre Sicherheitskonfiguration nicht vollständig auf die empfohlenen bewährten Methoden ausgerichtet ist und die vorgeschlagenen Verbesserungsaktionen nur teilweise erfüllt sind. In diesen Szenarien erhalten Sie nur einen Teil der maximalen Punktzahl/Bewertung, die für die Kontrolle zur Verfügung steht.

Jede Empfehlung wird basierend auf Ihrer Azure AD-Konfiguration bewertet. Wenn Sie Drittanbieterprodukte verwenden, um eine Empfehlung gemäß den bewährten Methoden umzusetzen, können Sie die entsprechende Konfiguration in den Einstellungen einer Verbesserungsaktion angeben. Sie können auch festlegen, dass Empfehlungen ignoriert werden sollen, wenn sie nicht auf Ihre Umgebung anwendbar sind. Eine ignorierte Empfehlung wird bei der Berechnung Ihrer Bewertung nicht berücksichtigt.

![Aktion ignorieren oder als durch Drittanbieter abgedeckt markieren](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>Wie kann mir der Secure Score helfen?

Anhand des Secure Score können Sie:

- Den Status Ihrer Identitätssicherheit objektiv messen
- Verbesserungen der Identitätssicherheit planen
- Den Erfolg Ihrer Verbesserungen überprüfen

## <a name="what-you-should-know"></a>Wichtige Informationen

### <a name="who-can-use-the-identity-secure-score"></a>Wer kann den Identity Secure Score nutzen?

Der Identity Secure Score kann von Benutzern mit den folgenden Rollen verwendet werden:

- Globaler Administrator
- Sicherheitsadministrator
- Sicherheitsleseberechtigte

### <a name="how-are-controls-scored"></a>Wie werden die Kontrollen bewertet?

Die Bewertung der Kontrollen kann auf zwei Arten erfolgen. Einige werden auf binäre Weise bewertet: Sie erhalten eine Bewertung von 100 %, wenn die Funktion oder Einstellung gemäß unserer Empfehlung konfiguriert ist. Andere Bewertungen werden als Prozentsatz der Gesamtkonfiguration berechnet. Wenn beispielsweise die Verbesserungsempfehlung besagt, dass Sie 30 Punkte erhalten, wenn Sie alle Ihre Benutzer mit MFA schützen, aber nur 5 von insgesamt 100 Benutzern geschützt werden, erhalten Sie eine Teilbewertung von ca. 2 Punkten (5 geschützt/100 insgesamt * 30 max. Punkte = 2 Punkte Teilbewertung).

### <a name="what-does-not-scored-mean"></a>Was bedeutet der Hinweis „[Not Scored]“ (Nicht bewertet)?

Aktionen mit der Bezeichnung „[Not Scored]“ (Nicht bewertet) sind Aktionen, die Sie in Ihrer Organisation durchführen können, die aber nicht bewertet werden, weil sie (noch) nicht in das Tool integriert sind. Sie können die Sicherheit mit diesen Aktionen verbessern, sie werden gegenwärtig jedoch nicht in der Bewertung berücksichtigt.

### <a name="how-often-is-my-score-updated"></a>Wie oft wird meine Bewertung aktualisiert?

Die Bewertung wird einmal am Tag (ungefähr um 1:00 Uhr Pacific Normalzeit) berechnet. Wenn Sie eine Änderung an einer bewerteten Aktion vornehmen, wird die Bewertung automatisch am nächsten Tag aktualisiert. Es dauert bis zu 48 Stunden, bis eine Änderung in Ihrer Bewertung berücksichtigt wird.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Meine Bewertung hat sich geändert. Wie ermittle ich den Grund für diese Änderung?

Navigieren Sie zum [Microsoft 365 Security Center](https://security.microsoft.com/). Dort finden Sie Ihre vollständige Microsoft-Sicherheitsbewertung. Sie können alle Änderungen Ihrer Sicherheitsbewertung (Secure Score) leicht erkennen, indem Sie auf der Registerkarte „Verlauf“ die detaillierten Änderungen überprüfen.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Misst die Sicherheitsbewertung mein Risiko einer Sicherheitsverletzung?

Kurz gesagt: Nein. Die Sicherheitsbewertung ist kein absolutes Maß dafür, wie hoch die Wahrscheinlichkeit einer Sicherheitsverletzung für Sie ist. Er gibt wieder, in welchem Umfang Sie die Funktionen nutzen, mit denen das Risiko einer Sicherheitsverletzung beeinflusst werden kann. Kein Dienst kann garantieren, dass es bei Ihnen zu keiner Sicherheitsverletzung kommt, und die Sicherheitsbewertung sollte nicht als eine solche Garantie aufgefasst werden.

### <a name="how-should-i-interpret-my-score"></a>Wie interpretiere ich meine Bewertung?

Sie erhalten Hinweise zur Konfiguration empfohlener Sicherheitsfunktionen oder Durchführung sicherheitsrelevanter Aufgaben (z. B. das Lesen von Berichten). Einige Aktionen werden bewertet, wenn sie teilweise abgeschlossen wurden, beispielsweise das Aktivieren von Multi-Factor Authentication (MFA) für Ihre Benutzer. Ihre Sicherheitsbewertung spiegelt die von Ihnen verwendeten Microsoft-Sicherheitsdienste direkt wider. Denken Sie daran, dass ein Gleichgewicht zwischen Sicherheit und Benutzerfreundlichkeit bestehen muss. Alle Sicherheitsmaßnahmen wirken sich auf Benutzer aus. Kontrollen mit geringen Benutzerauswirkungen sollten wenig oder gar keinen Einfluss auf das Tagesgeschäft Ihrer Benutzer haben.

Wenn Sie Ihren Bewertungsverlauf anzeigen möchten, navigieren Sie zum [Microsoft 365 Security Center](https://security.microsoft.com/), und sehen Sie sich Ihre gesamte Microsoft-Sicherheitsbewertung an. Sie können die Änderungen Ihrer gesamten Sicherheitsbewertung überprüfen, indem Sie auf „Verlauf anzeigen“ klicken. Wählen Sie ein Datum aus, um anzuzeigen, welche Kontrollen für diesen Tag aktiviert waren und welche Bewertung Sie für die einzelnen Kontrollen erhalten haben.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>In welcher Beziehung steht der Identity Secure Score zum Office 365 Secure Score?

Die [Microsoft-Sicherheitsbewertung](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) enthält fünf unterschiedliche Kontroll- und Bewertungskategorien:

- Identity
- Daten
- Geräte
- Infrastruktur
- Apps

Der Identity Secure Score stellt den Teil der Microsoft-Sicherheitsbewertung dar, der die Identitätssicherheit betrifft. Diese Überlappung bedeutet, dass Ihre Empfehlungen für den Identity Secure Score und die Identitätsbewertung in Microsoft identisch sind.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die [Microsoft-Sicherheitsbewertung](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score).
