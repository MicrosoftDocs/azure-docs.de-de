---
title: Worum handelt es sich bei der Bereitstellung zwischen Verzeichnissen mit Azure Active Directory? | Microsoft-Dokumentation
description: Hier finden Sie eine Übersicht über die Identitätsbereitstellung zwischen Verzeichnissen.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: df199afd85c788299334087321c3edac8482698d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168507"
---
# <a name="what-is-inter-directory-provisioning"></a>Worum handelt es sich bei der Bereitstellung zwischen Verzeichnissen?

Ein Verzeichnis ist eine gemeinsam genutzte Informationsinfrastruktur für die Suche, Verwaltung und Strukturierung von Elementen und Netzwerkressourcen.  Beispiele für Anwendungen, die Verzeichnisdienste verwenden, sind Microsoft Active Directory und Azure AD.  Anhand von Identitäten können Verzeichnissysteme beispielsweise ermitteln, wer worauf Zugriff hat und wer bestimmte Ressourcen verwenden darf.

Durch die Bereitstellung zwischen Verzeichnissen wird eine Identität zwischen zwei verschiedenen Verzeichnisdienstsystemen bereitgestellt.   Das gängigste Szenario für die Bereitstellung zwischen Verzeichnissen ist die Bereitstellung eines bereits in Active Directory vorhandenen Benutzers in Azure AD. Hierfür können Agents wie Azure AD Connect-Synchronisierung oder Azure AD Connect-Cloudbereitstellung verwendet werden.

Die Bereitstellung zwischen Verzeichnissen ermöglicht die Erstellung von Umgebungen mit [Hybrididentität](../hybrid/whatis-hybrid-identity.md).


## <a name="what-types-of-inter-directory-provisioning-does-azure-ad-support"></a>Von Azure AD unterstützte Arten der Bereitstellung zwischen Verzeichnissen

Von Azure AD werden aktuell drei Methoden für die Bereitstellung zwischen Verzeichnissen unterstützt. Diese Methoden werden im Anschluss beschrieben:

- [Azure AD Connect:](../hybrid/whatis-azure-ad-connect.md) Dieses Microsoft-Tool wurde entwickelt, um Sie beim Erreichen Ihrer Hybrididentitätsziele zu unterstützen – einschließlich der Bereitstellung zwischen Verzeichnissen von Active Directory in Azure AD.

- [Azure AD Connect-Cloudbereitstellung:](../cloud-provisioning/what-is-cloud-provisioning.md) Dieser neue Microsoft-Agent wurde entwickelt, um Sie beim Erreichen Ihrer Hybrididentitätsziele zu unterstützen.  Er bietet eine einfache Bereitstellung zwischen Verzeichnissen zwischen Active Directory und Azure AD.

- [Microsoft Identity Manager:](/microsoft-identity-manager/microsoft-identity-manager-2016) Die lokale Identitäts- und Zugriffsverwaltungslösung von Microsoft, die Sie bei der Verwaltung der Benutzer, Anmeldeinformationen und Richtlinien sowie des Zugriffs innerhalb Ihrer Organisation unterstützt. Darüber hinaus bietet MIM eine erweiterte Bereitstellung zwischen Verzeichnissen, um die Erstellung von Umgebungen mit Hybrididentität für Active Directory, Azure AD und andere Verzeichnisse zu ermöglichen.

### <a name="key-benefits"></a>Hauptvorteile

Die Möglichkeit zur Bereitstellung zwischen Verzeichnissen bietet erhebliche Geschäftsvorteile:

- [Kennworthashsynchronisierung](../hybrid/whatis-phs.md): Eine Anmeldemethode, die einen Hash für das lokale AD-Kennwort eines Benutzers mit Azure AD synchronisiert.
- [Passthrough-Authentifizierung](../hybrid/how-to-connect-pta.md): Eine Anmeldemethode, die Benutzern die Verwendung des gleichen Kennworts lokal und in der Cloud ermöglicht, die zusätzliche Infrastruktur einer Verbundumgebung jedoch nicht erfordert.
- [Verbundintegration](../hybrid/how-to-connect-fed-whatis.md): Kann zum Konfigurieren einer Hybridumgebung mithilfe einer lokalen AD FS-Infrastruktur verwendet werden. Er bietet auch AD FS-Verwaltungsfunktionen wie die Zertifikaterneuerung und zusätzliche AD FS-Serverbereitstellungen.
- [Synchronisierung](../hybrid/how-to-connect-sync-whatis.md): Dieser Prozess ist verantwortlich für das Erstellen von Benutzern, Gruppen und anderen Objekten.  Er stellt auch sicher, dass Identitätsinformationen für Ihre lokalen Benutzer und Gruppen denen in der Cloud entsprechen.  Diese Synchronisierung umfasst auch Kennworthashes.
- [Systemüberwachung](../hybrid/whatis-azure-ad-connect.md): Bietet eine stabile Überwachung und einen zentralen Ort im Azure-Portal, um diese Aktivität anzuzeigen. 


## <a name="next-steps"></a>Nächste Schritte 
- [Was ist Identity Lifecycle Management?](what-is-identity-lifecycle-management.md)
- [Was ist die Identitätsbereitstellung?](what-is-provisioning.md)
- [Worum handelt es sich bei der personalbasierten Bereitstellung?](what-is-hr-driven-provisioning.md)
- [Worum handelt es sich bei der App-Bereitstellung?](what-is-app-provisioning.md)