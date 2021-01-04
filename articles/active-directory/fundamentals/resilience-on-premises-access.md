---
title: Erzielen von Resilienz beim Anwendungszugriff mit dem Anwendungsproxy
description: Ein Leitfaden für Architekten und IT-Administratoren zur Verwendung des Anwendungsproxys für den robusten Zugriff auf lokale Anwendungen
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
ms.openlocfilehash: f8bfc3fb239f30911eddf0aa27496a465e36c486
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919043"
---
# <a name="build-resilience-in-application-access-with-application-proxy"></a>Erzielen von Resilienz beim Anwendungszugriff mit dem Anwendungsproxy

Der Anwendungsproxy ist ein Feature von Azure AD, mit dem Benutzer von einem Remoteclient aus auf lokale Webanwendungen zugreifen können. Der Anwendungsproxy umfasst den Anwendungsproxydienst in der Cloud und den Anwendungsproxyconnector, der auf einem lokalen Server ausgeführt wird. 

Benutzer greifen über eine URL, die über den Anwendungsproxy veröffentlicht wird, auf lokale Ressourcen zu. Sie werden zur Azure AD-Anmeldeseite umgeleitet. Der Anwendungsproxydienst in Azure AD sendet dann ein Token an den Anwendungsproxyconnector im Unternehmensnetzwerk, der das Token an das lokale Active Directory übergibt. Der authentifizierte Benutzer kann dann auf die lokale Ressource zugreifen. In der folgenden Abbildung werden [Connectors](../manage-apps/application-proxy-connectors.md) in einer [Connectorgruppe](../manage-apps/application-proxy-connector-groups.md) angezeigt.

> [!IMPORTANT]
> Wenn Sie Ihre Anwendungen über den Anwendungsproxy veröffentlichen, müssen Sie [Kapazitätsplanung und geeignete Redundanz für die Anwendungsproxyconnectors](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) implementieren.

![Architekturdiagramm von Anwendung Y](./media/resilience-on-prem-access/admin-resilience-app-proxy.png))

## <a name="how-do-i-implement-application-proxy"></a>Wie wird der Anwendungsproxy implementiert?

Informationen zum Implementieren des Remotezugriffs mit dem Azure AD-Anwendungsproxy finden Sie in den folgenden Ressourcen.

* [Planen der Bereitstellung eines Anwendungsproxys](../manage-apps/application-proxy-deployment-plan.md)

* [Bewährte Methoden für Hochverfügbarkeit und Lastenausgleich](../manage-apps/application-proxy-high-availability-load-balancing.md)

* [Konfigurieren von Proxyservern](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

* [Entwerfen einer robusten Zugriffsseuerungsstrategie](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Nächste Schritte
Resilienzressourcen für Administratoren und Architekten
 
* [Erzielen von Resilienz durch die Verwaltung von Anmeldeinformationen](resilience-in-credentials.md)

* [Erzielen von Resilienz mithilfe des Gerätestatus](resilience-with-device-states.md)

* [Erzielen von Resilienz durch die Nutzung fortlaufender Zugriffsevaluierung (Continuous Access Evaluation, CAE)](resilience-with-continuous-access-evaluation.md)

* [Erzielen von Resilienz bei der externen Benutzerauthentifizierung](resilience-b2b-authentication.md)

* [Erzielen von Resilienz bei der Hybridauthentifizierung](resilience-in-hybrid.md)

Resilienzressourcen für Entwickler

* [Erzielen von Resilienz bei der Identitäts- und Zugriffsverwaltung (IAM) für Ihre Anwendungen](resilience-app-development-overview.md)

* [Erzielen von Resilienz für Ihre CIAM-Systeme](resilience-b2c.md)
