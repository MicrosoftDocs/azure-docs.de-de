---
title: Worum handelt es sich bei der personalbasierten Bereitstellung mit Azure Active Directory? | Microsoft-Dokumentation
description: Hier finden Sie eine Übersicht über die personalbasierte Bereitstellung.
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
ms.openlocfilehash: 1ffe8c1397525348e472e965a407909fee36152d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168446"
---
# <a name="what-is-hr-driven-provisioning"></a>Worum handelt es sich bei der personalbasierten Bereitstellung?

![Personalbasierte Bereitstellung](./media/what-is-hr-driven-provisioning/cloud2a.png)

Die personalbasierte Bereitstellung ist die Erstellung digitaler Identitäten auf der Grundlage eines Personalsystems.  Die Personalsysteme werden zum Autoritätsursprung für diese neu erstellten digitalen Identitäten und sind häufig der Ausgangspunkt für zahlreiche Bereitstellungsprozesse.  Wenn beispielsweise in Ihrem Unternehmen ein neuer Mitarbeiter eingestellt wird, wird er im Personalsystem erstellt.  Durch die Erstellung wird die Bereitstellung eines Benutzerkontos in Active Directory ausgelöst. Dieses Konto wird dann von Azure AD Connect für Azure AD bereitgestellt und so weiter.

Die personalbasierte Bereitstellung kann lokal oder cloudbasiert sein.

## <a name="on-premises-based-hr-provisioning"></a>Lokale personalbasierte Bereitstellung
Bei der lokalen personalbasierten Bereitstellung werden ein lokales Personalsystem und eine Lösung zum Bereitstellen neuer digitaler Identitäten verwendet.

Personalsysteme sind in verschiedenen Paketen und Softwarepaketen verfügbar und können SQL Server-Instanzen, LDAP-Verzeichnisse und Ähnliches verwenden.

Die lokalen Personalbereitstellungslösungen von Microsoft nutzen aktuell Microsoft Identity Manager, um die Bereitstellung auszulösen, wenn in diesen Personalsystemen eine neue Identität erstellt wird.

Mithilfe von MIM können Sie Benutzer aus Ihren lokalen Personalsystemen in Active Directory oder Azure AD bereitstellen.

Informationen zu Microsoft Identity Manager und zu den unterstützten Systemen finden Sie in der [Dokumentation zu Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016).

[!INCLUDE [active-directory-hr-provisioning.md](../../../includes/active-directory-hr-provisioning.md)]



## <a name="next-steps"></a>Nächste Schritte 
- [Was ist Identity Lifecycle Management?](what-is-identity-lifecycle-management.md)
- [Was ist die Identitätsbereitstellung?](what-is-provisioning.md)
- [Worum handelt es sich bei der App-Bereitstellung?](what-is-app-provisioning.md)
- [Worum handelt es sich bei der Bereitstellung zwischen Verzeichnissen?](what-is-inter-directory-provisioning.md)