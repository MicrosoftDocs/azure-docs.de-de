---
title: SCIM-Synchronisierung mit Azure Active Directory
description: Architekturleitfaden zum Erzielen der SCIM-Synchronisierung mit Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b95aac504bc6ee72c353faecad25384e2dc90840
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172417"
---
# <a name="scim-synchronization-with-azure-active-directory"></a>SCIM-Synchronisierung mit Azure Active Directory

System for Cross-Domain Identity Management (SCIM) ist ein offenes Standardprotokoll für die Automatisierung des Austauschs von Benutzeridentitätsinformationen zwischen Identitätsdomänen und IT-Systemen. SCIM stellt sicher, dass für die dem System für die Humankapitalverwaltung (Human Capital Management, HCM) hinzugefügten Mitarbeiter automatisch Konten in Azure Active Directory (Azure AD) oder Windows Server Active Directory erstellt werden. Benutzerattribute und -profile werden zwischen den beiden Systemen synchronisiert, wobei das Entfernen von Benutzern basierend auf dem Benutzerstatus oder der Rollenänderung aktualisiert wird.

SCIM ist eine standardisierte Definition von zwei Endpunkten: einem „/Users“- und einem „/Groups“-Endpunkt. Es verwendet allgemeine REST-Verben, um Objekte zu erstellen, zu aktualisieren und zu löschen. Außerdem verwendet es ein vordefiniertes Schema für allgemeine Attribute wie Gruppenname, Benutzername, Vorname, Nachname und E-Mail-Adresse. Anwendungen, die eine SCIM 2.0-REST-API bieten, können den Aufwand für die Arbeit mit proprietären Benutzerverwaltungs-APIs oder -produkten reduzieren oder eliminieren. So kann beispielsweise jeder SCIM-kompatible Client eine HTTP POST-Anforderung für ein JSON-Objekt an den „/Users“-Endpunkt senden, um einen neuen Benutzereintrag zu erstellen. Anstatt eine leicht abweichende API für dieselben grundlegenden Aktionen zu benötigen, können Apps, die dem SCIM-Standard entsprechen, sofort die Vorteile bereits vorhandener Clients, Tools und Codes nutzen. 

## <a name="use-when"></a>Zu verwenden in folgenden Fällen: 

Sie möchten Benutzerinformationen aus einem HCM-System automatisch für Azure AD und Windows Server Active Directory und dann ggf. für Zielsysteme bereitstellen. 

![Architekturdiagramm](./media/authentication-patterns/scim-auth.png)


## <a name="components-of-system"></a>Komponenten des Systems 

* **HCM-System**: Anwendungen und Technologien, die HCM-Prozesse und -Praktiken ermöglichen, die HR-Prozesse während des gesamten Mitarbeiterlebenszyklus unterstützen und automatisieren. 

* **Azure AD-Bereitstellungsdienst**: Verwendet das SCIM 2.0-Protokoll für die automatische Bereitstellung. Der Dienst stellt eine Verbindung mit dem SCIM-Endpunkt für die Anwendung her und verwendet das SCIM-Benutzerobjektschema und REST-APIs, um die Bereitstellung und Aufhebung der Bereitstellung von Benutzern und Gruppen zu automatisieren.  

* **Azure AD**: Das zum Verwalten des Lebenszyklus von Identitäten und deren Berechtigungen verwendete Benutzerrepository. 

* **Zielsystem**: Die Anwendung oder das System, die/das über den SCIM-Endpunkt verfügt und die Azure AD-Bereitstellung verwendet, um die automatische Bereitstellung von Benutzern und Gruppen zu ermöglichen.  

## <a name="implement-scim-with-azure-ad"></a>Implementieren von SCIM mit Azure AD 

* [Funktionsweise der Bereitstellung in Azure AD](../app-provisioning/how-provisioning-works.md)

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps im Azure-Portal](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Erstellen eines SCIM-Endpunkts und Konfigurieren der Benutzerbereitstellung mit Azure AD](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Bekannte Probleme und Lösungen bei der Einhaltung des SCIM 2.0-Protokolls des Azure AD-Benutzerbereitstellungsdiensts](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md)