---
title: Erzielen von Resilienz mithilfe des Gerätestatus in Azure Active Directory
description: Ein Leitfaden für Architekten und IT-Administratoren zum Erzielen von Resilienz mithilfe des Gerätestatus
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa334c21e23ecddad287846d34aaf0d72af66cc
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602183"
---
# <a name="build-resilience-with-device-states"></a>Erzielen von Resilienz mithilfe des Gerätestatus

Durch die Aktivierung des [Gerätestatus](../devices/overview.md) mit Azure AD können Administratoren [Richtlinien für bedingten Zugriff](../conditional-access/overview.md) erstellen und damit den Zugriff auf Anwendungen nach Gerätestatus steuern. Der zusätzliche Vorteil bei Geräten besteht darin, dass sie strenge Authentifizierungsanforderungen für den Zugriff auf Ressourcen erfüllen. Dadurch reduzieren sich zusätzliche MFA-Authentifizierungsanforderungen, und die Resilienz wird erhöht. 

Im folgenden Flussdiagramm werden die verschiedenen Methoden zum Integrieren von Geräten in Azure AD dargestellt, welche die Nutzung des Gerätestatus ermöglichen. Sie können in Ihrer Organisation auch mehrere verwenden.

![Flussdiagramm für die Auswahl des Gerätestatus](./media/resilience-with-device-states/admin-resilience-devices.png)

Bei Verwendung des [Gerätestatus](../devices/overview.md) profitieren die Benutzer in den meisten Fällen durch ein [primäres Aktualisierungstoken](../devices/concept-primary-refresh-token.md) (Primary Refresh Token, PRT) vom einmaligen Anmelden bei Ressourcen. Das primäre Aktualisierungstoken enthält mit dem Benutzer und Gerät verbundene Ansprüche und kann zum Abrufen von Authentifizierungstoken für den Zugriff vom Gerät auf Anwendungen verwendet werden. Das primäre Aktualisierungstoken ist 14 Tage lang gültig und wird fortlaufend verlängert, solange der Benutzer das Gerät aktiv nutzt. So entsteht für den Benutzer eine resiliente Umgebung. Ein primäres Aktualisierungstoken kann auch auf unterschiedliche Weise einen MFA-Anspruch (Multi-Factor Authentication) erhalten. Weitere Informationen finden Sie unter [Wann erhält ein PRT einen MFA-Anspruch?](../devices/concept-primary-refresh-token.md)

## <a name="how-do-device-states-help"></a>In welcher Form ist der Gerätestatus hilfreich?

Wenn ein primäres Aktualisierungstoken verwendet wird, um den Zugriff auf eine Anwendung anzufordern, werden das zugehörige Gerät, die Sitzung und die MFA-Ansprüche von Azure AD als vertrauenswürdig eingestuft. Wenn Administratoren Richtlinien erstellen, die entweder eine gerätebasierte Steuerung oder ein Multi-Factor Authentication-Steuerelement erfordern, kann die Richtlinienanforderung ohne den Versuch einer mehrstufigen Authentifizierung durch den Gerätestatus erfüllt werden. Dem Benutzer werden auf demselben Gerät keine zusätzlichen MFA-Eingabeaufforderungen angezeigt. Dadurch erhöht sich die Resilienz bei einer Unterbrechung des Azure MFA-Diensts bzw. dessen Abhängigkeit von lokalen Telekommunikationsanbietern.

## <a name="how-do-i-implement-device-states"></a>Wie wird der Gerätestatus implementiert?

* Aktivieren Sie für unternehmenseigene Windows-Geräte [Azure AD Hybrid Join](../devices/hybrid-azuread-join-plan.md) und [Azure AD Join](../devices/azureadjoin-plan.md), und fordern Sie nach Möglichkeit die Einbindung an. Wenn dies nicht möglich ist, fordern Sie die Registrierung an.

  Wenn in Ihrer Organisation ältere Versionen von Windows vorhanden sind, aktualisieren Sie diese Geräte auf Windows 10.

* Legen Sie als Standard für den Browserzugriff für die Benutzer die Verwendung von [Microsoft Edge](https://docs.microsoft.com/deployedge/microsoft-edge-security-identity) oder Google Chrome mit [unterstützten](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) [Erweiterungen](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb) fest, bei denen das nahtlose einmalige Anmelden bei Webanwendungen mit dem primären Aktualisierungstoken (PRT) aktiviert ist.

* Stellen Sie für private oder unternehmenseigene iOS- und Android-Geräte die [Microsoft Authenticator-App](../user-help/user-help-auth-app-overview.md) bereit. Neben der mehrstufigen Authentifizierung (MFA) und den kennwortlosen Anmeldefunktionen ermöglicht die Microsoft Authenticator-App durch die [Brokerauthentifizierung](../develop/brokered-auth.md) (mit weniger Authentifizierungsaufforderungen für den Endbenutzer) das einmalige Anmelden, und zwar anwendungsübergreifend für native Anwendungen.

* Verwenden Sie bei privaten oder unternehmenseigenen iOS- und Android-Geräten die [mobile Anwendungsverwaltung](https://docs.microsoft.com/mem/intune/apps/app-management) für den sicheren Zugriff auf Unternehmensressourcen mit weniger Authentifizierungsanforderungen. 

* [Verwenden Sie das Microsoft Enterprise SSO-Plug-In für Apple-Geräte (Vorschau)](../develop/apple-sso-plugin.md). Dieses registriert das Gerät und stellt einmaliges Anmelden (browser- und anwendungsübergreifend) für native Azure AD-Anwendungen bereit. 

## <a name="next-steps"></a>Nächste Schritte
Resilienzressourcen für Administratoren und Architekten
 
* [Erzielen von Resilienz durch die Verwaltung von Anmeldeinformationen](resilience-in-credentials.md)

* [Erzielen von Resilienz durch die Nutzung der fortlaufenden Zugriffsevaluierung (Continuous Access Evaluation, CAE)](resilience-with-continuous-access-evaluation.md)

* [Erzielen von Resilienz bei der externen Benutzerauthentifizierung](resilience-b2b-authentication.md)

* [Erzielen von Resilienz bei der Hybridauthentifizierung](resilience-in-hybrid.md)

* [Erzielen von Resilienz beim Anwendungszugriff mit dem Anwendungsproxy](resilience-on-premises-access.md)


Resilienzressourcen für Entwickler

* [Erzielen von Resilienz bei der Identitäts- und Zugriffsverwaltung (IAM) für Ihre Anwendungen](resilience-app-development-overview.md)

* [Erzielen von Resilienz für Ihre CIAM-Systeme](resilience-b2c.md)
