---
title: Anzeigen und Suchen Ihrer letzten Anmeldeaktivität auf der Seite „Meine Anmeldungen“ – Azure Active Directory | Microsoft-Dokumentation
description: Details zur Anzeige und Suche Ihrer letzten Anmeldeaktivität auf der Seite „Meine Anmeldungen“ im Portal „Mein Konto“.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 08/03/2020
ms.author: curtand
ms.openlocfilehash: a002254995e42fc586b707e3cb373dac66e678ee
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94834148"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>Anzeigen und Suchen Ihrer letzten Anmeldeaktivität auf der Seite „Meine Anmeldungen“

Sie können die letzten Anmeldeaktivitäten für Ihr Geschäfts-, Schul- oder Unikonto auf der Seite **Meine Anmeldungen** im Portal **Mein Konto** einsehen. Durch die Überprüfung Ihres Anmeldeverlaufs können Sie feststellen, ob ungewöhnliche Aktivitäten stattgefunden haben. Dabei wird Ihnen Folgendes angezeigt:

- Ob jemand versucht, Ihr Kennwort zu erraten.
- Ob sich ein Angreifer erfolgreich bei ihrem Konto angemeldet hat und an welchem Standort die Anmeldung erfolgt ist.
- Auf welche Apps der Angreifer versucht hat, zuzugreifen.

## <a name="view-your-recent-sign-in-activity"></a>Anzeigen Ihrer letzten Anmeldeaktivität

1. Melden Sie sich bei Ihrem Geschäfts-, Schul- oder Unikonto an, und rufen Sie die Seite https://myaccount.microsoft.com/ auf.

2. Wählen Sie im linken Navigationsbereich **Meine Anmeldungen** aus, oder wählen Sie im Block **Meine Anmeldungen** den Link **Letzte Aktivität überprüfen** aus.

    ![Seite „Mein Konto“ mit hervorgehobenen Links für die letzte Aktivität](media/my-account-portal/my-account-portal-sign-ins.png)

3. Erweitern Sie alle Anmeldeelemente, und überprüfen Sie diese. Stellen Sie sicher, dass Sie alle erkennen. Wenn Sie ein Anmeldeelement finden, das Ihnen unbekannt ist, ändern Sie Ihr Kennwort, um das Konto vor einer Sicherheitsgefährdung zu schützen.

    ![Seite „Letzte Aktivität“ mit erweiterten Anmeldedetails](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>Wenn eine erfolgreiche Anmeldung angezeigt wird

Manchmal kann bei der Überprüfung der eigenen normalen Anmeldeaktivitäten eine erfolgreiche Anmeldung von einem unbekannten Standort, Browser oder Betriebssystem angezeigt werden. Unbekannte Anmeldungen können bedeuten, dass ein Angreifer Zugriff auf Ihr Konto erlangt hat. Wenn Sie Aktivitäten sehen, die nicht von Ihnen autorisiert wurden, wird empfohlen, Ihr Kennwort sofort zu ändern und dann zu [Sicherheitsinformation](https://mysignins.microsoft.com/security-info) zu wechseln und Ihre Sicherheitseinstellungen zu aktualisieren.

Bevor Sie festlegen, dass etwas nicht richtig ist, stellen Sie sicher, dass Sie kein falsch-positives Ergebnis sehen (wobei das Element fragwürdig aussieht, aber in Ordnung ist). So wird beispielsweise Ihr ungefährer Standort und die Zuordnung basierend auf Ihrer IP-Adresse ermittelt. Mobilfunknetze sind besonders schwer zu lokalisieren, da sie den Datenverkehr gelegentlich über entfernte Standorte leiten. Auch wenn Sie sich mit Ihrem mobilen Gerät im Bundesstaat Washington angemeldet haben, kann als Standort für die Anmeldung Kalifornien angegeben sein. Es wird dringend empfohlen, weitere Details als nur den Standort zu überprüfen. Vergewissern Sie sich, dass auch Betriebssystem, Browser und App für Sie Sinn ergeben.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Wenn eine nicht erfolgreiche Anmeldung angezeigt wird

Wenn eine nicht erfolgreiche Anmeldung angezeigt wird, kann das bedeuten, dass Sie Ihre Anmeldeinformationen falsch eingegeben haben. Es kann auch bedeuten, dass ein Angreifer versucht hat, Ihr Kennwort zu erraten. Als Reaktion auf dieses Risiko müssen Sie Ihr Kennwort nicht ändern, aber es wird empfohlen, sich für Azure AD Multi-Factor Authentication (MFA) zu registrieren. Mit Multi-Factor Authentication ist das Kennwort auch dann nicht ausreichend für den Zugriff auf Ihr Konto, wenn der Hacker es erraten hat.

![Kachel „Nicht erfolgreiche Anmeldung“](media/my-account-portal-sign-ins-page/unsuccessful.png)

Wenn eine nicht erfolgreiche Anmeldung angezeigt wird, bei der unter **Sitzungsaktivität** der Hinweis `Additional verification failed, invalid code` angegeben ist, bedeutet dies, dass Ihre primären Anmeldeinformationen für die Authentifizierung erfolgreich waren, Multi-Factor Authentication aber fehlgeschlagen ist. Das kann bedeuten, dass ein Angreifer Ihr Kennwort richtig erraten hat, aber Multi-Factor Authentication nicht durchlaufen konnte. Es wird empfohlen, Ihr Kennwort trotzdem zu ändern, da der Angreifer bereits darüber verfügen könnte, und zur Seite [Sicherheitsinformation](https://mysignins.microsoft.com/security-info) zu wechseln und Ihre Sicherheitseinstellungen zu aktualisieren.

## <a name="search-for-specific-sign-in-activity"></a>Suchen nach einer spezifischen Anmeldeaktivität

Sie können Ihre letzten Anmeldeaktivitäten anhand der verfügbaren Informationen suchen. Beispielsweise können Sie nach der aktuellen Anmeldeaktivität anhand von Betriebssystem, Standort, App usw. suchen.

1. Geben Sie auf der Seite **Letzte Aktivität überprüfen** die gesuchten Informationen in die Leiste **Suchen** ein. Geben Sie z. B. `Unsuccessful` ein, um nach allen von der App „Mein Konto“ gesammelten erfolglosen Anmeldeaktivitäten zu suchen.

2. Wählen Sie die Schaltfläche **Suche** aus, um die Suche zu starten.

    ![Seite „Letzte Aktivität“ mit hervorgehobener Suchleiste, Schaltfläche „Suche“ und den Ergebnissen](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>Bestätigen ungewöhnlicher Aktivitäten

Anmeldungen, die als ungewöhnliche Aktivitäten gekennzeichnet sind, können in der Kachel für diese Aktivität auf der Seite **Meine Anmeldungen** bestätigt werden.

![Kachel für ungewöhnliche Anmeldung zur Bestätigung, dass der Anmeldeversuch von Ihnen durchgeführt wurde](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="next-steps"></a>Nächste Schritte

Nach dem Anzeigen der letzten Anmeldeaktivität haben Sie folgende Möglichkeiten:

- Anzeigen oder Verwalten der [Sicherheitsinformationen](./security-info-setup-signin.md).

- Anzeigen oder Verwalten Ihrer verbundenen [Geräte](my-account-portal-devices-page.md).

- Anzeigen oder Verwalten Ihrer [Organisationen](my-account-portal-organizations-page.md)

- Anzeigen von Informationen zur [Nutzung der datenschutzrelevanten Informationen](my-account-portal-privacy-page.md) in Ihrer Organisation.

- Ändern der [Portaleinstellungen für „Mein Konto“](my-account-portal-settings.md)