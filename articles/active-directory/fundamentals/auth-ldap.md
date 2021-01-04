---
title: LDAP-Authentifizierung mit Azure Active Directory
description: Architekturleitfaden zum Implementieren der LDAP-Authentifizierung mit Azure Active Directory
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
ms.openlocfilehash: f209d394e1a0c2c4ddde9cbf8df2704647e2822a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168711"
---
# <a name="ldap-authentication-with-azure-active-directory"></a>LDAP-Authentifizierung mit Azure Active Directory

Lightweight Directory Access Protocol (LDAP) ist ein Anwendungsprotokoll, das mit verschiedenen Verzeichnisdiensten eingesetzt werden kann. Verzeichnisdienste wie z. B. Active Directory [speichern Benutzer- und Kontoinformationen](https://www.dnsstuff.com/active-directory-service-accounts) sowie Sicherheitsinformationen wie Kennwörter. Der Dienst ermöglicht dann die Freigabe der Informationen für andere Geräte im Netzwerk. Unternehmensanwendungen wie E-Mail-Programme, CRM-Systeme und Personalverwaltungssoftware können LDAP verwenden, um Informationen zu authentifizieren, zu suchen und darauf zuzugreifen. 

Azure Active Directory (Azure AD) unterstützt dieses Muster über Azure AD Domain Services (AD DS). So können Organisationen, die eine Cloud-First-Strategie verfolgen, ihre Umgebung modernisieren, indem sie ihre lokalen LDAP-Ressourcen in die Cloud verlagern. Dieses Verfahren bietet folgende unmittelbaren Vorteile: 

* Integration in Azure AD. Hinzufügungen von Benutzern und Gruppen oder Attributänderungen an Benutzer- oder Gruppenobjekten werden automatisch von Ihrem Azure AD-Mandanten mit AD DS synchronisiert. Änderungen an Objekten in der lokalen Active Directory-Instanz werden mit Azure AD und dann mit AD DS synchronisiert.

* Vereinfachen von Vorgängen. Lokale Infrastrukturen müssen seltener manuell verwaltet und gepatcht werden. 

* Zuverlässig. Sie profitieren von verwalteten, hoch verfügbaren Diensten. 

## <a name="use-when"></a>Wenn folgende Bedingungen vorliegen

Eine Anwendung oder ein Dienst muss die LDAP-Authentifizierung verwenden.

![Diagramm der Architektur](./media/authentication-patterns/ldap-auth.png)

## <a name="components-of-system"></a>Komponenten des Systems

* **Benutzer:** Der Benutzer greift über einen Browser auf von LDAP abhängige Anwendungen zu.

* **Webbrowser**: Die Schnittstelle, mit der der Benutzer interagiert, um auf die externe URL der Anwendung zuzugreifen.

* **Virtuelles Netzwerk**: Ein privates Netzwerk in Azure, über das die Legacyanwendung LDAP-Dienste nutzen kann. 

* **Legacyanwendungen**: Anwendungen oder Serverworkloads, für die LDAP in einem virtuellen Netzwerk in Azure bereitgestellt sein muss oder die über Netzwerkrouten Einblick in AD DS-Instanz-IPs haben. 

* **Azure AD**: Synchronisiert Identitätsinformationen aus dem lokalen Verzeichnis der Organisation über Azure AD Connect.

* **Azure AD Domain Services (AD DS)** : Dieser Dienst führt eine unidirektionale Synchronisierung von Azure AD durch, um Zugriff auf einen zentralen Satz aus Benutzern, Gruppen und Anmeldeinformationen zu ermöglichen. Die AD DS-Instanz ist einem virtuellen Netzwerk zugewiesen. Anwendungen, Dienste und VMs in Azure, die eine Verbindung mit diesem AD DS zugewiesenen virtuellen Netzwerk herstellen, können gemeinsame AD DS-Features wie LDAP, Domänenbeitritt, Gruppenrichtlinien, Kerberos und NTLM-Authentifizierung nutzen.
   > [!NOTE]
   >  In Umgebungen, in denen die Organisation keine Kennworthashes synchronisieren kann oder Benutzer sich mithilfe von Smartcards anmelden, empfiehlt sich die Verwendung einer Ressourcengesamtstruktur in AD DS. 

* **Azure AD Connect**: Ein Tool zum Synchronisieren von lokalen Identitätsinformationen mit Microsoft Azure AD. Der Bereitstellungs-Assistent und die Anleitungen unterstützen Sie beim Konfigurieren von Voraussetzungen und Komponenten, die für die Verbindung erforderlich sind – einschließlich Synchronisierungs- und Anmeldevorgängen von Active Directory in Azure AD. 

* **Active Directory**: Verzeichnisdienst, der [lokale Identitätsinformationen wie Benutzer- und Kontoinformationen](https://www.dnsstuff.com/active-directory-service-accounts) sowie Sicherheitsinformationen wie Kennwörter speichert.

## <a name="implement-ldap-authentication-with-azure-ad"></a>Implementieren der LDAP-Authentifizierung mit Azure AD

* [Tutorial: Erstellen und Konfigurieren einer verwalteten Azure Active Directory Domain Services-Domäne](../../active-directory-domain-services/tutorial-create-instance.md) 

* [Tutorial: Konfigurieren Sie virtuelle Netzwerke für eine verwaltete Azure Active Directory Domain Services-Domäne.](../../active-directory-domain-services/tutorial-configure-networking.md) 

* [Tutorial: Konfigurieren von Secure LDAP (LDAPS) für eine verwaltete Azure AD Domain Services-Domäne](../../active-directory-domain-services/tutorial-configure-ldaps.md) 

* [Tutorial: Erstellen einer ausgehenden Gesamtstruktur-Vertrauensstellung zu einer lokalen Domäne in Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-forest-trust.md)

