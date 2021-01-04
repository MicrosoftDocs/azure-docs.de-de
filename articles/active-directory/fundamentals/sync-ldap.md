---
title: LDAP-Synchronisierung mit Azure Active Directory
description: Architekturleitfaden zum Erzielen der LDAP-Synchronisierung mit Azure Active Directory
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
ms.openlocfilehash: 1f34e734b315c7c05ce77f5e168a452fc1c1c547
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168677"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>LDAP-Synchronisierung mit Azure Active Directory

Das Lightweight Directory Access-Protokoll (LDAP) ist ein auf dem TCP/IP-Stapel basierendes Verzeichnisdienstprotokoll. Es stellt einen Mechanismus bereit, mit dem Verbindungen mit Internetverzeichnissen hergestellt und diese durchsucht sowie geändert werden können. Der LDAP-Verzeichnisdienst basiert auf einem Client/Server-Modell und dient dazu, den Zugriff auf ein vorhandenes Verzeichnis zu ermöglichen. Viele Unternehmen nutzen lokale LDAP-Server, um Benutzer und Gruppen für ihre kritischen Geschäftsanwendungen zu speichern. 

Azure Active Directory (Azure AD) kann die LDAP-Synchronisierung durch Azure AD Connect ersetzen. Vom Azure AD Connect-Synchronisierungsdienst werden sämtliche Vorgänge im Zusammenhang mit der Synchronisierung von Identitätsdaten zwischen lokalen Umgebungen und Azure AD ausgeführt. 

## <a name="use-when"></a>Wenn folgende Bedingungen vorliegen

Sie müssen Identitätsdaten zwischen Ihren lokalen LDAP v3-Verzeichnissen und Azure AD synchronisieren. 

![Architekturdiagramm](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>Komponenten des Systems

* **Benutzer:** Greift auf eine Anwendung zu, die ein LDAP v3-Verzeichnis nutzt, um Benutzer und Kennwörter zu sortieren.

* **Webbrowser**: Die Komponente, mit der der Benutzer interagiert, um auf die externe URL der Anwendung zuzugreifen.

* **Web-App**: Anwendung, die von LDAP v3-Verzeichnissen abhängig ist.

* **Azure AD**: Azure AD synchronisiert Identitätsinformationen (Benutzer, Gruppen, Kennwörter) aus lokalen LDAP-Verzeichnissen der Organisation über Azure AD Connect. 

* **Azure AD Connect**: Ein Tool zum Verbinden von lokalen Identitätsinfrastrukturen mit Microsoft Azure AD. Der Assistent und die Anleitungen auf der Oberfläche unterstützen beim Bereitstellen und Konfigurieren von Voraussetzungen und erforderlichen Komponenten für die Verbindung. 

* **Benutzerdefinierter Connector**: Ein generischer LDAP-Connector ermöglicht die Integration des Azure AD Connect-Synchronisierungsdiensts in einen LDAP v3-Server. Er befindet sich in Azure AD Connect.

* **Active Directory**: Active Directory ist ein Verzeichnisdienst, der in den meisten Windows Server-Betriebssystemen enthalten ist. Server, auf denen Active Directory-Verzeichnisdienste ausgeführt werden, werden als Domänencontroller bezeichnet und authentifizieren und autorisieren alle Benutzer und Computer in einer Windows-Domäne.

* **LDAP v3-Server**: LDAP-konformes Verzeichnis, in dem Unternehmensbenutzer und -kennwörter für die Verzeichnisdiensteauthentifizierung gespeichert werden.

## <a name="implement-ldap-synchronization-with-azure-ad"></a>Implementieren der LDAP-Synchronisierung mit Azure AD

* [Vergleich von Tools für die Verzeichnisintegration für Hybrid-Identitäten](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Installationsübersicht: Azure AD Connect und Azure AD Connect Health](../hybrid/how-to-connect-install-roadmap.md) 

* [Technische Referenz für den generischen LDAP-Connector](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > Bei LDAP-Connectors handelt es sich um eine erweiterte Konfiguration, die eine gewisse Vertrautheit mit Forefront Identity Manager und/oder Microsoft Identity Manager voraussetzt. Falls Sie diese Konfiguration in der Produktion verwenden und Fragen haben, wenden Sie sich an den [Premier Support](https://support.microsoft.com/premier) oder an das Microsoft Partner Network.

