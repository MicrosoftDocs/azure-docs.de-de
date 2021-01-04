---
title: Übersicht über die Herausgeberüberprüfung – Microsoft Identity Platform | Azure
description: Hier erhalten Sie eine Übersicht über das Programm zur Herausgeberüberprüfung für die Microsoft Identity Platform. Darin werden die Vorteile, die Programmanforderungen und häufig gestellte Fragen aufgeführt. Wenn eine Anwendung als durch den Herausgeber verifiziert markiert ist, bedeutet dies, dass der Herausgeber seine Identität mithilfe eines Microsoft Partner Network-Kontos überprüft hat, für das der Überprüfungsvorgang abgeschlossen wurde, und dieses MPN-Konto seiner Anwendungsregistrierung zugeordnet hat.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 1e913e3a5356ad7f49d8b3066f5bd3da7eddd2c2
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308778"
---
# <a name="publisher-verification"></a>Herausgeberüberprüfung

Die Herausgeberüberprüfung unterstützt Administratoren und Endbenutzer dabei, die Authentizität von Anwendungsentwicklern zu verstehen, die in die Microsoft Identity Platform integriert sind. 

> [!VIDEO https://www.youtube.com/embed/IYRN2jDl5dc]

Wenn eine Anwendung als durch den Herausgeber verifiziert markiert ist, bedeutet dies, dass der Herausgeber seine Identität mithilfe eines [Microsoft Partner Network](https://partner.microsoft.com/membership)-Kontos überprüft hat, für das der [Überprüfungsvorgang](/partner-center/verification-responses) abgeschlossen wurde, und dieses MPN-Konto seiner Anwendungsregistrierung zugeordnet hat. 

In der Azure AD-Zustimmungsaufforderung und auf anderen Bildschirmen wird ein blauer Überprüfungsbadge angezeigt: ![Zustimmungsaufforderung](./media/publisher-verification-overview/consent-prompt.png)

Diese Funktion ist hauptsächlich für Entwickler vorgesehen, die mehrinstanzenfähige Apps entwickeln, die [OAuth 2.0 und OpenID Connect](active-directory-v2-protocols.md) auf der [Microsoft Identity Platform](v2-overview.md) nutzen. Diese Apps können Benutzer mithilfe von OpenID Connect anmelden, oder sie können OAuth 2.0 verwenden, um den Zugriff auf Daten mithilfe von APIs wie [Microsoft Graph](https://developer.microsoft.com/graph/) anzufordern.

## <a name="benefits"></a>Vorteile
Die Herausgeberüberprüfung bietet die folgenden Vorteile:
- **Erhöhte Transparenz und Risikominderung für Kunden** : Diese Funktion unterstützt Kunden dabei, zu verstehen, welche Apps in ihren Organisationen von Entwicklern veröffentlicht werden, denen sie vertrauen. 

- **Verbessertes Branding** : In der Azure AD- [Zustimmungsaufforderung](application-consent-experience.md), auf der Seite „Unternehmens-Apps“ und auf weiteren von Endbenutzern und Administratoren verwendeten Benutzeroberflächen wird ein Überprüfungsbadge angezeigt. 

- **Reibungslosere Einführung in Unternehmen** : Administratoren können [Benutzerzustimmungsrichtlinien](../manage-apps/configure-user-consent.md) konfigurieren, wobei der Status der Herausgeberüberprüfung eines der wichtigsten Richtlinienkriterien darstellt.

> [!NOTE]
> Ab November 2020 können Endbenutzer den meisten neu registrierten mehrinstanzenfähigen Apps ohne verifizierten Herausgeber keine Benutzerzustimmung mehr erteilen. Dies gilt für Apps, die nach dem 8. November 2020 registriert werden. Verwenden Sie OAuth 2.0, um Berechtigungen anzufordern, die über das grundlegende Benutzerprofil mit Anmelden und Lesen hinausgehen, und fordern Sie in anderen Mandanten als dem, für den die App registriert ist, die Zustimmung von den Benutzern an. Auf dem Zustimmungsbildschirm wird eine Warnung angezeigt, welche die Benutzer informiert, dass diese Apps riskant sind und von nicht verifizierten Herausgebern stammen.    

## <a name="requirements"></a>Anforderungen
Für die Herausgeberüberprüfung bestehen bestimmte Voraussetzungen, von denen einige bereits von vielen Microsoft-Partnern erfüllt wurden. Sie lauten wie folgt: 

-  Eine MPN-ID für ein gültiges [Microsoft Partner Network](https://partner.microsoft.com/membership)-Konto, für das der [Überprüfungsvorgang](/partner-center/verification-responses) abgeschlossen wurde. Bei diesem MPN-Konto muss es sich um das [globale Partnerkonto (Partner Global Account, PGA)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga) für Ihre Organisation handeln. 

-  Eine in einem Azure AD-Mandanten registrierte App, wobei eine [Herausgeberdomäne](howto-configure-publisher-domain.md) konfiguriert ist.

-  Die Domäne der E-Mail-Adresse, die bei der MPN-Kontoüberprüfung verwendet wird, muss entweder mit der für die App konfigurierten Herausgeberdomäne oder einer DNS-verifizierten, dem Azure AD-Mandanten hinzugefügten [benutzerdefinierten Domäne](../fundamentals/add-custom-domain.md) übereinstimmen. 

-  Der die Überprüfung durchführende Benutzer muss autorisiert sein, Änderungen an der App-Registrierung in Azure AD und am MPN-Konto in Partner Center vorzunehmen. 

    -  In Azure AD muss dieser Benutzer ein Mitglied einer der folgenden [Rollen](../roles/permissions-reference.md) sein: „Anwendungsadministrator“, „Cloudanwendungsadministrator“ oder „Globaler Administrator“. 

    -  In Partner Center muss dieser Benutzer über die folgenden [Rollen](/partner-center/permissions-overview) verfügen: „MPN-Administrator“, „Kontoadministrator“ oder „Globaler Administrator“ (dies ist eine freigegebene Rolle, die in Azure AD verwaltet wird).
    
-  Der Benutzer, der die Überprüfung durchführt, muss sich mit [mehrstufiger Authentifizierung](../authentication/howto-mfa-getstarted.md) anmelden.

-  Der Herausgeber stimmt den [Microsoft Identity Platform-Nutzungsbedingungen für Entwickler](/legal/microsoft-identity-platform/terms-of-use) zu.

Entwickler, die diese Voraussetzungen bereits erfüllt haben, können innerhalb weniger Minuten überprüft werden. Wenn die Anforderungen nicht erfüllt sind, ist die Einrichtung kostenlos. 

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen 
Nachstehend finden Sie einige häufig gestellte Fragen zum Herausgeberüberprüfungsprogramm. Häufig gestellte Fragen zu den Anforderungen und zum eigentlichen Vorgang finden Sie unter [Markieren einer App als durch den Herausgeber verifiziert](mark-app-as-publisher-verified.md).

- **Welche Informationen werden von der Herausgeberüberprüfung __nicht__ bereitgestellt?**  Wenn eine Anwendung als durch den Herausgeber verifiziert markiert ist, gibt dies nicht an, ob die Anwendung oder der Herausgeber bestimmte Zertifizierungen erlangt hat, den Branchenstandards entspricht, bewährte Methoden befolgt usw. Andere Microsoft-Programme einschließlich des [Microsoft 365-App-Zertifizierungsprogramms](/microsoft-365-app-certification/overview) stellen diese Informationen bereit.

- **Wie viel kostet das Programm? Ist eine Lizenz dafür erforderlich?** Microsoft stellt Entwicklern keine Gebühren für die Nutzung der Herausgeberüberprüfung in Rechnung, und es ist keine spezielle Lizenz dafür erforderlich. 

- **In welchem Zusammenhang steht dies mit dem Microsoft 365-Herausgebernachweis? Wie sieht es mit dem Microsoft 365-App-Zertifizierungsprogramm aus?** Dabei handelt es sich um ergänzende Programme, mit denen Entwickler vertrauenswürdige Apps erstellen können, die von Kunden bedenkenlos übernommen werden können. Die Herausgeberüberprüfung ist der erste Schritt in diesem Prozess und sollte von allen Entwicklern ausgeführt werden, die Apps erstellen, die die oben genannten Kriterien erfüllen. 

  Entwickler, die auch Microsoft 365 integrieren, können aus diesen Programmen zusätzliche Vorteile ziehen. Weitere Informationen finden Sie unter [Microsoft 365-Herausgebernachweis](/microsoft-365-app-certification/docs/attestation) und [Microsoft 365-App-Zertifizierungsprogramm](/microsoft-365-app-certification/docs/certification). 

- **Entspricht dies dem Azure AD-Anwendungskatalog?** Nein. Das Herausgeberüberprüfungsprogramm ist ein ergänzendes, aber vom [Azure Active Directory-Anwendungskatalog](v2-howto-app-gallery-listing.md) getrenntes Programm. Entwickler, die die oben genannten Kriterien erfüllen, sollten die Herausgeberüberprüfung unabhängig von der Teilnahme an diesem Programm ausführen. 

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie [eine App als durch den Herausgeber verifiziert markieren](mark-app-as-publisher-verified.md).
* Lesen Sie den Artikel [Problembehandlung bei der Herausgeberüberprüfung](troubleshoot-publisher-verification.md).
