---
title: Erzielen von Resilienz durch die Nutzung fortlaufender Zugriffsevaluierung in Azure Active Directory
description: Leitfaden für Architekten und IT-Administratoren zur Verwendung fortlaufender Zugriffsevaluierung (Continuous Access Evaluation, CAE)
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
ms.openlocfilehash: 8ad36c2a7f47948d9362b85e78355e6046cda703
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919041"
---
# <a name="build-resilience-by-using-continuous-access-evaluation"></a>Erzielen von Resilienz durch die Nutzung fortlaufender Zugriffsevaluierung (Continuous Access Evaluation, CAE)

Mit [fortlaufender Zugriffsevaluierung (Continuous Access Evaluation, CAE)](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) können Azure AD-Anwendungen kritische Ereignisse abonnieren, die dann ausgewertet und erzwungen werden können. Dies umfasst die Auswertung der folgenden Ereignisse:

* Das Benutzerkonto, das gelöscht oder deaktiviert wird.

* Das Kennwort für einen Benutzer wird geändert.

* MFA wird für den Benutzer aktiviert.

* Der Administrator widerruft explizit ein Token.

* Ein erhöhtes Benutzerrisiko wurde erkannt.

Infolgedessen können Anforderungen nicht abgelaufene Token basierend auf den von Azure AD signalisierten Ereignissen zurückweisen (wie in der folgenden Abbildung dargestellt).

![Konzeptdiagramm von CAE](./media/resilience-with-cae/admin-resilience-continuous-access-evaluation.png)

## <a name="how-does-cae-help"></a>Wie hilft CAE, dieses Problem zu lösen?

Dieser Mechanismus ermöglicht es Azure AD, Token mit längerer Lebensdauer auszugeben und gleichzeitig Anwendungen eine Möglichkeit zu bieten, den Zugriff zu widerrufen und eine erneute Authentifizierung nur bei Bedarf zu erzwingen. Das Ergebnis dieses Musters sind weniger Aufrufe zum Abrufen von Token. Dies bedeutet, dass der End-to-End-Flow robuster ist. 

Zum Verwenden von CAE müssen sowohl der Dienst als auch der Client CAE-fähig sein. Microsoft 365-Dienste wie Exchange Online, Teams und SharePoint Online unterstützen CAE. Auf der Clientseite sind browserbasierte Benutzeroberflächen, die diese Office 365-Dienste (z. B. Outlook Web App) verwenden, und bestimmte Versionen von nativen Office 365-Clients CAE-fähig. Weitere Microsoft Cloud Services werden in Zukunft CAE-fähig.

Microsoft arbeitet mit der Branche zusammen, um [Standards](https://openid.net/wg/sse/) zu etablieren, mit denen Anwendungen von Drittanbietern diese Funktion verwenden können. Sie können auch Anwendungen entwickeln, die CAE-fähig sind. Weitere Informationen finden Sie unter „Erzielen von Resilienz in Ihrer Anwendung“.

## <a name="how-do-i-implement-cae"></a>Wie implementiere ich CAE?

* [Aktivieren Sie CAE](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) in der Azure AD-Sicherheitskonfiguration.

* Stellen Sie sicher, dass Ihre Organisation [kompatible Versionen](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) nativer Microsoft Office-Anwendungen verwendet.

* [Optimieren Sie die Eingabeaufforderungen für erneute Authentifizierung](https://docs.microsoft.com/azure/active-directory/authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime).

 
## <a name="next-steps"></a>Nächste Schritte
Resilienzressourcen für Administratoren und Architekten
 
* [Erzielen von Resilienz durch die Verwaltung von Anmeldeinformationen](resilience-in-credentials.md)

* [Erzielen von Resilienz mithilfe des Gerätestatus](resilience-with-device-states.md)

* [Erzielen von Resilienz bei der externen Benutzerauthentifizierung](resilience-b2b-authentication.md)

* [Erzielen von Resilienz bei der Hybridauthentifizierung](resilience-in-hybrid.md)

* [Erzielen von Resilienz beim Anwendungszugriff mit dem Anwendungsproxy](resilience-on-premises-access.md)

Resilienzressourcen für Entwickler

* [Erzielen von Resilienz bei der Identitäts- und Zugriffsverwaltung (IAM) für Ihre Anwendungen](resilience-app-development-overview.md)

* [Erzielen von Resilienz für Ihre CIAM-Systeme](resilience-b2c.md)
