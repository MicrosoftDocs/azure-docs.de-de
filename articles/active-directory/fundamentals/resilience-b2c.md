---
title: Erzielen von Resilienz in der Kundenidentitäts- und Zugriffsverwaltung mit Azure AD B2C | Microsoft-Dokumentation
description: Methoden zum Erzielen von Resilienz in der Kundenidentitäts- und Zugriffsverwaltung mit Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba7b8ff2555fd7014cf16f66745721c6425ff868
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029522"
---
# <a name="build-resilience-in-your-customer-identity-and-access-management-with-azure-active-directory-b2c"></a>Erzielen von Resilienz in der Kundenidentitäts- und Zugriffsverwaltung mit Azure Active Directory B2C

[Azure Active Directory (AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview) ist eine Plattform für die Kundenidentitäts- und Zugriffsverwaltung (Customer Identity and Access Management, CIAM), die Sie beim erfolgreichen Starten Ihrer wichtigen kundenorientierten Anwendungen unterstützt. Für die [Resilienz](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/) gibt es viele integrierte Features, mit denen Sie Ihren Dienst auf Ihre Bedürfnisse zuschneiden und angesichts möglicher Ausfallszenarien die Resilienz verbessern können. Außerdem ist es beim Starten einer unternehmenskritischen Anwendung wichtig, verschiedene Entwurfs-und Konfigurationselemente in der Anwendung zu berücksichtigen und zu überlegen, wie die Anwendung in Azure AD B2C konfiguriert werden soll, um bei Ausfall- oder Fehlerszenarien ein resilientes Reaktionsverhalten zu gewährleisten. In diesem Artikel werden einige bewährte Methoden erläutert, die Ihnen helfen, die Resilienz zu erhöhen.

Ein robuster bzw. resilienter Dienst ist ein Dienst, der trotz Unterbrechungen weiterhin funktionsfähig ist. Folgende Punkte können dazu beitragen, die Resilienz Ihres Diensts zu erhöhen:

- Verstehen Sie alle Komponenten

- Beseitigen Sie einzelne Fehlerquellen

- Isolieren Sie fehlerhafte Komponenten, um deren Auswirkungen einzuschränken

- Stellen Sie Redundanz mit schnellen Failovermechanismen und Wiederherstellungspfaden bereit

Beim Entwickeln Ihrer Anwendung empfiehlt es sich, zu überlegen, wie Sie mit den Identitätskomponenten Ihrer Lösung [für mehr Resilienz bei Authentifizierung und Autorisierung in Ihren Anwendungen](resilience-app-development-overview.md) sorgen können. In diesem Artikel haben wir versucht, speziell auf die Verbesserung der Resilienz bei Azure AD B2C-Anwendungen einzugehen. Unsere Empfehlungen sind nach CIAM-Funktionen gruppiert.

![Abbildung mit den CIAM-Komponenten](media/resilience-b2c/high-level-components.png). In den folgenden Abschnitten finden Sie Anleitungen zum Erzielen von Resilienz in folgenden Bereichen:

- [Endbenutzererfahrung](resilient-end-user-experience.md): Aktivieren Sie einen Ausweichplan für den Authentifizierungsflow, und minimieren Sie die potenziellen Auswirkungen einer Unterbrechung des Azure AD B2C-Authentifizierungsdiensts.

- [Schnittstellen mit externen Prozessen](resilient-external-processes.md): Erzielen Sie Resilienz in Ihren Anwendungen und Schnittstellen durch die Wiederherstellung nach Fehlern.  

- [Bewährte Entwicklermethoden](resilience-b2c-developer-best-practices.md): Vermeiden Sie eine Anfälligkeit aufgrund von allgemeinen Problemen mit benutzerdefinierten Richtlinien, und verbessern Sie die Fehlerbehandlung zum Beispiel bei Interaktionen mit Anspruchsprüfern, Drittanbieteranwendungen und REST-APIs.

- [Überwachung und Analyse](resilience-with-monitoring-alerting.md): Bewerten Sie die Integrität Ihres Diensts durch Überwachen von Schlüsselindikatoren und Erkennen von Fehlern und Leistungsunterbrechungen durch Warnungen.

- [Schaffen von Resilienz für die Authentifizierungsinfrastruktur](resilience-in-infrastructure.md)

- [Steigern der Resilienz für Authentifizierung und Autorisierung in Ihren Anwendungen](resilience-app-development-overview.md)
