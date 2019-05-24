---
title: Ressourcen zum Migrieren von Apps zu Azure Active Directory | Microsoft-Dokumentation
description: Ressourcen, die Ihnen beim Migrieren des Anwendungszugriffs und der Authentifizierung zu Azure Active Directory (Azure AD) helfen.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/19/2018
ms.author: mimart
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12370d54500dbc1fcd6f812206fdfea3e5298112
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826077"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Ressourcen zum Migrieren von Anwendungen zu Azure Active Directory

Ressourcen, die Ihnen beim Migrieren des Anwendungszugriffs und der Authentifizierung zu Azure Active Directory (Azure AD) helfen. Nehmen Sie an dieser kurzen Umfrage teil (https://aka.ms/AppsMigrationFeedback), um Feedback zu Ihren Erfahrungen beim Migrieren von Apps zu Azure AD bereitzustellen (einschließlich Blockierungen der Migration, Bedarf an Tools/Anleitung oder Gründen für die Beibehaltung Ihres lokalen IDP). 

| Resource  | BESCHREIBUNG  |
|:-----------|:-------------|
|[Migrieren Ihrer Apps zu Azure AD](https://aka.ms/migrateapps/whitepaper) | Dieses Whitepaper stellt die Vorteile der Migration dar und beschreibt, wie Sie für die Migration in vier klar beschriebenen Phasen planen: Ermittlung, Klassifizierung, Migration und laufende Verwaltung. Sie werden durch den Prozess geführt und erfahren, wie Sie das Projekt in einfach umzusetzende Teilschritte aufteilen. Im gesamten Dokument finden Sie Links zu wichtigen Ressourcen, die Ihnen im Verlauf des Prozesses helfen. |
|[Lösungsanleitung: Migrieren von Apps aus Active Directory-Verbunddienste (AD FS) zu Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | Diese Lösungsanleitung führt Sie durch dieselben vier Phasen der Planung und Ausführung eines Anwendungsmigrationsprojekts, die im Whitepaper zur Migration auf einer höheren Ebene beschrieben sind. In dieser Anleitung erfahren Sie, wie Sie diese Phasen auf das spezifisches Ziel des Verschiebens einer Anwendung aus Azure Directory-Verbunddienste (AD FS) in Azure AD anwenden.|
| [Tool: Migrationsbereitschaftsskript für Active Directory-Verbunddienste (AD FS)](https://aka.ms/migrateapps/adfstools) | Dies ist ein Skript, das Sie auf Ihrem lokalen AD FS-Server (Active Directory-Verbunddienste) ausführen können, um die Bereitschaft von Apps für die Migration zu Azure AD zu bestimmen.|
| [Bereitstellungsplan: Migrieren von AD FS zu Kennworthashsynchronisierung](https://aka.ms/ADFSTOPHSDPDownload) | Mit der Kennworthashsynchronisierung werden Benutzerkennworthashes aus Ihrem lokalen Active Directory mit Azure AD synchronisiert. Dadurch kann Azure AD Benutzer authentifizieren, ohne mit dem lokalen Active Directory zu interagieren.| 
| [Bereitstellungsplan: Migrieren von AD FS zu Passthrough-Authentifizierung](https://aka.ms/ADFSTOPTADPDownload)|Mit der Azure AD-Passthrough-Authentifizierung können sich Benutzer mit denselben Kennwörtern sowohl bei lokalen als auch cloudbasierten Anwendungen anmelden. Diese Funktion bietet Ihren Benutzern eine bessere Erfahrung, da sie sich ein Kennwort weniger merken müssen. Sie verringert außerdem die Kosten des IT-Helpdesks, weil die Wahrscheinlichkeit sinkt, dass Benutzer ihr Kennwort vergessen, wenn sie sich nur noch ein Kennwort merken müssen. Wenn Benutzer sich mithilfe von Azure AD anmelden, überprüft dieses Feature die Benutzerkennwörter direkt anhand Ihres lokalen Active Directory.|
| [Bereitstellungsplan: Aktivieren des einmaligen Anmeldens bei einer SaaS-App mit Azure AD](https://aka.ms/SSODPDownload) | Einmaliges Anmelden (Single Sign-On, SSO) ermöglicht Ihnen mit nur einer Anmeldung und einem einzigen Benutzerkonto den Zugriff auf sämtliche Apps und Ressourcen, die Sie für Ihre Geschäftsaktivitäten benötigen. Beispielsweise kann ein Benutzer nach der Anmeldung von Microsoft Office zu Salesforce oder zu Box wechseln, ohne sich ein zweites Mal (beispielsweise durch Eingabe eines Kennworts) zu authentifizieren. 
| [Bereitstellungsplan: Erweitern von Apps auf Azure AD mit Anwendungsproxy](https://aka.ms/AppProxyDPDownload)| Für die Bereitstellung des Zugriffs von Mitarbeiter-Laptops und anderen Geräten aus auf lokale Anwendungen wurden bisher virtuelle private Netzwerke (VPNs) oder Umkreisnetzwerke (demilitarisierte Zonen, DMZs) eingesetzt. Diese Lösungen sind aber nicht nur komplex und schwer zu schützen, sondern können außerdem nur mit hohem Kostenaufwand eingerichtet und verwaltet werden. Azure AD-Anwendungsproxy erleichtert den Zugriff auf lokale Anwendungen. |
| [Bereitstellungspläne](../fundamentals/active-directory-deployment-plans.md) | Hier finden Sie weitere Bereitstellungspläne für die Bereitstellung von Features wie mehrstufige Authentifizierung, bedingten Zugriff, Benutzerbereitstellung, nahtloses SSO, Self-Service-Kennwortzurücksetzung und mehr. |


