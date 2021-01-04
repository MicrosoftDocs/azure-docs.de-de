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
ms.openlocfilehash: 5e50b9e5dc683eb30452dbb96d82c9f66de93763
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408004"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Nahtlose einmalige Anmeldung mit Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Was ist die nahtlose einmalige Anmeldung mit Azure Active Directory?

Die nahtlose einmalige Anmeldung mit Azure Active Directory (nahtlose SSO mit Azure AD) meldet Benutzer automatisch auf ihren Unternehmensgeräten an, die mit dem Unternehmensnetzwerk verbunden sind. Wenn diese Funktion aktiviert ist, müssen Benutzer zur Anmeldung bei Azure AD nicht ihr Kennwort und in der Regel nicht einmal ihren Benutzernamen eingeben. Diese Funktion ermöglicht Ihren Benutzern einen einfachen Zugriff auf Ihre cloudbasierten Anwendungen, ohne dass zusätzliche lokale Komponenten erforderlich sind.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Die nahtlose SSO kann mit den Anmeldemethoden [Kennworthashsynchronisierung](how-to-connect-password-hash-synchronization.md) oder [Passthrough-Authentifizierung](how-to-connect-pta.md) kombiniert werden. Die nahtlose einmalige Anmeldung ist _nicht_ auf Active Directory-Verbunddienste (AD FS) anwendbar.

![Nahtloses einmaliges Anmelden](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>Für das nahtlose einmalige Anmelden darf das Gerät des Benutzers ausschließlich **in die Domäne eingebunden** sein, für in [Azure AD eingebundene](../devices/concept-azure-ad-join.md) oder [In Hybrid-Azure AD eingebundene](../devices/concept-azure-ad-join-hybrid.md) Geräte kann diese Funktion nicht verwendet werden. Das einmalige Anmelden (SSO) für in Azure AD oder in Hybrid-Azure AD eingebundene Geräte sowie in Azure AD registrierte Geräte funktioniert basierend auf dem [primären Aktualisierungstoken](../devices/concept-primary-refresh-token.md).

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

>[!NOTE]
>Bei Windows 10 wird empfohlen, [Azure AD Join](../devices/concept-azure-ad-join.md) zu verwenden, um eine optimale Funktionsweise der nahtlosen SSO mit Azure AD sicherzustellen.

## <a name="next-steps"></a>Nächste Schritte

- [**Schnellstart**](how-to-connect-sso-quick-start.md): Einrichten und Ausführen der nahtlosen SSO mit Azure AD
- [**Bereitstellungsplan**](../manage-apps/plan-sso-deployment.md) – Plan für die Bereitstellung in einzelnen Schritten
- [**Technische Einzelheiten**](how-to-connect-sso-how-it-works.md) – Funktionsweise dieses Features verstehen
- [**Häufig gestellte Fragen:**](how-to-connect-sso-faq.md) Antworten auf häufig gestellte Fragen
- [**Problembehandlung**](tshoot-connect-sso.md) – Beheben von häufig auftretenden Problemen mit diesem Feature
- [**UserVoice:**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) Verfassen neuer Feature-Anforderungen