---
title: Erzielen von Resilienz für Identitäts- und Zugriffsverwaltung mit Azure Active Directory
description: Eine Anleitung für Architekten, IT-Administratoren und Entwickler zum Erreichen von Resilienz bei einer Unterbrechung ihrer Identitätssysteme.
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
ms.openlocfilehash: 39021fecb79f6b8e7536daad592378ed998760a7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454350"
---
# <a name="building-resilience-into-identity-and-access-management-with-azure-active-directory"></a>Erzielen von Resilienz für Identitäts- und Zugriffsverwaltung mit Azure Active Directory

Die Identitäts- und Zugriffsverwaltung (Identity and Access Management, IAM) ist ein Framework aus Prozessen, Richtlinien und Technologien, die die Verwaltung von Identitäten und deren Zugriff auf Ressourcen vereinfachen. Sie umfasst die zahlreichen Komponenten, die die Authentifizierung und Autorisierung von Benutzern und anderen Konten in Ihrem System unterstützen.

IAM-Resilienz ist die Fähigkeit, Unterbrechungen von Systemkomponenten zu überstehen und die Wiederherstellung mit minimalen Auswirkungen auf Ihr Unternehmen, Ihre Benutzer, Kunden und Abläufe zu gewährleisten. Das Reduzieren von Abhängigkeiten, Komplexität und Single Points of Failure bei Sicherstellung einer umfassenden Fehlerbehandlung erhöhen die Resilienz.

Unterbrechungen können von jeder Komponente Ihres IAM-Systems ausgehen. Gehen Sie zum Erstellen eines robusten IAM-Systems davon aus, dass Unterbrechungen auftreten werden, und planen sie Sie. 

Wenn Sie die Resilienz Ihrer IAM-Lösung planen, sollten Sie die folgenden Elemente berücksichtigen: 

* Anwendungen, die auf Ihrem IAM-System basieren.

* Die öffentlichen Infrastrukturen, die von Ihren Authentifizierungsaufrufen verwendet werden, einschließlich Telekommunikationsunternehmen, Internetdienstanbietern und Anbietern öffentlicher Schlüssel.

* Ihre Cloud- und lokalen Identitätsanbieter.

* Andere Dienste, die auf IAM basieren, sowie die APIs, die sie miteinander verbinden.

* Alle anderen lokalen Komponenten in Ihrem System.

Unabhängig von der Quelle ist das Erkennen und Planen von Eventualitäten wichtig. Das Hinzufügen zusätzlicher Identitätssysteme und die sich daraus ergebenden Abhängigkeiten und Komplexitäten können Ihre Resilienz jedoch eher verringern als erhöhen.

Lesen Sie die folgenden Artikel, um mehr Resilienz in Ihren Systemen zu erzielen:

* [Erzielen von Resilienz in der IAM-Infrastruktur](resilience-in-infrastructure.md)

* [Erzielen von IAM-Resilienz in Ihren Anwendungen](resilience-app-development-overview.md)

* [Erzielen von Resilienz in der Kundenidentitäts- und Zugriffsverwaltung mit Azure Active Directory B2C](resilience-b2c.md)
