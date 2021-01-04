---
title: Bedingter Zugriff für Benutzer von B2B Collaboration – Azure AD
description: Hier erfahren Sie, wie Sie Richtlinien der mehrstufigen Authentifizierung für Azure Active Directory B2B-Benutzer erzwingen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: eccbbb22814788aaf06fa6fd10d8c376203c1d49
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892450"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Bedingter Zugriff für Benutzer von B2B-Zusammenarbeit

## <a name="multi-factor-authentication-for-b2b-users"></a>Multi-Factor Authentication für B2B-Benutzer
Azure AD B2B-Zusammenarbeit bietet Organisationen die Möglichkeit, Multi-Factor Authentication-Richtlinien (MFA) für B2B-Benutzer zu erzwingen. Diese Richtlinien können auf Mandantenebene, App-Ebene oder der Ebene einzelner Benutzer auf die gleiche Weise erzwungen werden, wie sie für Vollzeitmitarbeiter und Mitglieder der Organisation aktiviert werden können. MFA-Richtlinien werden in der Ressourcenorganisation erzwungen.

Beispiel:
1. Ein Administrator oder Information-Worker in Unternehmen A lädt Benutzer aus Unternehmen B zu der Anwendung *Foo* in Unternehmen A ein.
2. Anwendung *Foo* in Unternehmen A ist so konfiguriert, dass für den Zugriff eine MFA erforderlich ist.
3. Wenn ein Benutzer aus Unternehmen B versucht, auf die Anwendung *Foo* im Mandanten von Unternehmen A zuzugreifen, wird er aufgefordert, eine MFA-Überprüfung durchzuführen.
4. Der Benutzer kann die MFA für Unternehmen A einrichten und dessen MFA-Option auswählen.
5. Dies funktioniert für alle Identitäten (z.B. Azure AD oder MSA, wenn sich Benutzer in Unternehmen B mithilfe der ID eines sozialen Netzwerks authentifizieren).
6. Unternehmen A muss über ausreichend viele Azure AD-Premium-Lizenzen verfügen, die MFA unterstützen. Der Benutzer aus Unternehmen B nutzt eine dieser Lizenzen von Unternehmen A.

Der einladende Mandant ist immer für die MFA der Benutzer aus der Partnerorganisation zuständig, selbst wenn diese über MFA-Funktionen verfügt.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Einrichten der MFA für Benutzer der B2B-Zusammenarbeit
Um herauszufinden, wie einfach sich die MFA für B2B-Zusammenarbeitsbenutzer einrichten lässt, sehen Sie sich das folgende Video an:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>MFA-Mechanismen für B2B-Benutzer beim Einlösen von Einladungen
Sehen Sie sich die folgende Animation an, die das Einlösen von Einladungen veranschaulicht:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Zurücksetzen der MFA für B2B-Zusammenarbeitsbenutzer
Zurzeit kann ein Administrator eine erneute Authentifizierung von B2B-Zusammenarbeitsbenutzern nur mithilfe von PowerShell-Cmdlets anfordern.

1. Stellen Sie eine Verbindung mit Azure AD her.

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Rufen Sie alle Benutzer mit Authentifizierungsmethoden ab.

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Beispiel:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Setzen Sie die MFA-Methode für einen bestimmten Benutzer zurück, damit der B2B-Zusammenarbeitsbenutzer die Nachweismethoden erneut festlegen muss. Beispiel:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Warum wird MFA im Ressourcenmandanten ausgeführt?

Im aktuellen Release wird MFA aus Gründen der Vorhersagbarkeit immer im Ressourcenmandanten ausgeführt. Nehmen wir beispielsweise an, dass ein Contoso-Benutzer (Sally) zu Fabrikam eingeladen wird und Fabrikam MFA für B2B-Benutzer aktiviert hat.

Wenn Contoso eine MFA-Richtlinie für App1, aber nicht für App2 aktiviert hat, könnte im Contoso-MFA-Anspruch im Token folgendes Problem auftreten:

* Tag 1: Ein Benutzer verfügt über MFA in Contoso und greift auf App1 zu, und es wird keine zusätzliche MFA-Aufforderung in Fabrikam angezeigt.

* Tag 2: Der Benutzer hat auf App 2 in Contoso zugegriffen, sodass er sich bei einem Zugriff auf Fabrikam dort für MFA registrieren muss.

Dieser Prozess kann verwirrend sein und zu einem Rückgang der abgeschlossenen Anmeldungen führen.

Außerdem vertraut Fabrikam nicht immer der MFA-Richtlinie von Contoso, selbst wenn Contoso die MFA-Funktionen nutzen kann.

Zudem kann die Ressourcenmandanten-MFA auch für MSAs und IDs für soziale Netzwerke als Identitätsanbieter sowie für Partnerorganisationen, die keine MFA eingerichtet haben, verwendet werden.

Deshalb wird im Hinblick auf MFA für B2B-Benutzer empfohlen, MFA immer im einladenden Mandanten zu erzwingen. Diese Anforderung kann in manchen Fällen zu doppelter MFA führen, jedoch ist bei jedem Zugriff auf einen einladenden Mandanten die Endbenutzererfahrung vorhersagbar: Sally muss sich beim einladenden Mandanten für MFA registrieren.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Bedingter Zugriff für B2B-Benutzer anhand von Gerät, Standort und Risiko

Wenn Contoso gerätebasierte Richtlinien für den bedingten Zugriff auf seine Unternehmensdaten aktiviert, wird der Zugriff von nicht verwalteten Geräten (d.h. von Geräten, die nicht von Contoso verwaltet werden und nicht die Contoso-Geräterichtlinien erfüllen) verhindert.

Wenn das Gerät des B2B-Benutzers nicht von Contoso verwaltet wird, wird der Zugriff von B2B-Benutzern aus den Partnerorganisationen in jedem Kontext, in dem diese Richtlinien erzwungen werden, blockiert. Contoso kann jedoch Ausschlusslisten erstellen, die bestimmte Partnerbenutzer enthalten, die gemäß der gerätebasierten Richtlinie für bedingten Zugriff ausgeschlossen werden sollen.

#### <a name="mobile-application-management-policies-for-b2b"></a>B2B-Richtlinien für die Verwaltung mobiler Anwendungen

App-Schutzrichtlinien für bedingten Zugriff können nicht auf B2B-Benutzer angewendet werden, da die einladende Organisation keinen Einblick in die Organisation des B2B-Benutzers hat.

#### <a name="location-based-conditional-access-for-b2b"></a>Standortbasierter bedingter Zugriff für B2B

Für B2B-Benutzer können standortbasierte Richtlinien für bedingten Zugriff erzwungen werden, wenn die einladende Organisation einen vertrauenswürdigen IP-Adressbereich erstellen kann, der ihre Partnerorganisationen definiert.

#### <a name="risk-based-conditional-access-for-b2b"></a>Risikobasierter bedingter Zugriff für B2B

Risikobasierte Anmelderichtlinien können derzeit nicht auf B2B-Benutzer angewendet werden, da die Risikobewertung in der eigenen Organisation des B2B-Benutzers erfolgt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zur Azure AD B2B-Zusammenarbeit:

* [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
* [Preise für externe Identitäten](external-identities-pricing.md)
* [Häufig gestellte Fragen zur Azure Active Directory B2B-Zusammenarbeit](faq.md)
