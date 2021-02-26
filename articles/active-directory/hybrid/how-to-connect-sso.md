---
title: 'Azure AD Connect: Nahtloses einmaliges Anmelden | Microsoft-Dokumentation'
description: In diesem Thema wird die nahtlose einmalige Anmeldung von Azure Active Directory (Azure AD) beschrieben und wie Sie damit eine echte einmalige Anmeldung für Desktopbenutzer im Unternehmen innerhalb Ihres Unternehmensnetzwerks bereitstellen können.
services: active-directory
keywords: Was ist Azure AD Connect, Active Directory installieren, erforderliche Komponenten für Azure AD, SSO, Single Sign-On, einmaliges Anmelden
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88eae702782e2f1af9c20797676214db458c2adc
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937617"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Nahtlose einmalige Anmeldung mit Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Was ist die nahtlose einmalige Anmeldung mit Azure Active Directory?

Die nahtlose einmalige Anmeldung mit Azure Active Directory (nahtlose SSO mit Azure AD) meldet Benutzer automatisch auf ihren Unternehmensgeräten an, die mit dem Unternehmensnetzwerk verbunden sind. Wenn diese Funktion aktiviert ist, müssen Benutzer zur Anmeldung bei Azure AD nicht ihr Kennwort und in der Regel nicht einmal ihren Benutzernamen eingeben. Diese Funktion ermöglicht Ihren Benutzern einen einfachen Zugriff auf Ihre cloudbasierten Anwendungen, ohne dass zusätzliche lokale Komponenten erforderlich sind.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Die nahtlose SSO kann mit den Anmeldemethoden [Kennworthashsynchronisierung](how-to-connect-password-hash-synchronization.md) oder [Passthrough-Authentifizierung](how-to-connect-pta.md) kombiniert werden. Die nahtlose einmalige Anmeldung ist _nicht_ auf Active Directory-Verbunddienste (AD FS) anwendbar.

![Nahtloses einmaliges Anmelden](./media/how-to-connect-sso/sso1.png)

## <a name="sso-via-primary-refresh-token-vs-seamless-sso"></a>Einmaliges Anmelden über primäres Aktualisierungstoken und nahtloses einmaliges Anmelden

Unter Windows 10 empfiehlt sich das einmalige Anmelden über das primäre Aktualisierungstoken. Unter Windows 7 und 8.1 ist die Verwendung des nahtlosen einmaligen Anmeldens empfehlenswert.
Das nahtlose einmalige Anmelden setzt voraus, dass das Gerät des Benutzers in die Domäne eingebunden ist, kann aber unter Windows 10 nicht für [in Azure AD eingebundene Geräte](../devices/concept-azure-ad-join.md)oder für [hybrid in Azure AD eingebundene Geräte](../devices/concept-azure-ad-join-hybrid.md) verwendet werden. Das einmalige Anmelden (SSO) für in Azure AD oder hybrid in Azure AD eingebundene Geräte sowie in Azure AD registrierte Geräte erfolgt auf Basis des [primären Aktualisierungstokens](../devices/concept-primary-refresh-token.md).

Das einmalige Anmelden (SSO) über das primäre Aktualisierungstoken funktioniert für hybrid in Azure AD eingebundene Geräte, in Azure AD eingebundene oder persönliche registrierte Geräte, sobald die Geräte durch Hinzufügen eines Geschäfts-, Schul- oder Unikontos bei Azure AD registriert wurden. Weitere Informationen zur Funktionsweise des einmaligen Anmeldens (SSO) über das primäre Aktualisierungstoken unter Windows 10 finden Sie unter: [Primäres Aktualisierungstoken und Azure AD](../devices/concept-primary-refresh-token.md)


## <a name="key-benefits"></a>Hauptvorteile

- *Große Benutzerfreundlichkeit*
  - Benutzer werden automatisch sowohl bei lokalen als auch bei cloudbasierten Anwendungen angemeldet.
  - Benutzer müssen nicht ständig ihr Kennwort eingeben.
- *Einfache Bereitstellung und Verwaltung*
  - Keine zusätzlichen lokalen Komponenten erforderlich.
  - Funktioniert mit jedem Verfahren für die Cloudauthentifizierung: [Kennworthashsynchronisierung](how-to-connect-password-hash-synchronization.md) oder [Passthrough-Authentifizierung](how-to-connect-pta.md).
  - Kann mithilfe einer Gruppenrichtlinie für einige oder alle Benutzer eingeführt werden.
  - Registrieren Sie Geräte ohne Windows 10 bei Azure AD, ohne dass eine AD FS-Infrastruktur erforderlich ist. Für diese Funktion benötigen Sie Version 2.1 oder höher des [Clients für die Arbeitsplatzeinbindung](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Wichtige Features

- Beim Anmeldebenutzernamen kann es sich entweder um den lokalen Standard-Benutzernamen (`userPrincipalName`) handeln oder um ein anderes Attribut (`Alternate ID`), das in Azure AD Connect konfiguriert ist. Beide Anwendungsfälle funktionieren, weil die nahtloses SSO den `securityIdentifier`-Anspruch im Kerberos-Ticket verwendet, um die entsprechenden Benutzerobjekte in Azure AD zu suchen.
- Die nahtlose SSO ist eine opportunistische Funktion. Tritt aus irgendeinem Grund ein Fehler auf, wird die reguläre Benutzeranmeldung durchgeführt, d.h. Benutzer müssen ihr Kennwort auf der Anmeldeseite eingeben.
- Leitet eine Anwendung (z.B. `https://myapps.microsoft.com/contoso.com`) die Parameter `domain_hint` (OpenID Connect) oder `whr` (SAML) – die Ihren Mandanten identifizieren – oder den Parameter `login_hint` – der den Benutzer identifiziert – in der Azure AD-Anmeldeanforderung weiter, werden Benutzer automatisch ohne Eingabe von Benutzername oder Kennwort angemeldet.
- Benutzer profitieren auch dann von einer Anmeldung ohne Benutzereingaben, wenn eine Anwendung (z.B. `https://contoso.sharepoint.com`) Anmeldeanforderungen an Azure AD-Endpunkte sendet, die als Mandanten eingerichtet sind – also `https://login.microsoftonline.com/contoso.com/<..>` oder `https://login.microsoftonline.com/<tenant_ID>/<..>` –, anstatt die Anforderungen an den allgemeinem Azure AD-Endpunkt – also `https://login.microsoftonline.com/common/<...>` – zu senden.
- Die Abmeldung wird unterstützt. Dadurch können Benutzer ein anderes Azure AD-Konto für die Anmeldung auswählen, anstatt mit der nahtlosen einmaligen Anmeldung automatisch angemeldet zu werden.
- Win32-Clients für Microsoft 365 (Outlook, Word, Excel usw.) ab Version 16.0.8730.xxxx werden mit einem nicht interaktiven Flow unterstützt. Bei OneDrive müssen Sie das [OneDrive-Feature zur automatischen Konfiguration](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) aktivieren, um von einer automatischen Anmeldung profitieren zu können.
- Kann über Azure AD Connect aktiviert werden
- Es handelt sich um ein kostenloses Feature, sodass Sie für dessen Verwendung keine kostenpflichtigen Editionen von Azure AD benötigen.
- Ist auf webbrowserbasierten Clients und Office-Clients möglich, die eine [moderne Authentifizierung](/office365/enterprise/modern-auth-for-office-2013-and-2016) auf Plattformen und Browsern unterstützen, die eine Kerberos-Authentifizierung ausführen können:

| Betriebssystem/Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Ja\*|Ja|Ja|Ja\*\*\*|–
|Windows 8.1|Ja\*|Ja*\*\*\*|Ja|Ja\*\*\*|–
|Windows 8|Ja\*|–|Ja|Ja\*\*\*|–
|Windows 7|Ja\*|–|Ja|Ja\*\*\*|–
|Windows Server 2012 R2 oder höher|Ja\*\*|–|Ja|Ja\*\*\*|–
|Mac OS X|–|–|Ja\*\*\*|Ja\*\*\*|Ja\*\*\*


\*Erfordert Internet Explorer 10 oder höher.

\*\*Erfordert Internet Explorer 10 oder höher. Deaktivieren Sie den erweiterten geschützten Modus.

\*\*\*Erfordert [zusätzliche Konfigurationsschritte](how-to-connect-sso-quick-start.md#browser-considerations).

\*\*\*\*Erfordert Microsoft Edge, Version 77 oder höher.

## <a name="next-steps"></a>Nächste Schritte

- [**Schnellstart**](how-to-connect-sso-quick-start.md): Einrichten und Ausführen der nahtlosen SSO mit Azure AD
- [**Bereitstellungsplan**](../manage-apps/plan-sso-deployment.md) – Plan für die Bereitstellung in einzelnen Schritten
- [**Technische Einzelheiten**](how-to-connect-sso-how-it-works.md) – Funktionsweise dieses Features verstehen
- [**Häufig gestellte Fragen:**](how-to-connect-sso-faq.md) Antworten auf häufig gestellte Fragen
- [**Problembehandlung**](tshoot-connect-sso.md) – Beheben von häufig auftretenden Problemen mit diesem Feature
- [**UserVoice:**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) Verfassen neuer Feature-Anforderungen
