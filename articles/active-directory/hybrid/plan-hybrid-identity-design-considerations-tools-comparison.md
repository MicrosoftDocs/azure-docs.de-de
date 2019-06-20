---
title: 'Hybrididentität: Vergleich von Tools für die Verzeichnisintegration | Microsoft-Dokumentation'
description: Diese Seite enthält eine umfassende Tabelle mit einem Vergleich der verschiedenen Tools für die Verzeichnisintegration.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aed01ea11c1f53cb090d9c2e65ee23f521575649
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60456916"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Vergleich von Tools für die Verzeichnisintegration für Hybrid-Identitäten
Im Laufe der Jahre sind die Tools für die Verzeichnisintegration umfangreicher geworden und wurden weiterentwickelt.  Das vorliegende Dokument bietet eine Übersicht über diese Tools und einen Vergleich der Features, die jedes der Tools bietet.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> Azure AD Connect umfasst die Komponenten und Funktionen, die zuvor als DirSync und AAD Sync veröffentlicht wurden. Diese Tools werden nicht mehr einzeln veröffentlicht, und alle künftigen Verbesserungen werden in den Updates von Azure AD Connect enthalten sein, sodass Sie immer wissen, wo Sie die aktuellsten Funktionen bekommen.
> 
> DirSync and Azure AD Sync sind veraltet. Weitere Informationen finden Sie [hier](reference-connect-dirsync-deprecated.md).
> 
> 

Verwenden Sie den folgenden Schlüssel für jede der Tabellen.

● = Jetzt verfügbar  
FR = Künftige Version  
PP = Öffentliche Vorschau  

## <a name="on-premises-to-cloud-synchronization"></a>Synchronisierung vom lokalen Standort zur Cloud
| Feature | Azure Active Directory Connect | Azure Active Directory-Synchronisierungsdienste (AAD Sync) – NICHT MEHR UNTERSTÜTZT | Azure Active Directory-Synchronisierungstool (DirSync) – NICHT MEHR UNTERSTÜTZT | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Herstellen einer Verbindung mit einer einzelnen lokalen AD-Gesamtstruktur |● |● |● |● |● |
| Herstellen einer Verbindung mit mehreren lokalen AD-Gesamtstrukturen |● |● | |● |● |
| Herstellen einer Verbindung mit mehreren lokalen Exchange-Organisationen |● | | | | |
| Herstellen einer Verbindung mit einem einzelnen lokalen LDAP-Verzeichnis |●* | | |● |● | 
| Herstellen einer Verbindung mit mehreren lokalen LDAP-Verzeichnissen |●*  | | |● |● | 
| Herstellen einer Verbindung mit lokalen Active Directory- und LDAP-Verzeichnissen |●* | | |● |● | 
| Herstellen einer Verbindung mit benutzerdefinierten Systeme (z. B. SQL, Oracle, MySQL usw.) |BV | | |● |● |
| Synchronisieren von benutzerdefinierten Attributen (Verzeichniserweiterungen) |● | | | | |
| Verbinden mit lokaler HR-Instanz (d. h. SAP, Oracle eBusiness,PeopleSoft) |BV | | |● |● |
| Unterstützt FIM-Synchronisierungsregeln und -Connectors für die Bereitstellung lokaler Systeme. | | | |● |● |

 
&#42; Derzeit gibt es hierfür zwei unterstützte Optionen.  Sie lauten wie folgt: 

   1. Sie können den generischen LDAP-Connector verwenden und ihn außerhalb von Azure AD Connect aktivieren.  Dies ist ein komplexer Vorgang, für den ein Partner für das Onboarding und eine Premier Support-Vereinbarung für die Wartung erforderlich sind.  Mit dieser Option können sowohl einzelne als auch mehrere LDAP-Verzeichnisse verarbeitet werden. 

   2. Sie können Ihre eigene Lösung zum Verschieben von Objekten von LDAP zu Active Directory entwickeln.  Synchronisieren Sie die Objekte anschließend mit Azure AD Connect.  MIM oder FIM kann als mögliche Lösung zum Verschieben der Objekte verwendet werden. 

## <a name="cloud-to-on-premises-synchronization"></a>Synchronisierung von der Cloud zum lokalen Standort
| Feature | Azure Active Directory Connect | Azure Active Directory-Synchronisierungsdienste – NICHT MEHR UNTERSTÜTZT  | Azure Active Directory-Synchronisierungstool (DirSync) – NICHT MEHR UNTERSTÜTZT  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Rückschreiben von Geräten |● | |● | | |
| Rückschreiben von Attributen (für Exchange-Hybridbereitstellung) |● |● |● |● |● |
| Rückschreiben von Gruppenobjekten |● | | | | |
| Rückschreiben von Kennwörtern (Self-Service-Kennwortzurücksetzung und Kennwortänderung) |● |● | | | |

## <a name="authentication-feature-support"></a>Unterstützung von Authentifizierungsfunktionen
| Feature | Azure Active Directory Connect | Azure Active Directory-Synchronisierungsdienste – NICHT MEHR UNTERSTÜTZT  | Azure Active Directory-Synchronisierungstool (DirSync) – NICHT MEHR UNTERSTÜTZT  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Kennworthashsynchronisierung für eine einzelne lokale AD-Gesamtstruktur |●|●|● | | |
| Kennworthashsynchronisierung für mehrere lokale AD-Gesamtstrukturen |●|● | | | |
| Passthrough-Authentifizierung für einzelne lokale AD-Gesamtstrukturen |●| | | | |
| Einmaliges Anmelden mit Verbund |● |● |● |● |● |
| Nahtloses einmaliges Anmelden|● |||||
| Rückschreiben von Kennwörtern (Self-Service-Kennwortzurücksetzung und Kennwortänderung) |● |● | | | |

## <a name="set-up-and-installation"></a>Einrichtung und Installation
| Feature | Azure Active Directory Connect | Azure Active Directory-Synchronisierungsdienste – NICHT MEHR UNTERSTÜTZT  | Azure Active Directory-Synchronisierungstool (DirSync) – NICHT MEHR UNTERSTÜTZT  | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|
| Unterstützt die Installation auf einem Domänencontroller |● |● |● | |
| Unterstützt die Installation mit SQL Express |● |● |● | |
| Einfaches Upgrade von DirSync |● | | | |
| Lokalisierung der Administrator-UX in die Windows Server-Sprachen |● |● |● | |
| Lokalisierung der Endbenutzer-UX in die Windows Server-Sprachen | | | |● |
| Unterstützung für Windows Server 2008 und Windows Server 2008 R2 |● für Synchronisierung, nicht für den Verbund |● |● |● |
| Unterstützung für Windows Server 2012 und Windows Server 2012 R2 |● |● |● |● |

## <a name="filtering-and-configuration"></a>Filterung und Konfiguration
| Feature | Azure Active Directory Connect | Azure Active Directory-Synchronisierungsdienste – NICHT MEHR UNTERSTÜTZT  | Azure Active Directory-Synchronisierungstool (DirSync) – NICHT MEHR UNTERSTÜTZT  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Filterung nach für Domänen und Organisationseinheiten |● |● |● |● |● |
| Filterung nach den Attributwerten eines Objekts |● |● |● |● |● |
| Zulassen eines minimalen Attributsatzes für die Synchronisierung (MinSync) |● |● | | | |
| Zulassen der Anwendung verschiedener Dienstvorlagen auf Attributflüsse |● |● | | | |
| Zulassen der Entfernung von Attributen aus dem Attributfluss von AD nach Azure AD |● |● | | | |
| Zulassen einer erweiterten Anpassung des Attributflusses |● |● | |● |● |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).

