---
title: Erreichen von Resilienz in einer IAM-Infrastruktur mit Azure Active Directory
description: Eine Anleitung für Architekten und IT-Administratoren zum Erreichen von Resilienz bei einer Unterbrechung ihrer IAM-Infrastruktur.
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
ms.openlocfilehash: f65ab02e06319519548eaa2c02120691a0ceef02
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498556"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>Erzielen von Resilienz in einer Identitäts- und Zugriffsverwaltungsinfrastruktur

Azure Active Directory ist ein globales Cloudidentitäts- und -zugriffsverwaltungssystem, das wichtige Dienste wie Authentifizierung und Autorisierung für die Ressourcen Ihrer Organisation bereitstellt. Dieses Dokument stellt Ihnen eine Anleitung zur Verfügung, um das Risiko einer Unterbrechung der Authentifizierungs- oder Autorisierungsdienste für Ressourcen, die auf Azure Active Directory (Azure AD) beruhen, zu verstehen, einzudämmen und zu mindern. 

Der Dokumentsatz wendet sich an

* Identitätsarchitekten

* Identitätsdienstbesitzer

* Identitätsbetriebsteams

Weitere Informationen finden Sie auch in der Dokumentation für [Anwendungsentwickler](https://aka.ms/azureadresilience/developer) und für [Azure AD B2C-Systeme](resilience-b2c.md).

## <a name="what-is-resilience"></a>Was ist Resilienz?

Im Zusammenhang mit Ihrer Identitätsinfrastruktur ist Resilienz die Fähigkeit, Unterbrechungen von Diensten wie Authentifizierung und Autorisierung oder den Ausfall anderer Komponenten mit minimalen oder gar keinen Auswirkungen auf Ihr Unternehmen, Ihre Benutzer und Ihren Betrieb zu überstehen. Die Auswirkungen von Ausfällen können schwerwiegend sein, und Resilienz erfordert eine sorgfältige Planung.

## <a name="why-worry-about-disruption"></a>Warum sollte über Unterbrechungen nachgedacht werden?

Für jeden Aufruf des Authentifizierungssystems können Unterbrechungen auftreten, wenn für eine Komponente des Aufrufs ein Fehler auftritt. Wenn die Authentifizierung aufgrund der Fehler für die zugrunde liegenden Komponenten gestört wird, können Ihre Benutzer nicht auf ihre Anwendungen zugreifen. Daher ist die Reduzierung der Anzahl von Authentifizierungsaufrufen und der Anzahl von Abhängigkeiten in diesen Aufrufen wichtig für die Resilienz. Anwendungsentwickler können eine gewisse Kontrolle darüber erlangen, wie häufig Token angefordert werden. Arbeiten Sie z. B. mit ihren Entwicklern zusammen, um sicherzustellen, dass Sie nach Möglichkeit verwaltete Azure AD-Identitäten für ihre Anwendungen verwenden. 

In einem tokenbasierten Authentifizierungssystem wie Azure AD muss die Anwendung eines Benutzers (der Client) ein Sicherheitstoken aus dem Identitätssystem abrufen, bevor auf eine Anwendung oder eine andere Ressource zugegriffen werden kann. Während der Gültigkeitsdauer kann ein Client das gleiche Token mehrmals verwenden, um auf die Anwendung zuzugreifen.

Wenn das Token, das für die Anwendung bereitgestellt wird, abläuft, weist die Anwendung das Token zurück, und der Client muss ein neues Token von Azure AD abrufen. Zur Beschaffung eines neuen Tokens ist ggf. ein Benutzereingriff erforderlich, z. B. das Angeben von Anmeldeinformationen oder die Erfüllung von anderen Anforderungen des Authentifizierungssystems. Indem die Häufigkeit von Authentifizierungsaufrufen durch die Nutzung von Token mit längerer Lebensdauer reduziert wird, wird die Anzahl von unnötigen Interaktionen verringert. Sie müssen jedoch die Lebensdauer des Tokens gegen das Risiko abwägen, das durch weniger Richtlinienauswertungen entsteht. Weitere Informationen zum Verwalten der Tokenlebensdauer finden Sie in diesem Artikel zum [Optimieren von Eingabeaufforderungen für erneute Authentifizierung](https://docs.microsoft.com/azure/active-directory/authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime).

## <a name="ways-to-increase-resilience"></a>Möglichkeiten zum Verbessern der Resilienz
Die folgende Abbildung zeigt sechs konkrete Möglichkeiten, die Resilienz zu steigern. Jede Methode wird ausführlich in den Artikeln erläutert, die im Abschnitt „Nächste Schritte“ dieses Artikels genannt werden.
  
![Abbildung: Diagramm mit einer Übersicht über Administratorresilienz](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>Nächste Schritte
Resilienzressourcen für Administratoren und Architekten
 
* [Erzielen von Resilienz durch die Verwaltung von Anmeldeinformationen](resilience-in-credentials.md)

* [Erzielen von Resilienz mithilfe des Gerätestatus](resilience-with-device-states.md)

* [Erzielen von Resilienz durch die Nutzung fortlaufender Zugriffsevaluierung (Continuous Access Evaluation, CAE)](resilience-with-continuous-access-evaluation.md)

* [Erzielen von Resilienz bei der externen Benutzerauthentifizierung](resilience-b2b-authentication.md)

* [Erzielen von Resilienz bei der Hybridauthentifizierung](resilience-in-hybrid.md)

* [Erzielen von Resilienz beim Anwendungszugriff mit dem Anwendungsproxy](resilience-on-premises-access.md)

Resilienzressourcen für Entwickler

* [Erzielen von Resilienz bei der Identitäts- und Zugriffsverwaltung (IAM) für Ihre Anwendungen](resilience-app-development-overview.md)

* [Erzielen von Resilienz für Ihre CIAM-Systeme](resilience-b2c.md)
