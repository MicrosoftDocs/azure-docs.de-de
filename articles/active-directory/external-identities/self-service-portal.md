---
title: 'Self-Service-Registrierungsportal für B2B-Zusammenarbeit: Azure AD'
description: Erfahren Sie, wie Sie den Onboarding-Workflow für Azure Active Directory B2B-Benutzer an die Bedürfnisse ihrer Organisation anpassen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a5b800e78448afcc970010535ba12b543d3cc74
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860506"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Self-Service-Registrierung für Azure AD B2B Collaboration

Kunden können mit den integrierten Features, die über das [Azure-Portal](https://portal.azure.com) und den [Anwendungszugriffsbereich](https://myapps.microsoft.com) für Endbenutzer verfügbar gemacht werden, viele Aufgaben erledigen. Unter Umständen müssen Sie jedoch den Onboarding-Workflow für B2B-Benutzer an die Bedürfnisse ihrer Organisation anpassen.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Azure AD-Berechtigungsverwaltung für die B2B-Gastbenutzerregistrierung

Als einladende Organisation wissen Sie möglicherweise im Vorfeld nicht, welche externen Mitarbeiter Zugriff auf Ihre Ressourcen benötigen. Benutzer von Partnerunternehmen müssen sich selbstständig registrieren können, und die Registrierung muss Richtlinien unterliegen, die von Ihnen gesteuert werden. Konfigurieren Sie mithilfe der [Azure AD-Berechtigungsverwaltung](../governance/entitlement-management-overview.md) Richtlinien zum [Verwalten des Zugriffs für externe Benutzer](../governance/entitlement-management-external-users.md#how-access-works-for-external-users), damit Benutzer aus anderen Organisationen Zugriff anfordern und nach erfolgter Genehmigung Gastkonten erhalten und Gruppen, Apps und SharePoint Online-Websites zugewiesen werden können.

## <a name="azure-active-directory-b2b-invitation-api"></a>Einladungs-API von Azure Active Directory B2B

Organisationen können die [Einladungs-Manager-API von Microsoft Graph](/graph/api/resources/invitation) verwenden, um ihre eigenen Onboardingumgebungen für B2B-Gastbenutzer zu erstellen. Wenn Sie eine Self-Service-B2B-Gastbenutzerregistrierung anbieten möchten, empfehlen wir die Verwendung der [Azure AD-Berechtigungsverwaltung](../governance/entitlement-management-overview.md). Wenn Sie dagegen eine eigene Umgebung erstellen möchten, können Sie die [API zum Erstellen von Einladungen](/graph/api/invitation-post?tabs=http) verwenden, um beispielsweise Ihre angepasste Einladungs-E-Mail automatisch direkt an den B2B-Benutzer zu senden. Alternativ kann Ihre App die in der Erstellungsantwort zurückgegebene URL vom Typ „inviteRedeemUrl“ verwenden, um Ihre eigene Einladung (über den von Ihnen bevorzugten Kommunikationsweg) für den eingeladenen Benutzer zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

* [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
* [Preise für externe Identitäten](external-identities-pricing.md)
* [Häufig gestellte Fragen zur Azure Active Directory B2B-Zusammenarbeit](faq.md)
