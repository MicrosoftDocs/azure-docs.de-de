---
title: 'Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung | Microsoft-Dokumentation'
description: Erläutert die Funktionsweise und Anpassung der Azure AD Connect-Synchronisierung.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3b87f40d75d4045155e7dd953dc76ffd9de2b34
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60348738"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung
Die Azure Active Directory Connect-Synchronisierungsdienste (Azure AD Connect Sync) sind eine Hauptkomponente von Azure AD Connect. Die Dienste verarbeiten alle Vorgänge in Bezug auf die Synchronisierung von Identitätsdaten zwischen Ihrer lokalen Umgebung und Azure AD. Azure AD Connect Sync ist der Nachfolger von DirSync, Azure AD Sync und Forefront Identity Manager mit konfiguriertem Azure Active Directory-Connector.

Dieses Thema ist das zentrale Thema für **Azure AD Connect Sync** (auch als **Synchronisierungsmodul bezeichnet**) und enthält eine Liste mit Links zu allen anderen verwandten Themen. Links zu Azure AD Connect finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](whatis-hybrid-identity.md).

Der Synchronisierungsdienst besteht aus zwei Komponenten: der lokalen **Azure AD Connect-Synchronisierungskomponente** und der Dienstseite in Azure AD, die als **Azure AD Connect-Synchronisierungsdienst** bezeichnet wird.

## <a name="azure-ad-connect-sync-topics"></a>Azure AD Connect Sync-Themen
| Thema | Inhalt und Relevanz |
| --- | --- |
| **Azure AD Connect Sync-Grundlagen** | |
| [Grundlagen der Architektur](concept-azure-ad-connect-sync-architecture.md) |Zielgruppe sind Benutzer, die sich noch nicht mit dem Synchronisierungsmodul auskennen und sich über die Architektur und die verwendeten Ausdrücke informieren möchten. |
| [Technische Konzepte](how-to-connect-sync-technical-concepts.md) |Eine Kurzversion des Themas zur Architektur mit einer kurzen Erklärung der verwendeten Ausdrücke. |
| [Topologien für Azure AD Connect](plan-connect-topologies.md) |Beschreibt die verschiedenen Topologien und Szenarien, die vom Synchronisierungsmodul unterstützt werden. |
| **Benutzerdefinierte Konfiguration** | |
| [Running the installation wizard again (Erneutes Ausführen des Installations-Assistenten)](how-to-connect-installation-wizard.md) |Erläutert die Optionen, die Ihnen beim erneuten Ausführen des Azure AD Connect-Installations-Assistenten zur Verfügung stehen. |
| [Understanding Declarative Provisioning (Grundlegendes zur deklarativen Bereitstellung)](concept-azure-ad-connect-sync-declarative-provisioning.md) |Beschreibt das als deklarative Bereitstellung bezeichnete Konfigurationsmodell. |
| [Grundlegendes zu Ausdrücken für die deklarative Bereitstellung](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |Beschreibt die Syntax für die bei der deklarativen Bereitstellung verwendete Ausdruckssprache. |
| [Grundlegendes zur Standardkonfiguration](concept-azure-ad-connect-sync-default-configuration.md) |Beschreibt die Standardregeln und die Standardkonfiguration. Außerdem wird beschrieben, wie die Regeln zusammenarbeiten, damit die Standardszenarien funktionieren. |
| [Grundlegendes zu Benutzern und Kontakten](concept-azure-ad-connect-sync-user-and-contacts.md) |Ist die Fortsetzung des vorherigen Themas und beschreibt, wie die Konfiguration für Benutzer und Kontakte zusammen funktioniert, insbesondere in einer Umgebung mit mehreren Gesamtstrukturen. |
| [Ändern der Standardkonfiguration](how-to-connect-sync-change-the-configuration.md) |Führt Sie durch die Schritte, die erforderlich sind, um eine allgemeine Konfigurationsänderung an Attributflüssen vorzunehmen. |
| [Bewährte Methoden zum Ändern der Standardkonfiguration](how-to-connect-sync-best-practices-changing-default-configuration.md) |Einschränkungen bei der Unterstützung und bei Änderungen der Standardkonfiguration. |
| [Konfigurieren der Filterung](how-to-connect-sync-configure-filtering.md) |Beschreibt die verschiedenen Optionen zum Begrenzen der Objekte, die mit Azure AD synchronisiert werden, und enthält eine Schrittanleitung für die Konfiguration dieser Optionen. |
| **Features und Szenarien** | |
| [Verhindern von versehentlichen Löschungen](how-to-connect-sync-feature-prevent-accidental-deletes.md) |Beschreibt das Feature zum *Verhindern von versehentlichen Löschungen* und der zugehörigen Konfiguration. |
| [Scheduler](how-to-connect-sync-feature-scheduler.md) |Beschreibt den integrierten Scheduler zum Importieren, Synchronisieren und Exportieren von Daten. |
| [Implementieren der Kennworthashsynchronisierung](how-to-connect-password-hash-synchronization.md) |Es wird beschrieben, wie die Synchronisierung von Kennwörtern funktioniert und wie die Implementierung sowie der Betrieb und die Problembehandlung durchgeführt werden. |
| [Geräterückschreiben](how-to-connect-device-writeback.md) |Beschreibt wie das Geräterückschreiben in Azure AD Connect funktioniert. |
| [Verzeichniserweiterungen](how-to-connect-sync-feature-directory-extensions.md) |Beschreibt, wie Sie das Azure AD-Schema mit Ihren eigenen benutzerdefinierten Attributen erweitern. |
| [Office 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) |Beschreibt, wie die Office 365-Ressourcen des Benutzers in der gleichen Region wie der Benutzer platziert werden. |
| **Synchronisierungsdienst** | |
| [Features des Azure AD Connect-Synchronisierungsdiensts](how-to-connect-syncservice-features.md) |Beschreibt die Dienstseite der Synchronisierung und das Ändern von Synchronisierungseinstellungen in Azure AD. |
| [Resilienz bei doppelten Attributen](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |Beschreibt die Aktivierung und Verwendung der Resilienz bei doppelten Attributwerten von **userPrincipalName** und **proxyAddresses**. |
| **Vorgänge und Benutzeroberfläche** | |
| [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) |Beschreibt die Benutzeroberfläche von Synchronization Service Manager, einschließlich der Registerkarten [Vorgänge](how-to-connect-sync-service-manager-ui-operations.md), [Connectors](how-to-connect-sync-service-manager-ui-connectors.md), [Metaverse Designer](how-to-connect-sync-service-manager-ui-mvdesigner.md) und [Metaverse Search](how-to-connect-sync-service-manager-ui-mvsearch.md). |
| [Operative Aufgaben und Überlegungen](how-to-connect-sync-operations.md) |Es werden operative Aspekte beschrieben, z. B. die Notfallwiederherstellung. |
| **Gewusst wie...** | |
| [Zurücksetzen des Azure AD-Kontos](how-to-connect-azureadaccount.md) |Informationen zum Zurücksetzen der Anmeldeinformationen des Dienstkontos, das für das Herstellen einer Verbindung zwischen Azure AD Connect-Synchronisierung und Azure AD verwendet wird |
| **Weitere Informationen und Referenzen** | |
| [Ports](reference-connect-ports.md) |Enthält eine Liste der Ports, die Sie zwischen dem Synchronisierungsmodul und Ihren lokalen Verzeichnissen und Azure AD geöffnet sein müssen. |
| [Mit Azure Active Directory synchronisierte Attribute](reference-connect-sync-attributes-synchronized.md) |Enthält eine Liste aller Attribute, die zwischen lokalem AD und Azure AD synchronisiert werden. |
| [Funktionsreferenz](reference-connect-sync-functions-reference.md) |Enthält eine Liste mit allen Funktionen, die für die deklarative Bereitstellung verfügbar sind. |

## <a name="additional-resources"></a>Weitere Ressourcen
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)
